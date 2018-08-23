# Creating your own Binding

## Mirroring a GraphQL API

The simplest form of a GraphQL Binding _mirrors_ the GraphQL API that it was created for. Consider the following GraphQL schema defining CRUD operations for a `User` type:

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

#### Extending the `Binding` class

To create your own binding for this API, you start by turning the deployed GraphQL API into a [remote executable schema](https://www.prisma.io/blog/how-do-graphql-remote-schemas-work-7118237c89d7/) using the `makeRemoteExecutableSchema` function from the [`graphql-tools`](https://www.apollographql.com/docs/graphql-tools/) library and then use this remote schema to extend the `Binding` class from the `graphql-binding` package.

Here's what that could look like:

```js
const fetch = require('node-fetch');
const { Binding } = require('graphql-binding');
const { HttpLink } = require('apollo-link-http');
const { makeRemoteExecutableSchema } = require('graphql-tools');
const typeDefs = require('./user-service.graphql');

class ExampleServiceBinding extends Binding {
  constructor() {
    // Create the `HttpLink` required for the remote executable schema
    const endpoint = `https://graphql-binding-example-service-kcbreqbsbh.now.sh`;
    const link = new HttpLink({ uri: endpoint, fetch });

    // Create the remote schema
    const schema = makeRemoteExecutableSchema({ link, schema: typeDefs });

    // Invoke the constructor of `Binding` with the remote schema
    super({
      schema: schema
    });
  }
}

module.exports = ExampleServiceBinding;
```

The `constructor` of the `Binding` class (that's called with `super` in the above example) builds the functions that represent the queries, mutations and subscriptions which can be sent to the GraphQL API.

Note that these functions effectively proxy the [`delegate`](./02-API-Reference.md#delegate) method, meaning all they do is invoke `delegate` with the corresponding `operation` and `fieldName` and passing on the `args`, `info` and `context` arguments (it is not possible to pass on `transforms`). It then attaches those functions to the `query`, `mutation` and `subscription` properties of the `Binding` class respectively.

#### API of the `ExampleServiceBinding` class

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

#### Generating TypeScript definitions for the `ExampleServiceBinding` class

Once you have implemented your `ExampleServiceBinding ` class, the [`graphql-binding` CLI](./03-CLI.md) helps you to generate the TypeScript type definitions for it.

Note that the CLI requires you to make an executable instance of the GraphQL schema available through a Node script. In this example, this Node script could look as follows:

```js
const fetch = require('node-fetch');
const { HttpLink } = require('apollo-link-http');
const { makeRemoteExecutableSchema } = require('graphql-tools');
const typeDefs = require('./user-service.grahpql');

// Create the `HttpLink` required for the remote executable schema
const endpoint = `https://graphql-binding-example-service-kcbreqbsbh.now.sh`;
const link = new HttpLink({ uri: endpoint, fetch });

// Create the remote schema
const schema = makeRemoteExecutableSchema({ link, schema: typeDefs });

module.exports = schema;
```

Assuming the above file is called `exampleService.js`, you can invoke the `graphql-binding` CLI as follows:

```sh
graphql-binding --language typescript --input userServiceSchema.js --outputBinding userServiceTypings.ts
```

#### Usage with GraphQL Config

The `graphql-binding` CLI integrates with GraphQL Config. This means instead of passing arguments to the command, you can write a `.graphqlconfig.yml` file which will be read by the CLI.

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

```sh
graphql-binding --language typescript --input schema.js --outputBinding mybinding.ts
```

#### Using the `ExampleServiceBinding` class

Here a few examplary calls you can now make with your `ExampleServiceBinding` class:

```js
const exampleServiceBinding = new ExampleServiceBinding();

// Send the `user` query and retrieve the `name` of `User` with id `user-0`
exampleServiceBinding.query.user({ id: `user-0` }, `{ name }`);

// Send the `users` query and retrieve the `id` and `name` of all retrieved `User`s
exampleServiceBinding.query.users({}, `{ id name }`);

// Send the `createUser` mutation and retrieve the `id` of the newly created `User`
exampleServiceBinding.mutation.createUser({ name: `Alice` }, `{ id }`);

// Send the `updateUser` mutation and retrieve the new `name` of the updated `User`
exampleServiceBinding.mutation.updateUser(
  { id: 'user-0', name: 'Bob' },
  `{ name }`
);

// Send the `deleteUser` mutation and retrieve the `id` and `name` of the deleted `User`
exampleServiceBinding.mutation.updateUser(
  { id: 'user-0', name: 'Bob' },
  `{ id name }`
);
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
    const endpoint = `https://graphql-binding-example-service-kcbreqbsbh.now.sh`;
    const link = new HttpLink({ uri: endpoint, fetch });

    // Create the remote schema
    const schema = makeRemoteExecutableSchema({ link, schema: typeDefs });

    // Invoke the constructor of `Binding` with the remote schema
    super({
      schema: schema
    });
  }

  async userExists(id) {
    const result = await this.delegate('query', 'user', { id });
    return Boolean(result);
  }
}
```

#### Using the `UserServiceBinding` class

An examplary call you can now make with your `UserServiceBinding` class:

```js
const userServiceBinding = new UserServiceBinding();

// Send the `userExists` method and retrieve a boolean if the user exists
userServiceBinding.userExists('cjkpro9ugnyqb0b77mawk139e');
```

## Business Logic Bindings

Another interesting implementation for GraphQL Bindings is server-side business logic.

Imagine we had the following schema:

```graphql
type Query {
  findUserById(id: ID!): User
}

type User {
  id: ID!
  name String
}
```

Let's implement the `findUserById` query by encapsulating our database API. Below is an example of using Bindings to encapsulate a MongoDB operation:

There are a lot of tools that help convert the GraphQL `info` parameter to your database model which can help make more efficient database operations.

```js
import graphqlMongodbProjection from 'graphql-mongodb-projection';
import typeDefs from './user-db.grahpql';

const resolvers = {
  findUserById: async (root, { id }, { db }, info) => {
    return await db
      .collection('users')
      .findOne({ _id: id }, graphqlMongodbProjection(info));
  }
};

// Create the remote schema
const schema = makeRemoteExecutableSchema({ resolvers, typeDefs });

class UserDBBinding extends Binding {
  constructor() {
    // Invoke the constructor of `Binding` with the schema
    super({
      schema
    });
  }
}
```

#### Using the `UserDBBinding` class

An examplary call you can now make with your `UserDBBinding` class:

```js
const userDBBinding = new UserDBBinding();

// Retrieve
import { MongoClient } from 'mongodb';

// Connect to the db
MongoClient.connect('mongodb://localhost:27017/exampleDb').then(async db => {
  const projection = `
    {
      id
      name
    }
  `;
  const user = await userDBBinding.query.findUserById({ id }, projection, {
    context: {
      db
    }
  });
});
```
