# Home SOC Lab — Wazuh SIEM + Sysmon + Attack Simulation

A fully functional home Security Operations Center (SOC) lab simulating
real-world attacks and practicing threat detection, log analysis, and
incident response — SIEM deployment, agent configuration, attack
simulation, and alert triage.

## Lab Architecture

| Machine | OS | IP | Role |
|---|---|---|---|
| Wazuh Server | Ubuntu 22.04 LTS | 192.168.1.119 | SIEM, indexer, dashboard |
| DC01 | Windows Server 2022 | 192.168.1.122 | Domain Controller |
| Windows Agent | Windows 10 Pro | 192.168.1.120 | Monitored endpoint |
| Kali Attacker | Kali Linux | 192.168.1.105 | Attack simulation |

## Labs

| Lab | Focus | Key Techniques |
|---|---|---|
| [Lab 1 — Wazuh SIEM](./lab1-wazuh-siem/) | SIEM deployment, recon & brute-force detection | T1046, T1110, T1078, T1574.001 |
| [Lab 2 — Active Directory Attacks](./lab2-active-directory/) | Kerberoasting, privilege escalation mapping | T1558.003 |
| [Lab 3 — Network Traffic Analysis](./lab3-network-traffic-analysis/) | Suricata/Zeek network sensor deployment | Network IDS/IPS |

## Key Skills Demonstrated

SIEM deployment · endpoint monitoring (Sysmon) · attack simulation ·
alert triage · MITRE ATT&CK mapping · vulnerability assessment

See each lab folder for detailed write-ups, commands, and evidence.
