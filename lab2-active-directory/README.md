# Lab 2 — Active Directory Attack Lab

![Status](https://img.shields.io/badge/Status-Active-brightgreen)
![AD](https://img.shields.io/badge/AD-Windows%20Server%202022-blue)
![Attacks](https://img.shields.io/badge/Attacks-Kerberoasting%20%7C%20NTLM-red)

## Overview

Active Directory attack simulation lab built on top of the Wazuh SOC lab.
Deploys a full Windows Server 2022 Domain Controller with intentionally 
vulnerable AD accounts to simulate real enterprise attack scenarios.
All attacks detected and logged by Wazuh SIEM in real time.

---

## Lab Architecture

| Machine | OS | IP | Role |
|---|---|---|---|
| Wazuh Server | Ubuntu 22.04 | 192.168.1.119 | SIEM |
| DC01 | Windows Server 2022 | 192.168.1.122 | Domain Controller |
| WORKSTATION01 | Windows 10 Pro | 192.168.1.120 | Domain workstation |
| Kali Linux | Kali | 192.168.1.105 | Attacker |

**Domain:** `SOC.LAB`

---

## Vulnerable Accounts Created

| Username | Role | Password | Vulnerability |
|---|---|---|---|
| jsmith | Domain User | Password123 | Weak password |
| jdoe | Domain User | Winter2024! | Weak password |
| badmin | **Domain Admin** | Admin123! | Privileged account |
| svc-sql | Service Account | SqlService1! | Kerberoastable SPN |

**SPN registered:** `MSSQLSvc/dc01.soc.lab:1433` on `svc-sql`

---

## Attack Simulations & Detections

### Attack 1 — Kerberoasting

**What it is:** Attacker requests Kerberos TGS tickets for service accounts
then cracks them offline to recover plaintext passwords.

**Command (Kali — Impacket):**
```bash
impacket-GetUserSPNs soc.lab/jsmith:Password123 -dc-ip 192.168.1.122 -request
```

**Result:** TGS ticket hash returned for `svc-sql`:

## Evidence — BloodHound AD Enumeration

### JSMITH — Pathfinding to Domain Admins
![JSMITH Pathfinding](../JSMITH_PATHFINDING_DOMAIN_ADMINS_SOC_LAB.png)

### JSMITH — Privilege Zones
![JSMITH Privilege Zones](../JSMITH_privileged_szones.png)

### BADMIN — Domain Admin Membership Chain
![BADMIN](BADMIN_SOC_LAB.png)

### SVC-SQL — Kerberoastable Service Account
![SVC-SQL Kerberoastable](../SVC-SQL_SOC_LAB.png)

### Hashcat — Kerberos TGS Hash Cracked
![Hash Cracked](../Password_carcked_hashcat.png)
