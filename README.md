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
                sylius: [ "^1.11" ]
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

            -   name: Run Behat
                run: |
                    vendor/bin/behat
```

## Inputs

| Name                 | Description                                                                                                | Default                                               | Required to be explicitly defined |
|----------------------|------------------------------------------------------------------------------------------------------------|-------------------------------------------------------|-----------------------------------|
| `build_type`         | Type of the build. Possible values: `sylius` (Sylius/Sylius repo), `app` (Sylius-Standard based), `plugin` | `app`                                                 | no                                |
| `cache_key`          | A cache key to be used                                                                                     |                                                       | No                                |
| `cache_restore_key`  | A cache restore key to be used                                                                             |                                                       | No                                |
| `e2e`                | Whether prepare the test application for e2e tests                                                         | `no`                                                  | No                                |
| `e2e_js`             | Whether prepare the test application for e2e tests with JS                                                 | `no`                                                  | No                                |
| `database`           | A database engine to be used. Possible values: `mysql`, `mariadb`, `postgresql`                            | `mysql`                                               | Yes                               |
| `database_version`   | MySQL version to be used                                                                                   | `8.0`                                                 | No                                |
| `php_version`        | PHP version to be used                                                                                     |                                                       | Yes                               |
| `php_extensions`     | PHP extensions to be installed                                                                             | intl, gd, opcache, mysql, pdo_mysql, pgsql, pdo_pgsql | No                                |
| `php_tools`          | PHP tools to be installed                                                                                  | symfony                                               | No                                |
| `symfony_version`    | Symfony version to be used                                                                                 |                                                       | Yes                               |
| `sylius_version`     | Sylius version to be used                                                                                  | by default no version restriction is applied          | no                                |
| `sylius_integration` | Parameter only for our internal purposes, where we test Sylius in different configurations                 | empty                                                 | No                                |
| `node_version`       | Node version to be used                                                                                    | `16.x`                                                | No                                |
