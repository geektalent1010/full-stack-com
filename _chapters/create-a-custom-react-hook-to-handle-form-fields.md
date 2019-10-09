---
layout: post
title: Create a Custom React Hook to Handle Form Fields
date: 2017-01-18 12:00:00
lang: en
ref: create-a-custom-react-hook-to-handle-form-fields
description: 
context: true
comments_id: 
---

Now before we move on to creating our sign up page, we are going to take a short detour to simplify how we handle form fields in React. We built a form as a part of our login page and we are going to do the same for our sign up page. You'll recall that in our login component we were creating two state variables to store the username and password.

``` javascript
const [email, setEmail] = useState("");
const [password, setPassword] = useState("");
```

And we also use something like this to set the state:

``` coffee
onChange={e => setEmail(e.target.value)}
```

Now we are going to do something similar for our sign up page and it'll have a few more fields than the login page. So it makes sense to simplify this process and have some common logic that all our form related components can share. Plus this is a good way to introduce the biggest benefit of React Hooks — reusing stateful logic between components.

### Creating a Custom React Hook

Let's pull the above logic into a custom React Hook.

<img class="code-marker" src="/assets/s.png" />Create a `src/libs/` directory to store all our common code.

``` bash
$ mkdir src/libs/
```

<img class="code-marker" src="/assets/s.png" />Add the following to `src/libs/hooksLib.js`.

``` javascript
import { useState } from "react";

export function useFormFields(initialState) {
  const [fields, setValues] = useState(initialState);

  return [
    fields,
    function(event) {
      setValues({
        ...fields,
        [event.target.id]: event.target.value
      });
    }
  ];
}
```

Creating a custom hook is amazingly simple. Let's go over how this works:

1. A custom React Hook starts with the word `use` in its name. So ours is called `useFormFields`.

2. Our Hook takes the initial state of our form fields as an object and saves it as a state variable called `fields`.

3. It returns an array with `fields` and a function that sets the new state based on the event object. The only difference here is that we are using `event.target.id` (which contains the id of our form field) to store the value (`event.target.value`).

And that's it! We can now use this in our Login component.

### Using Our Custom Hook

<img class="code-marker" src="/assets/s.png" />Replace our `src/containers/Login.js` with the following:

``` coffee
import React, { useState } from "react";
import { Auth } from "aws-amplify";
import { FormGroup, FormControl, ControlLabel } from "react-bootstrap";
import LoaderButton from "../components/LoaderButton";
import { useFormFields } from "../libs/hooksLib";
import "./Login.css";

export default function Login(props) {
  const [isLoading, setIsLoading] = useState(false);
  const [fields, handleFieldChange] = useFormFields({
    email: "",
    password: ""
  });

  function validateForm() {
    return fields.email.length > 0 && fields.password.length > 0;
  }

  async function handleSubmit(event) {
    event.preventDefault();

    setIsLoading(true);

    try {
      await Auth.signIn(fields.email, fields.password);
      props.userHasAuthenticated(true);
      props.history.push("/");
    } catch (e) {
      alert(e.message);
      setIsLoading(false);
    }
  }

  return (
    <div className="Login">
      <form onSubmit={handleSubmit}>
        <FormGroup controlId="email" bsSize="large">
          <ControlLabel>Email</ControlLabel>
          <FormControl
            autoFocus
            type="email"
            value={fields.email}
            onChange={handleFieldChange}
          />
        </FormGroup>
        <FormGroup controlId="password" bsSize="large">
          <ControlLabel>Password</ControlLabel>
          <FormControl
            type="password"
            value={fields.password}
            onChange={handleFieldChange}
          />
        </FormGroup>
        <LoaderButton
          block
          type="submit"
          bsSize="large"
          isLoading={isLoading}
          disabled={!validateForm()}
        >
          Login
        </LoaderButton>
      </form>
    </div>
  );
}
```

You'll notice that we are using our `useFormFields` Hook. A good way to think about custom React Hooks is to simply replace the line where we use it, with the Hook code itself. So instead of this line:

``` javascript
const [fields, handleFieldChange] = useFormFields({
  email: "",
  password: ""
});
```

Simply imagine the code for the `useFormFields` function instead!

Finally, we are setting our fields using the function our custom Hook is returning.

``` coffee
onChange={handleFieldChange}
```

Now we are ready to tackle our sign up page.
