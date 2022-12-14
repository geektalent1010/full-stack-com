---
layout: post
title: Set up SSL
date: 2017-02-11 00:00:00
description: We want to enable SSL or HTTPS for our React.js app on AWS. To do so we are going to request a certificate using the Certificate Manager service from AWS. We are then going to use the new certificate in our CloudFront Distributions.
context: all
comments_id: 67
---

Now that our app is being served through our domain, let's add a layer of security to it by switching to HTTPS. AWS makes this fairly easy to do, thanks to Certificate Manager.

### Request a Certificate

Select **Certificate Manager** from the list of services in your [AWS Console](https://console.aws.amazon.com).

![Select Certificate Manager service screenshot](/assets/select-certificate-manager-service.png)

Hit **Request a certificate** from the top and type in the name of our domain. Hit **Add another name to this certificate** and add our www version of our domain as well. Hit **Review and request** once you are done.

![Add domain names to certificate screenshot](/assets/add-domain-names-to-certificate.png)

On the next screen review to make sure you filled in the right domain names and hit **Confirm and request**.

![Review domain name details screenshot](/assets/review-domain-name-details.png)

And finally on the **Validation** screen, AWS let's you know which email addresses it's going to send emails to verify that it is your domain. Hit **Continue**, to send the verification emails.

![Validation for domains screenshot](/assets/validation-for-domains.png)

Now since we are setting up a certificate for two domains (the non-www and www versions), we'll be receiving two emails with a link to verify that you own the domains. Make sure to hit **I Approve** on both the emails.

![Domain verification screen screenshot](/assets/domain-verification.png)

Next, we'll associate this certificate with our CloudFront Distributions.

### Update CloudFront Distributions with Certificate

Open up our first CloudFront Distribution from our list of distributions and hit the **Edit** button.

![Select CloudFront Distribution screenshot](/assets/select-cloudfront-Distribution.png)

Now switch the **SSL Certificate** to **Custom SSL Certificate** and select the certificate we just created from the drop down. And scroll down to the bottom and hit **Yes, Edit**.

![Select custom SSL Certificate screenshot](/assets/select-custom-ssl-certificate.png)

Next, head over to the **Behaviors** tab from the top.

![Select Behaviors tab screenshot](/assets/select-behaviors-tab.png)

And select the only one we have and hit **Edit**.

![Edit Distribution Behavior screenshot](/assets/edit-distribution-behavior.png)

Then switch the **Viewer Protocol Policy** to **Redirect HTTP to HTTPS**. And scroll down to the bottom and hit **Yes, Edit**.

![Switch Viewer Protocol Policy screenshot](/assets/switch-viewer-protocol-policy.png)

Now let's do the same for our other CloudFront Distribution.

![Select custom SSL Certificate screenshot](/assets/select-custom-ssl-certificate-2.png)

But leave the **Viewer Protocol Policy** as **HTTP and HTTPS**. This is because we want our users to go straight to the HTTPS version of our non-www domain. As opposed to redirecting to the HTTPS version of our www domain before redirecting again.

![Dont switch Viewer Protocol Policy for www distribution screenshot](/assets/dont-switch-viewer-protocol-policy-for-www-distribution.png)

### Update S3 Redirect Bucket

The S3 Redirect Bucket that we created in the last chapter is redirecting to the HTTP version of our non-www domain. We should switch this to the HTTPS version to prevent the extra redirect.

Open up the S3 Redirect Bucket we created in the last chapter. Head over to the **Properties** tab and select **Static website hosting**.

![Open S3 Redirect Bucket Properties screenshot](/assets/open-s3-redirect-bucket-properties.png)

Change the **Protocol** to **https** and hit **Save**.

![Change S3 Redirect to HTTPS screenshot](/assets/change-s3-redirect-to-https.png)

And that's it. Our app should be served out on our domain through HTTPS.

![App live with certificate screenshot](/assets/app-live-with-certificate.png)

Next up, let's look at the process of deploying updates to our app.
