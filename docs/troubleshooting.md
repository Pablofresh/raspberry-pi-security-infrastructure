# Troubleshooting Notes

## VNC Session Confusion

During the lab, multiple TigerVNC sessions existed simultaneously. Different displays mapped to different ports:

| Display | Port |
|---:|---:|
| `:1` | 5901 |
| `:2` | 5902 |
| `:3` | 5903 |

A stale session was detected and cleaned up. One unexpected behavior was that a stale cleanup command also started a new localhost-only VNC session. This reinforced the need to verify actual listeners after every change.

Useful commands:

```bash
tigervncserver -list
sudo ss -ltnp | grep -E '590[0-9]'
```

## UFW Rule Numbering

UFW numbered rules renumber after deletion. To avoid deleting the wrong rule, broad rules were removed from highest rule number downward.

Useful command:

```bash
sudo ufw status numbered
```

## Listening vs Reachable

A major lesson was the distinction between a service listening locally and a service being reachable remotely.

Example:

- TigerVNC was listening on `0.0.0.0:5902`.
- UFW only allowed port 5902 through `tailscale0`.
- LAN testing confirmed that port 5902 was blocked from the ordinary LAN address.

## rpcbind Investigation

`rpcbind` was listening on port 111. Before disabling it, the registered RPC services were checked:

```bash
sudo rpcinfo -p
```

Only portmapper entries were present, so `rpcbind` was disabled and verified as inactive.

## PowerShell Timeout Behavior

When testing blocked ports with `Test-NetConnection`, PowerShell may wait before returning `TcpTestSucceeded : False`. This is normal when the firewall silently drops traffic instead of rejecting it.
