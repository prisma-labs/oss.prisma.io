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
* File uploadgit st
* GraphQL Subscriptions
* TypeScript typings
* GraphQL Playground
* Extensible via Express middlewares
* Query Performance Tracing (via Apollo Tracing)
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

> See [here](./04-Examples.md) for more examples and use cases.

## Workflow

Once your `graphql-yoga` server is running, you can use [GraphQL Playground](https://github.com/graphcool/graphql-playground) out of the box – typically running on `localhost:4000`. (Read [here](https://blog.graph.cool/introducing-graphql-playground-f1e0a018f05d) for more information.)

[![](https://imgur.com/6IC6Huj.png)](https://www.graphqlbin.com/RVIn)