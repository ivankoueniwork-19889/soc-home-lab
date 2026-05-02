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
sudo nmap -sS -sV -O -p- 192.168.1.120
```

**Findings — Open ports on Windows target:**
| Port | Service | Risk |
|---|---|---|
| 135/tcp | Microsoft RPC | Lateral movement vector |
| 139/tcp | NetBIOS-SSN | Legacy protocol, info disclosure |
| 445/tcp | SMB | Critical — ransomware, lateral movement |
| 49664-49672/tcp | RPC dynamic | Attack surface |

**OS Fingerprint:** Microsoft Windows 10 (1709–21H2)

**Wazuh Detection:** Sysmon Event ID 3 (Network Connection) events 
captured showing rapid port connection attempts from `192.168.1.105`

---

### Attack 2 — Brute Force Authentication Attack

**Command:**
```powershell
# Simulated 20 rapid failed authentication attempts
1..20 | ForEach-Object {
    Start-Process -FilePath "cmd.exe" -Credential $fakecredential
}
```

**Wazuh Detections:**
| Rule ID | Description | Level | Event ID |
|---|---|---|---|
| 60122 | Logon Failure - Unknown user or bad password | 5 | 4625 |
| 60204 | Multiple Windows Logon Failures | **10** | 4625 |

**Alert Details (Event ID 4625):**
- Target Account: `fakeuser`
- Workstation: `DESKTOP-5M171KU`
- Severity: `AUDIT_FAILURE`
- 32 events captured within 15-minute window
- Triggered escalation rule at **Level 10** (high severity)

---

### Additional Findings — Automated Detections

| Detection | Rule ID | Level | Description |
|---|---|---|---|
| DLL Search Order Hijack | 92219 | 6 | Possible DLL hijacking via Windows Update path |
| Vulnerability Detection | — | Critical | 10 Critical CVEs on fresh Windows 10 install |
| CIS Benchmark | — | — | 32% compliance score, 263 failed controls |
| Software Protection | 60642 | 3 | Service scheduled event |

---

## Key Skills Demonstrated

- **SIEM Deployment** — Installed and configured Wazuh all-in-one stack
- **Endpoint Monitoring** — Deployed Wazuh agent + Sysmon on Windows
- **Log Ingestion** — Configured custom log sources in ossec.conf
- **Attack Simulation** — Executed recon and brute force from Kali
- **Alert Triage** — Investigated Event ID 4625 brute force chain
- **Vulnerability Assessment** — Identified CVEs and CIS benchmark gaps
- **Documentation** — Structured findings in SOC report format

---

## Evidence — Screenshots

### Wazuh Dashboard — Agent Active
![Wazuh Agent Active](wazuh_screensho.png)

### Threat Hunting — DLL Hijack Detections
![DLL Hijack Detection](wazuh_events_port_scan.png)

### Brute Force Attack — Multiple Logon Failures Detected
![Brute Force Detection](wazuh_logon_failed_dasborad.png)

### Event Detail — Windows Event ID 4625 (AUDIT_FAILURE)
![Event 4625 Detail](wazuh_single_event_detail_failed_logon.png)

### Kali Linux — Nmap Reconnaissance Output
![Nmap Scan](kali_nmap_scan.png)

---## MITRE ATT&CK Mapping

| Technique | ID | Tactic | Detected |
|---|---|---|---|
| Network Service Discovery | T1046 | Discovery | ✅ Nmap scan |
| Brute Force | T1110 | Credential Access | ✅ Rule 60204 |
| Valid Accounts | T1078 | Defense Evasion | ✅ Event 4625 |
| DLL Search Order Hijacking | T1574.001 | Persistence | ✅ Rule 92219 |

---

## Next Steps / Roadmap

- [ ] Deploy Kali attack → Metasploit exploitation detection
- [ ] Add Linux Ubuntu VM as second agent
- [ ] Write custom Wazuh detection rules (XML)
- [ ] Configure active response (auto-block attacking IP)
- [ ] Integrate VirusTotal API for IOC enrichment
- [ ] Add Shuffle SOAR for automated alert response

---

## References

- [Wazuh Documentation](https://documentation.wazuh.com)
- [SwiftOnSecurity Sysmon Config](https://github.com/SwiftOnSecurity/sysmon-config)
- [MITRE ATT&CK Framework](https://attack.mitre.org)
- [Sysmon Events Reference](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)
