# Products API

## Querying Products

```php
// List products
$products = $client->products()->all([
    'per_page' => 12,
    'page'     => 1,
]);

// Single product
$product = $client->products()->get(productId: 42);

// Search
$results = $client->products()->all(['search' => 'hoodie']);

// Filter by category
$byCategory = $client->products()->all(['category' => 23]);
```
