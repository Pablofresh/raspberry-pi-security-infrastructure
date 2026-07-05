# Lessons Learned

## Security Lessons

- Default-deny inbound firewall policy is a strong starting point for a home lab server.
- Administrative services should not be broadly exposed when a private management network is available.
- Interface-aware firewall rules provide practical segmentation on a single host.
- SSH changes should be performed in a safe order: add restricted access, test it, then remove broad access.
- Fail2Ban is useful, but it is not a replacement for reducing exposure at the firewall level.

## Linux Administration Lessons

- `ss -tulpn` is one of the most useful commands for service exposure audits.
- Systemd services and sockets both need to be considered when disabling services.
- UFW rule deletion should be done carefully because rule numbers change.
- Localhost-only services can support internal workflows without increasing network exposure.

## Portfolio Lessons

- A good portfolio project should show the reasoning behind security decisions, not just screenshots.
- Validation evidence is stronger when collected from a second host.
- Troubleshooting notes are valuable because they show real operational learning.
- Raw evidence should be sanitized before publication.

## Next Learning Goals

- Build an isolated virtualization lab on the Dell T7910 using Proxmox.
- Create separate networks for management, lab targets, and monitoring.
- Add vulnerable target VMs for controlled testing.
- Add a detection layer such as Suricata or a SIEM.
- Document attack simulation and detection workflows safely and legally.
