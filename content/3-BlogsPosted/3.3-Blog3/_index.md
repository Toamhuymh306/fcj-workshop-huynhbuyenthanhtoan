---
title: "Blog 3"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---

# BUILDING MULTI-TURN RL INFRASTRUCTURE FOR AI AGENTS WITH AMAZON SAGEMAKER HYPERPOD

When AI Agents must solve multi-step workflows, traditional RL approaches that optimize single responses are not enough. This architecture shows how AWS builds Multi-turn RL infrastructure on Amazon SageMaker HyperPod.

In multi-turn settings, action quality depends on downstream outcomes over many turns. To address this, AWS introduces an event-driven training architecture for Amazon Nova models.

## How the solution works

The design has three core layers:

- Amazon SageMaker HyperPod (on EKS) for response generation and model weight updates.
- AWS Fargate as the Reward Environment for evaluation.
- Amazon Nova Forge SDK for end-to-end orchestration.

## Core idea: two-phase deployment model

Phase 1 (fixed baseline): provision foundational infrastructure once with AWS CDK (VPC, EKS, ECS, S3, Step Functions).

Phase 2 (per-run temporary compute): when a .jsonl dataset is uploaded to S3, Step Functions automatically provisions training compute (including GPU resources) and tears it down after completion.

## Outcomes

- Full workflow automation from data ingestion to training execution.
- Better observability via Step Functions and queue-level troubleshooting through SQS.
- Improved cost control with on-demand compute allocation and reduced GPU idle time.

This architecture is a strong reference pattern for Multi-turn RL workloads that require dynamic, high-performance compute.

## Practical guide

1. Deploy baseline infrastructure once using AWS CDK (network, compute, storage, and orchestration).
2. Prepare training data in .jsonl format and upload to the designated S3 input path.
3. Configure S3-triggered Step Functions to run the training pipeline automatically.
4. Track each stage in Step Functions and inspect queue/reward flow with SQS and CloudWatch metrics.
5. Verify compute teardown after each run to prevent unnecessary GPU charges.

## Article link

- [Read Blog 3 on AWS](https://aws.amazon.com/blogs/machine-learning/deploying-multi-turn-rl-infrastructure-for-amazon-nova-on-amazon-sagemaker-hyperpod/)

## Blog image

![Blog 3 image](/images/3-BlogsPosted/blog3.jpg)
