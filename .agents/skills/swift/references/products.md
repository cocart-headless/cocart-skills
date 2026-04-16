# Products API

## Querying Products

```swift
// List products
let products = try await client.products().all(perPage: 12, page: 1)

// Single product
let product = try await client.products().get(productId: 42)

// Search
let results = try await client.products().all(search: "hoodie")

// Filter by category
let byCategory = try await client.products().all(category: "23")
```

## Further Reading

- [SDK Repository](https://github.com/cocart-headless/cocart-swift-sdk)
- [CoCart Docs](https://docs.cocartapi.com/)
- [OpenAPI Spec](https://github.com/cocart-headless/cocart-openapi)
