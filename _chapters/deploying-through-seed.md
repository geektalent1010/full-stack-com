---
layout: post
title: Deploying Through Seed
lang: en
date: 2018-03-14 00:00:00
description: We are going to trigger a deployment in Seed by pushing a commit to our Serverless project in Git. In the Seed console you can view the build logs and look at the CloudFormation output.
context: true
ref: deploying-through-seed
comments_id: deploying-through-seed/177
---

Now, we are ready to make our first deployment. You can either Git push a new change to master to trigger it. Or we can just go into the **dev** stage and hit the **Trigger Deploy** button.

Let's do it through Git.

<img class="code-marker" src="/assets/s.png" />Go back to your project root and run the following.

``` bash
$ npm version patch
```

This is simply updating the NPM version for your project. It is a good way to keep track of the changes you are making to your project. And it also creates a quick Git commit for us.

<img class="code-marker" src="/assets/s.png" />Push the change using.

``` bash
$ git push
```

Now if you head into the **dev** stage in Seed, you should see a build in progress. Now to see the build logs, you can hit **Build v1**.

![Seed dev build in progress screenshot](/assets/part2/seed-dev-build-in-progress.png)

Here you'll see the build taking place live. Click on the service that is being deployed. In this case, we only have one service.

![Dev build page in progress screenshot](/assets/part2/dev-build-page-in-progress.png)

You'll see the build logs for the in progress build here.

![Dev build logs in progress screenshot](/assets/part2/dev-build-logs-in-progress.png)

Notice the tests are being run as a part of the build.

![Dev build run tests screenshot](/assets/part2/dev-build-run-tests.png)

Once the build is complete, take a look at the build log and make a note of the following:

- Region: `region`
- Cognito User Pool Id: `UserPoolId`
- Cognito App Client Id: `UserPoolClientId`
- Cognito Identity Pool Id: `IdentityPoolId`
- S3 File Uploads Bucket: `AttachmentsBucketName`
- API Gateway URL: `ServiceEndpoint`

We'll be needing these later in our frontend and when we test our APIs.

![Dev build stack output screenshot](/assets/part2/dev-build-stack-output.png)

Now head over to the app home page. You'll notice that we are ready to promote to production.

We have a manual promotion step so that you get a chance to review the changes and ensure that you are ready to push to production.

Hit the **Promote** button.

![Dev build ready to promote screenshot](/assets/part2/dev-build-ready-to-promote.png)

This brings up a dialog that will generate a Change Set. It compares the resources that are being updated with respect to what you have in production. It's a great way to compare the infrastructure changes that are being promoted.

![Review promote change set screenshot](/assets/part2/review-promote-change-set.png)

Scroll down and hit **Promote to Production**.

![Confirm promote dev build screenshot](/assets/part2/confirm-promote-dev-build.png)

You'll notice that the build is being promoted to the **prod** stage.

![prod build in progress screenshot](/assets/part2/prod-build-in-progress.png)

And if you head over to the **prod** stage, you should see your prod deployment in action. It should take a second to deploy to production. And just like before, make a note of the following.

- Region: `region`
- Cognito User Pool Id: `UserPoolId`
- Cognito App Client Id: `UserPoolClientId`
- Cognito Identity Pool Id: `IdentityPoolId`
- S3 File Uploads Bucket: `AttachmentsBucketName`
- API Gateway URL: `ServiceEndpoint`

![Prod build stack output screenshot](/assets/part2/prod-build-stack-output.png)

Next let's configure our serverless API with a custom domain.
