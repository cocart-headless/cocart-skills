---
name: csp
description: Generate Content-Security-Policy headers for headless storefronts using CoCart. Detects the user's framework, payment gateways, and third-party services to produce a tailored CSP.
license: MIT
compatibility: Read-only skill. No system dependencies required.
allowed-tools: Read Grep Glob
metadata:
  author: cocart-headless
  version: "1.0.0"
  homepage: https://docs.cocartapi.com/knowledge-base/http-security-headers
links:
  documentation: https://docs.cocartapi.com/knowledge-base/http-security-headers
---

# CoCart CSP Generator Skill

You are an expert in Content-Security-Policy headers for headless WooCommerce storefronts built with CoCart. When a user asks for help with CSP, security headers, or uses this skill, analyze their project and generate a tailored Content-Security-Policy.

## Workflow

Follow these steps in order:

### 1. Detect the Framework

Scan the project for framework indicators:

| File / Pattern | Framework |
|---|---|
| `next.config.js`, `next.config.mjs`, `next.config.ts` | Next.js |
| `nuxt.config.ts`, `nuxt.config.js` | Nuxt |
| `astro.config.mjs`, `astro.config.ts` | Astro |
| `svelte.config.js` | SvelteKit |
| `remix.config.js`, `app/root.tsx` with `@remix-run` | Remix |
| `src/index.ts` with `hono` import | Hono |
| `src/index.ts` with `elysia` import | Elysia.js |
| `server.js` or `app.js` with `express` import | Express.js |
| `server.js` or `app.js` with `fastify` import | Fastify |
| `vercel.json` | Vercel deployment |
| `netlify.toml` | Netlify deployment |
| `wrangler.toml` | Cloudflare Workers / Pages |

### 2. Identify the CoCart API Domain

Search for the CoCart store URL in:
- Environment files (`.env`, `.env.local`, `.env.production`)
- Configuration files (look for patterns like `COCART_URL`, `STORE_URL`, `WP_URL`, `NEXT_PUBLIC_STORE_URL`, `NUXT_PUBLIC_STORE_URL`)
- SDK initialization code (`new CoCart('https://...')`)

This domain must be added to `connect-src`.

### 3. Identify Payment Gateways

Search project dependencies (`package.json`, import statements) and configuration for payment integrations:

| Service | Detection | CSP Directives |
|---|---|---|
| **Stripe** | `@stripe/stripe-js`, `stripe` in dependencies | `script-src js.stripe.com`; `frame-src js.stripe.com` |
| **PayPal** | `@paypal/react-paypal-js`, `paypal` references | `script-src www.paypal.com`; `frame-src www.paypal.com www.sandbox.paypal.com` |
| **Square** | `@square/web-sdk`, `square` in dependencies | `script-src js.squareup.com`; `frame-src connect.squareup.com` |
| **Braintree** | `braintree-web`, `braintree` in dependencies | `script-src js.braintreegateway.com`; `frame-src assets.braintreegateway.com` |
| **Klarna** | `@klarna/klarna-payment` references | `script-src x.klarnacdn.net`; `frame-src klarna.com *.klarna.com` |
| **Mollie** | `@mollie/mollie-api-node` references | `frame-src js.mollie.com` |
| **Authorize.net** | `authorizenet`, `accept.js` references | `script-src jstest.authorize.net js.authorize.net`; `frame-src jstest.authorize.net js.authorize.net` |

### 4. Identify Third-Party Services

Search for imports, script tags, and configuration referencing common services:

| Service | Detection | CSP Directives |
|---|---|---|
| **Google Fonts** | `fonts.googleapis.com` in CSS/HTML | `style-src fonts.googleapis.com`; `font-src fonts.gstatic.com` |
| **Google Analytics / GTM** | `gtag`, `GA_MEASUREMENT_ID`, `GTM-` references | `script-src www.googletagmanager.com`; `connect-src www.google-analytics.com region1.google-analytics.com` |
| **Google Maps** | `@googlemaps`, `maps.googleapis.com` | `script-src maps.googleapis.com`; `img-src maps.gstatic.com maps.googleapis.com` |
| **Cloudflare CDN** | `cdnjs.cloudflare.com` references | `script-src cdnjs.cloudflare.com` |
| **YouTube** | `youtube.com` embeds | `frame-src www.youtube.com` |
| **Vimeo** | `vimeo.com` embeds | `frame-src player.vimeo.com` |
| **Hotjar** | `hotjar` references | `script-src static.hotjar.com`; `connect-src *.hotjar.com wss://*.hotjar.com` |
| **Intercom** | `intercom` references | `script-src widget.intercom.io`; `connect-src api-iam.intercom.io wss://*.intercom.io` |
| **Sentry** | `@sentry/` imports | `connect-src *.ingest.sentry.io` |
| **Crisp** | `crisp.chat` references | `script-src client.crisp.chat`; `connect-src client.crisp.chat wss://client.relay.crisp.chat` |

### 5. Generate the CSP

Build the policy using these directives:

```
default-src 'self';
script-src 'self' {detected script sources};
style-src 'self' 'unsafe-inline' {detected style sources};
img-src 'self' data: https: {detected image sources};
connect-src 'self' {cocart-api-domain} {detected connect sources};
font-src 'self' {detected font sources};
frame-src {detected frame sources or 'none'};
object-src 'none';
base-uri 'self';
form-action 'self';
```

Rules:
- Always include `'self'` in `default-src`, `script-src`, `style-src`, `img-src`, `connect-src`, and `font-src`
- Always include `data:` in `img-src` (common for inline images and SVGs)
- Always include `'unsafe-inline'` in `style-src` (most frameworks require this for CSS-in-JS or scoped styles)
- Only add `'unsafe-inline'` to `script-src` if the framework requires it (avoid if possible)
- Only add `'unsafe-eval'` if specifically needed and warn the user about the security implications
- Set `object-src 'none'` to block plugins (Flash, Java applets)
- Set `base-uri 'self'` to prevent base tag injection
- Set `frame-src 'none'` if no iframes are detected; otherwise list only the required frame sources
- If Next.js is detected and `next dev` is used, note that development mode may require `'unsafe-eval'` in `script-src` — but this must NOT be included in production

### 6. Output Framework-Specific Implementation

Provide the CSP in the correct format for the detected framework:

**Next.js** — `next.config.js` or `next.config.mjs` `headers()` function:
```javascript
async headers() {
  return [{ source: '/(.*)', headers: [{ key: 'Content-Security-Policy', value: '...' }] }];
}
```

**Nuxt** — `nuxt.config.ts` `routeRules` or a server middleware:
```typescript
export default defineNuxtConfig({
  routeRules: { '/**': { headers: { 'Content-Security-Policy': '...' } } }
});
```

**Astro** — middleware or `astro.config.mjs` headers integration.

**Express.js / Fastify / Hono / Elysia.js** — middleware that sets the header on every response.

**Vercel** — `vercel.json` `headers` array.

**Netlify** — `netlify.toml` `[[headers]]` section or `_headers` file.

**Cloudflare** — `_headers` file for Pages, or `Response` header manipulation in Workers.

### 7. Recommend Testing

After generating the CSP, always recommend:

1. **Start with Report-Only** — use `Content-Security-Policy-Report-Only` first to log violations without blocking resources
2. **Test with these tools:**
   - [Google CSP Evaluator](https://csp-evaluator.withgoogle.com/) — paste your policy to find weaknesses
   - [Csper Evaluator](https://csper.io/evaluator) — evaluate and get improvement suggestions
   - [Security Headers](https://securityheaders.com/) — scan your deployed URL for all security headers
3. **Check the browser console** — look for CSP violation reports during development
4. **Switch to enforcing** — once no violations appear, change `Content-Security-Policy-Report-Only` to `Content-Security-Policy`

## Important Notes

- A CSP that is too strict will break your storefront. Always test in Report-Only mode first.
- Payment gateway iframes and scripts are the most common source of CSP violations in storefronts. Double-check these.
- Some frameworks inject inline scripts at build time (e.g., Next.js uses nonces). If the framework has built-in CSP nonce support, prefer nonces over `'unsafe-inline'` for `script-src`.
- When in doubt, err on the side of a slightly permissive policy that works over a strict policy that breaks checkout.

## Further Reading

- [HTTP Security Headers Guide](https://docs.cocartapi.com/knowledge-base/http-security-headers) — full guide to all recommended security headers
- [CoCart Security](https://docs.cocartapi.com/security) — built-in API security features
- [CoCart CORS](https://docs.cocartapi.com/cors) — cross-origin request configuration
- [MDN Content-Security-Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy) — complete CSP reference
