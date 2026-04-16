# Authentication

## Guest (default)

```php
$client = new CoCart('https://your-store.com');
// Cart key is automatically tracked via PHP session
```

## Basic Auth

```php
$client = new CoCart('https://your-store.com', [
    'username' => 'customer@email.com',
    'password' => 'password',
]);
```

## JWT Auth with Auto-Refresh

Requires the free [CoCart JWT Authentication](https://wordpress.org/plugins/cocart-jwt-authentication/) plugin.

```php
// Login and store tokens
$tokens = $client->jwt()->login('customer@email.com', 'password');

// Use auto-refresh — transparently refreshes expired tokens
$cart = $client->jwt()->withAutoRefresh(function ($c) {
    return $c->cart()->get();
});
```

## Consumer Keys (WooCommerce admin)

```php
$client = new CoCart('https://your-store.com', [
    'consumer_key'    => 'ck_xxxxx',
    'consumer_secret' => 'cs_xxxxx',
]);
```
