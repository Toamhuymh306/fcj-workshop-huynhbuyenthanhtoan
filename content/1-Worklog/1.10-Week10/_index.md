---
title: "Week 10: Authorization and Security Testing"
date: 2026-06-19
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Week 10 Objectives

- Verify that result history is isolated by Cognito user.
- Review IAM, CORS, validation, and data deletion.

### Tasks

| Date | Task | Evidence |
| :--- | :--- | :--- |
| Jun 19 | Test JPEG, PNG, WebP, and invalid inputs. | Test matrix and API results |
| Jun 22 | Verify User A cannot read User B's history. | Unit/integration test |
| Jun 23 | Verify User A cannot delete User B's record or images. | `403` response and logs |
| Jun 24 | Review least-privilege IAM, CORS, and the Cognito Authorizer. | Security checklist |
| Jun 25 | Verify owners can delete their DynamoDB record and both S3 objects. | DynamoDB/S3 evidence |

### Outcomes

- User-authorization tests pass and cross-user access is rejected.
- IAM and CORS retain only the permissions/origins required by the project.
