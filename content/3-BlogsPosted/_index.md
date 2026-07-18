---
title: "Blogs Posted"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

# Blogs Posted

Below are the three main blog posts I delivered during the internship, focused on cloud-native architecture, microservices security, and AWS infrastructure for AI Agent training.

### [Blog 1 - AMAZON EKS AUTO MODE & ISTIO AMBIENT MESH - THE BETTER TOGETHER SOLUTION FOR MICROSERVICES](3.1-Blog1/)
This blog introduces the powerful combination of EKS Auto Mode and Istio Ambient Mesh to address two major microservices challenges: infrastructure operations and security. It explains how EKS Auto Mode automates node lifecycle management (provisioning and scaling with Karpenter), while Istio Ambient Mesh uses a sidecar-less model (ztunnel and HBONE) to provide mTLS. The result is lower operational overhead, better cost efficiency, and strong data security from L4 to L7.

### [Blog 2 - HOW TO TRAIN A SMART AI AGENT WITHOUT GETTING OUTSMARTED](3.2-Blog2/)
This blog shares practical lessons from training real AI Agents with Multi-turn Reinforcement Learning. It highlights three core principles: (1) build a fully isolated Sandbox to prevent failures on production systems, (2) design Dense Rewards with independent evaluation to prevent Reward Hacking, and (3) strictly control trajectory and Turn Budget to reduce token waste and improve final-outcome learning quality.

### [Blog 3 - BUILDING MULTI-TURN RL INFRASTRUCTURE FOR AI AGENTS WITH AMAZON SAGEMAKER HYPERPOD](3.3-Blog3/)
This blog presents an event-driven AWS architecture for training AI Agents that make sequential multi-turn decisions. It breaks down a three-layer design: Amazon SageMaker HyperPod (on EKS) for response generation and weight updates, AWS Fargate as the Reward Environment, and Amazon Nova Forge SDK for orchestration. A key focus is the two-phase deployment strategy that automatically provisions GPU resources on S3 data upload and tears them down when processing completes to maximize cost efficiency.
