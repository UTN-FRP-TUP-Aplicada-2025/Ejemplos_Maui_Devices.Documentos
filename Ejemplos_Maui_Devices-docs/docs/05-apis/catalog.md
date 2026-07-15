---
doc_id: API-CAT-001
doc_type: api-catalog
title: Catálogo de APIs y contratos
version: 1.0.0
status: draft
origin: reverse-engineered
confidence: high
owner: fernandofilipuzzi
last_review: 2026-07-15
review_cycle_days: 180
audience: [dev, agentes-automaticos]
classification: uso-interno
traces: [API-BRIDGE-001, ADR-0003]
---

# Catálogo de APIs y contratos

> **Resumen ejecutivo.** Los contratos de la solución en un solo lugar: el puente de comandos por URL (el «API» que la web usa para pedir acciones nativas), los endpoints REST del backend Blazor y los servicios externos consumidos. El detalle parámetro a parámetro vive en el [contrato del puente](../pieces/integrada/bridge-contract.md); acá el inventario y su estado.

## 1. Puente de comandos por URL (WebView → nativo)

Contrato por convención de URL, sin tipado estático ([ADR-0003](../04-decisions/0003-puente-webview-comandos-url.md)). Orden = prioridad de despacho.

| # | Comando | Efecto nativo | Retorno a la web |
|---|---|---|---|
| 1 | `coordenadas=coordenadas` | Geolocalización con overlay | Re-navegación con `Latitud`/`Longitud` |
| 2 | `phone=phone` | Llamada directa | Solo overlay nativo |
| 3 | `photo=photo&param={id}` | Cámara + normalización | JS: base64 en `img#id` |
| 4 | `selfie=selfie&param={id}` | Cámara frontal con máscara | JS: base64 en `img#id` |
| 5 | `qr=qr&param={id}` | Lector QR modal (BSN) | JS: lecturas JSON en `#id.textContent` |
| 6 | `sendApi=sendApi&httpMethod=…&url=…&param={id}&body=…` | Relay REST (allowlist de hosts; solo GET/POST) | JS: `{ok,status,body}` en `#id` |
| 7 | `action=print` | Comprobante → MotorDSL → impresora BT | Solo overlay nativo |

- **Owner**: pieza `Ejemplo_Maui_Hibrida` · **Estado**: activo · **Detalle**: [bridge-contract](../pieces/integrada/bridge-contract.md)
- **Guardrails**: hosts del relay limitados por allowlist (`ApiRelayService`); resultados serializados con `System.Text.Json` (anti-XSS).

## 2. Backend `Ejemplo_ws_Blazor` — REST

| Endpoint | Método | Propósito | Consumidor |
|---|---|---|---|
| `/api/Tikects/comprobante` | GET | `PrintDocument` JSON (comprobante de ejemplo hardcodeado) | `PrintCommandHandler` (flujo de impresión) |
| `/api/GeoReporter/track` | POST | Recibe `LocationDto {Latitude, Longitude}` | Destino de prueba del relay `sendApi` |
| `/api/pagofake/pago` | POST | `302` cross-host (prueba de redirect en WebView) | Pruebas del Canal A/B |
| `/api/pagofake/pago-form` | GET | HTML con form auto-submit cross-host | Pruebas del Canal A/B |
| `/openapi/v1.json` · `/scalar` | GET | Especificación OpenAPI generada + UI Scalar | Documentación navegable |

- **Owner**: pieza `Ejemplo_ws_Blazor` · **Estado**: activo (backend de apoyo, datos simulados)
- **Contrato**: la especificación OpenAPI se **genera en runtime** (`AddOpenApi`); **no hay `openapi.yaml` versionado en el repo** → gap declarado en el [manifiesto](../../docs-manifest.yaml) y el [GAP-REPORT](../../GAP-REPORT.md). El contrato efectivo se consulta en `/openapi/v1.json` con el backend corriendo.
- **DTOs del comprobante** (`PrintDocument`, `PrintNode`, `PrintStyle`, `N`): definidos **por duplicado** (idénticos) en backend y app para desacoplarlos — divergencia potencial vigilada como deriva (ia-db [índice 08 §7.2](../../../ia-db/indexes/08_App-Hibrida-Integrada.md)).

## 3. Servicios externos consumidos

| Servicio | Usado por | Contrato/nota |
|---|---|---|
| Google Geocoding | pieza [gps](../pieces/gps/README.md) | API key vía mecanismo [ADR-0007](../04-decisions/0007-secretos-fuera-del-repo.md) |
| Google Maps (SDK Android) | pieza [maps](../pieces/maps/README.md) | Ídem; hallazgo de key hardcodeada reportado en la pieza |
| `geolocate.somee.com` | relay `sendApi` | Único host de la allowlist |

## Preguntas guía

- ¿Otro equipo puede integrarse al puente solo con el [bridge-contract](../pieces/integrada/bridge-contract.md)? Ese es el criterio de calidad del contrato (Marco §6).
- ¿Cambiaste un DTO del comprobante? Hay que tocarlo en **dos** lugares (backend y app) — o la impresión valida y falla.

## Referencias

- ia-db: [índice 08 §4, §7](../../../ia-db/indexes/08_App-Hibrida-Integrada.md)
- [Vistas de runtime](../01-architecture/04-runtime-views.md) — los contratos en acción
