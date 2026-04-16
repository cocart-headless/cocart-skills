---
name: go
description: CoCart Go SDK patterns for headless WooCommerce. Use when setting up CoCart in Go projects using net/http, Gin, Echo, Fiber, or any Go HTTP framework.
license: MIT
compatibility: Read-only skill. No system dependencies required.
allowed-tools: Read Grep Glob
metadata:
  author: cocart-headless
  version: "1.0.0"
  homepage: https://cocartapi.com/docs/
  source: https://github.com/cocart-headless/cocart-go
links:
  repository: https://github.com/cocart-headless/cocart-go
  documentation: https://cocartapi.com/docs/
  openapi: https://github.com/cocart-headless/cocart-openapi
references:
  - references/authentication.md
  - references/cart.md
  - references/products.md
---

# CoCart Go SDK Skill

You are an expert in the CoCart Go SDK (`cocart-headless/cocart-go`). When a user adds "use CoCart API" to a prompt and their project is Go-based, apply this skill.

> **Note:** The Go SDK is still in development and not yet production-ready. Mention this when relevant and encourage users to report bugs.

## Installation

```bash
go get github.com/cocart-headless/cocart-sdk-go
```

**Requirements:** Go 1.23+ (required for `iter.Seq2` range-over-func used by the paginator). Zero external dependencies — uses only the Go standard library.

## Quick Start

```go
package main

import (
    "context"
    "fmt"
    "log"

    cocart "github.com/cocart-headless/cocart-sdk-go"
)

func main() {
    ctx := context.Background()
    client := cocart.NewClient("https://your-store.com")

    // Browse products (no auth required)
    products, err := client.Products().All(ctx, nil)
    if err != nil {
        log.Fatal(err)
    }
    fmt.Println("Products:", string(products.Body))

    // Add to cart (guest session created automatically)
    response, err := client.Cart().AddItem(ctx, 123, 2)
    if err != nil {
        log.Fatal(err)
    }
    fmt.Println("Added item:", response.Get("item_key", ""))
}
```

## Configuration Options

```go
client := cocart.NewClient(
    "https://your-store.com",
    cocart.WithCartKey("existing_cart_key"),
    cocart.WithBasicAuth("customer@email.com", "password"),
    cocart.WithJWTToken("your-jwt-token"),
    cocart.WithWooCommerceKeys("ck_xxxxx", "cs_xxxxx"),
    cocart.WithTimeout(15 * time.Second),
    cocart.WithMaxRetries(2),
    cocart.WithRESTPrefix("wp-json"),
    cocart.WithNamespace("cocart"),
    cocart.WithETag(true),
    cocart.WithDebug(true),
)
```

## Common Mistakes

1. **Forgetting `context.Background()`** — Every SDK call requires a `context.Context` as the first argument. Always pass `ctx` or a request-scoped context.
2. **Not checking errors** — Go SDK returns `(response, error)` tuples. Always handle the error before accessing response fields.
3. **Using Go < 1.23** — The paginator uses `iter.Seq2` range-over-func which requires Go 1.23+. Older versions will fail to compile.
4. **Ignoring the second return from `Get()`** — `cart.Get("totals.total", "")` takes a default value. Don't discard it or you'll get empty strings silently.

## When to Load References

- **Configuring authentication** (guest, Basic Auth, consumer keys, JWT) → [references/authentication.md](references/authentication.md)
- **Working with cart operations** (add, update, remove, coupons, currency) → [references/cart.md](references/cart.md)
- **Querying products** (list, search, filter, pagination) → [references/products.md](references/products.md)

## WordPress / WooCommerce Requirements

- WooCommerce installed and active
- [CoCart plugin](https://wordpress.org/plugins/cart-rest-api-for-woocommerce/) installed (free tier available)
- [CoCart Plus](https://cocartapi.com/pricing/) for advanced features
- Optional: [CoCart JWT Authentication](https://wordpress.org/plugins/cocart-jwt-authentication/) for JWT auth
