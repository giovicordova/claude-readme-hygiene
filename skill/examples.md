# Template Examples

## Minimal (small project, no unusual conventions)

```markdown
# Project: invoice-api

REST API for generating and sending invoices to UK clients.

## Stack
- Node.js + Express + TypeScript
- PostgreSQL via Prisma
- Resend for email delivery

## Commands
- dev: `npm run dev`
- test: `npm test`
- lint: `npm run lint --fix`

## Gotchas
- Resend API has a 100 emails/day limit on free tier — use `MOCK_EMAIL=true` in dev.
- `src/templates/` files are loaded at runtime via dynamic import — they look unused but aren't.
```

## Complex (monorepo, overrides needed)

```markdown
# Project: soundboard-platform

Multi-tenant audio processing platform with real-time collaboration.

## Stack
- Turborepo monorepo: apps/web (Next.js), apps/api (Fastify), packages/shared
- PostgreSQL + Redis
- Web Audio API + custom DSP pipeline in `packages/audio-engine`

## Commands
- dev: `turbo dev`
- test: `turbo test`
- lint: `turbo lint`
- build: `turbo build`
- test single package: `turbo test --filter=@soundboard/audio-engine`

## Structure
- `apps/web/` — Next.js frontend, pages router
- `apps/api/` — Fastify backend, route files auto-loaded from `src/routes/`
- `packages/audio-engine/` — DSP processing, WebAssembly builds
- `packages/shared/` — types, utils, constants shared across apps

## Conventions
- All API routes return `{ data, error, meta }` envelope — never raw values.
- Audio processing params use sample-accurate timing (samples, not ms).
- State: Zustand in frontend, no Redux.

## Gotchas
- `packages/audio-engine` compiles to WASM — run `npm run build:wasm` after changes there, or tests will use stale binary.
- Route files in `apps/api/src/routes/` are auto-registered by Fastify — don't manually import them.
- `.env.local` in `apps/web/` overrides `.env` — check both when debugging config.

## Source-of-Truth Docs
- `.planning/` — GSD workflow, update after structural changes
- `packages/shared/src/types/` — canonical type definitions, update when adding new entities

## Global Overrides
- Override: no auto-commits — this repo uses conventional PR workflow. Stage changes only.
```
