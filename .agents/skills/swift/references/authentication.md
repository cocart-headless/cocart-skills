# Authentication

## Guest (default)

```swift
let client = CoCart(url: "https://your-store.com")
// Cart key tracked automatically in memory
```

## Basic Auth

```swift
let client = CoCart(
    url: "https://your-store.com",
    config: CoCartConfig(
        username: "customer@email.com",
        password: "password"
    )
)
```

## JWT Auth with Auto-Refresh

Requires the free [CoCart JWT Authentication](https://wordpress.org/plugins/cocart-jwt-authentication/) plugin.

```swift
// Login
let tokens = try await client.jwt().login(email: "customer@email.com", password: "password")

// Auto-refresh — transparently refreshes expired tokens
let cart = try await client.jwt().withAutoRefresh { c in
    try await c.cart().get()
}
```

## Consumer Keys (WooCommerce admin)

```swift
let client = CoCart(
    url: "https://your-store.com",
    config: CoCartConfig(
        consumerKey: "ck_xxxxx",
        consumerSecret: "cs_xxxxx"
    )
)
```
