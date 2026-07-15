---
doc_id: GAP-001
doc_type: gap-report
title: Reporte de gap documental y derivas
version: 1.0.0
status: draft
origin: reverse-engineered
confidence: high
owner: fernandofilipuzzi
last_review: 2026-07-15
review_cycle_days: 90
audience: [dev, arquitectos, agentes-automaticos]
classification: uso-interno
traces: []
---

# Reporte de gap documental y derivas

> **Resumen ejecutivo.** Qué quedó documentado, qué falta (y por qué), y qué contradicciones entre fuentes se detectaron. Es el complemento honesto del [manifiesto](docs-manifest.yaml): el manifiesto dice qué *debería* existir; este reporte dice qué *existe*, qué se *omitió con criterio* y qué necesita *decisión humana*. Todo el corpus está en `status: draft`; la promoción a `approved` es humana (Marco §12.4).

## 1. Ancla de versión del corpus

| Dato | Valor |
|---|---|
| Commit indexado por la ia-db | `24d611d` («iniciando repositorio») |
| Commit del origen al cerrar la documentación | `fd6a1ed` («fix(hibrida): evita doble navegación en pull-to-refresh y agrega overlay de impresión») |
| Estampas de procedencia en snippets | `@24d611d` (coherentes con la ia-db) |

**Deriva D-01 (avance del origen durante la documentación).** El origen avanzó un commit entre el inicio y el cierre. `fd6a1ed` modificó 12 archivos; los relevantes para el corpus:

| Archivo cambiado por `fd6a1ed` | Impacto en la doc | Acción |
|---|---|---|
| `Printer/Ejemplo_MotorDSL_Dialog.csproj` | Bump `MotorDsl.*` 1.0.12 → **1.0.13** | **Corregido** en pieza Printer, ADR-0005 e ia-db (índices 00/03) — «el código manda» |
| `Integrada/…/PrintCommandHandler.cs` | Líneas pueden diferir de las citadas `@24d611d` | Re-verificar líneas de los snippets de Integrada antes de `approved` |
| `Integrada/…/UrlCommandDispatcher.cs` | Ídem | Ídem |
| `Integrada/…/ViewModels/MainViewModel.cs` | Ídem | Ídem |
| `Integrada/…/Platforms/Windows/*` | Eliminación de la plataforma Windows | Coherente con la doc (target Android/iOS, sin Windows) |
| `Integrada/…/AndroidManifest.xml`, `MainPage.xaml`, `CHANGELOG.md` | Cambios menores | Sin contradicción con la doc de permisos consolidada |

> Los snippets `.cs` del resto de las piezas (Camera, QR, GPS, Maps, Phone, Red, Printer) **no** se ven afectados: `fd6a1ed` no tocó sus fuentes.

## 2. Gap documental (piezas requeridas que no se generan, con justificación)

| Requerido por perfil | Estado | Motivo |
|---|---|---|
| `02-requirements/` (SRS, RF/RNF, matriz de trazabilidad) | **Omitido** | Colección didáctica sin requisitos formales reconstruibles con evidencia (Marco §9: no inventar) |
| `03-data/` (diccionario, ER dbml, fixtures §14/§15) | **No aplica** | La solución no posee base de datos; no se proveyeron `{conexiones}` |
| `06-security-compliance/threat-model.md` · `regulatory-mapping.md` | **Omitido** | No regulado (`solution.regulated: false`); las notas de seguridad viven en [01-architecture/06-crosscutting](docs/01-architecture/06-crosscutting.md) |
| `openapi.yaml` versionado (`Ejemplo_ws_Blazor`) | **Gap parcial** | La spec se genera en runtime (`AddOpenApi`) y se sirve en `/openapi/v1.json`; no hay YAML en el repo. Contrato efectivo documentado en el [catálogo de APIs](docs/05-apis/catalog.md) y el [bridge-contract](docs/pieces/integrada/bridge-contract.md) |
| Guía de release por store (perfil `mobile-app`) | **Omitido** | Los ejemplos no se publican en tiendas; iOS solo se compila en CI con firma ad-hoc ([ADR-0006](docs/04-decisions/0006-ci-ios-adhoc-android-local.md)) |
| `05-deployment.md`, `03-components.md` (C4-L3) | **Omitido** | Sin infraestructura de despliegue propia; C4-L1/L2 cubren la arquitectura (arc42: omisión explícita) |

## 3. Derivas y hallazgos por dominio (para decisión humana)

### 3.1 Seguridad — requiere acción

| ID | Hallazgo | Fuente | Acción recomendada |
|---|---|---|---|
| **S-01** | **API key de Google Maps hardcodeada y versionada** en el `AndroidManifest.xml` de `Maps/Ejemplo_Maui_Mapas` (valor **no reproducido** en la doc). El historial de git la conserva. | [pieza maps — Observaciones](docs/pieces/maps/README.md) · [ADR-0007](docs/04-decisions/0007-secretos-fuera-del-repo.md) | Rotar la key en Google Cloud, restringirla (package + SHA-1) y migrarla al mecanismo plantilla+secrets del propio repo |

### 3.2 Bugs latentes detectados (documentados, no corregidos — el origen es solo lectura)

| ID | Hallazgo | Fuente |
|---|---|---|
| B-01 | GPS: el setter de `Domicilio` escribe sobre `_coordenadas` (backing field equivocado); el label de domicilio nunca muestra la dirección | [pieza gps](docs/pieces/gps/README.md) |
| B-02 | Camera (ej. 2): llama `IsCaptureSupported` sin declarar el intent `IMAGE_CAPTURE` en `<queries>` → posible falso negativo en Android 11+ | [pieza camera](docs/pieces/camera/README.md) |
| B-03 | Camera (ej. 1): inyección por tipo concreto `CamaraService` en vez de `ICamaraService`; `using Android.Media;` sin `#if` en archivo compartido | [pieza camera](docs/pieces/camera/README.md) |
| B-04 | Printer (no-Dialog): permisos con carrera (`RequestPermissions` sin await; `Task.Delay(3000)` fijo) | [pieza printer](docs/pieces/printer/README.md) |
| B-05 | Red: `ConnectivityTest` no se instancia en ningún lado; UI vacía; desuscripción vía finalizador (poco fiable) | [pieza red](docs/pieces/red/README.md) |

### 3.3 Discrepancias índice ↔ código detectadas (drift de la ia-db)

| ID | Discrepancia | Resolución |
|---|---|---|
| DR-01 | Versión `MotorDsl.*` del Dialog (ver §1, deriva D-01) | Corregido: docs e ia-db a 1.0.13 |
| DR-02 | Maps: los `Readme.md` están **invertidos** respecto del índice 05 (el contenido vive en `Ejemplo_Maui_Mapas/Readme.md`, no en `Ejemplo_Docs_Maps/`) | Registrado en [pieza maps](docs/pieces/maps/README.md); pendiente corregir índice 05 de la ia-db |
| DR-03 | Phone (DirectCall): comentarios afirman `tel` en `LSApplicationQueriesSchemes` pero el `Info.plist` no lo contiene | Registrado en [pieza phone](docs/pieces/phone/README.md) |
| DR-04 | Integrada: doc-comments desactualizados (`tipoRequest` vs `httpMethod`; QR «no inyecta al cancelar» pero sí inyecta `[]`) | Registrado en [bridge-contract §Divergencias](docs/pieces/integrada/bridge-contract.md) |

### 3.4 Inconsistencias de higiene (menores, no bloquean)

Permisos/`<queries>` duplicados en el manifest consolidado de la híbrida; namespaces que no coinciden con su carpeta (Red: `Ejemplo_Maui_Conexion`; Camera ej.4/5); `GetQueryValue` duplicado en los 7 handlers; banners de scripts con copy-paste. Detalle en las secciones «Observaciones» de cada pieza y en el [runbook](docs/07-operations/build-and-run.md).

## 4. Puntos no verificados (declarados como tales)

- Bug de iOS del Canal A (SignalR en WKWebView sobre hosting gratuito): descripto en el material del origen (`Docs/web-hibrida/maui-hibrido.md`), **no reproducido** en este relevamiento.
- Relación entre los hosts `aplicada.somee.com` y `geolocate.somee.com`: presumiblemente dos publicaciones del mismo backend; **no verificable** desde el repo.

## 5. Cómo un agente calcula el gap

1. Leer [docs-manifest.yaml](docs-manifest.yaml) → `required_docs` por pieza y los `gaps` declarados.
2. Contrastar con el frontmatter de los `.md` bajo `docs/` (todos `status: draft`).
3. Este reporte enumera las excepciones justificadas; lo que no está aquí ni en el manifiesto y falta, es un gap real a reportar.

## Referencias

- [Manifiesto](docs-manifest.yaml) · [Mapa del sistema](docs/00-overview/system-map.md) · [ADRs](docs/04-decisions/)
- ia-db: [README](../ia-db/README.md)
