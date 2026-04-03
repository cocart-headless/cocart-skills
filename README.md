# CoCart API — Claude Code Plugin

Official [Claude Code](https://claude.ai/code) plugin for [CoCart](https://cocartapi.com) — the headless WooCommerce REST API.

Add `use CoCart API` to any prompt in Claude Code to get accurate SDK installation, adapter configuration, and code patterns for your framework.

## How to Use

After installing, just append `use CoCart API` to any prompt:

```
Set up CoCart in my Next.js App Router project. use CoCart API
```

```
Add a cart page to my Astro site with CoCart. use CoCart API
```

```
Install the CoCart SDK and show me how to list products and handle auth. use CoCart API
```

Claude will automatically apply the correct SDK, adapter, and patterns for your framework.

## Install

### Option 1 — From the Anthropic Marketplace

```
/plugin install cocart-api
```

### Option 2 — From this GitHub repo directly

```
/plugin marketplace add cocart-headless/claude-plugin
/plugin install cocart-api@cocart-headless
```

## What It Covers

- SDK installation for all package managers (npm, yarn, pnpm, bun, CDN)
- Framework adapters: **Astro, Next.js, Nuxt, SvelteKit, Remix, Hono, Elysia.js, Fastify, Deno, Framer, Webflow**
- Authentication: guest sessions, Basic Auth, consumer keys, JWT with auto-refresh
- Cart operations: add, update, remove, coupons, clear
- Products API: list, filter, search
- Session and encrypted localStorage setup
- Environment variable patterns
- Error handling
- Links to full docs and OpenAPI spec

## SDKs

| Language | Package |
|---|---|
| TypeScript / JavaScript | [`@cocartheadless/sdk`](https://github.com/cocart-headless/cocart-js) |
| PHP | [`cocart-headless/cocart-php`](https://github.com/cocart-headless/cocart-php) |
| Python | [`cocart-headless/cocart-python`](https://github.com/cocart-headless/cocart-python) |
| Go | [`cocart-headless/cocart-go`](https://github.com/cocart-headless/cocart-go) |

## Links

- [CoCart Docs](https://cocartapi.com/docs/)
- [OpenAPI Spec](https://github.com/cocart-headless/cocart-openapi)
- [GitHub Organisation](https://github.com/cocart-headless)
- [Community Discord](https://cocartapi.com/community/)

## License

MIT
