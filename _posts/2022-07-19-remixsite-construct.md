---
layout: blog
title: "`RemixSite` construct"
author: jay
image: assets/social-cards/remixsite-construct.png
---

The new [`RemixSite`]({{ site.docs_url }}/constructs/RemixSite) construct makes it easy to deploy [Remix](https://remix.run) apps to AWS.

Remix is a frontend focussed framework that delivers modern web experiences but by relying on web standards. The SST community has been looking for a way to deploy a Remix app with SST.

Recently, [Sean Matheson](https://twitter.com/controlplusb) put together [a very comprehensive PR]({{ site.sst_github_repo }}/pull/1800) implementing an SST construct that would deploy a Remix app.

[Frank](https://twitter.com/fanjiewang) made a couple of changes and merged the PR to officially add the `RemixSite` construct to the SST family of constructs.

It allows you to:

- Access other AWS resources in your Remix loaders and actions.
- Reference environment variables from your backend.
- Easily set custom domains.
- Host your Remix apps in single-region or on the Edge.

## Launch event

We hosted a [launch livestream on YouTube](https://www.youtube.com/watch?v=ZBbRZTTCwvU) where we did a deep dive of the construct and its features.

<div class="youtube-container">
  <iframe src="https://www.youtube-nocookie.com/embed/ZBbRZTTCwvU" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

We also, took a look at the internal architecture and the construct code. Here's roughly what we covered.

- Intro
- Architecture
- Environment variables
- Access AWS services
- Custom domains
- Deploy to Edge
- How to get started
- Deep dive into the code
- How does it work with `sst start`?
- Why is the non-Edge setup the default?
- Should we be talk to the DB directly?
- Does the app size impact deploy time?
- What about the cache policy limit?
- Which Remix deployment target to use?
- Q&A

The video is timestamped, so you can jump ahead.

## Get started

You can get started with the `RemixSite` construct by taking our example for a spin:

``` bash
$ npx create-sst@latest --template=examples/remix-app my-sst-app
$ cd my-sst-app
$ npm install
$ npx sst start

# Deploy to prod
$ npx sst deploy --stage prod
```

Also make sure to [check out the docs]({{ site.docs_url }}/constructs/RemixSite).
