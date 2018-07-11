# Use Prisma GraphQL API from Code

## Step 1: Setup Node.JS project

```bash
mkdir myapp
cd myapp
touch index.js
yarn init -y
yarn add node-fetch prisma-binding graphql
```

## Step 2: Consume Prisma GraphQL API via plain HTTP using `fetch`

Prisma's GraphQL API can be accessed via HTTP. This code example shows a simple setup using JavaScript's `fetch` to construct an HTTP request and send it to your Prisma service.

## Step 2.1: Update the Node script to construct and send an HTTP request to Prisma

Paste the following code snippet into **index.js** (you need to replace the `__YOUR_PRISMA_ENDPOINT__` placeholder with the `endpoint` specified in your `prisma.yml` that was created when you ran `prisma init` initially.):

```js
const fetch = require('node-fetch')

const endpoint = '__YOUR_PRISMA_ENDPOINT__'

const query = `
query {
  users {
    id
    name
    posts {
      id
      title
    }
  }
}
`

fetch(endpoint, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ query: query })
})
  .then(response => response.json())
  .then(result => console.log(JSON.stringify(result)))
```

## Step 2.2: Execute the Node script

```bash
node src/index.js
```

## Step 3: Consume Prisma GraphQL API via Prisma bindings

Prisma bindings provide a convenient way to interact with a Prisma GraphQL API directly from code. Instead of manually constructing HTTP requests and sending them to Prisma, you can save writing boilerplate code by invoking the auto-generated binding functions to send queries, mutations and subscriptions.

### Step 3.1: Download Prisma's GraphQL schema

Like in **index.js** before, you need to replace `__YOUR_PRISMA_ENDPOINT__` with the `endpoint` from your `prisma.yml` in this terminal command:

```bash
npx graphql get-schema --endpoint __YOUR_PRISMA_ENDPOINT__  --output prisma.graphql --no-all
```

### Step 3.2: Update the Node script to use Prisma bindings

Replace the current contents in **index.js** with the following (again, you also need to replace `__YOUR_PRISMA_ENDPOINT__` with the `endpoint` from your `prisma.yml`.):

```js
const { Prisma } = require('prisma-binding')

const prisma = new Prisma({
  typeDefs: 'prisma.graphql',
  endpoint: '__YOUR_PRISMA_ENDPOINT__'
})

// send `users` query
prisma.query.users({}, `{ id name }`)
  .then(users => console.log(users))
  .then(() =>
    // send `createUser` mutation
    prisma.mutation.createUser(
      {
        data: { name: `Sarah` },
      },
      `{ id name }`,
    ),
  )
  .then(newUser => {
    console.log(newUser)
    return newUser
  })
  .then(newUser =>
    // send `user` query
    prisma.query.user(
      {
        where: { id: newUser.id },
      },
      `{ name }`,
    ),
  )
  .then(user => console.log(user))
```

### Step 3.3: Execute the Node script

```bash
node src/index.js
```

## Next

[**Build GraphQL Servers with Prisma**](../4-Build-GraphQL-Servers-with-Prisma/README.md)