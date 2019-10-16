---
title: State Management
---

Falcon comes with [Apollo Client](https://www.apollographql.com/docs/react/v2.5/api/apollo-client/) for state management out of the box when you use [`create-falcon-app`](getting-started/installation.md#create-falcon-app) to generate a new application. Apollo Client will be pre-configured and does not require any additional setup.

## Fetching data (Query)

TODO

## Sending data (Mutation)

TODO

## Local state management

Apollo is great when using GraphQL in your front-end, but you can also use Apollo Client to do local state management.

You can read more about local state management with Apollo Client [here](https://www.apollographql.com/docs/react/v2.5/essentials/local-state/). Since Falcon Client does not support Apollo's hooks yet you should use the examples with Render Prop. You can use the dropdown above their code examples to do so.

### Using and updating local client state

When using local client state you need to write resolvers to access and edit the data. You can read more about that in Apollo's documentation about [local resolvers](https://www.apollographql.com/docs/react/v2.5/essentials/local-state/#local-resolvers)

You can use `client/src/clientState.js` to define your client's initial state and resolvers (queries and mutations).

#### Querying local state

[TODO]

Something about Falcon's `Query` component.

Example in `client/src/pages/shop/components/Sidebar/SidebarQuery.js` and `client/src/clientState.js`
