# Diagramas de Componentes (Flowcharts)

Propósito: mapa estable del sistema. Actualizar solo si cambia topología.

## Contexto (alto nivel)

```mermaid
flowchart LR
    User([Agricultor])
    PWA[AgriPlan Scan PWA]
    RH[Route Handler /api/scan]
    GPT[OpenAI GPT-4o Vision]
    IDB[(IndexedDB / Dexie)]

    User -->|Foto| PWA
    PWA -->|FormData| RH
    RH -->|Base64 + prompt| GPT
    GPT -->|JSON estructurado| RH
    RH -->|ScanResult| PWA
    PWA -->|Persistir| IDB
    PWA -->|Leer historial| IDB
```

## Componentes principales

```mermaid
flowchart TB
    subgraph Cliente ["PWA (Browser)"]
        direction TB
        Camera[CameraCapture]
        Preview[ImagePreview]
        Scanner[useScan hook]
        Result[ScanResult]
        History[HistoryList]
        Plants[usePlants hook]
        Alerts[useAlerts hook]
        Store[Zustand Store]
    end

    subgraph Server ["Next.js Server"]
        RouteHandler["/api/scan\n(Route Handler)"]
    end

    subgraph External ["Servicios Externos"]
        OpenAI["OpenAI API\nGPT-4o Vision"]
    end

    subgraph Storage ["Almacenamiento Local"]
        Dexie["IndexedDB\n(Dexie.js)"]
    end

    Camera -->|Blob| Preview
    Preview -->|Blob| Scanner
    Scanner -->|FormData| RouteHandler
    RouteHandler -->|generateObject| OpenAI
    OpenAI -->|Structured JSON| RouteHandler
    RouteHandler -->|ScanResult| Scanner
    Scanner -->|Guardar| Dexie
    Scanner -->|Resultado| Result
    History -->|Leer| Dexie
    Plants -->|CRUD| Dexie
    Alerts -->|CRUD| Dexie
    Result -->|Crear alerta| Alerts
```
