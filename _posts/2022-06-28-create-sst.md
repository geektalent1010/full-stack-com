---
layout: blog
title: "`create sst`"
author: jay
---

Today we are launching [`create sst`](https://github.com/serverless-stack/sst/tree/master/packages/create-sst) — a CLI that helps you get started with SST. It creates a full-stack starter that you can use to build your serverless applications.

While serverless has been around for a few years, there isn't a [Twelve-Factor App](https://12factor.net) setup for it.

With `create sst`, we are trying to bake a lot of these principles into a starter that you can use right away. Simply run:

```bash
$ npm init sst
```

This will bootstrap a full-stack serverless application with end to end type-safety:

- RDS as the database (with an option to use DynamoDB)
  - [Kysely](https://github.com/koskimas/kysely) for SQL
  - [ElectroDB](https://github.com/tywalch/electrodb) for DynamoDB
- GraphQL as the API
  - [Pothos](https://pothos-graphql.dev) for building GraphQL schemas
- React as the frontend

It also does a few other things that you can step through using a new tutorial we added in our docs: [docs.sst.dev/learn](https://docs.sst.dev/learn/)

## Launch event

We livestreamed the `create sst` launch event. You can [check it out on YouTube](https://www.youtube.com/watch?v=wBTDkLIyMhw).

<div class="youtube-container">
  <iframe src="https://www.youtube-nocookie.com/embed/wBTDkLIyMhw" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

It includes a little demo of `create sst`. We also interview the open source maintainers of a couple of projects we use in the starter:

- [Tyler W. Walch](https://twitter.com/tinkertamper) from [ElectroDB](https://github.com/tywalch/electrodb)
- [Michael Hayes](https://twitter.com/yavascript) from [Pothos](https://pothos-graphql.dev)

## Looking ahead

With `create sst` we are planning to address most of the common concerns of application development; including handling authentication, managing secrets, writing tests, and more. As a team we are able to spend a lot more time on little details to get this setup in a place that makes your team as productive as possible. We'll also be open sourcing a codebase that we are working on internally that uses `create sst`. We'd like it to serve as an example of a real world production application.

While `create sst` helps you get started with SST easily, it's better thought of as an opinionated way to assemble the primitives that SST provides. You might want to tweak this setup or use a different one entirely.

We've got a lot more in store and we can't wait to share it with you all!
