# Architecture

## Overview

This project uses a Raspberry Pi 5 as an always-on security infrastructure node in a home cybersecurity lab. The Pi provides DNS filtering, recursive DNS resolution, monitoring, visualization, and secure remote administration.

## High-Level Design

```text
Windows 11 Workstation
        |
        | LAN access for approved web/DNS services
        v
Home LAN ---- Raspberry Pi 5 Security Node
        |        | Pi-hole: 53, 80, 443
        |        | Unbound: 127.0.0.1:5335
        |        | Grafana: 3000 via Tailscale only
        |        | SSH: 22 via Tailscale only
        |        | VNC: 5902 via Tailscale only
        |
        +---- Tailscale private network for management
```

## Network Zones

| Zone | Purpose |
|---|---|
| LAN | Local access for DNS and Pi-hole web management |
| Tailscale | Private management network for SSH, Grafana, VNC, and remote Pi-hole access |
| Localhost | Internal-only resolver communication between Pi-hole and Unbound |

## Service Exposure Strategy

The design separates user-facing infrastructure services from administrative services.

- DNS and Pi-hole web access are available to the LAN.
- Administrative services are restricted to Tailscale.
- Unbound is not exposed externally and listens only on localhost.
- Prometheus and Node Exporter listen locally but are not allowed through UFW from the LAN.

## Security Rationale

The goal is to reduce attack surface while keeping the lab usable. Remote administration is routed through Tailscale instead of being exposed broadly on the LAN. DNS remains available to approved client networks because it is an infrastructure service, but administrative access is treated as privileged and restricted.
