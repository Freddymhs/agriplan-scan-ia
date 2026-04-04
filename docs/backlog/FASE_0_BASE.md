# FASE 0: Base del Proyecto

**Status**: ⏸️ PENDIENTE
**Prioridad**: Alta
**Dependencias**: Ninguna

## Orden de ejecución

> Las tareas de esta fase tienen dependencias internas. Ejecutar en este orden:

1. Tarea 4 — crea la estructura de carpetas que el resto necesita
2. Tarea 5 — variables de entorno antes de cualquier código
3. Tarea 3 — instala dependencias antes de usarlas
4. Tarea 6 — logger antes de cualquier hook o componente
5. Tarea 7 — Zustand store antes de cualquier hook que lo consuma
6. Tarea 1 — PWA (usa next-pwa ya instalado)
7. Tarea 2 — layout base

## Tareas

### Tarea 1: Configurar PWA

- Archivo: `next.config.ts` (modificar)
- Archivo: `public/manifest.json` (crear)
- Archivo: `public/icons/` (crear iconos 192x192, 512x512)
- Que hacer:
  1. Instalar `@ducanh2912/next-pwa`
  2. Configurar en `next.config.ts` con `dest: "public"`, solo cache de assets
  3. Crear `manifest.json` con nombre "AgriPlan Scan", theme verde agrícola, display standalone
  4. Generar iconos PWA (placeholder inicial)
  5. Agregar meta tags PWA en `layout.tsx` (`<link rel="manifest">`, `theme-color`, `apple-touch-icon`)

### Tarea 2: Configurar layout base mobile-first

- Archivo: `src/app/layout.tsx` (modificar)
- Archivo: `src/app/globals.css` (modificar)
- Que hacer:
  1. Cambiar `lang="en"` → `lang="es"`
  2. Actualizar metadata: título "AgriPlan Scan", descripción en español
  3. Configurar viewport para mobile (`width=device-width, initial-scale=1, maximum-scale=1`)
  4. Definir paleta de colores en CSS variables (verde agrícola, neutros, estados)
  5. Agregar safe-area-inset para notch de móviles

### Tarea 3: Instalar dependencias core

- Archivo: `package.json` (modificar)
- Que hacer:
  1. `pnpm add ai @ai-sdk/openai` (Vercel AI SDK)
  2. `pnpm add dexie` (IndexedDB)
  3. `pnpm add zustand` (estado global)
  4. `pnpm add zod` (validación schemas)
  5. Verificar build después de instalar

### Tarea 4: Estructura de carpetas

- Directorios a crear:
  - `src/components/`
  - `src/hooks/`
  - `src/lib/db/`
  - `src/lib/constants/`
  - `src/lib/utils/`
  - `src/types/`
- Que hacer:
  1. Crear directorios vacíos con archivos `index.ts` donde aplique barrel exports
  2. Crear `src/lib/constants/app.ts` con constantes base (nombre app, límites, etc.)

### Tarea 5: Configurar variables de entorno

- Archivo: `.env.local` (crear, gitignored)
- Archivo: `.env.example` (crear)
- Que hacer:
  1. Definir `OPENAI_API_KEY` en `.env.example` (sin valor)
  2. Documentar en `.env.example` qué modelo se usa (gpt-4o)
  3. Verificar que `.env*` está en `.gitignore` (ya lo está)

### Tarea 6: Logger centralizado

- Archivo: `src/lib/utils/logger.ts` (crear)
- Que hacer:
  1. Crear wrapper sobre console que respete `NODE_ENV`
  2. Métodos: `info()`, `warn()`, `error()` con prefijo `[AgriScan]`
  3. En producción: silenciar `info` y `warn`, solo `error`
  4. Exportar instancia singleton

### Tarea 7: Zustand store global

- Archivo: `src/hooks/useAppStore.ts` (crear)
- Que hacer:
  1. Estado global: `currentScanBlob`, `scanStatus`, `lastResult`
  2. Acciones: `setScanBlob`, `clearScan`, `setStatus`
  3. Solo estado efímero de UI — datos persistentes van a Dexie
  4. Mantener mínimo: no duplicar estado de Dexie

## Criterios de Aceptacion

- [ ] `pnpm build` pasa sin errores
- [ ] `pnpm lint` pasa sin errores
- [ ] PWA es instalable en Chrome mobile (manifest válido)
- [ ] Layout responde a mobile-first (320px–768px)
- [ ] Variables de entorno documentadas en `.env.example`
- [ ] Logger funcional, sin `console.log` directo en el código
- [ ] Zustand store creado y tipado antes de FASE_1
