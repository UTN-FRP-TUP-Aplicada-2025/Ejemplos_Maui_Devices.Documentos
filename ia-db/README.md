# ia-db — Ejemplos_Maui_Devices

> **Para el agente IA**: este archivo es el **punto de entrada único** a la base de conocimiento (`ia-db`) del proyecto `Ejemplos_Maui_Devices`. Antes de releer el código, consultá la tabla de navegación de abajo, cargá **solo** los índices relevantes desde [`indexes/`](indexes/) y amplía a los archivos fuente únicamente si el índice resulta insuficiente. No recorras el repositorio completo cuando existe información indexada.
>
> El código fuente indexado vive en el repositorio hermano `../../Ejemplos_Maui_Devices/`; las rutas de fuentes en los índices son **relativas a la raíz de ese proyecto** (p.ej. `Ejemplos_Devices/Camera/...`).

---

## Navegación — «Necesitas saber… → Lee este índice»

| Necesitás saber… | Leé este índice |
|------------------|-----------------|
| Qué es el repo, su stack y dónde está cada ejemplo | [00 — Índice maestro](indexes/00_MASTER-INDEX.md) |
| Capturar una foto (MediaPicker vs. picker propio, callback vs. Task, EXIF, selfie) | [01 — Cámara](indexes/01_Camara.md) |
| Escanear QR y comparar librerías (BSM · BSN · CS · ZN; página vs. diálogo) | [02 — Lectura de QR](indexes/02_QR.md) |
| Imprimir en térmica Bluetooth 58 mm (ESC/POS directo vs. motor `MotorDsl.*`) | [03 — Impresión térmica](indexes/03_Impresion-Termica.md) |
| Obtener ubicación y geocodificar (permisos, seguimiento) | [04 — GPS](indexes/04_GPS.md) |
| Mostrar un mapa con pines | [05 — Mapas](indexes/05_Mapas.md) |
| Llamar por teléfono (marcador vs. llamada directa) | [06 — Telefonía](indexes/06_Telefonia.md) |
| Detectar estado y cambios de red | [07 — Red y conectividad](indexes/07_Red-Conectividad.md) |
| Arquitectura híbrida WebView + puente de comandos por URL + backend Blazor | [08 — App híbrida integrada](indexes/08_App-Hibrida-Integrada.md) |
| Compilar/publicar en CI (iOS) y arrancar ejemplos localmente | [09 — CI/CD y build](indexes/09_CI-CD-y-Build.md) |
| SSL/certificados, notas transversales y convención de CHANGELOG | [10 — Documentación transversal](indexes/10_Documentacion-Transversal.md) |

---

## Resumen ejecutivo

| Dato | Valor |
|------|-------|
| Proyecto | `Ejemplos_Maui_Devices` |
| Tipo | Colección de ejemplos didácticos de acceso a dispositivos desde .NET MAUI |
| Stack | .NET 10 · .NET MAUI 10.x · MVVM (`CommunityToolkit.Mvvm`) |
| Targets | `net10.0-android`, `net10.0-ios` (iOS en CI/macOS) |
| Dominios | Cámara · QR · Impresión térmica · GPS · Mapas · Telefonía · Red · App híbrida integrada |
| Repo de código | `../../Ejemplos_Maui_Devices/` (hermano de esta carpeta `.Documentos`) |
| Versión ia-db | 1.3 |

**Función principal.** Cada dispositivo de plataforma se presenta como uno o varios proyectos MAUI mínimos y comparables que contrastan enfoques de implementación; una app híbrida (`Ejemplo_Maui_Hibrida`) consolida todos los dispositivos detrás de un `WebView` con un puente de comandos por URL y un backend Blazor de apoyo.

**Arquitectura en una línea.** Ejemplos aislados por dispositivo (servicio tipado + overlay MVVM) reutilizados por una app híbrida WebView↔nativo, construidos con pipelines CI de iOS y scripts de arranque para Android.

---

## Estructura de la ia-db

```
ia-db/
├── README.md                       ← Este archivo (punto de entrada único)
└── indexes/
    ├── 00_MASTER-INDEX.md          ← Visión general, stack, catálogo de ejemplos
    ├── 01_Camara.md
    ├── 02_QR.md
    ├── 03_Impresion-Termica.md
    ├── 04_GPS.md
    ├── 05_Mapas.md
    ├── 06_Telefonia.md
    ├── 07_Red-Conectividad.md
    ├── 08_App-Hibrida-Integrada.md
    ├── 09_CI-CD-y-Build.md
    └── 10_Documentacion-Transversal.md
```

---

## Restricciones para un agente que consuma esta ia-db

- **Recuperar antes que releer.** Cargá los índices necesarios, no el repositorio completo.
- **No duplicar conocimiento.** Los índices se referencian entre sí; no copies contenido de un índice a otro.
- **El código manda.** Si la evidencia del código contradice un índice, prevalece el código: corregí el índice (ver *Actualizar*) y eliminá referencias obsoletas.
- **Trazabilidad.** Toda afirmación de un índice cita su fuente por ruta relativa al proyecto; no completes por inferencia lo no verificable.
- **No modificar el proyecto indexado.** Esta ia-db es de solo lectura respecto de `../../Ejemplos_Maui_Devices/`.

---

## Conjunto documental derivado

Sobre esta ia-db se generó el conjunto documental completo (Marco de Documentación de Software) en la carpeta hermana [`../Ejemplos_Maui_Devices-docs/`](../Ejemplos_Maui_Devices-docs/README.md): visión, mapa del sistema, arquitectura C4, vistas de runtime, 8 ADRs, catálogo de APIs, runbook, onboarding y un documento por dominio de dispositivo, más el [reporte de gap y derivas](../Ejemplos_Maui_Devices-docs/GAP-REPORT.md). Punto de entrada humano: [`../Ejemplos_Maui_Devices-docs/README.md`](../Ejemplos_Maui_Devices-docs/README.md); punto de entrada máquina: [`../Ejemplos_Maui_Devices-docs/docs-manifest.yaml`](../Ejemplos_Maui_Devices-docs/docs-manifest.yaml).

> **Nota de sincronización (`@fd6a1ed`):** el origen avanzó del commit `24d611d` (que indexó esta ia-db) a `fd6a1ed` durante la documentación. Correcciones incrementales aplicadas: versión de `MotorDsl.*` en `Ejemplo_MotorDSL_Dialog` (1.0.12 → 1.0.13, índices [00](indexes/00_MASTER-INDEX.md)/[03](indexes/03_Impresion-Termica.md)) y Readmes invertidos del dominio Maps (índice [05](indexes/05_Mapas.md)). Detalle en el [GAP-REPORT §1](../Ejemplos_Maui_Devices-docs/GAP-REPORT.md).
>
> **Actualización incremental v1.1 (2026-07-17):** dos lotes de trabajo posteriores a `fd6a1ed`, todos acotados a la app híbrida (`Ejemplo_Maui_Hibrida`) y a `Ejemplo_MotorDSL_Dialog`; **la librería `MotorDsl.*` no se modificó** (sigue en 1.0.13) y los ejemplos aislados de GPS/Red/Telefonía **no se tocaron** (índices [04](indexes/04_GPS.md)/[06](indexes/06_Telefonia.md)/[07](indexes/07_Red-Conectividad.md) sin cambios). (1) *UX de impresión* (CHANGELOG 2026-07-16): catálogo de errores con código, `DocumentResult` tipado, salida al cambiar de impresora — ya reflejado en el [índice 03 §10](indexes/03_Impresion-Termica.md). (2) *Armonización de overlays y primer proyecto de tests* (CHANGELOG 2026-07-17): los cuatro overlays de la híbrida al mismo patrón (interfaces `I*Service`, `IUiDispatcher`, catálogos `GPS-*`/`TEL-*`) y `Ejemplo_Maui_Hibrida.Tests` (116 tests xUnit, 5 invariantes) — [índice 08 §5.1 y §9](indexes/08_App-Hibrida-Integrada.md).

> **Actualización incremental v1.2 (2026-07-17):** el workflow CI de la app híbrida se **recategorizó** de `gps` a una categoría propia `Integrada`: `cd-ios-gps.Ejemplo_Maui_Hibrida.yml` → `cd-ios-Integrada.Ejemplo_Maui_Hibrida.yml` (contenido idéntico; solo cambia el archivo/categoría). Sigue habiendo **18 workflows** en total; GPS queda con 1. Corregido en el [índice 09](indexes/09_CI-CD-y-Build.md) (§1 árbol, §2 convención, §3.1/§3.2 tablas) y en el [índice 10 §4](indexes/10_Documentacion-Transversal.md) (referencia de estilo del CHANGELOG y puntero a la entrada más reciente, ahora `2026-07-17`). El resto de cambios del árbol de trabajo (armonización de overlays, tests, UX de impresión) ya estaban reflejados en v1.1; las utilidades de simulación en la raíz `Utilities/` (`flows/`, `end2end/`, `simular_ui.sh`) quedan fuera del alcance indexado.

> **Actualización incremental v1.3 (2026-07-17):** las **pruebas end2end sobre la UI real** (Maestro «dedo virtual» + grabación de video en el simulador iOS: workflow `cd-ios-Integrada.Ejemplo_Maui_Hibrida.yml` → `Utilities/simular_ui.sh` → `Utilities/end2end/<PACKAGE_NAME>.yaml`) **siguen fuera del alcance indexado** (nota v1.2), pero ya **no quedan sin documentar**: el conjunto documental derivado las describe como puntero en su [README (§ Pruebas End2End)](../Ejemplos_Maui_Devices-docs/README.md). Verificado contra el origen `@a994257`; detalle en el [CHANGELOG documental](../CHANGELOG.md) (entrada «Documentación de pruebas End2End»). No cambió ningún índice temático (00–10): esta nota es solo una referencia cruzada.

## Manifiesto de generación

- Generado por : /IA.Prompting.Templates/Tool-Prompts/Iniciar-Indexado.md
- Alcance      : Ejemplos_Maui_Devices (dominios: Cámara, QR, Impresión térmica, GPS, Mapas, Telefonía, Red, App híbrida integrada, CI/CD, Documentación transversal)
- Fuentes      : Ejemplos_Devices/ (Camera, QR, Printer, GPS, Maps, Phone, Red, Integrada, Docs, scripts), .github/workflows/, README.md, CHANGELOG.md, vs.bat, .gitignore
- Exclusiones  : .git, bin, obj, .vs, Platforms/*/Resources, wwwroot/lib, binarios/imágenes, artefactos de build, y lo ignorado por .gitignore
- Generado     : 2026-07-14 · Versión: 1.0
- Actualizado  : 2026-07-17 · Versión: 1.3 (incremental; referencia cruzada a la documentación de pruebas End2End — ver nota de sincronización)
- Actualizar   : /IA/IA.Prompts/Tool-Prompts/Indexado/Actualizar-Indexado.md
