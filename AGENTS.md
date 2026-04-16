# CoCart Skills

This repository provides AI coding agent skills for [CoCart](https://cocartapi.com) — the headless WooCommerce REST API. Add `use CoCart API` to any prompt to get accurate SDK installation, adapter configuration, and code patterns for your framework.

## Available Skills

| Skill | Description |
|---|---|
| `setup` | Main entry point — detects your language and routes to the correct SDK skill |
| `js` | TypeScript / JavaScript SDK — Astro, Next.js, Nuxt, SvelteKit, Remix, Hono, Deno, and more |
| `go` | Go SDK — net/http, Gin, Echo, Fiber |
| `php` | PHP SDK — Laravel, Symfony, WordPress, plain PHP |
| `python` | Python SDK — Django, FastAPI, Flask |
| `flutter` | Flutter / Dart SDK — iOS, Android, web, desktop |
| `swift` | Swift SDK — iOS, macOS, watchOS, tvOS, visionOS |

## Skill Locations

Skills are provided in two directories for maximum agent compatibility:

- `skills/` — Canonical source, used by Claude Code, Cursor, and OpenAI Codex (via plugin.json)
- `.agents/skills/` — Auto-synced copy, used by Gemini CLI, GitHub Copilot, Cline, and other agents that follow the Agent Skills discovery standard

## Synced Skills (Do Not Edit)

The `.agents/skills/` directory is auto-synced from `skills/` via a [GitHub Actions workflow](.github/workflows/sync-skills.yml). Any manual edits to files in `.agents/skills/` will be overwritten on the next push to `main`.

Edit these in `skills/` instead:

- `.agents/skills/setup/` — synced from `skills/setup/`
- `.agents/skills/js/` — synced from `skills/js/`
- `.agents/skills/go/` — synced from `skills/go/`
- `.agents/skills/php/` — synced from `skills/php/`
- `.agents/skills/python/` — synced from `skills/python/`
- `.agents/skills/flutter/` — synced from `skills/flutter/`
- `.agents/skills/swift/` — synced from `skills/swift/`

## Authored Skills (Safe to Edit)

All skills in `skills/` are locally authored and safe to edit directly.

## Links

- [CoCart Docs](https://cocartapi.com/docs/)
- [OpenAPI Spec](https://github.com/cocart-headless/cocart-openapi)
- [GitHub Organisation](https://github.com/cocart-headless)
- [Community Discord](https://cocartapi.com/community/)
