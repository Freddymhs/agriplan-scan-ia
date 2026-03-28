# FASE 5: Sistema de Alertas

**Status**: ⏸️ PENDIENTE
**Prioridad**: Media
**Dependencias**: FASE_3, FASE_4

## Tareas

### Tarea 1: Generación automática de alertas

- Archivo: `src/lib/utils/alerts.ts` (crear)
- Que hacer:
  1. Función `generateAlerts(scan: Scan, result: ScanResult, plant?: Plant): Alert[]`
  2. Si `healthStatus !== "healthy"` → alert `disease_detected` con severidad mapeada
  3. Si planta tiene scan anterior con misma enfermedad pero menor severidad → alert `progression`
  4. Severidad: healthy→none, low→low, medium→medium, high/critical→high+action_required
  5. Llamar automáticamente después de cada scan exitoso (desde `useScan`)

### Tarea 2: Hook de alertas

- Archivo: `src/hooks/useAlerts.ts` (crear)
- Que hacer:
  1. Lista de alertas activas (no dismissed) via Dexie live query
  2. Función `dismiss(alertId)` — marca como dismissed
  3. Función `dismissAll(plantId)` — descarta todas las de una planta
  4. Contador de alertas activas para badge

### Tarea 3: Componente de alertas

- Archivo: `src/components/AlertBadge.tsx` (crear)
- Archivo: `src/components/AlertCard.tsx` (crear)
- Que hacer:
  1. `AlertBadge`: círculo rojo/amarillo/verde sobre thumbnail de planta. Muestra severidad máxima.
  2. `AlertCard`: card expandible con mensaje, fecha, severidad, botón "Descartar"
  3. Sección de alertas activas visible en página principal (entre scanner y historial)
  4. Ordenar por severidad descendente, luego por fecha

## Criterios de Aceptacion

- [ ] Alertas se generan automáticamente tras cada scan
- [ ] Progresión detectada comparando scans anteriores de la misma planta
- [ ] Badges visibles en lista de plantas
- [ ] Alertas se pueden descartar individualmente
- [ ] UI actualiza en tiempo real cuando cambian alertas
