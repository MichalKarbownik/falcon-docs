---
title: Extension Container
---

The main purpose of `ExtensionContainer` is to store, initialize and manage all provided
[`extensions`](https://github.com/deity-io/falcon/blob/master/packages/falcon-server-env/src/models/Extension.ts)
from the configuration. It also generates main configuration object for ApolloServer instance.

## `new ExtensionContainer(eventEmitter)`

The constructor expects to receive an instance of EventEmitter.

## `extensionContainer.registerExtensions(extensions: Object<string, ExtensionInstanceConfig>, dataSources: Map<string,ApiDataSource>)`

 list of  objects and an initialized list of `dataSources` provided
by [`ApiContainer`](./ApiContainer.md).

This method registers the provided extensions
([`ExtensionInstanceConfig`](#ExtensionInstanceConfig-type)) into
`extensionContainer.extensions` Map.
Constructor will create a new Map, so any further manual calls of
`registerExtensions` method will add new Extensions to it.

`extension.api` will be assigned automatically by `registerExtensions` method based on the
provided configuration and `dataSources` value.

## `async initialize()`

This method will be called by [`FalconServer.start()`](https://github.com/deity-io/falcon/blob/master/packages/falcon-server/src/index.js) method.
It will initialize each registered extension.

## `async createGraphQLConfig(defaultConfig = {})`

This method must return a valid [ApolloServer](https://www.apollographql.com/docs/apollo-server/getting-started.html#Step-3-Create-the-server)
configuration. This method will be called right before starting the ApolloServer instance,
after initializing all the [Extensions](https://github.com/deity-io/falcon/blob/master/packages/falcon-server-env/src/models/Extension.ts)
and [API DataSources](https://github.com/deity-io/falcon/blob/master/packages/falcon-server-env/src/models/ApiDataSource.ts).

### `ExtensionInstanceConfig` type

- `package: string` - Node package path (example: `@deity/falcon-blog-extension`)
- `config: object` - Config object to be passed to Extension Instance constructor
- `config.api: string` - API instance name to be used by the Extension