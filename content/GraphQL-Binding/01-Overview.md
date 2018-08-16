# Overview

<a href="https://github.com/graphql-binding/graphql-binding"><img src="https://imgur.com/fTa1vMv.png" alt="Prisma" height="36px"></a>

## Description

The most straightforward way to interact with a GraphQL API is to make an HTTP POST request to a GraphQL server, for example using JavaScript's fetch:

```js
require('isomorphic-fetch');

const query = `
  query {
    posts {
      title
    }
  }
`;

fetch('https://1jzxrj179.lp.gql.zone/graphql', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ query }),
})
  .then(res => res.json())
  .then(res => console.log(res.data));
```

GraphQL bindings provide a convenient interface for interacting with a GraphQL
API with your favorite programming language. Instead of sending queries as strings
like we demonstrated above, you can invoke a binding function which constructs a GraphQL request and sends it to a GraphQL server for you.

Bindings can be useful in combination with statically
typed languages that IDEs and code editors to leverage tooling that can be configured in such a way that they validate GraphQL requests at compile time or even provide auto-completion for an advanced developer experience.

A perspective that onboards engineers is to think of GraphQL bindings as an auto-generated [SDK](https://en.wikipedia.org/wiki/Software_development_kit) for GraphQL APIs.

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
# or
npm install graphql-binding-example
```

> You can find code for the `graphql-binding-example` [here](https://github.com/graphql-binding/graphql-binding-example-service). Learn how to create your own GraphQL binding [here](https://github.com/prisma/oss.prisma.io/blob/GraphQLBinding/content/GraphQL-Binding/04-Creating-your-own-Binding.md).

In the following, we'll discuss three scenarios illustrating how the example binding can be used.

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

<div class="glitch-embed-wrap" style="height: 420px; width: 100%;">
  <iframe src="https://glitch.com/embed/#!/embed/holy-shad?path=server.js&previewSize=0&sidebarCollapsed=true" alt="holy-shad on glitch" style="height: 100%; width: 100%; border: 0;"></iframe>
</div>

To test out a webhook against this Glitch example you can run the following:

```sh
curl -d '{"name": "Andy"}' -H "Content-Type: application/json" -X POST https://holy-shad.glitch.me/users/created
```

An example response from running this command is below:

```sh
The ID of the created user is: cjkpu8tboo6b00b77sdbnaii5 and the name is Andy
```


### 3. Usage in a GraphQL gateway

When building gateways for GraphQL-based microservices, bindings are especially helpful. In the previous example, we have demonstrated the API where the _selection set_ for the constructed query is passed as a string. For this use case, the binding API allows to pass on the `info` argument of GraphQL resolvers.

For this example, consider the `graphql-binding-example` binding representing the SDK of a GraphQL microservice and assume you are now building a GraphQL gateway layer on top of it to adjust the functionality. Let's say we want to generate a `User` from an allowed list of names:

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
  createUserWithAllowedName: User!
}
```

Now, when implementing the resolvers for this schema, you're going to delegate the queries to the underlying GraphQL microservice - using the binding instance from the `'graphql-binding-example'` package.

<div class="glitch-embed-wrap" style="height: 420px; width: 100%;">
  <iframe src="https://glitch.com/embed/#!/embed/twilight-beauty?path=server.js&sidebarCollapsed=true" alt="twilight-beauty on glitch" style="height: 100%; width: 100%; border: 0;"></iframe>
</div>

Play around with the hosted playground to see the binding in action!

For the `user` and `createUserWithAllowedName` resolvers, the binding will behave exactly like in the previous example and construct a GraphQL query/mutation to send to the API.

The big difference to the previous example with the simple Node script is that now the selection set is not _hardcoded_ any more. Instead, it is provided through the [`info`](https://blog.graph.cool/graphql-server-basics-demystifying-the-info-argument-in-graphql-resolvers-6f26249f613a) object of the incoming query/mutation. The `info` object carries the [abstract syntax tree](https://medium.com/@cjoudrey/life-of-a-graphql-query-lexing-parsing-ca7c5045fad8) (AST) of the incoming GraphQL query, meaning it knows the requested fields as well as any arguments and can simply pass them along to the underlying GraphQL API - this is called query [delegation](https://blog.graph.cool/graphql-schema-stitching-explained-schema-delegation-4c6caf468405).

> To learn more about the `info`object, check out this deep-dive [article](https://blog.graph.cool/graphql-server-basics-demystifying-the-info-argument-in-graphql-resolvers-6f26249f613a) into its structure as well as its role during the query resolution process.
