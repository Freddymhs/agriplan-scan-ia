# FASE 1: Tipos y Base de Datos Local

**Status**: ⏸️ PENDIENTE
**Prioridad**: Alta
**Dependencias**: FASE_0

## Orden de ejecución

1. Tarea 1 — tipos TypeScript (schema Dexie y helpers los importan)
2. Tarea 2 — schema Dexie (usa los tipos)
3. Tarea 3 — helpers CRUD (usan schema y tipos)
4. Tarea 4 — utilidades de storage (independiente, pero usa constantes de Tarea 3)

## Tareas

### Tarea 1: Definir tipos TypeScript

- Archivo: `src/types/index.ts` (crear)
- Que hacer:
  1. Definir `Plant` interface (id, name, species, createdAt, thumbnailBlob)
  2. Definir `Scan` interface (id, plantId nullable, imageBlob, createdAt, results, source)
  3. Definir `ScanResult` interface (label, score, category, displayName, recommendation)
  4. Definir `Alert` interface (id, plantId, scanId, type, severity, message, createdAt, dismissed)
  5. Definir enums: `ScanSource`, `AlertType`, `AlertSeverity`, `HealthStatus`, `ScanCategory`
  6. Schemas Zod correspondientes para validación en Route Handler
- Referencia: `CONCEPTO.md` sección "Modelo de datos"

### Tarea 2: Configurar Dexie.js schema

- Archivo: `src/lib/db/schema.ts` (crear)
- Que hacer:
  1. Definir Dexie database class con tablas: plants, scans, alerts
  2. Índices: scans por plantId y createdAt, alerts por plantId y dismissed
  3. NO indexar blobs (solo metadata)
  4. Versión 1 del schema

### Tarea 3: Crear helpers Dexie

- Archivo: `src/lib/db/plants.ts` (crear)
- Archivo: `src/lib/db/scans.ts` (crear)
- Archivo: `src/lib/db/alerts.ts` (crear)
- Archivo: `src/lib/db/index.ts` (barrel export)
- Que hacer:
  1. CRUD para plants: create, getAll, getById, update, delete
  2. CRUD para scans: create, getByPlantId, getById, getRecent(limit), delete, countAll
  3. CRUD para alerts: create, getByPlantId, getActive, dismiss, deleteByPlantId
  4. Todas las funciones tipadas con los interfaces de `src/types/`
  5. Nunca exponer Dexie directo — solo helpers

### Tarea 4: Utilidad de almacenamiento

- Archivo: `src/lib/db/storage.ts` (crear)
- Que hacer:
  1. Función `getStorageUsage()` — usa StorageManager API para reportar uso
  2. Función `getPhotoCount()` — cuenta total de scans con blob
  3. Constante `MAX_PHOTOS = 100`
  4. Función `isStorageFull()` — retorna boolean
  5. Función `requestPersistentStorage()` — solicita cuota persistente al SO

## Criterios de Aceptacion

- [ ] Tipos TypeScript exportados y usables desde cualquier archivo
- [ ] Dexie schema creado con índices correctos (sin blobs indexados)
- [ ] Helpers CRUD funcionales para las 3 tablas
- [ ] `pnpm build` pasa sin errores de tipos
- [ ] Utilidades de storage reportan uso correctamente
