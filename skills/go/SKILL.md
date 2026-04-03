# CoCart Go SDK Skill

You are an expert in the CoCart Go SDK (`cocart-headless/cocart-go`). When a user adds "use CoCart API" to a prompt and their project is Go-based, apply this skill to provide accurate SDK installation, configuration, and code patterns.

> **Note:** The Go SDK is still in development and not yet production-ready. Mention this when relevant and encourage users to report bugs.

---

## What is CoCart?

CoCart is a WordPress plugin that extends WooCommerce with a developer-friendly REST API for headless storefronts. The Go SDK lets any Go application talk to a WooCommerce store via the CoCart API, with zero external dependencies — only the standard library.

- **Docs:** https://cocartapi.com/docs/
- **SDK Repo:** https://github.com/cocart-headless/cocart-go
- **Module:** `github.com/cocart-headless/cocart-sdk-go`

---

## Requirements

- Go 1.23+ (required for `iter.Seq2` range-over-func used by the paginator)
- CoCart plugin installed on the WooCommerce store
- Zero external dependencies — uses only the Go standard library

---

## Installation

```bash
go get github.com/cocart-headless/cocart-sdk-go
```

---

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

    // Create a client pointing to your WooCommerce store
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

    // Get cart
    cart, err := client.Cart().Get(ctx, nil)
    if err != nil {
        log.Fatal(err)
    }
    fmt.Println("Items:", cart.GetItems())
    fmt.Println("Total:", cart.Get("totals.total", ""))
}
```

---

## Configuration Options

The Go SDK uses the functional options pattern for clean, flexible configuration:

```go
client := cocart.NewClient(
    "https://your-store.com",

    // Guest session
    cocart.WithCartKey("existing_cart_key"),

    // Basic Auth
    cocart.WithBasicAuth("customer@email.com", "password"),

    // JWT Auth
    cocart.WithJWTToken("your-jwt-token"),
    cocart.WithJWTRefreshToken("your-refresh-token"),

    // WooCommerce consumer keys (admin / Sessions API)
    cocart.WithWooCommerceKeys("ck_xxxxx", "cs_xxxxx"),

    // HTTP settings
    cocart.WithTimeout(15 * time.Second),
    cocart.WithMaxRetries(2),

    // Custom REST prefix / namespace (WhiteLabel add-on)
    cocart.WithRESTPrefix("wp-json"),
    cocart.WithNamespace("cocart"),

    // Plugin variant
    cocart.WithMainPlugin(cocart.MainPluginBasic), // or MainPluginLegacy

    // ETag conditional requests (default: true)
    cocart.WithETag(true),

    // Storage adapter for cart key / token persistence
    cocart.WithStorage(myStorage),

    // Auth header name (for proxies that strip Authorization)
    cocart.WithAuthHeaderName("X-CoCart-Auth"),

    // Custom HTTP client
    cocart.WithHTTPClient(myHTTPClient),

    // Debug logging
    cocart.WithDebug(true),
)
```

---

## Authentication

### Guest (default)

```go
client := cocart.NewClient("https://your-store.com")
// Cart key is tracked automatically in memory via MemoryStorage
```

### Basic Auth

```go
client := cocart.NewClient(
    "https://your-store.com",
    cocart.WithBasicAuth("customer@email.com", "password"),
)
```

### JWT Auth with Auto-Refresh

Requires the free [CoCart JWT Authentication](https://wordpress.org/plugins/cocart-jwt-authentication/) plugin.

```go
ctx := context.Background()

// Login
tokens, err := client.JWT().Login(ctx, "customer@email.com", "password")
if err != nil {
    log.Fatal(err)
}

// Auto-refresh — transparently refreshes expired tokens
cart, err := client.JWT().WithAutoRefresh(ctx, func(c *cocart.Client) (*cocart.CartResponse, error) {
    return c.Cart().Get(ctx, nil)
})
```

### Consumer Keys (admin)

```go
client := cocart.NewClient(
    "https://your-store.com",
    cocart.WithWooCommerceKeys("ck_xxxxx", "cs_xxxxx"),
)
```

---

## Cart Operations

```go
ctx := context.Background()
client := cocart.NewClient("https://your-store.com")

// Add item
resp, err := client.Cart().AddItem(ctx, 123, 2)

// Add item with variation
resp, err := client.Cart().AddItem(ctx, 123, 1, cocart.WithVariationID(456))

// Get cart
cart, err := client.Cart().Get(ctx, nil)

// Dot-notation access
total := cart.Get("totals.total", "")
currencyCode := cart.Get("currency.currency_code", "")

// Get typed items
items := cart.GetItems()

// Update item quantity
err = client.Cart().UpdateItem(ctx, itemKey, cocart.UpdateItemParams{Quantity: 3})

// Remove item
err = client.Cart().RemoveItem(ctx, itemKey)

// Apply coupon
err = client.Cart().ApplyCoupon(ctx, "SAVE10")

// Clear cart
err = client.Cart().Clear(ctx)
```

---

## Products API

```go
ctx := context.Background()

// List products
products, err := client.Products().All(ctx, &cocart.ProductsParams{
    PerPage: 12,
    Page:    1,
})

// Single product
product, err := client.Products().Get(ctx, 42)

// Search
results, err := client.Products().All(ctx, &cocart.ProductsParams{
    Search: "hoodie",
})

// Filter by category
byCategory, err := client.Products().All(ctx, &cocart.ProductsParams{
    Category: "23",
})
```

---

## Pagination (range-over-func, Go 1.23+)

```go
ctx := context.Background()

// Iterate all products across all pages
for page, err := range client.Products().Paginate(ctx, &cocart.ProductsParams{PerPage: 20}) {
    if err != nil {
        log.Fatal(err)
    }
    for _, product := range page {
        fmt.Println(product["name"])
    }
}
```

---

## Typed Response Deserialization

```go
type Product struct {
    ID   int    `json:"id"`
    Name string `json:"name"`
}

var products []Product
resp, err := client.Products().All(ctx, nil)
if err != nil {
    log.Fatal(err)
}
if err := resp.Unmarshal(&products); err != nil {
    log.Fatal(err)
}
```

---

## Sessions API (admin)

```go
admin := cocart.NewClient(
    "https://your-store.com",
    cocart.WithWooCommerceKeys("ck_xxxxx", "cs_xxxxx"),
)
ctx := context.Background()

// List all active sessions
sessions, err := admin.Sessions().All(ctx, nil)

// Get a specific session
session, err := admin.Sessions().Get(ctx, cartKey)

// Delete a session
err = admin.Sessions().Delete(ctx, cartKey)
```

---

## Event System

```go
client.On(cocart.EventRequest, func(e cocart.Event) {
    fmt.Printf("→ %s %s\n", e.Method, e.URL)
})
client.On(cocart.EventResponse, func(e cocart.Event) {
    fmt.Printf("← %d in %dms\n", e.StatusCode, e.Duration.Milliseconds())
})
client.On(cocart.EventError, func(e cocart.Event) {
    fmt.Printf("✗ %v\n", e.Error)
})
```

---

## Currency Formatting

```go
cart, err := client.Cart().Get(ctx, nil)
if err != nil {
    log.Fatal(err)
}

currency := cart.GetCurrency()
fmt.Println(cocart.FormatCurrency(4599, currency))        // "$45.99"
fmt.Println(cocart.FormatCurrencyDecimal(4599, currency)) // "45.99"
```

---

## Error Handling

```go
import "github.com/cocart-headless/cocart-sdk-go/errors"

_, err := client.Cart().AddItem(ctx, -1, 0)
if err != nil {
    switch {
    case errors.IsValidation(err):
        fmt.Println("Validation:", err)
    case errors.IsAuthentication(err):
        fmt.Println("Auth:", err)
    case errors.IsNetwork(err):
        fmt.Println("Network:", err)
    default:
        fmt.Println("API error:", err)
    }
}
```

---

## HTTP Server Example (net/http)

```go
package main

import (
    "encoding/json"
    "net/http"

    cocart "github.com/cocart-headless/cocart-sdk-go"
)

func cartHandler(w http.ResponseWriter, r *http.Request) {
    // Extract cart key from header (passed by the browser client)
    cartKey := r.Header.Get("X-CoCart-Key")

    client := cocart.NewClient(
        "https://your-store.com",
        cocart.WithCartKey(cartKey),
    )

    cart, err := client.Cart().Get(r.Context(), nil)
    if err != nil {
        http.Error(w, err.Error(), http.StatusInternalServerError)
        return
    }

    w.Header().Set("Content-Type", "application/json")
    json.NewEncoder(w).Encode(cart.ToMap())
}
```

---

## Further Reading

- [Configuration & Setup](https://github.com/cocart-headless/cocart-go/blob/main/docs/installation.md)
- [Authentication Guide](https://github.com/cocart-headless/cocart-go/blob/main/docs/authentication.md)
- [Cart API](https://github.com/cocart-headless/cocart-go/blob/main/docs/cart.md)
- [Products API](https://github.com/cocart-headless/cocart-go/blob/main/docs/products.md)
- [Store API](https://github.com/cocart-headless/cocart-go/blob/main/docs/store.md)
- [Sessions API](https://github.com/cocart-headless/cocart-go/blob/main/docs/sessions.md)
- [Error Handling](https://github.com/cocart-headless/cocart-go/blob/main/docs/error-handling.md)
- [Utilities](https://github.com/cocart-headless/cocart-go/blob/main/docs/utilities.md)
- [Examples](https://github.com/cocart-headless/cocart-go/tree/main/examples)
