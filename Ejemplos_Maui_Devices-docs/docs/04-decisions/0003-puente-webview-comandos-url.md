---
doc_id: ADR-0003
doc_type: adr
title: Puente WebView ↔ nativo por comandos en la URL
version: 1.0.0
status: draft
origin: reverse-engineered
confidence: medium
owner: fernandofilipuzzi
last_review: 2026-07-15
review_cycle_days: 0
audience: [dev, arquitectos]
classification: uso-interno
traces: [ADR-0002, PIECE-INT-001, API-BRIDGE-001]
---

# ADR-0003: Puente WebView ↔ nativo por comandos en la URL

> Decisión **reconstruida** desde la implementación de `Ejemplo_Maui_Hibrida` (confidence media: el detalle fino del despacho se documenta en el [contrato del puente](../pieces/integrada/bridge-contract.md)).

## Contexto

La app híbrida presenta una web (backend Blazor) dentro de un `WebView`, pero necesita capacidades nativas (cámara, QR, impresión, GPS, llamada) que el contenido web no puede invocar directamente.

## Decisión

La web solicita acciones nativas **navegando a URLs convencionales**; la app intercepta la navegación del `WebView` y despacha al **primer `IUrlCommandHandler` cuya condición coincide** con la URL. Cada handler resuelve un dispositivo usando los servicios de `LibApp/Devices/` (ADR-0002) y devuelve el resultado a la web.

## Alternativas

- **Bridge JavaScript (`EvaluateJavaScriptAsync` + objeto inyectado)**: canal bidireccional más rico, pero acoplado a cada plataforma de WebView y más difícil de demostrar didácticamente.
- **HybridWebView de .NET MAUI (mensajería integrada)**: componente más nuevo; la solución demuestra el patrón portable por URL, que funciona con cualquier contenido web sin tocar su JavaScript.

## Consecuencias

- (+) El contenido web queda desacoplado: cualquier página que sepa construir la URL convenida puede disparar acciones nativas.
- (+) Agregar un comando = agregar un handler y registrarlo (punto de extensión documentado en la [pieza integrada](../pieces/integrada/README.md)).
- (–) El «contrato» vive en convenciones de URL no tipadas: sin validación estática, las divergencias web↔app solo aparecen en runtime — mitigado documentándolo en [bridge-contract](../pieces/integrada/bridge-contract.md).
- (–) El orden de registro de handlers importa (gana el primero que coincide).

## Evidencia

- ia-db: [índice 08 — App híbrida integrada](../../../ia-db/indexes/08_App-Hibrida-Integrada.md)
- Pieza: [integrada](../pieces/integrada/README.md) · [contrato del puente](../pieces/integrada/bridge-contract.md)
