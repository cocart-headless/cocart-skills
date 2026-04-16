# Cart Operations

## Common Operations

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

## Sessions API (admin)

```go
admin := cocart.NewClient(
    "https://your-store.com",
    cocart.WithWooCommerceKeys("ck_xxxxx", "cs_xxxxx"),
)
ctx := context.Background()

sessions, err := admin.Sessions().All(ctx, nil)
session, err := admin.Sessions().Get(ctx, cartKey)
err = admin.Sessions().Delete(ctx, cartKey)
```

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

## HTTP Server Example (net/http)

```go
func cartHandler(w http.ResponseWriter, r *http.Request) {
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
