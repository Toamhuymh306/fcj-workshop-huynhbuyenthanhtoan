---
title : "Upload Image to S3"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 5.4.3 </b> "
---

#### Upload an Image via the Web Interface

In this step, we will test the image upload functionality. As defined in the architecture, the frontend client will first request a Pre-signed URL via API Gateway & Lambda, and then use that URL to upload the image directly to the Amazon S3 bucket.

1. Open your web browser and navigate to your application's frontend URL (your CloudFront domain).
2. Authenticate using your Cognito user credentials.
3. On the upload interface, select a sample image from your local machine and click **Upload**.
4. Wait for the success message confirming that the upload process via the Pre-signed URL is complete.

![upload-ui](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-upload/upload-ui.png)

#### Verify the Object in the S3 Bucket

Now, let's check if the image was successfully stored in the correct S3 bucket.

1. Navigate to the AWS Management Console and open the **S3 console**.
2. In the left navigation pane, choose **Buckets**.
3. Click on the name of your **Raw Images** bucket (the bucket designated for initial uploads).
4. You should see the newly uploaded image file listed in the objects tab.

![check bucket](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-upload/check-bucket.png)