# CLI

GraphQL binding has a dedicated CLI to support the worklows all around creating and using GraphQL.

## Install

Install with `npm`:

```sh
npm install -g graphql-binding
```

Install with `yarn`:

```sh
yarn global add graphql-binding
```

## Usage

```
Usage: graphql-binding -i [input] -g [generator] -b [outputBinding]

Options:
  --help                Show help                                      [boolean]
  --version             Show version number                            [boolean]
  --input, -i           Path to schema.js or schema.ts file            [string] [required]
  --language, -l        Language of the generator. Available generators:
                        typescript, javascript                         [string] [required]
  --outputBinding, -b   Output binding. Example: binding.ts            [string] [required]
  --outputTypedefs, -t  Output type defs. Example: typeDefs.graphql    [string]
```

## Usage with GraphQL Config

The `graphql-binding` CLI integrates with GraphQL Config. This means instead of passing arguments to the command, you can write a `.graphqlconfig.yml` file which will be read by the CLI.

For example, consider the following `.graphqlconfig.yml`:

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

Invoking simply `graphql prepare` in a directory where the above `.graphqlconfig` is available is equivalent to invoking the following terminal command:

```sh
graphql-binding \
  --language typescript \
  --input schema.graphql \ // TODO: does this work or should this be an executable schema?
  --outputBinding mybinding.ts
```
