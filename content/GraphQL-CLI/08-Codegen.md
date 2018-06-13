# Code generation

You can use the `codegen` command to generate code (e.g. TypeScript type definitions) for the types of your GraphQL schema or related to GraphQL bindings.

## Get schema for an endpoint

With `graphql get-schema` command, it is possible to get the schema SDL for a live GraphQL endpoint. 

Assume the following `.graphqlconfig`:

```yaml
projects:
  hello-world:
    schemaPath: schema.graphql
    extensions:
      endpoints:
        dev: https://myapiserver.com/graphql/dev
```

You can use the following variations of `graphql get-schema` command to replace the local schema with the live schema at the endpoint via introspection. 

`graphql get-schema --project hello-world`

`graphql get-schema --endpoint http://some-graphql-endpoint`

And there are various other options available that can be found by typing `graphql get-schema help`

## Generate Typescript typings for a project

With `graphql codegen` command, it is possible to generate typescript typings based on schema and TS files. A Typescript typings generator is built into the CLI and can be use by using `generator` as `typegen` is `codegen` extension via the graphql config file. 

Assume the following `.graphqlconfig`:

```yaml
projects:
  hello-world:
    schemaPath: schema.graphql
    extensions:
      endpoints:
        dev: https://myapiserver.com/graphql/dev
      codegen: 
        generator: typegen
        language: typescript
        input: "{binding,prisma}/*.ts"
        output:
          typings: typings.ts
```

Running `graphql codegen` will generate Typescript bindings and save them in the file `typings.ts`. 


## Generating type definitions for a GraphQL binding

Codegen can also create full Typescript binding for a GraphQL binding like Prisma binding based on a schema. 

Assume the following `.graphqlconfig`:

```yaml
projects:
  hello-world:
    schemaPath: schema.graphql
    extensions:
      endpoints:
        dev: https://myapiserver.com/graphql/dev
      codegen: 
        generator: prisma-binding
        language: typescript
        output:
          binding: prisma.ts
```

Running `graphql codegen` with such a graphql config file will generate Prisma binding. 