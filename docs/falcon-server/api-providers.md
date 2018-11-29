---
title: API Providers
---

> This is a list of officially supported API Providers by DEITY.

## Falcon Magento 2 API

If you used `create-falcon-app` to generate your project, `falcon-magento2-api` and `falcon-shop-extension` may already be installed in which case you can **skip the installation step.**

### Overview and installation

This API class realizes communication with Magento2 backend.
It provides resolvers for queries and mutations required by [Falcon Shop Extension](extensions#shop-extension).
To add this API to your Falcon-based app install it in the server directory:

with **yarn**:

```bash
# Install Shop Extension and Magento2 API Provider
yarn add @deity/falcon-shop-extension @deity/falcon-magento2-api
```

or with **npm**:

```bash
# Install Shop Extension and Magento2 API Provider
npm install --save @deity/falcon-shop-extension @deity/falcon-magento2-api
```

and add extension and api to the configuration of the server:

```js
{
  "extensions": {
    // enable shop extension by adding it to "extensions" object
    "shop": {
      "package": "@deity/falcon-shop-extension",
      "config": {
        "api": "api-magento2" // must match an API Provider name set in "apis" object below
      }
    }
  },
  "apis": {
    // provide api that will be used by magneto
    "api-magento2": { // must match "config.api" from shop-extension configuration
      "package": "@deity/falcon-magento2-api",
      "config": {
        // your magento2 api configuration
        "host": "my-magento-domain.com",
        "protocol": "https",
        "username": "api-user",
        "password": "api-user-password"
      }
    }
  }
}
```

### Implementation notes

This api uses Deity Falcon Module for Magento2 (#todo: add link to the module).

### Lifecycle of the GrahpQL request
This is a short overview of the way authentication between Falcon Magento2 API and Magento2 backend works.

Falcon Magento2 API provides implementation for endpoints that require authorization (customer related data) as well as endpoints that don't require customer to be authenticated (product catalog etc).

Once user signs in with his Magento2 credentials his access token is stored in the session so it can be used for interaction with Magneto2.

When GraphQL request comes in [Falcon Server](https://github.com/deity-io/falcon/tree/master/packages/falcon-server) invokes context handlers for all the extensions. [Falcon Shop Extension](https://github.com/deity-io/falcon/tree/master/packages/falcon-shop-extension) calls `createContextData(context)` of the connected API instance (in this case it's Falcon Magento2 API) and passes GraphQL execution context as the param to that method. That way API instance can fill in context with its own data that can be reused during query execution.

Falcon Magento2 API gets then data from the session (which is available as `context.req.session`) and puts it under `magento2` property in context. That way all the required data (like auth token, currency, storeToken) are available in `this.context.magento2` during query processing.

## Falcon Wordpress API

TODO