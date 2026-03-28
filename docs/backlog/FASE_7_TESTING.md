# FASE 7: Testing

**Status**: ⏸️ PENDIENTE
**Prioridad**: Media
**Dependencias**: FASE_6

## Tareas

### Tarea 1: Configurar Vitest

- Archivo: `vitest.config.ts` (crear)
- Archivo: `package.json` (modificar scripts)
- Que hacer:
  1. `pnpm add -D vitest @testing-library/react @testing-library/jest-dom jsdom`
  2. Configurar con `environment: "jsdom"`, path aliases `@/*`
  3. Scripts: `pnpm test`, `pnpm test:watch`, `pnpm test:coverage`
  4. Mock de Dexie para tests unitarios (fake-indexeddb)

### Tarea 2: Tests unitarios — utilidades

- Archivos: `src/lib/utils/__tests__/image.test.ts`, `alerts.test.ts`, `logger.test.ts`
- Que hacer:
  1. `compressImage()`: verifica reducción de tamaño y dimensiones
  2. `generateAlerts()`: verifica generación correcta por healthStatus y progresión
  3. `logger`: verifica silenciamiento en producción
  4. Storage utils: verifica conteo y límite

### Tarea 3: Tests unitarios — hooks

- Archivos: `src/hooks/__tests__/useScan.test.ts`, `useAlerts.test.ts`
- Que hacer:
  1. Mock de fetch para `/api/scan`
  2. Verificar estados: idle → scanning → success/error
  3. Verificar que resultado se persiste en Dexie (mock)
  4. Verificar generación de alertas post-scan

### Tarea 4: Configurar Playwright

- Archivo: `playwright.config.ts` (crear)
- Que hacer:
  1. `pnpm add -D @playwright/test`
  2. Configurar baseURL localhost:3000
  3. Proyecto: Chrome mobile (Pixel 5 viewport)
  4. Scripts: `pnpm test:e2e`

### Tarea 5: Tests E2E — flujos críticos

- Archivo: `e2e/scan-flow.spec.ts` (crear)
- Archivo: `e2e/history-flow.spec.ts` (crear)
- Que hacer:
  1. Flujo scan: subir imagen → ver loading → ver resultado → guardar
  2. Flujo historial: crear planta → ver en lista → ver detalle → eliminar
  3. Mock de API response (no llamar a OpenAI real en tests)
  4. Verificar estados vacíos en primera visita

## Criterios de Aceptacion

- [ ] `pnpm test` ejecuta tests unitarios con Vitest
- [ ] Coverage > 70% en utilidades y hooks
- [ ] `pnpm test:e2e` ejecuta flujos E2E con Playwright
- [ ] Tests no dependen de API externa (mocked)
- [ ] CI-ready (pueden correr en pipeline)
