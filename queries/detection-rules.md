# Detection Rules & SIEM Queries

## 1. SSH Brute Force Detection (Splunk)
This query triggers an alert if there are more than 5 failed login attempts from the same IP within 5 minutes.

```spl
index=linux_logs sourcetype=sshd "Failed password"
| stats count by src_ip
| where count > 5
