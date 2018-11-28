---
title: Falcon Server Env
---

This package delivers base classes and helpers for ApiDataSources and Extensions.

- [API reference](falcon-server/falcon-server-env-api.md)

## Installation

With npm:

```bash
npm install @deity/falcon-server-env
```

or with yarn:

```bash
yarn add @deity/falcon-server-env
```

## Usage

To define your own extension (high-level data provider) - you would need to create a new package and export its main class:

```javascript
// package.json - "name": "my-custom-falcon-extension"
const { Extension } = require('@deity/falcon-server-env');

module.exports = class CustomExtension extends Extension {
  getGraphQLConfig() {
    return {
      schema: [`
        extend type Query {
          customQuery: CustomResult
        }

        type CustomResult {
          foo: String
        }
      `],
      dataSources: {
        [this.api.name]: this.api
      },
      resolvers: {
        Query: {
          customQuery: (root, params) => this.api.customQuery(params)
        }
      }
    };
  }
}
```

To define your own API class (low-level data provider) - you would need to create a new package and export its main class:

```javascript
// package.json - "name": "my-custom-falcon-api"
const { ApiDataSource } = require('@deity/falcon-server-env');

module.exports = class CustomApi extends ApiDataSource {
  async customQuery(params) {
    // perform any "params" transformation if needed
    // let's assume it will be set to { "customParams": true }
    const queryParams = params;
    // GET request will be send to https://example.com/custom-api-endpoint?customParams=true
    // this endpoint has to return { "foo": "some string" } type result
    return this.get('custom-api-endpoint', queryParams);
  }
}
```

Then in your project's config (`server` app) - you need to declare them and assign your API to your Extension:

```json
{
  "apis": [{
    "name": "my-custom-api",
    "package": "my-custom-falcon-api",
    "config": {
      "host": "example.com",
      "protocol": "https"
    }
  }],
  "extensions": [{
    "package": "my-custom-falcon-extension",
    "config": {
      "api": "my-custom-api"
    }
  }]
}
```