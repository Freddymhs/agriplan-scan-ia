# DECISION: Persistencia local con Dexie (IndexedDB), no Supabase

**Fecha:** 2026-03-28
**Status:** ✅ Vigente

## Contexto

`agriplan-scan-ia` registra:

- **Plant**: cada planta del usuario
- **Scan**: cada foto escaneada (puede pesar varios MB)
- **ScanResult**: la respuesta de la IA (label, score, category, recomendación)
- **Alert**: alertas generadas a partir de scans

La pregunta es **dónde guardar estos datos**. Las opciones son:

1. **Supabase** (PostgreSQL + Storage) — igual que la PWA principal
2. **IndexedDB local** con Dexie.js — base de datos del navegador
3. **localStorage** simple — solo strings, sin queries

## Decisión

Usar **IndexedDB local vía Dexie.js**. Sin backend de persistencia.

## Por qué

- **Privacidad del usuario**: las fotos de su huerto no salen del dispositivo. No hay servidor que almacene imágenes potencialmente identificables
- **Cero costo de infra**: no hay quota de Supabase Storage, ni cron de limpieza, ni gestión de buckets
- **Funciona offline por diseño**: si el usuario está sin conexión, sigue navegando su historial sin degradación. Solo el scan IA en sí requiere red
- **Quota generosa del navegador**: IndexedDB permite cientos de MB sin pedir permiso al usuario (en la mayoría de browsers modernos)
- **Caso de uso es individual, no compartido**: a diferencia de la PWA principal, acá no hay multi-usuario ni colaboración. Cada usuario tiene su historial privado
- **Dexie sobre IndexedDB crudo**: API más limpia, soporta promesas nativas, queries con `where`, esquema versionado con migraciones

## Trade-offs aceptados

- **El usuario pierde sus datos si cambia de navegador o limpia caché**: no hay sync entre dispositivos. **Mitigación futura**: implementar export/import JSON manual si los usuarios lo piden
- **Imágenes pueden ocupar mucho espacio** si el usuario escanea cientos: necesitaremos política de retención automática (ej. borrar scans > 6 meses) o avisarle cuando se acerque al límite
- **Sin backup automático**: si el dispositivo se rompe, datos perdidos. **Aceptado**: el valor del producto es el diagnóstico inmediato, no el historial perenne

## Alternativas descartadas

| Alternativa                     | Por qué no                                                                                            |
| ------------------------------- | ----------------------------------------------------------------------------------------------------- |
| Supabase                        | Sobrecargado para datos personales no compartidos; costo storage; problemas de privacidad de imágenes |
| Firebase Firestore              | Mismo problema + lock-in con Google                                                                   |
| localStorage                    | Sin queries, sin schemas, sin soporte para blobs grandes (imágenes)                                   |
| WebSQL                          | Deprecado                                                                                             |
| Sin persistencia (solo memoria) | Cada refresh pierde todo — inviable                                                                   |

## Implementación

Ver `FASE_1_TIPOS_Y_DB.md` para el detalle del schema Dexie y las interfaces TypeScript.
