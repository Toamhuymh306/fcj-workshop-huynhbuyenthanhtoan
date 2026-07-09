---
title: "Week 7 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.7. </b> "
---
### Week 7 Objectives:

* Implement event-driven architecture.
* Decouple storage and compute tiers using Amazon SQS.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Learn Amazon SQS (Standard vs FIFO) and messaging patterns | 06/01/2026 | 06/01/2026 | <https://000031.awsstudygroup.com/> |
| 3 | - Create Standard SQS queue for Inference pipeline | 06/02/2026 | 06/02/2026 | <https://000032.awsstudygroup.com/> |
| 4 | - Configure S3 Event Notifications to send s3:ObjectCreated:* events to SQS | 06/03/2026 | 06/03/2026 | <https://000033.awsstudygroup.com/> |
| 5 | - Configure SQS queue as trigger for AI Inference Lambda | 06/04/2026 | 06/04/2026 | <https://000034.awsstudygroup.com/> |
| 6 | - Test event flow: Upload image -> S3 -> SQS -> Lambda trigger | 06/05/2026 | 06/05/2026 | <https://000035.awsstudygroup.com/> |

### Week 7 Achievements:

* Built a robust asynchronous pipeline.
* Improved resilience under traffic spikes by queueing inference requests.
