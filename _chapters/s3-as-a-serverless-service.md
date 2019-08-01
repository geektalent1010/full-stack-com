---
layout: post
title: S3 as a Serverless Service
description: To use CloudFormation cross-stack references for S3 in Serverless we need to "Export" the S3 bucket name using the "Ref" and the ARN of the S3 bucket using "Fn::GetAtt".
date: 2018-04-02 15:00:00
context: true
code: mono-repo
comments_id: s3-as-a-serverless-service/407
---

Just as we did with [DynamoDB in the last chapter]({% link _chapters/dynamodb-as-a-serverless-service.md %}), we'll look at splitting S3 into a separate Serverless service. It should be noted that for our simple note taking application, it does make too much sense to split S3 into its own service. But it is useful to go over the case to better understand cross-stack references in Serverless.

In the [example repo]({{ site.backend_mono_github_repo }}), you'll notice that we have a `uploads` service in the `services/` directory. And the `serverless.yml` in this service looks like the following.

``` yml
service: notes-app-mono-uploads

custom:
  # Our stage is based on what is passed in when running serverless
  # commands. Or falls back to what we have set in the provider section.
  stage: ${opt:stage, self:provider.stage}

provider:
  name: aws
  runtime: nodejs8.10
  stage: dev
  region: us-east-1

resources:
  Resources:
    S3Bucket:
      Type: AWS::S3::Bucket
      Properties:
        # Set the CORS policy
        CorsConfiguration:
          CorsRules:
            -
              AllowedOrigins:
                - '*'
              AllowedHeaders:
                - '*'
              AllowedMethods:
                - GET
                - PUT
                - POST
                - DELETE
                - HEAD
              MaxAge: 3000

  # Print out the name of the bucket that is created
  Outputs:
    AttachmentsBucketArn:
      Value:
         Fn::GetAtt:
          - S3Bucket
          - Arn
      Export:
        Name: ${self:custom.stage}-AttachmentsBucketArn

    AttachmentsBucketName:
      Value:
        Ref: S3Bucket
      Export:
        Name: ${self:custom.stage}-AttachmentsBucket
```

Most of the `Resources:` section should be fairly straightforward and is based on [Part II of this guide]({% link _chapters/configure-s3-in-serverless.md %}). So let's go over the cross-stack exports in the `Outputs:` section.

1. Just as in the [DynamoDB service]({% link _chapters/dynamodb-as-a-serverless-service.md %}), we are exporting the [ARN]({% link _chapters/what-is-an-arn.md %}) (`AttachmentsBucketArn`). For this service, we are also exporting the name of the bucket (`AttachmentsBucketName`).

2. The names of the exported values are based on the stage: `${self:custom.stage}-AttachmentsBucketArn` and `${self:custom.stage}-AttachmentsBucket`.

3. We can get the ARN by using the `Fn::GetAtt` function by passing in the ref (`S3Bucket`) and the attribute we need (`Arn`).

4. And finally, we can get the bucket name by just using the ref (`S3Bucket`). Note that unlike the DynamoDB table name, the S3 bucket name is auto-generated. So while we could get away with not exporting the DynamoDB table name; in the case of S3, we need to export it.

Now that we have the main infrastructure pieces created, let's take a look at our APIs next. For illustrative purposes we are going to create two separate API services and look at how to group them under the same API Gateway domain.
