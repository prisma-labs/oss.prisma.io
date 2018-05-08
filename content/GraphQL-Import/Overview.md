# GraphQL Import

🗂 **Compose GraphQL Schemas**

`graphql-import` is a package that allows importing &amp; exporting schema definitions in GraphQL SDL (also refered to as GraphQL modules).

## Install

```sh
yarn add graphql-import
```

## Usage

```ts
import { importSchema } from 'graphql-import'
import { makeExecutableSchema } from 'graphql-tools'

const typeDefs = importSchema('schema.graphql')
const resolvers = {}

const schema = makeExecutableSchema({ typeDefs, resolvers })
```

## Example

Assume the following directory structure:

```
.
├── a.graphql
├── b.graphql
└── o.graphql
```

🅰️ `a.graphql`

```graphql
# import B from "b.graphql"

type A {
  # test 1
  first: String
  second: Float
  b: B
}
```

🅱️ `b.graphql`

```graphql
# import O from 'o.graphql'

type B {
  o: O
  hello: String!
}
```

🅾️ `o.graphql`

```graphql
type O {
  id: ID!
}
```

Running `console.log(importSchema('a.graphql'))` produces the following output:

```graphql
type A {
  first: String
  second: Float
  b: B
}

type B {
  o: O
  hello: String!
}

type O {
  id: ID!
}
```
