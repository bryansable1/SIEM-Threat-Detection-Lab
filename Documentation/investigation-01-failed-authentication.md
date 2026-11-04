# Incident Investigation 01 — Failed Authentication Activity

## 1. Executive Summary

A controlled authentication security test was conducted against the Windows Server endpoint using a Kali Linux testing machine.

The test generated multiple failed authentication events. Windows Security Event ID 4625 was collected and analyzed in Splunk to identify the source IP address, targeted account, and authentication failure details.

The investigation demonstrated how centralized Windows security telemetry can be used to identify and analyze repeated authentication failures.

## 2. Investigation Details

| Field | Value |
|---|---|
| Investigation | Failed Authentication |
| Source System | Kali Linux |
| Source IP | `192.168.222.132` |
| Target System | Windows Server |
| Target Account | `bryan` |
| Event ID | `4625` |
| Splunk Index | `main` |
| MITRE ATT&CK | T1110 — Brute Force |

## 3. Attack Simulation

The Kali Linux machine used the `smbclient` utility to attempt SMB authentication against the Windows endpoint using invalid credentials.

The authentication attempts returned:

```text
NT_STATUS_LOGON_FAILURE
```

These attempts generated Windows Security Event ID 4625 records.

## 4. Evidence Collection

The Windows Security logs were forwarded to Splunk Enterprise and searched using SPL.

The investigation extracted:

- Target username
- Source IP address
- Authentication status
- Authentication substatus
- Number of failed authentication attempts

The observed status and substatus values were:

| Field | Value | Interpretation |
|---|---|---|
| Status | `0xc000006d` | Logon failure |
| Substatus | `0xc000006a` | Incorrect password |

## 5. Splunk Investigation Findings

The investigation identified repeated failed authentication activity originating from `192.168.222.132` and targeting the account `bryan`.

The initial focused investigation identified eight failed attempts. A broader aggregation identified eleven Event ID 4625 records, including three interactive logon events and eight network logon events.

These counts represent different search scopes and should not be treated as the same measurement.

## 6. MITRE ATT&CK Mapping

- **Tactic:** Credential Access (TA000
  
## 7. Disclaimer
  **Note on Event Counts:** The failed-authentication event count varies across the investigation because different SPL searches use different search scopes and aggregation methods. The focused investigation returned 8 events, the broader aggregation returned 11, and the dashboard displayed 12. These values represent results from their respective searches and should not be interpreted as a single identical event count.
