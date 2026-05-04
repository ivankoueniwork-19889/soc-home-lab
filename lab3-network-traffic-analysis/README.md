# Lab 3 — Network Traffic Analysis with Suricata IDS + Zeek

![Status](https://img.shields.io/badge/Status-Active-brightgreen)
![Suricata](https://img.shields.io/badge/Suricata-7.0.10-blue)
![Zeek](https://img.shields.io/badge/Zeek-Docker-teal)
![Platform](https://img.shields.io/badge/Platform-Ubuntu%2025.10-orange)

## Overview

Network traffic analysis lab deploying Suricata IDS and Zeek NSM on a 
dedicated Ubuntu sensor VM. Monitors all lab network traffic, detects 
attack patterns using 65,000+ Emerging Threats signatures, and ships 
alerts to Wazuh SIEM in real time.

---

## Lab Architecture

| Machine | OS | IP | Role |
|---|---|---|---|
| Wazuh Server | Amazon Linux 2023 | 192.168.1.119 | SIEM |
| Network Sensor | Ubuntu 25.10 | 192.168.1.123 | Suricata + Zeek IDS |
| Kali Linux | Kali | 192.168.1.105 | Attacker |
| WORKSTATION01 | Windows 10 | 192.168.1.120 | Monitored endpoint |
| DC01 | Windows Server 2022 | 192.168.1.122 | Domain Controller |

---

## Tools & Technologies

| Tool | Version | Purpose |
|---|---|---|
| Suricata | 7.0.10 | IDS/IPS — signature-based detection |
| Zeek | Docker CE | NSM — connection/DNS/HTTP logging |
| Emerging Threats ruleset | 65,823 rules | Attack signatures |
| Wazuh Agent | 4.9.2 | Log shipping to SIEM |
| Nmap | 7.95 | Attack simulation |

---

## Setup

### Suricata Configuration
- Interface: `enp0s3`
- HOME_NET: `[192.168.0.0/16,10.0.0.0/8,172.16.0.0/12]`
- Rules: Emerging Threats (65,823 signatures)
- Output: `fast.log` + `eve.json`

### Zeek Configuration
- Deployed via Docker with `--network host`
- Logs: `conn.log`, `dns.log`, `http.log`
- Output path: `/var/log/zeek/`

### Wazuh Integration
- Agent shipping Suricata `eve.json` to Wazuh manager
- Wazuh ingesting Zeek connection logs
- 170 events captured in first 15 minutes

---

## Attack Simulations & Detections

### Attack 1 — Nmap Network Reconnaissance

**Command (Kali):**
```bash
sudo nmap -sS -sV -p 22,80,443,8080 192.168.1.123
sudo nmap -sS -p 1-1000 --min-rate 1000 192.168.1.123
```

**Suricata Detections:**
| Rule | Description | Priority |
|---|---|---|
| 1:2056212:1 | ET INFO Internet Printing Protocol | 3 |
| 1:2027695:5 | ET INFO Cloudflare DNS over HTTPS | 3 |
| 1:2200025:2 | SURICATA ICMPv4 unknown code | 3 |
| 1:2100498:7 | GPL ATTACK_RESPONSE id check returned root | 2 |

**Wazuh Dashboard:**
- Agent: `network-sensor (003)`
- 170 events in 15 minutes
- Rule 40704 — Systemd failure events

---

## MITRE ATT&CK Mapping

| Technique | ID | Tactic | Detected |
|---|---|---|---|
| Network Service Discovery | T1046 | Discovery | ✅ Suricata ICMPv4 |
| Active Scanning | T1595 | Reconnaissance | ✅ ET INFO rules |
| DNS over HTTPS | T1071.004 | C2 | ✅ Rule 2027695 |

---

## Key Skills Demonstrated

- **IDS deployment** — Suricata 7.0.10 on Ubuntu with ET ruleset
- **NSM deployment** — Zeek via Docker monitoring live traffic
- **Rule management** — 65,823 Emerging Threats signatures loaded
- **SIEM integration** — Suricata/Zeek logs shipped to Wazuh
- **Traffic analysis** — Real-time packet inspection on enp0s3
- **Attack detection** — Nmap, ICMP, DNS anomaly detection

---

## Evidence — Screenshots

### Suricata fast.log — Live Detections
![Suricata Detections](https://raw.githubusercontent.com/ivankoueniwork-19889/soc-home-lab/main/ET_SCAN__1-1000_--MIN.png)

### Wazuh Dashboard — network-sensor Agent (170 events)
![Wazuh Network Sensor](https://raw.githubusercontent.com/ivankoueniwork-19889/soc-home-lab/main/wazuh_network_wazuh_feed.png)

### Kali Nmap Scan Against Network Sensor
![Nmap Scan](https://raw.githubusercontent.com/ivankoueniwork-19889/soc-home-lab/main/kali_nmap_scanon_network_sendor.png)

---

## Next Steps

- [ ] Configure Zeek to output structured JSON logs
- [ ] Write custom Suricata rules for lab-specific threats
- [ ] Add Suricata IPS mode to block attacking IPs
- [ ] Integrate Zeek DNS logs into Wazuh for C2 detection
- [ ] Deploy Security Onion for full NSM platform

---

## References

- [Suricata Documentation](https://docs.suricata.io)
- [Zeek Documentation](https://docs.zeek.org)
- [Emerging Threats Rules](https://rules.emergingthreats.net)
- [Wazuh Suricata Integration](https://documentation.wazuh.com)
