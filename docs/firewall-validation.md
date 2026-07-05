# Firewall Validation

## Objective

Validate the host firewall from a separate Windows workstation instead of relying only on local firewall configuration.

## Tools Used

- `sudo ufw status numbered`
- `sudo ss -tulpn`
- Windows PowerShell `Test-NetConnection`
- `nslookup`

## LAN Validation

The LAN address was tested from the Windows workstation.

```powershell
Test-NetConnection 192.168.0.10 -Port 22
Test-NetConnection 192.168.0.10 -Port 3000
Test-NetConnection 192.168.0.10 -Port 5902
Test-NetConnection 192.168.0.10 -Port 80
Test-NetConnection 192.168.0.10 -Port 443
```

Expected results:

| Port | Service | Expected |
|---:|---|---|
| 22 | SSH | Blocked |
| 3000 | Grafana | Blocked |
| 5902 | VNC | Blocked |
| 80 | Pi-hole HTTP | Allowed |
| 443 | Pi-hole HTTPS | Allowed |

## Tailscale Validation

The Tailscale address was tested from the Windows workstation.

```powershell
Test-NetConnection 100.x.x.x -Port 22
Test-NetConnection 100.x.x.x -Port 3000
Test-NetConnection 100.x.x.x -Port 5902
Test-NetConnection 100.x.x.x -Port 80
Test-NetConnection 100.x.x.x -Port 443
```

Expected results:

| Port | Service | Expected |
|---:|---|---|
| 22 | SSH | Allowed |
| 3000 | Grafana | Allowed |
| 5902 | VNC | Allowed |
| 80 | Pi-hole HTTP | Allowed |
| 443 | Pi-hole HTTPS | Allowed |

## DNS Validation

DNS resolution was tested explicitly against Pi-hole:

```powershell
nslookup example.com 192.168.0.10
```

A successful response confirmed that the Pi was answering DNS queries on port 53 for the approved LAN path.

## Key Takeaway

`ss` confirms what is listening locally, while `Test-NetConnection` confirms whether another host can actually reach the service through the firewall. Both views are necessary for a meaningful security audit.
