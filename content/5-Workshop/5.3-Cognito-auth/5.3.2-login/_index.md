---
title: "Login Demo & Token Retrieval"
date: 2026-07-10
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

#### Launch the Frontend Web App

1. Open your Terminal or Command Prompt in the directory containing the KTs Smart Agriculture Frontend source code.
2. Start a local web server (For example, using Node.js: type `npx http-server` or use the Live Server extension in VS Code).

![Launch Web](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.3-Cognito-auth/run-server.png)

3. Open a browser and navigate to `http://localhost:8080` (or the corresponding port). The application's homepage will appear.

![Web Interface](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.3-Cognito-auth/web-home.png)

#### Sign Up & Sign In

1. On the web interface, click the **Sign Up** button.
2. Enter a valid email address and a password that meets the policy configured in the previous section. Click Sign Up.
3. Switch to the **Sign In** form, enter the newly created credentials, and log into the system.

![Login](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.3-Cognito-auth/web-login.png)

#### Verify the JWT Token in the Browser

The core purpose of this step is to confirm that Amazon Cognito has successfully returned a valid authorization ticket (Token) to the Frontend.

1. In the browser where the Web App is open, press **F12** to open the Developer Tools.
2. Navigate to the **Console** or **Application** tab (under Local Storage).
3. You should see a very long encrypted string starting with `eyJ...`. This is the **ID Token** and **Access Token** issued by Cognito.

![Token Console](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.3-Cognito-auth/console-token.png)

{{% notice info %}}
This token acts as a "boarding pass". Whenever you click the upload image button, the Javascript code in `app.js` will automatically attach this Token to the Header of the HTTP Request to prove your identity to the AWS backend.
{{% /notice %}}

#### Section Summary

Congratulations to the KTs team on successfully setting up and integrating the user authentication flow. The first security wall has been firmly established, completely blocking anonymous access.

The system is now ready for the actual data upload flow. To prepare for the crucial AI verification step next, keep the image file of a cacao leaf attacked by red spiders (not VSD fungus or anthracnose) handy to see if the classification system returns an accurate diagnosis.
