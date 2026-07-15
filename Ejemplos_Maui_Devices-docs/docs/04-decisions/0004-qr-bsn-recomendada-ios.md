---
doc_id: ADR-0004
doc_type: adr
title: QR — BSN (Apple Vision) como opción recomendada frente a BSM (ML Kit) en iOS
version: 1.0.0
status: draft
origin: reverse-engineered
confidence: high
owner: fernandofilipuzzi
last_review: 2026-07-15
review_cycle_days: 0
audience: [dev]
classification: uso-interno
traces: [PIECE-QR-001, ADR-0006]
---

# ADR-0004: QR — BSN (Apple Vision) como opción recomendada frente a BSM (ML Kit) en iOS

> Decisión **reconstruida** desde el análisis comparativo del propio repo (`Docs/qr-nuget/04-recomendacion.md`) y los ajustes de CI que exigió BSM.

## Contexto

El dominio QR compara 4 librerías (BSM · BSN · CS · ZN). BSM (`BarcodeScanner.Mobile.Maui`, basada en Google ML Kit) funciona, pero ML Kit **no publica slice de simulador arm64 para iOS**: compilar el simulador exige RID `iossimulator-x64`, runtime universal y Rosetta 2 en el runner (`Docs/otros/propuesta-rosetta.md`), además de dependencias extra (`GoogleUtilities`).

## Decisión

Adoptar **BSN (Apple Vision)** como opción recomendada para iOS: usa el framework nativo de visión de Apple, sin dependencias de Google ni requisitos de Rosetta en simulador. BSM se mantiene como ejemplo comparativo con su CI adaptado.

## Alternativas

- **BSM (ML Kit)**: mayor ecosistema Google, pero costo de CI/simulador documentado; los cambios Rosetta ya están incorporados en `cd-ios-qr.BSM.*.yml`.
- **CS (Camera.MAUI) y ZN (ZXing.Net.Maui)**: decodificadores multiplataforma; comparados en la [pieza QR](../pieces/qr/README.md).

## Consecuencias

- (+) Pipeline iOS de BSN sin pasos especiales; menos superficie de fallo.
- (–) BSN es específica del stack Apple para el motor de visión; en Android las cuatro opciones siguen siendo válidas y la comparativa de la pieza QR guía la elección.

## Evidencia

- Recomendación del origen: `Ejemplos_Devices/Docs/qr-nuget/04-recomendacion.md` · propuesta Rosetta: `Ejemplos_Devices/Docs/otros/propuesta-rosetta.md`
- ia-db: [índice 02 — QR](../../../ia-db/indexes/02_QR.md) · [índice 10 §3](../../../ia-db/indexes/10_Documentacion-Transversal.md)
- Pieza: [qr](../pieces/qr/README.md) · CI: [runbook](../07-operations/build-and-run.md)
