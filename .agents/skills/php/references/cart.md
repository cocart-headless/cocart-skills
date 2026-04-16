# Cart Operations

## Common Operations

```php
$client = new CoCart('https://your-store.com');

// Add item
$client->cart()->addItem(productId: 123, quantity: 2);

// Add item with variation
$client->cart()->addItem(123, 1, ['variation_id' => 456]);

// Get cart
$cart = $client->cart()->get();

// Update item quantity
$client->cart()->updateItem($itemKey, ['quantity' => 3]);

// Remove item
$client->cart()->removeItem($itemKey);

// Apply coupon
$client->cart()->applyCoupon('SAVE10');

// Remove coupon
$client->cart()->removeCoupon('SAVE10');

// Clear cart
$client->cart()->clear();
```

## Batch Requests

```php
// Multiple cart operations in a single HTTP call
$results = $client->batch([
    ['method' => 'POST', 'path' => '/cocart/v2/cart/add-item', 'body' => ['id' => 123, 'quantity' => 1]],
    ['method' => 'POST', 'path' => '/cocart/v2/cart/add-item', 'body' => ['id' => 456, 'quantity' => 2]],
]);
```

## Sessions API (admin)

```php
$admin = new CoCart('https://your-store.com', [
    'consumer_key'    => 'ck_xxxxx',
    'consumer_secret' => 'cs_xxxxx',
]);

$sessions = $admin->sessions()->all();
$session = $admin->sessions()->get($cartKey);
$admin->sessions()->delete($cartKey);
```

## ETag Support

ETags are enabled by default and reduce bandwidth by returning `304 Not Modified` when data hasn't changed:

```php
$client = new CoCart('https://your-store.com', ['etag' => true]);
$cart = $client->cart()->get(); // Full response
$cart = $client->cart()->get(); // 304 if unchanged — uses cached response
```

## Error Handling

```php
use CoCart\SDK\Exceptions\CoCartException;
use CoCart\SDK\Exceptions\ValidationException;
use CoCart\SDK\Exceptions\AuthenticationException;
use CoCart\SDK\Exceptions\NetworkException;

try {
    $client->cart()->addItem(-1, 0);
} catch (ValidationException $e) {
    echo 'Validation error: ' . $e->getMessage();
} catch (AuthenticationException $e) {
    echo 'Auth error: ' . $e->getMessage();
} catch (NetworkException $e) {
    echo 'Network error: ' . $e->getMessage();
} catch (CoCartException $e) {
    echo 'API error: ' . $e->getMessage() . ' (code: ' . $e->getCode() . ')';
}
```

## Further Reading

- [Installation & HTTP Adapters](https://github.com/cocart-headless/cocart-php/blob/main/docs/installation.md)
- [Authentication Guide](https://github.com/cocart-headless/cocart-php/blob/main/docs/authentication.md)
- [Cart API](https://github.com/cocart-headless/cocart-php/blob/main/docs/cart.md)
- [Products API](https://github.com/cocart-headless/cocart-php/blob/main/docs/products.md)
- [Sessions API](https://github.com/cocart-headless/cocart-php/blob/main/docs/sessions.md)
- [Error Handling](https://github.com/cocart-headless/cocart-php/blob/main/docs/error-handling.md)
