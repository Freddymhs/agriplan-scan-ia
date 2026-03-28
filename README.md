# AgriPlan Scan IA

PWA para identificar especies de plantas y diagnosticar su estado de salud mediante análisis de imagen con IA. Parte del ecosistema AgriPlan, pero independiente (sin backend compartido, sin Supabase, sin dependencia de API).

## Tech Stack

- **Framework:** Next.js 16 App Router + React 19 + TypeScript strict
- **Styling:** TailwindCSS 4
- **IA (MVP):** Vercel AI SDK (`ai` + `@ai-sdk/openai`) — GPT-4o Vision
- **Storage:** IndexedDB (Dexie.js) para historial local
- **Estado:** Zustand
- **PWA:** @ducanh2912/next-pwa (service worker para assets, NO datos)
- **Deploy:** Vercel

## Setup

```bash
pnpm install
pnpm dev          # Dev server en localhost:3000
pnpm build        # Build de producción
pnpm start        # Servir build de producción
pnpm lint         # ESLint
```

## Estructura

```
src/
├── app/              # Pages y Route Handlers (App Router)
├── components/       # Componentes UI
├── hooks/            # Hooks de estado y lógica
├── lib/
│   ├── db/           # Schema y helpers Dexie.js
│   ├── constants/    # Constantes de la app
│   └── utils/        # Utilidades compartidas
└── types/            # Interfaces TypeScript
```

## Funcionalidades (MVP)

- Captura de foto (cámara nativa o galería)
- Identificación de especie vía GPT-4o Vision
- Diagnóstico de salud (enfermedad, deficiencia, plaga, estrés)
- Recomendaciones en español
- Historial local de análisis (IndexedDB)
- Alertas basadas en diagnóstico
- PWA instalable

## Estado

En desarrollo inicial (scaffold).
