# SOC-Detection-Labs
A collection of Incident Detection &amp; Response (IDR) labs, SIEM queries, and threat hunting reports.

# Incident Detection & Response (IDR) Home Lab 🛡️

Welcome to my IDR portfolio. This repository documents my hands-on labs focused on threat detection, log analysis, and incident response using a SIEM (Security Information and Event Management) system.

## 🎯 Lab Objective
The goal of this lab is to simulate real-world attacks (e.g., brute force, network scanning) and build the detection rules required to catch them.

## 🛠️ Environment & Tools
*   **SIEM:** Splunk (or Elastic/Wazuh)
*   **Target OS:** Windows 10 / Windows 11 / Metasploitable
*   **Attacker Tools:** Nmap, Hydra, Kali Linux
*   **Log Sources:** Windows Event Logs, Sysmon, Firewall Logs

## 📂 Repository Structure
*   **[Incident Reports](./docs/)** - Detailed write-ups of simulated attacks and my response to them.
*   **[Detection Queries](./queries/)** - The actual SIEM queries (SPL/KQL) used to trigger alerts.
*   **[Dashboards](./images/)** - Screenshots of SIEM dashboards and visualizations.

## 🚀 Featured Lab: [Name of your first lab, e.g., SSH Brute Force Detection]
*   **Attack:** Simulated an SSH brute-force attack using Hydra.
*   **Detection:** Wrote a Splunk query to detect multiple failed login events (Event ID 4625) within a 5-minute window.
*   **Response:** Blocked the attacker IP and documented the incident.

*(Click [here](./docs/) to read the full incident reports!)*
