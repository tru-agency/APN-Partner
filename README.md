# DevOps Automation Framework (DAF) on AWS

The **DevOps Automation Framework (DAF)** is an enterprise-grade toolkit designed to accelerate CI/CD, infrastructure provisioning, and operational workflows using AWS-native services.

---

## 🏗 Architecture & Resources

**Requirement IDs:** `INT-002`, `INT-003`, `ARCH-001`  

> **Note:** Ensure your architecture diagram is uploaded to the `/images` folder in this repository to render above.

### Typical Deployment Resources
- **Networking:** Multi-AZ VPC, NAT Gateways, and Private Subnets across 3 Availability Zones.
- **Compute:** Amazon EKS/ECS Clusters with Fargate profiles.
- **CI/CD:** AWS CodePipeline, CodeBuild, and CodeGuru.

---

## 🔒 Security & Compliance

**Requirement IDs:** `DSEC-001` to `DSEC-011`

### Public Access Disclosure (`DSEC-004`)
- The framework follows a **"Private-First"** architecture.
- **S3 Buckets:** All buckets are private with public access blocks enabled at both the account and bucket levels.
- **Databases:** RDS/DynamoDB instances reside in private subnets without public IPs.

### IAM Roles & Policies (`DSEC-005`)
Scoped IAM roles follow the **Principle of Least Privilege**:

| Role Name | Purpose | Location |
|-----------|---------|---------|
| DAF-CodeBuild-Role | Permissions for build environments to pull from ECR and push logs to CloudWatch. | IAM Service |
| DAF-EKS-Execution-Role | Allows EKS to manage AWS resources (Load Balancers, EBS) on behalf of pods. | IAM Service |
| DAF-Pipeline-Service-Role | Grants CodePipeline access to S3 artifacts and triggers for CodeBuild. | IAM Service |
| DAF-KMS-Admin-Role | Specific role for managing and rotating cryptographic keys. | IAM Service |

### Key Management & Encryption (`DSEC-006`, `DSEC-009`)
- Data protection is handled via **AWS Key Management Service (KMS)**.
- **DAF-S3-Key:** CMK used exclusively for S3 bucket-side encryption (SSE-KMS).  
- **DAF-EKS-Key:** Used for envelope encryption of Kubernetes Secrets within the EKS cluster.  
- **Rotation:** Automated yearly rotation is enabled for all CMKs.

### Secrets Management (`DSEC-007`)
- AWS Secrets Manager is used to manage all sensitive programmatic credentials.
- **Database Credentials:** RDS passwords are automatically generated and stored securely.  
- **API Keys:** Third-party integration keys (e.g., GitHub PATs) are retrieved at runtime by CodeBuild using secret ARNs.

---

## 🚀 Deployment Guide

**Requirement IDs:** `INT-004`, `INT-005`, `DSEC-002`  
**Estimated Time:** 35–45 minutes  
**Supported Regions:** All AWS Commercial Regions  

> ⚠️ **Security Warning:** Do NOT use the AWS Root User for deployment. Use an IAM user with appropriate permissions.

---

## 🛠 Operations & Recovery

### Emergency Maintenance (`EMER-001`)
- Monitor faults via the CloudWatch Dashboard `DAF-Operational-Health`.  
- For infrastructure failures, use CloudFormation/Terraform rollback to revert to the last stable state.

### Software Recovery (`EMER-002`)
- Recover critical failures by executing `terraform apply` to re-provision resources from the state file.  
- Application state is restored using the most recent **RDS/S3 snapshots**.

---

## 🎧 Support & SLAs

**Requirement IDs:** `SUP-001`, `SUP-002`, `SUP-003`  

- **How to Get Support:** Open an issue in this GitHub repository or email `support@yourdomain.com`.  
- **SLA Policy:**  
  - **Critical (P0):** 4-hour response time  
  - **Standard:** 24-hour response time

---

Created by **TRUQUANTUM IT PRIVATE LIMITED - AWS Partner Network**
