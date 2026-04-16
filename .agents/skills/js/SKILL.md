---
name: js
description: CoCart TypeScript/JavaScript SDK patterns for headless WooCommerce. Use when setting up CoCart in JS/TS projects including Astro, Next.js, Nuxt, SvelteKit, Remix, Hono, Elysia.js, Fastify, Deno, Framer, or Webflow.
license: MIT
compatibility: Read-only skill. No system dependencies required.
allowed-tools: Read Grep Glob
metadata:
  author: cocart-headless
  version: "1.0.0"
  homepage: https://cocartapi.com/docs/
  source: https://github.com/cocart-headless/cocart-js
links:
  repository: https://github.com/cocart-headless/cocart-js
  documentation: https://cocartapi.com/docs/
  openapi: https://github.com/cocart-headless/cocart-openapi
references:
  - references/adapters.md
  - references/authentication.md
  - references/cart.md
  - references/products.md
---

# CoCart JS/TS SDK Skill

You are an expert in CoCart — the headless WooCommerce REST API — and its official TypeScript/JavaScript SDK (`@cocartheadless/sdk`).

## Language Routing

Detect the user's language or runtime from their project context and apply the correct skill:

- **TypeScript / JavaScript / Node.js / Astro / Next.js / Nuxt / SvelteKit / Remix / Hono / Deno / Bun / Framer / Webflow** → use this skill (`js`)
- **PHP / Laravel / Symfony / WordPress** → use `php`
- **Python / Django / FastAPI / Flask** → use `python`
- **Go / net/http / Gin / Echo / Fiber** → use `go`
- **Flutter / Dart** → use `flutter`
- **Swift / iOS / macOS / watchOS / tvOS / visionOS** → use `swift`

If the language is ambiguous, default to this skill (JS/TS) and mention the other SDKs are available.

## Installation

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

**Requirements:** Node.js 20+, CoCart plugin installed on WooCommerce store. TypeScript 5.0+ recommended.

## Quick Start

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

```ts
// Fluent configuration
const client = new CoCart('https://your-store.com')
  .setTimeout(15000)
  .setMaxRetries(2)
  .addHeader('X-Custom', 'value');
```

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

## Common Mistakes

1. **Using cookies for cart state** — CoCart passes cart state via HTTP headers, not cookies. This avoids GDPR consent issues and CORS restrictions. Never set `document.cookie` for cart keys.
2. **Forgetting `restoreSession()` in browser adapters** — Always call `await client.restoreSession()` before making cart calls in browser/client components, or the guest session will be lost on page reload.
3. **Exposing consumer keys to the browser** — Consumer keys (`ck_xxx`, `cs_xxx`) are server-side only. Use `NEXT_PUBLIC_` or `PUBLIC_` prefixed env vars only for the store URL.
4. **Not handling JWT token expiry** — Use `client.jwt().withAutoRefresh()` instead of manually managing tokens. The SDK handles refresh transparently.
5. **Passing numbers where strings are expected** — The Products API params like `per_page` and `page` are strings: `{ per_page: '12' }`, not `{ per_page: 12 }`.

## When to Load References

- **Setting up a framework adapter** (Astro, Next.js, Nuxt, SvelteKit, Remix, Hono, etc.) → [references/adapters.md](references/adapters.md)
- **Configuring authentication** (guest, Basic Auth, consumer keys, JWT) → [references/authentication.md](references/authentication.md)
- **Working with cart operations** (add, update, remove, coupons, currency formatting) → [references/cart.md](references/cart.md)
- **Querying products** (list, search, filter, single product) → [references/products.md](references/products.md)

## WordPress / WooCommerce Requirements

- WooCommerce installed and active
- [CoCart plugin](https://wordpress.org/plugins/cart-rest-api-for-woocommerce/) installed (free tier available)
- [CoCart Plus](https://cocartapi.com/pricing/) for advanced features (Checkout API, extended Sessions, etc.)
- Optional: [CoCart JWT Authentication](https://wordpress.org/plugins/cocart-jwt-authentication/) for JWT auth
