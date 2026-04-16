# Cart Operations

## Common Operations

```python
client = CoCart("https://your-store.com")

# Add item
response = client.cart().add_item(product_id=123, quantity=2)

# Add item with variation
response = client.cart().add_item(123, 1, variation_id=456)

# Get cart
cart = client.cart().get()
print(cart.get_item_count())
print(cart.get_totals())

# Update item quantity
client.cart().update_item(item_key, quantity=3)

# Remove item
client.cart().remove_item(item_key)

# Apply coupon
client.cart().apply_coupon("SAVE10")

# Remove coupon
client.cart().remove_coupon("SAVE10")

# Clear cart
client.cart().clear()
```

## Currency Formatting

```python
from cocart import CurrencyFormatter

fmt = CurrencyFormatter()
cart = client.cart().get()
currency = cart.get_currency()

formatted = fmt.format(4599, currency)        # "$45.99"
decimal   = fmt.format_decimal(4599, currency) # "45.99"
```

## Sessions API (admin)

```python
admin = CoCart(
    "https://your-store.com",
    consumer_key="ck_xxxxx",
    consumer_secret="cs_xxxxx",
)

sessions = admin.sessions().all()
session = admin.sessions().get(cart_key)
admin.sessions().delete(cart_key)
```

## Error Handling

```python
from cocart.exceptions import (
    CoCartException,
    ValidationError,
    AuthenticationError,
    NetworkError,
)

try:
    client.cart().add_item(-1, 0)
except ValidationError as e:
    print(f"Validation: {e}")
except AuthenticationError as e:
    print(f"Auth: {e}")
except NetworkError as e:
    print(f"Network: {e}")
except CoCartException as e:
    print(f"API error: {e} (code: {e.code})")
```

## Django Example

```python
# views.py
from cocart import CoCart
from django.http import JsonResponse

def cart_view(request):
    cart_key = request.session.get("cocart_cart_key")
    client = CoCart("https://your-store.com", cart_key=cart_key)
    cart = client.cart().get()
    request.session["cocart_cart_key"] = client.get_cart_key()
    return JsonResponse(cart.to_dict())
```

## FastAPI Example

```python
from fastapi import FastAPI, Request
from cocart import CoCart

app = FastAPI()

@app.get("/cart")
async def get_cart(request: Request):
    cart_key = request.cookies.get("cocart_cart_key")
    client = CoCart("https://your-store.com", cart_key=cart_key)
    cart = client.cart().get()
    return cart.to_dict()
```

## Further Reading

- [Installation & HTTP Adapters](https://github.com/cocart-headless/cocart-python/blob/main/docs/installation.md)
- [Authentication Guide](https://github.com/cocart-headless/cocart-python/blob/main/docs/authentication.md)
- [Cart API](https://github.com/cocart-headless/cocart-python/blob/main/docs/cart.md)
- [Products API](https://github.com/cocart-headless/cocart-python/blob/main/docs/products.md)
- [Sessions API](https://github.com/cocart-headless/cocart-python/blob/main/docs/sessions.md)
- [Error Handling](https://github.com/cocart-headless/cocart-python/blob/main/docs/error-handling.md)
