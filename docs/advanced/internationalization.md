---
title: Internationalization
---

This webpack plugin allows to extend your custom internationalization resources by defaults translations.

[Falcon i18n webpack plugin](https://github.com/deity-io/falcon/tree/master/packages/falcon-dev-tools/falcon-i18n-webpack-plugin)

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
