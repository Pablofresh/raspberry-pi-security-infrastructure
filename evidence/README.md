# Evidence Folder

This folder is for sanitized evidence from the lab.

Recommended files to include after sanitization:

- `ufw-status-sanitized.txt`
- `listening-ports-sanitized.txt`
- `fail2ban-status-sanitized.txt`
- `firewall-test-results-sanitized.txt`
- `storage-layout-sanitized.txt`

Do not upload raw files that contain private Tailscale IPs, email addresses, account handles, hostnames you do not want public, passwords, keys, tokens, or full internal network details.

Suggested sanitization examples:

| Original | Public Version |
|---|---|
| `100.122.136.68` | `100.x.x.x` |
| Personal email or handle | `[redacted]` |
| Exact hostname if private | `raspberrypi` or `[host-redacted]` |
| Full Tailscale status | Sanitized device list |
