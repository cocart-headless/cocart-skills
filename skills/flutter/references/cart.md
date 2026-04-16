# Cart Operations

## Common Operations

```dart
final client = CoCart('https://your-store.com');

// Add item
await client.cart().addItem(productId: 123, quantity: 2);

// Add item with variation
await client.cart().addItem(productId: 123, quantity: 1, variationId: 456);

// Get cart
final cart = await client.cart().get();
print(cart.getItems());
print(cart.get('totals.total'));

// Update item quantity
await client.cart().updateItem(itemKey, quantity: 3);

// Remove item
await client.cart().removeItem(itemKey);

// Apply coupon
await client.cart().applyCoupon('SAVE10');

// Remove coupon
await client.cart().removeCoupon('SAVE10');

// Clear cart
await client.cart().clear();
```

## Currency Formatting

```dart
final formatter = CurrencyFormatter();
final cart = await client.cart().get();
final currency = cart.getCurrency();

print(formatter.format(4599, currency));        // "$45.99"
print(formatter.formatDecimal(4599, currency)); // "45.99"
```

## Sessions API (admin)

```dart
final admin = CoCart(
  'https://your-store.com',
  config: CoCartConfig(
    consumerKey: 'ck_xxxxx',
    consumerSecret: 'cs_xxxxx',
  ),
);

final sessions = await admin.sessions().all();
final session = await admin.sessions().get(cartKey);
await admin.sessions().delete(cartKey);
```

## Error Handling

```dart
import 'package:cocart/exceptions.dart';

try {
  await client.cart().addItem(productId: -1, quantity: 0);
} on ValidationException catch (e) {
  print('Validation: ${e.message}');
} on AuthenticationException catch (e) {
  print('Auth: ${e.message}');
} on NetworkException catch (e) {
  print('Network: ${e.message}');
} on CoCartException catch (e) {
  print('API error: ${e.message} (code: ${e.code})');
}
```

## Flutter Widget Example

```dart
import 'package:flutter/material.dart';
import 'package:cocart/cocart.dart';

class CartPage extends StatefulWidget {
  @override
  State<CartPage> createState() => _CartPageState();
}

class _CartPageState extends State<CartPage> {
  final client = CoCart('https://your-store.com');
  late Future<CartResponse> _cartFuture;

  @override
  void initState() {
    super.initState();
    _cartFuture = client.cart().get();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Cart')),
      body: FutureBuilder<CartResponse>(
        future: _cartFuture,
        builder: (context, snapshot) {
          if (snapshot.connectionState == ConnectionState.waiting) {
            return const Center(child: CircularProgressIndicator());
          }
          if (snapshot.hasError) {
            return Center(child: Text('Error: ${snapshot.error}'));
          }
          final cart = snapshot.data!;
          final items = cart.getItems();
          return ListView.builder(
            itemCount: items.length,
            itemBuilder: (context, index) {
              final item = items[index];
              return ListTile(
                title: Text(item['name'] ?? ''),
                subtitle: Text('Qty: ${item['quantity']}'),
                trailing: Text(item['totals']?['total'] ?? ''),
              );
            },
          );
        },
      ),
    );
  }
}
```

## Environment Variables

```bash
# .env (use with flutter_dotenv)
STORE_URL=https://your-store.com
COCART_CONSUMER_KEY=ck_xxx
COCART_CONSUMER_SECRET=cs_xxx
```

```dart
import 'package:flutter_dotenv/flutter_dotenv.dart';

await dotenv.load();
final client = CoCart(dotenv.env['STORE_URL']!);
```
