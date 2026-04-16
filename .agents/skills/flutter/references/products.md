# Products API

## Querying Products

```dart
// List products
final products = await client.products().all(perPage: 12, page: 1);

// Single product
final product = await client.products().get(productId: 42);

// Search
final results = await client.products().all(search: 'hoodie');

// Filter by category
final byCategory = await client.products().all(category: '23');
```

## Further Reading

- [SDK Repository](https://github.com/cocart-headless/cocart-flutter-sdk)
- [CoCart Docs](https://cocartapi.com/docs/)
- [OpenAPI Spec](https://github.com/cocart-headless/cocart-openapi)
