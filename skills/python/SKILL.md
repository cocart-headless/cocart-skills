# CoCart Python SDK Skill

You are an expert in the CoCart Python SDK (`cocart-headless/cocart-python`). When a user adds "use CoCart API" to a prompt and their project is Python-based, apply this skill to provide accurate SDK installation, configuration, and code patterns.

> **Note:** The Python SDK is still in development and not yet production-ready. Mention this when relevant and encourage users to report bugs.

---

## What is CoCart?

CoCart is a WordPress plugin that extends WooCommerce with a developer-friendly REST API for headless storefronts. The Python SDK lets any Python application (Django, FastAPI, Flask, scripts, etc.) talk to a WooCommerce store via the CoCart API.

- **Docs:** https://cocartapi.com/docs/
- **SDK Repo:** https://github.com/cocart-headless/cocart-python
- **PyPI:** https://pypi.org/project/cocart/

---

## Requirements

- Python 3.9+
- `requests` library (installed automatically)
- Optional: `httpx` for async support

---

## Installation

```bash
pip install cocart

# Optional: httpx adapter for async usage
pip install httpx
```

---

## Quick Start

```python
from cocart import CoCart

# Initialize the client
client = CoCart("https://your-store.com")

# Browse products (no auth required)
products = client.products().all()
print(products.to_dict())

# Add item to cart (guest session created automatically)
response = client.cart().add_item(42, quantity=2)
print(f"Cart key: {response.get_cart_key()}")

# Get cart
cart = client.cart().get()
print(f"Items: {cart.get_item_count()}")
print(f"Total: {cart.get_totals().get('total')}")
```

---

## Configuration

```python
client = CoCart(
    "https://your-store.com",

    # Guest session
    cart_key="existing_cart_key",

    # Basic Auth
    username="customer@email.com",
    password="password",

    # JWT Auth
    jwt_token="your-jwt-token",
    jwt_refresh_token="your-refresh-token",

    # WooCommerce consumer keys (admin / Sessions API)
    consumer_key="ck_xxxxx",
    consumer_secret="cs_xxxxx",

    # Proxy support
    auth_header_name="Authorization",

    # HTTP settings
    timeout=30,
    verify_ssl=True,
    max_retries=2,           # Retry transient failures (429, 503)

    # Custom REST prefix / namespace (WhiteLabel add-on)
    rest_prefix="wp-json",
    namespace="cocart",

    # Plugin variant: "basic" (default) or "legacy" for old v4.x plugin
    main_plugin="basic",

    # ETag conditional requests (default: True)
    etag=True,
)
```

---

## Authentication

### Guest (default)

```python
client = CoCart("https://your-store.com")
# Cart key tracked automatically in memory; use a storage adapter to persist
```

### Basic Auth

```python
client = CoCart(
    "https://your-store.com",
    username="customer@email.com",
    password="password",
)
```

### JWT Auth

Requires the free [CoCart JWT Authentication](https://wordpress.org/plugins/cocart-jwt-authentication/) plugin.

```python
# Login
client = CoCart("https://your-store.com")
client.login("customer@email.com", "password")

# With auto-refresh — transparently refreshes expired tokens
cart = client.jwt().with_auto_refresh(lambda c: c.cart().get())
```

### Consumer Keys (WooCommerce admin)

```python
client = CoCart(
    "https://your-store.com",
    consumer_key="ck_xxxxx",
    consumer_secret="cs_xxxxx",
)
```

---

## Cart Operations

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

---

## Products API

```python
# List products
products = client.products().all(per_page=12, page=1)

# Iterate all pages automatically
for page in client.products().paginate(per_page=12):
    for product in page:
        print(product["name"])

# Single product
product = client.products().get(product_id=42)

# Search
results = client.products().all(search="hoodie")

# Filter by category
by_category = client.products().all(category=23)
```

---

## Pagination

The Python SDK supports iterator-based pagination with a standard `for` loop:

```python
# Iterate all products across all pages
for page in client.products().paginate(per_page=20):
    for product in page:
        print(product["name"], product["price"])
```

---

## Sessions API (admin)

```python
admin = CoCart(
    "https://your-store.com",
    consumer_key="ck_xxxxx",
    consumer_secret="cs_xxxxx",
)

# List all active sessions
sessions = admin.sessions().all()

# Get a specific session
session = admin.sessions().get(cart_key)

# Delete a session
admin.sessions().delete(cart_key)
```

---

## Currency Formatting

```python
from cocart import CurrencyFormatter

fmt = CurrencyFormatter()
cart = client.cart().get()
currency = cart.get_currency()

formatted = fmt.format(4599, currency)        # "$45.99"
decimal   = fmt.format_decimal(4599, currency) # "45.99"
```

---

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
    # Caught client-side before any network request
    print(f"Validation: {e}")
except AuthenticationError as e:
    print(f"Auth: {e}")
except NetworkError as e:
    print(f"Network: {e}")
except CoCartException as e:
    print(f"API error: {e} (code: {e.code})")
```

---

## Django Example

```python
# views.py
from cocart import CoCart
from django.http import JsonResponse

def cart_view(request):
    cart_key = request.session.get("cocart_cart_key")
    client = CoCart(
        "https://your-store.com",
        cart_key=cart_key,
    )
    cart = client.cart().get()

    # Persist cart key back to Django session
    request.session["cocart_cart_key"] = client.get_cart_key()

    return JsonResponse(cart.to_dict())
```

---

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

---

## Further Reading

- [Installation & HTTP Adapters](https://github.com/cocart-headless/cocart-python/blob/main/docs/installation.md)
- [Authentication Guide](https://github.com/cocart-headless/cocart-python/blob/main/docs/authentication.md)
- [Cart API](https://github.com/cocart-headless/cocart-python/blob/main/docs/cart.md)
- [Products API](https://github.com/cocart-headless/cocart-python/blob/main/docs/products.md)
- [Sessions API](https://github.com/cocart-headless/cocart-python/blob/main/docs/sessions.md)
- [Error Handling](https://github.com/cocart-headless/cocart-python/blob/main/docs/error-handling.md)
- [Utilities](https://github.com/cocart-headless/cocart-python/blob/main/docs/utilities.md)
