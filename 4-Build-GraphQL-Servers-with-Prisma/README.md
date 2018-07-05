# Build GrapgQL Servers with Prisma

## Step 1: Update Node.JS project

```bash
yarn add graphql-yoga
touch schema.graphql
```

## Step 2: Configure application schema (in schema.graphql)

The _application schema_ defines the API operations of your GraphQL server. Add the following code to **schema.graphql**:

```graphql
# import User, Post from "prisma.graphql"

type Query {
  publishedPosts: [Post!]!
  post(postId: ID!): Post
  postsByUser(userId: ID!): [Post!]!
}

type Mutation {
  createUser(name: String!): User
  createDraft(title: String!, content: String!, userId: ID!): Post
  publish(postId: ID!): Post
}
```

## Step 3: Implement resolvers & setup GraphQL server

Replace the current contents in **index.js** with the following (again, you also need to replace `__YOUR_PRISMA_ENDPOINT__` with the `endpoint` from your `prisma.yml`.):

```js
const { GraphQLServer } = require('graphql-yoga')
const { Prisma } = require('prisma-binding')

const resolvers = {
  Query: {
    publishedPosts(parent, args, ctx, info) {
      return ctx.db.query.posts({ where: { isPublished: true } }, info)
    },
    post(parent, args, ctx, info) {
      return ctx.db.query.post({ where: { id: args.postId } }, info)
    },
    postsByUser(parent, args, ctx, info) {
      return ctx.db.query.posts(
        {
          where: {
            author: { id: args.userId }
          }
        },
        info
      )
    }
  },
  Mutation: {
    createDraft(parent, args, ctx, info) {
      return ctx.db.mutation.createPost(
        {
          data: {
            title: args.title,
            text: args.text,
            author: {
              connect: { id: args.userId }
            }
          },
        },
        info
      )
    },
    publish(parent, args, ctx, info) {
      return ctx.db.mutation.updatePost(
        {
          where: { id: args.postId },
          data: { isPublished: true },
        },
        info
      )
    },
    createUser(parent, args, ctx, info) {
      return ctx.db.mutation.createUser(
        {
          data: { name: args.name }
        },
        info
      )
    }
  },
}

const server = new GraphQLServer({
  typeDefs: './src/schema.graphql',
  resolvers,
  context: req => ({
    ...req,
    db: new Prisma({
      typeDefs: ‘prisma.graphql’,
      endpoint: '__YOUR__PRISMA_ENDPOINT__',
    }),
  }),
})

server.start(() => console.log('Server is running on http://localhost:4000'))
```

## Step 4: Start the server

```bash
node src/index.js
```

## Step 5: Explore the API of the application server

**Create a new `User`**:

```graphql
mutation {
  createUser(name: "Jenny") {
    id
  }
}
```

> Copy the `id` of the `createUser` object returned in the Playground and use it for the `__USER_ID__` placeholer value in the next mutation.

**Create a new `Post` as a _draft_**:

```graphql
mutation {
  createDraft(data: {
    title: "GraphQL is great"
    author: {
      connect: {
        name: "__USER_ID__"
      }
    }
  }) {
    id
    published
    author {
      id
    }
  }
}
```

> Copy the `id` of the `createDraft` object returned in the Playground and use it for the `__POST_ID__` placeholer value in the next mutation.

**Publish a `Post`**:

```graphql
mutation {
  publish(postId: "__POST_ID__") {
    id
    title
    published
  }
}
```

**Retrieve all published `Post`s and their `author`s**:

```graphql
query {
  publishedPosts {
    id
    title
    author {
      id
      name
    }
  }
}
```