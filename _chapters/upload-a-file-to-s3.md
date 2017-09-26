---
layout: post
title: Upload a File to S3
date: 2017-01-24 00:00:00
description: We want users to be able to upload a file in our React.js app and add it as an attachment to their note. To upload files to S3 directly from our React.js app we first need to get temporary IAM credentials using the AWS JS SDK. We can then use the AWS.S3 upload method to upload a file.
context: frontend
code: frontend
comments_id: 49
---

Let's now add an attachment to our note. The flow we are using here is very simple.

1. The user selects a file to upload.
2. The file is uploaded to S3 under the user's space and we get a URL back. 
3. Create a note with the file URL as the attachment.

We are going to use the AWS JS SDK to upload our files to S3. The S3 Bucket that we created previously, is secured using our Cognito Identity Pool. So before we can upload a file we should ensure that our user is authenticated and has a set of temporary IAM credentials. This is exactly the same process as when we were making secured requests to our API in the [Connect to API Gateway with IAM auth]({% link _chapters/connect-to-api-gateway-with-iam-auth.md %}) chapter.

### Upload to S3

<img class="code-marker" src="/assets/s.png" />Append the following in `src/libs/awsLib.js`.

``` coffee
export async function s3Upload(file) {
  if (!await authUser()) {
    throw new Error("User is not logged in");
  }

  const s3 = new AWS.S3({
    params: {
      Bucket: config.s3.BUCKET
    }
  });
  const filename = `${AWS.config.credentials
    .identityId}-${Date.now()}-${file.name}`;

  return s3
    .upload({
      Key: filename,
      Body: file,
      ContentType: file.type,
      ACL: "public-read"
    })
    .promise();
}
```

<img class="code-marker" src="/assets/s.png" />And add this to our `src/config.js` above the `apiGateway` block. Make sure to replace `YOUR_S3_UPLOADS_BUCKET_NAME` with the your S3 Bucket name from the [Create an S3 bucket for file uploads]({% link _chapters/create-an-s3-bucket-for-file-uploads.md %}) chapter.

```
s3: {
  BUCKET: "YOUR_S3_UPLOADS_BUCKET_NAME"
},
```

The above method does a couple of things.

1. It takes a file object as a parameter.

2. Generates a unique file name prefixed with the `identityId`. This is necessary to secure the files on a per-user basis.

3. Upload the file to S3 and set it's permissions to `public-read` to ensure that we can download it later.

4. And return a Promise object.

### Upload Before Creating a Note

Now that we have our upload methods ready, let's call them from the create note method.

<img class="code-marker" src="/assets/s.png" />Replace the `handleSubmit` method in `src/containers/NewNote.js` with the following.

``` javascript
handleSubmit = async event => {
  event.preventDefault();

  if (this.file && this.file.size > config.MAX_ATTACHMENT_SIZE) {
    alert("Please pick a file smaller than 5MB");
    return;
  }

  this.setState({ isLoading: true });

  try {
    const uploadedFilename = this.file
      ? (await s3Upload(this.file)).Location
      : null;

    await this.createNote({
      content: this.state.content,
      attachment: uploadedFilename
    });
    this.props.history.push("/");
  } catch (e) {
    alert(e);
    this.setState({ isLoading: false });
  }
}
```

<img class="code-marker" src="/assets/s.png" />And make sure to include `s3Upload` in the header by replacing the `import { invokeApig }` line with this:

``` javascript
import { invokeApig, s3Upload } from "../libs/awsLib";
```

The change we've made in the `handleSubmit` is that:

1. We upload the file using `s3Upload`.

2. Use the returned URL and add that to the note object when we create the note.

Now when we switch over to our browser and submit the form with an uploaded file we should see the note being created successfully. And the app being redirected to the homepage.

Next up we are going to make sure we clear out AWS credentials that are cached by the AWS JS SDK before we move on.
