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

The lab simulates a real **Security Operations Center (SOC)** environment where a central Wazuh server monitors multiple independent Windows endpoints across different networks, detects live attacks launched from a dedicated Kali Linux attacker machine, and automatically maps every detection to the MITRE ATT&CK framework.

Key areas covered:

- SSH brute-force detection with MITRE ATT&CK technique mapping
- Windows endpoint monitoring across independent machines on different networks
- Multi-agent centralized management from a single Wazuh dashboard
- Real-time alert visualization with severity escalation up to Level 12 Critical
- Compliance framework monitoring — PCI-DSS triggered automatically

> **Phase 1:** [OSSEC Host-Based IDS Lab](https://github.com/Emmanuel-cpp/Host-Based-Intrusion-Detection-System-HIDS-Lab-OSSEC.git)

---

## 🔗 From OSSEC to Wazuh

OSSEC is the foundation. Wazuh is built directly on top of it — inheriting the same rule engine, log collection architecture, and agent communication protocol. Every concept from Phase 1 carries forward. The difference is everything built around it.

| Feature | OSSEC | Wazuh |
|---|---|---|
| Log Analysis | Yes | Yes — enhanced |
| File Integrity Monitoring | Yes | Yes — real-time |
| Active Response | Basic | Advanced |
| Web Interface | Legacy PHP UI (2016) | Modern full SIEM dashboard |
| MITRE ATT&CK Mapping | No | Yes — automatic |
| Vulnerability Detection | No | Yes — CVE scanning |
| Multi-Agent Dashboard | Text output | Full visual management |
| Compliance Monitoring | No | PCI-DSS, HIPAA, GDPR |
| Alert Visualization | Raw log files | Charts, graphs, timelines |

---

## 🏗️ Lab Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           NETWORK OVERVIEW                               │
│                                                                           │
│   ┌──────────────────┐                                                    │
│   │    KALI LINUX    │──── SSH brute force ──────────────────────┐       │
│   │    (Attacker)    │                                            │       │
│   │   192.168.0.x    │──── SMB/RDP brute force ──────┐           │       │
│   └──────────────────┘                               │           │       │
│                                                       ▼           ▼       │
│   ┌──────────────────┐    ┌────────────────────────────────────────────┐ │
│   │ WINDOWS AGENT 1  │───▶│              UBUNTU 24.04 LTS              │ │
│   │ windows-endpoint │    │           (Wazuh Server — Brain)           │ │
│   │  192.168.56.1    │    │                                            │ │
│   │  Host-Only Net   │    │  enp0s8: 192.168.56.101  (Host-Only)       │ │
│   └──────────────────┘    │  enp0s9: 192.168.0.150   (Bridged WiFi)    │ │
│                            │                                            │ │
│                            │  Wazuh Manager  — rule engine + AR        │ │
│                            │  Wazuh Indexer  — OpenSearch storage      │ │
│                            │  Wazuh Dashboard— real-time SIEM UI       │ │
│                            └───────────────────┬────────────────────────┘ │
│                                                 │ receives all agent logs  │
│                            ┌────────────────────┴──────────────────┐      │
│                             │                                       │      │
│   ┌──────────────────┐      │                 ┌─────────────────────────┐ │
│   │ WINDOWS AGENT 2  │──────┘                 │    WINDOWS AGENT 3      │ │
│   │ windows-endpoint │                        │  windows-agent-class-1  │ │
│   │     -angel       │                        │     192.168.0.195       │ │
│   │  192.168.0.200   │                        │  Independent External   │ │
│   │   WiFi Network   │                        │        Laptop           │ │
│   └──────────────────┘                        └─────────────────────────┘ │
└───────────────────────────────────────────────────────────────────────────┘
```

| Machine | Role | OS | IP Address | Network |
|---|---|---|---|---|
| Kali Linux | Attacker | Kali Linux (Rolling) | 192.168.0.x | WiFi |
| Ubuntu VM | Wazuh Server | Ubuntu 24.04 LTS | 192.168.56.101 / 192.168.0.150 | Host-Only + Bridged |
| Windows Agent 1 | Monitored Endpoint | Windows 11 Pro | 192.168.56.1 | Host-Only |
| Windows Agent 2 | Monitored Endpoint | Windows 10 Pro | 192.168.0.200 | WiFi |
| Windows Agent 3 | Independent External Laptop | Windows 11 Pro | 192.168.0.195 | WiFi |

### Role of Each Machine

**🔴 Kali Linux — The Attacker**
Simulates a real-world threat actor launching attacks against the monitored network. Used Hydra for SSH, SMB and RDP brute-force attacks to generate detectable malicious activity across multiple endpoints simultaneously.

**🟢 Ubuntu VM — The Wazuh Server**
The brain of the entire lab. Runs the Wazuh Manager, Indexer and Dashboard simultaneously. Collects logs from all agents across two network segments, fires detection rules, triggers Active Response, and visualizes everything in real time through the browser-based dashboard.

**🟡 Windows Agent 1 — VirtualBox Host Machine**
The Windows machine hosting the VirtualBox environment. Runs the Wazuh agent and forwards Windows Event Logs and security events to the Ubuntu Wazuh server over the Host-Only network.

**🟠 Windows Agent 2 — WiFi Endpoint**
A Windows 10 machine connected over the real WiFi network. Demonstrates monitoring of endpoints outside the VirtualBox environment using the bridged network interface.

**🔵 Windows Agent 3 — Independent External Laptop**
A completely independent Windows 11 laptop not part of the VirtualBox setup. Connected to the Wazuh server purely over shared WiFi — demonstrating real-world distributed endpoint monitoring with no special configuration beyond the agent install.

---

## 🛠️ Tools & Technologies

| Tool | Version | Purpose |
|---|---|---|
| **Wazuh Manager** | 4.7.5 | Central SIEM/XDR engine |
| **Wazuh Indexer** | 4.7.5 | OpenSearch-based alert storage |
| **Wazuh Dashboard** | 4.7.5 | Real-time browser-based visualization |
| **Wazuh Agent** | 4.7.5 | Endpoint monitoring on all Windows machines |
| **Hydra** | v9.6 | SSH, SMB and RDP brute-force simulation |
| **Kali Linux** | Rolling | Dedicated attack platform |
| **VirtualBox** | 7.2.4 | Virtualization platform |
| **UFW / iptables** | — | Firewall managed by Active Response |

---

## ⚙️ Wazuh Features Demonstrated

| Feature | Description |
|---|---|
| **Log Analysis** | Monitors auth.log, syslog, Windows Event Logs across all agents |
| **Multi-Agent Management** | Centralized monitoring of 3 Windows endpoints from one dashboard |
| **MITRE ATT&CK Mapping** | Every alert automatically mapped to technique and tactic |
| **Alert Correlation** | Individual events correlated into pattern-based severity escalation |
| **Active Response** | Automated IP blocking via iptables on brute force detection |
| **Compliance Monitoring** | PCI-DSS requirements triggered and flagged automatically |
| **Real-Time Dashboard** | Live alert visualization with charts, graphs and agent status |

---

## 🚀 Installation & Setup

### Prerequisites
- Ubuntu 24.04 VM — minimum 4GB RAM, 20GB disk, 2 CPU cores
- 2GB swap file required for Wazuh Indexer stability on 4GB RAM
- OSSEC stopped if Phase 1 was completed

### Step 1 — Add Swap File
```bash
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

### Step 2 — Stop OSSEC
```bash
sudo /var/ossec/bin/ossec-control stop
sudo systemctl disable apache2
```

### Step 3 — Download and Run Wazuh Installer
```bash
cd /tmp
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh
curl -sO https://packages.wazuh.com/4.7/config.yml
sudo bash /tmp/wazuh-install.sh -a -i
```

Installation takes 15-20 minutes. At completion:
```
INFO: You can access the web interface https://127.0.0.1
    User: admin
    Password: <auto-generated — save immediately>
```

### Step 4 — Reduce Indexer Memory (4GB RAM systems)
```bash
sudo nano /etc/wazuh-indexer/jvm.options
# Change -Xms1g / -Xmx1g to -Xms512m / -Xmx512m
sudo systemctl restart wazuh-indexer
```

### Step 5 — Open Firewall Ports
```bash
sudo ufw allow 443/tcp
sudo ufw allow 1514/tcp
sudo ufw allow 1514/udp
sudo ufw allow 1515/tcp
sudo ufw reload
```

### Step 6 — Make Bridged Network IP Persistent
```bash
sudo nano /etc/netplan/01-network-manager-all.yaml
```
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

### Step 7 — Verify All Services Running
```bash
sudo systemctl status wazuh-manager wazuh-indexer wazuh-dashboard --no-pager | grep Active
```

### Step 8 — Access Dashboard
```
From Ubuntu:        https://127.0.0.1
From Windows host:  https://192.168.56.101
From WiFi network:  https://192.168.0.150

Username: admin
Password: <generated during install>
```

### Step 9 — Add Windows Agents
On the **Wazuh Dashboard:**
1. Click **Agents** → **Deploy new agent**
2. Select **Windows** → set Manager IP → set Agent name
3. Copy the generated PowerShell command

On the **Windows machine — PowerShell as Administrator:**
```powershell
# Paste the generated command, then:
NET START WazuhSvc
```

### Step 10 — Verify All Agents Connected
```bash
sudo /var/ossec/bin/agent_control -l
```

Expected output:
```
ID: 000, Name: ubuntu (server),          IP: 127.0.0.1,     Active/Local
ID: 001, Name: windows-endpoint,         IP: 192.168.56.1,  Active
ID: 002, Name: windows-endpoint-angel,   IP: 192.168.0.200, Active
ID: 003, Name: windows-agent-class-1,    IP: 192.168.0.195, Active
```

---

## 📸 Lab Setup

### All 3 Agents Active on Wazuh Dashboard
![Agents Dashboard](screenshots/wazuh-agents-dashboard.png)
> Wazuh simultaneously managing 3 independent Windows endpoints — Windows 11 Pro (Host-Only), Windows 10 Pro (WiFi), and an independent external Windows 11 Pro laptop (WiFi). Agent 003 is a completely independent machine not part of the VirtualBox setup.

---

## 🔴 Demo 1 — SSH Brute Force Detection

**Attack from Kali — 14 million password attempts against Ubuntu:**
```bash
hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://192.168.0.150 -t 4 -V
```

![Hydra SSH Attack](screenshots/hydra-ssh-bruteforce.png)
> Hydra actively hammering 192.168.0.150 with the full rockyou wordlist — 14,344,399 attempts, 4 parallel threads

**Alerts generated:**
```
Rule 5760  (Level 5)  → sshd: authentication failed
Rule 5763  (Level 10) → sshd: brute force trying to get access to the system
```

![SSH Brute Force Level 10](screenshots/wazuh-ssh-bruteforce-level10.png)
> Rule 5763 Level 10 firing repeatedly — Wazuh correlating authentication failures into a brute force pattern. MITRE T1110 Credential Access + T1021.004 Lateral Movement mapped automatically.

---

## 🔴 Demo 2 — Windows Endpoint Attack

**SMB and RDP brute force against external Windows machine:**
```bash
hydra -l administrator -P /usr/share/wordlists/rockyou.txt smb://192.168.0.200 -t 4 -V
hydra -l administrator -P /usr/share/wordlists/rockyou.txt rdp://192.168.0.200 -t 4 -V
```

![Windows T1078 Alerts](screenshots/wazuh-windows-endpoint-alerts.png)
> MITRE technique T1078 (Valid Accounts) automatically mapped across four tactics — Defense Evasion, Persistence, Privilege Escalation, and Initial Access. Windows audit events forwarded from the remote agent and analyzed in real time.

![Windows Audit Failure](screenshots/wazuh-windows-audit-failure.png)
> Rule 60104 firing continuously — Windows audit failure events flooding in from the windows-endpoint agent showing the volume of authentication attempts being logged and forwarded to the Wazuh server

---

## 🔴 Demo 3 — Level 12 Critical Alert

The combined volume of simultaneous attacks across multiple agents overwhelmed the Wazuh event processing queue — generating a **Level 12 Critical alert**. This mirrors a real SOC scenario where attackers deliberately flood logging systems to exhaust resources and obscure malicious activity.

![Level 12 Queue Flooded](screenshots/wazuh-level12-queue-flooded.png)
> Rule 204 Level 12 — Agent event queue is flooded. Top 5 alerts panel dominated by agent_flooding. PCI-DSS requirement 10.6.1 triggered automatically.

![3098 Alerts Dashboard](screenshots/wazuh-3098-alerts-dashboard.png)
> 3,098 total alerts from windows-endpoint-angel in a single session — 1 confirmed Level 12 critical. Alert groups evolution chart showing the sharp attack spike in real time.

---

## 🔴 Demo 4 — MITRE ATT&CK Automatic Mapping

Every attack across all agents was automatically classified into the MITRE ATT&CK framework — no manual tagging required.

![MITRE ATT&CK Map](screenshots/wazuh-mitre-attack-map.png)
> Wazuh Security Events filtered by authentication failures — MITRE technique and tactic columns automatically populated on every alert row

![MITRE Donut Chart](screenshots/wazuh-mitre-donut-chart.png)
> MITRE ATT&CK visualization — Password Guessing, SSH, and Brute Force techniques detected and categorized across all agents

**Techniques detected:**

| Technique | ID | Tactic | Source |
|---|---|---|---|
| Brute Force | T1110 | Credential Access | SSH attack on Ubuntu |
| Password Guessing | T1110.001 | Credential Access | SSH attack on Ubuntu |
| Remote Services SSH | T1021.004 | Lateral Movement | SSH attack on Ubuntu |
| Valid Accounts | T1078 | Defense Evasion, Persistence, Privilege Escalation, Initial Access | Windows endpoint attack |

---

## 📊 Alert Summary Table

| Rule | Level | Severity | Description | Source |
|---|---|---|---|---|
| 5760 | 5 | Medium | sshd: authentication failed | auth.log |
| 5763 | 10 | 🔴 Critical | sshd: brute force trying to get access | auth.log |
| 60104 | 5 | Medium | Windows audit failure event | WinEvtLog |
| 60107 | 4 | Low | Failed attempt to perform privileged operation | WinEvtLog |
| **204** | **12** | **🔴 Maximum** | **Agent event queue is flooded** | **Wazuh** |

Total alerts captured — single agent session: **3,098**
Highest severity level reached: **Level 12 Critical**
PCI-DSS requirements triggered: **10.6.1**

---

## 🔑 Key Takeaways

**Wazuh as a SOC Platform**
A single Wazuh server replicated the core monitoring capability of an enterprise SOC — centralized multi-agent detection, automatic MITRE ATT&CK classification, and compliance monitoring all running simultaneously on a 4GB RAM virtual machine.

**Multi-Network Distributed Monitoring**
Connecting agents across two network segments — a VirtualBox Host-Only network and a real WiFi network — reflects how enterprise environments actually work. Endpoints on different subnets and physical locations all reporting to one central server.

**Independent External Agent**
Agent 003 was a completely independent laptop connected only over shared WiFi. This demonstrates that Wazuh scales to any machine that can reach the manager IP — no VPN, no domain, no special configuration beyond the agent install.

**MITRE ATT&CK Without Manual Tagging**
Every detection was automatically classified into MITRE ATT&CK techniques and tactics. This is the standard framework used by SOC analysts globally — having it map automatically means alerts are immediately actionable without additional analysis overhead.

**Alert Escalation Chain**
A single failed SSH login is Level 5. Sustained brute force escalates to Level 10. Flooding the agent queue under combined attack load hit Level 12 Critical. This demonstrates how SIEM tools transform raw log volume into prioritized threat intelligence.

---

## 📁 Repository Structure

```
Wazuh-Enterprise-Security-Lab/
├── README.md
├── screenshots/
│   ├── wazuh-agents-dashboard.png        ← All 3 agents active
│   ├── hydra-ssh-bruteforce.png          ← Kali attacking Ubuntu
│   ├── wazuh-ssh-bruteforce-level10.png  ← Rule 5763 Level 10
│   ├── wazuh-mitre-attack-map.png        ← MITRE technique mapping
│   ├── wazuh-mitre-donut-chart.png       ← MITRE ATT&CK donut chart
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
Cloud Infrastructure | Network and Cloud Security | The Copperbelt University, Kitwe, Zambia

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue?style=flat&logo=linkedin)](https://www.linkedin.com/in/emmanuel-siamoonga-98b30929b/)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-black?style=flat&logo=github)](https://github.com/Emmanuel-cpp)

> *"Security is not a product, but a process."* — Bruce Schneier
