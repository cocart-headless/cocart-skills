# Authentication

CoCart supports guest sessions (automatic), Basic Auth, consumer key/secret, and JWT.

## Guest (default — no config needed)

```ts
const client = new CoCart('https://your-store.com');
// Guest cart key is tracked automatically via headers
```

## Basic Auth (development only)

```ts
const client = new CoCart('https://your-store.com', {
  auth: { username: 'admin', password: 'password' }
});
```

## JWT with Auto-Refresh

Requires the free [CoCart JWT Authentication](https://wordpress.org/plugins/cocart-jwt-authentication/) plugin on the WooCommerce store.

```ts
// Log in and get tokens
const { access_token, refresh_token } = await client.jwt().login('user@email.com', 'password');

// Auto-refresh expired tokens transparently
const result = await client.jwt().withAutoRefresh(async (c) => {
  return await c.cart().get();
});
```

## Consumer Keys (WooCommerce REST API keys)

```ts
const client = new CoCart('https://your-store.com', {
  auth: {
    consumer_key: 'ck_xxx',
    consumer_secret: 'cs_xxx'
  }
});
```
