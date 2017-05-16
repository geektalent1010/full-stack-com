---
layout: post
title: Add a Get Note API
date: 2017-01-01 00:00:00
description: Tutorial on adding a HTTP GET endpoint with path parameters and CORS support to AWS Lambda and API Gateway using the Serverless Framework.
code: backend
comments_id: 24
---

Now that we created a note and saved it to our database. Let's add an API to retrieve a note given it's id.

### Add the Function

<img class="code-marker" src="{{ site.url }}/assets/s.png" />Create a new file `get.js` and paste the following code

``` javascript
import * as dynamoDbLib from './libs/dynamodb-lib';
import { success, failure } from './libs/response-lib';

export async function main(event, context, callback) {
  const params = {
    TableName: 'notes',
    // 'Key' defines the partition key and sort key of the time to be retrieved
    // - 'userId': federated identity ID of the authenticated user
    // - 'noteId': path parameter
    Key: {
      userId: event.requestContext.authorizer.claims.sub,
      noteId: event.pathParameters.id,
    },
  };

  try {
    const result = await dynamoDbLib.call('get', params);
    if (result.Item) {
      // Return the retrieved item
      callback(null, success(result.Item));
    }
    else {
      callback(null, failure({status: false, error: 'Item not found.'}));
    }
  }
  catch(e) {
    callback(null, failure({status: false}));
  }
};
```

This follows exactly the same structure as our previous `create.js` function. The major difference here is that we are doing a `dynamoDbLib.call('get', params)` to get a note object given the `noteId` and `userId` that is passed in through the request.

### Configure the API Endpoint

<img class="code-marker" src="{{ site.url }}/assets/s.png" />Open the `serverless.yml` file and append the following to it. Replace `YOUR_USER_POOL_ARN` with the **Pool ARN** from the [Create a Cognito user pool]({% link _chapters/create-a-cognito-user-pool.md %}) chapter.

``` yaml
  get:
    # Defines an HTTP API endpoint that calls the main function in get.js
    # - path: url path is /notes/{id}
    # - method: GET request
    handler: get.main
    events:
      - http:
          path: notes/{id}
          method: get
          cors: true
          authorizer:
            arn: YOUR_USER_POOL_ARN
```

This defines our get note API. It adds a GET request handler with the endpoint `/notes/{id}`. And just as before we use our Cognito User Pool as the authorizer.

### Test

To test our get note API we need to mock passing in the `noteId` parameter. We are going to use the `noteId` of the note we created in the previous chapter and add in a `pathParameters` block to our mock. So it should look similar to the one below.

<img class="code-marker" src="{{ site.url }}/assets/s.png" />In our `mocks/` directory create a `get-event.json` file and add the following.

``` json
{
  "pathParameters": {
    "id": "578eb840-f70f-11e6-9d1a-1359b3b22944"
  },
  "requestContext": {
    "authorizer": {
      "claims": {
        "sub": "USER-SUB-1234"
      }
    }
  }
}
```

And we invoke our newly created function.

``` bash
$ serverless webpack invoke --function get --path mocks/get-event.json
```

The response should look similar to this.

``` bash
{
  statusCode: 200,
  headers: {
    'Access-Control-Allow-Origin': '*',
    'Access-Control-Allow-Credentials': true
  },
  body: '{"attachment":"hello.jpg","content":"hello world","createdAt":1487800950620,"noteId":"578eb840-f70f-11e6-9d1a-1359b3b22944","userId":"USER-SUB-1234"}'
}
```

Next, let's create an API to list all the notes a user has.
