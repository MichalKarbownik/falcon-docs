---
title: Magento 2 Module
---

## Getting Started

Install and configure your Magento 2 shop to proceed with Deity Module installation

### Prerequisites

DEITY Magento module is compatible with Magento version 2.2+ CE and EE versions.
Full scale support of versions 2.0.* and 2.1.* is not our priority.
However if you encounter an issue running with Magento version lower than 2.2 feel free
to open an issue or reach out to our support channel.

### Installing

Installing DEITY Magento 2 Module is similar to installing any module for the Magento 2 platform

```bash
composer require deity/falcon-magento ^3.0.0
bin/magento setup:upgrade
```

When the module is setup, create one extra magento admin user for DEITY Falcon to connect.
This user exists purely for the API.

```bash
bin/magento admin:user:create \
  --admin-user='your-admin-username' \
  --admin-password='your-admin-password' \
  --admin-email='admin@deity.test' \
  --admin-firstname='node' \
  --admin-lastname='Deity'
```

#### Connect Falcon to your Magento instance

Configure Falcon Server to connect to your Magento instance.
You can do so by changing your [Falcon-Server config](miscellaneous/config.md).

> Note: Use your API admin user credentials that you've just created here

```json
{
  "apis": {
    "api-magento2": {
      "package": "@deity/falcon-magento2-api",
      "config": {
        "host": "your-magento-host-url-here",
        "protocol": "https",
        "username": "your-admin-username",
        "password": "your-admin-password"
      }
    }
  }
}
```

Please be aware that Magento performance depends heavily on which mode it is running in, `developer` mode will negatively impact performance. Also remember to follow Magento's best practices.

## Versioning

[SemVer](http://semver.org/) is used for versioning. For the versions available, see the [tags on this repository](https://github.com/deity-io/falcon-magento2-module/tags).

## Elements introduced to Magento by this module

Custom API endpoints provided by this module:

- `[GET] /rest/V1/attributes/filters` - get list of attributes used in catalog filters
- `[GET] /rest/V1/categories/:categoryId/breadcrumbs` - get category breadcrumbs to the root category
- `[GET] /rest/V1/categories/homepage` - get categories marked as "Show on homepage" (max - 6)
- `[GET] /rest/V1/categories` (**overridden**) - get category tree with `url_path` data
- `[GET] /rest/V1/customer-payment/:customerId/:orderId/adyen-link` - get Adyen payment link for redirection (for registered customer)
- `[GET] /rest/V1/customers/me/address` - get list of customer addresses (filterable with searchCriteria parameter)
- `[GET] /rest/V1/customers/me/address/:addressId` - get info about specific customer address
- `[PUT] /rest/V1/customers/password/reset` - reset password with reset token (missing in magento API)
- `[GET] /rest/V1/guest-carts/:cartId/paypal-fetch-token` - get PayPal token
- `[GET] /rest/V1/guest-orders/:orderId/order-info` - get data for the order specified by masked id for guest orders
- `[GET] /rest/V1/guest-payment/:cartId/:orderId/adyen-link` - get Adyen payment link for redirection
- `[GET] /rest/V1/info` - get basic settings for current shop
- `[GET] /rest/V1/menu` - get menu tree from magento
- `[GET] /rest/V1/order-info/:quoteId` - get order information from quote ID (orderId, revenue, shipping, tax etc)
- `[GET] /rest/V1/orders/:orderId/order-info` - get data for the order specified by order id for logged in customer
- `[GET] /rest/V1/orders/mine` - get list of logged customer orders
- `[GET] /rest/V1/products` (**overridden**) - get product list with a custom `filters` data in response
- `[GET] /rest/V1/url/?requestPath=:url` - get info about product, category or cms page by the given URL
- `[POST] /rest/V1/carts/mine/payment-information` (**overridden**) - modifies the docblock of function return type (may be int or OrderResponse object)
- `[POST] /rest/V1/customers/me/address` - create new address for customer
- `[POST] /rest/V1/contact` - send a contact email
- `[POST] /rest/V1/guest-carts/:cartId/payment-information` (**overridden**) - modifies the docblock of function return type (may be int or OrderResponse object)
- `[POST] /rest/V1/integration/admin/token` (**overridden**) - return object with token and valid time in hours
- `[POST] /rest/V1/integration/customer/token` (**overridden**) - adding guestQuoteId param to merge current guest quote with logged in customer
- `[PUT] /rest/V1/carts/mine/deity-order` - place order with Adyen credit card as a logged in customer - getting an object as a response
- `[PUT] /rest/V1/guest-carts/:cartId/deity-order` - place order with Adyen credit card - getting an object as a response
- `[PUT] /rest/V1/customers/me/address` - update customer address
- `[PUT] /rest/V1/customers/me/newsletter/subscribe` - subscribe customer to newsletter
- `[PUT] /rest/V1/customers/me/newsletter/unsubscribe` - unsubscribe customer to newsletter
- `[DELETE] /rest/V1/customers/me/address/:addressId` - remove customer address

Extension attributes:

- `Magento\Bundle\Api\Data\LinkInterface`:
    ```json
    {
      "name": "string",
      "catalog_display_price": "string"
    }
    ```
- `Magento\Catalog\Api\Data\ProductInterface`:
    ```json
    {
      "thumbnail_resized_url": "string",
      "thumbnail_url": "string",
      "media_gallery_sizes": "Deity\MagentoApi\Api\Data\GalleryMediaEntrySizeInterface[]",
      "catalog_display_price": "float",
      "min_price": "float",
      "max_price": "float",
      "breadcrumbs": "Deity\MagentoApi\Api\Data\BreadcrumbInterface[]"
    }
    ```
- `Magento\Catalog\Api\Data\CategoryInterface`:
    ```json
    {
      "breadcrumbs": "Deity\MagentoApi\Api\Data\BreadcrumbInterface[]"
    }
    ```
- `Magento\Customer\Api\Data\CustomerInterface`:
    ```json
    {
      "guest_quote_id": "string",
      "newsletter_subscriber": "bool"
    }
    ```
- `Magento\Sales\Api\Data\OrderInterface`:
    ```json
    {
      "currency": "string",
      "masked_id": "string",
      "shipping_address": "Magento\Sales\Api\Data\OrderAddressInterface"
    }
    ```
- `Magento\Sales\Api\Data\OrderItemInterface`:
    ```json
    {
      "currency": "string",
      "display_price": "string",
      "link": "string",
      "row_total_incl_tax": "string",
      "thumbnail_url": "string",
      "url_key": "string",
      "options": "string"
    }
    ```
- `Magento\Store\Api\Data\StoreConfigInterface`:
    ```json
    {
      "optional_post_codes": "mixed",
      "min_password_length": "int",
      "min_password_char_class": "int"
    }
    ```
- `Magento\Store\Api\Data\StoreInterface`:
    ```json
    {
      "is_active": "int"
    }
    ```
- `Magento\Quote\Api\Data\TotalsItemInterface`:
    ```json
    {
      "thumbnail_url": "string",
      "url_key": "string",
      "available_qty": "string"
    }
    ```

Custom changes:

- Price for Configurable products (`$product->setPriceCalculation(false)`)

Custom commands:

- `rapidflow:attribute:options [-s|--shop="..."] [--attribute_codes="..."]` - Generate import file for option label translation


## Development

### GrumPHP

If you develop the module within `vendor` of a Magento installation, follow these steps to use the code analysis tools:

- Run `composer install` inside the repository directory
- If you encounter an error like

    ```bash
    PHP Fatal error:  Uncaught Error: Call to undefined method Symfony\Component\Yaml\Parser::parseFile() in /home/fs/Projekte/deity/deity-sandbox/magento/vendor/symfony/dependency-injection/Loader/YamlFileLoader.php:667
    ```

    it is because of version conflicts of Symfony components between tools and the Magento installation. To prevent loading dependencies from the Magento installation, edit `vendor/bin/grumphp` and remove these lines:

    ```bash
        __DIR__ . '/../vendor/autoload.php',
        __DIR__ . '/../../../autoload.php',
    ```

    Then run `vendor/bin/grumphp git:init`
- You should see a message like:

    ```bash
    Watch out! GrumPHP is sniffing your commits!
    ```
