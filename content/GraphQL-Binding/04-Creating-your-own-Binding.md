# Creating your own Binding

## Example

### Simple use case: Mirroring a GraphQL API

The simplest form of a GraphQL binding simply _mirrors_ the GraphQL API that it was created for. Consider the following GraphQL schema defining CRUD operations for a `User` type:

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

Assume the corresponding GraphQL API is deployed to this URL: `https://example.org/user-service`

#### Extending the `Binding` class

The most straightforward to create your own binding for this API is by turning the deployed GraphQL API into a [remote executable schema](https://blog.graph.cool/how-do-graphql-remote-schemas-work-7118237c89d7) using the `makeRemoteExecutableSchema` function from the [`graphql-tools`](https://www.apollographql.com/docs/graphql-tools/) library and then use this remote schema to extend the `Binding` class from the `graphql-binding` package.

Here's what that could look like:

```js
const fetch = require('node-fetch')
const { Delegate } = require('graphql-binding')
const { HttpLink } = require('apollo-link-http')
const { makeRemoteExecutableSchema } = require('graphql-tools')
const typeDefs = require('./user-service.graphql')

class UserServiceBinding extends Binding {
  constructor() {

    // Create the `HttpLink` required for the remote executable schema
    const endpoint = `https://example.org/user-service`
    const link = new HttpLink({ uri: endpoint, fetch })

    // Create the remote schema
    const schema = makeRemoteExecutableSchema({ link, schema: typeDefs })

    // Invoke the constructor of `Binding` with the remote schema
    super({
      schema: schema,
    })
  }

}

module.exports = UserServiceBinding
```

The `constructor` of the `Binding` class (that's called with `super` in the above example) builds the functions that represent the queries, mutations and subscriptions which can be sent to the GraphQL API. Note that these functions effectively proxy the [`delegate`](./02-API-Reference.md#delegate) method, meaning all they do is invoke `delegate` with the corresponding `operation` and `fieldName` and passing on the `args`, `infoOrQuery` and `context` arguments (it is not possible to pass on `transforms`). It then attaches those functions to the `query`, `mutation` and `subscription` properties of the `Binding` class respectively.

#### API of the `UserServiceBinding` class

The `query` property represents the fields of the `Query` type from the above GraphQL schema.

- `query.user`:

  ```ts
  user: (args: <T = User | null>{ id: ID_Output }, info?: GraphQLResolveInfo | string, context?: { [key: string]: any }) => Promise<T>
  ```

- `query.users`:

  ```ts
  users: (args?: <T = User[]>{}, info?: GraphQLResolveInfo | string, context?: { [key: string]: any }) => Promise<T>
  ```

The `mutation` property represents the fields of the `Mutation` type from the above GraphQL schema.

- `mutation.createUser`:

  ```ts
  createUser: (args: <T = User>{ name: String }, info?: GraphQLResolveInfo | string, context?: { [key: string]: any }) => Promise<T>
  ```

- `mutation.updateUser`:

  ```ts
  updateUser: (args: <T = User | null>{ id: ID_Output, name: String }, info?: GraphQLResolveInfo | string, context?: { [key: string]: any }) => Promise<T>
  ```

- `mutation.deleteUser`:

    ```ts
    deleteUser: (args: <T = User | null>{ id: ID_Output }, info?: GraphQLResolveInfo | string, context?: { [key: string]: any }) => Promise<T>
    ```

The `subscription` property represents the fields of the `Subscription` type from the above GraphQL schema.

    ```ts
  userCreated: (args: <T = User>{}, info?: GraphQLResolveInfo | string, context?: { [key: string]: any }) =>  Promise<AsyncIterator<any>>
    ```

#### Generating TypeScript type definitions for the `UserServiceBinding` class

Once you have implemented your `UserServiceBinding` class, the [`graphql-binding` CLI](./03-CLI.md) helps you to generate the TypeScript type definitions for it.

Note that the CLI requires you to make an executable instance of the GraphQL schema available through a Node script. In this example, this Node script could look as follows:

```js
const fetch = require('node-fetch')
const { HttpLink } = require('apollo-link-http')
const { makeRemoteExecutableSchema } = require('graphql-tools')
const typeDefs = require('./user-service.grahpql)

// Create the `HttpLink` required for the remote executable schema
const endpoint = `https://example.org/user-service`
const link = new HttpLink({ uri: endpoint, fetch })

// Create the remote schema
const schema = makeRemoteExecutableSchema({ link, schema: typeDefs })

module.exports = schema
```

Assuming the above file is called `userServiceSchema.js`, you can invoke the `graphql-binding` CLI as follows:

```sh
graphql-binding \
  --language typescript \
  --input userServiceSchema.js \
  --outputBinding userServiceTypings.ts
```

For convenience, you can achieve the same by using the `codegen` command from the GraphQL CLI in combination with a `.graphqlconfig` file.

Assuming you have the following `.graphqlconfig` file available in your project:

```yaml
projects:
  myapp:
    schemaPath: schema.graphql
    extensions:
      codegen:
        - generator: graphql-binding
          language: typescript
          output:
            binding: mybinding.ts
```

Now invoking the `graphql codegen` command has the same effect as using the `graphql-binding` CLI with the `language`, `input` and `outputBinding` arguments.

#### Using the `UserServiceBinding` class

Here a few examplary calls you can now make with your `UserServiceBinding` class:

```js
const userServiceBinding = new UserServiceBinding()

// Send the `user` query and retrieve the `name` of `User` with id `user-0`
userServiceBinding.user({ id: `user-0` }, `{ name }`)

// Send the `users` query and retrieve the `id` and `name` of all retrieved `User`s
userServiceBinding.users({}, `{ id name }`)

// Send the `createUser` mutation and retrieve the `id` of the newly created `User`
userServiceBinding.createUser({ name: `Alice` }, `{ id }`)

// Send the `updateUser` mutation and retrieve the new `name` of the updated `User`
userServiceBinding.updateUser({ id: 'user-0',  name: 'Bob' }, `{ name }`)

// Send the `deleteUser` mutation and retrieve the `id` and `name` of the deleted `User`
userServiceBinding.updateUser({ id: 'user-0',  name: 'Bob' }, `{ id name }`)
```

#### Adding custom functionality to your binding

One common use case for GraphQL bindings is adding custom functionality to the abstracted GraphQL API. The neat thing here is that you can effectively customize a GraphQL API without adjusting its GraphQL schema. When customizing the API, you're extremely flexible since you can virtually add any kind of functionality your programming language offers.

Examples for when you'd want to do this are:

- Extending the API with custom methods (e.g. `exists` from `prisma-binding`)
- If a GraphQL API requires authentication, the binding can take care of it
- Setting default arguments for certain queries, mutations or subscriptions
- Validating and transforming input arguments or returned results
- Renaming queries and mutations (should be used with care)
- Special error handling

To add custom functionality to your binding, you need to extend your `Binding` subclass with the desired functionality. For example, here is how you could a `userExist` method which simply checks if a `User` with a given `id` exists and directly returns a boolean value as the result.

```js
class UserServiceBinding extends Binding {
  constructor() {

    // Create the `HttpLink` required for the remote executable schema
    const endpoint = `https://example.org/user-service`
    const link = new HttpLink({ uri: endpoint, fetch })

    // Create the remote schema
    const schema = makeRemoteExecutableSchema({ link, schema: typeDefs })

    // Invoke the constructor of `Binding` with the remote schema
    super({
      schema: schema,
    })
  }

  async userExists(id) {
    const result = await this.delegate(
      'query',
      'user',
      { id }
    )
    return Boolean(result)
  }
}
```