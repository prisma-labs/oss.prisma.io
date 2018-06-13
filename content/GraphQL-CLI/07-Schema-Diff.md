# GraphQL Schema Diff

## Showing the diff between two schemas

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