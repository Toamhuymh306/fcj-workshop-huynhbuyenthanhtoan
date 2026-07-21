---
title: "User authentication with Cognito"
date: 2026-07-18
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Objective

In this section, you create an Amazon Cognito User Pool, enable email verification, and connect the frontend to a public app client. API Gateway Cognito Authorizers validate the Cognito ID token before allowing Presign API and Results API calls.

![Cognito Auth Flow](/images/5-Workshop/5.3-Cognito-auth/diagram2.png)

Authentication flow:

1. A user registers with an email address and password.
2. Cognito sends a verification code by email.
3. The user confirms the account and signs in.
4. The frontend retains the ID token for the session.
5. API Gateway validates the token issuer, audience, signature, and expiration.

#### Contents

1. [Create a Cognito User Pool](5.3.1-user-pool-setup/)
2. [Register, verify email, and sign in](5.3.2-login/)
