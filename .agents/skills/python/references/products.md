# Products API

## Querying Products

```python
# List products
products = client.products().all(per_page=12, page=1)

# Single product
product = client.products().get(product_id=42)

# Search
results = client.products().all(search="hoodie")

# Filter by category
by_category = client.products().all(category=23)
```

## Pagination

```python
# Iterate all products across all pages
for page in client.products().paginate(per_page=20):
    for product in page:
        print(product["name"], product["price"])
```
