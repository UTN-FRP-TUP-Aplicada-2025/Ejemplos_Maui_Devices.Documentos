---
doc_id: OV-VIS-001
doc_type: vision
title: Visión y alcance
version: 1.0.0
status: draft
origin: reverse-engineered
confidence: high
owner: fernandofilipuzzi
last_review: 2026-07-14
review_cycle_days: 365
audience: [dev, arquitectos]
classification: uso-interno
traces: []
---

# Visión y alcance — `Ejemplos_Maui_Devices`

> **Resumen ejecutivo.** `Ejemplos_Maui_Devices` es un repositorio de **ejemplos didácticos de acceso a dispositivos desde .NET MAUI**. Sirve a desarrolladores que necesitan integrar hardware y servicios de plataforma (cámara, QR, impresora térmica, GPS, mapas, telefonía, red) y quieren **comparar enfoques de implementación** antes de elegir uno. No es una aplicación de producción.

## Qué es y para qué sirve

Cada dispositivo de plataforma se presenta como uno o varios proyectos MAUI **mínimos, autocontenidos y comparables**. La variación entre proyectos de un mismo dominio es deliberada y constituye el objeto de estudio:

| Eje de comparación | Ejemplo concreto |
|---|---|
| API nativa vs. control propio | `MediaPicker` vs. `MiMediaPicker` (Camera) |
| Callback vs. `Task` | `*_Callback` vs. `*_Task` (Camera) |
| Página embebida vs. diálogo | `X.LectorQR` vs. `X.LectorQR_Dialog` (QR) |
| Librería A vs. librería B | BSM · BSN · CS · ZN (QR) |
| Protocolo directo vs. motor | ESC/POS crudo vs. `MotorDsl.*` (Printer) |

Sobre los ejemplos aislados, la **app híbrida integrada** (`Ejemplo_Maui_Hibrida`) demuestra cómo consolidar todos los dispositivos detrás de un `WebView` con un **puente de comandos por URL** y un backend Blazor de apoyo — el paso de «técnica aislada» a «arquitectura de aplicación».

## Audiencia y stakeholders

| Rol | Qué obtiene de la solución |
|---|---|
| Desarrollador MAUI (principal) | Técnica de integración lista para copiar/adaptar, con las alternativas ya contrastadas |
| Arquitecto | Patrón de referencia: servicio tipado + overlay MVVM; arquitectura híbrida WebView↔nativo |
| QA | Flujos de dispositivo narrados de punta a punta (ver [runtime views](../01-architecture/04-runtime-views.md)) |
| Docente/formador | Material de comparación de enfoques con proyectos mínimos ejecutables |

> **Nota (origin: reverse-engineered, confidence: media):** los stakeholders se infieren del carácter didáctico del repositorio; no hay documento de visión original en el origen.

## Alcance

- **Dentro**: Android como plataforma principal de ejecución local; iOS vía CI (build/simulación); los 8 dominios de dispositivo del [mapa del sistema](system-map.md).
- **Fuera**: Windows/macOS como targets productivos, publicación en tiendas, hardening de producción, backend real (el Blazor de apoyo simula comprobantes, geo y pagos).

## Preguntas guía

- ¿El ejemplo que voy a copiar es la variante recomendada para mi caso (p. ej. BSN para iOS en QR)? → ver comparativas en cada [pieza](../pieces/).
- ¿Necesito la técnica aislada o la arquitectura consolidada de la híbrida? → ver [contexto C4](../01-architecture/01-context.md).

## Referencias

- [Mapa del sistema](system-map.md) — inventario completo de piezas
- ia-db: [índice maestro](../../../ia-db/indexes/00_MASTER-INDEX.md)
