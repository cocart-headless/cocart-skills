# Framework Adapters

The JS SDK ships with first-class adapters. Cart state is passed via HTTP headers (not cookies), avoiding GDPR consent issues and CORS restrictions.

## Encrypted Session Storage

```ts
import { CoCart, EncryptedStorage } from '@cocartheadless/sdk';

const storage = new EncryptedStorage('your-secret-key');
const client = new CoCart('https://your-store.com', { storage });
```

## Astro

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

## Next.js (App Router)

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

## Nuxt

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

## SvelteKit

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

## Remix

```ts
// app/routes/cart.tsx — loader
import { createServerClient } from '@cocartheadless/sdk/remix';

export async function loader({ request }: LoaderArgs) {
  const client = createServerClient(process.env.STORE_URL!, request);
  return json(await client.cart().get());
}
```

## Hono (Node / Bun / Cloudflare Workers / Deno)

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

## Elysia.js (Bun)

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

## Fastify

```ts
import Fastify from 'fastify';
import { createServerClient } from '@cocartheadless/sdk/fastify';

const app = Fastify();
app.get('/cart', async (request) => {
  const client = createServerClient(process.env.STORE_URL!, request);
  return await client.cart().get();
});
```

## Deno

```ts
import { createServerClient } from 'npm:@cocartheadless/sdk/deno';

Deno.serve(async (req) => {
  const client = createServerClient(Deno.env.get('STORE_URL')!, req);
  const cart = await client.cart().get();
  return new Response(JSON.stringify(cart.toObject()));
});
```

## CDN / Framer / Webflow

```html
<script src="https://cdn.jsdelivr.net/npm/@cocartheadless/sdk/dist/index.global.js"></script>
<script>
  const client = new CoCart('https://your-store.com');
  const cart = await client.cart().get();
</script>
```
