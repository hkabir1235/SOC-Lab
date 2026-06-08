# 🛡️ SOC Lab Project: Splunk Enterprise, Sysmon, Universal Forwarder & Atomic Red Team

## 👨‍💻 Author

**Abdullah Al Mamun**

---

## 📌 Project Overview

This project demonstrates the deployment of a Security Operations Center (SOC) lab environment using:

* Kali Linux (SIEM Server)
* Splunk Enterprise
* Windows Server 2019
* Sysmon
* Splunk Universal Forwarder
* Atomic Red Team

The primary objective was to collect Windows endpoint telemetry, forward the logs to Splunk Enterprise, execute MITRE ATT&CK-based adversary simulations using Atomic Red Team, and validate detection capabilities through Sysmon-generated events.

---

## 🏗️ Lab Architecture

```text
Windows Server 2019
│
├── Sysmon
├── Splunk Universal Forwarder
└── Atomic Red Team
        │
        ▼
Kali Linux
│
└── Splunk Enterprise
        │
        ▼
Detection & Analysis
```

---

## 🎯 Objectives

* Install and configure Splunk Enterprise
* Configure Splunk Receiving on TCP Port 9997
* Install Sysmon and configure telemetry collection
* Install and configure Splunk Universal Forwarder
* Forward Sysmon logs to Splunk
* Install Atomic Red Team
* Simulate adversary techniques
* Detect activities through Sysmon telemetry
* Validate SOC monitoring capabilities

---

# 🖥️ Environment Information

## Kali Linux

| Component         | Version    |
| ----------------- | ---------- |
| OS                | Kali Linux |
| Splunk Enterprise | 10.4.0     |

## Windows Server

| Component           | Version                    |
| ------------------- | -------------------------- |
| OS                  | Windows Server 2019        |
| Sysmon              | Sysmon64                   |
| Universal Forwarder | Latest Supported Version   |
| Atomic Red Team     | Invoke-AtomicRedTeam 2.3.0 |

---

# 🚀 Splunk Enterprise Deployment

## Splunk Login Verification

### 📷 Image-1

Successfully accessed Splunk Enterprise login portal.

### 📷 Image-2

Logged in as Administrator and verified dashboard accessibility.

### 📷 Image-3

Verified Splunk Search & Reporting interface.

Functions available:

* Search and analyze logs
* Create detections
* Build dashboards
* Perform investigations

---

# 🔄 Splunk Receiving Configuration

### 📷 Image-4

Navigated to:

```text
Settings → Forwarding and Receiving
```

Configured Splunk as a receiving indexer.

### 📷 Image-5

Enabled Splunk receiving on:

```text
TCP Port 9997
```

### 📷 Image-6

Verified listener:

```bash
sudo ss -tlnp | grep 9997
```

Output:

```text
0.0.0.0:9997
```

This confirmed Splunk was actively accepting forwarded logs.

---

# 🌐 Windows Server Preparation

### 📷 Image-7

Verified internet connectivity:

```cmd
ping google.com
```

Internet access was required to:

* Download Sysmon
* Download Atomic Red Team dependencies
* Install PowerShell modules

---

# 🔍 Sysmon Deployment

## Sysmon Package

### 📷 Image-8

Downloaded and extracted:

```text
Sysmon64.exe
```

---

## Sysmon Configuration

### 📷 Image-9

Verified Sysmon XML configuration.

Configured monitoring for:

* Process Creation
* Network Connections
* File Creation
* Registry Activity
* Image Loads

---

## Sysmon Installation

### 📷 Image-10

Installed Sysmon:

```powershell
.\Sysmon64.exe -accepteula -i .\sysmonconfig.xml
```

Verification:

```powershell
Get-Service Sysmon64
```

Status:

```text
Running
```

---

## Event Viewer Verification

### 📷 Image-11

Verified logs in:

```text
Applications and Services Logs
 └─ Microsoft
     └─ Windows
         └─ Sysmon
             └─ Operational
```

---

### 📷 Image-12

Verified Sysmon Event ID 1.

**Event ID 1 – Process Creation**

Captured:

* Process Name
* Command Line
* Process ID
* Parent Process
* User Context
* Hash Values

---

# 📡 Splunk Universal Forwarder Deployment

## Connectivity Verification

### 📷 Image-13

Commands:

```powershell
ping 192.168.68.102
```

```powershell
Get-Service SplunkForwarder
```

Status:

```text
Running
```

---

## Universal Forwarder Configuration

### inputs.conf

```ini
[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = 0
index = main
renderXml = true
```

### outputs.conf

```ini
[tcpout]
defaultGroup = default-autolb-group

[tcpout:default-autolb-group]
server = 192.168.68.102:9997
```

---

# 📈 Log Ingestion Validation

### 📷 Image-14

Initial search returned:

```text
0 Events
```

This occurred before log forwarding was fully configured.

---

### 📷 Image-15

After troubleshooting, Sysmon events were successfully ingested.

Observed Event IDs:

* Event ID 1
* Event ID 3
* Event ID 11
* Event ID 22

---

# ⚔️ Atomic Red Team Installation

### 📷 Image-16

Installed required modules:

```powershell
Install-Module powershell-yaml -Force
Install-Module Invoke-AtomicRedTeam -Force
```

Verified:

```powershell
Get-Command Invoke-AtomicTest
```

---

# 🧪 Atomic Red Team Enumeration

### 📷 Image-17

Enumerated:

```text
T1059.001 – PowerShell
```

Reviewed:

* Technique details
* Atomic tests
* Required commands
* Execution methods

---

### 📷 Image-18

Verified prerequisites:

```powershell
Invoke-AtomicTest T1059.001 -TestNumbers 1 -CheckPrereqs
```

---

# 📊 Event Analysis

### 📷 Image-19

Query:

```spl
source="WinEventLog:Microsoft-Windows-Sysmon/Operational"
| rex "<EventID>(?<EventID>\d+)</EventID>"
| stats count by EventID
| sort -count
```

Purpose:

* Analyze Sysmon telemetry distribution
* Identify most frequent event types

---

# 🎯 Atomic Test Execution

### 📷 Image-20

Attempted execution:

```text
T1059.001 – PowerShell
```

Execution generated telemetry but encountered dependency restrictions.

---

### 📷 Image-21

Executed:

```text
T1105 – Ingress Tool Transfer
```

Observed:

* Windows Defender Alert
* Threat Prevention Notification

---

# 🔵 Detection Engineering

## Sysmon Source Verification

### 📷 Image-22

Verified source:

```text
WinEventLog:Microsoft-Windows-Sysmon/Operational
```

---

## PowerShell Activity Detection

### 📷 Image-23

Query:

```spl
source="WinEventLog:Microsoft-Windows-Sysmon/Operational" powershell
```

Mapped Technique:

```text
MITRE ATT&CK T1059.001
```

Detected:

* powershell.exe execution
* Command-line activity
* PowerShell telemetry

---

## Log Source Validation

### 📷 Image-24

Query:

```spl
source="WinEventLog:Microsoft-Windows-Sysmon/Operational"
| stats count by source
```

Result:

```text
WinEventLog:Microsoft-Windows-Sysmon/Operational → 6222 Events
```

This confirms successful collection and indexing of Sysmon logs.

---

# 🔎 Key Splunk Queries

## All Sysmon Events

```spl
source="WinEventLog:Microsoft-Windows-Sysmon/Operational"
```

## PowerShell Detection

```spl
source="WinEventLog:Microsoft-Windows-Sysmon/Operational" powershell
```

## Event ID Analysis

```spl
source="WinEventLog:Microsoft-Windows-Sysmon/Operational"
| rex "<EventID>(?<EventID>\d+)</EventID>"
| stats count by EventID
| sort -count
```

## Source Validation

```spl
source="WinEventLog:Microsoft-Windows-Sysmon/Operational"
| stats count by source
```

## Process Creation Events

```spl
source="WinEventLog:Microsoft-Windows-Sysmon/Operational"
EventID=1
```

## Network Connection Events

```spl
source="WinEventLog:Microsoft-Windows-Sysmon/Operational"
EventID=3
```

---

# ⚠️ Challenges Encountered

1. Splunk dispatch storage became full
2. Kali Linux virtual disk required expansion
3. Universal Forwarder troubleshooting
4. Atomic Red Team dependency issues
5. Windows Defender blocking attack simulations
6. Event parsing challenges

All issues were successfully resolved.

---

# ✅ Conclusion

This project successfully demonstrated the implementation of a SOC monitoring environment using:

* Splunk Enterprise
* Sysmon
* Splunk Universal Forwarder
* Atomic Red Team

### Key Achievements

* Successfully deployed Splunk Enterprise
* Configured Sysmon telemetry collection
* Forwarded Windows logs to Splunk
* Simulated ATT&CK techniques
* Detected PowerShell-based activity
* Collected and indexed **6,222+ Sysmon events**

The lab provides a practical foundation for:

* Threat Detection
* Threat Hunting
* Incident Response
* MITRE ATT&CK Analysis
* SIEM Operations

---

# 📚 References

* https://www.splunk.com
* https://learn.microsoft.com/sysinternals/downloads/sysmon
* https://github.com/redcanaryco/atomic-red-team
* https://attack.mitre.org
* https://www.splunk.com/en_us/blog/security.html
