---
doc_id: ADR-0005
doc_type: adr
title: Impresión térmica — motor DSL (JSON → raster) sobre ESC/POS directo
version: 1.0.0
status: draft
origin: reverse-engineered
confidence: high
owner: fernandofilipuzzi
last_review: 2026-07-15
review_cycle_days: 0
audience: [dev]
classification: uso-interno
traces: [PIECE-PRN-001, ADR-0002]
---

# ADR-0005: Impresión térmica — motor DSL (JSON → raster) sobre ESC/POS directo

> Decisión **reconstruida**: el repo implementa ambos enfoques y la app híbrida adopta el motor (`MotorDsl.*`), lo que evidencia la elección.

## Contexto

Imprimir un comprobante en una térmica Bluetooth 58 mm admite dos caminos: enviar comandos **ESC/POS crudos** (control total, acoplado al protocolo y al modelo de impresora) o describir el comprobante en un **DSL JSON** que un motor transforma en raster ESC/POS.

## Decisión

Usar los paquetes **`MotorDsl.*`** como capa de composición: la app describe el comprobante (plantilla JSON), el motor lo rasteriza y el servicio Bluetooth (SPP) lo transporta. `Ejemplo_ThermalPrinter` conserva el enfoque directo como línea base comparativa.

## Alternativas

- **ESC/POS directo** (`Ejemplo_ThermalPrinter`): sin dependencia externa y útil para entender el protocolo; frágil ante variaciones de firmware y tipografías, y el layout se programa a mano.
- **Librerías de terceros tipo ESCPOS_NET**: documentadas en el material del origen (`Ejemplo_Docs_Printer/`), no adoptadas como camino principal.

## Consecuencias

- (+) El layout del comprobante es **datos** (JSON versionable) y no código; el mismo template sirve en los ejemplos aislados y en la híbrida.
- (+) El raster evita depender del juego de fuentes/páginas de código de cada impresora.
- (–) Dependencia de paquetes externos `MotorDsl.*` con **versiones divergentes** en el repo: `Ejemplo_MotorDSL` en 1.0.12; `Ejemplo_MotorDSL_Dialog` y la app híbrida en 1.0.13 (el origen avanzó a `fd6a1ed` durante la documentación) — deriva declarada en el [GAP-REPORT](../../GAP-REPORT.md).
- (–) Rasterizar es más pesado que texto plano ESC/POS (más bytes por ticket vía SPP).

## Evidencia

- Pieza: [printer](../pieces/printer/README.md) (comparativa y snippets con procedencia)
- ia-db: [índice 03 — Impresión térmica](../../../ia-db/indexes/03_Impresion-Termica.md) · [índice maestro §2 nota de versiones](../../../ia-db/indexes/00_MASTER-INDEX.md)
