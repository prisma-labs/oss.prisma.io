# API Reference

## `GraphQLServer`

### `constructor`

```ts
constructor(props: Props): GraphQLServer
```

The `props` argument accepts the following fields:

| Key | Type | Default | Note |
| ---  | --- | --- | --- |
| `typeDefs` | String  |  `null` | Contains GraphQL type definitions in [SDL](https://blog.graph.cool/graphql-sdl-schema-definition-language-6755bcb9ce51) or file path to type definitions (required if `schema` is not provided \*)  |
| `resolvers`  | Object  |  `null`  | Contains resolvers for the fields specified in `typeDefs` (required if `schema` is not provided \*) |
| `schema`  | Object |  `null`  | An instance of [`GraphQLSchema`](http://graphql.org/graphql-js/type/#graphqlschema) (required if `typeDefs` and `resolvers` are not provided \*) |
| `context`  | Object or Function  |  `{}`  | Contains custom data being passed through your resolver chain. This can be passed in as an object, or as a Function with the signature `(req: Request) => any`  |

> (*) There are two major ways of providing the [schema](https://blog.graph.cool/graphql-server-basics-the-schema-ac5e2950214e) information to the `constructor`:
>
> 1. Provide `typeDefs` and `resolvers` and omit the `schema`, in this case `graphql-yoga` will construct the `GraphQLSchema` instance using [`makeExecutableSchema`](https://www.apollographql.com/docs/graphql-tools/generate-schema.html#makeExecutableSchema) from [`graphql-tools`](https://github.com/apollographql/graphql-tools).
> 1. Provide the `schema` directly and omit `typeDefs` and `resolvers`.

Here is example of creating a new server:

```js
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
```

## `server.start(...)`

```ts
start(options: Options, callback: ((options: Options) => void) = (() => null)): Promise<void>
```

Once your `GraphQLServer` is instantiated, you can call the `start` method on it. It takes two arguments: `options`, the options object defined above, and `callback`, a function that's invoked right before the server is started. As an example, the `callback` can be used to print information that the server was now started.

The `options` object has the following fields:

| Key | Type | Default | Note |
| ---  | --- | --- | --- |
| `cors` | Object |  `null` | Contains [configuration options](https://github.com/expressjs/cors#configuration-options) for [cors](https://github.com/expressjs/cors) |
| `tracing`  | Boolean or String  |  `'http-header'`  | Indicates whether [Apollo Tracing](https://github.com/apollographql/apollo-tracing) should be en- or disabled for your server (if a string is provided, accepted values are: `'enabled'`, `'disabled'`, `'http-header'`) |
| `port`  | Number |  `4000`  | Determines the port your server will be listening on (note that you can also specify the port by setting the `PORT` environment variable) |
| `endpoint`  | String  |  `'/'`  | Defines the HTTP endpoint of your server |
| `subscriptions` | String or `false`  |  `'/'`  | Defines the subscriptions (websocket) endpoint for your server; setting to `false` disables subscriptions completely |
| `playground` | String or `false` |  `'/'`  | Defines the endpoint where you can invoke the [Playground](https://github.com/graphcool/graphql-playground); setting to `false` disables the playground endpoint |
| `uploads` | Object or `false`  | `null`  | Provides information about upload limits; the object can have any combination of the following three keys: `maxFieldSize`, `maxFileSize`, `maxFiles`; each of these have values of type Number; setting to `false` disables file uploading |

Additionally, the `options` object exposes these `apollo-server` options:

| Key | Type | Note |
| ---  | --- | --- |
| `cacheControl`  | Boolean  | Enable extension that returns Cache Control data in the response |
| `formatError`  | Number | A function to apply to every error before sending the response to clients |
| `logFunction`  | LogFunction  | A function called for logging events such as execution times |
| `rootValue` | any  | RootValue passed to GraphQL execution |
| `validationRules` | Array of functions | DAdditional GraphQL validation rules to be applied to client-specified queries |
| `fieldResolver` | GraphQLFieldResolver  | Provides information about upload limits; the object can have any combination of the following three keys: `maxFieldSize`, `maxFileSize`, `maxFiles`; each of these have values of type Number; setting to `false` disables file uploading |
| `formatParams` | Function  | A function applied for each query in a batch to format parameters before execution |
| `formatResponse` | Function | A function applied to each response after execution |
| `debug` | boolean  | Print additional debug logging if execution errors occur |

```ts
const options = {
  port: 8000,
  endpoint: '/graphql',
  subscriptions: '/subscriptions',
  playground: '/playground',
}

server.start(options, ({ port }) => console.log(`Server started, listening on port ${port} for incoming requests.`))
```

## `PubSub`

See the original documentation in [`graphql-subscriptions`](https://github.com/apollographql/graphql-subscriptions).