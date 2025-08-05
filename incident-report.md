# SOC Incident Report – AWS GuardDuty Threat Monitoring Simulation

**Incident ID:** AWS-GD-2025-001  
**Date/Time Detected:** 2025-08-04 17:14 UTC  
**Reported by:** AWS GuardDuty  
**Severity Level:** Low  

---

## 1. Executive Summary
During a controlled AWS GuardDuty simulation exercise, suspicious activity was detected involving an IAM user named `malicious-user-simulation`.  
The user disabled S3 Block Public Access on a bucket named `nahjeewill-testing` using the `PutBucketPublicAccessBlock` API call.  
This action could have exposed the bucket contents to the public if not remediated.  
CloudTrail logs confirmed the unauthorized configuration change and associated it with a remote IP address in Oklahoma City, United States.

---

## 2. Incident Details

**Detection Source:** AWS GuardDuty  
**Affected AWS Account ID:** 728861385404  
**Region(s) Involved:** `us-east-1`  

**GuardDuty Findings:**
| Finding ID | Title | Severity | Resource Affected | Count |
|------------|-------|----------|-------------------|-------|
| f4cc3b933758da01cf77ad9a6c5bcb02 | Policy:S3/BucketBlockPublicAccessDisabled | Low | S3 Bucket: `nahjeewill-testing` | 1 |
| [Secondary Finding] | API ListMembers invoked using root credentials | Low | AWS Root Account | 1 |

---

## 3. Timeline of Events

| Timestamp (UTC) | Event Description |
|-----------------|-------------------|
| 17:07:44 | `malicious-user-simulation` disables S3 Block Public Access on `nahjeewill-testing` |
| 17:14:24 | GuardDuty generates finding `Policy:S3/BucketBlockPublicAccessDisabled` |
| 22:07:44 | CloudTrail logs confirm API call from IP `156.110.24.142` (Oklahoma City, US) |
| 22:10:46 | IAM activity logs show group and role modifications |

---

## 4. Investigation Summary
Analysis in AWS CloudTrail correlated the GuardDuty finding with specific API calls:
- **API Call:** `PutBucketPublicAccessBlock`
- **Bucket Affected:** `nahjeewill-testing`
- **Effective Permission After Action:** PUBLIC
- **Actor:** IAM User `malicious-user-simulation`
- **Caller Type:** Remote IP (`156.110.24.142`)
- **Organization ASN:** ONENET-AS-1 (Oklahoma Network for Education Enrichment)

---

## 5. Impact Assessment
If this had occurred in a production environment, the disabling of S3 Block Public Access could have exposed sensitive or confidential data to the public internet.  
The IAM user had `AdministratorAccess` privileges, meaning further privilege abuse was possible.

---

## 6. Containment and Remediation
- Re-enabled Block Public Access for the S3 bucket.
- Deleted IAM user `malicious-user-simulation`.
- Rotated all access keys associated with affected accounts.
- Enforced MFA for all IAM users.
- Applied least privilege principle to IAM permissions.

---

## 7. Lessons Learned
- AWS GuardDuty effectively detects unauthorized or risky configuration changes.
- CloudTrail is essential for validating detection findings and pinpointing actor details.
- S3 public access settings must be locked down with service control policies or IAM guardrails to prevent accidental or intentional exposure.

---

## 8. Evidence

### GuardDuty Findings
<img width="1680" height="1050" alt="AWS GuardDuty" src="https://github.com/user-attachments/assets/7eb6e811-c146-416a-80ee-b9eef5a76b34" />


### CloudTrail Logs
 <img width="1680" height="1050" alt="AWS CloudTrail Logs" src="https://github.com/user-attachments/assets/372a0c23-2d7d-457a-9a55-e13749ef1f4d" />


---

## 9. References
- [AWS GuardDuty Documentation](https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html)
- [AWS CloudTrail Documentation](https://docs.aws.amazon.com/cloudtrail/index.html)
- [S3 Block Public Access Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-control-block-public-access.html)
