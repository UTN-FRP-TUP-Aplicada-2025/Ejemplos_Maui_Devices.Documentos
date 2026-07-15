---
doc_id: ADR-0002
doc_type: adr
title: Patrón por dispositivo — servicio tipado + overlay MVVM
version: 1.0.0
status: draft
origin: reverse-engineered
confidence: high
owner: fernandofilipuzzi
last_review: 2026-07-15
review_cycle_days: 0
audience: [dev, arquitectos]
classification: uso-interno
traces: [ADR-0001]
---

# ADR-0002: Patrón por dispositivo — servicio tipado + overlay MVVM

> Decisión **reconstruida** desde el patrón recurrente en los dominios (Camera, Printer, Phone, QR `_Dialog`) y su consolidación en `LibApp/Devices/` de la app híbrida.

## Contexto

Cada dispositivo de plataforma (cámara, impresora, GPS…) mezcla tres preocupaciones: la API nativa, el estado de la interacción (permisos, progreso, error) y la UI que la presenta. Sin una separación estable, cada ejemplo la resolvería distinto y no habría nada reutilizable hacia la app integradora.

## Decisión

Separar cada dispositivo en dos mitades:

1. **Servicio tipado** — interfaz `I*Service` (p. ej. `IThermalPrinterService`) registrada en DI (`MauiProgram`), que encapsula la API de plataforma detrás de un contrato .NET.
2. **Overlay MVVM** — `ContentView`/página superpuesta con `ViewModel` (`CommunityToolkit.Mvvm`) que maneja permisos, progreso y resultado sin abandonar la pantalla anfitriona (variantes `*_Dialog`).

La app híbrida reutiliza la misma separación consolidando los servicios bajo `LibApp/Devices/`.

## Alternativas

- **Code-behind directo contra la API nativa**: menos capas, pero nada testeable ni reutilizable; los ejemplos más antiguos del dominio Camera muestran la evolución desde este enfoque.
- **Plugin/paquete compartido entre ejemplos**: contradice ADR-0001 (cada ejemplo autocontenido y copiable).

## Consecuencias

- (+) El consumidor de un ejemplo copia el par servicio+overlay a su app con cambios mínimos.
- (+) El contraste callback vs. `Task` (`TaskCompletionSource`) queda aislado en la firma del servicio — es una lección en sí misma (Camera, QR `_Dialog`).
- (–) Duplicación deliberada de código entre ejemplos hermanos (costo aceptado del enfoque didáctico).

## Evidencia

- Piezas: [camera](../pieces/camera/README.md) · [printer](../pieces/printer/README.md) · [phone](../pieces/phone/README.md) · [qr](../pieces/qr/README.md)
- ia-db: [índice maestro §6](../../../ia-db/indexes/00_MASTER-INDEX.md)
