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

Set these values to match your resources:

```bash
RAW_BUCKET=<RAW_BUCKET>
QUEUE_URL=<QUEUE_URL>
AWS_REGION=ap-southeast-1
```

**Step 1 — Inspect the bucket notification**

```bash
aws s3api get-bucket-notification-configuration \
  --bucket "$RAW_BUCKET" \
  --region "$AWS_REGION"
```

The response must contain one `QueueConfigurations` entry where:

- `QueueArn` matches the inference queue ARN.
- `Events` contains `s3:ObjectCreated:*`.
- Any prefix filter matches the actual upload path, such as `uploads/`.

**Step 2 — Inspect the queue policy and visibility timeout**

```bash
aws sqs get-queue-attributes \
  --queue-url "$QUEUE_URL" \
  --attribute-names QueueArn Policy VisibilityTimeout \
  --region "$AWS_REGION"
```

Confirm that `VisibilityTimeout` is at least `720`, the policy permits `s3.amazonaws.com` to call `sqs:SendMessage`, and both `aws:SourceArn` and `aws:SourceAccount` are present.

**Step 3 — Send a test event from S3**

Use a small leaf image and a development Cognito `sub`:

```bash
aws s3 cp ./verification-leaf.jpg \
  "s3://$RAW_BUCKET/uploads/verification-leaf.jpg" \
  --metadata user-id=<COGNITO_SUB> \
  --content-type image/jpeg \
  --region "$AWS_REGION"

sleep 5

aws sqs get-queue-attributes \
  --queue-url "$QUEUE_URL" \
  --attribute-names ApproximateNumberOfMessages ApproximateNumberOfMessagesNotVisible \
  --region "$AWS_REGION"
```

Because Lambda is not connected yet, `ApproximateNumberOfMessages` should increase after a few seconds. Avoid repeatedly selecting **Poll for messages**, because a received message becomes temporarily hidden for the visibility-timeout period.

**5.4.4 completion criteria**

- The S3 notification points to the correct inference queue.
- The queue policy permits only the raw bucket in the expected AWS account.
- Uploading a new object increases the queue message count.
- No SQS event source mapping exists yet; create it only after the Lambda function in section 5.5 is ready.

If the count does not increase, verify the bucket/queue Region, notification ARN, queue policy, and prefix/suffix filter before continuing.
