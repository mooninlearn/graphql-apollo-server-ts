# References

> [Get started with Apollo Server](https://www.apollographql.com/docs/apollo-server/getting-started/)

# Step 1: Create a new project

```bash
mkdir apollo-server-ts
cd apollo-server-ts

npm init --yes && npm pkg set type="module"
```

# Step 2: Install dependencies

```bash
npm install @apollo/server graphql

# typscript
mkdir src
touch src/index.ts

touch tsconfig.json

# typescript가 global로 설치되어 있지 않은 경우
npm install --save-dev typescript @types/node
```

> /tsconfig.json

```json
{
  "compilerOptions": {
    "rootDirs": ["src"],
    "outDir": "dist",
    "lib": ["es2020"],
    "target": "es2020",
    "module": "esnext",
    "moduleResolution": "node",
    "esModuleInterop": true,
    "types": ["node"]
  }
}
```

> /package.json

```json
{
  // ...etc.
  "type": "module",
  "scripts": {
    "compile": "tsc",
    "start": "npm run compile && node ./dist/index.js"
  }
  // other dependencies
}
```

```bash
# > apollo-server-ts@1.0.0 start
> npm run compile && node ./dist/index.js
```

# Step 3: Define your GraphQL schema

> index.ts

```ts
import { ApolloServer } from '@apollo/server';
import { startStandaloneServer } from '@apollo/server/standalone';

// A schema is a collection of type definitions (hence "typeDefs")
// that together define the "shape" of queries that are executed against
// your data.
const typeDefs = `#graphql
  # Comments in GraphQL strings (such as this one) start with the hash (#) symbol.

  # This "Book" type defines the queryable fields for every book in our data source.
  type Book {
    title: String
    author: String
  }

  # The "Query" type is special: it lists all of the available queries that
  # clients can execute, along with the return type for each. In this
  # case, the "books" query returns an array of zero or more Books (defined above).
  type Query {
    books: [Book]
  }
`;

```

# Step 4: Define your data set

> index.ts

```ts
const books = [
  {
    title: 'The Awakening',
    author: 'Kate Chopin',
  },
  {
    title: 'City of Glass',
    author: 'Paul Auster',
  },
];
```

# Step 5: Define a resolver

> index.ts

```ts
// Resolvers define how to fetch the types defined in your schema.
// This resolver retrieves books from the "books" array above.
const resolvers = {
  Query: {
    books: () => books,
  },
};
```

# Step 6: Create an instance of ApolloServer

> index.ts

```ts
// The ApolloServer constructor requires two parameters: your schema
// definition and your set of resolvers.
const server = new ApolloServer({
  typeDefs,
  resolvers,
});

// Passing an ApolloServer instance to the `startStandaloneServer` function:
//  1. creates an Express app
//  2. installs your ApolloServer instance as middleware
//  3. prepares your app to handle incoming requests
const { url } = await startStandaloneServer(server, {
  listen: { port: 4000 },
});

console.log(`🚀  Server ready at: ${url}`);
```

# Step 7: Start the server

```
npm start
```

# Step 8: Execute your first query

```
http://localhost:4000
```

```graphql
query GetBooks {
  books {
    title
    author
  }
}
```

