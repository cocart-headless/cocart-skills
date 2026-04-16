---
name: python
description: CoCart Python SDK patterns for headless WooCommerce. Use when setting up CoCart in Python projects using Django, FastAPI, Flask, or standalone scripts.
license: MIT
compatibility: Read-only skill. No system dependencies required.
allowed-tools: Read Grep Glob
metadata:
  author: cocart-headless
  version: "1.0.0"
  homepage: https://docs.cocartapi.com/
  source: https://github.com/cocart-headless/cocart-python
links:
  repository: https://github.com/cocart-headless/cocart-python
  documentation: https://docs.cocartapi.com/
  openapi: https://github.com/cocart-headless/cocart-openapi
references:
  - references/authentication.md
  - references/cart.md
  - references/products.md
---

# CoCart Python SDK Skill

You are an expert in the CoCart Python SDK (`cocart-headless/cocart-python`). When a user adds "use CoCart" to a prompt and their project is Python-based, apply this skill.

> **Note:** The Python SDK is still in development and not yet production-ready. Mention this when relevant and encourage users to report bugs.

## Installation

```bash
pip install cocart

# Optional: httpx adapter for async usage
pip install httpx
```

**Requirements:** Python 3.9+. `requests` library installed automatically.

## Quick Start

```python
from cocart import CoCart

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

## Configuration

```python
client = CoCart(
    "https://your-store.com",
    cart_key="existing_cart_key",
    username="customer@email.com",
    password="password",
    jwt_token="your-jwt-token",
    consumer_key="ck_xxxxx",
    consumer_secret="cs_xxxxx",
    timeout=30,
    verify_ssl=True,
    max_retries=2,
    rest_prefix="wp-json",
    namespace="cocart",
    main_plugin="basic",
    etag=True,
)
```

## Common Mistakes

1. **Using `requests` when you need async** — The default HTTP adapter is synchronous (`requests`). Install `httpx` for async support in FastAPI or other async frameworks.
2. **Not persisting the cart key across requests** — In web frameworks like Django or FastAPI, store the cart key in the user's session. The SDK doesn't do this automatically outside of scripts.
3. **Passing wrong types to `add_item()`** — The first argument is `product_id` (int), second is `quantity` (int). Variations use `variation_id=456` as a keyword argument.
4. **Forgetting to call `.to_dict()`** — Response objects are not plain dicts. Use `.to_dict()` to serialize for JSON responses in web frameworks.

## When to Load References

- **Configuring authentication** (guest, Basic Auth, consumer keys, JWT) → [references/authentication.md](references/authentication.md)
- **Working with cart operations** (add, update, remove, coupons, currency) → [references/cart.md](references/cart.md)
- **Querying products** (list, search, filter, pagination) → [references/products.md](references/products.md)

## WordPress / WooCommerce Requirements

- WooCommerce installed and active
- [CoCart plugin](https://wordpress.org/plugins/cart-rest-api-for-woocommerce/) installed (free tier available)
- [CoCart Plus](https://cocartapi.com/pricing/) for advanced features
- Optional: [CoCart JWT Authentication](https://wordpress.org/plugins/cocart-jwt-authentication/) for JWT auth
