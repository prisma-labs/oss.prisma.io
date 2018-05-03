# Common Workflows

## Bootstrapping GraphQL boilerplates

[GraphQL boilerplates](https://github.com/graphql-boilerplates) provide starter kits for your next GraphQL projects. They are based on industry best practices and the latest tooling from the GraphQL ecosystem. They are also preconfigured with `.graphqlconfig`.

You can bootstrap a GraphQL boilerplate using the `graphql create` command.

**Example 1**: Use the interactive prompt to select one of the available boilerplate projects (e.g. Node, TypeScript, React, Vue, ...)

```sh
graphql create myapp
```

This creates a new directory called `myapp` where your project files will be located.

**Example 2**: Bootstrap the [`typescript-advanced`](https://github.com/graphql-boilerplates/typescript-graphql-server/tree/master/advanced) boilerplate

```sh
graphql create myapp --boilerplate typescript-advanced
```

This creates a new directory called `myapp` where your project files will be located.

**Example 3**: Bootstrap the [`react-fullstack-basic`](https://github.com/graphql-boilerplates/react-fullstack-graphql/tree/master/basic) boilerplate

```sh
graphql create myapp --boilerplate react-fullstack-basic
```

This creates a new directory called `myapp` where your project files will be located.

## Schema handling

### Downloading a GraphQL schema

The `get-schema` command can be used to download the GraphQL schema from a GraphQL API via an [introspection](http://graphql.org/learn/introspection/) query.

**Example 1**: Download the GraphQL schema from the `app` project specified in `.graphqlconfig` and store it in `src/schema.graphql`

Assume the following `.graphqlconfig`:

```yaml
projects:
  app:
    schemaPath: src/schema.graphql
    extensions:
      endpoints:
        default: http://localhost:4000
```

You can use the following command to download the GraphQL schema from `http://localhost:4000` and store it in `src/schema.graphql`:

```sh
graphql get-schema --project app
```

**Example 2**: Download the schema from a Prisma API (deployed to a Prisma Sandbox) that requires authentication via an API token passed in the `Authorization` header and store it in `prisma.graphql`

```
graphql get-schema \
--endpoint https://eu1.prisma.sh/public-lucksight-113/myservice/dev \
--header Authorization=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7InNlcnZpY2UiOiJkYXRhYmFzZUBkZXYiLCJyb2xlcyI6WyJhZG1pbiJdfSwiaWF0IjoxNTI0NzUyNjQyLCJleHAiOjE1MjUzNTc0NDJ9.ricwDHJoHtmYN8ZD4ldjH3ek_YMIUJqervXZ0ApjYpU \
--output prisma.graphql
```

**Example 3**: Download the schema from a Prisma API (deployed to a Prisma Sandbox) that requires authentication via an API token passed in the `Authorization` header and store it in `prisma.json` formatted as JSON

```
graphql get-schema \
--endpoint https://eu1.prisma.sh/public-lucksight-113/myservice/dev \
--header Authorization=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkYXRhIjp7InNlcnZpY2UiOiJkYXRhYmFzZUBkZXYiLCJyb2xlcyI6WyJhZG1pbiJdfSwiaWF0IjoxNTI0NzUyNjQyLCJleHAiOjE1MjUzNTc0NDJ9.ricwDHJoHtmYN8ZD4ldjH3ek_YMIUJqervXZ0ApjYpU \
--output prisma.json \
--json
```

### Showing the diff between two schemas

Sometimes it might be helpful to compare two GraphQL schemas with each other. For example, when working with multiple GraphQL APIs that each represent the same service but deployed to different _development environments_ (e.g. dev, staging or prod). The `graphql diff` command allows to quickly get an overview of the changes between two GraphQL schemas. They are compared via [introspection](http://graphql.org/learn/introspection/) queries.

Assume the following `.graphqlconfig`:

```yaml
projects:
  hello-world:
    extensions:
      endpoints:
        dev: https://myapiserver.com/graphql/dev
        prod: https://myapiserver.com/graphql/prod
```

**Example**: Show the difference between the two GraphQL schemas on the `dev` and `prod` endpoints.

```sh
graphql diff --endpoint dev --target prod --project hello-world
```

## Code generation

You can use the `typegen` and `codegen` commands to generate code (e.g. TypeScript type definitions) for the types of your GraphQL schema or related to GraphQL bindings.

### Generating type definitions for a GraphQL binding




### Generating type definitions for a GraphQL schema


<!-- 

### Managing .graphqlconfig

### Misc
-->