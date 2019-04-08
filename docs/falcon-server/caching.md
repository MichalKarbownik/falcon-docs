---
title: Caching
---

Falcon-Server exposes `@cache` directive through Base GraphQL Schema for the rest of the extensions to define
cache options. Such cache will be applied by the implementation of `@cache` directive itself, so the Extension
does not have to do anything by itself.

## Schema Cache options

To use cache within your Extension - you need to specify `@cache` directive next to your field you want to cache
in Extension's GraphQL Schema:

```graphql
type Query {
  myData: Data @cache
  myOtherData: Data @cache(ttl: 20)
}
```

In this case - `myData` Query will be cached by Falcon-Server with a default cache TTL, and `myOtherData` Query
will be cached with a cache TTL of `20` (20 minutes).

> Default cache TTL equals `10` (10 minutes)

## Query Cache options

If a user of your Extension wants to adjust a predefined cache TTL - `@cache` directive injects input arguments
into Query inputs so you could easily set it like this:

```graphql
query {
  myData(cacheOptions: { ttl: 60 })
}
```

In this case - cache TTL of `60` will be applied to `myData` Query resolver.

## Sub-caching

It is also possible to cache a nested data of your Extension (which for example may require some heavy
requests or calculations):

```graphql
type Query {
  data(id: String): Data @cache
}

type Data {
  nestedData: [String] @cache
}
```

In order to ensure the uniqueness of the cache key - `@cache` directive takes into account the name
of the cached field, all input arguments for the current resolver and also checks its the "parent" value:

- in case of `data` - it will check `id` input value
- in case of `nestedData` - it will parent `data` value
