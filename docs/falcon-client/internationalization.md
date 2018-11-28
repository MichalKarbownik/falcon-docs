---
title: Internationalization
---

Internationalization is based on [i18next](https://www.i18next.com/).

## Basics

All i18n resources should be placed in `./i18n` directory, and folder structure should follow pattern `{{lng}}/{{ns}}.json`. Which means each language needs to have own directory with `json` file per namespace:

```text
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

## Options

- `mainSource: string` - relative path to your custom i18n resources which should extend defaults
- `defaultSources: string[]` - array of absolute paths to resources which should be merged to your custom i18n resources
- `output: string` - relative path to output directory
- `filter.lng: string[]` - array of language codes to filter `defaultSources` directories, if any, then only matching will be returned, otherwise all of them
- `filter.ns: string[]` - array of namespace codes to filter `defaultSources` directories, if any, then only matching will be returned, otherwise all of them

All paths should point to directories with file structure compatible with `{{lng}}/{{ns}}.json` pattern, e.g:

```text
-en
  |-common.json
  |-blog.json
  |-...
-...
```