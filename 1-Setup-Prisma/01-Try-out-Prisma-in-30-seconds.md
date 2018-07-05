# Try out Prisma in 30 seconds

## Step 1: Install the Prisma CLI on your machine

```bash
npm install -g prisma
```

## Step 2: Create your Prisma service

```bash
mkdir hello-world
cd hello-world
prisma init
```

After running, the Prisma CLI prompts you to select _where_ you want to deploy your Prisma service. Select the **Demo server** and just hit **enter** for all subsequent questions.

## Step 3: Deploy your Prisma service

```bash
prisma deploy
```

## Step 4: Open a GraphQL Playground

```bash
prisma playground
```

## Step 5: Explore the service's CRUD GraphQL API

**Create a new `User`**:

```graphql
mutation {
  createUser(data: {
    name: "Alice"
  }) {
    id
  }
}
```

**Query all `User`s**:

```graphql
query {
  users {
    id
    name
  }
}
```

**Filter `User`s by `name`**:

```graphql
query {
  users(where: {
    name_contains: "Bob"
  }) {
    id
    name
  }
}
```