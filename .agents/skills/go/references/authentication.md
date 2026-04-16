# Authentication

## Guest (default)

```go
client := cocart.NewClient("https://your-store.com")
// Cart key is tracked automatically in memory via MemoryStorage
```

## Basic Auth

```go
client := cocart.NewClient(
    "https://your-store.com",
    cocart.WithBasicAuth("customer@email.com", "password"),
)
```

## JWT Auth with Auto-Refresh

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

## Consumer Keys (admin)

```go
client := cocart.NewClient(
    "https://your-store.com",
    cocart.WithWooCommerceKeys("ck_xxxxx", "cs_xxxxx"),
)
```
