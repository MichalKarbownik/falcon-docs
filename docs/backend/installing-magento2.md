---
title: Magento 2 Module
---

## Getting Started

Install and configure your Magento 2 shop to proceed with Deity Module installation

### Prerequisites

DEITY Magento module is compatible with Magento version 2.2. CE and EE versions.
Full scale support of versions 2.0.* and 2.1.* is not our priority.
However if you encounter an issue running with Magento version lower than 2.2 feel free
to open an issue or reach out to our support channel.

### Installing

Installing DEITY Magento 2 Module is similar to installing any module for the Magento 2 platform

```bash
composer require deity/falcon-magento 1.0.2
bin/magento setup:upgrade
```

When the module is setup, create one extra magento admin user for DEITY Falcon to connect

```bash
bin/magento admin:user:create \
  --admin-user='your-admin-username' \
  --admin-password='your-admin-password' \
  --admin-email='admin@deity.test' \
  --admin-firstname='node' \
  --admin-lastname='Deity'
```

## Documentation

Check out the integration documentation for the module.
[Integration_notes.md](docs/Integration_notes.md)

## Versioning

[SemVer](http://semver.org/) is used for versioning. For the versions available, see the [tags on this repository](https://github.com/deity-io/falcon-magento2-module/tags).
