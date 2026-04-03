# CoCart PHP SDK Skill

You are an expert in the CoCart PHP SDK (`cocart-headless/cocart-php`). When a user adds "use CoCart API" to a prompt and their project is PHP-based, apply this skill to provide accurate SDK installation, configuration, and code patterns.

> **Note:** The PHP SDK is still in development and not yet production-ready. Mention this when relevant and encourage users to report bugs.

---

## What is CoCart?

CoCart is a WordPress plugin that extends WooCommerce with a developer-friendly REST API for headless storefronts. The PHP SDK lets any PHP application (Laravel, Symfony, plain PHP, WordPress plugins, etc.) talk to a WooCommerce store via the CoCart API.

- **Docs:** https://cocartapi.com/docs/
- **SDK Repo:** https://github.com/cocart-headless/cocart-php
- **Packagist:** https://packagist.org/packages/cocart-headless/cocart-php

---

## Requirements

- PHP 8.2+
- CoCart plugin installed on the WooCommerce store
- One of: Guzzle (recommended), cURL, or PHP Streams for HTTP

---

## Installation

```bash
# Install the SDK
composer require cocart/sdk

# Recommended: Guzzle for best performance
composer require guzzlehttp/guzzle
```

For environments without Composer, see the [manual install guide](https://github.com/cocart-headless/cocart-php/blob/main/docs/installation.md).

---

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

---

## Configuration

Pass an options array as the second argument to configure the client:

```php
$client = new CoCart('https://your-store.com', [
    // Guest session
    'cart_key'          => 'existing_cart_key',

    // Basic Auth
    'username'          => 'customer@email.com',
    'password'          => 'password',

    // JWT Auth
    'jwt_token'         => 'your-jwt-token',
    'jwt_refresh_token' => 'your-refresh-token',

    // WooCommerce consumer keys (for admin Sessions API)
    'consumer_key'      => 'ck_xxxxx',
    'consumer_secret'   => 'cs_xxxxx',

    // Proxy support
    'auth_header'       => 'Authorization',

    // HTTP settings
    'timeout'           => 30,
    'verify_ssl'        => true,

    // Custom REST prefix / namespace (WhiteLabel add-on)
    'rest_prefix'       => 'wp-json',
    'namespace'         => 'cocart',

    // Plugin variant: 'basic' (default) or 'legacy' for old cart-rest-api-for-woocommerce v4.x
    'main_plugin'       => 'basic',

    // ETag conditional requests to reduce bandwidth (default: true)
    'etag'              => true,

    // Auto-persist cart key to $_SESSION (default: true)
    'auto_storage'      => true,
    'session_key'       => 'cocart_cart_key',
]);
```

---

## Authentication

### Guest (default)

```php
$client = new CoCart('https://your-store.com');
// Cart key is automatically tracked via PHP session
```

### Basic Auth

```php
$client = new CoCart('https://your-store.com', [
    'username' => 'customer@email.com',
    'password' => 'password',
]);
```

### JWT Auth with Auto-Refresh

Requires the free [CoCart JWT Authentication](https://wordpress.org/plugins/cocart-jwt-authentication/) plugin.

```php
// Login and store tokens
$tokens = $client->jwt()->login('customer@email.com', 'password');
// $tokens contains access_token and refresh_token

// Use auto-refresh — transparently refreshes expired tokens
$cart = $client->jwt()->withAutoRefresh(function ($c) {
    return $c->cart()->get();
});
```

### Consumer Keys (WooCommerce admin)

```php
$client = new CoCart('https://your-store.com', [
    'consumer_key'    => 'ck_xxxxx',
    'consumer_secret' => 'cs_xxxxx',
]);
```

---

## Cart Operations

```php
$client = new CoCart('https://your-store.com');

// Add item
$client->cart()->addItem(productId: 123, quantity: 2);

// Add item with variation
$client->cart()->addItem(123, 1, ['variation_id' => 456]);

// Get cart
$cart = $client->cart()->get();

// Update item quantity
$client->cart()->updateItem($itemKey, ['quantity' => 3]);

// Remove item
$client->cart()->removeItem($itemKey);

// Apply coupon
$client->cart()->applyCoupon('SAVE10');

// Remove coupon
$client->cart()->removeCoupon('SAVE10');

// Clear cart
$client->cart()->clear();
```

---

## Products API

```php
// List products
$products = $client->products()->all([
    'per_page' => 12,
    'page'     => 1,
]);

// Single product
$product = $client->products()->get(productId: 42);

// Search
$results = $client->products()->all(['search' => 'hoodie']);

// Filter by category
$byCategory = $client->products()->all(['category' => 23]);
```

---

## Sessions API (admin)

```php
$admin = new CoCart('https://your-store.com', [
    'consumer_key'    => 'ck_xxxxx',
    'consumer_secret' => 'cs_xxxxx',
]);

// List all active sessions
$sessions = $admin->sessions()->all();

// Get a specific session
$session = $admin->sessions()->get($cartKey);

// Delete a session
$admin->sessions()->delete($cartKey);
```

---

## Batch Requests

```php
// Multiple cart operations in a single HTTP call
$results = $client->batch([
    ['method' => 'POST', 'path' => '/cocart/v2/cart/add-item', 'body' => ['id' => 123, 'quantity' => 1]],
    ['method' => 'POST', 'path' => '/cocart/v2/cart/add-item', 'body' => ['id' => 456, 'quantity' => 2]],
]);
```

---

## Error Handling

```php
use CoCart\SDK\Exceptions\CoCartException;
use CoCart\SDK\Exceptions\ValidationException;
use CoCart\SDK\Exceptions\AuthenticationException;
use CoCart\SDK\Exceptions\NetworkException;

try {
    $client->cart()->addItem(-1, 0);
} catch (ValidationException $e) {
    // Client-side validation, no network request made
    echo 'Validation error: ' . $e->getMessage();
} catch (AuthenticationException $e) {
    echo 'Auth error: ' . $e->getMessage();
} catch (NetworkException $e) {
    echo 'Network error: ' . $e->getMessage();
} catch (CoCartException $e) {
    echo 'API error: ' . $e->getMessage() . ' (code: ' . $e->getCode() . ')';
}
```

---

## ETag Support

ETags are enabled by default and reduce bandwidth by returning `304 Not Modified` when data hasn't changed:

```php
$client = new CoCart('https://your-store.com', [
    'etag' => true, // default
]);

$cart = $client->cart()->get(); // Full response
$cart = $client->cart()->get(); // 304 if unchanged — uses cached response
```

---

## Further Reading

- [Installation & HTTP Adapters](https://github.com/cocart-headless/cocart-php/blob/main/docs/installation.md)
- [Authentication Guide](https://github.com/cocart-headless/cocart-php/blob/main/docs/authentication.md)
- [Cart API](https://github.com/cocart-headless/cocart-php/blob/main/docs/cart.md)
- [Products API](https://github.com/cocart-headless/cocart-php/blob/main/docs/products.md)
- [Sessions API](https://github.com/cocart-headless/cocart-php/blob/main/docs/sessions.md)
- [Error Handling](https://github.com/cocart-headless/cocart-php/blob/main/docs/error-handling.md)
- [Utilities](https://github.com/cocart-headless/cocart-php/blob/main/docs/utilities.md)
