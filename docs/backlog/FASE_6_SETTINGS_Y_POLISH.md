# FASE 6: Settings, UX y Polish Final

**Status**: ⏸️ PENDIENTE
**Prioridad**: Media
**Dependencias**: FASE_4, FASE_5

## Tareas

### Tarea 1: Página de settings

- Archivo: `src/app/settings/page.tsx` (crear)
- Que hacer:
  1. Mostrar uso de almacenamiento (fotos usadas / 100, bytes estimados)
  2. Botón "Borrar todos los datos" con confirmación
  3. Botón "Borrar scans antiguos" (> 6 meses)
  4. Info de la app: versión, stack, enlace a ecosistema AgriPlan
  5. Estado de PWA: instalada / no instalada

### Tarea 2: Navegación y layout de app

- Archivo: `src/components/AppHeader.tsx` (crear)
- Archivo: `src/components/BottomNav.tsx` (crear)
- Que hacer:
  1. Header: logo/nombre "AgriPlan Scan", icono settings
  2. Bottom nav (mobile): Escáner (home) | Historial (scroll a sección) | Settings
  3. Diseño mobile-first, thumb-friendly (botones grandes, zona inferior)
  4. Safe areas para notch y barra de navegación del móvil

### Tarea 3: Estados vacíos y onboarding

- Archivo: `src/components/EmptyState.tsx` (crear)
- Que hacer:
  1. Estado vacío para historial: "Aún no has analizado plantas. ¡Toma tu primera foto!"
  2. Estado vacío para alertas: "Sin alertas activas"
  3. Primera vez: mini-onboarding (3 pasos) explicando cómo usar la app
  4. Ilustraciones simples o iconos (sin assets pesados)

### Tarea 4: Prompt de instalación PWA

- Archivo: `src/hooks/useInstallPrompt.ts` (crear)
- Archivo: `src/components/InstallBanner.tsx` (crear)
- Que hacer:
  1. Capturar evento `beforeinstallprompt`
  2. Mostrar banner no intrusivo: "Instala AgriPlan Scan para acceso rápido"
  3. Botón "Instalar" y "Ahora no"
  4. No mostrar si ya está instalada (`display-mode: standalone`)
  5. Recordar dismissal en localStorage (no mostrar por 7 días)

### Tarea 5: Zustand store global

- Archivo: `src/hooks/useAppStore.ts` (crear)
- Que hacer:
  1. Estado global: `currentScanBlob`, `scanStatus`, `lastResult`
  2. Acciones: `setScanBlob`, `clearScan`, `setStatus`
  3. Solo estado efímero de UI — datos persistentes van a Dexie
  4. Mantener mínimo: no duplicar estado de Dexie

### Tarea 6: Manejo de errores global

- Archivo: `src/components/ErrorBoundary.tsx` (crear)
- Archivo: `src/components/ErrorToast.tsx` (crear)
- Que hacer:
  1. Error boundary React para errores de render
  2. Toast no intrusivo para errores de API (timeout, rate limit, etc.)
  3. Mensajes en español, legibles para usuario no técnico
  4. "Sin conexión a internet" si navigator.onLine es false

## Criterios de Aceptacion

- [ ] Settings muestra storage y permite borrar datos
- [ ] Navegación mobile-first funcional (header + bottom nav)
- [ ] Estados vacíos amigables en español
- [ ] Prompt de instalación PWA aparece correctamente
- [ ] Error boundary captura crashes sin pantalla blanca
- [ ] Zustand store mínimo, sin duplicar estado de Dexie
