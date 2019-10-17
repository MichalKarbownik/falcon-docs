---
title: State Management
---

Falcon comes with [Apollo Client](https://www.apollographql.com/docs/react/v2.5/api/apollo-client/) for client-side state management when you use [`create-falcon-app`](getting-started/installation.md#create-falcon-app) to generate a new application. Apollo Client will be pre-configured and does not require any additional setup. You can start using it right away.

## The short version

If you already know how to use Apollo Client, you pretty much already know how to use it with Falcon. If you need a refresher, continue to the [long version](falcon-client/state-management.md#the-long-version) or the [Apollo documentation](https://www.apollographql.com/docs/react/v2.5/essentials/get-started/).

### Fetching and sending data from and to the server

TODO

### Local state management

Your initialized local state and the resolvers go in `client/src/clientState.js`.

### Falcon's additional components

TODO

## The long version

This will go in-depth about how to use Apollo for managing the client state of your Falcon application with Apollo.

### Fetching data (Query)

TODO

### Sending data (Mutation)

TODO

### Local state management

Apollo is great when using GraphQL to fetch data in your front-end, but you can also use Apollo Client to do local state management, just like you would use the [React Context API](https://reactjs.org/docs/context.html), MobX or Redux. You can use those other options as well, but using Apollo Client for local state management is ideal because your Apollo cache will be the single source of truth in your application's front-end.

You can read more about local state management with Apollo Client [here](https://www.apollographql.com/docs/react/v2.5/essentials/local-state/). Since Falcon Client does not support Apollo's hooks yet you should use the examples with Render Prop. You can use the dropdown above their code examples to change the examples from hooks to render props.

#### Using and updating local client state

When using local client state you need to write resolvers (queries and mutations) to access and edit the data. You can read more about that in Apollo's documentation about [local resolvers](https://www.apollographql.com/docs/react/v2.5/essentials/local-state/#local-resolvers).

You can use `client/src/clientState.js` to define your client's initial state and resolvers.

##### Mutations

You can use `cache.writeData` for one-off mutations that don't depend on the date that's currently in the cache. This is an alternative for writing a `Mutation` component. More on that at [writing resolvers for local client state](falcon-client/state-management.md#writing-resolvers-for-local-client-state).

> It's important to note that direct writes are not implemented as GraphQL mutations under the hood, so you shouldn't include them in your schema. They also do not validate that the data you're writing to the cache is in the shape of valid GraphQL data. If either of these features are important to you, you should opt to use a local resolver instead. - [Apollo Docs](https://www.apollographql.com/docs/react/v2.5/essentials/local-state/#direct-writes)

```jsx
import React from "react";
import { ApolloConsumer } from "react-apollo";
import { Button } from "@deity/falcon-ui";

const FilterButton = ({ filter, children }) => (
  <ApolloConsumer>
    {client => (
      <Button
        // use `cache.WriteData` to write to the client-side cache immediately
        onClick={() => client.writeData({ data: { visibilityFilter: filter } })}
      >
        {children}
      </Button>
    )}
  </ApolloConsumer>
);
```

You can then subscribe to this data with a `Query` component. The `@client` directive in the GraphQL query let's Apollo Client know to fetch this data locally as opposed to fetching it from a server. All cache writes and reads are synchronous, so you don't have to worry about loading state.

```jsx
import React from "react";
import { Query } from "react-apollo";
import gql from "graphql-tag";
import { Button } from "@deity/falcon-ui";

const GET_VISIBILITY_FILTER = gql`
  {
    visibilityFilter @client
  }
`;

const FilterButton = ({ filter, children }) => (
  <Query query={GET_VISIBILITY_FILTER}>
    {({ data, client }) => (
      <Button
        onClick={() => client.writeData({ data: { visibilityFilter: filter } })}
        active={data.visibilityFilter === filter}
      >
        {children}
      </Button>
    )}
  </Query>
);
```

The other way is by creating a `Mutation` component with a GraphQL mutation that calls a local client-side resolver. Writing a `Mutation` is recommended by Apollo when your mutation depends on existing values in the cache. To do this you need to specify a function in your local resolver map, located in `client/src/clientState.js`.

###### Writing resolvers for local client state

First, you should write the GraphQL query. In this example we use a sidebar component to showcase how you would write your mutation resolver to toggle its `isOpen` state.

```js
/// components/Sidebar/SidebarQuery.js
import gql from "graphql-tag";
import { Query } from "@deity/falcon-ecommerce-uikit";

export const GET_SIDEBAR_STATE = gql`
  query Sidebar {
    sidebar @client {
      isOpen
    }
  }
`;

export class SidebarQuery extends Query {
  static defaultProps = {
    query: GET_SIDEBAR_STATE
  };
}
```

Then we write our resolver functions so we can change the `isOpen` state.

```jsx
// client/src/clientState.js
import { GET_SIDEBAR_STATE } from "./pages/shop/components/Sidebar";
export default {
  // Your initial cache state
  data: {
    sidebar: {
      isOpen: false,
      __typename: "SidebarStatus" // any name you like
    }
  },
  // Resolvers to get or edit the state
  resolvers: {
    Query: {
      // Query resolvers go here
    },
    Mutation: {
      toggleSidebar: (_, _variables, { cache }) => {
        const queryResponse = cache.readQuery({ query: GET_SIDEBAR_STATE });
        const sidebar = { ...queryResponse.sidebar };
        sidebar.isOpen = !sidebar.isOpen;

        cache.writeQuery({
          query: GET_SIDEBAR_STATE,
          data: { sidebar }
        });

        return null;
      }
    }
  }
};
```

Note: You need to return `null` here because TODO

At this point you are ready to write your `Mutation` component which will expose a function to your React components you can use to toggle the Sidebar's `isOpen` state.

```jsx
// SidebarMutation.js
import gql from "graphql-tag";
import { Mutation } from "react-apollo";

const TOGGLE_SIDEBAR_MUTATION = gql`
  mutation ToggleSidebarMutation {
    toggleSidebar @client
  }
`;

export class ToggleSidebarMutation extends Mutation {
  static defaultProps = {
    mutation: TOGGLE_SIDEBAR_MUTATION
  };
}
```

Then you can use the components like this to manage the state of your sidebar component:

```jsx
import React from "react";
import PropTypes from "prop-types";
import { ToggleSidebarMutation } from "./SidebarMutation";
import { SidebarQuery } from "./SidebarQuery";

export const SidebarContainer = ({ children }) => (
  <SidebarQuery>
    {({ sidebar }) => (
      <ToggleSidebarMutation>
        {toggleSidebar =>
          children({
            isOpen: sidebar.isOpen,
            toggle: toggleSidebar
          })
        }
      </ToggleSidebarMutation>
    )}
  </SidebarQuery>
);

SidebarContainer.propTypes = {
  children: PropTypes.func.isRequired
};
```

Then, somewhere in your application:

```jsx
<SidebarContainer>
  {sidebarProps => {
    const { isOpen, toggle } = sidebarProps;
    return <Sidebar isOpen={isOpen} toggle={toggle} />;
  }}
</SidebarContainer>
```

In the end `<Sidebar />` only cares about the props it receives and all the other logic is abstracted away. This is a lot of work for just toggling the `isOpen` state for a sidebar, but as you can imagine this is great for abstracting away more complicated interactions with your application's data.

##### Querying local state

Querying the local state works just like fetching data from a GraphQL server. The only difference is the `@client` directive you already saw in the section about mutating client state above. `@client` tells a `Query` component that the data should be pulled from the Apollo Client cache or resolved using pre-defined local resolvers.

```jsx
export const GET_SIDEBAR_STATE = gql`
  query Sidebar {
    sidebar @client {
      isOpen
    }
  }
`;
```

##### Resetting your store

From the [Apollo documentation](https://www.apollographql.com/docs/react/v2.5/essentials/local-state/#initializing-the-cache):

> Sometimes you may need to [reset the store](https://www.apollographql.com/docs/react/v2.5/api/apollo-client/#ApolloClient.resetStore) in your application, when a user logs out for example. If you call `client.resetStore` anywhere in your application, you will likely want to initialize your cache again. You can do this using the `client.onResetStore` method to register a callback that will call `cache.writeData` again.

##### Local data query flow

Here's [an example from the Apollo documentation](https://www.apollographql.com/docs/react/v2.5/essentials/local-state/#initializing-the-cache) about a query that fetches both local client state with `@client` and some data from a GraphQL server.

##### Intregrating `@client` into remote queries

Adding new GraphQL object types to your resolver map is a way to combine local and remote queries. By adding a new GraphQL object type, we mean that you add a new key like this in your `client/src/clientState.js`:

[TODO] add part about this adding `isLoggedIn` to Member types

```jsx
export default {
  data: {
    /* ... */
  },
  resolvers: {
    Query: {
      /* ... */
    },
    Mutation: {
      /* ... */
    },
    Member: {
      isLoggedIn() {
        return someInternalLoginVerificationFunction();
      }
    }
  }
};
```

For more information, continue reading on the Apollo Documentation about [intregrating `@client` into remote queries](https://www.apollographql.com/docs/react/v2.5/essentials/local-state/#integrating-client-into-remote-queries).

There are some things to consider when integrating `@client` into remote queries.

- [Fetch policies](https://www.apollographql.com/docs/react/v2.5/essentials/local-state/#working-with-fetch-policies).

- [Forcing resolvers to run `@client` parts on every request](https://www.apollographql.com/docs/react/v2.5/essentials/local-state/#forcing-resolvers-with-clientalways-true). Use this carefully, as it can impact performance.

- [Using `@client` fields as variables (in both remote and other `@client` queries)](https://www.apollographql.com/docs/react/v2.5/essentials/local-state/#using-client-fields-as-variables).

#### Other resources

Here are some other resources by Apollo about local state management.

- [Apollo cache API's most relevant methods and common use cases](https://www.apollographql.com/docs/react/v2.5/essentials/local-state/#managing-the-cache)

- [Client-side Schema](https://www.apollographql.com/docs/react/v2.5/essentials/local-state/#client-side-schema)

- [Code splitting](https://www.apollographql.com/docs/react/v2.5/essentials/local-state/#code-splitting)

- [Apollo Client local state API](https://www.apollographql.com/docs/react/v2.5/essentials/local-state/#code-splitting)
