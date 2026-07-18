---
title: "Register, verify email, and sign in"
date: 2026-07-18
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

#### 1. Configure the frontend

Update the frontend configuration with the Region, User Pool ID, App Client ID, and API endpoint:

```javascript
const AWS_CONFIG = {
  region: "ap-southeast-1",
  cognito: {
    userPoolId: "<USER_POOL_ID>",
    clientId: "<APP_CLIENT_ID>"
  },
  apiGateway: {
    endpoint: "https://<REST_API_ID>.execute-api.ap-southeast-1.amazonaws.com/dev"
  }
};
```

#### 2. Run the frontend locally

Open `http://127.0.0.1:8000`.

#### 3. Register and verify the account

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![cognito confirm email](/images/5-Workshop/5.3-Cognito-auth/cognito-confirm-email.png)

![cognito login success](/images/5-Workshop/5.3-Cognito-auth/cognito-login-success.png)


1. Choose **Sign up**.
2. Enter an email and a password that satisfies the password policy.
3. Retrieve the verification code from the email.
4. Enter the code in the confirmation form.
5. Sign in after the account reaches `CONFIRMED` state.

The application must also support **Resend code** when a code expires or delivery is delayed.

#### 4. Inspect token claims safely

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![cognito token claims](/images/5-Workshop/5.3-Cognito-auth/cognito-token-claims.png)


Run this in DevTools Console:

```javascript
(() => {
  const token = localStorage.getItem("idToken");
  if (!token) return { error: "idToken not found" };
  const payload = JSON.parse(atob(token.split(".")[1].replace(/-/g, "+").replace(/_/g, "/")));
  return {
    token_use: payload.token_use,
    issuer: payload.iss,
    audience: payload.aud,
    expires_at: new Date(payload.exp * 1000).toISOString()
  };
})()
```

The result must contain `token_use: "id"` and an issuer for the new User Pool.

{{% notice warning %}}
The workshop's REST API Cognito Authorizer expects the raw JWT in the `Authorization` header. Do not add the `Bearer` prefix to these configured routes.
{{% /notice %}}
