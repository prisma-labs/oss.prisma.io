# Schema handling

## Downloading a GraphQL schema

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