# Cart Operations

## Common Operations

```ts
const client = new CoCart('https://your-store.com');

// Add item
await client.cart().addItem(productId, quantity);

// Add item with variation
await client.cart().addItem(productId, 1, { variation_id: 456 });

// Get cart
const cart = await client.cart().get();
cart.getItems();                  // CartItem[]
cart.get('totals.total');         // dot-notation access
cart.get('currency.currency_code');

// Type-safe field filtering (return type narrows automatically)
const filtered = await client.cart().getFiltered(['items', 'totals']);
const data = filtered.toObject(); // only { items, totals } typed

// Update item quantity
await client.cart().updateItem(itemKey, { quantity: 3 });

// Remove item
await client.cart().removeItem(itemKey);

// Apply coupon
await client.cart().applyCoupon('SAVE10');

// Clear cart
await client.cart().clear();
```

## Currency Formatting

```ts
import { CurrencyFormatter } from '@cocartheadless/sdk';

const fmt = new CurrencyFormatter();
const cart = await client.cart().get();
const currency = cart.getCurrency();

fmt.format(4599, currency);        // "$45.99"
fmt.formatDecimal(4599, currency); // "45.99"
```

## Event System

```ts
client.on('request', (e) => console.log(`→ ${e.method} ${e.url}`));
client.on('response', (e) => console.log(`← ${e.status} in ${e.duration}ms`));
client.on('error', (e) => console.error('CoCart error:', e.error));
```

## Error Handling

```ts
import { CoCartError, ValidationError, NetworkError } from '@cocartheadless/sdk';

try {
  await client.cart().addItem(-1, 0);
} catch (err) {
  if (err instanceof ValidationError) {
    // Caught client-side before any network request
    console.error('Validation:', err.message);
  } else if (err instanceof NetworkError) {
    console.error('Network:', err.message);
  } else if (err instanceof CoCartError) {
    console.error('API:', err.message, err.code);
  }
}
```
