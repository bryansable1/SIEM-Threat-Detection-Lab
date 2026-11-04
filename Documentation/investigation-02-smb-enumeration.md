# Incident Investigation 02 — SMB Network Share Enumeration

## 1. Executive Summary

A controlled SMB network share enumeration test was conducted against the Windows Server endpoint using a Kali Linux testing machine.

The objective was to identify accessible network shares and determine whether Windows Security telemetry could capture the resulting SMB activity.

Splunk Enterprise was used to analyze Event IDs 5140 and 5145, identify the source system, and examine the network share activity.

## 2. Investigation Details

| Field | Value |
|---|---|
| Investigation | SMB Network Share Enumeration |
| Source System | Kali Linux |
| Source IP | `192.168.222.132` |
| Target System | Windows Server |
| Target IP | `192.168.222.131` |
| Target Account | `bryan` |
| Event IDs | `5140`, `5145` |
| Splunk Index | `main` |
| MITRE ATT&CK | T1135 — Network Share Discovery |

## 3. Attack Simulation

The Kali Linux machine was used to identify SMB services and enumerate network shares on the Windows endpoint.

First, Nmap was used to identify the SMB service on TCP port 445.

The following command was executed:

```bash
nmap -p 445 192.168.222.131
```

The SMB service was found to be accessible.

Next, the following command was used to enumerate available shares:

```bash
smbclient -L //192.168.222.131 -U bryan
```

The enumeration returned the following shares:

- `ADMIN$`
- `C$`
- `IPC$`

## 4. Windows Audit Configuration

Windows auditing was configured to capture successful and failed file-share activity.

The following commands were executed in an elevated PowerShell session:

```powershell
auditpol /set /subcategory:"File Share" /success:enable /failure:enable

auditpol /set /subcategory:"Detailed File Share" /success:enable /failure:enable
```

This enabled auditing for the relevant SMB share activity.

## 5. Evidence Collection

The Windows Security logs were collected by Splunk Enterprise.

The investigation focused on two event types:

| Event ID | Description |
|---|---|
| `5140` | A network share was accessed |
| `5145` | A network share object was checked to see whether client access could be granted |

The Splunk investigation extracted the following fields:

- Source IP address
- Subject username
- Share name
- Windows Event ID
- Total event count

## 6. Splunk Investigation Findings

The investigation identified two relevant Windows Security events:

| Finding | Observed Value |
|---|---|
| Source IP | `192.168.222.132` |
| Target Host | `SOC-enpoint` |
| Username | `bryan` |
| Total Events | `2` |
| Event IDs | `5140`, `5145` |
| Observed Share | `\\*\IPC$` |

The events demonstrated network share access and detailed access-check activity associated with the IPC$ share.

The Kali Linux enumeration command also returned `ADMIN$`, `C$`, and `IPC$` as available shares.

## 7. MITRE ATT&CK Mapping

- **Tactic:** Discovery (TA0007)
- **Technique:** T1135 — Network Share Discovery

The simulated activity aligns with T1135 because the testing machine enumerated network shares to identify available resources.

## 8. Analyst Assessment

The investigation confirmed that SMB activity generated Windows Security telemetry that was successfully collected and analyzed in Splunk.

The observed Event IDs 5140 and 5145 provided visibility into network share access and access-check activity.

The results demonstrate network share discovery behavior. They do not independently establish that every enumerated share was accessed or that lateral movement occurred.

## 9. Conclusion

This investigation demonstrated the ability to:

- Identify SMB services using Nmap.
- Enumerate Windows network shares using smbclient.
- Configure Windows file-share auditing.
- Collect Windows Security Event IDs 5140 and 5145.
- Develop SPL searches to investigate SMB activity.
- Extract source IP, username, and share information.
- Map observed activity to MITRE ATT&CK T1135.

The investigation provides practical experience in monitoring Windows file-share activity and analyzing network discovery behavior in a SIEM environment.
