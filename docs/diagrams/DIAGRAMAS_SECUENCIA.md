# Diagramas de Secuencia

Propósito: contratos críticos de interacción. Actualizar solo si cambia el flujo de negocio.

## Flujo 1: Escanear planta (MVP — online)

```mermaid
sequenceDiagram
    actor User as Agricultor
    participant PWA as AgriPlan Scan
    participant Hook as useScan
    participant API as /api/scan
    participant GPT as GPT-4o Vision
    participant DB as IndexedDB

    User->>PWA: Toma foto / sube imagen
    PWA->>PWA: compressImage (JPEG 80%, max 1024px)
    PWA->>Hook: scan(imageBlob)
    Hook->>Hook: Estado → scanning
    Hook->>API: POST FormData {image}
    API->>API: Validar (tamaño, tipo)
    API->>API: Blob → Base64
    API->>GPT: generateObject(schema, image, systemPrompt)

    alt Éxito
        GPT-->>API: ScanResult (JSON estructurado)
        API-->>Hook: Response.json(result)
        Hook->>DB: Guardar Scan + ScanResult
        Hook->>Hook: generateAlerts(scan, result)
        Hook->>DB: Guardar Alerts (si hay)
        Hook->>Hook: Estado → success
        Hook-->>PWA: Mostrar resultado
    else Timeout / Error
        GPT-->>API: Error / Timeout 10s
        API-->>Hook: Error response
        Hook->>Hook: Estado → error
        Hook-->>PWA: Mostrar mensaje error en español
    end
```

## Flujo 2: Gestionar plantas e historial

```mermaid
sequenceDiagram
    actor User as Agricultor
    participant PWA as AgriPlan Scan
    participant Hook as usePlants
    participant DB as IndexedDB

    User->>PWA: Ver historial
    PWA->>Hook: Dexie liveQuery (plantas)
    Hook->>DB: SELECT plants ORDER BY createdAt DESC
    DB-->>Hook: Plant[]
    Hook-->>PWA: Render lista

    User->>PWA: Tap en planta
    PWA->>Hook: getScans(plantId)
    Hook->>DB: SELECT scans WHERE plantId ORDER BY createdAt
    DB-->>Hook: Scan[]
    Hook-->>PWA: Render timeline

    User->>PWA: Tap en scan
    PWA->>PWA: Navegar a /scan/[id]
    PWA->>DB: getScan(id)
    DB-->>PWA: Scan + ScanResult + imageBlob
    PWA-->>User: Detalle completo
```

## Flujo 3: Alertas automáticas

```mermaid
sequenceDiagram
    participant Hook as useScan
    participant Util as generateAlerts()
    participant DB as IndexedDB
    participant UI as AlertCard

    Hook->>Hook: Scan completado (success)
    Hook->>DB: Obtener scans previos de la planta
    DB-->>Hook: Scan[] anteriores
    Hook->>Util: generateAlerts(scan, result, previousScans)

    alt Enfermedad detectada
        Util-->>Hook: Alert {type: disease_detected}
    end

    alt Progresión (misma enfermedad, mayor severidad)
        Util-->>Hook: Alert {type: progression}
    end

    Hook->>DB: Guardar Alert[]
    DB-->>UI: Dexie liveQuery actualiza badges
    UI-->>UI: Badge rojo/amarillo en planta
```
