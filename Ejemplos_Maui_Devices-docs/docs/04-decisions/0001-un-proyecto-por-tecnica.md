---
doc_id: ADR-0001
doc_type: adr
title: Un proyecto MAUI mínimo por técnica de integración
version: 1.0.0
status: draft
origin: reverse-engineered
confidence: high
owner: fernandofilipuzzi
last_review: 2026-07-15
review_cycle_days: 0
audience: [dev, arquitectos]
classification: uso-interno
traces: [OV-MAP-001]
---

# ADR-0001: Un proyecto MAUI mínimo por técnica de integración

> Decisión **reconstruida** desde la estructura del repositorio (23 proyectos en 8 dominios, cada uno ilustrando una variante). No existe registro original de la decisión.

## Contexto

El repositorio enseña integración de dispositivos en .NET MAUI. Una misma necesidad (p. ej. «leer un QR») admite varias implementaciones (4 librerías × 2 formas de UI). Mezclar variantes en una sola app las volvería difíciles de comparar y de copiar de forma aislada.

## Decisión

Cada técnica vive en **su propio proyecto MAUI mínimo y autocontenido**: `{BSM,BSN,CS,ZN}.LectorQR[_Dialog]`, `Ejemplo_Photo_*`, `Ejemplo_MotorDSL*`, etc. La variación entre proyectos hermanos es el objeto de estudio; la app híbrida (`Ejemplo_Maui_Hibrida`) es la única pieza integradora.

## Alternativas

- **Una app «demo» con menú de técnicas**: menos proyectos, pero cada ejemplo dejaría de ser mínimo y copiable; las dependencias de todas las librerías convivirían en un mismo `.csproj` (conflictos como el de ML Kit en simulador iOS afectarían a todo).
- **Una rama de git por técnica**: comparación imposible lado a lado.

## Consecuencias

- (+) Cada ejemplo se compila, ejecuta y copia de forma independiente; comparación directa entre variantes.
- (+) Un fallo de librería (p. ej. BSM en simulador iOS) queda acotado a su proyecto y su pipeline.
- (–) Las versiones de paquetes divergen entre proyectos (MAUI 10.0.30/31/40/80; `MotorDsl.*` 1.0.12/1.0.13) — no hay gestión central de versiones.
- (–) 18 pipelines de CI casi idénticos (uno por ejemplo publicable) en vez de uno parametrizado — ver [ADR-0006](0006-ci-ios-adhoc-android-local.md).

## Evidencia

- Estructura de dominios y catálogo: [system-map](../00-overview/system-map.md) · ia-db [índice maestro §5–6](../../../ia-db/indexes/00_MASTER-INDEX.md)
