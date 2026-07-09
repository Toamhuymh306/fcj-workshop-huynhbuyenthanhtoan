---
title: "Week 8 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.8. </b> "
---
### Week 8 Objectives:

* Persist AI inference results using NoSQL database.
* Implement Amazon DynamoDB.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Learn DynamoDB basics (Table, Partition Key, Sort Key) | 06/08/2026 | 06/08/2026 | <https://000036.awsstudygroup.com/> |
| 3 | - Create AI_Diagnosis_Results table with Partition Key ImageID | 06/09/2026 | 06/09/2026 | <https://000037.awsstudygroup.com/> |
| 4 | - Add IAM policies for Inference Lambda with dynamodb:PutItem | 06/10/2026 | 06/10/2026 | <https://000038.awsstudygroup.com/> |
| 5 | - Update Lambda Python code to parse inference results and write records to DynamoDB | 06/11/2026 | 06/11/2026 | <https://000039.awsstudygroup.com/> |
| 6 | - Validate data persistence in DynamoDB Console after processing image | 06/12/2026 | 06/12/2026 | <https://000040.awsstudygroup.com/> |

### Week 8 Achievements:

* Successfully integrated a highly scalable NoSQL database.
* System now stores metadata and diagnosis output reliably.
