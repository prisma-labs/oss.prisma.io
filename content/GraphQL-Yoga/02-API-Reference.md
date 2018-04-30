# API Reference

## GraphQLServer

### constructor

```ts
constructor(props: Props): GraphQLServer
```

The `props` argument accepts the following keys:

- `typeDefs`: Contains GraphQL type definitions in [SDL](https://blog.graph.cool/graphql-sdl-schema-definition-language-6755bcb9ce51) or file path to type definitions (required if `schema` is not provided).
- `resolvers`: Contains resolvers for the fields specified in `typeDefs` (required if `schema` is not provided).
- `schema`: An instance of [`GraphQLSchema`](http://graphql.org/graphql-js/type/#graphqlschema) (required if `typeDefs` and `resolvers` are not provided).
- `context`: Contains custom data being passed through your resolver chain. This can be passed in as an object, or as a function with the signature `(req: Request) => any`.
- `schemaDirectives`: A map of schema directives where the `key` of each field corresponds to the name of a directive and the value the belonging `SchemaDirectiveVisitor`. More info [here](https://www.apollographql.com/docs/graphql-tools/schema-directives.html#Using-schema-directives).
- `directiveResolvers`: A map of directive resolvers where the `key` of each field corresponds to the name of a directive and the value the belonging directive resolver. More info [here](https://www.apollographql.com/docs/graphql-tools/schema-directives.html#What-about-directiveResolvers).
- `middlwares`:  A list of middleware functions that are based on [`graphql-middleware`](https://github.com/graphcool/graphql-middleware).
- `resolverValidationOptions`: A list of validation options for your resolvers. More info [here](https://www.apollographql.com/docs/graphql-tools/resolvers.html#addResolveFunctionsToSchema).

<!-- | **Key** | **Type** | **Default** | **Note** |
| ---  | --- | --- | --- |
| `typeDefs` | String  |  `null` | Contains GraphQL type definitions in [SDL](https://blog.graph.cool/graphql-sdl-schema-definition-language-6755bcb9ce51) or file path to type definitions (required if `schema` is not provided)  |
| `resolvers`  | Object  |  `null`  | Contains resolvers for the fields specified in `typeDefs` (required if `schema` is not provided) |
| `schema`  | Object |  `null`  | An instance of [`GraphQLSchema`](http://graphql.org/graphql-js/type/#graphqlschema) (required if `typeDefs` and `resolvers` are not provided) |
| `context`  | Object or Function  |  `{}`  | Contains custom data being passed through your resolver chain. This can be passed in as an object, or as a Function with the signature `(req: Request) => any`  |
| `directiveResolvers`  | Object  | `null` |  A map of directive resolvers where the `key` of each field corresponds to the name of a directive and the value the belonging directive resolver. More info [here](https://www.apollographql.com/docs/graphql-tools/schema-directives.html#What-about-directiveResolvers). |
| `schemaDirectives`  | Object | `null` | A map of schema directives where the `key` of each field corresponds to the name of a directive and the value the belonging `SchemaDirectiveVisitor`. More info [here](https://www.apollographql.com/docs/graphql-tools/schema-directives.html#Using-schema-directives). |
| `middlewares`  | [Function] |  | A list of middleware functions based on [`graphql-middleware`](https://github.com/graphcool/graphql-middleware). |
| `resolverValidationOptions` | Object | `null` | A list of validation options for your resolvers. More info [here](https://www.apollographql.com/docs/graphql-tools/resolvers.html#addResolveFunctionsToSchema). | -->

Here is simple example of using the `GraphQLServer` constructor:

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

## start

```ts
start(options: Options, callback: ((options: Options) => void) = (() => null)): Promise<void>
```

Once your `GraphQLServer` is instantiated, you can call the `start` method on it. It takes two arguments:

- `options`: The `options` object defined below.
- `callback`: A function that's invoked right before the server is started. As an example, the `callback` can be used to print information that the server was now started.

The `options` argument accepts the following keys:

- `port` (default: `4000`): Determines the port your server will be listening on (note that you can also specify the port by setting the `PORT` environment variable).
- `cors`: Contains [configuration options](https://github.com/expressjs/cors#configuration-options) for [cors](https://github.com/expressjs/cors).
- `uploads`: Lets you specify maximum file capacities. More info [here](https://github.com/jaydenseric/apollo-upload-server#options).
- `endpoint` (default: `'/'`): Defines the HTTP endpoint of your server.
- `subscriptions` (default: `'/'`): Defines the subscriptions (websocket) endpoint of your server. Setting to `false` disables subscriptions entirely.
- `playground` (default: `'/'`): Defines the endpoint where you can invoke the [Playground](https://github.com/graphcool/graphql-playground). Setting to `false` disables the Playground endpoint entirely.
- `https`: Options for securing your web server via HTTPS. More info [here](https://nodejs.org/api/https.html).
- `deduplicator` (default: `true`): Enables [graphql-deduplicator](https://github.com/gajus/graphql-deduplicator). Once enabled sending the header `X-GraphQL-Deduplicate` will deduplicate the data.
- `getEndpoint` (default: `false`): Adds a GraphQL HTTP GET endpoint to your server (defaults to `endpoint` if `true`) (used for leveraging CDN level caching). Setting to `false` means the web server only accepts POST requests.
- `bodyParserOptions`: Lets you pass through options for the JSON `body-parser` used by Express. More info [here](https://github.com/expressjs/body-parser#options).

Note that the `options` argument also exposes a number of fields from `apollo-server`. Here is an overview of the keys of the inherited fields (source: [Apollo Server docs](https://github.com/apollographql/apollo-server#options)):

* `rootValue`: The value passed to the first resolve function.
* `formatError`: A function to apply to every error before sending the response to clients.
* `validationRules`: Additional GraphQL validation rules to be applied to client-specified queries.
* `formatParams`: A function applied for each query in a batch to format parameters before execution.
* `formatResponse`: A function applied to each response after execution.
* `tracing`: When set to `true`, collect and expose trace data in the [Apollo Tracing format](https://github.com/apollographql/apollo-tracing).
* `logFunction`: A function called for logging events such as execution times.
* `fieldResolver`: A custom default field resolver.
* `debug`: A boolean that will print additional debug logging if execution errors occur.
* `cacheControl`: When set to `true`, enable built-in support for Apollo Cache Control.

<!-- | Key | Type | Note |
| ---  | --- | --- |
| `cacheControl`  | Boolean  | Enable extension that returns Cache Control data in the response |
| `formatError`  | Number | A function to apply to every error before sending the response to clients |
| `logFunction`  | LogFunction  | A function called for logging events such as execution times |
| `rootValue` | any  | RootValue passed to GraphQL execution |
| `validationRules` | Array of functions | DAdditional GraphQL validation rules to be applied to client-specified queries |
| `fieldResolver` | GraphQLFieldResolver  | Provides information about upload limits; the object can have any combination of the following three keys: `maxFieldSize`, `maxFileSize`, `maxFiles`; each of these have values of type Number; setting to `false` disables file uploading |
| `formatParams` | Function  | A function applied for each query in a batch to format parameters before execution |
| `formatResponse` | Function | A function applied to each response after execution |
| `debug` | boolean  | Print additional debug logging if execution errors occur | -->

### Example

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