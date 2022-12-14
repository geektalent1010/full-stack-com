---
layout: post
title: Clear the Session on Logout
date: 2017-01-16 00:00:00
description: We need to make sure to clear the logged in session using the Amazon Cognito JS SDK in our React.js app when the user logs out. We can do this using the signOut method.
context: frontend
code: frontend
comments_id: 41
---

Currently we are only removing the user token from our app's state. But when we refresh the page, we load the user token from the browser session, in effect logging them back in.

<img class="code-marker" src="/assets/s.png" />To clear the browser session on logout, replace the `handleLogout` method in our `src/App.js` with this:

``` javascript
handleLogout = (event) => {
  const currentUser = this.getCurrentUser();

  if (currentUser !== null) {
    currentUser.signOut();
  }

  this.updateUserToken(null);
}
```

Here we are once again using the AWS Cognito JS SDK to log the user out by calling `currentUser.signOut()`.

Now if you head over to your browser, logout and then refresh the page; you should be logged out completely.

If you try out the entire login flow from the beginning you'll notice that, we continue to stay on the login page through out the entire process. Next, we'll look at redirecting the page after we login and logout to make the flow makes more sense.
