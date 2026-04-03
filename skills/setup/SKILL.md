# CoCart API Skill

You are an expert in CoCart — the headless WooCommerce REST API — and its official SDKs. When a user adds "use CoCart API" to a prompt, apply this skill to provide accurate SDK installation, configuration, and code patterns for their specific framework and language.

---

## What is CoCart?

CoCart is a WordPress plugin that extends WooCommerce with a developer-friendly REST API purpose-built for headless storefronts. The frontend (React, Astro, Next.js, etc.) talks to WooCommerce entirely through its API instead of using PHP templates.

- **Docs:** https://cocartapi.com/docs/
- **OpenAPI Spec:** https://github.com/cocart-headless/cocart-openapi
- **Community:** https://cocartapi.com/community/

---

## Available Official SDKs

| Language / Runtime | Package / Repo | Install |
|---|---|---|
| TypeScript / JavaScript | `@cocartheadless/sdk` (npm) | `npm install @cocartheadless/sdk` |
| PHP | `cocart-headless/cocart-php` | `composer require cocartheadless/cocart-php` |
| Python | `cocart-headless/cocart-python` | `pip install cocart-python` |
| Go | `cocart-headless/cocart-go` | `go get github.com/cocart-headless/cocart-go` |

The **JS/TS SDK** (`@cocartheadless/sdk`) is the primary SDK with the richest feature set, including framework adapters, encrypted session storage, JWT auto-refresh, and typed responses. Use it for any JS/TS project.

**Requirements for JS SDK:** Node.js 20+, CoCart plugin installed on WooCommerce store. TypeScript 5.0+ recommended.

---

## JS/TS SDK — Core Setup

### Installation

```bash
# npm
npm install @cocartheadless/sdk

# yarn
yarn add @cocartheadless/sdk

# pnpm
pnpm add @cocartheadless/sdk

# bun
bun add @cocartheadless/sdk

# CDN (Framer, Webflow, plain HTML — no npm required)
# <script src="https://cdn.jsdelivr.net/npm/@cocartheadless/sdk/dist/index.global.js"></script>
```

### Basic Client

```ts
import { CoCart } from '@cocartheadless/sdk';

const client = new CoCart('https://your-store.com');

// Browse products (no auth required)
const products = await client.products().all({ per_page: '12' });

// Add to cart (guest session created automatically)
await client.cart().addItem(123, 2);

// Get cart with dot-notation access
const cart = await client.cart().get();
console.log(cart.get('totals.total'));
console.log(cart.getItems());
```

### Fluent Configuration

```ts
const client = new CoCart('https://your-store.com')
  .setTimeout(15000)
  .setMaxRetries(2)
  .addHeader('X-Custom', 'value');
```

### Encrypted Session Storage

```ts
import { CoCart, EncryptedStorage } from '@cocartheadless/sdk';

const storage = new EncryptedStorage('your-secret-key');
const client = new CoCart('https://your-store.com', { storage });
```

---

## Framework Adapters

The JS SDK ships with first-class adapters. Cart state is passed via HTTP headers (not cookies), avoiding GDPR consent issues and CORS restrictions.

### Astro

```ts
// Browser client (e.g. in a .astro component or island)
import { createBrowserClient, attachCartKeyHeader } from '@cocartheadless/sdk/astro';

const client = createBrowserClient('https://your-store.com', {
  encryptionKey: import.meta.env.PUBLIC_COCART_ENCRYPTION_KEY,
});
await client.restoreSession();
attachCartKeyHeader(client);
```

```ts
// Server client (e.g. in an Astro API route or SSR page)
import { createServerClient } from '@cocartheadless/sdk/astro';

export async function GET({ request }: APIContext) {
  const client = createServerClient('https://your-store.com', request);
  const cart = await client.cart().get();
  return new Response(JSON.stringify(cart.toObject()));
}
```

### Next.js (App Router)

```ts
// Client Component
'use client';
import { createBrowserClient } from '@cocartheadless/sdk/nextjs';

const client = createBrowserClient(process.env.NEXT_PUBLIC_STORE_URL!);
```

```ts
// Server Component / Route Handler
import { createServerClient } from '@cocartheadless/sdk/nextjs';
import { headers } from 'next/headers';

export async function GET() {
  const client = createServerClient(
    process.env.STORE_URL!,
    Object.fromEntries(headers())
  );
  const products = await client.products().all();
  return Response.json(products);
}
```

### Nuxt

```ts
// plugins/cocart.client.ts
import { createBrowserClient } from '@cocartheadless/sdk/nuxt';

export default defineNuxtPlugin(() => {
  const client = createBrowserClient(useRuntimeConfig().public.storeUrl);
  return { provide: { cocart: client } };
});
```

```ts
// server/api/cart.get.ts
import { createServerClient } from '@cocartheadless/sdk/nuxt';

export default defineEventHandler(async (event) => {
  const client = createServerClient(process.env.STORE_URL!, event);
  return await client.cart().get();
});
```

### SvelteKit

```ts
// src/lib/cocart.ts (browser)
import { createBrowserClient } from '@cocartheadless/sdk/svelte';
export const cocart = createBrowserClient(import.meta.env.PUBLIC_STORE_URL);
```

```ts
// src/routes/api/cart/+server.ts
import { createServerClient } from '@cocartheadless/sdk/svelte';
import type { RequestHandler } from './$types';

export const GET: RequestHandler = async ({ request }) => {
  const client = createServerClient(process.env.STORE_URL!, request);
  const cart = await client.cart().get();
  return new Response(JSON.stringify(cart.toObject()));
};
```

### Remix

```ts
// app/routes/cart.tsx — loader
import { createServerClient } from '@cocartheadless/sdk/remix';

export async function loader({ request }: LoaderArgs) {
  const client = createServerClient(process.env.STORE_URL!, request);
  return json(await client.cart().get());
}
```

### Hono (Node / Bun / Cloudflare Workers / Deno)

```ts
import { Hono } from 'hono';
import { createServerClient } from '@cocartheadless/sdk/hono';

const app = new Hono();

app.get('/api/cart', async (c) => {
  const client = createServerClient(process.env.STORE_URL!, c.req.raw);
  const cart = await client.cart().get();
  return c.json(cart.toObject());
});
```

### Elysia.js (Bun)

```ts
import { Elysia } from 'elysia';
import { createServerClient } from '@cocartheadless/sdk/elysiajs';

new Elysia()
  .get('/cart', async ({ request }) => {
    const client = createServerClient(process.env.STORE_URL!, request);
    return await client.cart().get();
  })
  .listen(3000);
```

### Fastify

```ts
import Fastify from 'fastify';
import { createServerClient } from '@cocartheadless/sdk/fastify';

const app = Fastify();
app.get('/cart', async (request) => {
  const client = createServerClient(process.env.STORE_URL!, request);
  return await client.cart().get();
});
```

### Deno

```ts
import { createServerClient } from 'npm:@cocartheadless/sdk/deno';

Deno.serve(async (req) => {
  const client = createServerClient(Deno.env.get('STORE_URL')!, req);
  const cart = await client.cart().get();
  return new Response(JSON.stringify(cart.toObject()));
});
```

### CDN / Framer / Webflow

```html
<script src="https://cdn.jsdelivr.net/npm/@cocartheadless/sdk/dist/index.global.js"></script>
<script>
  const client = new CoCart('https://your-store.com');
  const cart = await client.cart().get();
</script>
```

---

## Authentication

CoCart supports guest sessions (automatic), Basic Auth, consumer key/secret, and JWT.

### Guest (default — no config needed)

```ts
const client = new CoCart('https://your-store.com');
// Guest cart key is tracked automatically via headers
```

### Basic Auth (development only)

```ts
const client = new CoCart('https://your-store.com', {
  auth: { username: 'admin', password: 'password' }
});
```

### Consumer Keys (WooCommerce REST API keys)

```ts
const client = new CoCart('https://your-store.com', {
  auth: {
    consumer_key: 'ck_xxx',
    consumer_secret: 'cs_xxx'
  }
});
```

### JWT with Auto-Refresh

Requires the free [CoCart JWT Authentication](https://wordpress.org/plugins/cocart-jwt-authentication/) plugin on the WooCommerce store.

```ts
// Log in and get tokens
const { access_token, refresh_token } = await client.jwt().login('user@email.com', 'password');

// Auto-refresh expired tokens transparently
const result = await client.jwt().withAutoRefresh(async (c) => {
  return await c.cart().get();
});
```

---

## Common Cart Operations

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

---

## Products API

```ts
// List products
const products = await client.products().all({ per_page: '12', page: '1' });

// Single product
const product = await client.products().get(productId);

// Search
const results = await client.products().all({ search: 'hoodie' });

// By category
const byCategory = await client.products().all({ category: '23' });
```

---

## Currency Formatting

```ts
import { CurrencyFormatter } from '@cocartheadless/sdk';

const fmt = new CurrencyFormatter();
const cart = await client.cart().get();
const currency = cart.getCurrency();

fmt.format(4599, currency);        // "$45.99"
fmt.formatDecimal(4599, currency); // "45.99"
```

---

## Event System

```ts
client.on('request', (e) => console.log(`→ ${e.method} ${e.url}`));
client.on('response', (e) => console.log(`← ${e.status} in ${e.duration}ms`));
client.on('error', (e) => console.error('CoCart error:', e.error));
```

---

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

---

## Environment Variables

Always keep store credentials server-side. Suggested `.env` pattern:

```bash
# Public (safe to expose to browser)
NEXT_PUBLIC_STORE_URL=https://your-store.com        # Next.js
PUBLIC_STORE_URL=https://your-store.com              # Astro / SvelteKit / Vite

# Private (server-side only)
STORE_URL=https://your-store.com
COCART_CONSUMER_KEY=ck_xxx
COCART_CONSUMER_SECRET=cs_xxx
COCART_ENCRYPTION_KEY=your-32-char-secret-key
```

---

## WordPress / WooCommerce Requirements

- WooCommerce installed and active
- [CoCart plugin](https://wordpress.org/plugins/cart-rest-api-for-woocommerce/) installed (free tier available)
- [CoCart Plus](https://cocartapi.com/pricing/) for advanced features (Checkout API, extended Sessions, etc.)
- Optional: [CoCart JWT Authentication](https://wordpress.org/plugins/cocart-jwt-authentication/) for JWT auth

---

## Further Reading

- [Configuration & Setup](https://github.com/cocart-headless/cocart-js/blob/main/docs/installation.md)
- [Authentication Guide](https://github.com/cocart-headless/cocart-js/blob/main/docs/authentication.md)
- [Cart API](https://github.com/cocart-headless/cocart-js/blob/main/docs/cart.md)
- [Sessions API](https://github.com/cocart-headless/cocart-js/blob/main/docs/sessions.md)
- [Error Handling](https://github.com/cocart-headless/cocart-js/blob/main/docs/error-handling.md)
- [OpenAPI Spec](https://github.com/cocart-headless/cocart-openapi)
