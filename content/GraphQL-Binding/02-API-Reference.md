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

## The `Delegate` class

### Constructor

Creates a new instance of `Delegate`.

#### API

```ts
constructor({schema, fragmentReplacements, before}: BindingOptions)
```

- `schema` (required): An executable instance of `GraphQLSchema` which represents the API that should be abstracted.
- `fragmentReplacements`: TODO
- `before`: A function that's executed _before_ a query or mutation is sent to the API

### Properties

#### schema

### Methods

#### before

// TODO

##### API

```ts
before: () => void
```

##### Example

// TODO

#### request

`request` allows to send a GraphQL query / mutation to the GraphQL API that's abstracted by this binding. It is a proxy for `request` from `graphql-request`.

##### API

```ts
request<T = any>(query: string, variables?: {
    [key: string]: any;
}): Promise<T>;
```

- `query` (required): A string that contains the query or mutation
- `variables` (optional): Any variables that are defined in the `query` string

##### Example

```js
const mutation = `
  mutation CreateUserMutation($name: String!) {
    createUser(name: $name) {
      id
    }
  }
`
const variables = { name: `Alice` }
userServiceDelegate.request(query, variables)
  .then(createUserResult => JSON.stringify(createUserResult))
```

#### delegate

`delegate` allows to [delegate](https://blog.graph.cool/graphql-schema-stitching-explained-schema-delegation-4c6caf468405) the execution of a query or mutation to the GraphQL API that's abstracted by this binding. This function is often used when building a GraphQL gateway layer.

##### API

```ts
delegate(operation: QueryOrMutation, fieldName: string, args: {
    [key: string]: any;
}, infoOrQuery?: GraphQLResolveInfo | string, options?: Options): Promise<any>;
```

- `operation` (required): Specifies whether the root field that's targeted by the delegation is query or mutation.
  - Possible values: `query`, `mutation`
- `fieldName` (required): The name of the root field that's targeted by the delegation.
- `args` (required): Any arguments to be passed to the root field that's targeted by the delegation.
- `infoOrQuery` (optional): Either a string or the `info` object - in any case this specifies the selection set for the operation which is send to the GraphQL API that's abstract by this binding.
  - If not provided, the selection set will contain all scalar fields of the requested type except for those with required arguments.
- `options` (optional): The `options` object can have two fields:
  - `transforms` (optional): Allows to perform transformations on the `info` object or the `schema`. // TODO
  - `context` (optional): Allows to pass additional information to the GraphQL API that's abstracted.

##### Example

**Hardcode the selection set as a string:**

```js
const args = { name: `Alice` }
const selectionSet = `{ id }`
userServiceDelegate.delegate(
  `mutation`,
  `createUser`,
  args,
  selectionSet
).then(createUserResult => JSON.stringify(createUserResult))
```

**Dynamic selection set based on the `info` object inside a GraphQL resolver**:

```js
const Mutation = {
  createUserWithRandomName: (parent, args, context, info) => {
    const randomName = generateRandomName()
    const args = { name: randomName }
    return userServiceDelegate.delegate(
      `mutation`,
      `createUser`,
      args,
      info
    )
  }
}
```

> [Learn more about the `info` object.](https://blog.graph.cool/graphql-server-basics-demystifying-the-info-argument-in-graphql-resolvers-6f26249f613a)

#### delegateSubscription

`delegateSubscription` allows to [delegate](https://blog.graph.cool/graphql-schema-stitching-explained-schema-delegation-4c6caf468405) the execution of a subscription to the GraphQL API that's abstracted by this binding. This function is often used when building a GraphQL gateway layer.

##### API

```ts
delegateSubscription(fieldName: string, args?: {
    [key: string]: any;
}, infoOrQuery?: GraphQLResolveInfo | string, options?: Options): Promise<AsyncIterator<any>>;
```

- `operation` (required): Specifies whether the root field that's targeted by the delegation is query or mutation.
  - Possible values: `query`, `mutation`
- `fieldName` (required): The name of the root field that's targeted by the delegation.
- `args` (required): Any arguments to be passed to the root field that's targeted by the delegation.
- `infoOrQuery`: Either a string or the `info` object - in any case this specifies the selection set for the operation which is send to the GraphQL API that's abstract by this binding.
- `options`: TODO

##### Example

**Hardcode the selection set as a string:**

```js
const selectionSet = `{ id name }`
userServiceDelegate.delegateSubscription(
  `userCreated`,
  {},
  selectionSet
)
// TODO -> what to do with the AsyncIterator in the returned Promise?
```

**Dynamic selection set based on the `info` object inside a GraphQL resolver**:

```js
// TODO
```

> [Learn more about the `info` object.](https://blog.graph.cool/graphql-server-basics-demystifying-the-info-argument-in-graphql-resolvers-6f26249f613a)

#### getAbstractResolvers

##### API

```ts
getAbstractResolvers(filterSchema?: GraphQLSchema | string): IResolvers;
```

##### Example

// TODO

## The `Binding` class

Note that in addition to the properties and methods in this section, `Binding` inherits all properties and methods from the `Delegate` class documented above.

### Properties

#### `query`

Exposes all the queries of the GraphQL API that is abstracted by this binding.

#### `mutation`

Exposes all the mutations of the GraphQL API that is abstracted by this binding.

#### `subscriptions`

Exposes all the subscriptions of the GraphQL API that is abstracted by this binding.

### Methods

// TODO

> should `buildMethods`, `buildQueryMethods` and `buildSubscriptionMethods` even be exposed? when would you want to call them?
> I guess after `super.schema` has been changed you'd want to call them? is that a use case we should document?

## Understanding `Options`

The `Options` type has two fields:

- `transforms` (optional): Allows to perform transformations on the `info` object or the `schema`.
- `context` (optional): Allows to pass additional information to the GraphQL API that's abstracted.

### Transforms

// TODO

> [Learn more about the `info` object.](https://blog.graph.cool/graphql-server-basics-demystifying-the-info-argument-in-graphql-resolvers-6f26249f613a)

### Context

// TODO