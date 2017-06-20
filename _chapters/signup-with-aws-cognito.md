---
layout: post
title: Signup with AWS Cognito
date: 2017-01-21 00:00:00
description: Tutorial on how to signup a user to the AWS Cognito User Pool using the AWS JS SDK in your React.js app.
code: frontend
comments_id: 46
---

Now let's go ahead and implement the `handleSubmit` and `handleConfirmationSubmit` methods and connect it up with our AWS Cognito setup.

<img class="code-marker" src="{{ site.url }}/assets/s.png" />Replace our `handleSubmit` and `handleConfirmationSubmit` methods in `src/containers/Signup.js` with the following.

``` javascript
handleSubmit = async (event) => {
  event.preventDefault();

  this.setState({ isLoading: true });

  try {
    const newUser = await this.signup(this.state.username, this.state.password);
    this.setState({
      newUser: newUser
    });
  }
  catch(e) {
    alert(e);
  }

  this.setState({ isLoading: false });
}

handleConfirmationSubmit = async (event) => {
  event.preventDefault();

  this.setState({ isLoading: true });

  try {
    await this.confirm(this.state.newUser, this.state.confirmationCode);
    const userToken = await this.authenticate(
      this.state.newUser,
      this.state.username,
      this.state.password
    );

    this.props.updateUserToken(userToken);
    this.props.history.push('/');
  }
  catch(e) {
    alert(e);
    this.setState({ isLoading: false });
  }
}

signup(username, password) {
  const userPool = new CognitoUserPool({
    UserPoolId: config.cognito.USER_POOL_ID,
    ClientId: config.cognito.APP_CLIENT_ID
  });
  const attributeEmail = new CognitoUserAttribute({ Name : 'email', Value : username });

  return new Promise((resolve, reject) => (
    userPool.signUp(username, password, [attributeEmail], null, (err, result) => {
      if (err) {
        reject(err);
        return;
      }

      resolve(result.user);
    })
  ));
}

confirm(user, confirmationCode) {
  return new Promise((resolve, reject) => (
    user.confirmRegistration(confirmationCode, true, function(err, result) {
      if (err) {
        reject(err);
        return;
      }
      resolve(result);
    })
  ));
}

authenticate(user, username, password) {
  const authenticationData = {
    Username: username,
    Password: password
  };
  const authenticationDetails = new AuthenticationDetails(authenticationData);

  return new Promise((resolve, reject) => (
    user.authenticateUser(authenticationDetails, {
      onSuccess: (result) => resolve(result.getIdToken().getJwtToken()),
      onFailure: (err) => reject(err),
    })
  ));
}
```

<img class="code-marker" src="{{ site.url }}/assets/s.png" />Also, include the following in our header.

``` javascript
import {
  AuthenticationDetails,
  CognitoUserPool,
  CognitoUserAttribute,
} from 'amazon-cognito-identity-js';
import config from '../config.js';
```

The flow here is pretty simple:

1. In `handleSubmit` we make a call to signup a user. This creates a new user object.

2. Save that user object to the state as `newUser`.

3. In `handleConfirmationSubmit` use the confirmation code to confirm the user.

4. With the user now confirmed, Cognito now knows that we have a new user that can login to our app.

5. Use the username and password to authenticate the newly created user. We use the `newUser` object that we had previously saved in the state. The `authenticate` call returns the user token of our new user.

6. Save the `userToken` to the app's state using the `updateUserToken` call. This is the same call we made back in our `Login` component.

7. Finally, redirect to the homepage.

Now if you were to switch over to your browser and try signing up for a new account it should redirect you to the homepage after sign up successfully completes.

![Redirect home after signup screenshot]({{ site.url }}/assets/redirect-home-after-signup.png)

A quick note on the signup flow here. If the user refreshes their page at the confirm step, they won't be able to get back and confirm that account. It forces them to create a new account instead. We are keeping things intentionally simple here but you can fix this by creating a separate page that handles the confirm step based on the email address.

[Here](http://docs.aws.amazon.com/cognito/latest/developerguide/using-amazon-cognito-user-identity-pools-javascript-examples.html#using-amazon-cognito-identity-user-pools-javascript-example-confirming-user) is some sample code that you can use to confirm an unauthenticated user.

You can also confirm an unauthenticated user from the command line, using AWS CLI:

```bash
aws cognito-idp admin-confirm-sign-up \
   --region us-east-1 \
   --user-pool-id {USER_POOL_ID} \
   --username {USERNAME}
```

Next up, we are going to create our first note.
