# Detection Rule 01 — Failed Authentication Activity

## Overview

This detection identifies failed Windows authentication attempts using Windows Security Event ID 4625.

The rule extracts the target account, source IP address, and authentication status fields to help identify repeated authentication failures and investigate their origin.

## Data Source

- **Platform:** Windows Server
- **Log Source:** Windows Security Event Log
- **Splunk Index:** `main`
- **Event ID:** `4625`
- **MITRE ATT&CK:** T1110 — Brute Force

## SPL Detection Search

```spl
index=main host="SOC-enpoint"
| rex "<EventID>\s*(?<event_id>\d+)\s*</EventID>"
| search event_id=4625
| rex "Name='TargetUserName'>(?<target_user>[^<]*)</Data>"
| rex "Name='IpAddress'>(?<source_ip>[^<]*)</Data>"
| rex "Name='Status'>(?<status>[^<]*)</Data>"
| rex "Name='SubStatus'>(?<substatus>[^<]*)</Data>"
| stats count as failed_attempts values(status) as status values(substatus) as substatus by source_ip target_user
| sort -failed_attempts
```

## Detection Logic

1. Searches Windows Security events indexed in Splunk.
2. Extracts the Windows Event ID.
3. Filters for Event ID 4625.
4. Extracts the target username and source IP address.
5. Extracts authentication status and substatus codes.
6. Aggregates failed authentication events by source IP and target account.
7. Sorts results by the number of failed attempts.

## Lab Investigation Results

The simulated authentication investigation produced the following observations:

- Source IP: `192.168.222.132`
- Target account: `bryan`
- Failed attempts in the dashboard investigation: 9
- Windows Event ID: `4625`
- Status: `0xc000006d`
- Substatus: `0xc000006a`

The status and substatus values are consistent with an unsuccessful logon caused by incorrect credentials.

## Analyst Assessment

The events demonstrate repeated failed authentication attempts originating from the Kali Linux testing machine against the Windows endpoint.

This activity is relevant to MITRE ATT&CK technique T1110 (Brute Force). However, the presence of failed logons alone does not establish a successful compromise.

## Investigation Value

This detection provides visibility into failed authentication activity and supports further investigation of:

- Repeated login failures
- Potential password guessing
- Accounts receiving repeated authentication attempts
- Source systems generating authentication failures
