---
doc_id: ADR-0006
doc_type: adr
title: CI de iOS con firma ad-hoc en macOS; ejecución local en Android físico
version: 1.0.0
status: draft
origin: reverse-engineered
confidence: high
owner: fernandofilipuzzi
last_review: 2026-07-15
review_cycle_days: 0
audience: [dev, ops]
classification: uso-interno
traces: [OPS-RB-001, ADR-0001]
---

# ADR-0006: CI de iOS con firma ad-hoc en macOS; ejecución local en Android físico

> Decisión **reconstruida** desde los 18 workflows `cd-ios-<cat>.<Ejemplo>.yml` y los scripts `Ejemplos_Devices/scripts/*.bat`.

## Contexto

Los ejemplos tienen dos targets (`net10.0-android`, `net10.0-ios`), pero compilar iOS exige macOS/Xcode, que el entorno de desarrollo local (Windows) no tiene. A la vez, el valor didáctico está en ver los ejemplos corriendo sobre hardware real.

## Decisión

Partir el ciclo por plataforma:

- **Android**: build y despliegue **local** a dispositivo físico vía scripts `.bat` (`dotnet build -t:Run`), sin CI.
- **iOS**: build **solo en CI** (`macos-15`), un workflow por ejemplo publicable, con **firma ad-hoc** (sin cuenta de distribución) y validación por simulador con captura GIF como evidencia.

## Alternativas

- **Un único workflow parametrizado por ejemplo**: menos duplicación, pero cada ejemplo tiene particularidades (ApiKeys en GPS/Maps, Rosetta/x64 en BSM) que hoy se resuelven por copia especializada del YAML.
- **CI también para Android**: los APK se generan y prueban localmente en segundos; el pipeline no aportaría evidencia adicional al objetivo didáctico.

## Consecuencias

- (+) Se compila iOS sin poseer Mac; la firma ad-hoc evita gestionar certificados de distribución.
- (+) El GIF de simulación en el workflow deja evidencia visual de que el ejemplo corre.
- (–) 18 YAML casi idénticos que divergen con el tiempo (las variantes ApiKeys/Rosetta ya son especializaciones) — deriva vigilada en el [runbook](../07-operations/build-and-run.md).
- (–) Sin distribución instalable de iOS (no hay TestFlight/tienda): gap declarado del perfil `mobile-app`.

## Evidencia

- ia-db: [índice 09 — CI/CD y build](../../../ia-db/indexes/09_CI-CD-y-Build.md)
- Runbook: [build-and-run](../07-operations/build-and-run.md) · Justificación Rosetta: `Ejemplos_Devices/Docs/otros/propuesta-rosetta.md`
