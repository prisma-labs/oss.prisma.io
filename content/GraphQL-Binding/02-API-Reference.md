# API

All examples on this page are based on a GraphQL binding instance for a GraphQL API with the following schema:

```graphql
type Query {
  user(id: ID!): User
  users: [User!]!
}

type Mutation {
  createUser(name: String!): User!
  updateUser(id: ID!, name: String!): User
  deleteUser(id: ID!): User
}

type Subscription {
  userCreated: User!
}

type User {
  id: ID!
  name: String!
}
```

The GraphQL API above is deployed to this URL: `https://graphql-binding-example-service-kcbreqbsbh.now.sh`

## The `Delegate` class

The `Delegate` class is a binding instance that gives you a lower level API to make GraphQL requests to a GraphQL server.

### Constructor

```ts
constructor({ schema, fragmentReplacements, before }: BindingOptions)
```

Creates a new instance of `Delegate`.

- `schema` (required): An executable instance of a [`GraphQLSchema`](http://graphql.org/graphql-js/type/#graphqlschema) which represents the API that should be abstracted. You can create such an instance by using the `makeExecutableSchema` or `makeRemoteExecutableSchema` functions from the [`graphql-tools`](https://www.apollographql.com/docs/graphql-tools/) library. Learn more [here](https://blog.graph.cool/graphql-server-basics-the-schema-ac5e2950214e).
- `fragmentReplacements`: A GraphQL fragment that's applied to each query, subscription or mutation sent to the abstracted API.
- `before`: A function that's executed _before_ a query, mutation or subscription request is sent to the abstracted API.

**Example**

```js
const fetch = require('node-fetch')
const { Delegate } = require('graphql-binding/dist/Delegate')
const { HttpLink } = require('apollo-link-http')
const { makeRemoteExecutableSchema } = require('graphql-tools')

const typeDefs = `
  type Query {
    user(id: ID!): User
    users: [User!]!
  }

  type Mutation {
    createUser(name: String!): User!
    updateUser(id: ID!, name: String!): User
    deleteUser(id: ID!): User
  }

  type Subscription {
    userCreated: User!
  }

  type User {
    id: ID!
    name: String!
  }
`

// Create the remote schema
const endpoint = `https://graphql-binding-example-service-kcbreqbsbh.now.sh`
const link = new HttpLink({ uri: endpoint, fetch })
const schema = makeRemoteExecutableSchema({ link, schema: typeDefs })

// Create the `before` function
const before = () => console.log(`Sending a request to the GraphQL API ...`)

const delegate = new Delegate({ schema, before })
```

In this example, we create a new instance of the `Delegate` class. First we take our remote endpoint and make an `HttpLink` which creates an `HTTP` transport for this binding. This means all requests to our GraphQL API are transported via `HTTP`. Next we make a `remote` schema, meaning the schema and it's API's exist on a remote server. The `before` function is unique to the `Delegate` class. Use this to execute code _prior_ to a GraphQL request.

### Methods

#### `before`

```ts
before: () => void
```

A function that's executed _before_ a query, mutation or subscription request is sent to the API. This applies to [`request`](#request), [`delegate`](delegate) and [`delegateSubscription`](#delegateSubscription). This lets you for example modify the `link` that's used to reach the API, implement analytics features or add a logging statement before each API request.

#### `request`

```ts
request<T = any>(query: string, variables?: {
    [key: string]: any;
}): Promise<T>;
```

`request` allows you to send a GraphQL query / mutation to the GraphQL API abstracted by this binding. It is a direct proxy for `request` from `graphql-request`, a library that enables a low level request pattern for GraphQL servers using `fetch`.

- `query` (required): A string that contains the query or mutation
- `variables` (optional): All variables that are defined in the `query` string

**Example: Sending a `createUser` mutation to the API**

```js
const mutation = `
  mutation CreateUserMutation($name: String!) {
    createUser(name: $name) {
      id
      name
    }
  }
`
const variables = { name: `Andy` }

delegate.request(mutation, variables)
  .then(createUserResult => console.log(JSON.stringify(createUserResult)))
  .catch((e) => { console.error(e) })
```

#### `delegate`

```ts
delegate(operation: QueryOrMutation, fieldName: string, args: {
    [key: string]: any;
}, infoOrQuery?: GraphQLResolveInfo | string, options?: Options): Promise<any>;
```

`delegate` allows you to [delegate](https://blog.graph.cool/graphql-schema-stitching-explained-schema-delegation-4c6caf468405) the execution of a query or mutation to the GraphQL API that's abstracted by this binding. This function is often used when building a GraphQL gateway layer.

##### Properties

- `operation` (required): Specifies whether the root field that's targeted by the delegation is query or mutation.
  - Possible values: `query`, `mutation`
- `fieldName` (required): The name of the root field that's targeted by the delegation.
- `args` (required): Any arguments to be passed to the root field that's targeted by the delegation.
- `infoOrQuery` (optional): Either a string or the `info` object - in any case this specifies the selection set for the operation which is send to the GraphQL API that's abstract by this binding.
  - If not provided, the selection set will contain all scalar fields of the requested type except for those with required arguments.
- `options` (optional): The `options` object can have two fields:
  - `transforms` (optional): Allows to perform transformations on the `info` object or the `schema`.
  - `context` (optional): Allows to pass additional information to the GraphQL API that's abstracted.

**Example: Hardcode the selection set as a string:**

```js
const args = { name: `Rowan` }
const selectionSet = `{ id, name }`

delegate.delegate(
  `mutation`,
  `createUser`,
  args,
  selectionSet
)
  .then(createUserResult => console.log(JSON.stringify(createUserResult)))
  .catch((e) => { console.error(e) })
```

<div class="glitch-embed-wrap" style="height: 420px; width: 100%;">
  <iframe src="https://glitch.com/embed/#!/embed/silver-pilot?path=server.js&sidebarCollapsed=true" alt="silver-pilot on glitch" style="height: 100%; width: 100%; border: 0;"></iframe>
</div>


#### `delegateSubscription`

```ts
delegateSubscription(fieldName: string, args?: {
    [key: string]: any;
}, infoOrQuery?: GraphQLResolveInfo | string, options?: Options): Promise<AsyncIterator<any>>;
```

`delegateSubscription` allows you to [delegate](https://blog.graph.cool/graphql-schema-stitching-explained-schema-delegation-4c6caf468405) the execution of a subscription to the GraphQL API that's abstracted by this binding. This function is often used when building a GraphQL gateway layer.

##### Properties

- `fieldName` (required): The name of the root field that's targeted by the delegation.
- `args` (required): Any arguments to be passed to the root field that's targeted by the delegation.
- `infoOrQuery`: Either a string or the `info` object - in any case this specifies the selection set for the operation which is send to the GraphQL API that's abstract by this binding.
- `options` (optional): The `options` object can have two fields:
  - `transforms` (optional): Allows to perform transformations on the `info` object or the `schema`.
  - `context` (optional): Allows to pass additional information to the GraphQL API that's abstracted.

**Example: Selection set based on the `info` object inside a GraphQL resolver**:

```js
const Subscription = {
  userCreatedSub: (parent, args, context, info) => {
    return userServiceDelegate.delegateSubscription(
      `userCreated`,
      args,
      // passing the info AST from the parent resolver
      info,
      {
        context
      }
    )
  }
}
```

> [Learn more about the `info` object.](https://blog.graph.cool/graphql-server-basics-demystifying-the-info-argument-in-graphql-resolvers-6f26249f613a)

#### `getAbstractResolvers`

```ts
getAbstractResolvers(filterSchema?: GraphQLSchema | string): IResolvers;
```

For _abstract_ GraphQL Types (`Union`, `Interface`), you need to add concrete resolvers to the graphql-js server in order to know which concrete type to return.

## The `Binding` class

Note that in addition to the properties and methods in this section, `Binding` inherits all properties and methods from the `Delegate` class documented above.

### Properties

#### `query`

Exposes all the queries of the GraphQL API that is abstracted by this binding.

#### `mutation`

Exposes all the mutations of the GraphQL API that is abstracted by this binding.

#### `subscriptions`

Exposes all the subscriptions of the GraphQL API that is abstracted by this binding.

#### `addFragmentToInfo`

```ts
addFragmentToInfo(info: GraphQLResolverInfo, fragment: string): GraphQLResolverInfo
```

Can be used to ensure that specific fields are included in the `info` object passed into the bindings.

**Example: Ensure the binding fetches the `email` field from the `User`**.

```js
import userBinding from 'graphql-binding-users'
import { addFragmentToInfo } from 'graphql-binding'

async findUser(parent, args, context, info) {
  const user = await userBinding.query.user({ id: args.id }, addFragmentToInfo(info, 'fragment EnsureEmail on User { email }'), { context })

  return user
}
```

## Understanding `Options`

The `Options` type has two fields:

- `transforms` (optional): Allows to perform transformations on the `info` object or the `schema`.
- `context` (optional): Allows to pass additional information to the GraphQL API that's abstracted.

### Transforms

Coming soon

<!-- > [Learn more about the `info` object.](https://blog.graph.cool/graphql-server-basics-demystifying-the-info-argument-in-graphql-resolvers-6f26249f613a) -->

### Context

Coming soon
