# Lab Setup

## Environment Overview

This lab was built entirely inside VirtualBox on a single host machine, using two isolated virtual machines connected via a host-only network.

## Virtual Machines

**Target — Windows 11 (SOC-LAB1-Win11)**
- Windows 11 Home, clean install
- 4GB RAM, 2 vCPUs, 60GB disk
- Sysmon installed with the SwiftOnSecurity configuration
- Splunk Enterprise Free installed as the SIEM
- OpenSSH Server enabled (Windows Home does not support inbound RDP)

**Attacker — Kali Linux**
- Official pre-built Kali VirtualBox appliance (2026.2)
- Used to run Hydra against the Windows target

## Network Configuration

Both VMs were configured with two network adapters:
- **Adapter 1 (NAT):** internet access, used for downloading tools/updates
- **Adapter 2 (Host-only):** isolated private network (192.168.56.0/24) used exclusively for attack traffic between the two VMs, with no route to the internet or the host's real network

| VM | Host-only IP |
|---|---|
| Windows target | 192.168.56.101 |
| Kali attacker | 192.168.56.104 |

Connectivity between the two VMs was verified with `ping` and `nc` (netcat) after adding the necessary inbound firewall rules on Windows (ICMP and TCP/22).

## Data Pipeline

Splunk was configured to ingest the Windows Security Event Log via a local event log input (`WinEventLog:Security`), alongside the Sysmon Operational log, giving visibility into both authentication events and detailed process/system activity.

Add lab setup documentation
