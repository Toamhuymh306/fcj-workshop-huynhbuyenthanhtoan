---
title : "Prerequisite"
date : 2026-07-10
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---

#### Account and Access Permissions

An active AWS account with IAM user privileges sufficient to deploy SAM stacks, manage Lambda, S3, DynamoDB, Cognito, API Gateway, SQS, SNS, ECR, CloudFront, WAFv2, CloudTrail, and CloudWatch resources. To obtain security keys for workstation connection, perform the following steps:

**Step 1 (On AWS Console):** Log in to AWS Management Console, navigate to **IAM (Identity and Access Management)** → **Users**, select your username (e.g., `kts-agri-dev`), go to the **Security credentials** tab and locate the **Access keys** section. Click **Create access key**, select the **Command Line Interface (CLI)** use case, acknowledge the terms, and confirm creation to generate an Access Key ID and Secret Access Key pair. Download the `.csv` file to store these security keys safely.

![create iam](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.2-Prerequisite/create_iam.png)

**Step 2 (On Workstation):** Open Terminal or PowerShell on your personal computer and run `aws configure`. Enter the Access Key ID and Secret Access Key created in Step 1, set **Default region name** to `ap-southeast-1` (Singapore) and **Default output format** to `json`. This configuration is automatically saved in the user's home directory (`%USERPROFILE%\.aws\` on Windows or `~/.aws/` on Linux/macOS).

Verify the configuration is correct by running:
```bash
aws sts get-caller-identity
```
The command should return your Account ID and IAM user ARN without any authentication errors.

---

#### Local Workstation Environment

Ensure the workstation has the following tools installed before deploying:

- **AWS CLI v2** — used to interact with AWS services and deploy the WAF stack directly via CloudFormation.
- **SAM CLI** — used to build and deploy the serverless backend (Lambda functions, API Gateway, DynamoDB, and related resources).
- **Docker Desktop** — required for `sam build` to build the AI Inference Lambda Docker image containing PyTorch and the ResNet50 + LeNet model weights. Docker Desktop must be **running** at the time of build.
- **Python 3.11+** — required by SAM CLI to resolve Lambda dependencies during the build phase.
- **Node.js v20+** and **npm** — required to install dependencies and build the React frontend application.

The local environment requires a stable Internet connection to download pip packages (torch, torchvision, boto3), Docker base images from `public.ecr.aws`, and npm packages during the build phase.

---

#### Region Selection

This system deploys across two AWS regions due to a CloudFront requirement:

- **WAF WebACL** must be deployed in **us-east-1 (N. Virginia)** because CloudFront only accepts WAF WebACLs with `CLOUDFRONT` scope created in us-east-1.
- All other backend resources (Lambda, API Gateway, DynamoDB, S3, Cognito, SQS, SNS, ECR, CloudTrail) are deployed in **ap-southeast-1 (Singapore)** to optimize connection latency for end users in the Southeast Asian market and ensure compatibility of all AWS services used in the architecture.
