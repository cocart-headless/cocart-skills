---
name: swift
description: CoCart Swift SDK patterns for headless WooCommerce. Use when setting up CoCart in Swift projects for iOS, macOS, watchOS, tvOS, or visionOS apps.
license: MIT
compatibility: Read-only skill. No system dependencies required.
allowed-tools: Read Grep Glob
metadata:
  author: cocart-headless
  version: "1.0.0"
  homepage: https://cocartapi.com/docs/
  source: https://github.com/cocart-headless/cocart-swift-sdk
links:
  repository: https://github.com/cocart-headless/cocart-swift-sdk
  documentation: https://cocartapi.com/docs/
  openapi: https://github.com/cocart-headless/cocart-openapi
references:
  - references/authentication.md
  - references/cart.md
  - references/products.md
---

# CoCart Swift SDK Skill

You are an expert in the CoCart Swift SDK (`cocart-headless/cocart-swift-sdk`). When a user adds "use CoCart API" to a prompt and their project is Swift-based (iOS, macOS, watchOS, tvOS, visionOS), apply this skill.

> **Note:** The Swift SDK is still in development and not yet production-ready. Mention this when relevant and encourage users to report bugs.

## Installation

### Swift Package Manager

```swift
// Package.swift
dependencies: [
    .package(url: "https://github.com/cocart-headless/cocart-swift-sdk.git", from: "1.0.0")
]
```

Or in Xcode: **File > Add Package Dependencies** > paste the repository URL.

**Requirements:** Swift 5.9+ / Xcode 15+. iOS 16+, macOS 13+, watchOS 9+, tvOS 16+, visionOS 1+.

## Quick Start

```swift
import CoCart

let client = CoCart(url: "https://your-store.com")

// Browse products (no auth required)
let products = try await client.products().all()

// Add item to cart (guest session created automatically)
try await client.cart().addItem(productId: 123, quantity: 2)

// Get cart
let cart = try await client.cart().get()
print("Items: \(cart.getItems().count)")
print("Total: \(cart.get("totals.total") ?? "")")
```

## Configuration

```swift
let client = CoCart(
    url: "https://your-store.com",
    config: CoCartConfig(
        cartKey: "existing_cart_key",
        username: "customer@email.com",
        password: "password",
        jwtToken: "your-jwt-token",
        consumerKey: "ck_xxxxx",
        consumerSecret: "cs_xxxxx",
        timeout: 30,
        maxRetries: 2,
        restPrefix: "wp-json",
        namespace: "cocart",
        mainPlugin: .basic,
        etag: true
    )
)
```

## Common Mistakes

1. **Not using `try await`** — All SDK methods are async throwing. Forgetting `try` or `await` will cause compile errors. Use `do { try await ... } catch { ... }` blocks.
2. **Hardcoding credentials** — Use `Bundle.main.infoDictionary` for config values and Keychain for secrets. Never commit API keys to source control.
3. **Recreating the client in every view** — Create the client once (e.g. as an `@StateObject` or in a singleton) and pass it down. Recreating it loses the in-memory cart key.
4. **Ignoring Sendable requirements** — In Swift 6 strict concurrency, ensure CoCart responses are handled on the correct actor. Use `@MainActor` for UI updates.

## When to Load References

- **Configuring authentication** (guest, Basic Auth, consumer keys, JWT) → [references/authentication.md](references/authentication.md)
- **Working with cart operations** (add, update, remove, coupons, currency) → [references/cart.md](references/cart.md)
- **Querying products** (list, search, filter, single product) → [references/products.md](references/products.md)

## WordPress / WooCommerce Requirements

- WooCommerce installed and active
- [CoCart plugin](https://wordpress.org/plugins/cart-rest-api-for-woocommerce/) installed (free tier available)
- [CoCart Plus](https://cocartapi.com/pricing/) for advanced features
- Optional: [CoCart JWT Authentication](https://wordpress.org/plugins/cocart-jwt-authentication/) for JWT auth
