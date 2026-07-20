---
title: "Worklog"
date: 2026-04-17
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

**On this page**, the KTs team records the internship work completed while building **KTs Smart Agriculture**.

- **Official internship period:** 12 weeks, from **April 17, 2026, to July 10, 2026**.
- **Extended program period:** through July 30, 2026; July 11–30 is not counted in the official 12-week worklog.
- **Project source code:** [github.com/Toamhuymh306/kts-smart-agri](https://github.com/Toamhuymh306/kts-smart-agri)

The project detects crop diseases from PlantVillage images. Amazon Cognito authenticates users; API Gateway and Lambda issue pre-signed URLs; the browser uploads images directly to Amazon S3; S3 sends events through Amazon SQS to a Lambda container running the PyTorch/ResNet model. DynamoDB stores user-scoped results and history, while CloudWatch provides monitoring and troubleshooting.

{{% notice info %}}
A week is marked complete only when supporting evidence exists, such as a commit/branch, AWS configuration screenshot, test log, model result, or reviewed document.
{{% /notice %}}

### 12-week roadmap

**Week 1 (Apr 17–23):** [Program kickoff, IAM, AWS CLI, and Budget](1.1-week1/)

**Week 2 (Apr 24–30):** [PlantVillage data preparation and AI requirements](1.2-week2/)

**Week 3 (May 01–07):** [LeNet/ResNet training and evaluation](1.3-week3/)

**Week 4 (May 08–14):** [Docker packaging and Amazon ECR](1.4-week4/)

**Week 5 (May 15–21):** [Serverless backend, S3, and DynamoDB](1.5-week5/)

**Week 6 (May 22–28):** [API Gateway, Cognito, and pre-signed URLs](1.6-week6/)

**Week 7 (May 29–Jun 04):** [Frontend and image-upload flow](1.7-week7/)

**Week 8 (Jun 05–11):** [End-to-end MVP and integration testing](1.8-week8/)

**Week 9 (Jun 12–18):** [Complete the S3 → SQS → Lambda pipeline](1.9-week9/)

**Week 10 (Jun 19–25):** [Authorization and security testing](1.10-week10/)

**Week 11 (Jun 26–Jul 02):** [CloudWatch, performance, and cost optimization](1.11-week11/)

**Week 12 (Jul 03–10):** [Acceptance, documentation, demo, and handover](1.12-week12/)
