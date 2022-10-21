# Sylius Build Test Application GitHub Action

The goal of this action is to reduce the repetitive part of our every Sylius GitHub Workflow file.

## Usage

Below you can find an example of a workflow file that uses this action. Keep in mind `actions/checkout@v2` and `shivammathur/setup-php@v2`
action are required and must be run **before** `SyliusLabs/BuildTestAppAction` action.

```yaml
name: Sample Workflow

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

            -   name: Setup PHP
                uses: shivammathur/setup-php@v2
                env:
                    runner: self-hosted
                with:
                    php-version: "${{ matrix.php }}"
                    extensions: intl
                    tools: symfony
                    coverage: none

            -   name: Build test application
                uses: SyliusLabs/BuildTestAppAction@<tag>       # <tag> is the version of the action you want to use, you can find the latest version on the releases page
                with:
                    sylius-version: "${{ matrix.sylius }}"      # Sylius version, required
                    symfony-version: "${{ matrix.symfony }}"    # Symfony version, required
                    mysql-version: "${{ matrix.mysql }}"        # MySQL version, required
                    node-version: "${{ matrix.node }}"          # Node version, optional, default: 16.x
                    environment: "test"                         # Environment, optional, default: test
                    working-directory: "src/SomeSyliusPlugin"   # Working directory in which commands are run, optional, default: "." (root directory)
                    plugin-build: "yes"                         # Tells whether we build an app or plugin, optional, default: no

            -   name: Run Behat
                run: |
                    vendor/bin/behat
```
