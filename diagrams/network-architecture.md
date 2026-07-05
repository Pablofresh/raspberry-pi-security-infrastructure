# Network Architecture Diagram

This diagram shows the network paths, service relationships, monitoring flow, and management access controls implemented in the Raspberry Pi security infrastructure lab.

```mermaid
flowchart TD
    Internet[Internet]
    Router[TP-Link ER706W Router]
    Switch[TP-Link SG2008 Managed Switch]
    Windows[Windows 11 Workstation]
    Pi[Raspberry Pi 5 Security Node<br/>LAN: 192.168.0.10]
    Tailnet[Tailscale Private Network]
    Remote[Remote Tailscale Device]

    PiHole[Pi-hole<br/>DNS Filtering<br/>Port 53]
    Unbound[Unbound<br/>Recursive DNS<br/>127.0.0.1:5335]
    Prometheus[Prometheus<br/>Metrics Collection<br/>Port 9090]
    NodeExporter[Node Exporter<br/>System Metrics<br/>Port 9100]
    Grafana[Grafana<br/>Monitoring Dashboards<br/>Port 3000]
    SSH[SSH<br/>Port 22]
    VNC[TigerVNC<br/>Port 5902]
    Fail2Ban[Fail2Ban<br/>SSH Protection]
    UFW[UFW<br/>Host Firewall]

    Internet --> Router
    Router --> Switch
    Switch --> Windows
    Switch --> Pi

    Windows -->|DNS queries| PiHole
    PiHole -->|Recursive queries| Unbound
    Unbound --> Internet

    NodeExporter --> Prometheus
    Prometheus --> Grafana

    Windows -.->|Private management path| Tailnet
    Remote -.->|Remote management path| Tailnet
    Tailnet -.->|SSH 22| SSH
    Tailnet -.->|Grafana 3000| Grafana
    Tailnet -.->|VNC 5902| VNC

    Pi --- PiHole
    Pi --- Unbound
    Pi --- NodeExporter
    Pi --- Prometheus
    Pi --- Grafana
    Pi --- SSH
    Pi --- VNC
    Pi --- Fail2Ban
    Pi --- UFW
```

## Access-Control Summary

| Service | LAN Access | Tailscale Access | Exposure |
|---|---|---|---|
| DNS — 53 | Allowed | Allowed | Required for DNS clients |
| Pi-hole Web — 80/443 | Allowed | Allowed | Administrative interface |
| SSH — 22 | Blocked | Allowed | Private management only |
| Grafana — 3000 | Blocked | Allowed | Private monitoring access |
| TigerVNC — 5902 | Blocked | Allowed | Private remote desktop |
| Unbound — 5335 | Localhost only | Not exposed | Internal DNS resolver |
