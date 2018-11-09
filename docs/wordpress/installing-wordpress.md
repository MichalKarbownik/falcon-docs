---
title: Installing Wordpress Module
---

This Wordpress Module registers required endpoints and filters for DEITY Falcon

## Installation

- Clone Deity Wordpress to your plugin folder
- Enable the plugin

## Currently handled features

- fetch url method
- get acf fields - You will need to install [Advanced Custom Fields plugin](https://www.advancedcustomfields.com/) 
    ```text
    [apiPrefix]/acf/v3
    ```
    ```text
    [apiPrefix]/acf/v2
    ```

- get posts, pages, authors, tags, video
    ```text
    [apiPrefix]/v2/posts
    ```
    ```text
    [apiPrefix]/v2/pages
    ```
    ```text
    [apiPrefix]/v2/media
    ```
    ```text
    [apiPrefix]/v2/types
    ```
    ```text
    [apiPrefix]/v2/tags
    ```
    ```text
    [apiPrefix]/v2/statuses
    ```
    ```text
    [apiPrefix]/v2/taxonomies
    ```
    ```text
    [apiPrefix]/v2/comments
    ```
    ```text
    [apiPrefix]/v2/settings
    ```

- get popular posts
    ```text
    [apiPrefix]/v2/posts/popular
    ```

- fetch languages - To support Wordpress languages use [WPML plugin](https://wpml.org/).

- get logo
   ```text
    [apiPrefix]/v2/blog/info
   ```

- fetch categories
    ```text
    [apiPrefix]/v2/categories
    ```

- get menus with acf fields support in menu item 
    ```text
    [apiPrefix]/v2/menus/
    ```

- unset users endpoint
    ```text
    [apiPrefix]/v2/posts/users
    ```
    
- 404 status

## Add cdn support
To support CDN make sure that W3 Total Cache plugin is installed : https://wordpress.org/plugins/w3-total-cache/

## Versioning

We use [SemVer](http://semver.org/) for versioning. For the versions available, see the [tags on this repository](https://github.com/deity-io/falcon-wordpress-module/tags). 