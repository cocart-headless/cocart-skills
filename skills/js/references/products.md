# Products API

## Querying Products

```ts
// List products
const products = await client.products().all({ per_page: '12', page: '1' });

// Single product
const product = await client.products().get(productId);

// Search
const results = await client.products().all({ search: 'hoodie' });

// By category
const byCategory = await client.products().all({ category: '23' });
```

## Further Reading

- [Configuration & Setup](https://github.com/cocart-headless/cocart-js/blob/main/docs/installation.md)
- [Authentication Guide](https://github.com/cocart-headless/cocart-js/blob/main/docs/authentication.md)
- [Cart API](https://github.com/cocart-headless/cocart-js/blob/main/docs/cart.md)
- [Sessions API](https://github.com/cocart-headless/cocart-js/blob/main/docs/sessions.md)
- [Error Handling](https://github.com/cocart-headless/cocart-js/blob/main/docs/error-handling.md)
- [OpenAPI Spec](https://github.com/cocart-headless/cocart-openapi)
