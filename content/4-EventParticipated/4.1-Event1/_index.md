---
title: "Event 1"
date: 2026-05-23
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# FCAJ Community Day Reflection Report

### Event information

- **Event:** FCAJ Community Day
- **Time:** 09:00 AM - 12:00 PM, Saturday, May 23, 2026
- **Location:** Floor 26, Bitexco Financial Tower, Ho Chi Minh City
- **Format:** In person
- **Role:** Attendee

### Event objectives

- Connect the technology community and share experience in deploying enterprise systems.
- Explore Platform Engineering and the effective use of AI with business context.
- Learn how to build secure, reliable, and scalable multi-agent systems.
- Understand how Amazon CloudFront improves application performance, cost, security, and availability.
- Learn from a 36-hour hackathon product journey and understand why LLM outputs are not fully deterministic.

### Speakers and participating team

- **Tinh Truong** - Platform Engineer at GoTymeX
- **Anh Pham** - AWS Community Builder and Cloud Consultant at G-AsiaPacific Vietnam
- **Thinh Nguyen** - DevOps Engineer at First Cloud AI Journey
- **Team VIB** - LotusHacks 2026 participant team
- **Duc Dao** - Solutions Architect at Cloud Kinetics
- **Vy Lam** - Senior Business Systems Analyst at VPBank

### Key sessions

#### Enterprise multi-agent systems for startup credit assessment

This session presented startup credit assessment as a use case where traditional models are often insufficient. Startups may lack several years of financial statements or collateral, so an effective assessment must also consider intellectual property, the founding team, the product, market potential, and metrics such as TAM, SAM, and SOM.

- A **single-agent** approach fits simple, single-domain workflows with few tools and low-risk decisions.
- A **multi-agent** approach fits diverse data that requires financial analysis, team evaluation, and risk assessment expertise.
- Each agent should have a clear role, goal, context, permission boundary, and focused responsibility.
- The architecture should support parallel processing, audit trails, independent scaling, and fault tolerance.
- Humans must approve high-impact decisions; AI supports analysis but does not own final accountability.

#### Enterprise AI security and governance

A proof of concept may be built quickly, but production AI requires additional controls:

- Reduce the attack surface through appropriate network design, security groups, network ACLs, subnets, and private connectivity.
- Manage identity, authentication, and authorization according to least privilege.
- Mitigate prompt injection and validate model inputs and outputs.
- Protect and rotate access keys and API keys instead of embedding secrets in source code.
- Log and trace LLM decisions for audits and incident investigation.
- Follow a controlled roadmap: **Build Core → Internal Testing/SIT → UAT → Pilot → Scale**.

The session also explained that tool integration through the **Model Context Protocol (MCP)** must be governed carefully. Easier connectivity is valuable, but it can expand the risk boundary when permissions are configured incorrectly.

#### Platform Engineering in the AI era

Platform Engineering provides a shared, self-service layer for development teams instead of requiring every developer to manage the entire infrastructure stack.

- Standardize build, deployment, operations, and monitoring workflows.
- Reduce developers' cognitive load and allow them to focus on product value.
- Combine Platform Engineering, DevOps, and SRE practices to improve reliability.
- Give AI specific industry, user, and goal context rather than relying on generic questions.
- Maintain strong Software Engineering, Cloud, CI/CD, Infrastructure as Code, and product skills; GenAI supports rather than replaces these foundations.

#### Optimizing and protecting content with Amazon CloudFront

The CloudFront session showed that a CDN does more than accelerate a website. It also supports cost optimization, security, and resilience:

- Cache content at edge locations and Regional Edge Caches to reduce requests to the origin.
- Use AWS WAF, geographic restrictions, custom headers, and origin access policies to prevent unauthorized access.
- Configure Origin Failover and custom error pages to improve availability.
- Use HTTP/3, TLS session reuse, and CloudFront Functions to improve the user experience.
- Monitor traffic and select a pricing approach that matches actual usage.

#### The LotusHacks 2026 product journey

Team VIB shared its experience building an AI-assisted UI generation and editing tool within 36 hours. The team divided the work by feature, integrated the components, prioritized the demo, and repeatedly reduced scope to meet the deadline.

The most valuable lessons were to avoid **over-featuring**, focus on the core user value, prepare a clear demonstration flow, ask for help when blocked, and gather feedback from test users.

#### Why are LLM outputs not fully consistent?

Even with `temperature = 0`, cloud-hosted inference may produce different outputs because of floating-point precision, parallel computation order, sampling behavior, and provider-side infrastructure optimizations.

- An LLM should not be treated as an absolutely deterministic function.
- Prompts should be explicit, structured, and strict about the output format.
- Systems require repeated testing against realistic cases and complete logging.
- Self-hosting may be considered when control, security, or reproducibility requirements are exceptionally high.
- Production workflows need validation, retry, fallback, and human review appropriate to their risk level.

### What I learned

#### System design mindset

- Start with the users, business problem, and expected value before selecting technology.
- Separate agents by expertise and define clear responsibility boundaries.
- Treat scalability, reliability, security, and auditability as initial design requirements.

#### Technical knowledge

- I gained a clearer understanding of MCP, IAM, network isolation, logging, and tracing in enterprise AI.
- I learned how CloudFront combines caching, WAF, origin protection, and failover to protect a frontend.
- I better understand the probabilistic nature of LLMs and the need for validation, testing, retry, and fallback mechanisms.

#### Product development skills

- A strong demo should solve the core problem instead of presenting too many features.
- A technical team must be able to present the product, explain its value, and incorporate user feedback.
- Software Engineering and Cloud foundations remain essential for taking GenAI into production.

### Application to the KTS Smart Agri project

- Preserve the asynchronous Amazon S3, SQS, and Lambda architecture so components can scale and recover independently.
- Apply Cognito Authorizers, IAM least privilege, and per-user data ownership.
- Use CloudFront to serve the frontend over HTTPS, reduce origin load, and strengthen the delivery layer.
- Record CloudWatch logs, monitor inference failures, and design retry and fallback behavior for the AI pipeline.
- Validate incoming images with Amazon Rekognition before inference to reduce invalid input.
- Keep a human in the loop: disease predictions are decision-support information and should be verified before important action is taken.

### Event experience

The event clarified the difference between an AI model that works in a demonstration and a system that is safe enough for enterprise production. The sessions complemented one another: Platform Engineering provided the operational foundation; CloudFront addressed performance and delivery-layer protection; multi-agent design expanded business-processing capability; and the LLM session explained why AI systems always require testing and controls.

Team VIB's experience also showed that a deadline can help a team identify its true core feature. This lesson applies directly to KTS Smart Agri: the first priority is a stable, secure, demonstrable end-to-end flow before adding more functions.

### Event photo

![Attending FCAJ Community Day](/images/4-EventParticipated/event1.jpg)

> Overall, FCAJ Community Day strengthened my architectural knowledge, production deployment awareness, and product mindset. The central lesson is that a strong AI system must do more than return an answer: it must be secure, reliable, auditable, and genuinely useful to its users.
