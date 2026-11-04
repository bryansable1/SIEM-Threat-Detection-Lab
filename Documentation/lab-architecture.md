# SIEM Lab Architecture and Environment

## 1. Project Overview

The SIEM Threat Detection and Incident Investigation Lab is a virtualized cybersecurity environment designed to demonstrate security monitoring, log analysis, threat detection, and incident investigation.

The lab uses Splunk Enterprise to collect and analyze Windows Security event logs generated during controlled security testing.

The primary objective is to simulate suspicious activity, identify relevant security events, develop Splunk Search Processing Language (SPL) queries, and investigate the resulting telemetry.

## 2. Lab Components

| Component | Platform | Purpose |
|---|---|---|
| Host Machine | Windows 11 | Runs the virtualization environment |
| Hypervisor | VMware Workstation Pro 17.5.2 | Hosts the virtual machines |
| SIEM | Splunk Enterprise | Searches, analyzes, and visualizes security events |
| Monitored Endpoint | Windows Server | Generates Windows Security telemetry |
| Testing Machine | Kali Linux | Performs controlled security testing |

## 3. Network Environment

The lab uses the following virtual machine addresses:

| System | IP Address |
|---|---|
| Windows Endpoint | `192.168.222.131` |
| Kali Linux | `192.168.222.132` |

The Kali Linux machine is used to generate controlled network activity against the Windows endpoint.

## 4. SIEM Data Collection

Windows Security event logs are collected and indexed in Splunk.

**Splunk configuration:**
- Index: `main`
- Host field: `SOC-enpoint`
- Log source: `XmlWinEventLog:Security`

The collected telemetry is used to investigate authentication failures and SMB network share activity.

## 5. Monitoring and Detection

The lab currently includes two investigation scenarios:

### Scenario 1 — Failed Authentication
- Windows Event ID: `4625`
- Activity: Repeated failed authentication attempts
- Source: Kali Linux
- Target account: `bryan`
- MITRE ATT&CK relevance: T1110 — Brute Force

### Scenario 2 — SMB Network Share Enumeration
- Windows Event IDs: `5140`, `5145`
- Activity: SMB network share enumeration and access checks
- Source: Kali Linux
- Target: Windows endpoint
- MITRE ATT&CK relevance: T1135 — Network Share Discovery

## 6. Data Analysis and Visualization

Splunk is used to:
- Search Windows Security event logs.
- Extract relevant fields using SPL.
- Aggregate security events by source IP and account.
- Identify authentication failures and SMB activity.
- Visualize security telemetry through a SOC monitoring dashboard.

## 7. Project Scope

This project demonstrates foundational Security Operations Center (SOC) analyst skills, including:

- Windows security event monitoring
- SIEM configuration and log analysis
- SPL search development
- Authentication investigation
- Network share activity investigation
- MITRE ATT&CK mapping
- Security dashboard development
- Technical documentation

All testing is conducted within the controlled lab environment.
