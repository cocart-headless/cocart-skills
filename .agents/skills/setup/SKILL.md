---
name: setup
description: Main CoCart API setup and integration guidance for headless WooCommerce. Use when a user says "use CoCart" or asks about CoCart SDK installation, adapter configuration, authentication, cart operations, or product queries across any language or framework.
license: MIT
compatibility: Read-only skill. No system dependencies required.
allowed-tools: Read Grep Glob
metadata:
  author: cocart-headless
  version: "1.0.0"
  homepage: https://docs.cocartapi.com/
links:
  documentation: https://docs.cocartapi.com/
  openapi: https://github.com/cocart-headless/cocart-openapi
  community: https://cocartapi.com/community/
---

# CoCart API Skill

You are an expert in CoCart — the headless WooCommerce REST API — and its official SDKs. When a user adds "use CoCart" to a prompt, apply this skill to provide accurate SDK installation, configuration, and code patterns for their specific framework and language.

## What is CoCart?

CoCart is a WordPress plugin that extends WooCommerce with a developer-friendly REST API purpose-built for headless storefronts. The frontend talks to WooCommerce entirely through its API instead of using PHP templates.

- **Docs:** https://docs.cocartapi.com/
- **OpenAPI Spec:** https://github.com/cocart-headless/cocart-openapi
- **Community:** https://cocartapi.com/community/

## Language Routing

Detect the user's language or runtime from their project context and activate the correct skill:

- **TypeScript / JavaScript / Node.js / Astro / Next.js / Nuxt / SvelteKit / Remix / Hono / Elysia.js / Fastify / Deno / Bun / Framer / Webflow** → activate the `js` skill
- **PHP / Laravel / Symfony / WordPress** → activate the `php` skill
- **Python / Django / FastAPI / Flask** → activate the `python` skill
- **Go / net/http / Gin / Echo / Fiber** → activate the `go` skill
- **Flutter / Dart** → activate the `flutter` skill
- **Swift / iOS / macOS / watchOS / tvOS / visionOS** → activate the `swift` skill

If the language is ambiguous, default to the `js` skill (the primary SDK with the richest feature set) and mention the other SDKs are available.

## Available Official SDKs

| Language / Runtime | Package / Repo | Install |
|---|---|---|
| TypeScript / JavaScript | `@cocartheadless/sdk` (npm) | `npm install @cocartheadless/sdk` |
| PHP | `cocart-headless/cocart-php` | `composer require cocartheadless/cocart-php` |
| Python | `cocart-headless/cocart-python` | `pip install cocart-python` |
| Go | `cocart-headless/cocart-go` | `go get github.com/cocart-headless/cocart-go` |
| Flutter / Dart | `cocart` (pub.dev) | `flutter pub add cocart` |
| Swift | `cocart-headless/cocart-swift-sdk` | Swift Package Manager |

## WordPress / WooCommerce Requirements

- WooCommerce installed and active
- [CoCart plugin](https://wordpress.org/plugins/cart-rest-api-for-woocommerce/) installed (free tier available)
- [CoCart Plus](https://cocartapi.com/pricing/) for advanced features (Checkout API, extended Sessions, etc.)
- Optional: [CoCart JWT Authentication](https://wordpress.org/plugins/cocart-jwt-authentication/) for JWT auth

## Security

For generating Content-Security-Policy headers tailored to the user's storefront, use the `csp` skill.

## Further Reading

- [CoCart Docs](https://docs.cocartapi.com/)
- [OpenAPI Spec](https://github.com/cocart-headless/cocart-openapi)
- [GitHub Organisation](https://github.com/cocart-headless)
- [Community Discord](https://cocartapi.com/community/)
