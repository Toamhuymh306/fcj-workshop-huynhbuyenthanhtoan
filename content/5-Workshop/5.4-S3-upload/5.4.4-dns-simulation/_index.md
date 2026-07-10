---
title: "On-premises DNS Simulation"
date: 2024-01-01
weight: 4
chapter: false
pre: " <b> 5.4.4 </b> "
---

#### Architecture Context

AWS PrivateLink endpoints rely on Elastic Network Interfaces (ENIs) deployed across different Availability Zones (AZs). While these ENIs maintain fixed IP addresses for their lifecycle, AWS strongly recommends utilizing DNS for endpoint resolution. This best practice ensures that downstream applications can seamlessly route traffic to the correct, up-to-date IP addresses even if ENIs are modified or expanded to new AZs.

In this phase, we will simulate an on-premises conditional forwarding setup. Our goal is to configure a routing mechanism that forwards DNS queries from a simulated local network directly to a Route 53 Private Hosted Zone.

#### 1. Configuring DNS Alias Records for the Interface Endpoint

First, we need to map the endpoint to a friendly DNS name within our VPC infrastructure.

1. Access the [Route 53 Hosted Zones console](https://us-east-1.console.aws.amazon.com/route53/v2/hostedzones?region=us-east-1#). Locate and select the Private Hosted Zone named `s3.us-east-1.amazonaws.com` (provisioned earlier during the environment setup).

![hosted zone](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/hosted-zone.png)

2. Create the primary A-record by selecting **Create record** and configuring the routing parameters:
   - **Record name & type:** Leave as default (Type A).
   - **Alias:** Enable the toggle.
   - **Route traffic to:** Select **Alias to VPC Endpoint**.
   - **Region:** Choose **US East (N. Virginia) [us-east-1]**.
   - **Choose endpoint:** Paste the Regional VPC Endpoint DNS name retrieved in section 4.3.

![record1](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/record1.png)

3. Instead of saving immediately, click **Add another record** to create a wildcard entry with identical routing configurations:
   - **Record name:** `*`
   - **Record type:** Type A (default).
   - **Alias:** Enable.
   - **Route traffic to:** Alias to VPC Endpoint.
   - **Region:** US East (N. Virginia) [us-east-1].
   - **Choose endpoint:** Paste the same Regional VPC Endpoint DNS name.

   Click **Create records** to finalize both entries.

![record 2](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/record2.png)
![result](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/result.png)

#### 2. Establishing a Resolver Forwarding Rule

Route 53 Resolver Forwarding Rules act as a bridge, directing DNS queries from your VPC to external or on-premises DNS servers. To mimic an on-premises environment here, we will create a rule that catches Amazon S3 DNS queries and forwards them to our Private Hosted Zone in the "VPC Cloud" to resolve the PrivateLink interface endpoint correctly.

1. In the Route 53 console, navigate to **Inbound endpoints** on the left panel.
2. Select the ID of your active inbound endpoint.

![Inbound endpoint](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/route53-1.png)

3. Note down the two provided IP addresses.

![Ip addresses](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/route53-2.png)

4. Go to **Resolver** > **Rules**, and select **Create rule**.

![Ip addresses](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/route53-3.png)

5. Define the rule specifications:
   - **Name:** `myS3Rule`
   - **Rule type:** Forward
   - **Domain name:** `s3.us-east-1.amazonaws.com`
   - **VPC:** Select `VPC On-prem`
   - **Outbound endpoint:** Select `VPCOnpremOutboundEndpoint`
   - **Target IP Addresses:** Input the two inbound endpoint IP addresses you saved earlier.

   Click **Submit** to deploy the rule.

![create rule](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/route53-4.png)
![create rule](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/route53-5.png)
![create rule](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/route53-6.png)
![create rule](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/route53-7.png)

#### 3. Validating the Architecture

Now we must verify that traffic from our simulated local network successfully resolves and routes to S3 via the Interface Endpoint.

1. Use **Session Manager** to securely connect to the `Test-Interface-Endpoint` EC2 instance.

![create rule](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/test1.png)

2. Execute a DNS lookup using the `dig` utility:
   ```bash
   dig +short s3.us-east-1.amazonaws.com
   ```
