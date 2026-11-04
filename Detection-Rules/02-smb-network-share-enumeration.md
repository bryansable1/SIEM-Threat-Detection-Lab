# Detection Rule 02 — SMB Network Share Enumeration

## Overview

This detection identifies Windows network share activity using Security Event IDs 5140 and 5145.

Event ID 5140 records network share access, while Event ID 5145 records detailed checks to determine whether a client can access a network share.

These events provide visibility into SMB activity and can help analysts investigate potential network share discovery.

## Data Source

- **Platform:** Windows Server
- **Log Source:** Windows Security Event Log
- **Splunk Index:** `main`
- **Event IDs:** `5140`, `5145`
- **MITRE ATT&CK:** T1135 — Network Share Discovery
- **Tactic:** Discovery

## SPL Detection Search

```spl
index=main host="SOC-enpoint"
| rex "<EventID>\s*(?<event_id>\d+)\s*</EventID>"
| search event_id=5140 OR event_id=5145
| rex "Name='SubjectUserName'>(?<username>[^<]*)</Data>"
| rex "Name='IpAddress'>(?<source_ip>[^<]*)</Data>"
| rex "Name='ShareName'>(?<share_name>[^<]*)</Data>"
| stats count as total_events dc(event_id) as distinct_event_types values(event_id) as event_ids values(share_name) as shares by host source_ip username
| eval mitre_technique="T1135"
| table host source_ip username total_events distinct_event_types event_ids shares mitre_technique
```

## Detection Logic

1. Searches Windows Security events indexed in Splunk.
2. Extracts the Windows Event ID.
3. Filters for Event IDs 5140 and 5145.
4. Extracts the account name, source IP address, and network share name.
5. Aggregates events by endpoint, source IP, and username.
6. Counts the total events and distinct event types.
7. Associates the activity with MITRE ATT&CK technique T1135.

## Lab Investigation Results

During the simulated investigation, the Kali Linux testing machine performed SMB share enumeration against the Windows endpoint.

Observed results:

- **Source IP:** `192.168.222.132`
- **Target endpoint:** `SOC-enpoint`
- **Account:** `bryan`
- **Total events:** 2
- **Event IDs:** 5140 and 5145
- **Observed share:** `\\*\IPC$`

The Kali machine also successfully enumerated the available shares through SMB, including `ADMIN$`, `C$`, and `IPC$`.

## Analyst Assessment

The collected events demonstrate SMB network share activity originating from the Kali Linux testing machine.

This behavior is relevant to MITRE ATT&CK technique T1135 (Network Share Discovery), which describes the discovery of network shares.

The events provide evidence of access to and access checks against the observed IPC$ share. They do not independently establish that every enumerated share was accessed or that the activity resulted in lateral movement.

## Investigation Value

This detection supports investigation of:

- SMB network share access
- Potential network share discovery
- Source systems accessing Windows shares
- Accounts associated with SMB activity
- Unusual access to administrative or hidden shares
