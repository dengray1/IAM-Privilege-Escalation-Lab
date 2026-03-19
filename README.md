# IAM Privilege Escalation Detection Lab

## Overview
Designed and executed a cloud-based IAM privilege escalation attack in AWS, then built a real-time detection pipeline using CloudTrail, Splunk SIEM, tcpdump, and Wireshark.

## Tools Used
- **AWS IAM** — Created vulnerable IAM users and roles
- **AWS CloudTrail** — Logged all API calls for forensic analysis
- **AWS EC2** — Hosted network traffic capture
- **Splunk SIEM** — Built detection dashboard with custom SPL queries
- **tcpdump** — Captured network traffic during attack
- **Wireshark** — Analyzed TLS handshakes to AWS STS endpoints

## Attack Flow
1. Configured `attacker-user` with IAMFullAccess permissions
2. Created misconfigured `escalation-target-role` with AdministratorAccess
3. Used AWS CLI to assume the admin role from the attacker account
4. Verified escalation by accessing S3 buckets with no prior S3 permissions

## Detection
- CloudTrail captured all `AssumeRole` API calls
- Splunk ingested CloudTrail logs and parsed JSON records
- Custom SPL queries isolated the escalation event by role ARN
- Built 3-panel Splunk dashboard showing attack detection, IAM event overview, and attack timeline

## Evidence
![Splunk Dashboard](screenshots/dashboard.png)
![Wireshark Capture](screenshots/wireshark.png)

## Key Findings
- `attacker-user` successfully assumed `escalation-target-role` twice
- Attack originated from IP `35.23.69.177`
- CloudTrail logged both `AssumeRole` events with full metadata
- Network capture confirmed TLS connections to `sts.amazonaws.com`

## Mitigation Recommendations
- Implement least privilege — never attach AdministratorAccess to assumable roles
- Set up CloudTrail alerts for AssumeRole events on sensitive roles
- Use AWS SCPs (Service Control Policies) to restrict role assumption
- Regularly audit IAM roles and their trust policies
