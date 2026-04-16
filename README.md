# CoCart Skills

A collection of skills for AI coding agents following the [Agent Skills](https://agentskills.io) format — compatible with [Claude Code](https://claude.ai/code), [GitHub Copilot](https://github.com/features/copilot), [Gemini CLI](https://github.com/google-gemini/gemini-cli), [Cursor](https://cursor.com/), [OpenAI Codex](https://openai.com/codex/), [Windsurf](https://windsurf.com/), [Cline](https://cline.bot/), and any agent that supports the Agent Skills standard.

Add `use CoCart` to any prompt in your AI coding agent to get accurate SDK installation, adapter configuration, and code patterns for your framework.

## How to Use

After installing, just append `use CoCart` to any prompt:

```
Set up CoCart in my Next.js App Router project. use CoCart
```

```
Add a cart page to my Astro site with CoCart. use CoCart
```

```
Install the CoCart SDK and show me how to list products and handle auth. use CoCart
```

Your AI coding agent of choice will automatically apply the correct SDK, adapter, and patterns for your framework.

You can also invoke skills directly:

| Command | Description |
|---|---|
| `/cocart:setup` | Main CoCart API setup and integration guidance |
| `/cocart:js` | TypeScript / JavaScript SDK patterns |
| `/cocart:go` | Go SDK patterns |
| `/cocart:php` | PHP SDK patterns |
| `/cocart:python` | Python SDK patterns |
| `/cocart:flutter` | Flutter / Dart SDK patterns |
| `/cocart:swift` | Swift SDK patterns (iOS, macOS, etc.) |

## Install

### Claude Code

```bash
# From the marketplace
/plugin install cocart

# Or from this repo directly
/plugin marketplace add cocart-headless/cocart-skills
/plugin install cocart@cocart-headless
```

### GitHub Copilot (VS Code)

Clone this repo into your project's `.github/skills/` directory, or add the `.agents/skills/` path to your VS Code `chat.skillsLocations` setting. Ensure `chat.useAgentSkills` is enabled.

### Gemini CLI

Skills are auto-discovered from `.agents/skills/` when this repo is cloned as a project dependency or placed in `~/.gemini/skills/`.

### Cursor

```bash
# From the marketplace (when available)
/plugin install cocart

# Or from this repo directly
/plugin marketplace add cocart-headless/cocart-skills
/plugin install cocart@cocart-headless
```

Also detected automatically via `.cursor-plugin/plugin.json` when this repo is cloned into your project.

### OpenAI Codex

Detected automatically via `.codex-plugin/plugin.json` when this repo is cloned into your project.

### Windsurf / Cline / Other Agents

Any agent that supports the [Agent Skills](https://agentskills.io) standard will discover skills from `.agents/skills/`. Clone this repo and the skills are ready to use.

### Manual

```bash
git clone https://github.com/cocart-headless/cocart-skills.git
# Skills are in skills/ and .agents/skills/
```

## What It Covers

- SDK installation for all package managers (npm, yarn, pnpm, bun, CDN)
- Framework adapters for TypeScript/JavaScript: **Astro, Next.js, Nuxt, SvelteKit, Remix, Hono, Elysia.js, Fastify, Deno, Framer, Webflow**
- Authentication: guest sessions, Basic Auth, consumer keys, JWT with auto-refresh
- Cart operations: add, update, remove, coupons, clear
- Products API: list, filter, search
- Session and encrypted localStorage setup
- Environment variable patterns
- Error handling
- Links to full docs and OpenAPI spec

## Supported Platforms

| Directory | Platforms |
|---|---|
| `skills/` | Canonical source — all skills live here |
| `.agents/skills/` | GitHub Copilot, Gemini CLI, Cline, Windsurf, OpenCode, and other Agent Skills-compatible agents |
| `.claude-plugin/` | Claude Code |
| `.cursor-plugin/` | Cursor |
| `.codex-plugin/` | OpenAI Codex |

## Contributing

**Always edit skills in the `skills/` directory.** The `.agents/skills/` directory is an auto-synced copy maintained by a [GitHub Actions workflow](.github/workflows/sync-skills.yml) — do not edit it directly. Changes pushed to `skills/` on the `main` branch will be synced automatically.

## SDKs

| Language | Package |
|---|---|
| TypeScript / JavaScript | [`@cocartheadless/sdk`](https://github.com/cocart-headless/cocart-js) |
| PHP | [`cocart-headless/cocart-php`](https://github.com/cocart-headless/cocart-php) |
| Python | [`cocart-headless/cocart-python`](https://github.com/cocart-headless/cocart-python) |
| Go | [`cocart-headless/cocart-go`](https://github.com/cocart-headless/cocart-go) |
| Flutter | [`cocart-headless/cocart-flutter-sdk`](https://github.com/cocart-headless/cocart-flutter-sdk) |
| Swift | [`cocart-headless/cocart-swift-sdk`](https://github.com/cocart-headless/cocart-swift-sdk) |

## Links

- [CoCart Docs](https://cocartapi.com/docs/)
- [OpenAPI Spec](https://github.com/cocart-headless/cocart-openapi)
- [GitHub Organisation](https://github.com/cocart-headless)
- [Community Discord](https://cocartapi.com/community/)

## License

MIT
