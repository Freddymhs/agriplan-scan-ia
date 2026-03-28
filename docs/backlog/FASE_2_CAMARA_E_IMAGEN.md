# FASE 2: Captura de Imagen y Cámara

**Status**: ⏸️ PENDIENTE
**Prioridad**: Alta
**Dependencias**: FASE_0

## Tareas

### Tarea 1: Hook de cámara

- Archivo: `src/hooks/useCamera.ts` (crear)
- Que hacer:
  1. Intentar `navigator.mediaDevices.getUserMedia({ video: { facingMode: "environment" } })`
  2. Si falla (permisos denegados, no disponible) → flag `fallbackToInput: true`
  3. Exponer: `stream`, `isSupported`, `fallbackToInput`, `error`, `start()`, `stop()`
  4. Cleanup del stream en unmount
  5. Manejar caso iOS PWA (requiere agregar a home screen)

### Tarea 2: Componente de captura

- Archivo: `src/components/CameraCapture.tsx` (crear)
- Que hacer:
  1. `"use client"` — requiere interacción
  2. Si `useCamera.isSupported`: mostrar video preview con botón "Capturar"
  3. Si `fallbackToInput`: mostrar `<input type="file" accept="image/*" capture="environment">`
  4. Botón "Subir desde galería" siempre visible (`<input type="file" accept="image/*">`)
  5. Al capturar: canvas snapshot del video → Blob

### Tarea 3: Procesamiento de imagen

- Archivo: `src/lib/utils/image.ts` (crear)
- Que hacer:
  1. Función `compressImage(blob: Blob): Promise<Blob>` — redimensiona a max 1024px, JPEG 80%
  2. Función `blobToBase64(blob: Blob): Promise<string>` — para enviar a API
  3. Usar OffscreenCanvas si disponible, fallback a canvas normal
  4. Validar que el input es imagen (type check)

### Tarea 4: Preview de imagen

- Archivo: `src/components/ImagePreview.tsx` (crear)
- Que hacer:
  1. Recibe `blob: Blob` como prop
  2. Muestra imagen con `URL.createObjectURL()` + cleanup en unmount
  3. Botones: "Analizar" (envía al scan) y "Descartar" (limpia)
  4. Indicador de tamaño del archivo comprimido

## Criterios de Aceptacion

- [ ] Cámara funciona en Chrome Android (getUserMedia)
- [ ] Fallback a input file funciona en iOS Safari
- [ ] Galería funciona en todos los browsers
- [ ] Imagen comprimida a JPEG 80%, max 1024px
- [ ] Preview muestra imagen antes de analizar
- [ ] No hay memory leaks (objectURL revocados, streams parados)
