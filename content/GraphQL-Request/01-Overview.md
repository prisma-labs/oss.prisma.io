---
alias: chinohsh2j
description: GraphQL Request is a minimal GraphQL client supporting Node and browsers for scripts or simple apps.
---

# graphql-request

📡 [`graphql-request`](https://github.com/graphcool/graphql-request) is a minimal GraphQL client supporting Node and browsers for scripts or simple apps.

## Features

* Most **simple and lightweight** GraphQL client
* Promise-based API (works with `async` / `await`)
* Typescript support (Flow coming soon)

## Install

```sh
npm install graphql-request
```

## Quickstart

Send a GraphQL query with a single line of code. ▶️ [Try it out](https://runkit.com/593130bdfad7120012472003/593130bdfad7120012472004).

```js
import { request } from "graphql-request";

const query = `{
  Movie(title: "Inception") {
    releaseDate
    actors {
      name
    }
  }
}`;

request("https://api.graph.cool/simple/v1/movies", query).then(data =>
  console.log(data)
);
```

## Usage

```js
import { request, GraphQLClient } from "graphql-request";

// Run GraphQL queries/mutations using a static function
request(endpoint, query, variables).then(data => console.log(data));

// ... or create a GraphQL client instance to send requests
const client = new GraphQLClient(endpoint, { headers: {} });
client.request(query, variables).then(data => console.log(data));
```

## FAQ

### What's the difference between `graphql-request`, Apollo and Relay?

`graphql-request` is the most minimal and simplest to use GraphQL client. It's perfect for small scripts or simple apps.

Compared to GraphQL clients like Apollo or Relay, `graphql-request` doesn't have a built-in cache and has no integrations for frontend frameworks. The goal is to keep the package and API as minimal as possible.

### So what about Lokka?

Lokka is great but it still requires [a lot of setup code](https://github.com/kadirahq/lokka-transport-http) to be able to send a simple GraphQL query. `graphql-request` does less work compared to Lokka but is a lot simpler to use.
