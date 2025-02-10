# Sylius Build Test Application GitHub Action

The goal of this action is to reduce the repetitive part of our every Sylius GitHub Workflow file.

## Usage

Below you can find an example of a workflow file that uses this action. Keep in mind `actions/checkout@v2`
action is required and must be run **before** `SyliusLabs/BuildTestAppAction` action.

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
            -   uses: actions/checkout@v4

            -   name: Build application
                uses: SyliusLabs/BuildTestAppAction@v3.0
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

| Name                      | Description                                                                                                   | Default                                               | Required to be explicitly defined |
|---------------------------|---------------------------------------------------------------------------------------------------------------|-------------------------------------------------------|-----------------------------------|
| `build_type`              | Type of the build. Possible values: `sylius` (Sylius/Sylius repo), `app` (Sylius-Standard based), `plugin`    | `app`                                                 | No                                |
| `application_root_dir`    | Working directory for composer                                                                                | `.`                                                   | No                                |
| `application_test_dir`    | Directory of the test app (if empty the value gets resolved based on `build_type` and `application_root_dir`) | empty                                                 | No                                |
| `cache_key`               | Cache key to be used (if empty no cache will be used)                                                         | empty                                                 | No                                |
| `cache_restore_key`       | Cache restore key to be used (if empty no cache will be used)                                                 | empty                                                 | No                                |
| `e2e`                     | Whether prepare the test application for e2e tests                                                            | `no`                                                  | No                                |
| `e2e_js`                  | Whether prepare the test application for e2e tests with JS                                                    | `no`                                                  | No                                |
| `database`                | Database engine to be used. Possible values: `mysql`, `mariadb`, `postgresql`                                 | `mysql`                                               | No                                |
| `database_version`        | Database engine version to be used                                                                            |                                                       | Yes                               |
| `legacy_postgresql_setup` | Whether to use legacy PostgreSQL setup (necessary for Sylius <1.13)                                           | `no`                                                  | No                                |
| `php_version`             | PHP version to be used                                                                                        |                                                       | Yes                               |
| `php_extensions`          | PHP extensions to be installed                                                                                | intl, gd, opcache, mysql, pdo_mysql, pgsql, pdo_pgsql | No                                |
| `php_tools`               | PHP tools to be installed                                                                                     | symfony                                               | No                                |
| `php_coverage`            | PHP coverage to be used. Possible values: `none`, `xdebug`, `pcov`                                            | `none`                                                | No                                |
| `symfony_version`         | Symfony version to be used                                                                                    |                                                       | Yes                               |
| `sylius_version`          | Sylius version to be used (if empty no version restriction is applied)                                        | empty                                                 | No                                |
| `sylius_integration`      | Parameter only for our internal purposes, where we test Sylius in different configurations                    | empty                                                 | No                                |
| `node_version`            | Node version to be used                                                                                       | `20.x`                                                | No                                |
| `chrome_version`          | Chrome version to be used                                                                                     | `stable`                                              | No                                |
