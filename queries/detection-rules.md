# 🛡️ SIEM Detection Rules

## Overview
This document outlines the detection logic developed during the Incident Detection & Response (IDR) lab. The objective was to identify a local brute-force credential attack against a Windows 11 host.

*   **SIEM Tool:** Splunk Enterprise
*   **Target Log Source:** Windows Security Event Log (`WinEventLog:Security`)
*   **MITRE ATT&CK Mapping:** T1110 (Brute Force)

---

## Rule 1: Multiple Failed Logons Detected

### The Query
```splunk
index=main EventCode=4625
| stats count by Account_Name, ComputerName
| where count > 1
```

## Line-by-Line Explanation
* **`index=main:`** This tells Splunk to search the default index where the Windows Event Logs are being ingested.

* **`EventCode=4625:`** This is the primary filter. It tells Splunk to only look for Event ID 4625, which is the Windows Security Event for "An account failed to log on."

* **`| stats count by Account_Name, ComputerName:`** The pipe `(|)` passes the results to the stats command. Instead of showing individual raw log lines, this aggregates (counts) the number of failures for every specific `Account_Name` on every specific `ComputerName`.

* **`| where count > 1:`** This filters the aggregated results. It only displays the accounts that have failed to log in more than once. By setting the threshold to `> 1`, we are looking for a pattern of failure rather than a single user typo.

## Expected Output
When executed, the query returns a statistical table with three columns:

1. `Account_Name:` The username that failed to authenticate (e.g., `kioskUser0` or `hacker`).

2. `ComputerName:` The host machine where the failure occurred (e.g., `Godsfavour`).

3. `count:` The total number of failed attempts for that specific account.

## Example Output:

| Account_Name | ComputerName | count| 
| :--- | :---: | ---: |
| kioskUser0 | Godsfavour | 2 |

## How a SOC Analyst Uses This in the Real World
In a production environment, this query is the foundation of a **Brute-Force Detection Rule**.
A SOC analyst would schedule this query to run automatically every 5 to 15 minutes. If the results return a count greater than 5 (or 10, depending on the company's baseline), Splunk generates an alert.

The analyst would then triage the alert:

* **Verify the Context:** Check if the failed attempts are coming from an internal user who forgot their password, or an external IP address.

* **Check for Success:** The analyst would immediately run a follow-up query for `EventCode=4624` (Successful Logon) to see if the attacker eventually guessed the password correctly.

* **Tune the Rule:** If the alert triggers too often on background accounts (like `kioskUser0`), the analyst would modify the query to exclude system accounts (e.g., `| where Account_Name != "kioskUser0`") to reduce false positives and alert fatigue.

---
*Disclaimer: This detection logic was tested in an isolated home lab environment using simulated attack traffic.*

