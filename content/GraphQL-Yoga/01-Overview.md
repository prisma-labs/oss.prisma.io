# GraphQL Yoga

[**Open on GitHub**](https://github.com/graphcool/graphql-yoga)

## Description

GraphQL Yoga is a fully-featured GraphQL Server with focus on easy setup, performance & great developer experience.

## Overview

* **Easiest way to run a GraphQL server:** Sensible defaults & includes everything you need with minimal setup.
* **Includes Subscriptions:** Built-in support for realtime GraphQL Subscriptions using WebSockets.
* **Compatible:** Works with all GraphQL clients (Apollo, Relay...) and fits seamless in your GraphQL workflow.

GraphQL Yoga is based on the following libraries & tools:

* [`express`](https://github.com/expressjs/express)/[`apollo-server`](https://github.com/apollographql/apollo-server): Performant, extensible web server framework
* [`graphql-subscriptions`](https://github.com/apollographql/graphql-subscriptions)/[`subscriptions-transport-ws`](https://github.com/apollographql/subscriptions-transport-ws): GraphQL subscriptions server
* [`graphql.js`](https://github.com/graphql/graphql-js)/[`graphql-tools`](https://github.com/apollographql/graphql-tools): GraphQL engine & schema helpers
* [`graphql-playground`](https://github.com/graphcool/graphql-playground): Interactive GraphQL IDE

## Features

* GraphQL spec-compliant
* File upload
* GraphQL Subscriptions
* TypeScript typings
* GraphQL Playground
* Extensible via Express middlewares
* Apollo Tracing
* Accepts both `application/json` and `application/graphql` content-type
* Runs everywhere: Can be deployed via `now`, `up`, AWS Lambda, Heroku etc

## Install

Install with `npm`:

```sh
npm install graphql-yoga --save
```

Install with `yarn`:

```sh
yarn add graphql-yoga
```

## Quickstart

```ts
import { GraphQLServer } from 'graphql-yoga'
// ... or using `require()`
// const { GraphQLServer } = require('graphql-yoga')

const typeDefs = `
  type Query {
    hello(name: String): String!
  }
`

const resolvers = {
  Query: {
    hello: (_, { name }) => `Hello ${name || 'World'}`,
  },
}

const server = new GraphQLServer({ typeDefs, resolvers })
server.start(() => console.log('Server is running on localhost:4000'))
```

> To get started with `graphql-yoga`, follow the instructions in the READMEs of the [examples](./examples).

## Workflow

Once your `graphql-yoga` server is running, you can use [GraphQL Playground](https://github.com/graphcool/graphql-playground) out of the box – typically running on `localhost:4000`. (Read [here](https://blog.graph.cool/introducing-graphql-playground-f1e0a018f05d) for more information.)

[![](https://imgur.com/6IC6Huj.png)](https://www.graphqlbin.com/RVIn)


## FAQ

### How does `graphql-yoga` compare to `apollo-server` and other tools?

As mentioned above, `graphql-yoga` is built on top of a variety of other packages, such as `graphql.js`, `express` and  `apollo-server`. Each of these provide a certain piece of functionality required for building a GraphQL server.

Using these packages individually incurs overhead in the setup process and requires you to write a lot of boilerplate. `graphql-yoga` abstracts away the initial complexity and required boilerplate and let's you get started quickly with a set of sensible defaults for your server configuration.

`graphql-yoga` is like [`create-react-app`](https://github.com/facebookincubator/create-react-app) for building GraphQL servers.

### Can't I just setup my own GraphQL server using `express` and `graphql.js`?

`graphql-yoga` is all about convenience and a great "Getting Started"-experience by abstracting away the complexity that comes when you're building your own GraphQL from scratch. It's a pragmatic approach to bootstrap a GraphQL server, much like [`create-react-app`](https://github.com/facebookincubator/create-react-app) removes friction when first starting out with React.

Whenever the defaults of `graphql-yoga` are too tight of a corset for you, you can simply _eject_ from it and use the tooling it's build upon - there's no lock-in or any other kind of magic going on preventing you from this.

### How to eject from the standard `express` setup?

The core value of `graphql-yoga` is that you don't have to write the boilerplate required to configure your [express.js](https://github.com/expressjs/) application. However, once you need to add more customized behaviour to your server, the default configuration provided by `graphql-yoga` might not suit your use case any more. For example, it might be the case that you want to add more custom _middleware_ to your server, like for logging or error reporting.

For these cases, `GraphQLServer` exposes the `express.Application` directly via its [`express`](./src/index.ts#L17) property:

```js
server.express.use(myMiddleware())
```

Middlewares can also be added specifically to the GraphQL endpoint route, by using:

```js
server.express.post(server.options.endpoint, myMiddleware())
```

Any middlewares you add to that route, will be added right before the `apollo-server-express` middleware.
