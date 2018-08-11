# Overview

<a href="https://github.com/graphql-binding/graphql-binding"><img src="https://imgur.com/fTa1vMv.png" alt="Prisma" height="36px"></a>

## Description

A simple way to interact with a GraphQL API is to make an HTTP `POST` request to
a GraphQL server:

Using JavaScript's `fetch`:

```js
require('isomorphic-fetch');

fetch('https://1jzxrj179.lp.gql.zone/graphql', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ query: '{ posts { title } }' }),
})
  .then(res => res.json())
  .then(res => console.log(res.data));
```

GraphQL bindings provide a convenient interface for interacting with a GraphQL
API with your favorite programming language. Instead of sending queries as strings
like we demonstrated above, you can invoke a binding function which constructs GraphQL requests and sends it to a GraphQL server for you.

The API of a GraphQL binding is very flexible, making them the perfect tool for
use cases like: accessing GraphQL APIs from scripts or webhooks, `service-to-service` communication in a microservice architecture, or building
GraphQL API gateways.

To take it even further, bindings can be useful in combination with statically
typed languages that IDEs and code editors to leverage tooling that can be configuredin such a way that they validate GraphQL requests at compile time or even provide auto-completion for an advanced developer experience.

A perspective that onboards both new engineers and seasoned teams is to think of GraphQL bindings as an auto-generated SDK (software development kit) for GraphQL APIs.

![](../../assets/bindings.png)

## Use cases

- Accessing GraphQL APIs from a script
- GraphQL-based service-to-Service communication
- Building GraphQL gateways
- Composing/Sharing GraphQL APIs as modular building blocks
- Implementing a custom GraphQL API on top of Prisma
- Implementing custom operations for an existing GraphQL API

## Simple example

Assume the following GraphQL schema is deployed to a GraphQL server on the web:

```graphql
type User {
  id: ID!
  name: String
}

type Query {
  users: [User!]!
}

type Mutation {
  createUser(name: String!): User!
}
```

The server's API accepts two operations:

- A query to retrieve all `User`s
- A mutation to create a new `User`

Now install the package `graphql-binding-example` through NPM or yarn:

```sh
yarn add graphql-binding-example

npm install graphql-binding-example
```

To see the code for this example binding, you can view that [here](https://github.com/graphql-binding/graphql-binding-example-service). You can learn how to create your own binding [here](./04-Creating-your-own-Binding.md)).

Here are three scenarios how this binding could be used:

### 1. Usage in a simple Node script

Inside a Node script, you could install the `graphql-binding-example` via `npm` or `yarn` and then use it inside the script:


<div class="glitch-embed-wrap" style="height: 420px; width: 100%;">
  <iframe src="https://glitch.com/embed/#!/embed/gem-darkness?path=server.js&previewSize=0&sidebarCollapsed=true" alt="gem-darkness on glitch" style="height: 100%; width: 100%; border: 0;"></iframe>
</div>


In this example, `createUser` takes two arguments:

1. The **arguments** for the mutation
2. The **selection set** of the mutation

The invocation of the `createUser` binding function therefore constructs and sends the following mutation to the GraphQL server:

```graphql
mutation {
  createUser(name: "Alice") {
    id
  }
}
```

Similarly, you could write script that sends the `users` query via the invocation of a binding function:

<div class="glitch-embed-wrap" style="height: 420px; width: 100%;">
  <iframe src="https://glitch.com/embed/#!/embed/boulder-earwig?path=server.js&previewSize=0&sidebarCollapsed=true" alt="boulder-earwig on glitch" style="height: 100%; width: 100%; border: 0;"></iframe>
</div>


This time, no arguments are being passed and the selection set asks for the `id` and the `name` of the `User`s being returned.

The binding function therefore constructs and sends the following query to the GraphQL server:

```graphql
query {
  users {
    id
    name
  }
}
```

### 2. Usage in a Webhook

When responding to webhooks from 3rd party services, bindings are useful to access your GraphQL API to do specific work.

For this example, consider an application which responds to webhooks from a service that has its own user accounts system. In this service, the system sends a HTTP `POST` whenever a new user is created and we want to add that user to our database.

```js

const express = require('express')

const app = express()

const userBinding = require('graphql-binding-users').default;

app.post('/users/created', function (req) {
  const name = req.body.name;

  // Create a new `User`
  userBinding.mutation
    .createUser(
      {
        name: 'Alice',
      },
      `{ id, name }`,
    )
    .then(({ id, name }) => console.log(`The ID of the created user is: ${id} and the name is ${name}`))
});
```

### 3. Usage in a GraphQL gateway

When building gateways for GraphQL-based microservices, bindings are especially helpful. In the previous example, we have demonstrated the API where the _selection set_ for the constructed query is passed as a string. For this use case, the binding API allows to pass on the `info` argument of GraphQL resolvers.

For this example, consider the `graphql-binding-users` binding representing the SDK of a GraphQL microservice and assume you are now building a GraphQL gateway layer on top of it to adjust the functionality. Let's say we want to add a search feature to the `users` query and also generate random names for the `User`s instead of letting them choose their own names.

This means the gateway layer will have the following schema:

```graphql
type User {
  id: ID!
  name: String
}

type Query {
  user(id: ID!): User
}

type Mutation {
  createUserWithRandomName: User!
}
```

Now, when implementing the resolvers for this schema, you're going to delegate the queries to the underlying GraphQL microservice - using the binding instance from the `'graphql-binding-users'` package.

```js
const userBinding = require('graphql-binding-users').default
const generateName = require('sillyname')

const resolvers = {
  Query: {
    user: async (parent, args, context, info) => {
      const users = await userBinding.query.users({}, info)
      return users.find(user => user.id === args.id)
    },
  },
  Mutation: {
    createUserWithRandomName: (parent, args, context, info) => {
      const sillyName = generateName()
      return userBinding.mutation.createUser(
        {
          name: sillyname,
        },
        info,
      )
  }
}
```

For the `users` and `createUserWithRandomName` resolvers, the binding will behave exactly like in the previous example and construct a GraphQL query/mutation to send to the API.

The big difference to the previous example with the simple Node script is that now the selection set is not _hardcoded_ any more. Instead, it is provided through the [`info`](https://blog.graph.cool/graphql-server-basics-demystifying-the-info-argument-in-graphql-resolvers-6f26249f613a) object of the incoming query/mutation. The `info` object carries the [abstract syntax tree](https://medium.com/@cjoudrey/life-of-a-graphql-query-lexing-parsing-ca7c5045fad8) (AST) of the incoming GraphQL query, meaning it knows the requested fields as well as any arguments and can simply pass them along to the underlying GraphQL API - this is called query [delegation](https://blog.graph.cool/graphql-schema-stitching-explained-schema-delegation-4c6caf468405).

> To learn more about the `info`object, check out this deep-dive [article](https://blog.graph.cool/graphql-server-basics-demystifying-the-info-argument-in-graphql-resolvers-6f26249f613a) into its structure as well as its role during the query resolution process.
