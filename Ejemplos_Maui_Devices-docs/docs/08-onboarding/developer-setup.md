---
doc_id: ONB-SETUP-001
doc_type: onboarding
title: Setup del desarrollador
version: 1.0.0
status: draft
origin: reverse-engineered
confidence: high
owner: fernandofilipuzzi
last_review: 2026-07-15
review_cycle_days: 180
audience: [dev]
classification: uso-interno
traces: [OPS-RB-001]
---

# Setup del desarrollador

> **Resumen ejecutivo.** Camino mínimo para que un desarrollador nuevo deje corriendo su primer ejemplo en un Android físico y sepa dónde está cada cosa. Los procedimientos completos de build/CI viven en el [runbook](../07-operations/build-and-run.md); acá solo el orden de los pasos y la orientación.

## 1. Prerrequisitos

| Requisito | Detalle | Verificación |
|---|---|---|
| .NET SDK 10 + workloads `android`/`ios` | Versiones exactas usadas en local y CI: ver [runbook §Prerrequisitos](../07-operations/build-and-run.md) | `dotnet sdk check` · `dotnet workload list` |
| 7-Zip | Requerido por el SDK de Android | — |
| Dispositivo Android físico | Con depuración USB habilitada (los scripts locales despliegan a dispositivo, no a emulador) | `adb devices` |
| VS Code (o Visual Studio) | El repo incluye `vs.bat` que abre VS Code en la raíz | — |

> Fuente de prerrequisitos: `Ejemplos_Devices/Docs/otros/GetEnviromentVersion.md` y su script pareja `Ejemplos_Devices/scripts/GetEnviromentVersion.bat`.

## 2. Primer ejemplo corriendo (10 minutos)

1. Clonar el repo y abrirlo: `vs.bat` (raíz).
2. Elegir un dominio **sin claves externas** para el primer intento — recomendado: [Red](../pieces/red/README.md) o [Phone/Dialer](../pieces/phone/README.md) (no requieren permisos especiales ni API keys).
3. Conectar el Android físico y ejecutar el script del ejemplo desde `Ejemplos_Devices/scripts/` (procedimiento, validaciones y errores comunes: [runbook §Ejecución local](../07-operations/build-and-run.md)).
4. Para GPS/Maps: antes del build crear `Services/ApiKeys.cs` desde la plantilla — ver [runbook §Gestión de secretos](../07-operations/build-and-run.md); el primer build sin ese archivo falla y es esperado.

## 3. Cómo navegar la solución

| Necesitás… | Andá a |
|---|---|
| Entender qué hay y cómo encaja | [Visión](../00-overview/vision.md) → [Mapa del sistema](../00-overview/system-map.md) |
| Elegir la variante correcta de una técnica | El README del [dominio](../pieces/) (tablas comparativas y preguntas guía) |
| Copiar código a tu app | Sección «Snippets canónicos» de cada pieza (con procedencia al fuente) |
| Entender el porqué de una decisión | [ADRs](../04-decisions/) |
| Buscar algo en el código sin leerlo todo | [ia-db](../../../ia-db/README.md) (tabla «Necesitás saber… → Leé este índice») |
| Compilar iOS | [Runbook §Pipelines CI](../07-operations/build-and-run.md) (no se compila iOS en local Windows) |

## 4. Convenciones al contribuir

- Un ejemplo nuevo = un proyecto nuevo mínimo y autocontenido ([ADR-0001](../04-decisions/0001-un-proyecto-por-tecnica.md)) siguiendo el patrón servicio tipado + overlay ([ADR-0002](../04-decisions/0002-servicio-tipado-overlay-mvvm.md)).
- Registrar cambios en `CHANGELOG.md` (Keep a Changelog, es-ES) — convención descripta en el [runbook §Versionado](../07-operations/build-and-run.md).
- Claves: nunca en el repo ([ADR-0007](../04-decisions/0007-secretos-fuera-del-repo.md)).

## Preguntas guía

- ¿Podés dejar corriendo Red o Dialer sin abrir más que esta página y el runbook? Si no, falta algo en esta doc: reportalo.
- ¿Tu primer build de GPS falló por `ApiKeys.cs`? Es el comportamiento esperado — ver runbook.

## Referencias

- [Runbook — build y ejecución](../07-operations/build-and-run.md)
- ia-db: [README](../../../ia-db/README.md) · [índice 09](../../../ia-db/indexes/09_CI-CD-y-Build.md)
