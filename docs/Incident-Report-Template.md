# INCIDENT RESPONSE REPORT

**Incident ID:** IR-2026-0142  
**Date/Time Detected:** October 30, 2026, 08:13:44 AM (Local Time)  
**Reported By:** SOC Analyst (Tier 1)  
**Severity:** Medium  
**Status:** Closed  

---

## 1. Executive Summary
On October 30, 2026, the Security Operations Center (SOC) SIEM (Splunk) generated an alert for multiple failed authentication attempts on a Windows 11 host (`Godsfavour`). An investigation confirmed that a local simulated credential attack (brute-force) was executed against the host. The activity was detected in near real-time using a custom Splunk correlation search. **No unauthorized access was gained**, and no data was compromised.

## 2. Incident Description & Timeline
The detection was triggered by a custom Splunk rule designed to identify brute-force patterns. 

*   **08:13:00:** The Red Team (acting as a threat actor) executed a series of failed authentication commands (`net use \\127.0.0.1\IPC$ /user:[username] [password]`) against the local Windows host.
*   **08:13:44:** Splunk ingested the Windows Security Event Logs (Event ID 4625) and the automated detection rule fired.
*   **08:14:00:** The SOC Analyst (Tier 1) triaged the alert and initiated an investigation.
*   **08:20:00:** Investigation confirmed the anomalies were isolated to the host machine and no successful logons (Event ID 4624) followed the failed attempts. Incident closed.

## 3. Indicators of Compromise (IOCs)
*   **Source IP:** `127.0.0.1` (Loopback) / *[Insert Attacker IP if applicable]*
*   **Target IP:** `192.168.56.1` (Host-Only Adapter) / *[Insert Target IP]*
*   **Target Account:** `kioskUser0` (Windows background system account)
*   **Event Codes Observed:** `4625` (Failed Logon), `5379` (Credential Manager credentials were read)

## 4. Technical Analysis & MITRE ATT&CK Mapping
The investigation revealed a sequence of failed logon attempts. Notably, the telemetry captured the failed attempts under the background system account `kioskUser0` rather than the explicitly typed username, demonstrating the importance of analyzing raw Windows event telemetry rather than just relying on surface-level input.

**MITRE ATT&CK Mapping:**
*  **Tactic:** Credential Access (TA0006)
*  **Technique:** Brute Force (T1110)
*  **Platform:** Windows

## 5. Detection Query Used
The following Splunk SPL (Search Processing Language) query was used to identify the anomaly. It searches for failed logons, groups them by account and computer, and flags any account that failed more than once:

```splunk
index=main EventCode=4625
| stats count by Account_Name, ComputerName
| where count > 1
```

## 6. Impact Analysis
*  **Confidentiality:** None. No data was accessed.

* **Integrity:** None. No data was modified.

* **Availability:** None. The host remained fully operational.

* **Business Impact:** The attempts were isolated to the local loopback address within an isolated lab environment. Zero impact on production or external networks.

## 7. Remediation Steps & Actions Taken
* **Detection & Analysis:** The SIEM successfully correlated Event ID 4625 (Failed Logon) events, exceeding the threshold of >1 failure.

* **Containment:** Because the source was the local loopback address and the attempts failed, immediate network isolation was not required. The analyst verified that no Event ID 4624 (Successful Logon) followed the failures.

* **Eradication:** The simulated attacker process was terminated.

* **Recovery:** No recovery actions were necessary; the system was not compromised.

## 8. Lessons Learned & Recommendations
1. **Rule Tuning:** Modify the Splunk alert to exclude background system accounts like kioskUser0 to reduce alert fatigue in a real production environment.

2. **Threshold** Adjustment: Increase the alert threshold to >5 failed attempts within a 2-minute window to better identify genuine automated brute-force tools (like Hydra).

3. **Account Lockout Policy:** Verify that the Windows Local Security Policy is set to lock accounts after 5 consecutive failed attempts.

---
 * **Analyst Sign-off:** 
 * **Prepared By:** Ibeh Chukwuebuka Anthony
 * **Role:** SOC Analyst / Incident Responder
 * **Date:** October 30, 2026

