# AgriPlan Scan IA — Bosquejo de Concepto

**Estado:** Idea / Bosquejo
**Fecha:** 2026-03-28
**Autor:** Freddy

---

## Qué es

App PWA (Next.js) que permite al usuario tomar o subir una foto de una planta y obtener:

1. **Identificación de especie** — "Esto es una tuna (Opuntia ficus-indica)"
2. **Diagnóstico de salud** — sana, enfermedad, deficiencia nutricional, plaga, estrés hídrico
3. **Recomendaciones** — qué hacer según el diagnóstico
4. **Historial de fotos** — registro por planta con evolución en el tiempo
5. **Alertas** — basadas en análisis de imagen (ej: "Esta planta muestra signos de clorosis")

---

## Decisión clave: IA via Vercel AI SDK (online)

**Objetivo MVP:** App funcional que identifica plantas y diagnostica enfermedades usando GPT-4o Vision via Vercel AI SDK. Requiere conexión a internet.

**Objetivo v2:** Agregar modelo local (Transformers.js) para funcionar offline. Si el modelo local tiene baja confianza (< 60%), fallback automático a Vercel AI online.

### Flujo de uso (MVP — online)

```
1. Usuario abre la app (PWA)
2. Toma foto o sube desde galería
3. Foto se envía a /api/scan (Route Handler)
4. Route Handler envía imagen a GPT-4o Vision via Vercel AI SDK
5. IA retorna: { especie, salud, confianza, recomendaciones }
6. Resultado se guarda en historial local (Dexie.js / IndexedDB)
7. Requiere internet
```

### Flujo de uso (v2 — hybrid offline/online)

```
1. Usuario abre la app (PWA)
2. Toma foto o sube desde galería
3. Imagen se procesa localmente con Transformers.js (modelo en browser)
4. Si confianza >= 60%: resultado local inmediato (offline)
5. Si confianza < 60%: envía a Vercel AI como fallback (online)
6. Resultado se guarda en historial local
7. Funciona offline para plantas conocidas, online para desconocidas
```

### ¿Por qué Vercel AI SDK para MVP?

- Reconoce CUALQUIER planta del mundo (incluyendo tuna, pitahaya, higuera de Arica)
- Sin necesidad de convertir modelos, entrenar, ni configurar ONNX
- Streaming nativo con `streamText()` / `streamObject()`
- Agnóstico de modelo: OpenAI hoy, cambiar a otro mañana
- Desarrollo rápido: una semana vs un mes con modelo local

### Limitaciones MVP

- Requiere conexión a internet (sin offline)
- Costo por request (API de OpenAI)
- Timeout de 10s en Vercel free tier (suficiente para análisis de imagen)
- Dependencia de servicio externo

---

## Stack

```
Framework:    Next.js 16 App Router + TypeScript
Styling:      TailwindCSS 4
PWA:          @ducanh2912/next-pwa (service worker + cache de assets)
IA (MVP):     Vercel AI SDK (ai + @ai-sdk/openai) — GPT-4o Vision
IA (v2):      Transformers.js (@huggingface/transformers) — modelo local offline
Storage:      IndexedDB (Dexie.js) para historial local
Cámara:       navigator.mediaDevices.getUserMedia() + fallback <input capture>
Estado:       Zustand (simple, global)
Testing:      Vitest + Playwright
Deploy:       Vercel
```

---

## Funcionalidades detalladas

### F1: Captura de imagen

- Botón "Tomar foto" → abre cámara nativa del dispositivo
- Botón "Subir foto" → selector de archivos (galería)
- Preview de la imagen antes de analizar
- getUserMedia() con fallback automático a `<input type="file" capture="environment">`
- Requiere HTTPS (excepto localhost)
- iOS: PWA requiere agregar a home screen para acceso a cámara

### F2: Identificación de especie

- Foto se envía a GPT-4o Vision via Route Handler
- Output: nombre de la planta, familia, nombre científico
- Confianza: "92% seguro que es Tomate"
- Si confianza < 60%: "No estoy seguro, podría ser X o Y"
- Reconoce cultivos de Arica (tuna, pitahaya, higuera, olivo) — ventaja de usar IA cloud

### F3: Diagnóstico de salud

- Mismo request a GPT-4o Vision analiza salud
- Output: nombre de la enfermedad, severidad, parte afectada
- Categorías: sana, enfermedad fúngica, bacteriana, viral, deficiencia, plaga
- Recomendación: "Aplicar fungicida de cobre" / "Revisar riego"

### F4: Historial de fotos

- Guardar cada análisis en IndexedDB (Dexie.js)
- Campos: foto (blob), fecha, resultado, planta asociada
- Timeline visual: ver evolución de una planta en el tiempo
- Poder crear "plantas" y asociar múltiples fotos a cada una

### F5: Alertas basadas en imagen

- Si el diagnóstico detecta enfermedad → alerta "Acción requerida"
- Si una planta empeora entre fotos → alerta "Progresión detectada"
- Badge en la planta con nivel de urgencia (verde/amarillo/rojo)

### F6: PWA Instalable

- manifest.json con iconos, colores, nombre
- Service Worker cachea app shell (assets UI)
- Prompt de instalación en mobile
- Funciona offline para ver historial guardado (no para nuevos análisis en MVP)

---

## Pantallas

```
/              → Página única con secciones:
                 - Arriba: escáner (cámara/upload + botón analizar)
                 - Centro: resultado del último análisis
                 - Abajo: historial de plantas y scans recientes
                 - Alertas activas visibles como badges/cards

/scan/[id]     → Resultado detallado de un análisis específico

/settings      → Config (borrar datos, info de storage, cuenta)
```

Todo vive en una sola página. Lista de plantas, alertas y timeline son secciones/componentes dentro de `/`.

---

## Modelo de datos (IndexedDB / Dexie)

```typescript
interface Plant {
  id: string;
  name: string; // "Mi tomatera del patio"
  species: string; // "Solanum lycopersicum"
  createdAt: string;
  thumbnailBlob: Blob; // Última foto
}

interface Scan {
  id: string;
  plantId: string | null; // Puede ser scan suelto o asociado a planta
  imageBlob: Blob;
  createdAt: string;
  results: ScanResult[];
  source: "cloud" | "local"; // MVP siempre "cloud", v2 puede ser "local"
}

interface ScanResult {
  label: string; // "Tomato___Late_blight" o nombre libre de GPT
  score: number; // 0.92
  category: "species" | "disease" | "healthy";
  displayName: string; // "Tizón tardío del tomate"
  recommendation: string; // "Aplicar fungicida..."
}

interface Alert {
  id: string;
  plantId: string;
  scanId: string;
  type: "disease_detected" | "progression" | "action_required";
  severity: "low" | "medium" | "high" | "critical";
  message: string;
  createdAt: string;
  dismissed: boolean;
}
```

---

## Almacenamiento

- Máximo **100 fotos** en IndexedDB
- Comprimir a JPEG 80% y limitar resolución a 1024px
- Al llegar al límite, sugerir borrar las más antiguas
- **No indexar blobs** — solo indexar metadata (fecha, plantId, etc.)
- Binary format (33% más eficiente que Base64)
- Usar StorageManager API para solicitar cuota persistente al SO
- 100 fotos JPEG 80% @ 1024px ≈ 2-4 MB total (sin problema de quota)

---

## Route Handler: /api/scan

```typescript
// src/app/api/scan/route.ts
import { generateObject } from "ai";
import { openai } from "@ai-sdk/openai";
import { z } from "zod";

const ScanResultSchema = z.object({
  species: z.string(),
  scientificName: z.string(),
  confidence: z.number(),
  healthStatus: z.enum(["healthy", "disease", "deficiency", "pest", "stress"]),
  diseaseName: z.string().nullable(),
  severity: z.enum(["none", "low", "medium", "high", "critical"]),
  recommendation: z.string(),
  displayName: z.string(),
});

export async function POST(request: Request) {
  const formData = await request.formData();
  const image = formData.get("image") as File;

  const imageBuffer = Buffer.from(await image.arrayBuffer());
  const base64Image = imageBuffer.toString("base64");

  const result = await generateObject({
    model: openai("gpt-4o"),
    schema: ScanResultSchema,
    messages: [
      {
        role: "user",
        content: [
          {
            type: "image",
            image: base64Image,
          },
          {
            type: "text",
            text: "Analiza esta imagen de planta. Identifica la especie y diagnostica su estado de salud. Responde en español.",
          },
        ],
      },
    ],
    system:
      "Eres un agrónomo experto en identificación de plantas y diagnóstico de enfermedades. Especializas en cultivos de zonas áridas del norte de Chile (Arica): tuna, pitahaya, higuera, olivo, guayaba, dátil. Responde siempre en español.",
  });

  return Response.json(result.object);
}
```

---

## Decisiones tomadas

1. **MVP = Vercel AI online (GPT-4o Vision).** Sin modelo local, sin Transformers.js, sin ONNX. Lo más rápido de construir. Reconoce cualquier planta.

2. **v2 = Modelo local + Vercel AI fallback.** Transformers.js con modelo PlantVillage (38 clases). Si confianza < 60%, envía a Vercel AI. Funciona offline para plantas conocidas.

3. **Una sola página con secciones.** Todo en `/` — escáner arriba, historial abajo. Solo `/scan/[id]` y `/settings` como rutas separadas.

4. **Almacenamiento:** Máximo 100 fotos en IndexedDB (Dexie.js). JPEG 80%, 1024px. Blobs sin indexar.

5. **Labels en español:** GPT-4o responde directamente en español. En v2, crear `labels-es.ts` para mapear los 38 labels del modelo local.

6. **Cámara:** getUserMedia() con fallback a `<input type="file" capture="environment">`. HTTPS requerido.

---

## Roadmap v2: IA Local (Investigación Previa)

> Esta sección contiene investigación web realizada para la versión 2 (offline).
> No aplica al MVP. Se mantiene como referencia futura.

### Transformers.js v3/v4

- Paquete: `@huggingface/transformers` (v3). El antiguo `@xenova/transformers` está obsoleto.
- Corre en browser via ONNX Runtime Web (WebAssembly + WebGPU).
- Requiere webpack config: `sharp$: false`, `onnxruntime-node$: false`.
- Modelo debe cargarse en Web Worker dedicado (singleton) para no bloquear UI.

### Modelo candidato

- `linkanjarad/mobilenet_v2_1.0_224-plant-disease` — 95.41% accuracy, 38 clases, ~13MB.
- Dataset: PlantVillage (54,305 imágenes, 14 especies).
- **NO cubre cultivos de Arica** (tuna, pitahaya, higuera, olivo).
- Requiere conversión a ONNX con Hugging Face Optimum CLI.

### Dataset alternativo: LeafNet

- 186,000+ imágenes, 97 clases, 22 especies.
- Más completo que PlantVillage pero igualmente no cubre Arica.

### Fine-tuning para Arica (futuro)

- Recolectar ~200 fotos por cultivo/enfermedad.
- Fine-tune MobileNetV2 con PyTorch (congelar capas inferiores).
- Cuantizar INT8 (-75% tamaño: 13MB → 3.4MB).
- Convertir a ONNX y validar en browser.

### WebGPU vs WebAssembly

- WebGPU: 2-3x más rápido, soportado en Chrome/Edge v121+ Android.
- WASM: Compatible everywhere, más lento.
- Recomendación: WebGPU con fallback automático a WASM.

---

## Relación con ecosistema AgriPlan

- **Independiente por ahora** — No consume API de AgriPlan
- **Futuro:** Podría compartir catálogo de cultivos con la PWA
- **Futuro:** Podría enviar diagnósticos al chat IA del Portal como contexto
- **Futuro:** Si se agrega backend, podría subir fotos a Supabase para análisis cloud

---

## Próximo paso

Cuando estemos listos: crear backlog real con `/work:create-backlog` dentro de la carpeta `agriplan-scan-ia/`.
