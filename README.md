# 🛡️ Enterprise Security Monitoring Lab — Wazuh SIEM/XDR

![Wazuh](https://img.shields.io/badge/Wazuh-4.7.5-blue?style=for-the-badge)
![Ubuntu](https://img.shields.io/badge/Ubuntu-24.04-orange?style=for-the-badge&logo=ubuntu)
![Kali](https://img.shields.io/badge/Kali-Linux-557C94?style=for-the-badge&logo=kalilinux)
![Windows](https://img.shields.io/badge/Windows-3%20Agents-0078D6?style=for-the-badge&logo=windows)
![MITRE](https://img.shields.io/badge/MITRE-ATT%26CK%20Mapped-red?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)

---

## 📌 Objective

This project is **Phase 2** of a two-part security monitoring lab series. Having built a foundational understanding of Host-based Intrusion Detection using OSSEC in Phase 1, this phase upgrades the entire environment to **Wazuh 4.7.5** — the modern enterprise-grade evolution of OSSEC — adding a full SIEM dashboard, MITRE ATT&CK mapping, multi-agent management across independent network segments, and real-time threat visualization.

The lab simulates a real **Security Operations Center (SOC)** environment where a central Wazuh server monitors multiple independent Windows endpoints across different networks, detects live attacks from a dedicated Kali Linux attacker machine, and automatically maps every detection to the MITRE ATT&CK framework.

> **Phase 1:** [OSSEC Host-Based IDS Lab](https://github.com/Emmanuel-cpp/Host-Based-Intrusion-Detection-System-HIDS-Lab-OSSEC.git) — Start here to understand the foundation Wazuh is built on.

---

## 🔗 Why Wazuh — From OSSEC to Enterprise SIEM

OSSEC is the engine. Wazuh is the full vehicle built around it.

Wazuh was chosen deliberately as the upgrade path because it is built directly on top of OSSEC — inheriting the same rule engine, log collection architecture, agent communication protocol, and active response system. Every concept from Phase 1 carries forward. The difference is everything around it.

| Feature | OSSEC | Wazuh |
|---|---|---|
| Log Analysis | Yes | Yes — enhanced with decoders |
| File Integrity Monitoring | Yes | Yes — real-time with hash comparison |
| Active Response | Basic iptables | Advanced with custom scripts |
| Web Interface | Legacy PHP UI (2016) | Modern full SIEM dashboard |
| MITRE ATT&CK Mapping | No | Yes — automatic on every alert |
| Vulnerability Detection | No | Yes — CVE scanning per agent |
| Multi-Agent Dashboard | Text output only | Full visual centralized management |
| Compliance Monitoring | No | PCI-DSS, HIPAA, GDPR built-in |
| Alert Visualization | Raw log files | Interactive charts, graphs, timelines |
| Threat Intelligence | No | Yes — integrated feeds |

---

## 🏗️ Lab Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              NETWORK OVERVIEW                                │
│                                                                               │
│   ┌──────────────────┐                                                        │
│   │    KALI LINUX    │──── SSH brute force ────────────────────────────┐     │
│   │    (Attacker)    │                                                  │     │
│   │   192.168.0.x    │──── SMB/RDP brute force ──────────┐             │     │
│   └──────────────────┘                                   │             │     │
│                                                           ▼             ▼     │
│   ┌──────────────────┐      ┌──────────────────────────────────────────────┐ │
│   │  WINDOWS AGENT 1 │─────▶│              UBUNTU 24.04 LTS                │ │
│   │ windows-endpoint │      │           (Wazuh Server — Brain)             │ │
│   │  192.168.56.1    │      │                                              │ │
│   │  Host-Only Net   │      │  enp0s8: 192.168.56.101 (Host-Only)          │ │
│   └──────────────────┘      │  enp0s9: 192.168.0.150  (Bridged WiFi)       │ │
│                              │                                              │ │
│                              │  Wazuh Manager  — rule engine + AR          │ │
│                              │  Wazuh Indexer  — OpenSearch storage        │ │
│                              │  Wazuh Dashboard— real-time SIEM UI         │ │
│                              └──────────────────┬───────────────────────────┘ │
│                                                  │ receives all agent logs     │
│                              ┌───────────────────┴───────────────┐            │
│                              │                                   │            │
│   ┌──────────────────┐       │                   ┌──────────────────────────┐ │
│   │  WINDOWS AGENT 2 │───────┘                   │     WINDOWS AGENT 3      │ │
│   │ windows-endpoint │                            │  windows-agent-class-1   │ │
│   │      -angel      │                            │     192.168.0.195        │ │
│   │  192.168.0.200   │                            │  Independent External    │ │
│   │  WiFi Network    │                            │       Laptop             │ │
│   └──────────────────┘                            └──────────────────────────┘ │
└───────────────────────────────────────────────────────────────────────────────┘
```

### Network Segments

| Network | Subnet | Purpose |
|---|---|---|
| Host-Only | 192.168.56.0/24 | VirtualBox internal — Ubuntu ↔ Windows host |
| Bridged WiFi | 192.168.0.0/24 | Real network — Ubuntu ↔ external machines |

The Ubuntu Wazuh server has **two active network interfaces** simultaneously — one facing the VirtualBox Host-Only network and one bridged to the real WiFi network. This allows it to receive agent logs from machines on completely different network segments through a single central dashboard.

---

## 🖥️ Agent Inventory

| ID | Agent Name | IP Address | OS | Network | Status |
|---|---|---|---|---|---|
| 000 | ubuntu (server) | 127.0.0.1 | Ubuntu 24.04 LTS | Local | Active/Local |
| 001 | windows-endpoint | 192.168.56.1 | Windows 11 Pro 10.0.26200 | Host-Only | Active |
| 002 | windows-endpoint-angel | 192.168.0.200 | Windows 10 Pro 10.0.19045 | WiFi | Active |
| 003 | windows-agent-class-1 | 192.168.0.195 | Windows 11 Pro 10.0.26100 | WiFi External | Active |

**Agent 003 is a fully independent external laptop** — not part of the VirtualBox environment and not owned by the lab setup. It connects to the Wazuh server purely over a shared WiFi network, demonstrating real distributed endpoint monitoring across independently managed machines.

---

## 🛠️ Tools & Technologies

| Tool | Version | Purpose |
|---|---|---|
| **Wazuh Manager** | 4.7.5 | Central SIEM/XDR engine — rule analysis, active response |
| **Wazuh Indexer** | 4.7.5 | OpenSearch-based alert indexing and storage |
| **Wazuh Dashboard** | 4.7.5 | Real-time browser-based security visualization |
| **Wazuh Agent** | 4.7.5 | Installed on all Windows endpoints |
| **Hydra** | v9.6 | SSH, SMB, and RDP brute force simulation |
| **Kali Linux** | Rolling | Dedicated attacker machine |
| **VirtualBox** | 7.2.4 | Virtualization platform |
| **UFW / iptables** | — | Host firewall — managed by active response |

---

## ⚙️ Wazuh Components Explained

| Component | Role |
|---|---|
| **Wazuh Manager** | The brain — receives logs from all agents, runs them through the rule engine, fires alerts, triggers active response |
| **Wazuh Indexer** | Based on OpenSearch — stores and indexes all alerts so the dashboard can query them |
| **Wazuh Dashboard** | Browser-based UI — visualizes alerts, agents, MITRE ATT&CK maps, compliance status in real time |
| **Wazuh Agent** | Installed on each endpoint — collects logs, monitors files, sends everything to the manager |

---

## 🚀 Installation & Setup

### System Requirements
```
RAM:    4GB minimum (2GB swap file added for indexer stability)
Disk:   20GB free minimum
CPU:    2 cores minimum
OS:     Ubuntu 24.04 LTS
```

### Step 1 — Add Swap File (Critical for 4GB RAM systems)
```bash
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
free -h
```

### Step 2 — Stop OSSEC (If Phase 1 was completed)
```bash
sudo /var/ossec/bin/ossec-control stop
sudo systemctl disable apache2
```

### Step 3 — Download Wazuh Installer
```bash
cd /tmp
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh
curl -sO https://packages.wazuh.com/4.7/config.yml
```

### Step 4 — Configure Installation
```bash
nano /tmp/config.yml
```

```yaml
nodes:
  indexer:
    - name: node-1
      ip: "127.0.0.1"
  server:
    - name: wazuh-1
      ip: "127.0.0.1"
  dashboard:
    - name: dashboard
      ip: "127.0.0.1"
```

### Step 5 — Run All-in-One Installer
```bash
sudo bash /tmp/wazuh-install.sh -a -i
```

Installation takes 15-20 minutes. At completion the terminal shows:
```
INFO: You can access the web interface https://127.0.0.1
    User: admin
    Password: <auto-generated — save this immediately>
```

### Step 6 — Reduce Indexer Memory (For 4GB RAM systems)
```bash
sudo nano /etc/wazuh-indexer/jvm.options
# Change -Xms1g / -Xmx1g → -Xms512m / -Xmx512m
sudo systemctl restart wazuh-indexer
```

### Step 7 — Open Firewall Ports
```bash
sudo ufw allow 443/tcp
sudo ufw allow 1514/tcp
sudo ufw allow 1514/udp
sudo ufw allow 1515/tcp
sudo ufw allow 55000/tcp
sudo ufw reload
```

### Step 8 — Make Bridged Network IP Persistent
```bash
sudo nano /etc/netplan/01-network-manager-all.yaml
```

Add:
```yaml
network:
  version: 2
  ethernets:
    enp0s9:
      dhcp4: no
      addresses:
        - 192.168.0.150/24
      routes:
        - to: default
          via: 192.168.0.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
```

```bash
sudo netplan apply
```

### Step 9 — Verify All Services Running
```bash
sudo systemctl status wazuh-manager wazuh-indexer wazuh-dashboard --no-pager | grep Active
```

Expected:
```
Active: active (running)
Active: active (running)
Active: active (running)
```

### Step 10 — Access Dashboard
```
From Ubuntu:          https://127.0.0.1
From Windows host:    https://192.168.56.101
From WiFi network:    https://192.168.0.150

Username: admin
Password: <generated during install>
```

---

## 📱 Adding Windows Agents

### From the Wazuh Dashboard
1. Click **Agents** → **Deploy new agent**
2. Select **Windows** → **x86_64**
3. Set **Manager IP** to the correct interface IP for that machine's network
4. Set a unique **Agent name**
5. Copy the generated PowerShell command

### Run on Windows Machine (PowerShell as Administrator)
```powershell
# Paste the generated command — example:
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.7.5-1.msi `
  -OutFile $env:tmp\wazuh-agent.msi
msiexec.exe /i $env:tmp\wazuh-agent.msi /q `
  WAZUH_MANAGER='192.168.0.150' `
  WAZUH_AGENT_NAME='windows-endpoint-angel'

# Start the agent
NET START WazuhSvc
```

### Enable Full Windows Audit Policies
```powershell
auditpol /set /category:"Account Logon" /success:enable /failure:enable
auditpol /set /category:"Logon/Logoff" /success:enable /failure:enable
auditpol /set /category:"Account Management" /success:enable /failure:enable
auditpol /set /category:"Privilege Use" /success:enable /failure:enable
auditpol /set /category:"Object Access" /success:enable /failure:enable
auditpol /set /category:"Detailed Tracking" /success:enable /failure:enable
```

### Verify Agent Connected on Ubuntu
```bash
sudo /var/ossec/bin/agent_control -l
```

---

## 📸 Lab Setup

### All 3 Agents Active on Wazuh Dashboard
![Agents Dashboard](screenshots/wazuh-agents-dashboard.png)
> Wazuh simultaneously managing 3 independent Windows endpoints — Windows 11 Pro (Host-Only), Windows 10 Pro (WiFi), and Windows 11 Pro (external independent laptop on WiFi). Agent 003 is a completely independent machine not part of the VirtualBox setup.

---

## 🔴 Attack Demonstrations

### Demo 1 — SSH Brute Force on Ubuntu Server

**From Kali — 14 million password attempts against Ubuntu:**
```bash
hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://192.168.0.150 -t 4 -V
```

![Hydra SSH Attack](screenshots/hydra-ssh-bruteforce.png)
> Hydra actively hammering 192.168.0.150 — 14,344,399 login attempts from the rockyou wordlist, 4 parallel threads, with verbose output showing every attempt in real time

**Wazuh detected and escalated through alert levels:**

| Rule | Level | Description |
|---|---|---|
| 5760 | 5 | sshd: authentication failed |
| 5763 | 10 | sshd: brute force trying to get access |

![SSH Brute Force Level 10](screenshots/wazuh-ssh-bruteforce-level10.png)
> Rule 5763 Level 10 firing repeatedly — Wazuh correlating individual authentication failures into a brute force pattern. MITRE technique T1110 Credential Access mapped automatically.

**MITRE ATT&CK Visualization:**
![MITRE ATT&CK Donut](screenshots/wazuh-mitre-donut-chart.png)
> Wazuh automatically categorized the attack into Password Guessing, SSH, and Brute Force techniques — no manual tagging required

---

### Demo 2 — Windows Endpoint Attack (External Machine)

**From Kali — SMB and authentication brute force against windows-endpoint-angel:**
```bash
hydra -l administrator -P /usr/share/wordlists/rockyou.txt smb://192.168.0.200 -t 4 -V
hydra -l administrator -P /usr/share/wordlists/rockyou.txt rdp://192.168.0.200 -t 4 -V
```

![Windows T1078 Alerts](screenshots/wazuh-windows-endpoint-alerts.png)
> MITRE technique T1078 (Valid Accounts) mapped across four tactics simultaneously — Defense Evasion, Persistence, Privilege Escalation, and Initial Access. Wazuh correlating Windows audit events from the remote agent in real time.

![Windows Audit Failure Flood](screenshots/wazuh-windows-audit-failure.png)
> Windows audit failure events flooding in from windows-endpoint agent — Rule 60104 firing continuously showing the volume of authentication attempts being logged and forwarded to the Wazuh server

---

### Demo 3 — Level 12 Critical Alert — Agent Queue Flood

The combined volume of simultaneous attacks across multiple agents overwhelmed the Wazuh event processing queue — generating a **Level 12 Critical alert**. This is a realistic SOC scenario where an attacker deliberately floods logging systems to exhaust resources and obscure malicious activity.

![Level 12 Queue Flooded](screenshots/wazuh-level12-queue-flooded.png)
> Rule 204 Level 12 — Agent event queue is flooded. Top 5 alerts panel dominated entirely by agent_flooding. PCI DSS requirement 10.6.1 triggered automatically — compliance framework flagging the event without any manual configuration.

![3098 Alerts Dashboard](screenshots/wazuh-3098-alerts-dashboard.png)
> 3,098 total alerts generated from windows-endpoint-angel alone in a single session — 1 confirmed Level 12 critical alert. Alert groups evolution chart showing the sharp attack spike. Alert severity breakdown showing levels 5, 7, 9, and 12 all represented.

---

### Demo 4 — Multi-Agent MITRE ATT&CK Coverage

Every attack across all agents was automatically mapped to the MITRE ATT&CK framework:

| Technique | ID | Tactic | Detected On |
|---|---|---|---|
| Brute Force | T1110 | Credential Access | Ubuntu — SSH attack |
| Password Guessing | T1110.001 | Credential Access | Ubuntu — SSH attack |
| Remote Services SSH | T1021.004 | Lateral Movement | Ubuntu — SSH attack |
| Valid Accounts | T1078 | Defense Evasion, Persistence, Privilege Escalation, Initial Access | windows-endpoint-angel |

![MITRE Attack Map](screenshots/wazuh-mitre-attack-map.png)
> Wazuh Security Events dashboard filtered by authentication failures — showing MITRE technique and tactic columns automatically populated. 16 total authentication failures, 16 mapped to Credential Access tactic.

---

## 📊 Results Summary

| Metric | Result |
|---|---|
| Total agents monitored | 4 (Ubuntu server + 3 Windows endpoints) |
| Independent external endpoints | 2 (not part of VirtualBox) |
| Network segments covered | 2 (Host-Only 192.168.56.x + WiFi 192.168.0.x) |
| Windows OS versions monitored | Windows 10 Pro + Windows 11 Pro (x2) |
| Total alerts — single agent session | 3,098 |
| Highest alert level triggered | Level 12 — Critical |
| MITRE ATT&CK techniques auto-detected | T1110, T1110.001, T1021.004, T1078 |
| MITRE tactics covered | Credential Access, Lateral Movement, Defense Evasion, Persistence, Privilege Escalation, Initial Access |
| Compliance frameworks triggered | PCI-DSS 10.6.1 |
| Rule IDs fired | 5760, 5763, 60104, 60107, 204 |

---

## 🔑 Key Takeaways

**Wazuh as a SOC Platform**
A single Wazuh server replicated the core monitoring capability of an enterprise SOC — centralized log collection, real-time multi-agent detection, automatic MITRE ATT&CK classification, and compliance monitoring all running simultaneously on a 4GB RAM virtual machine.

**Multi-Network Distributed Monitoring**
Connecting agents across two different network segments — a VirtualBox Host-Only network and a real WiFi network — reflects how enterprise environments actually work. Endpoints on different subnets, VLANs, and physical locations all reporting to one central server through different network interfaces.

**Independent External Agent**
Agent 003 was a completely independent Windows laptop connected only over shared WiFi. This demonstrates that Wazuh agent deployment scales to any machine that can reach the manager IP — no VPN, no domain membership, no special configuration beyond the agent install.

**MITRE ATT&CK Without Manual Tagging**
Every single detection was automatically classified into MITRE ATT&CK techniques and tactics. This is the standard framework used by SOC analysts globally — having it map automatically means alerts are immediately actionable and communicable without additional analysis overhead.

**Alert Correlation and Severity Escalation**
A single failed SSH login is Level 5 noise. Eight failed logins in 30 seconds from the same source is a Level 10 brute force. Flooding the agent queue under combined attack load is Level 12 critical. This escalation chain demonstrates exactly how SIEM tools transform raw log volume into prioritized threat intelligence.

**The OSSEC Foundation Matters**
Wazuh is built on OSSEC. The same rule IDs (5760, 5763) fire in both systems. The same log collection paths apply. The same agent-server communication protocol is used. Building Phase 1 on raw OSSEC made every aspect of Wazuh immediately understandable — the dashboard is visualization on top of a familiar engine.

---

## 🔧 Maintenance Commands

```bash
# Start all services
sudo systemctl start wazuh-manager wazuh-indexer wazuh-dashboard

# Check all services status
sudo systemctl status wazuh-manager wazuh-indexer wazuh-dashboard --no-pager | grep Active

# Restart all services
sudo systemctl restart wazuh-manager wazuh-indexer wazuh-dashboard

# Check all agents
sudo /var/ossec/bin/agent_control -l

# Watch live alerts
sudo tail -f /var/ossec/logs/alerts/alerts.log

# Watch active response
sudo tail -f /var/ossec/logs/active-responses.log
```

---

## 📁 Repository Structure

```
Wazuh-Enterprise-Security-Lab/
├── README.md
├── screenshots/
│   ├── wazuh-agents-dashboard.png        ← All 3 agents active
│   ├── hydra-ssh-bruteforce.png          ← Kali attacking Ubuntu
│   ├── wazuh-ssh-bruteforce-level10.png  ← Rule 5763 Level 10
│   ├── wazuh-mitre-donut-chart.png       ← MITRE ATT&CK donut
│   ├── wazuh-mitre-attack-map.png        ← Auth failures with MITRE mapping
│   ├── wazuh-windows-endpoint-alerts.png ← T1078 Windows alerts
│   ├── wazuh-windows-audit-failure.png   ← Windows audit flood
│   ├── wazuh-level12-queue-flooded.png   ← Rule 204 Level 12
│   └── wazuh-3098-alerts-dashboard.png   ← 3098 alerts spike chart
└── configs/
    └── ossec.conf
```

---

## 🔗 References

- [Wazuh Official Documentation](https://documentation.wazuh.com)
- [Wazuh GitHub Repository](https://github.com/wazuh/wazuh)
- [MITRE ATT&CK Framework](https://attack.mitre.org)
- [Phase 1 — OSSEC Host-Based IDS Lab](https://github.com/Emmanuel-cpp/Host-Based-Intrusion-Detection-System-HIDS-Lab-OSSEC.git)

---

## 👨‍💻 Author

**Emmanuel Siamoonga**
Bachelor of Science in Computer Science — The Copperbelt University, Kitwe, Zambia

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue?style=flat&logo=linkedin)](https://www.linkedin.com/in/emmanuel-siamoonga-98b30929b/)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-black?style=flat&logo=github)](https://github.com/Emmanuel-cpp)

> *"Security is not a product, but a process."* — Bruce Schneier
