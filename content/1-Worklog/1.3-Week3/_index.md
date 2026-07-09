---
title: "Week 3 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.3. </b> "
---
### Week 3 Objectives:

* Architect a secure file upload pipeline.
* Implement S3 Pre-signed URLs to bypass API Gateway payload limits.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Research API Gateway (10 MB) and Lambda (6 MB payload) limits <br> - Learn S3 Pre-signed URL mechanism | 05/04/2026 | 05/04/2026 | <https://000011.awsstudygroup.com/> |
| 3 | - Write Lambda function (Python/Boto3) to generate put_object Pre-signed URL | 05/05/2026 | 05/05/2026 | <https://000012.awsstudygroup.com/> |
| 4 | - Attach IAM roles allowing Lambda to generate URLs for the target S3 bucket | 05/06/2026 | 05/06/2026 | <https://000013.awsstudygroup.com/> |
| 5 | - Connect URL-generation Lambda to secured API Gateway endpoint | 05/07/2026 | 05/07/2026 | <https://000014.awsstudygroup.com/> |
| 6 | - Test end-to-end direct client upload to S3 using generated URL | 05/08/2026 | 05/08/2026 | <https://000015.awsstudygroup.com/> |

### Week 3 Achievements:

* Solved large-file upload bottleneck with direct-to-S3 uploads.
* Successfully generated temporary secure upload links using Lambda and Boto3.
