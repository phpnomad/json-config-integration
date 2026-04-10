# phpnomad/json-config-integration

[![Latest Version](https://img.shields.io/packagist/v/phpnomad/json-config-integration.svg)](https://packagist.org/packages/phpnomad/json-config-integration)
[![Total Downloads](https://img.shields.io/packagist/dt/phpnomad/json-config-integration.svg)](https://packagist.org/packages/phpnomad/json-config-integration)
[![PHP Version](https://img.shields.io/packagist/php-v/phpnomad/json-config-integration.svg)](https://packagist.org/packages/phpnomad/json-config-integration)
[![License](https://img.shields.io/packagist/l/phpnomad/json-config-integration.svg)](https://packagist.org/packages/phpnomad/json-config-integration)

`phpnomad/json-config-integration` wires JSON-file-backed configuration into a PHPNomad application. It binds the strategies from `phpnomad/array-json-config` to the interfaces defined in `phpnomad/config`, then registers a set of JSON files against `ConfigService` during the loader bootstrap. Install this package when you want configuration values stored in JSON files on disk and accessible anywhere via dot-notation keys.

## Installation

```bash
composer require phpnomad/json-config-integration
```

## What This Provides

- A single `ConfigInitializer` class that binds `ArrayConfigStrategy` to `ConfigStrategy` and `JsonFileLoader` to `ConfigFileLoaderStrategy` in the DI container.
- During `load()`, it walks the constructor-supplied `['namespace' => '/path/to/file.json']` map and calls `ConfigService::registerConfig()` for each entry, so every file is parsed and indexed in one pass.
- Implementations of `HasClassDefinitions`, `Loadable`, and `CanSetContainer`, so the initializer drops into any `phpnomad/loader` bootstrap sequence without extra glue.

## Requirements

- `phpnomad/config`
- `phpnomad/array-json-config`
- `phpnomad/di` ^2.0
- `phpnomad/loader` ^1.0 || ^2.0

## Usage

Register the initializer with your application's bootstrapper and hand it a map of namespace keys to JSON file paths.

```php
<?php

use PHPNomad\Component\JsonConfigIntegration\ConfigInitializer;
use PHPNomad\Config\Services\ConfigService;
use PHPNomad\Loader\Bootstrapper;

$configInitializer = new ConfigInitializer([
    'app'      => __DIR__ . '/config/app.json',
    'database' => __DIR__ . '/config/database.json',
    'mailer'   => __DIR__ . '/config/mailer.json',
]);

$bootstrapper = new Bootstrapper($container, $configInitializer);
$bootstrapper->load();

/** @var ConfigService $config */
$config = $container->get(ConfigService::class);

$config->get('app.debug');             // value from app.json
$config->get('database.default.host'); // nested lookup into database.json
$config->get('mailer.port', 587);      // default fallback
```

Each file is parsed once during `load()` and its contents are stored under the namespace key you provided, so consumers read values through `ConfigService` without re-reading the filesystem.

## Documentation

Full documentation for PHPNomad lives at [phpnomad.com](https://phpnomad.com).

## License

MIT. See [LICENSE](LICENSE).
</content>
</invoke>