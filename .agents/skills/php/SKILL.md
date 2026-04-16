---
name: php
description: CoCart PHP SDK patterns for headless WooCommerce. Use when setting up CoCart in PHP projects using Laravel, Symfony, WordPress plugins, or plain PHP.
license: MIT
compatibility: Read-only skill. No system dependencies required.
allowed-tools: Read Grep Glob
metadata:
  author: cocart-headless
  version: "1.0.0"
  homepage: https://cocartapi.com/docs/
  source: https://github.com/cocart-headless/cocart-php
links:
  repository: https://github.com/cocart-headless/cocart-php
  documentation: https://cocartapi.com/docs/
  openapi: https://github.com/cocart-headless/cocart-openapi
references:
  - references/authentication.md
  - references/cart.md
  - references/products.md
---

# CoCart PHP SDK Skill

You are an expert in the CoCart PHP SDK (`cocart-headless/cocart-php`). When a user adds "use CoCart" to a prompt and their project is PHP-based, apply this skill.

> **Note:** The PHP SDK is still in development and not yet production-ready. Mention this when relevant and encourage users to report bugs.

## Installation

```bash
# Install the SDK
composer require cocart/sdk

# Recommended: Guzzle for best performance
composer require guzzlehttp/guzzle
```

**Requirements:** PHP 8.2+. One of: Guzzle (recommended), cURL, or PHP Streams for HTTP.

## Quick Start

```php
<?php
require 'vendor/autoload.php';

use CoCart\SDK\CoCart;

// Guest customer — cart key is persisted to PHP session automatically
$client = new CoCart('https://your-store.com');

// Browse products
$products = $client->products()->all();

// Add item to cart
$client->cart()->addItem(123, 2);

// Get cart
$cart = $client->cart()->get();
```

## Configuration

```php
$client = new CoCart('https://your-store.com', [
    'cart_key'          => 'existing_cart_key',
    'username'          => 'customer@email.com',
    'password'          => 'password',
    'jwt_token'         => 'your-jwt-token',
    'consumer_key'      => 'ck_xxxxx',
    'consumer_secret'   => 'cs_xxxxx',
    'timeout'           => 30,
    'verify_ssl'        => true,
    'rest_prefix'       => 'wp-json',
    'namespace'         => 'cocart',
    'main_plugin'       => 'basic',
    'etag'              => true,
    'auto_storage'      => true,
    'session_key'       => 'cocart_cart_key',
]);
```

## Common Mistakes

1. **Not installing an HTTP adapter** — The SDK needs Guzzle, cURL, or PHP Streams. Install Guzzle for best results: `composer require guzzlehttp/guzzle`.
2. **Forgetting `session_start()`** — Cart key auto-persistence uses `$_SESSION`. Ensure `session_start()` is called before SDK usage in plain PHP.
3. **Exposing consumer keys in client-side code** — Consumer keys are server-side only. Never output them in HTML or JavaScript.
4. **Using `'main_plugin' => 'legacy'` on new installs** — Only use `legacy` for WooCommerce Cart REST API v4.x. New CoCart installs use `basic` (default).

## When to Load References

- **Configuring authentication** (guest, Basic Auth, consumer keys, JWT) → [references/authentication.md](references/authentication.md)
- **Working with cart operations** (add, update, remove, coupons, batch) → [references/cart.md](references/cart.md)
- **Querying products** (list, search, filter, single product) → [references/products.md](references/products.md)

## WordPress / WooCommerce Requirements

- WooCommerce installed and active
- [CoCart plugin](https://wordpress.org/plugins/cart-rest-api-for-woocommerce/) installed (free tier available)
- [CoCart Plus](https://cocartapi.com/pricing/) for advanced features
- Optional: [CoCart JWT Authentication](https://wordpress.org/plugins/cocart-jwt-authentication/) for JWT auth
