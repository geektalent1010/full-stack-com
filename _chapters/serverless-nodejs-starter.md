---
layout: post
title: Serverless Node.js Starter
redirect_from: /chapters/serverless-es7-service.html
date: 2017-05-30 00:00:00
description: A Serverless Node.js starter project that adds support for ES6/ES7 async/await methods and unit tests to your Serverless Framework project.
context: all
comments_id: 72
---

Now that we know how to set our Serverless projects up, it makes sense that we have a good starting point for our future projects. For this we created a couple of Serverless starter projects that you can use called, [Serverless Node.js Starter](https://github.com/AnomalyInnovations/serverless-nodejs-starter). We also have a Python version called [Serverless Python Starter](https://github.com/AnomalyInnovations/serverless-python-starter). Our starter projects also work really well with [Seed](https://seed.run); a fully-configured CI/CD pipeline for Serverless Framework.

[Serverless Node.js Starter](https://github.com/AnomalyInnovations/serverless-nodejs-starter) uses the [serverless-webpack](https://github.com/serverless-heaven/serverless-webpack) plugin, [Babel](https://babeljs.io), and [Jest](https://facebook.github.io/jest/). It supports:

- **Use async/await in your handler functions**
- **Support for unit tests**
  - Run `npm test` to run your tests
- **Sourcemaps for proper error messages**
  - Error message show the correct line numbers
  - Works in production with CloudWatch
- **Automatic support for multiple handler files**
  - No need to add a new entry to your `webpack.config.js`

### Demo

A demo version of this service is hosted on AWS - [`https://cvps1pt354.execute-api.us-east-1.amazonaws.com/dev/hello`](https://cvps1pt354.execute-api.us-east-1.amazonaws.com/dev/hello).

And here is the ES7 source behind it.

``` coffee
export const hello = async (event, context, callback) => {
  const response = {
    statusCode: 200,
    body: JSON.stringify({
      message: `Go Serverless v1.0! ${(await message({ time: 1, copy: 'Your function executed successfully!'}))}`,
      input: event,
    }),
  };

  callback(null, response);
};

const message = ({ time, ...rest }) => new Promise((resolve, reject) => 
  setTimeout(() => {
    resolve(`${rest.copy} (with a delay)`);
  }, time * 1000)
);
```

### Requirements

- [Configure your AWS CLI]({% link _chapters/configure-the-aws-cli.md %})
- Install the Serverless Framework `npm install serverless -g`

### Installation

To create a new Serverless project with ES7 support.

``` bash
$ serverless install --url https://github.com/AnomalyInnovations/serverless-nodejs-starter --name my-project
```

Enter the new directory.

``` bash
$ cd my-project
```

Install the Node.js packages.

``` bash
$ npm install
```

### Usage

To run a function on your local

``` bash
$ serverless invoke local --function hello
```

Run your tests

``` bash
$ npm test
```

We use Jest to run our tests. You can read more about setting up your tests [here](https://facebook.github.io/jest/docs/en/getting-started.html#content).

Deploy your project

``` bash
$ serverless deploy
```

Deploy a single function

``` bash
$ serverless deploy function --function hello
```

So give it a try and send us an [email](mailto:contact@anoma.ly) if you have any questions or open a [new issue](https://github.com/AnomalyInnovations/serverless-nodejs-starter/issues/new) if you've found a bug.


