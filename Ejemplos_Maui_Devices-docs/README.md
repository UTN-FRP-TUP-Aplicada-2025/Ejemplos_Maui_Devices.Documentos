---
doc_id: DOCS-README-001
doc_type: index
title: Ejemplos_Maui_Devices — índice maestro de documentación
version: 1.0.0
status: draft
origin: reverse-engineered
confidence: high
owner: fernandofilipuzzi
last_review: 2026-07-15
review_cycle_days: 180
audience: [dev, arquitectos, qa, agentes-automaticos]
classification: uso-interno
traces: []
---

# Ejemplos_Maui_Devices — documentación

> **Resumen ejecutivo.** Conjunto documental de `Ejemplos_Maui_Devices`, una colección de **ejemplos didácticos de acceso a dispositivos desde .NET MAUI** (cámara, QR, impresión térmica, GPS, mapas, telefonía, red) más una **app híbrida** que los integra tras un WebView con backend Blazor. Generado conforme al Marco de Documentación de Software: navegable por humanos desde este índice y operable por agentes desde [`docs-manifest.yaml`](docs-manifest.yaml) + el frontmatter de cada documento. Todo el corpus está en `status: draft`.

## Cómo está organizado

```
Ejemplos_Maui_Devices.Documentos/
├── ia-db/                     ← índices navegables del código (Fase 1; punto de entrada para agentes)
└── Ejemplos_Maui_Devices-docs/  ← este conjunto documental
    ├── README.md              ← este índice maestro
    ├── GLOSSARY.md            ← vocabulario controlado
    ├── docs-manifest.yaml     ← manifiesto máquina-legible (piezas, types, gaps)
    ├── GAP-REPORT.md          ← qué falta, qué se omitió y las derivas detectadas
    └── docs/
        ├── 00-overview/       ← visión + mapa del sistema
        ├── 01-architecture/   ← C4 contexto/contenedores, runtime views, transversales
        ├── 04-decisions/      ← 8 ADRs reconstruidos
        ├── 05-apis/           ← catálogo de contratos (puente URL + backend)
        ├── 07-operations/     ← runbook de build y ejecución
        ├── 08-onboarding/     ← setup del desarrollador
        └── pieces/            ← doc por dominio de dispositivo
```

## Navegación — «Necesito… → voy a…»

| Necesito… | Voy a… |
|---|---|
| Entender qué es la solución y para quién | [Visión](docs/00-overview/vision.md) |
| Ver todas las piezas y su tipo | [Mapa del sistema](docs/00-overview/system-map.md) |
| Ver cómo encaja todo y por qué | [Contexto C4](docs/01-architecture/01-context.md) → [Contenedores](docs/01-architecture/02-containers.md) |
| Seguir un flujo de punta a punta (imprimir, leer QR, GPS) | [Vistas de runtime](docs/01-architecture/04-runtime-views.md) |
| Entender el porqué de una decisión | [ADRs](docs/04-decisions/) |
| Integrar la web con lo nativo | [Catálogo de APIs](docs/05-apis/catalog.md) · [Contrato del puente](docs/pieces/integrada/bridge-contract.md) |
| Compilar y ejecutar los ejemplos | [Runbook](docs/07-operations/build-and-run.md) |
| Arrancar como dev nuevo | [Setup del desarrollador](docs/08-onboarding/developer-setup.md) |
| Saber qué falta o qué revisar | [GAP-REPORT](GAP-REPORT.md) |
| Buscar en el código sin leerlo todo | [ia-db](../ia-db/README.md) |

## Piezas por dominio

| Dominio | Qué demuestra | Documento |
|---|---|---|
| Cámara | Captura de foto: 5 variantes (MediaPicker vs. picker propio, callback vs. Task, normalización EXIF, selfie) | [pieces/camera](docs/pieces/camera/README.md) |
| QR | Escaneo: 4 librerías (BSM·BSN·CS·ZN) × 2 UIs (página/diálogo); recomendación fundada | [pieces/qr](docs/pieces/qr/README.md) |
| Impresión térmica | BT 58 mm: ESC/POS directo vs. motor DSL (JSON→raster) | [pieces/printer](docs/pieces/printer/README.md) |
| GPS | Geolocalización + geocodificación inversa | [pieces/gps](docs/pieces/gps/README.md) |
| Mapas | Control `Map` nativo con pines | [pieces/maps](docs/pieces/maps/README.md) |
| Telefonía | Marcador del sistema vs. llamada directa | [pieces/phone](docs/pieces/phone/README.md) |
| Red | Estado y cambios de conectividad | [pieces/red](docs/pieces/red/README.md) |
| Integrada | App híbrida WebView + puente de comandos por URL + backend Blazor | [pieces/integrada](docs/pieces/integrada/README.md) |

## Decisiones de arquitectura (ADRs)

| ADR | Decisión |
|---|---|
| [0001](docs/04-decisions/0001-un-proyecto-por-tecnica.md) | Un proyecto MAUI mínimo por técnica |
| [0002](docs/04-decisions/0002-servicio-tipado-overlay-mvvm.md) | Patrón servicio tipado + overlay MVVM por dispositivo |
| [0003](docs/04-decisions/0003-puente-webview-comandos-url.md) | Puente WebView ↔ nativo por comandos en la URL |
| [0004](docs/04-decisions/0004-qr-bsn-recomendada-ios.md) | QR: BSN (Apple Vision) recomendada frente a BSM (ML Kit) en iOS |
| [0005](docs/04-decisions/0005-impresion-motor-dsl.md) | Impresión: motor DSL (JSON→raster) sobre ESC/POS directo |
| [0006](docs/04-decisions/0006-ci-ios-adhoc-android-local.md) | CI de iOS con firma ad-hoc; ejecución local en Android físico |
| [0007](docs/04-decisions/0007-secretos-fuera-del-repo.md) | Secretos de build fuera del repo (plantilla + secrets de CI) |
| [0008](docs/04-decisions/0008-trust-anchors-embebidos-webview.md) | TLS en WebView Android: trust anchors embebidos |

## Estado y trazabilidad

- **Origen** (solo lectura): `../../Ejemplos_Maui_Devices/` · corpus anclado a `@24d611d`; el origen avanzó a `@fd6a1ed` durante la documentación (ver [GAP-REPORT §1](GAP-REPORT.md)).
- **Todo el corpus está en `draft`**: lo generó un agente por ingeniería inversa (`origin: reverse-engineered`); la promoción a `approved` es humana (Marco §12.4).
- **Lo inferido lleva `confidence`**; las contradicciones entre fuentes se **reportan**, no se resuelven en silencio (ver [GAP-REPORT §3](GAP-REPORT.md)).
- ⚠️ **Requiere acción humana**: hay una API key de Google Maps hardcodeada en el origen — ver [GAP-REPORT §3.1](GAP-REPORT.md).

## Para agentes

1. Leer [`docs-manifest.yaml`](docs-manifest.yaml): piezas, `type`, `required_docs` y `gaps` declarados.
2. Leer el frontmatter de los `.md` bajo `docs/` (IDs estables `PIECE-*`, `ARCH-*`, `ADR-*`, `API-*`, `OPS-*`).
3. Recuperar contexto de código desde la [ia-db](../ia-db/README.md) antes de releer fuentes.
4. El gap documental se calcula de `docs-manifest.yaml` + frontmatter + [GAP-REPORT](GAP-REPORT.md); no requiere leer prosa.
