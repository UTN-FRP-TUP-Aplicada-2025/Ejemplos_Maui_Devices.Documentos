# Changelog

Cambios notables de la documentación de `Ejemplos_Maui_Devices` (`Ejemplos_Maui_Devices.Documentos`).
Formato basado en [Keep a Changelog](https://keepachangelog.com/es-ES/1.1.0/).

## [2026-07-15] — Conjunto documental inicial conforme al Marco de Documentación de Software

Documentación generada por ingeniería inversa sobre el origen `Ejemplos_Maui_Devices`
(anclada al commit `24d611d`; el origen avanzó a `fd6a1ed` durante la generación).
Todo el corpus queda en `status: draft` a la espera de revisión y promoción humana.

### Agregado

- **Índice maestro y contrato máquina-legible** en `Ejemplos_Maui_Devices-docs/`:
  `README.md` (navegación humana), `docs-manifest.yaml` (piezas, `type`, gaps),
  `GLOSSARY.md` (vocabulario controlado) y `GAP-REPORT.md` (gap documental y derivas).
- **Nivel overview** (`docs/00-overview/`): `vision.md` y `system-map.md` con el
  inventario de las 23 piezas en 8 dominios, cada una con su `type` (Marco §7).
- **Arquitectura** (`docs/01-architecture/`): contexto y contenedores C4,
  `04-runtime-views.md` (tres flujos de punta a punta con datos sintéticos) y
  `06-crosscutting.md` (seguridad, permisos y convenciones MVVM).
- **8 ADRs reconstruidos** (`docs/04-decisions/`): un proyecto por técnica, servicio
  tipado + overlay MVVM, puente WebView↔nativo por URL, QR (BSN vs. BSM), motor DSL de
  impresión, CI iOS ad-hoc, secretos fuera del repo y trust anchors del WebView.
- **Catálogo de APIs** (`docs/05-apis/catalog.md`): puente de comandos por URL,
  endpoints del backend Blazor y servicios externos.
- **Runbook de build/CI** (`docs/07-operations/build-and-run.md`) y **onboarding**
  (`docs/08-onboarding/developer-setup.md`).
- **Nueve documentos de pieza** (`docs/pieces/`): camera, qr, printer, gps, maps, phone,
  red e integrada (con `bridge-contract.md`), con snippets de procedencia verificada.
- **ia-db**: nota de sincronización con el conjunto documental derivado y su ancla de commit.

### Corregido

- **Deriva por avance del origen** (`24d611d` → `fd6a1ed`): versión de `MotorDsl.*` en
  `Ejemplo_MotorDSL_Dialog` (1.0.12 → 1.0.13) actualizada en la pieza Printer, el
  ADR-0005 y los índices ia-db 00/03 («el código manda»).
- **Readmes invertidos del dominio Maps** en el índice ia-db 05: la nota útil vive en
  `Ejemplo_Maui_Mapas/Readme.md`, no en `Ejemplo_Docs_Maps/Readme.md`.
- Verificación de finalización: 29 `doc_id` únicos, cero enlaces relativos rotos y
  frontmatter completo en todo el corpus.
