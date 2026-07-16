---
title: "Cognito User Pool Setup"
date: 2026-07-10
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

1. Open the [Amazon Cognito Console](https://ap-southeast-1.console.aws.amazon.com/cognito/home?region=ap-southeast-1).
2. Click the **Create user pool** button.

{{% notice note %}}
The Cognito User Pool acts as an identity management database. It will help the KTs Smart Agriculture project securely manage user registration, login, and authorization (via JWT).
{{% /notice %}}

![cognito](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.3-Cognito-auth/cognito-home.png)

3. In the Create user pool console, configure the following:

- **Step 1 (Sign-in experience):** Select **Email** as the primary sign-in option.
- **Step 2 (Security requirements):** Configure the password policy. You can select _No MFA_ (Multi-Factor Authentication) to make the web demo process smoother and faster.

![cognito](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.3-Cognito-auth/cognito-signin.png)

- **Step 3 & Step 4:** Keep the default settings for the sign-up experience and email delivery.
- **Step 5 (Integrate your app):** + Name your User Pool `kts-smart-agri-user-pool-prod`.
  - Select to add an **App client**. Name the App client `KTs-Web-Client`.
  - Ensure you select **Public client** and **Don't generate a client secret** (Since our Frontend is Vanilla JS/HTML running in the browser, it cannot securely store a secret key).

![cognito](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.3-Cognito-auth/cognito-app-client.png)

- Click **Next** to review the configuration.
- Finally, click **Create user pool**. After successful creation, make sure to copy the **User Pool ID** and **Client ID** as we will need them for the `config.js` file in our Frontend.

![cognito](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.3-Cognito-auth/cognito-complete.png)
