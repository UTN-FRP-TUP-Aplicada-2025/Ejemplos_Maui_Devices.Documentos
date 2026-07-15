---
doc_id: ADR-0007
doc_type: adr
title: Secretos de build fuera del repositorio (plantilla ApiKeys + secrets de CI)
version: 1.0.0
status: draft
origin: reverse-engineered
confidence: high
owner: fernandofilipuzzi
last_review: 2026-07-15
review_cycle_days: 0
audience: [dev, ops]
classification: uso-interno
traces: [OPS-RB-001, PIECE-GPS-001, PIECE-MAP-001]
---

# ADR-0007: Secretos de build fuera del repositorio (plantilla ApiKeys + secrets de CI)

> Decisión **reconstruida** desde `.gitignore` (`**/Services/ApiKeys.cs`), la plantilla `ApiKeys.cs.template` (GPS) y los workflows que inyectan la clave desde secrets.

## Contexto

Algunos ejemplos requieren claves de servicios externos (Google Maps/Geocoding). Versionarlas en el repositorio las expondría públicamente y obligaría a rotarlas.

## Decisión

Las claves viven **fuera del repo**: `**/Services/ApiKeys.cs` está en `.gitignore`; cada desarrollador genera el archivo local desde la **plantilla** (`ApiKeys.cs.template`) y el CI lo materializa **en build** desde los **secrets** del repositorio.

## Alternativas

- **appsettings/variables de entorno en runtime**: válido en servicios; en una app móvil didáctica la clave debe quedar compilada, y el mecanismo plantilla+secret es el más simple de enseñar.
- **Gestor de secretos externo**: desproporcionado para ejemplos didácticos.

## Consecuencias

- (+) El repositorio puede ser público sin exponer claves; el mecanismo es reproducible (plantilla documentada en el [runbook](../07-operations/build-and-run.md)).
- (–) El primer build local falla hasta crear `ApiKeys.cs` — error esperado documentado en el runbook.
- (–) **Deriva detectada:** el mecanismo no se aplicó de forma uniforme — existe una API key de Google **hardcodeada** en el `AndroidManifest.xml` del ejemplo de Maps (hallazgo confirmado, valor no reproducido aquí; ver [pieza maps — Observaciones](../pieces/maps/README.md) y [GAP-REPORT](../../GAP-REPORT.md)). Requiere decisión humana: rotar la clave y migrar el ejemplo al mecanismo de plantilla.

## Evidencia

- `.gitignore` del origen (`**/Services/ApiKeys.cs`) · `Ejemplos_Devices/GPS/Ejemplo_Maui_GPS/Services/ApiKeys.cs.template`
- ia-db: [índice maestro §6](../../../ia-db/indexes/00_MASTER-INDEX.md) · [índice 09](../../../ia-db/indexes/09_CI-CD-y-Build.md)
