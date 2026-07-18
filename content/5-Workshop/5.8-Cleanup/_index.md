---
title: "Clean up resources"
date: 2026-07-18
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

#### Before deletion

{{% notice danger %}}
Do not perform this section in an environment serving users. Back up any images and results that must be retained before deleting buckets or the DynamoDB table.
{{% /notice %}}

Delete resources in dependency order to avoid resource-in-use errors.

#### 1. Stop the pipeline

1. Disable and delete the Inference Lambda SQS event source mapping.
2. Remove the raw bucket event notification.
3. Wait until no required messages remain in SQS.

#### 2. Delete API and Lambda resources

1. Delete the API Gateway stage/deployment, or the REST API if it is dedicated to this lab.
2. Remove API Gateway invoke permissions from Lambda.
3. Delete Results Lambda, Presign Lambda, and Inference Lambda.
4. Delete CloudWatch log groups when audit retention is not required.

#### 3. Delete data

```powershell
aws s3 rm "s3://$RawBucket" --recursive --region $AwsRegion
aws s3 rm "s3://$ProcessedBucket" --recursive --region $AwsRegion
aws s3 rm "s3://$ArchiveBucket" --recursive --region $AwsRegion

aws s3api delete-bucket --bucket $RawBucket --region $AwsRegion
aws s3api delete-bucket --bucket $ProcessedBucket --region $AwsRegion
aws s3api delete-bucket --bucket $ArchiveBucket --region $AwsRegion

aws dynamodb delete-table --table-name $ResultTable --region $AwsRegion
aws sqs delete-queue --queue-url $QueueUrl --region $AwsRegion
```

#### 4. Delete images and repository

```powershell
aws ecr delete-repository `
  --repository-name $EcrRepository `
  --force `
  --region $AwsRegion
```

#### 5. Delete Cognito and IAM resources

1. Delete the Cognito app client and User Pool.
2. Delete inline policies from the three Lambda execution roles.
3. Detach managed policies.
4. Delete the roles last.

#### 6. Verify cost removal

Check AWS Billing/Cost Explorer after 24 hours:

- Lambda has no invocations.
- S3 has no remaining storage.
- ECR has no image storage.
- Rekognition receives no `DetectLabels` calls.
- The DynamoDB table and SQS queue are deleted.

#### Deployment results

![Deployment result - cleanup empty buckets](/images/5-Workshop/5.8-Cleanup/cleanup-empty-buckets.png)

![Deployment result - cleanup resources removed](/images/5-Workshop/5.8-Cleanup/cleanup-resources-removed.png)

![Deployment result - cleanup cost check](/images/5-Workshop/5.8-Cleanup/cleanup-cost-check.png)
