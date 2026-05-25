# DECISION: Usar Vercel AI SDK (no Anthropic SDK directo)

**Fecha:** 2026-03-28
**Status:** ✅ Vigente

## Contexto

`agriplan-scan-ia` necesita enviar imágenes de plantas a un modelo de visión (GPT-4o Vision o Claude 3.5 Sonnet Vision) y recibir respuesta estructurada (especie, salud, recomendaciones). Hay dos caminos principales:

1. SDK oficial del proveedor (Anthropic SDK, OpenAI SDK)
2. Vercel AI SDK (abstracción multi-proveedor con `generateObject`, streaming, tooling integrado para Next.js)

## Decisión

Usar **Vercel AI SDK** (`@ai-sdk/openai`).

## Por qué

- **Integración nativa con Next.js**: el route handler `/api/scan` usa `Response.json()` que se integra natural con el ecosistema Vercel/Next
- **`generateObject` con schema Zod**: garantiza respuesta estructurada y tipada sin parseo manual de la respuesta
- **Cambio de proveedor con 1 línea**: si en el futuro pasamos de OpenAI a Claude/Gemini, cambia solo el provider import, no la lógica
- **Streaming integrado**: si más adelante queremos respuestas progresivas, el SDK ya lo soporta sin re-escribir
- **Deploy en Vercel sin friction**: telemetría, edge runtime, y timeouts ya están optimizados

## Trade-offs aceptados

- **Una capa de abstracción más** sobre el SDK oficial → si OpenAI saca features muy nuevos, pueden tardar días/semanas en estar disponibles en Vercel AI SDK
- **Dependencia adicional** → más superficie de actualización en npm
- **No control directo sobre headers/retry policy** del SDK oficial — usás lo que Vercel decida

## Alternativas descartadas

| Alternativa           | Por qué no                                                                           |
| --------------------- | ------------------------------------------------------------------------------------ |
| OpenAI SDK directo    | Más boilerplate (parseo, schemas manuales), menos portable                           |
| Anthropic SDK directo | Mismo problema + cero integración con Next.js                                        |
| LangChain             | Sobrecarga conceptual masiva para un caso simple (1 endpoint, 1 modelo, 1 respuesta) |
| Llamada `fetch` cruda | Reinventar la rueda                                                                  |

## Implementación

Ver `FASE_3_SCAN_IA.md` para el detalle de la implementación del route handler `/api/scan`.
