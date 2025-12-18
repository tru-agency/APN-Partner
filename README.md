# DevOps Automation Framework (DAF) on AWS

[![AWS Partner](https://img.shields.io/badge/AWS-Partner-orange.svg)](https://aws.amazon.com/partners/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

The **DevOps Automation Framework (DAF)** is an enterprise-grade toolkit designed to accelerate CI/CD, infrastructure provisioning, and operational workflows using AWS-native services.

---

## 🏗 Architecture & Resources
**Requirement IDs:** `INT-002`, `INT-003`, `ARCH-001`, `ARCH-005`, `ARCH-006`

### Typical Deployment Resources
The framework deploys a high-availability environment consisting of:
* **Networking:** Multi-AZ VPC, NAT Gateways, Internet Gateway (IGW), and Private Subnets across 3 Availability Zones.
* **Compute:** Amazon EKS/ECS Clusters with Fargate profiles for serverless container orchestration.
* **CI/CD:** AWS CodePipeline for orchestration, CodeBuild for automated builds, and CodeGuru for static analysis.
* **Integration:** Secure integration with GitHub/GitLab via webhooks and external API connectivity via managed NAT Gateways.

### 💰 Cost Model & Sizing (`CST-001`, `CST-002`, `SIZ-001`)

| Service | Requirement | Billable? | Recommended Size (Dev/Prod) |
| :--- | :--- | :--- | :--- |
| **Amazon EKS** | Mandatory | Yes | Cluster Fee (~$0.10/hr) |
| **EC2 Nodes** | Mandatory | Yes | `t3.medium` (Dev) / `m5.large` (Prod) |
| **NAT Gateway** | Mandatory | Yes | 1 per AZ (~$0.045/hr) |
| **AWS KMS** | Mandatory | Yes | $1/month per key |

> **Licensing:** This framework is provided under the **MIT License**. All AWS infrastructure consumption costs are billed directly to the customer's AWS account.

---

## 🔒 Security & Compliance
**Requirement IDs:** `DSEC-001` to `DSEC-011`

### Data Protection (`DSEC-004`, `DSEC-006`, `DSEC-009`)
* **Private-First Access:** All S3 buckets and RDS/DynamoDB instances reside in private subnets with **S3 Public Access Block** enforced at the account level.
* **Encryption:** AES-256 server-side encryption is mandatory. Data at rest is encrypted using AWS KMS Customer Managed Keys (CMKs).
* **Key Rotation:** Yearly automated rotation is enabled for all CMKs by default.

### Identity & Access Management (`DSEC-005`)
We follow the **Principle of Least Privilege (PoLP)**:
* **DAF-CodeBuild-Role:** Scoped strictly to ECR image pulls and CloudWatch log streams.
* **DAF-EKS-Execution-Role:** Permissions restricted to Load Balancer and EBS volume management.
* **IMDSv2 Enforcement:** To prevent metadata SSRF, **IMDSv2 is strictly enforced** for all compute instances (`http-tokens: required`).

---

## 🚀 Deployment Guide
**Requirement IDs:** `INT-004`, `INT-005`, `DSEC-002`, `RM-004`

**Estimated Setup Time:** 35–45 minutes  
**Supported Regions:** All AWS Commercial Regions

### Prerequisites
1.  **Service Quotas:** Ensure quotas for VPC Elastic IPs and EKS Clusters are sufficient. Request increases via the [Service Quotas Console](https://console.aws.amazon.com/servicequotas/).
2.  **IAM User:** Do **NOT** use the AWS Root User. Deploy using an IAM User/Role with Administrative privileges.

### Steps
1.  Clone this repository.
2.  Configure your AWS CLI profile.
3.  Run `terraform init` followed by `terraform apply`.

---

## 🛠 Operations & Maintenance
**Requirement IDs:** `RM-001`, `RM-002`, `HLCH-001`

### Health Monitoring
* **Dashboard:** View the CloudWatch Dashboard `DAF-Operational-Health` for real-time metrics.
* **Manual Check:** Run `kubectl get nodes` to verify cluster node status and `aws codepipeline get-pipeline-state` for CI/CD health.

### Routine Maintenance
* **Credential Rotation:** Rotate programmatic secrets via **AWS Secrets Manager**. Click "Rotate secret immediately" for manual rotation.
* **Software Patching:** EKS worker nodes are updated via rolling updates. Update the `eks_version` variable in your configuration and re-run the deployment script.

---

## 🚑 Emergency Recovery
**Requirement IDs:** `EMER-001`, `EMER-002`, `BAR-001`

* **Infrastructure Faults:** In case of drift, use the `terraform rollback` mechanism to revert to the last stable state file.
* **Data Recovery:** Backups are managed via **RDS Automated Snapshots** and **S3 Versioning**. To restore data, use the AWS Console or CLI to restore the specific Snapshot ID to a new instance.

---

## 🎧 Support & SLAs
**Requirement IDs:** `SUP-001`, `SUP-002`, `SUP-003`

* **Technical Support:** Open a [GitHub Issue](https://github.com/tru-agency/APN-Partner/issues) or email `support@yourdomain.com`.
* **Response Times:**
    * **Critical (P0):** 4-hour response time.
    * **Standard (P1):** 24-hour response time.

---
**Created by:** TRUQUANTUM IT PRIVATE LIMITED - AWS Partner Network
