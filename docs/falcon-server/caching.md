---
title: Caching
---

Falcon-Server exposes `@cache` directive through the Base GraphQL Schema to the rest of the extensions to define
their own caching options. Such cache will be handled by `@cache` directive itself, so the Extension does not have
to do anything by itself in terms of cache checks, cache-key generations, etc.

## Schema Caching options

To use cache within your Extension - you need to put `@cache` directive next to the field you want to cache
in the Extension's GraphQL Schema:

```graphql
type Query {
  myData: Data @cache
  myOtherData: Data @cache(ttl: 20)
}
```

In this example - `myData` Query will be cached by Falcon-Server with a default cache TTL, and `myOtherData` Query
will be cached with a cache TTL of `20` (20 minutes).

> Default cache TTL equals `10` (10 minutes)

## Sub-caching

It is also possible to cache nested data of your Extension (which for example may require some heavy requests
or calculations):

```graphql
type Query {
  data(id: String): Data @cache
}

type Data {
  nestedData: [String] @cache
}
```

In order to ensure the uniqueness of the cache key - `@cache` directive takes into account the name
of the cached field, all input arguments for the current resolver and also checks its "parent" value:

- in case of `data` - it will check `id` input value
- in case of `nestedData` - it will check parent `data` value

## Cache settings

As it was previously mentioned, caching mechanism may be configured to use different TTL values depending on your needs.

There's a default cache TTL value, defined by `@cache` directive itself, which is being used when `@cache` directive is
being used without any extra arguments. If you want to change this value - you could easily do it via your
[Falcon-Server config](falcon-server/basics.md#configuration):

```json
{
  "cache": {
    "resolvers": {
      "enabled": true,
      "default": {
        "ttl": 60
      }
    }
  }
}
```

> You could use special values for your `production` environment via `config/production.json` config file

Every author of the Extension can "mark" the provided Queries as "cacheable" by putting the `@cache` directive next to the corresponding
queries in his Extension's GQL Schema. In some cases - you may disagree with these decisions and you may want to use
higher values for TTL. It is possible to alter those values without touching the Extension's code by changing your Falcon-Server config:

```json
{
  "cache": {
    "resolvers": {
      "default": {
        "ttl": 10
      },
      "query.menu": {
        "ttl": 30
      }
    }
  }
}
```

In this case, `query.menu` represents a "path" of your GraphQL Query like this:

```graphql
query Menu {
  menu {
    id
    name
    urlPath
  }
}
```

`query` - is a name of the GQL operation and `menu` is a name of the Query field. So in this case, instead of using a predefined TTL,
Falcon-Server will cache the response of `query.menu` resolver for 30 minutes (from the config above).

> `@cache` directive works on a Schema level only, meaning that you cannot mark existing Queries as cacheable by yourself
> via config to avoid introducing unintended behavior of the Extension.

### Overriding cache options

Cache options are being checked with this order (from highest to lowest priority):

- "named" GQL path in app config
- `@cache` directive arguments in GQL Schema
- global cache options in app config
- default cache options defined by Base GraphQL Schema

### Cache adapters

By default, Falcon-Server uses
[`InMemoryLRUCache`](https://www.apollographql.com/docs/apollo-server/features/data-sources#using-memcachedredis-as-a-cache-storage-backend)
as a cache backend.

There's an option to configure Redis or Memcached as a cache backend via Falcon-Server config:

<!--DOCUSAURUS_CODE_TABS-->
<!--redis-->

```json
{
  "cache": {
    "type": "redis",
    "options": {
      "host": "redis-server"
    }
  }
}
```

<!--memcached-->

```json
{
  "cache": {
    "type": "memcached",
    "options": ["memcached-server-1", "memcached-server-2"]
  }
}
```

<!--END_DOCUSAURUS_CODE_TABS-->
