---
title: "Week 6 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.6. </b> "
---
### Week 6 Objectives:

* Deploy containerized AI model to AWS Lambda.
* Configure Lambda resources for heavy compute tasks.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Create Lambda function using Container Image from ECR | 05/25/2026 | 05/25/2026 | <https://000026.awsstudygroup.com/> |
| 3 | - Configure Lambda settings: Timeout 5 minutes, Memory 3008 MB | 05/26/2026 | 05/26/2026 | <https://000027.awsstudygroup.com/> |
| 4 | - Grant IAM permissions for reading S3 Raw images and writing processed outputs | 05/27/2026 | 05/27/2026 | <https://000028.awsstudygroup.com/> |
| 5 | - Update Lambda logic: download object, run inference, upload result | 05/28/2026 | 05/28/2026 | <https://000029.awsstudygroup.com/> |
| 6 | - Perform manual testing of Inference Lambda in AWS Console | 05/29/2026 | 05/29/2026 | <https://000030.awsstudygroup.com/> |

### Week 6 Achievements:

* Successfully ran PyTorch model fully in a serverless environment.
* Overcame Lambda cold start and memory limitation issues.
