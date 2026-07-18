---
title: "Connect S3 to SQS"
date: 2026-07-18
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

#### 1. Create the inference queue

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![sqs inference queue](/images/5-Workshop/5.4-S3-upload/sqs-inference-queue.png)


Set the visibility timeout above the Lambda timeout. For a 120-second Lambda timeout, use at least 720 seconds to accommodate event source mapping retries.

#### 2. Allow S3 to send messages

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![sqs access policy](/images/5-Workshop/5.4-S3-upload/sqs-access-policy.png)


The queue policy must allow `s3.amazonaws.com` to call `sqs:SendMessage`, constrained by:

```text
aws:SourceArn     = arn:aws:s3:::<RAW_BUCKET>
aws:SourceAccount = <ACCOUNT_ID>
```

Do not expose `SendMessage` to principal `*` without these conditions.

#### 3. Create the S3 notification

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![s3 event notification](/images/5-Workshop/5.4-S3-upload/s3-event-notification.png)


The raw bucket sends every `s3:ObjectCreated:*` event to the queue:

```json
{
  "QueueConfigurations": [
    {
      "Id": "TriggerSQSInference",
      "QueueArn": "<QUEUE_ARN>",
      "Events": ["s3:ObjectCreated:*"]
    }
  ]
}
```

Create the SQS-to-Lambda event source mapping only after Inference Lambda is deployed in section 5.5.

#### 4. Verify
