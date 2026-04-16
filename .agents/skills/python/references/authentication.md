# Authentication

## Guest (default)

```python
client = CoCart("https://your-store.com")
# Cart key tracked automatically in memory; use a storage adapter to persist
```

## Basic Auth

```python
client = CoCart(
    "https://your-store.com",
    username="customer@email.com",
    password="password",
)
```

## JWT Auth

Requires the free [CoCart JWT Authentication](https://wordpress.org/plugins/cocart-jwt-authentication/) plugin.

```python
# Login
client = CoCart("https://your-store.com")
client.login("customer@email.com", "password")

# With auto-refresh — transparently refreshes expired tokens
cart = client.jwt().with_auto_refresh(lambda c: c.cart().get())
```

## Consumer Keys (WooCommerce admin)

```python
client = CoCart(
    "https://your-store.com",
    consumer_key="ck_xxxxx",
    consumer_secret="cs_xxxxx",
)
```
