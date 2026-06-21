# 🛡️ Wazuh SIEM Home Lab – Real-Time Security Monitoring

A hands-on **SIEM home lab** built using **Wazuh v4.14.5**, demonstrating real-time endpoint monitoring, security event detection, CIS compliance checking, and MITRE ATT&CK mapping on a live Windows 11 endpoint.

> ⚡ This is not a theoretical project — this lab is actively running and detecting real security events.

---

## 📌 What This Lab Does

- Monitors a **live Windows 11 endpoint** in real time
- Detects **484+ security events** including authentication events and system changes
- Performs **CIS Microsoft Windows 11 Enterprise Benchmark v3.0.0** compliance checks
- Maps alerts to the **MITRE ATT&CK framework** automatically
- Runs **File Integrity Monitoring (FIM)** to detect unauthorized file changes
- Provides a full **SOC-style dashboard** with alert timelines and severity levels

---

## 🧰 Tools & Technologies

| Tool | Purpose |
|------|---------|
| Wazuh v4.14.5 | SIEM / XDR platform |
| VirtualBox | Hosting the Wazuh server VM |
| Ubuntu Server | Wazuh Manager OS |
| Windows 11 Home | Monitored endpoint (agent) |
| Wazuh Dashboard | Alert visualization & analysis |
| PowerShell | Agent installation & management |

---

## 🏗️ Lab Architecture

```
┌─────────────────────┐         ┌──────────────────────────┐
│  Windows 11 Laptop  │──1514──▶│   Wazuh Server (Ubuntu)  │
│  LAPTOP-61MDGHxx    │  TCP    │   192.168.x.xx            │
│  Agent ID: 001      │         │   Wazuh v4.14.5           │
│  IP: 192.168.x.x    │         │   Dashboard on HTTPS      │
└─────────────────────┘         └──────────────────────────┘
```

---

## 🚀 Setup Steps

### 1. Deploy Wazuh Server
- Downloaded the official Wazuh OVA (pre-built virtual machine)
- Imported into VirtualBox and configured with 4GB RAM / 2 CPUs
- Accessed the Wazuh Dashboard via browser at `https://<manger_ip>`

### 2. Deploy Windows Agent
```powershell
# Download and install the Wazuh agent silently
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.14.5-1.msi `
  -OutFile $env:tmp\wazuh-agent
msiexec.exe /i $env:tmp\wazuh-agent /q `
  WAZUH_MANAGER='<manger_ip>' `
  WAZUH_AGENT_GROUP='default' `
  WAZUH_AGENT_NAME='my-windows-pc'

# Register agent with the server
& "C:\Program Files (x86)\ossec-agent\agent-auth.exe" -m <manger_ip>

# Start the agent service
NET START WazuhSvc
```

### 3. Verify Connection
```powershell
# Test network connectivity to Wazuh server
Test-NetConnection -ComputerName <manger_ip> -Port 1514
# Result: TcpTestSucceeded: True ✅

# Check service status
Get-Service WazuhSvc | Select-Object Status, Name
# Result: Running ✅
```

---

## 📊 Real Results — What Wazuh Detected

### Live Dashboard Stats
- **484 security events** captured from the Windows endpoint
- **Active agent:** LAPTOP-61MDxx (Windows 11 Home)
- **Agent version:** Wazuh v4
- **MITRE ATT&CK techniques detected:** Valid Accounts (T1078)

### CIS Benchmark Compliance Findings
Wazuh automatically ran **CIS Microsoft Windows 11 Enterprise Benchmark v3.0.0** checks and flagged real misconfigurations:

| Rule ID | Finding | Severity |
|---------|---------|----------|
| 19005 | SCA Summary: Score less than 30% | Level 9 |
| 19007 | Preview builds and feature updates policy not configured | Level 7 |
| 19008 | Optional updates not disabled | Level 3 |
| 19009 | Quality update receive time not configured | Level 3 |

> A score below 30% means the endpoint has significant security hardening gaps — exactly the kind of finding a SOC analyst investigates.

---

## 🔍 Alert Analysis

### Authentication Event Detection
- Wazuh detected and categorized **authentication success** events
- Mapped to **MITRE ATT&CK: Valid Accounts (T1078)**
- Alert timeline shows spike in events during agent connection

### File Integrity Monitoring
Configured to monitor Windows system directories for unauthorized changes:
```xml
<syscheck>
  <directories check_all="yes" realtime="yes">C:\SensitiveData</directories>
  <directories check_all="yes" realtime="yes">C:\Windows\System32</directories>
</syscheck>
```

---

## 🧠 Skills Demonstrated

- SIEM deployment and configuration (Wazuh)
- Windows endpoint agent installation and registration
- Security event analysis and alert triage
- CIS Benchmark compliance assessment
- MITRE ATT&CK framework mapping
- Network troubleshooting (port testing, config debugging)
- Blue Team / SOC analyst workflows

---

## 🚀 Planned Enhancements

- [ ] Add Linux agent (Kali VM) for cross-platform monitoring
- [ ] Write custom Wazuh detection rules
- [ ] Simulate brute force attack and capture alerts
- [ ] Configure active response to auto-block IPs
- [ ] Integrate with Atomic Red Team for attack simulation
- [ ] Add Shuffle SOAR for automated incident response

---

## 📁 Project Structure

```
wazuh-siem-homelab/
├── README.md          ← This file
├── screenshots/       ← Dashboard & alert screenshots (coming soon)
└── rules/             ← Custom detection rules (coming soon)
```

---

## 👤 Author

**Emmanuel Senaij**  
Cybersecurity Analyst | CEHv13 Certified | TryHackMe Top 6%  
🔗 [GitHub](https://github.com/fLsh123) • [LinkedIn](https://linkedin.com/in/your-linkedin)
