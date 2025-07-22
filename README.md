# Sylius Build Test Application GitHub Action

The goal of this action is to reduce the repetitive part of our every Sylius GitHub Workflow file.

## Usage

Below you can find an example of a workflow file that uses this action. Keep in mind `actions/checkout@v2`
action is required and must be run **before** `SyliusLabs/BuildTestAppAction` action.

> **Note!** If you want to use this action with Sylius 1.12.7 or lower, please use version `2.0` of this action. Version `2.1` introduced
> a support for fixed PostgreSQL migrations (available since Sylius 1.12.8), and will fail on older Sylius versions.

```yaml
name: Example Workflow

on:
    push: ~

jobs:
    tests:
        runs-on: ubuntu-latest

        name: "Sylius ${{ matrix.sylius }}, PHP ${{ matrix.php }}, Symfony ${{ matrix.symfony }}, MySQL ${{ matrix.mysql }}"

        env:
            APP_ENV: test_cached
            DATABASE_URL: "mysql://root:root@127.0.0.1/sylius?serverVersion=${{ matrix.mysql }}"

        strategy:
            fail-fast: false
            matrix:
                php: [ "8.1" ]
                symfony: [ "^5.4" ]
                sylius: [ "1.12", "1.13" ]
                node: [ "16.x" ]
                mysql: [ "8.0" ]

        steps:
            -   uses: actions/checkout@v2

            -   name: Build application
                uses: SyliusLabs/BuildTestAppAction@v2.1
                with:
                    e2e: "yes"
                    database_version: ${{ matrix.mysql }}
                    php_version: ${{ matrix.php }}
                    symfony_version: ${{ matrix.symfony }}
                    legacy_postgresql_setup: ${{ matrix.sylius == '1.13' && 'no' || 'yes' }}

            -   name: Run Behat
                run: |
                    vendor/bin/behat
```

## Inputs

| Name                      | Description                                                                                       | Default                                                 | Required to be explicitly defined |
|---------------------------|---------------------------------------------------------------------------------------------------|---------------------------------------------------------|-----------------------------------|
| `build_type`              | Type of the build. Possible values: `sylius`, `app`, `plugin`.                                    | `app`                                                   | No                                |
| `application_root_dir`    | Root directory of the application (useful in monorepos).                                          | Resolved automatically based on `build_type`.           | No                                |
| `application_test_dir`    | Directory of the test application (used to run console commands, fixtures, etc.).                 | Resolved automatically based on `build_type`.           | No                                |
| `console_path`            | Relative path to Symfony's `console` binary (overrides auto-detection)             | `vendor/bin/console` or `bin/console` (auto)         | No |
| `cache_key`               | A cache key to be used (used with `actions/cache`).                                               | *(empty)*                                               | No                                |
| `cache_restore_key`       | A cache restore key to be used.                                                                   | *(empty)*                                               | No                                |
| `e2e`                     | Whether to prepare the test application for end-to-end tests.                                     | `no`                                                    | No                                |
| `e2e_js`                  | Whether to prepare the test application for JS-based end-to-end tests.                            | `no`                                                    | No                                |
| `database`                | Database engine to use. One of: `mysql`, `mariadb`, `postgresql`.                                 | `mysql`                                                 | Yes                               |
| `database_version`        | Version of the database to be used.                                                               | `8.0` (MySQL)                                           | No                                |
| `legacy_postgresql_setup` | Whether to use legacy PostgreSQL setup (for Sylius versions before 1.13).                         | `no`                                                    | No                                |
| `php_version`             | PHP version to be used.                                                                           | *(none)*                                                | Yes                               |
| `php_extensions`          | List of PHP extensions to install.                                                                | `intl, gd, opcache, mysql, pdo_mysql, pgsql, pdo_pgsql` | No                                |
| `php_tools`               | PHP tools to install (e.g. `symfony`, `composer`).                                                | `symfony`                                               | No                                |
| `php_coverage`            | Coverage tool to use (e.g. `xdebug`, `pcov`, `none`).                                             | `none`                                                  | No                                |
| `symfony_version`         | Symfony version to be used.                                                                       | *(none)*                                                | Yes                               |
| `sylius_version`          | Sylius version to be used.                                                                        | *(none)* – no restriction applied                       | No                                |
| `sylius_integration`      | Internal usage: run Sylius with a specific integration.                                           | *(empty)*                                               | No                                |
| `node_version`            | Node.js version to install (used when `e2e_js: yes`).                                             | `20.x`                                                  | No                                |
| `chrome_version`          | Google Chrome version to install (for JS e2e tests).                                              | `stable`                                                | No                                |
