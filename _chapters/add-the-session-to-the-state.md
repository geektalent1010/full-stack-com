---
layout: post
title: Add the Session to the State
date: 2017-01-15 00:00:00
redirect_from: /chapters/add-the-user-token-to-the-state.html
description: We need to add the user session to the state of our App component in our React.js app. By lifting the state up we can pass the session to all the child containers.
context: frontend
code: frontend
comments_id: 39
---

To complete the login process we would need to update the app state with the session to reflect that the user has logged in.

### Update the App State

First we'll start by updating the application state by setting that the user is logged in. We might be tempted to store this in the `Login` container, but since we are going to use this in a lot of other places, it makes sense to lift up the state. The most logical place to do this will be in our `App` component.

<img class="code-marker" src="/assets/s.png" />Add the following to `src/App.js` right below the `class App extends Component {` line.

``` javascript
constructor(props) {
  super(props);

  this.state = {
    isAuthenticated: false
  };
}

userHasAuthenticated = authenticated => {
  this.setState({ isAuthenticated: authenticated });
}
```

This initializes the `isAuthenticated` flag in the App's state. And calling `userHasAuthenticated` updates it. But for the `Login` container to call this method we need to pass a reference of this method to it.

### Pass the Session State to the Routes

We can do this by passing in a couple of props to the child component of the routes that the `App` component creates.

<img class="code-marker" src="/assets/s.png" />Add the following right below the `render() {` line in `src/App.js`.

``` javascript
const childProps = {
  isAuthenticated: this.state.isAuthenticated,
  userHasAuthenticated: this.userHasAuthenticated
};
```

<img class="code-marker" src="/assets/s.png" />And pass them into our `Routes` component by replacing the following line in the `render` method of `src/App.js`.

``` coffee
<Routes />
```

<img class="code-marker" src="/assets/s.png" />With this.

``` coffee
<Routes childProps={childProps} />
```

Currently, our `Routes` component does not do anything with the passed in `childProps`. We need it to apply these props to the child component it is going to render. In this case we need it to apply them to our `Login` component.

<img class="code-marker" src="/assets/s.png" />To do this, create a new component in `src/components/AppliedRoute.js` and add the following.

``` coffee
import React from "react";
import { Route } from "react-router-dom";

export default ({ component: C, props: cProps, ...rest }) =>
  <Route {...rest} render={props => <C {...props} {...cProps} />} />;
```

This simple component creates a `Route` where the child component that it renders contains the passed in props.

Now to use this component, we are going to include it in the routes where we need to have the `childProps` passed in.

<img class="code-marker" src="/assets/s.png" />Replace the `export default () => (` method in `src/Routes.js` with the following.

``` coffee
export default ({ childProps }) =>
  <Switch>
    <AppliedRoute path="/" exact component={Home} props={childProps} />
    <AppliedRoute path="/login" exact component={Login} props={childProps} />
    { /* Finally, catch all unmatched routes */ }
    <Route component={NotFound} />
  </Switch>;
```

<img class="code-marker" src="/assets/s.png" />And import the new component in the header of `src/Routes.js`.

``` coffee
import AppliedRoute from "./components/AppliedRoute";
```

Now in the `Login` container we'll call the `userHasAuthenticated` method.

<img class="code-marker" src="/assets/s.png" />Replace the `alert('Logged in');` line with the following in `src/containers/Login.js`.

``` javascript
this.props.userHasAuthenticated(true);
```

### Create a Logout Button

We can now use this to display a Logout button once the user logs in. Find the following in our `src/App.js`.

``` coffee
<RouteNavItem href="/signup">Signup</RouteNavItem>
<RouteNavItem href="/login">Login</RouteNavItem>
```

<img class="code-marker" src="/assets/s.png" />And replace it with this:

``` coffee
{this.state.isAuthenticated
  ? <NavItem onClick={this.handleLogout}>Logout</NavItem>
  : [
      <RouteNavItem key={1} href="/signup">
        Signup
      </RouteNavItem>,
      <RouteNavItem key={2} href="/login">
        Login
      </RouteNavItem>
    ]}
```

Also, import the `NavItem` in the header.

<img class="code-marker" src="/assets/s.png" />Replace the `react-bootstrap` import in the header of `src/App.js` with the following.

``` coffee
import { Nav, NavItem, Navbar } from "react-bootstrap";
```

<img class="code-marker" src="/assets/s.png" />And add this `handleLogout` method to `src/App.js` above the `render() {` line as well.

``` coffee
handleLogout = event => {
  this.userHasAuthenticated(false);
}
```

Now head over to your browser and try logging in with the admin credentials we created in the [Create a Cognito Test User]({% link _chapters/create-a-cognito-test-user.md %}) chapter. You should see the Logout button appear right away.

![Login state updated screenshot](/assets/login-state-updated.png)

Now if you refresh your page you should be logged out again. This is because we are not initializing the state from the browser session. Let's look at how to do that next.
