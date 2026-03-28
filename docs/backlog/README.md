# Backlog — AgriPlan Scan IA

## Fases

| Fase | Nombre | Prioridad | Dependencias | Tareas |
|------|--------|-----------|--------------|--------|
| 0 | [Base del Proyecto](FASE_0_BASE.md) | Alta | — | 6 |
| 1 | [Tipos y DB Local](FASE_1_TIPOS_Y_DB.md) | Alta | FASE_0 | 4 |
| 2 | [Cámara e Imagen](FASE_2_CAMARA_E_IMAGEN.md) | Alta | FASE_0 | 4 |
| 3 | [Scan IA](FASE_3_SCAN_IA.md) | Alta | FASE_1, FASE_2 | 4 |
| 4 | [Historial y Plantas](FASE_4_HISTORIAL.md) | Alta | FASE_1, FASE_3 | 5 |
| 5 | [Alertas](FASE_5_ALERTAS.md) | Media | FASE_3, FASE_4 | 3 |
| 6 | [Settings y Polish](FASE_6_SETTINGS_Y_POLISH.md) | Media | FASE_4, FASE_5 | 6 |
| 7 | [Testing](FASE_7_TESTING.md) | Media | FASE_6 | 5 |

**Total: 8 fases, 37 tareas**

## Grafo de Dependencias

```
FASE_0 (Base)
├── FASE_1 (Tipos + DB)
│   ├── FASE_3 (Scan IA) ← también depende de FASE_2
│   │   ├── FASE_4 (Historial) ← también depende de FASE_1
│   │   │   ├── FASE_5 (Alertas) ← también depende de FASE_3
│   │   │   │   └── FASE_6 (Settings + Polish) ← también depende de FASE_4
│   │   │   │       └── FASE_7 (Testing)
├── FASE_2 (Cámara)
```

FASE_1 y FASE_2 pueden ejecutarse en paralelo (ambas dependen solo de FASE_0).

## Diagramas de Referencia

| Archivo | Tipo | Contenido |
|---------|------|-----------|
| [`../diagrams/DIAGRAMAS_COMPONENTES.md`](../diagrams/DIAGRAMAS_COMPONENTES.md) | Flowcharts | Topología del sistema |
| [`../diagrams/DIAGRAMAS_SECUENCIA.md`](../diagrams/DIAGRAMAS_SECUENCIA.md) | Sequences | Scan, historial, alertas |
| [`../diagrams/DIAGRAMAS_ESTADOS.md`](../diagrams/DIAGRAMAS_ESTADOS.md) | States | Ciclo de vida Scan y Alert |

Estos diagramas son referencia estructural estable. Las fases los referencian pero no los repiten. Actualizar solo ante cambios de topología, flujos de negocio o reglas de estado.

## Validación de Flujos

| Archivo | Propósito |
|---------|-----------|
| [`../flows.md`](../flows.md) | Flujos de usuario documentados manualmente. Guía para QA y E2E. |

Llenar después de probar cada feature. Usar como referencia con `/run-browser_test`.

## Decisiones Técnicas

Carpeta [`../decisions/`](../decisions/) — documentar aquí decisiones arquitectónicas cuando surjan durante el desarrollo.
Formato: `DECISION_[TEMA].md` — explica el POR QUÉ, no el QUÉ.
