# FASE 3: Análisis IA (Route Handler + GPT-4o Vision)

**Status**: ⏸️ PENDIENTE
**Prioridad**: Alta
**Dependencias**: FASE_1, FASE_2

## Orden de ejecución

1. Tarea 1 — schema Zod (el Route Handler lo importa)
2. Tarea 2 — Route Handler (usa el schema)
3. Tarea 3 — hook useScan (llama al Route Handler, usa `useAppStore` de FASE_0)
4. Tarea 4 — componente ScanResult (consume el hook)

## Tareas

### Tarea 1: Schema Zod para resultado del scan

- Archivo: `src/lib/constants/scan-schema.ts` (crear)
- Que hacer:
  1. Definir `ScanResultSchema` con Zod (species, scientificName, confidence, healthStatus, diseaseName, severity, recommendation, displayName)
  2. Exportar tipo inferido `ScanResultResponse`
  3. Reutilizar enums de `src/types/` para healthStatus y severity

### Tarea 2: Route Handler /api/scan

- Archivo: `src/app/api/scan/route.ts` (crear)
- Que hacer:
  1. `export async function POST(request: Request)`
  2. Recibir FormData con campo `image` (File)
  3. Validar: imagen existe, tamaño < 5MB, tipo image/\*
  4. Convertir a base64
  5. Llamar `generateObject()` de Vercel AI SDK con modelo `openai("gpt-4o")`
  6. System prompt: agrónomo experto, cultivos de Arica, respuestas en español
  7. Retornar `Response.json(result.object)`
  8. Timeout: 10s (límite Vercel free tier)
  9. Manejo de errores: API key faltante, timeout, rate limit, respuesta malformada
- Referencia: `_CONCEPTO.md` sección "Route Handler: /api/scan"

### Tarea 3: Hook de scan

- Archivo: `src/hooks/useScan.ts` (crear)
- Que hacer:
  1. `"use client"` hook
  2. Estado efímero vía `useAppStore` (FASE_0): `scanStatus`, `currentScanBlob`, `lastResult`
  3. Función `scan(imageBlob: Blob): Promise<ScanResult>`
  4. Envía FormData a `/api/scan` via fetch
  5. En success: guarda resultado en Dexie (scan + scanResult) + actualiza store
  6. En error: mensaje legible al usuario en español
  7. Timeout client-side: 15s (mayor que server para capturar timeout del server)
- Nota: la llamada a `generateAlerts()` se agrega en FASE_5 (la función aún no existe aquí)

### Tarea 4: Componente de resultado

- Archivo: `src/components/ScanResult.tsx` (crear)
- Que hacer:
  1. Muestra: especie, nombre científico, confianza (%), estado de salud
  2. Si enfermedad: nombre, severidad (badge color), recomendación
  3. Si confianza < 60%: advertencia "Resultado incierto"
  4. Botón "Guardar en planta" (asociar a planta existente o crear nueva)
  5. Botón "Nuevo análisis" (volver a cámara)
  6. Loading state con skeleton mientras escanea

## Criterios de Aceptacion

- [ ] Route Handler responde con JSON estructurado (schema Zod validado)
- [ ] Error si no hay OPENAI_API_KEY en env
- [ ] Timeout manejado con mensaje al usuario
- [ ] Resultado se guarda en IndexedDB automáticamente
- [ ] UI muestra loading → resultado/error correctamente
- [ ] Respuestas siempre en español
