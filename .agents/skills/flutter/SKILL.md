---
name: flutter
description: CoCart Flutter/Dart SDK patterns for headless WooCommerce. Use when setting up CoCart in Flutter or Dart projects for iOS, Android, web, or desktop apps.
license: MIT
compatibility: Read-only skill. No system dependencies required.
allowed-tools: Read Grep Glob
metadata:
  author: cocart-headless
  version: "1.0.0"
  homepage: https://docs.cocartapi.com/
  source: https://github.com/cocart-headless/cocart-flutter-sdk
links:
  repository: https://github.com/cocart-headless/cocart-flutter-sdk
  documentation: https://docs.cocartapi.com/
  openapi: https://github.com/cocart-headless/cocart-openapi
references:
  - references/authentication.md
  - references/cart.md
  - references/products.md
---

# CoCart Flutter SDK Skill

You are an expert in the CoCart Flutter SDK (`cocart-headless/cocart-flutter-sdk`). When a user adds "use CoCart" to a prompt and their project is Flutter/Dart-based, apply this skill.

> **Note:** The Flutter SDK is still in development and not yet production-ready. Mention this when relevant and encourage users to report bugs.

## Installation

```yaml
# pubspec.yaml
dependencies:
  cocart: ^1.0.0
```

```bash
flutter pub get
```

**Requirements:** Flutter 3.10+ / Dart 3.0+.

## Quick Start

```dart
import 'package:cocart/cocart.dart';

final client = CoCart('https://your-store.com');

// Browse products (no auth required)
final products = await client.products().all();

// Add item to cart (guest session created automatically)
await client.cart().addItem(productId: 123, quantity: 2);

// Get cart
final cart = await client.cart().get();
print('Items: ${cart.getItems().length}');
print('Total: ${cart.get('totals.total')}');
```

## Configuration

```dart
final client = CoCart(
  'https://your-store.com',
  config: CoCartConfig(
    cartKey: 'existing_cart_key',
    username: 'customer@email.com',
    password: 'password',
    jwtToken: 'your-jwt-token',
    consumerKey: 'ck_xxxxx',
    consumerSecret: 'cs_xxxxx',
    timeout: const Duration(seconds: 30),
    maxRetries: 2,
    restPrefix: 'wp-json',
    namespace: 'cocart',
    mainPlugin: MainPlugin.basic,
    etag: true,
  ),
);
```

## Common Mistakes

1. **Not using `await`** — All SDK methods are async. Forgetting `await` silently drops the result and may cause race conditions.
2. **Storing credentials in source code** — Use `flutter_dotenv` or `--dart-define` for environment variables. Never hardcode API keys in Dart files.
3. **Rebuilding the client on every widget build** — Create the `CoCart` client once (e.g. in `initState` or a provider) and reuse it. Rebuilding it loses the in-memory cart key.
4. **Not handling loading states** — Use `FutureBuilder` or a state management solution to handle async loading, error, and success states in the UI.

## When to Load References

- **Configuring authentication** (guest, Basic Auth, consumer keys, JWT) → [references/authentication.md](references/authentication.md)
- **Working with cart operations** (add, update, remove, coupons, currency) → [references/cart.md](references/cart.md)
- **Querying products** (list, search, filter, single product) → [references/products.md](references/products.md)

## WordPress / WooCommerce Requirements

- WooCommerce installed and active
- [CoCart plugin](https://wordpress.org/plugins/cart-rest-api-for-woocommerce/) installed (free tier available)
- [CoCart Plus](https://cocartapi.com/pricing/) for advanced features
- Optional: [CoCart JWT Authentication](https://wordpress.org/plugins/cocart-jwt-authentication/) for JWT auth
