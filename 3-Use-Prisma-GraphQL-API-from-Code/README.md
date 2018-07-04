# Use Prisma GraphQL API from Code

## Step 1: Setup Node.JS project

```bash
mkdir myapp
cd myapp
touch index.js
yarn init -y
yarn add node-fetch prisma-binding
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

Prisma bindings provide a convenient way to interact with a Prisma GraphQL API directly from code. Instead of manually constructing HTTP requests and sending them to Prisma, you can invoke the auto-generated binding functions to send queries, mutations and subscriptions.

### Step 3.1: Download Prisma's GraphQL schema

```bash
npx graphql get-schema __YOUR_PRISMA_ENDPOINT__ -o prisma.graphql
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
prisma.users({}, `{ id name }`)
  .then(users => console.log(users))
  // send `createUser` mutation
  .then(() => prisma.createUser({
    data: { name: `Sarah` }
  }, `{ id name }`))
  .then(newUser => {
    console.log(newUser)
    return newUser
  })
  // send `user` query
  .then(newUser => prisma.user({
    where: { id: newUser.id }
  }, `{ id name }`))
  .then(user => console.log(user))
```

### Step 3.3: Execute the Node script

```bash
node src/index.js
```