# FASE 4: Historial y Gestión de Plantas

**Status**: ⏸️ PENDIENTE
**Prioridad**: Alta
**Dependencias**: FASE_1, FASE_3

## Tareas

### Tarea 1: Hook de plantas

- Archivo: `src/hooks/usePlants.ts` (crear)
- Que hacer:
  1. Lista de plantas del usuario (Dexie)
  2. CRUD: crear planta (nombre + thumbnail del último scan), editar nombre, eliminar (con cascada de scans y alerts)
  3. Obtener scans de una planta específica (timeline)
  4. Reactividad: `useLiveQuery` de Dexie para auto-update de la UI

### Tarea 2: Lista de historial en página principal

- Archivo: `src/components/HistoryList.tsx` (crear)
- Que hacer:
  1. `"use client"` — usa Dexie live query
  2. Sección inferior de `/`: lista de plantas con thumbnail, nombre, último scan
  3. Scans sin planta asociada: sección "Análisis sueltos"
  4. Ordenar por fecha descendente
  5. Badge de alerta si la planta tiene alertas activas
  6. Tap en planta → expandir timeline de scans
  7. Tap en scan → navegar a `/scan/[id]`

### Tarea 3: Página de detalle del scan

- Archivo: `src/app/scan/[id]/page.tsx` (crear)
- Que hacer:
  1. Mostrar imagen original (blob de Dexie)
  2. Resultado completo: especie, salud, confianza, recomendación
  3. Fecha y hora del análisis
  4. Botón "Asociar a planta" si es scan suelto
  5. Botón "Eliminar análisis"
  6. Navegación: volver a `/`
  7. Si scan no existe (ID inválido) → redirect a `/`

### Tarea 4: Crear/asociar planta desde scan

- Archivo: `src/components/PlantSelector.tsx` (crear)
- Que hacer:
  1. Modal/sheet que aparece al guardar un scan
  2. Opción 1: "Crear nueva planta" (input nombre)
  3. Opción 2: "Asociar a planta existente" (lista seleccionable)
  4. Al crear nueva: usa thumbnail del scan actual
  5. Al asociar: actualiza `scan.plantId` en Dexie

### Tarea 5: Límite de almacenamiento

- Archivo: `src/components/StorageWarning.tsx` (crear)
- Que hacer:
  1. Si `getPhotoCount() >= 90`: banner amarillo "Almacenamiento casi lleno"
  2. Si `getPhotoCount() >= 100`: bloquear nuevos scans, mostrar diálogo para borrar antiguos
  3. Botón "Liberar espacio" → lista de scans más antiguos para borrar
  4. Mostrar uso actual (ej: "87/100 fotos")

## Criterios de Aceptacion

- [ ] Lista de plantas se actualiza en tiempo real (Dexie live query)
- [ ] Timeline de scans por planta visible y ordenado
- [ ] Página `/scan/[id]` muestra detalle completo
- [ ] Crear planta y asociar scans funciona correctamente
- [ ] Límite de 100 fotos se aplica con advertencias apropiadas
- [ ] Eliminar planta elimina scans y alerts asociados (cascada)
