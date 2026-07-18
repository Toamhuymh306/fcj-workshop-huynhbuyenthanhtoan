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

```powershell
aws cognito-idp describe-user-pool `
  --user-pool-id <USER_POOL_ID> `
  --region ap-southeast-1 `
  --query "UserPool.{Name:Name,Status:Status,AutoVerifiedAttributes:AutoVerifiedAttributes}"

aws cognito-idp describe-user-pool-client `
  --user-pool-id <USER_POOL_ID> `
  --client-id <APP_CLIENT_ID> `
  --region ap-southeast-1 `
  --query "UserPoolClient.{ClientName:ClientName,GenerateSecret:GenerateSecret,ExplicitAuthFlows:ExplicitAuthFlows}"
```

`GenerateSecret` must be `false`, and `AutoVerifiedAttributes` must contain `email`.

#### Deployment results

![Deployment result - cognito user pool overview](/images/5-Workshop/5.3-Cognito-auth/cognito-user-pool-overview.png)

![Deployment result - cognito signup settings](/images/5-Workshop/5.3-Cognito-auth/cognito-signup-settings.png)

![Deployment result - cognito app client](/images/5-Workshop/5.3-Cognito-auth/cognito-app-client.png)
