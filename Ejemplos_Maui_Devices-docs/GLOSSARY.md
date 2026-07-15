---
doc_id: GLO-001
doc_type: glossary
title: Glosario — vocabulario controlado
version: 0.1.0
status: draft
origin: reverse-engineered
confidence: high
owner: fernandofilipuzzi
last_review: 2026-07-14
review_cycle_days: 365
audience: [dev, qa, agentes-automaticos]
classification: uso-interno
traces: []
---

# Glosario — `Ejemplos_Maui_Devices`

> **Resumen ejecutivo.** Vocabulario controlado de la solución (Marco §12.3). Toda la documentación —humana y máquina-legible— usa estos términos canónicos; los sinónimos se registran como *alias*. Un término se define aquí una sola vez y el resto de los documentos lo enlazan.

| Término canónico | Alias | Definición | Dónde se ve |
|---|---|---|---|
| **Servicio tipado** | device service | Interfaz `I*Service` que encapsula el acceso a un dispositivo de plataforma detrás de un contrato .NET testeable | Todos los dominios; consolidado en `LibApp/Devices/` de la híbrida |
| **Overlay MVVM** | overlay | `ContentView`/página superpuesta con su `ViewModel` (CommunityToolkit.Mvvm) que presenta la UI del dispositivo sin abandonar la página anfitriona | Variantes `*_Dialog` de QR e impresión |
| **Puente de comandos por URL** | url-command bridge | Mecanismo de la app híbrida: el `WebView` intercepta navegaciones y despacha la acción nativa al primer `IUrlCommandHandler` que coincide con la URL | `Ejemplo_Maui_Hibrida` |
| **App híbrida integrada** | híbrida | `Ejemplo_Maui_Hibrida`: consolida todos los dispositivos detrás de un `WebView` con backend Blazor de apoyo | Dominio Integrada |
| **ESC/POS** | — | Protocolo de comandos de impresoras térmicas (Epson Standard Code); se envía por Bluetooth SPP | Dominio Printer |
| **SPP** | Serial Port Profile | Perfil Bluetooth clásico que emula un puerto serie; transporte hacia la impresora térmica 58 mm | Dominio Printer |
| **Motor DSL** | MotorDsl | Paquetes `MotorDsl.*`: motor que transforma una plantilla JSON de comprobante en raster ESC/POS listo para imprimir | `Ejemplo_MotorDSL*`, híbrida |
| **Normalización EXIF** | — | Corrección de la rotación declarada en metadatos EXIF + redimensionado + recompresión JPEG (SkiaSharp) para obtener imágenes consistentes | Camera (`*_Normalizacion`) |
| **Transferencia por callback** | — | Devolución del resultado del dispositivo mediante delegado/evento registrado por el llamador | Camera, variantes `Callback` |
| **Transferencia por Task** | TCS | Devolución del resultado como `Task` completada vía `TaskCompletionSource` (permite `await`) | Camera `*_Task`, QR `*_Dialog` |
| **Geocodificación inversa** | reverse geocoding | Traducción de coordenadas (lat, lon) a dirección postal legible | Dominio GPS |
| **Firma ad-hoc** | ad-hoc signing | Firma de iOS sin certificado de distribución, usada por los pipelines CI para compilar sin publicar | Workflows `cd-ios-*` |
| **Workload (MAUI)** | — | Componente del SDK .NET (`android`, `ios`) requerido para compilar targets móviles | CI y setup local |
| **ia-db** | base de conocimiento | Índices navegables del origen que los agentes consultan antes de releer fuentes | [ia-db](../ia-db/README.md) |

## Términos del dominio Integrada (puente y backend)

| Término canónico | Alias | Definición | Dónde se ve |
|---|---|---|---|
| **Canal A** | interactividad Blazor | Circuito SignalR de Blazor Interactive Server que sostiene el render y los `@onclick` de la web dentro del WebView | [bridge-contract](docs/pieces/integrada/bridge-contract.md) |
| **Canal B** | puente de dispositivos | El puente de comandos por URL: navegación interceptada que dispara acciones nativas | [bridge-contract](docs/pieces/integrada/bridge-contract.md) |
| **Marcador de URL** | `clave=clave` | Convención de query string que identifica un comando del puente (p. ej. `action=print`) | [catálogo de APIs](docs/05-apis/catalog.md) |
| **`IUrlCommandHandler`** | handler de comando | Contrato `CanHandle(url)` + `HandleAsync(url)`; un handler por comando | [pieza integrada](docs/pieces/integrada/README.md) |
| **`UrlCommandDispatcher`** | dispatcher | Despacha al primer handler cuyo `CanHandle` coincide (*first-match-wins*); orden de registro = prioridad | [ADR-0003](docs/04-decisions/0003-puente-webview-comandos-url.md) |
| **`BridgeOutcome`** | — | `record(CancelNavigation, NavigateTo?)`: cómo termina un comando (JS, re-navegación o solo efecto nativo) | [bridge-contract](docs/pieces/integrada/bridge-contract.md) |
| **Overlay de estado** | `StatusOverlayViewModel` | Base MVVM común de los overlays de dispositivo: estados `None/Busy/Error` + `OverlayAction` | [pieza integrada](docs/pieces/integrada/README.md) |
| **Relay REST con allowlist** | `ApiRelayService` | Reenvío HTTP del comando `sendApi` restringido a hosts permitidos (`geolocate.somee.com`) y verbos GET/POST | [catálogo de APIs](docs/05-apis/catalog.md) |
| **`PrintDocument` / `PrintNode` / `N`** | DSL de impresión | Árbol JSON declarativo del comprobante (`N` es la fábrica de nodos) que MotorDSL rasteriza a ESC/POS | [pieza printer](docs/pieces/printer/README.md) |
| **`DeviceProfile`** | perfil de impresión | Configuración del render (p. ej. `58HB6`, 32 columnas, `escpos-bitmap`) | [pieza integrada](docs/pieces/integrada/README.md) |

## Candidatos pendientes de definición

> Términos detectados que aún requieren definición canónica validada por un humano.

- **`AsyncRelayCommand` didáctico** — la implementación propia (~50 líneas) vs. la de CommunityToolkit; documentada en [06-crosscutting](docs/01-architecture/06-crosscutting.md).
- **Firma ad-hoc de iOS** — ya definida arriba; validar el término preferido con el equipo (¿«ad-hoc» o «sin distribución»?).
