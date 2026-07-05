# Network Hardening

## Initial Audit

Listening services were audited using:

```bash
sudo ss -tulpn
sudo ss -ltnp
sudo ss -lunp
```

The audit identified expected services such as Pi-hole, Unbound, Grafana, Prometheus, Node Exporter, SSH, Tailscale, and TigerVNC. It also identified `rpcbind` on port 111 and multiple TigerVNC sessions.

## VNC Cleanup

TigerVNC sessions were reviewed using:

```bash
tigervncserver -list
ps -fp <pid1>,<pid2>
```

Stale or unnecessary sessions were removed so only the intended VNC display remained active.

## rpcbind Investigation and Removal

The `rpcbind` service was investigated with:

```bash
systemctl status rpcbind --no-pager
sudo rpcinfo -p
```

Only the portmapper service was registered, and no NFS-related RPC services were present. The service was disabled with:

```bash
sudo systemctl disable --now rpcbind.socket
sudo systemctl disable --now rpcbind.service
```

Verification:

```bash
systemctl is-active rpcbind.service rpcbind.socket
systemctl is-enabled rpcbind.service rpcbind.socket
sudo ss -tulpn | grep ':111'
```

The desired result was no listener on port 111.

## SSH Restriction

Before removing broad SSH access, Tailscale SSH access was verified from the Windows workstation:

```powershell
ssh pablofresh@100.x.x.x
```

A Tailscale-only UFW rule was added before removing the broad SSH rule:

```bash
sudo ufw allow in on tailscale0 to any port 22 proto tcp
```

Then the old broad SSH rules were removed using numbered UFW deletion.

## Grafana Restriction

Grafana was restricted to Tailscale:

```bash
sudo ufw allow in on tailscale0 to any port 3000 proto tcp
```

The old broad Grafana rules were then removed.

## Pi-hole Web Restriction

Pi-hole web access was allowed from the LAN and Tailscale:

```bash
sudo ufw allow from 192.168.0.0/24 to any port 80 proto tcp
sudo ufw allow from 192.168.0.0/24 to any port 443 proto tcp
sudo ufw allow in on tailscale0 to any port 80 proto tcp
sudo ufw allow in on tailscale0 to any port 443 proto tcp
```

The old broad web rules were then removed.

## DNS Restriction

DNS was restricted to the LAN and Tailscale:

```bash
sudo ufw allow from 192.168.0.0/24 to any port 53
sudo ufw allow in on tailscale0 to any port 53
```

The old broad DNS rules were then removed.

## Final Rule Philosophy

- Keep DNS available only to approved networks.
- Keep administrative services available only over Tailscale.
- Keep Unbound local-only.
- Avoid exposing monitoring services broadly.
