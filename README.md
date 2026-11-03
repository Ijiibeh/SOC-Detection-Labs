# 🛡️ Incident Detection & Response (IDR) Home Lab

Welcome to my IDR portfolio. This repository documents my hands-on labs focused on threat detection, log analysis, and incident response. Drawing on my networking background, I designed this lab to simulate a credential-based attack and detect it in real-time using a SIEM, following the NIST SP 800-61 Incident Response Lifecycle.

## 🏗️ Environment Setup
To ensure safety and prevent interference with external networks, the lab was built in an isolated environment.
*   **Host / Target Machine:** Windows 11 (HP EliteBook 840 G7)
*   **SIEM (Monitoring):** Splunk Enterprise (Installed locally on the Windows Host)
*   **Attacker Simulation:** PowerShell (Executing local authentication attempts)
*   **Network Configuration:** Local loopback (127.0.0.1) and isolated Host-Only network (192.168.56.0/24) to ensure all testing remained entirely within the controlled environment.

## ⚔️ The Attack (Red Team)
The objective was to simulate a threat actor attempting to gain unauthorized access via credential guessing.
*   **The Tools:** PowerShell `net use` command.
*   **The Execution:** I executed a series of rapid, failed authentication attempts against the local Windows SMB/RPC service using a mock username.
*   **MITRE ATT&CK Mapping:**
    *   **Tactic:** Credential Access (TA0006)
    *   **Technique:** T1110 (Brute Force)

## 🔍 The Detection (Blue Team)
Splunk was configured to ingest Windows Security Event Logs. I developed a custom Splunk Processing Language (SPL) correlation search to identify the brute-force pattern.
*   **The Logic:** The rule monitors for EventCode `4625` (An account failed to log on) and aggregates the results by account name. If an account fails to log in more than once within the timeframe, it triggers an alert.
*   **The Findings:** The SIEM successfully caught the anomaly. Interestingly, the Windows telemetry logged the failures under a background system account (`kioskUser0`) rather than the mock username. This highlighted a crucial real-world lesson: analysts must review raw event telemetry, as systems don't always log what you expect.

## 🚑 The Response (Incident Handling)
Upon the alert triggering, I acted as the SOC Analyst to triage and investigate the incident.
*   **Analysis:** Confirmed no successful logons (Event ID 4624) followed the failures, indicating the attack was unsuccessful and no unauthorized access was gained.
*   **Response:** Because the activity was isolated to the local host and failed, no heavy containment (like network isolation) was required.
*   **Lessons Learned:** While the detection query was effective, the report recommends tuning the alert to filter out known background accounts (like `kioskUser0`) to prevent alert fatigue in a production SOC environment.

## 📂 Repository Structure
To view the detailed analysis, technical evidence, and detection logic, please explore the following folders:

*   📁 **[docs/](./docs/)** - Contains the full, professional `Incident_Report.md` (Executive Summary, Timeline, Impact, and Recommendations).
*   📁 **[queries/](./queries/)** - Contains the exact `.spl` (Splunk) detection rule used to catch the attack.
*   📁 **[images/](./images/)** - Visual evidence of the simulated attack and the resulting Splunk search results.


## 📸 Visual Evidence

### 1. Log Ingestion
*(Proof that Splunk is receiving Windows Event Logs)*
![Log Ingestion](./01_splunk_log_ingestion.png)

### 2. Attack Execution
*(Proof of the PowerShell brute-force simulation)*
![Attack Execution](./02_attack_powershell.png)

### 3. The Detection Rule
*(The custom SPL query catching the anomaly and displaying the account name)*
![Detection Rule](./03_splunk_detection_rule.png)

### 4. The Raw Telemetry
*(The raw Windows Security Event 4625 logs showing the failed logons)*
![Raw Events](./04_splunk_raw_events.png)

---
*Disclaimer: All activities documented in this repository were performed in an isolated, controlled lab environment for educational purposes only. No unauthorized systems were targeted.*
