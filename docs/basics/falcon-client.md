---
title: Falcon Client
---

This is a `@deity/falcon-client` host for your shop.

Falcon Client is the entrypoint for frontend features of Falcon stack. It acts as host for your shop - provides app building capabilities and features set which can be used to create your desired shop application.

## Installation

With npm:

```bash
npm install @deity/falcon-client
```

or with yarn:

```bash
yarn add @deity/falcon-client
```

## Quick Start

Use the project generator:

[Create-falcon-app](https://github.com/deity-io/falcon/tree/master/packages/create-falcon-app)

Out of the box, it connects to a public back-end service (Magento & WordPress), so you can start developing right away.

## Exposed Commands

Falcon Client exposes set of handy commands:

### `falcon-client start`

Runs the project in development mode. You can view your application at `http://localhost:3000`.

The page will reload if you make edits (both backend and frontend HMR is enabled).

### `falcon-client start -- --inspect=[host:port]`

To debug the node server, you can use `falcon-client start --inspect`. This will start the node server and enable the inspector agent. The `=[host:port]` is optional and defaults to `=127.0.0.1:9229`. For more information, see [this](https://nodejs.org/en/docs/guides/debugging-getting-started/).

### `falcon-client start -- --inspect-brk=[host:port]`

This is the same as --inspect, but will also break before user code starts. (to give a debugger time to attach before early code runs) For more information, see [this](https://nodejs.org/en/docs/guides/debugging-getting-started/).

### `rs`

If your application is running, and you need to manually restart your server, you do not need to completely kill and rebundle your application. Instead you can just type `rs` and press enter in terminal.

### `falcon-client test`

Runs the test watcher (Jest) in an interactive mode.
By default, runs tests related to files changed since the last commit.

If you want to know more about how to test, see [this](#testing)

### `falcon-client build`

Builds the app for production, and outputs to the `./build` folder.

The build is minified and the filenames include the hashes. Your app is ready to be deployed!

### `falcon-client size`

Runs an interactive zoomable treemap of output files to visualize their size. Report will be automatically open in default browser at `http://localhost:8888`.

## API contract

Application needs to have `index.js` file, and the following optional configuration `bootstrap.js` and `falcon-client.build.config.js` files. Each of them should be placed into the application root directory.

### `index.js` (required)

This is an application entry point which needs to export valid React element `React.ReactElement<any>` as default:

```js
import App from './src/App';

export default App;
```

In order to use your custom Apollo Schema you need to export it via `clientApolloSchema`:

```js
import clientApolloSchema from './src/clientState';

export { clientApolloSchema };
```

For more information se [this](#state-management)

### `bootstrap.js`

This is an optional runtime configuration file.

```js
const config = require('config');

export default {
  config: { ...config },
  onServerCreated: server => {},
  onServerInitialized: server => {},
  onServerStarted: server => {}
};
```

#### config

This is configuration object used to setup `@deity/falcon-client`

`config: object`

- `logLevel: string` - (default: `'error'`) [@deity/falcon-logger](https://github.com/deity-io/falcon/tree/master/packages/falcon-logger) logger level
- `serverSideRendering: boolean` - (default `true`) switch to control whether the [SSR](#server-side-rendering) is enabled
- `googleTagManager: object` - Google Tag Manager configuration, [see the details](#google-tag-manager)
- `googleAnalytics`:
  - `trackerID` - Google Analytics tracking code
- `i18n: object` - internationalization configuration, [see the details](#internationalization)
- `menus: object` - menus configuration [TODO]
- `apolloClient` - ApolloClient configuration object
  - `apolloClient.httpLink` - configuration object that will be passed to the
    `apollo-link-http` method which defines HTTP link the remote Falcon Server
    ([available options](https://www.apollographql.com/docs/link/links/http.html#options))
  - `apolloClient.config` - configuration object that will be passed to the
    main `ApolloClient` constructor
    ([available options](https://www.apollographql.com/docs/react/api/apollo-client.html#apollo-client))

All configuration passed by `config` is accessible via `ApolloClient`, which mean you can access any of its property via graphQL query.

In order to retrieve `logLevel` you can run following query:

```graphql
gql`
  query LogLevel {
    config @client {
        logLevel
      }
    }
  }
`
```

It is also possible to extend `config` object about your custom properties.

#### Runtime hooks

Falcon Client exposes set of hooks to which you can attache custom logic:

- `onServerCreated(server: Koa)` - handler invoked immediately after koa server creation
- `onServerInitialized(server: Koa)` - handler invoked immediately after koa server setup (when middlewares like handling errors, serving static files and routes were set up)
- `onServerStarted(server: Koa)` - handler invoked when koa server started with no errors

### `falcon-client.build.config.js`

This is an optional build-time configuration file which is used to set up the entire build process. Bellow, there is an example of `falcon-client.build.config.js` file content with defaults:

```js
module.exports = {
  clearConsole: true,
  useWebmanifest: false,
  i18n: {},
  envToBuildIn: [],
  plugins: []
};
```

- `clearConsole: boolean` - (default: `true`) determines whether console should be cleared when starting script
- `useWebmanifest: boolean` - (default: `false`) determines whether [Web App Manifest](#webmanifest) should be processed via webpack and included in output bundle
- `i18n: object` - (default: `{}`) internationalization configuration, [see the details](#internationalization)
- `envToBuildIn` - (default: `[]`) an array of environment variable names which should be build in into bundle, [see the details](#environment-variables)
- `plugins` - (default: `[]`) an array of plugins which can modify underlying [webpack configuration](#webpack).

Falcon Client provides you much more build configuration options. You can find all of them described in [Build process configuration](#build-process-configuration) section.

## Build process configuration

Falcon Client comes with all necessary features and development tools turned on by default:

- Universal [HMR](https://webpack.js.org/concepts/hot-module-replacement/) - page auto-reload if you make edits (on both backend and frontend)
- Latest JavaScript achieved via babel 7 compiler, [see the details](#babel).
- ESLint with [Prettier](https://github.com/prettier/prettier) - to keep your code base clean and consistent, [see the details](#eslint)
- Jest test runner setup with sensible defaults [see the details](#jest).

However, you can still modify Falcon Client defaults

### Environment Variables

<!-- ### Build-time Variables -->

Falcon client uses set of environment variables. All of them can be accessed via `process.env` anywhere in your application.

- `process.env.NODE_ENV` - `development` or `production`
- `process.env.BABEL_ENV`- `development` or `production`
- `process.env.PORT`- (default: `3000`), builded in only when `development`
- `process.env.HOST`- default is `0.0.0.0`
- `process.env.BUILD_TARGET` - `client` or `server`
- `process.env.ASSETS_MANIFEST` - path to webpack assets manifest,
- `process.env.PUBLIC_DIR`: directory with your static assets

You can create your own custom build-time environment variables. They must be listed on `envToBuildIn` in `falcon-client.build.config.js` file. Any other variables except the ones listed above will be ignored to avoid accidentally exposing a private key on the machine that could have the same name. Changing any environment variables will require you to restart the development server if it is running.

For example, bellow configuration expose environment variable named `SECRET_CODE` as `process.env.SECRET_CODE`:

```js
module.exports = {
  envToBuildIn: ['SECRET_CODE']
};
```

### Webpack

Falcon Client gives you the possibility to extend the underlying webpack configuration. You can do it via exposed plugins API (not webpack plugins). It is worth to mention that Falcon Client plugins API is [razzle](https://github.com/jaredpalmer/razzle#plugins) compatible.

To use Falcon Client (or Razzle) plugin, you need to install it in your project, and add it to `plugins` array in `falcon-client.build.config.js` file:

```js
module.exports = {
  plugins: ['plugin-name']
};
```

Plugins are simply functions that modify and return Falcon Client's webpack config. Each plugin function will receive the following arguments:

- `config: object` - webpack configuration object,
- `env.target: 'web' | 'node'` - webpack build target,
- `env.dev: boolean` - `true` when `process.env.NODE_ENV` is `development`, otherwise `false`,
- `webpack: Webpack` - webpack reference,

```js
module.exports = function myFalconClientPlugin(config, env, webpack) {
  const { target, dev } = env;

  if (target === 'web') {
    // client only
  }
  if (target === 'server') {
    // server only
  }
  if (dev) {
    // development only
  } else {
    // production only
  }

  // your webpack config modifications

  return webpackConfig;
};
```

`falcon-client.build.config.js` file accepts also `modify` setting which is an escape hatch function, which can be used for quick webpack configuration modifications. Basically, it works same as plugins, but can be specified inline. Below you can find an example of extending webpack configuration about `MyWebpackPlugin` only in `development`:

```js
module.exports = {
  modify: (config, { target, dev }, webpack) => {
    if (dev) {
      config.plugins = [...config.plugins, new MyWebpackPlugin()];
    }

    // your webpack config modifications
    return config;
  }
};
```

### Babel

Falcon Client gives you Ecma Script 6 compiled via Babel 7. However, if you want to add your own babel transformations, you can override defaults by adding the `.babelrc` file into the root of your project. Please note that it is necessary to at the very minimum the default `@deity/babel-preset-falcon-client` preset:

```js
{
  "presets": [
    "@deity/babel-preset-falcon-client", // needed
  ],
  "plugins": [
    // additional plugins
  ]
}
```

### ESLint

Falcon Client comes with ESLint with [Prettier](https://github.com/prettier/prettier) rules - to keep your code base clean and consistent, [see presets](https://github.com/deity-io/falcon/tree/master/packages/falcon-dev-tools/eslint-config-falcon).
You can override (or extend) defaults by adding the `.eslintrc` file into the root of your project:

```JSON
{
  "extends": ["@deity/eslint-config-falcon"],
  "rules": {
    "foo/bar": "error",
  }
}
```

### Jest

Falcon Client comes with configured [Jest](https://jestjs.io/) test runner. However it is possible to override it by adding `jest` node into `package.json`. Below example configures `setupTestFrameworkScriptFile` file:

```JSON
// package.json
{
 "jest": {
   "setupTestFrameworkScriptFile": "./setupTests.js"
 }
}
```

## State Management

[TODO]

### Apollo Link State

### client state

## Routing

In-app routing is based on [react-router](https://github.com/ReactTraining/react-router) in version 4. If you are not familiar with it, see [this](https://reacttraining.com/react-router/web/example/basic)

Falcon Client support async routes, more information can be found in [Code Splitting](#code-splitting) section.

### Falcon Dynamic Routing

[TODO]

## PWA

Falcon client offers wide range of Progressive Web App features out of the box

### Webmanifest

The web app manifest provides information about an application (such as its name, author, icon, and description) in a JSON text file. The manifest informs details for websites installed on the homescreen of a device, providing users with quicker access and a richer experience.

For more information, see [this](https://developer.mozilla.org/en-US/docs/Web/Manifest).

Web App Manifest file should be located in `./src/manifest.webmanifest` and could be edited according to your needs. Pleas bear in mind that paths to icons files should be relative:

```json
{
  "icons": [{ "src": "./assets/logo.svg", "sizes": "48x48", "type": "image/svg" }]
}
```

During build process webpack will take care about resolving file paths and generating hashes in order to improve file caching.

### Web Cache

All files emitted by webpack build into `./build/public/static/` directory contain hash part in file name, which is generated from its content. It allow us to set browser cache via setting `Cache-Control: max-age=31536000` header, which is 1 year.

### Service Worker

Production build generate Service Worker (file `./build/static/sw.js`) which is automatically installed in web browser. It cache all files from `./build/public/` and turns on offline capabilities.

For more information see [this](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorker)

## Code Splitting

This feature splits your code into various bundles which are loaded on demand. It is used to achieve smaller bundles and control resource load prioritization which, have a major impact on load time. To specify splitting point you need to import modules in asynchronous manner. To make it working with React, Falcon Client use [async component](#async-components).

### Async components

Async components is based on [react-async-component](https://github.com/ctrlplusb/react-async-component)

It is highly recommended to have each page component asynchronous. Then page chunks does not impact them self, as they contain only necessary amount of code.

To convert React Component into component which can be latter fetch on demand, you need to wrap it with `asyncComponent` function:

```jsx
import { asyncComponent } from 'react-async-component';

const AsyncHome: asyncComponent({
  resolve: () => import('./src/pages/Home')
})

export default () => (
  <Switch>
    <Route exact path="/" component={AsyncHome} />
    {
      /// ...
    }
  </Switch>
)
```

For more information see [this](https://github.com/ctrlplusb/react-async-component)

### Vendors bundle

`vendors.js` is chunk which combine only common project dependencies into single file. As they should not change often, even between subsequent releases, it allows to turn on long term caching for rather big file.

## Internationalization

Internationalization is based on [i18next](https://www.i18next.com/).

All i18n resources should be placed in `./i18n` directory, and folder structure should follow pattern `{{lng}}/{{ns}}.json`. Which means each language needs to have own directory with `json` file per namespace:

```
i18n
  |-en
    |-common.json
    |-blog.json
    |-...
  |-...
```

Default namespace is `common` and there is also fallback configured to it, in case if translation key can not be found in namespaces defined in `translate()` HoC from `react-i18next` module.

HMR for translation files is supported.

### Configuration

Configuration options base on [i18next](https://www.i18next.com/overview/configuration-options) and you can change them via configuration `config.i18n` exported from `falcon-client.config.js`

- `lng: string` - (default: `en`) default application language
- `fallbackLng: string` - (default: `en`) language to use if translations in selected language are not available
- `whitelist: string[]` - (default: `['en']`) available languages, it may be be narrowed, if installed extensions does not support all of specified
- `ns: string[]` - (default: `['common']`) namespaces used by application
- `debug: boolean` - (default: `false`) i18next debug mode switch
- `resources: object` - (default: `undefined`) allows to inject translations inline, useful during testing

### Using default resources

Default resources (if configured) will be merged with your custom translations from `./i18n` directory.

To use default resources you need to install `@deity/falcon-i18n` npm module (or any other compatible) which contains default translations

```bash
npm install --save @deity/falcon-i18n
```

Then in `falcon-client.build.config.js` file you need to update `i18n` configuration. Add `@deity/falcon-i18n` package name into `resourcePackages` array.

Imported package (like `@deity/falcon-i18n`) can contain more languages and/or namespaces than you are interested in. So if you don't want to use all of them (to save bundle size, or just not to use some namespace as it's not relevant to your project) you can filter them out by using `filter` option - that will instruct webpack to skip items not listed in the `filter` property.

```javascript
module.exports = {
  i18n: {
    resourcePackages: ['@deity/falcon-i18n'],
    filter: {
      lng: ['en'],
      ns: ['common', 'blog']
    }
  }
};
```

Above example configuration will deliver to your project default `common` and `blog` namespaces from English language.

## SEO

To make your shop SEO-friendly, following mechanisms are involved out of the box

### Server Side Rendering

SSR take place when a website is first opened. All operations are carried out on the server and the browser gets an HTML with all information, same as with typical websites with static pages which search engines can index. After JavaScript is loaded the web turns into a "single page app" and works respectively.

### Dynamic meta description

Page title and other meta tags can be dynamically changed directly in `jsx` in any place of your page. It is achieved via [react-helmet](https://github.com/nfl/react-helmet).

To change page title or add any kind of meta tag (e.g. [Open Graph Protocol](http://ogp.me/)) you need to wrap them by `<Helmet />` component:

```jsx
<Helmet defaultTitle="My Shop" titleTemplate="%s | My Shop">
  <meta name="description" content="This is My Shop powered by Deity Falcon" />
  <meta name="theme-color" content="#fff" />
</Helmet>
```

For more examples see [this](https://github.com/nfl/react-helmet#reference-guide)

## Google Analytics

[TODO]

## Google Tag Manager

[See more](https://marketingplatform.google.com/about/tag-manager/)

### Configuration

you can configure Google Tag Manager via `config` property in `falcon-client.config.js`.

`googleTagManager: object`

- `id: string`: (default `null`) Google Tag Manager ID

## Internal Server Error page

`falcon-client` provide default error page for http 500 error. You can override it and provide your own by placing `500.http` file in `./views/errors/` directory.

## Testing

### Mocking `falcon-client`

`falcon-client` exposes `FalconClientMock` component which allows you to setup application context inside unit test environment.
`FalconClientMock` can receive props for mock version of React context provider components used by `falcon-client` internally:

- `apollo: object` - props for `MockProvider` component from `react-apollo`
- `router: object` props for `MemoryRouter` component from `react-router-dom`
- `asyncComponent: object` - props for `AsyncComponentProvider` component from `react-async-component`
- `i18next: object` - props for `I18nextProvider` component from `react-i18next`

example unit test with `FalconClientMock` :

```jsx
import { FalconClientMock } from '@deity/falcon-client/test-utils';

describe('<Component />', () => {
  test('renders without exploding', () => {
    ReactDOM.render(
      <FalconClientMock>
        {
          // your <Component />
        }
      </FalconClientMock>,
      document.createElement('div')
    );
  });
});
```
