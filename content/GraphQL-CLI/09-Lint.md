# Linting

## Lint GraphQL Schema

With `graphql lint` you can validate a GraphQL schema against a perdefined set of rules. 

Assume the following `.graphqlconfig`:

```yaml
projects:
  hello-world:
    schemaPath: schema.graphql
    extensions:
      endpoints:
        dev: https://myapiserver.com/graphql/dev
```

Running `graphql lint` will emit validation errors against the schema.