# Home SOC Lab — Wazuh SIEM + Sysmon + Attack Simulation

![Status](https://img.shields.io/badge/Status-Active-brightgreen)
![SIEM](https://img.shields.io/badge/SIEM-Wazuh%204.14.3-blue)
![Platform](https://img.shields.io/badge/Platform-VirtualBox-lightgrey)
![OS](https://img.shields.io/badge/Attacker-Kali%20Linux-red)

## Overview

A fully functional home Security Operations Center (SOC) lab built to simulate 
real-world attack scenarios and practice threat detection, log analysis, and 
incident response workflows. This lab demonstrates core SOC analyst skills 
including SIEM deployment, agent configuration, attack simulation, and 
alert triage.

---

## Lab Architecture

| Machine | OS | IP | Role |
|---|---|---|---|
| Wazuh Server | Ubuntu 22.04 LTS | 192.168.1.119 | SIEM, indexer, dashboard |
| Windows Agent | Windows 10 Pro | 192.168.1.120 | Monitored endpoint |
| Kali Attacker | Kali Linux | 192.168.1.105 | Attack simulation |

**Network:** All VMs on bridged network, isolated for lab use
**Hypervisor:** Oracle VirtualBox on Windows 11 host (32GB RAM)

---

## Tools & Technologies

| Tool | Purpose |
|---|---|
| Wazuh 4.14.3 | SIEM, XDR, threat detection |
| Sysmon + SwiftOnSecurity config | Windows endpoint telemetry |
| Kali Linux | Attack simulation platform |
| Hydra / CrackMapExec | Brute force simulation |
| Nmap | Network reconnaissance |
| VirtualBox | Virtualization |

---

## Lab Setup

### 1. Wazuh Server (Ubuntu 22.04)
- Deployed Wazuh all-in-one installer (manager + indexer + dashboard)
- Configured admin credentials via wazuh-passwords-tool
- Verified cluster health: `green`
- Dashboard accessible at `https://192.168.1.119`

### 2. Windows 10 Agent
- Deployed Wazuh agent via PowerShell
- Installed Sysmon with SwiftOnSecurity detection ruleset
- Configured `ossec.conf` to ingest `Microsoft-Windows-Sysmon/Operational`
- Agent status: **Active**

### 3. Kali Linux Attacker
- Configured on same subnet for direct attack simulation
- Tools used: Nmap, Hydra, CrackMapExec

---

## Attack Simulations & Detections

### Attack 1 — Network Reconnaissance (Nmap)

**Command:**
```bash
