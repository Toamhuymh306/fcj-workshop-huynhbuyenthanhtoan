---
title: "User Authentication (Cognito)"
date: 2026-07-10
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Using Amazon Cognito User Pool

In this section, you will configure and demo the security mechanism of the KTs Smart Agriculture platform using **Amazon Cognito**.

To protect the AI analysis APIs and prevent spam uploads to the S3 system, the application requires users (farmers, agricultural experts) to log in. Upon successful authentication, Cognito issues an authorization pass (JWT Token). The Web App will attach this Token to the Header of every request sent to the AWS backend.

![Cognito Auth Flow](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.3-Cognito-auth/diagram2.png)

#### Contents

- [Cognito User Pool Setup](5.3.1-user-pool-setup/)
- [Login Demo & Token Retrieval](5.3.2-login-demo/)
