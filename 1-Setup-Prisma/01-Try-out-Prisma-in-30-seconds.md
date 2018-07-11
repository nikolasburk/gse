# Try out Prisma in 30 Seconds

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

After running `prisma init`, the Prisma CLI prompts you to select _where_ you want to deploy your Prisma service:

1. Select the **Demo server**
1. When your browser opens, register with Prisma Cloud (this is needed because that's where the Demo servers are hosted)
1. Go back to your terminal
1. Select a _region_
1. Select the suggested values fro _service_ and _stage_ by hitting **Enter** twice

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

> Copy the `id` of the `createUser` object returned in the Playground and use it for the `__USER_ID__` placeholer value in the following `updateUser`- and `deleteUser`-mutations.

**Query all `User`s**:

```graphql
query {
  users {
    id
    name
  }
}
```

**Update a `User`'s `name`**:

```graphql
mutation {
  updateUser(
    where: { id: "__USER_ID__" },
    data: { name: "Sarah" }
  ) {
    id
    name
  }
}
```

**Delete a `User`**:

```graphql
mutation {
  deleteUser(where: {
    id: "__USER_ID__"
  }) {
    id
    name
  }
}
```

## Next Step

[**Update Prisma GraphQL API**](../2-Update-Prisma-GraphQL-API/README.md)