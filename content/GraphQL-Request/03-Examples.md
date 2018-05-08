# Examples

## Authentication via HTTP header

```js
import { GraphQLClient } from 'graphql-request'

const client = new GraphQLClient('my-endpoint', {
  headers: {
    Authorization: 'Bearer my-jwt-token',
  },
})

const query = `{
  Movie(title: "Inception") {
    releaseDate
    actors {
      name
    }
  }
}`

client.request(query).then(data => console.log(data))
```

## Passing more options to fetch

```js
import { GraphQLClient } from 'graphql-request'

const client = new GraphQLClient('my-endpoint', {
 credentials: 'include',
 mode: 'cors'
})

const query = `{
  Movie(title: "Inception") {
    releaseDate
    actors {
      name
    }
  }
}`

client.request(query).then(data => console.log(data))
```

## Using variables

With `request`:

```js
import { request } from 'graphql-request'

const query = `query getMovie($title: String!) {
  Movie(title: $title) {
    releaseDate
    actors {
      name
    }
  }
}`

const variables = {
  title: 'Inception',
}

request('my-endpoint', query, variables).then(data => console.log(data))
```

With `GraphQLClient`:

```js
import { GraphQLClient } from 'graphql-request';

const query = `query getMovie($title: String!) {
  Movie(title: $title) {
    releaseDate
    actors {
      name
    }
  }
}`

const variables = {
  title: 'Inception',
}

const client = new GraphQLClient('https://api.graph.cool/simple/v1/movies')

client.request(query, variables).then(data => console.log(data))
```

## Error handling

```js
import { request } from 'graphql-request'

const wrongQuery = `{
  some random stuff
}`

request('my-endpoint', query)
  .then(data => console.log(data))
  .catch(err => {
    console.log(err.response.errors) // GraphQL response errors
    console.log(err.response.data) // Response data if available
  })
```

## Using `require` instead of `import`

```js
const { request } = require('graphql-request')

const query = `{
  Movie(title: "Inception") {
    releaseDate
    actors {
      name
    }
  }
}`

request('my-endpoint', query).then(data => console.log(data))
```

## Cookie support for `node`

```sh
npm install fetch-cookie/node-fetch
```

```js
import { GraphQLClient } from 'graphql-request'

// use this instead for cookie support
global['fetch'] = require('fetch-cookie/node-fetch')(require('node-fetch'))

const client = new GraphQLClient('my-endpoint')

const query = `{
  Movie(title: "Inception") {
    releaseDate
    actors {
      name
    }
  }
}`

client.request(query).then(data => console.log(data))
```

## Using `graphql-tag`

You can use `graphql-tag` to add syntax highlighting to your GraphQL queries. Since it transforms [GraphQL SDL](https://blog.graph.cool/graphql-sdl-schema-definition-language-6755bcb9ce51) into an [AST](https://en.wikipedia.org/wiki/Abstract_syntax_tree), you need to change it back into GraphQL SDL before passing it to `request`. You can do that by using `print`.

```js
import { GraphQLClient } from 'graphql-request';
import gql from 'graphql-tag';
import { print } from 'graphql';

const query = gql`{
  Movie(title: "Inception") {
    releaseDate
    actors {
      name
    }
  }
}`

const client = new GraphQLClient('https://api.graph.cool/simple/v1/movies')

client.request(print(query)).then(data => console.log(data))
```

## More examples coming soon...

* Fragments
* Typed Typescript return values