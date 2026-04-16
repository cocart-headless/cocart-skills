# Authentication

## Guest (default)

```dart
final client = CoCart('https://your-store.com');
// Cart key tracked automatically in memory
```

## Basic Auth

```dart
final client = CoCart(
  'https://your-store.com',
  config: CoCartConfig(
    username: 'customer@email.com',
    password: 'password',
  ),
);
```

## JWT Auth with Auto-Refresh

Requires the free [CoCart JWT Authentication](https://wordpress.org/plugins/cocart-jwt-authentication/) plugin.

```dart
// Login
final tokens = await client.jwt().login('customer@email.com', 'password');

// Auto-refresh — transparently refreshes expired tokens
final cart = await client.jwt().withAutoRefresh((c) async {
  return await c.cart().get();
});
```

## Consumer Keys (WooCommerce admin)

```dart
final client = CoCart(
  'https://your-store.com',
  config: CoCartConfig(
    consumerKey: 'ck_xxxxx',
    consumerSecret: 'cs_xxxxx',
  ),
);
```
