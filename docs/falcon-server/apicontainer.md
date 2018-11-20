---
title: API Container
---

The main purpose of `ApiContainer` is to store, initialize and manage all
provided APIs from the configuration. It also collects REST endpoints,
required by the API to handle requests in old-fashioned way (for example -
processing payment callbacks).

## `new ApiContainer(eventEmitter)`

The constructor expects to receive an instance of EventEmitter.

## `apiContainer.registerApis(apis: Object<string, ApiInstanceConfig>)`

This method registers the provided APIs
([`ApiInstanceConfig`](#ApiInstanceConfig-type)) into `apiContainer.dataSources` Map.
Constructor will create a new Map, so any further manual calls of `registerApis` method will add new API DataSources to it.

All endpoints that were collected from API DataSources will be stored in `apiContainer.endpoints` property.

### `ApiInstanceConfig` type

- `package: string` - Node package path (example: `@deity/falcon-wordpress-api`)
- `config: object` - Config object to be passed to Api Instance constructor