# Raspberry Pi Security Infrastructure and Network Hardening Lab

## Project Summary

Built and hardened a Raspberry Pi 5 as an always-on security infrastructure node for a home cybersecurity lab. The system provides DNS filtering, local recursive DNS resolution, monitoring, visualization, and secure remote administration. The project focused on service enumeration, attack-surface reduction, interface-aware firewall policy, remote access hardening, and validation from a separate Windows workstation.

## Objectives

- Deploy a Raspberry Pi 5 as a lightweight security infrastructure server.
- Provide network DNS filtering using Pi-hole.
- Use Unbound as a local recursive DNS resolver.
- Deploy Prometheus and Grafana for monitoring.
- Configure secure remote administration over Tailscale.
- Audit listening services and reduce unnecessary exposure.
- Apply least-privilege firewall rules with UFW.
- Validate firewall behavior from a separate host.
- Capture a post-hardening baseline for future comparison.

## Hardware and Environment

| Component | Role |
|---|---|
| Raspberry Pi 5 | Always-on security infrastructure node |
| Windows 11 workstation | Administration and validation host |
| Tailscale network | Private remote management path |
| Home LAN | Local client network using Pi services |
| Future Dell T7910 | Planned Proxmox virtualization/cyber range host |

## Services Deployed

| Service | Purpose | Security Posture |
|---|---|---|
| Pi-hole | DNS filtering and DNS sinkhole | DNS limited to LAN and Tailscale |
| Unbound | Local recursive DNS resolver | Bound to localhost only on port 5335 |
| Grafana | Monitoring dashboard | Restricted to Tailscale |
| Prometheus | Metrics collection | Not exposed through UFW |
| Node Exporter | System metrics | Not exposed through UFW |
| SSH | Remote administration | Restricted to Tailscale and key-based login |
| TigerVNC | Remote desktop access | Restricted to Tailscale |
| Fail2Ban | SSH brute-force protection | Active sshd jail verified |
| UFW | Host firewall | Default-deny inbound policy |

## Key Security Work Completed

- Audited listening TCP and UDP services using `ss`.
- Removed duplicate UFW rules.
- Disabled unnecessary `rpcbind` service after confirming no RPC services were registered beyond portmapper.
- Restricted SSH from broad access to Tailscale-only access.
- Restricted Grafana from broad access to Tailscale-only access.
- Restricted TigerVNC to Tailscale access through UFW.
- Restricted Pi-hole web access to LAN and Tailscale.
- Restricted DNS access to LAN and Tailscale.
- Verified Unbound was listening only on `127.0.0.1:5335`.
- Verified Fail2Ban service and `sshd` jail status.
- Validated firewall behavior from a Windows workstation using `Test-NetConnection`.
- Created a post-hardening baseline snapshot containing ports, firewall status, running services, Fail2Ban status, Tailscale status, and storage layout.

## Final Firewall Design

| Service | Port | Allowed Path |
|---|---:|---|
| SSH | 22 | Tailscale only |
| Grafana | 3000 | Tailscale only |
| TigerVNC | 5902 | Tailscale only |
| Pi-hole HTTP | 80 | LAN and Tailscale |
| Pi-hole HTTPS | 443 | LAN and Tailscale |
| DNS | 53 | LAN and Tailscale |
| Unbound | 5335 | Localhost only |

## Validation Results

The firewall policy was validated from a separate Windows workstation.

### LAN Tests

| Target | Expected Result | Actual Result |
|---|---|---|
| `192.168.0.10:22` | Blocked | Passed |
| `192.168.0.10:3000` | Blocked | Passed |
| `192.168.0.10:5902` | Blocked | Passed |
| `192.168.0.10:80` | Allowed | Passed |
| `192.168.0.10:443` | Allowed | Passed |

### Tailscale Tests

| Target | Expected Result | Actual Result |
|---|---|---|
| `100.x.x.x:22` | Allowed | Passed |
| `100.x.x.x:3000` | Allowed | Passed |
| `100.x.x.x:5902` | Allowed | Passed |
| `100.x.x.x:80` | Allowed | Passed |
| `100.x.x.x:443` | Allowed | Passed |

## Important Lessons Learned

- A service can be listening locally without being reachable remotely.
- `ss` shows local listeners; firewall validation from another host confirms reachability.
- UFW rule order and numbering matter when deleting rules.
- Interface-specific firewall rules are useful for separating LAN access from private management access.
- Remote access changes should be made carefully to avoid lockout: add the restricted rule first, verify access, then remove broad access.
- Default-deny inbound policy is a strong baseline for a home lab server.
- Raw baseline evidence should be sanitized before being published publicly.

## Repository Notes

This repository is intended as a sanitized public portfolio version of the lab. Private information such as full Tailscale IPs, account identifiers, SSH keys, passwords, and raw baseline archives should not be committed.

## Future Improvements

- Build an isolated Proxmox cyber range on the Dell T7910.
- Add a Kali Linux attack VM and intentionally vulnerable target VMs.
- Add a monitoring sensor for lab traffic.
- Create detection rules for controlled lab activity.
- Add sanitized screenshots of Pi-hole, Grafana, firewall tests, and service baselines.
- Automate baseline capture with a script.
