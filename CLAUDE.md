# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

@AGENTS.md

## What This Is

AgriPlan Scan IA — a PWA for plant identification and health diagnosis via image analysis. Part of the AgriPlan ecosystem but **independent** (no shared backend, no Supabase, no API dependency).

**MVP:** Online-only. User takes/uploads a photo → Route Handler sends to GPT-4o Vision via Vercel AI SDK → structured result (species, health, recommendation). History stored in IndexedDB (Dexie.js).

**v2 (future):** Hybrid offline/online with Transformers.js local model + Vercel AI fallback when confidence < 60%.

## Commands

```bash
pnpm dev          # Dev server (localhost:3000)
pnpm build        # Production build
pnpm start        # Serve production build
pnpm lint         # ESLint (flat config, eslint.config.mjs)
```

Package manager is **pnpm** (see `pnpm-lock.yaml`, `pnpm-workspace.yaml`).

## Stack

- **Next.js 16** App Router + React 19 + TypeScript strict
- **TailwindCSS 4** (via `@tailwindcss/postcss`)
- **Vercel AI SDK** (`ai` + `@ai-sdk/openai`) — not yet installed
- **Dexie.js** for IndexedDB history — not yet installed
- **Zustand** for global state — not yet installed
- Deploy target: **Vercel**

## Architecture (Planned)

```
src/
├── app/
│   ├── page.tsx              # Main page: scanner + results + history
│   ├── scan/[id]/page.tsx    # Detail view of a single scan
│   ├── settings/page.tsx     # User settings
│   └── api/scan/route.ts     # Route Handler → Vercel AI SDK → GPT-4o Vision
├── components/               # UI components
├── hooks/                    # State/logic hooks
├── lib/
│   ├── db/                   # Dexie.js schema and helpers
│   ├── constants/            # App constants
│   └── utils/                # Shared utilities
└── types/                    # TypeScript interfaces (Plant, Scan, ScanResult, Alert)
```

**Key patterns:**

- RSC by default. `"use client"` only where interaction is required (camera, scan UI, history list).
- Route Handler `/api/scan` receives FormData with image, calls `generateObject()` from Vercel AI SDK.
- All IndexedDB access through Dexie helpers — never raw IndexedDB in components.
- Images stored as Blobs in IndexedDB. JPEG 80%, max 1024px. Cap at 100 photos.
- Camera access via `getUserMedia()` with `<input capture>` fallback.

## Data Model

Four core entities stored in IndexedDB (Dexie): `Plant`, `Scan`, `ScanResult`, `Alert`. Full schemas are in `docs/_CONCEPTO.md`.

## Key Constraints

- All responses from the AI must be in **Spanish** (system prompt enforces this).
- Target users are small farmers in Arica, Chile — UI must be simple and mobile-first.
- PWA must be installable with service worker caching assets (not data).
- MVP requires internet for every scan. Offline only for viewing saved history.

## Ecosystem Context

This app is part of the AgriPlan ecosystem (see parent `CLAUDE.md` at `project-agricultura/CLAUDE.md`) but shares no backend, database, or auth with the PWA or API. Future integration is possible (shared crop catalog, sending diagnoses to Portal).

## Feature futura: Bitácora de campo

Scan IA podría evolucionar a bitácora de campo: foto + geolocalización + diagnóstico vinculado al cultivo del usuario.
Requiere: sistema de cuentas (Supabase Auth), relación con proyectos/zonas de la PWA, integración con la instancia Supabase compartida del ecosistema. Implica que dejaría de ser independiente.
No para el MVP actual. Documentado para no perder la idea.
