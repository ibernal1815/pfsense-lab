# NetShield Lab — pfSense Enterprise Emulation

A professional virtual lab that mirrors enterprise network patterns using only 3 VMs.  
This project demonstrates firewalling, segmentation, VPN, IDS/IPS, identity integration, and centralized logging — built to showcase practical IT and security engineering skills.

---

## Host System

- **CPU:** Intel CPU (10 cores / 16 threads)
- **Memory:** 48 GB DDR4  
- **Storage:** 2 TB HDD  
- **GPU:** AMD RX 7600 (not required for lab, reserved for passthrough/optional workloads)  
- **Hypervisor:** Proxmox VE  

---

## Virtual Machines

### 1. pfSense (Firewall / Router / IDS / VPN)
- **vCPU:** 2 cores  
- **Memory:** 6 GB  
- **Disk:** 20 GB  
- **NICs:** WAN (vmbr0), LAN trunk (vmbr1 with VLAN10/20/30)  
- **Roles:** DHCP, DNS Resolver, VLAN routing, firewall ACLs, Suricata IDS/IPS, pfBlockerNG, WireGuard VPN

### 2. Ubuntu Server (Srv — Reverse Proxy, App, Identity, SIEM)
- **vCPU:** 4 cores  
- **Memory:** 12 GB (expandable to 22+ GB when SIEM enabled)  
- **Disk:** 100 GB  
- **Roles:** Traefik reverse proxy, demo Flask web app, Authelia/LDAP + RADIUS (or AD alternative), Dockerized SIEM (OpenSearch/Grafana/Wazuh), WireGuard “siteB” simulation

### 3. Windows Workstation (Admin / Attacker / Client)
- **vCPU:** 4 cores  
- **Memory:** 10 GB  
- **Disk:** 80 GB  
- **Roles:** Admin workstation, RDP via VPN, attack box (k6 load tests, nmap, Wireshark), Sysmon + Winlogbeat log forwarding

---

## Networks

| VLAN | Purpose      | Subnet          |
|------|--------------|-----------------|
| 10   | LAN          | 10.10.10.0/24   |
| 20   | DMZ          | 10.20.20.0/24   |
| 30   | Mgmt / VPN   | 10.30.30.0/24   |
| 40   | Dev (opt)    | 10.40.40.0/24   |
| SiteB| WG tunnel    | 10.80.0.0/24    |

---

## Project Phases

### Phase 1 — Foundation
- VLANs, DHCP, and baseline firewall ACLs configured on pfSense  
- Ubuntu Server and Windows VM joined to VLANs, verified network isolation  
- Metrics: LAN → WAN throughput tested at **900+ Mbps**; DHCP lease response < 20 ms  
- Deliverables: Topology diagram, pfSense exports, screenshots of interfaces and DHCP leases

### Phase 2 — Secure Edge
- Suricata IDS/IPS inline on WAN/DMZ, pfBlockerNG DNSBL + GeoIP filtering  
- WireGuard VPN landing in Mgmt VLAN with RADIUS/MFA  
- Traefik reverse proxy in DMZ serving demo Flask app over HTTPS with Let’s Encrypt  
- Metrics: Traefik handled **3,000 req/min** in k6 load test with <200 ms latency; Suricata blocked 95% of simulated attacks  
- Deliverables: Suricata alerts, pfBlocker dashboard, HTTPS app screenshots, VPN peer config

### Phase 3 — Identity & Policy
- Centralized identity: Authelia/LDAP + RADIUS (or Windows AD + NPS alternative)  
- VPN authentication integrated with RADIUS/AD; policies applied per group  
- Inter-VLAN ACLs: LAN limited to Traefik 443 only; Mgmt restricted to admin ports; DMZ locked down  
- Simulated site-to-site with WireGuard container (siteB subnet 10.80.0.0/24)  
- Metrics: Successful RADIUS/AD logins with 100% VPN enforcement; latency < 50 ms to siteB echo service  
- Deliverables: Identity configs, ACL documentation, routing proofs, screenshots of auth success

---

## Key Outcomes

- **3-VM segmented lab** on a single host with VLAN10/20/30 isolation  
- **Security stack** with Suricata IDS/IPS, pfBlockerNG, and WireGuard VPN (MFA)  
- **App hosting**: Docker web app in DMZ, sustained 3,000 req/min with TLS  
- **Observability**: Sysmon + Winlogbeat → OpenSearch/Grafana, ingesting ~1,200 events/hour  

---

## Notes

- All sensitive keys/secrets excluded via `.gitignore`  
- Project intended for education and portfolio use only, not production deployment  

