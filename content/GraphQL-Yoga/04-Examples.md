# Examples

## Example projects

There are several [examples](https://github.com/graphcool/graphql-yoga/tree/master/examples) demonstrating how to quickly get started with GraphQL Yoga. For example:

- [hello-world](https://github.com/graphcool/graphql-yoga/tree/master/examples/hello-world): Basic setup for building a schema and allowing for a `hello` query.
- [subscriptions](https://github.com/graphcool/graphql-yoga/tree/master/examples/subscriptions): Basic setup for using subscriptions with a counter that increments every 2 seconds and triggers a subscriptions.
- [fullstack](https://github.com/graphcool/graphql-yoga/tree/master/examples/fullstack): Fullstack example based on [`create-react-app`](https://github.com/facebookincubator/create-react-app) demonstrating how to query data from `graphql-yoga` with [Apollo Client 2.0](https://www.apollographql.com/client/).

## GraphQL boilerplates

You can also check out the [GraphQL boilerplates](https://github.com/graphql-boilerplates) which are all based on the GraphQL Yoga library. GraphQL boilerplates provide flexible starter kits for your next GraphQL project (backend-only and fullstack variants which are based on various languages and frameworks).

The easiest way to get started with a GraphQL boilerplate project is by using the [GraphQL CLI](../GraphQL-CLI/01-Overview.md):

```sh
# Install the GraphQL CLI
npm install -g graphql-cli

# Choose a boilerplate from the interactive prompt ...
graphql create myapp

# ...or directly select a boilerplate project via the `--boilerplate` option (e.g. `typescript-advanced`)
graphql create myapp --boilerplate typescript-advanced
```

> See [here](../GraphQL-CLI/04-Common-Workflows.md#bootstrapping-graphql-boilerplates) for more info.