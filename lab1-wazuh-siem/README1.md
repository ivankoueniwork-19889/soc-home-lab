# Home SOC Lab — Wazuh SIEM + Sysmon + Attack Simulation

A fully functional home Security Operations Center (SOC) lab built to simulate
real-world attack scenarios and practice threat detection, log analysis, and
incident response workflows.

## Lab Architecture

| Machine | OS | IP | Role |
|---|---|---|---|
| Wazuh Server | Ubuntu 22.04 LTS | 192.168.1.119 | SIEM, indexer, dashboard |
| Windows Agent | Windows 10 Pro | 192.168.1.120 | Monitored endpoint |
| Kali Attacker | Kali Linux | 192.168.1.105 | Attack simulation |

**Network:** Bridged, isolated for lab use. **Hypervisor:** VirtualBox on Windows 11 host (32GB RAM)

## Tools & Technologies

| Tool | Purpose |
|---|---|
| Wazuh 4.14.3 | SIEM, XDR, threat detection |
| Sysmon + SwiftOnSecurity config | Windows endpoint telemetry |
| Kali Linux | Attack simulation platform |
| Hydra / CrackMapExec | Brute force simulation |
| Nmap | Network reconnaissance |

## Attack Simulations & Detections

### Attack 1 — Network Reconnaissance (Nmap)
`sudo nmap -sS -sV -O -p- 192.168.1.120`

Open ports found: 135 (RPC), 139 (NetBIOS), 445 (SMB — critical), 49664-49672 (RPC dynamic).
**Wazuh Detection:** Sysmon Event ID 3 (Network Connection) showing rapid port connections from 192.168.1.105.

### Attack 2 — Brute Force Authentication Attack
32 simulated failed logon attempts against a fake account.

| Rule ID | Description | Level | Event ID |
|---|---|---|---|
| 60122 | Logon Failure - Unknown user or bad password | 5 | 4625 |
| 60204 | Multiple Windows Logon Failures | **10** | 4625 |

32 events within a 15-minute window triggered escalation to Level 10.

### Additional Findings
| Detection | Rule ID | Level | Description |
|---|---|---|---|
| DLL Search Order Hijack | 92219 | 6 | Possible DLL hijacking via Windows Update path |
| Vulnerability Detection | — | Critical | 10 Critical CVEs on fresh Windows 10 install |
| CIS Benchmark | — | — | 32% compliance score, 263 failed controls |

## MITRE ATT&CK Mapping

| Technique | ID | Tactic | Detected |
|---|---|---|---|
| Network Service Discovery | T1046 | Discovery | ✅ Nmap scan |
| Brute Force | T1110 | Credential Access | ✅ Rule 60204 |
| Valid Accounts | T1078 | Defense Evasion | ✅ Event 4625 |
| DLL Search Order Hijacking | T1574.001 | Persistence | ✅ Rule 92219 |

## Evidence — Screenshots

![Wazuh Agent Active](wazuh%20screenshot.png)
![DLL Hijack Detection](wazuh%20events%20port%20scan.png)
![Brute Force Detection](wazuh%20logon%20failed%20dasborad.png)
![Threat Hunting](Threat%20hunting%20wazuh.png)
