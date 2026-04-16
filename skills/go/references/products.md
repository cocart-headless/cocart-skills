# Products API

## Querying Products

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

## Pagination (range-over-func, Go 1.23+)

```go
ctx := context.Background()

for page, err := range client.Products().Paginate(ctx, &cocart.ProductsParams{PerPage: 20}) {
    if err != nil {
        log.Fatal(err)
    }
    for _, product := range page {
        fmt.Println(product["name"])
    }
}
```

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

## Further Reading

- [Configuration & Setup](https://github.com/cocart-headless/cocart-go/blob/main/docs/installation.md)
- [Authentication Guide](https://github.com/cocart-headless/cocart-go/blob/main/docs/authentication.md)
- [Cart API](https://github.com/cocart-headless/cocart-go/blob/main/docs/cart.md)
- [Products API](https://github.com/cocart-headless/cocart-go/blob/main/docs/products.md)
- [Store API](https://github.com/cocart-headless/cocart-go/blob/main/docs/store.md)
- [Sessions API](https://github.com/cocart-headless/cocart-go/blob/main/docs/sessions.md)
- [Error Handling](https://github.com/cocart-headless/cocart-go/blob/main/docs/error-handling.md)
- [Examples](https://github.com/cocart-headless/cocart-go/tree/main/examples)
