---
title: "Create a Cognito User Pool"
date: 2026-07-18
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

#### 1. Open Cognito

1. Open **Amazon Cognito** in the AWS Console.
2. Select **Asia Pacific (Singapore) - ap-southeast-1**.
3. Choose **Create user pool**.

#### 2. Configure sign-in

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![cognito user pool overview](/images/5-Workshop/5.3-Cognito-auth/cognito-user-pool-overview.png)

![cognito signup settings](/images/5-Workshop/5.3-Cognito-auth/cognito-signup-settings.png)


- Application type: **Single-page application (SPA)**.
- Sign-in identifier: **Email**.
- Required attribute: **Email**.
- Self-registration: **Enabled**.
- Email verification: **Send verification code**.
- MFA: **Optional** or **Off** for the lab environment.

Pool name:

```text
kts-smartagri-dev-users
```

#### 3. Configure the app client

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![cognito app client](/images/5-Workshop/5.3-Cognito-auth/cognito-app-client.png)


Create a public app client:

```text
kts-smartagri-dev-web
```

- Do not generate a client secret for browser applications.
- Enable `ALLOW_USER_SRP_AUTH` and `ALLOW_REFRESH_TOKEN_AUTH`.
- Enable token revocation.

Record the following values:

```text
User Pool ID
App Client ID
```

A SPA cannot safely protect a client secret.

#### 4. Verify with AWS CLI

`GenerateSecret` must be `false`, and `AutoVerifiedAttributes` must contain `email`.
