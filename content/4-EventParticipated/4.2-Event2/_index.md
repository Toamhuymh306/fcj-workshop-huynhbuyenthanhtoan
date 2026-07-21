---
title: "Event 2"
date: 2026-06-06
weight: 1
chapter: false
pre: " <b> 4.2. </b> "
---

# FCAJ Technical Meetup Reflection Report

### Event information

- **Event:** First Cloud AI Journey (FCAJ) Technical Meetup
- **Time:** 09:00 AM - 12:00 PM, Saturday, June 6, 2026
- **Location:** Bitexco Financial Tower, Ho Chi Minh City
- **Format:** In person
- **Role:** Attendee

### Event objectives

- Share practical knowledge about Cloud, cybersecurity, AI/ML, and modern application development.
- Demonstrate how AWS WAF and an ML-based NIDS can improve threat detection.
- Present serverless WebSocket game architecture and GraphRAG implementation on AWS.
- Build foundations in Docker, IT career development, and effective teamwork.
- Connect technical concepts to real deployment and operational challenges.

### Sessions and presenters

- **Le Hoang Gia Dai** - AWS WAF and Machine Learning NIDS
- **Nguyen Quoc Bao** - Connecting Godot with AWS WebSocket
- **Viet Phat** - GraphRAG with Amazon Bedrock and Amazon Neptune
- **Bao** - Containerization with Docker
- **Huy Phuoc** - Building effective teamwork
- **Vinh Tran** - From IT Helpdesk to Senior System Administrator

### Key sessions

#### Improving threat detection with AWS WAF and ML-NIDS

AWS WAF protects websites, API Gateway, Application Load Balancers, and CloudFront against SQL injection, XSS, malicious bots, brute-force attempts, and scraping. Static rules are effective against known patterns but have limited ability to identify zero-day attacks or previously unseen behavior.

An ML-based NIDS adds behavior-based anomaly detection. The presented workflow covered:

- Cleaning and preparing the CSE-CIC-IDS2018 dataset.
- Handling missing or invalid values and removing unnecessary features.
- Balancing classes to improve minority-attack detection.
- Evaluating models with precision, recall, F1-score, and a confusion matrix.
- Deploying VPC, EC2, ALB, AWS WAF, S3, Kinesis Data Firehose, and Lambda.
- Integrating Security Hub, GuardDuty, CloudWatch, and SNS for visibility and alerts.

The main lesson was that signature-based WAF rules and ML anomaly detection complement each other. Data quality, real-time monitoring, and continuous model updates determine long-term effectiveness.

#### Connecting Godot to AWS WebSocket for multiplayer games

API Gateway WebSocket maintains two-way communication between a Godot client and the backend. A route key such as `$request.body.action` sends messages to Lambda, while DynamoDB stores the connection ID, match status, opponent, player choice, and creation time.

- Godot manages the connection through `WebSocketPeer`, `connect_to_url()`, and polling.
- The client serializes messages as JSON and sends them with `send_text()`.
- Lambda performs matchmaking, records choices, calculates results, and returns Win/Lose/Draw.
- States such as `waiting_for_opponent`, `match_found`, `result`, and `opponent_disconnected` drive client UI updates.
- The architecture must handle stale connections, TTL, efficient DynamoDB access, and Lambda's stateless behavior.

The choice between API Gateway WebSocket + Lambda and a dedicated game-server platform depends on synchronization frequency, state management, matchmaking, and player scale.

#### Building GraphRAG with Amazon Bedrock and Amazon Neptune

RAG injects external knowledge into a prompt at query time so an answer can be grounded in domain data. GraphRAG represents entities and relationships as a graph, enabling multi-hop reasoning across connected information.

- **Managed route:** Amazon Bedrock Knowledge Bases supports chunking, entity extraction, and embeddings; Neptune Analytics stores and analyzes graph relationships.
- **Custom route:** LlamaIndex controls the knowledge-graph pipeline; Amazon Neptune stores the graph and supports Cypher queries.

The managed route reduces operational work, while the custom route provides deeper control over processing, ontology, and querying.

#### Containerization with Docker

Unlike a virtual machine that packages a complete operating system, a container shares the host kernel and is lighter and faster. Docker implements **build once, run anywhere** by packaging an application and its dependencies into an image.

- A Dockerfile defines image layers.
- Layer caching accelerates rebuilds.
- Containers provide consistent development, test, and production environments.
- Docker supports CI/CD, microservices, cloud-native applications, and legacy modernization.

#### From IT Helpdesk to Senior System Administrator

The career path begins with troubleshooting, end-user communication, and understanding how systems operate. Moving into system administration requires deeper Linux, networking, patching, monitoring, and capacity-planning skills supported by hands-on labs.

The transition toward Cloud and DevOps replaces manual operations with elastic scaling, managed services, Infrastructure as Code, version control, automation, and CI/CD. Practical projects, incident analysis, and a strong portfolio are particularly valuable in interviews.

#### The art of effective teamwork

Trello, ClickUp, Google Workspace, Slack, and Discord improve coordination, but tools cannot replace personal responsibility. An effective team needs shared objectives, explicit ownership, transparent progress updates, and mutual support when blockers occur.

### What I learned

#### Technical knowledge

- I understand defense in depth through AWS WAF, ML-NIDS, logging, and alerting.
- I can explain a serverless WebSocket flow from Godot to API Gateway, Lambda, and DynamoDB.
- I can distinguish RAG from GraphRAG and managed from custom AWS implementation routes.
- I understand images, containers, Dockerfiles, layer caching, and Docker's CI/CD role.

#### Career and teamwork mindset

- Linux, networking, and troubleshooting are essential foundations for Cloud and DevOps.
- Effective learning requires focus on core capabilities and real projects.
- Documentation, automation, and never testing directly in production are important operational habits.
- Clear communication and ownership determine team effectiveness.

### Application to KTS Smart Agri

- Place AWS WAF before CloudFront/API Gateway to filter common malicious requests.
- Combine CloudWatch Logs, metrics, alarms, and SNS to monitor the inference pipeline.
- Apply anomaly-detection thinking to upload traffic and API request rates.
- Continue packaging the AI Lambda as a Docker image for a consistent inference runtime.
- Organize diagnosis data by user and consider future GraphRAG research for plant-disease and symptom knowledge.
- Manage teamwork through a backlog, explicit owners, deadlines, and regular progress updates.

### Event experience

The event created a connected learning journey from system protection, real-time applications, and AI knowledge retrieval to software packaging and career development. I was particularly interested in the combination of AWS WAF and ML-NIDS: one layer handles known threats while the other looks for abnormal behavior.

GraphRAG suggested a future direction for KTS Smart Agri, while Docker and WebSocket provided techniques that can be applied directly. The career and teamwork sessions reinforced that a successful product depends on discipline, communication, and coordination as much as technology.

> Overall, Event 2 connected Cloud, Security, AI, Backend, and DevOps knowledge into a system-level perspective. The central lesson is that each technology delivers value only when it is placed correctly in the architecture and operated by a team with a clear process.
