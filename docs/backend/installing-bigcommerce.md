---
title: BigCommerce
---

> BigCommerce is part of the Falcon Platform (Paid version)

In order to connect Falcon with your BigCommerce Store, you will need a BigCommerce API Account with modified permissions <https://support.bigcommerce.com/s/article/Store-API-Accounts>

Within the Falcon application, you can use these API credentials in the configuration files (server/config/default.json, server/config/local.json, etc).

```
"apis": {
   "bigcommerce": {
     "config": {
       "paymentHost": "payments.bigcommerce.com/stores/YOUR_STORE_ID/",
       "host": "api.bigcommerce.com/stores/YOUR_STORE_ID/",
       "accessToken": "YOUR_TOKEN",
       "clientId": "YOUR_ID",
       "clientSecret": "YOUR_SECRET",
       "gqlUrl": "YOUR_URL/graphql",
       "gqlToken": "YOUR_TOKEN"
     }
   }
 }
```

## Modules

The BigCommerce module consists of 2 packages:

- `@deity-io/falcon-bigcommerce-api` provides GraphQL resolvers for Shop extension

- `@deity-io/falcon-bigcommerce-endpoints` sets up a BigCommerce webhook to receive updates on product/category changes to ensure Frontend shows valid data

`falcon-bigcommerce-api` uses both REST API and GraphQL endpoints for communicating with BigCommerce.
Important notes:

The current BigCommerce REST API does not provide any endpoints to resolve frontend URLs, for this reason - the BigCommerce GraphQL API is used just to resolve frontend URLs. Once it gets the URL type, it's able to fetch the (category or product) data from the BigCommerce REST API.

The BigCommerce REST API does not support aggregations when navigating/filtering category products. In order to provide visitors with the best Search experience possible, it is recommended to use the `falcon-algoliasearch` module, which also allows fetching the products directly from the Algolia index (for this reason it's vital to configure Algolia and BigCommerce webhooks properly to ensure the correct data flow across backends because, Falcon-Server is the only middleware that connects them both).

In order to avoid the requirement for your server to be PCI compliant, it is recommended to use `@deity-io/falcon-payments` module which handles this requirement for you and provides payments on the client-side, so you only get a transaction ID to check/verify server side. Otherwise, you'll have to handle sensitive payment information and pass this data to BigCommerce.
