---
title: "Week 4: Docker & Amazon ECR"
date: 2026-05-08
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Week 4 Objectives:

- Package the machine learning model into an independent container.
- Push the image to the AWS cloud repository.

### Tasks:

| Day | Task                                              | Start Date | End Date   | Resources    |
| :-- | :------------------------------------------------ | :--------- | :--------- | :----------- |
| Fri | - Write the inference runtime.                         | 2026-05-08 | 2026-05-08 | Python Docs  |
| Mon | - Create the Dockerfile for a Lambda container image.  | 2026-05-11 | 2026-05-11 | Docker Docs  |
| Tue | - Build the image and run local smoke tests.           | 2026-05-12 | 2026-05-12 | Docker CLI   |
| Wed | - Create the Amazon ECR repository.                    | 2026-05-13 | 2026-05-13 | AWS ECR Docs |
| Thu | - Tag/push the image and inspect its OCI manifest.     | 2026-05-14 | 2026-05-14 | AWS CLI      |

### Outcomes:

- The AI Model is containerized, eliminating environmental conflicts.
- The image is available on Amazon ECR, ready for Lambda deployment.
