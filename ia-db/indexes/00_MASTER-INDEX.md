# Índice 00 — Índice maestro

> **Propósito**: visión general del repositorio `Ejemplos_Maui_Devices` — qué es, con qué stack, cómo está organizado y qué demuestra cada ejemplo — como punto de partida antes de abrir un índice temático.
> **Fuente primaria**: `README.md`, `CHANGELOG.md` y los `.csproj` de cada ejemplo.
> **Entrada ia-db**: [README](../README.md)

---

## 1. Qué es

Repositorio de **ejemplos didácticos de acceso a dispositivos desde .NET MAUI**. Cada dispositivo (cámara, QR, impresora térmica, GPS, mapas, teléfono, red) se presenta como uno o varios proyectos MAUI mínimos y autocontenidos que contrastan enfoques de implementación (API nativa vs. control propio, callback vs. `Task`, página embebida vs. diálogo, librería A vs. librería B). Sobre esos ejemplos aislados, una **app híbrida integrada** (`Ejemplo_Maui_Hibrida`) consolida todos los dispositivos detrás de un `WebView` y un puente de comandos por URL, con un backend Blazor de apoyo.

El objetivo del repositorio es **comparar y documentar** técnicas de integración de hardware/servicios de plataforma, no publicar una aplicación de producción.

## 2. Stack transversal

| Aspecto | Valor | Fuente |
|---------|-------|--------|
| Framework | .NET 10 · .NET MAUI | `.csproj` de cada ejemplo |
| Target frameworks | `net10.0-android`, `net10.0-ios` (iOS solo en macOS) | `Ejemplo_Maui_Hibrida.csproj` |
| MAUI (`Microsoft.Maui.Controls`) | 10.0.80 en la híbrida; varía por ejemplo (`$(MauiVersion)`, 10.0.30/31/40) | `.csproj` |
| SDK / workload (dev local) | 10.0.102 (android 36.1.12, ios 26.2.10191) | `.github/workflows/Readme.md` |
| SDK / workload (CI) | SDK 10.0.300 · manifest 10.0.100 | `.github/workflows/*.yml` |
| MVVM | `CommunityToolkit.Mvvm` 8.4.2 | `.csproj` |
| UI helpers | `CommunityToolkit.Maui` 14.2.0 / `.Core` / `.Camera` 6.x | `.csproj` |
| Plataformas objetivo | Android (todos) · iOS (CI de build/simulación) · sin Windows productivo | `.csproj`, workflows |
| Patrón dominante | Un proyecto MAUI por técnica; servicio tipado + overlay MVVM | ver índices 01–08 |

> **Nota sobre versiones por ejemplo.** Al ser proyectos independientes, las versiones de paquetes **no son homogéneas** entre ejemplos. Casos relevantes: `MotorDsl.*` está en **1.0.12** en `Ejemplo_MotorDSL` pero en **1.0.13** en `Ejemplo_MotorDSL_Dialog` y en la app híbrida (índices 03 y 08; el commit `fd6a1ed` bumpeó el Dialog tras la indexación inicial `@24d611d`); la versión de MAUI y de `CommunityToolkit.Maui.Camera` también difieren entre ejemplos de cámara. Cada índice temático registra las versiones exactas de su dominio.

## 3. Estructura del repositorio

```
Ejemplos_Maui_Devices/
├── README.md                     ← Portada: enlaces a Ejemplo_Docs_<Dominio>/Readme.md
├── CHANGELOG.md                  ← Keep a Changelog (es-ES)
├── vs.bat                        ← Abre el repo en VS Code (code .)
├── .github/workflows/            ← 18 workflows CI iOS (cd-ios-<cat>.<Ejemplo>.yml)   → Índice 09
└── Ejemplos_Devices/
    ├── Camera/     5 ejemplos + Ejemplo_Docs_Photo                                    → Índice 01
    ├── QR/         BSM · BSN · CS · ZN, cada uno página + diálogo                      → Índice 02
    ├── Printer/    ThermalPrinter · MotorDSL · MotorDSL_Dialog + Ejemplo_Docs_Printer  → Índice 03
    ├── GPS/        Ejemplo_Maui_GPS + Ejemplo_Docs_GPS                                 → Índice 04
    ├── Maps/       Ejemplo_Maui_Mapas + Ejemplo_Docs_Maps                              → Índice 05
    ├── Phone/      Ejemplo_Maui_Dialer · Ejemplo_Maui_DirectCall                       → Índice 06
    ├── Red/        Ejemplo_Maui_Connectivity + Ejemplo_Docs_Red                        → Índice 07
    ├── Integrada/  Ejemplo_Maui_Hibrida (WebView) · Ejemplo_ws_Blazor (backend)        → Índice 08
    ├── Docs/       Certificados-SSL · qr-nuget · web-hibrida · otros                   → Índices 02/08/10
    └── scripts/    .bat de arranque local (Android físico)                             → Índice 09
```

## 4. Mapa de dominios → índices

| # | Índice | Dominio | Ejemplos que cubre |
|---|--------|---------|--------------------|
| 01 | [Cámara](01_Camara.md) | Captura de foto | 5 apps: MediaPicker nativo vs. picker propio; callback vs. Task; normalización EXIF; selfie |
| 02 | [Lectura de QR](02_QR.md) | Escaneo de QR | 4 librerías (BSM/BSN/CS/ZN) × 2 variantes (página / diálogo) |
| 03 | [Impresión térmica](03_Impresion-Termica.md) | Impresora BT 58mm | Directo (ESC/POS) vs. motor DSL (`MotorDsl.*`); variante con overlay |
| 04 | [GPS](04_GPS.md) | Geolocalización | `Ejemplo_Maui_GPS` (permisos → servicio → geocodificación) |
| 05 | [Mapas](05_Mapas.md) | Mapa nativo | `Ejemplo_Maui_Mapas` (pines, `MapType`, `MoveToRegion`) |
| 06 | [Telefonía](06_Telefonia.md) | Llamadas | Dialer (`PhoneDialer`) vs. DirectCall (intent nativo) |
| 07 | [Red y conectividad](07_Red-Conectividad.md) | Estado de red | `Ejemplo_Maui_Connectivity` (`Connectivity` API) |
| 08 | [App híbrida integrada](08_App-Hibrida-Integrada.md) | WebView + Blazor | `Ejemplo_Maui_Hibrida` + backend `Ejemplo_ws_Blazor` |
| 09 | [CI/CD y build](09_CI-CD-y-Build.md) | Pipelines y scripts | 18 workflows iOS, scripts de arranque, versionado |
| 10 | [Documentación transversal](10_Documentacion-Transversal.md) | SSL, notas, CHANGELOG | `Docs/Certificados-SSL`, `Docs/otros`, convenciones |

## 5. Catálogo de proyectos ejecutables

| Proyecto (`.csproj`) | Dominio | Enfoque que ilustra |
|----------------------|---------|---------------------|
| `Camera/Ejemplo_Photo_MediaPicker` | Cámara | Diálogo nativo `MediaPicker` encapsulado en servicio |
| `Camera/Ejemplo_Photo_MiMediaPicker_Callback` | Cámara | Picker propio + transferencia por callback |
| `Camera/Ejemplo_Photo_MiMediaPicker_Task` | Cámara | Transferencia por `TaskCompletionSource` |
| `Camera/Ejemplo_Photo_MiMediaPicker_Callback_Normalizacion` | Cámara | Rotación EXIF + resize + JPEG (SkiaSharp) |
| `Camera/Ejemplo_Photo_MiMediaSelfie_Callback_Normalizacion` | Cámara | Cámara frontal + máscara ovalada |
| `QR/{BSM,BSN,CS,ZN}.LectorQR` | QR | Escáner como página embebida (4 librerías) |
| `QR/{BSM,BSN,CS,ZN}.LectorQR_Dialog` | QR | Escáner como diálogo con retorno por Task |
| `Printer/Ejemplo_ThermalPrinter` | Impresión | ESC/POS directo (SPP Bluetooth) |
| `Printer/Ejemplo_MotorDSL` | Impresión | Motor DSL puro (JSON → raster ESC/POS) |
| `Printer/Ejemplo_MotorDSL_Dialog` | Impresión | Motor DSL + overlay MVVM |
| `GPS/Ejemplo_Maui_GPS` | GPS | Geolocalización + geocodificación inversa |
| `Maps/Ejemplo_Maui_Mapas` | Mapas | Control `Map` con pines |
| `Phone/Ejemplo_Maui_Dialer` | Telefonía | Abrir el marcador del sistema |
| `Phone/Ejemplo_Maui_DirectCall` | Telefonía | Llamada directa por intent (Android) |
| `Red/Ejemplo_Maui_Connectivity` | Red | Estado y cambios de conectividad |
| `Integrada/Ejemplo_Maui_Hibrida` | Integrada | WebView + puente de comandos por URL |
| `Integrada/Ejemplo_ws_Blazor` | Integrada | Backend Blazor (comprobante, geo, pagos fake) |

## 6. Decisiones y convenciones clave

- **Un ejemplo = una técnica.** Los proyectos son deliberadamente mínimos y comparables; la variación (callback vs. Task, página vs. diálogo, librería A vs. B) es el objeto de estudio.
- **Servicio tipado + overlay MVVM.** El patrón recurrente por dispositivo es una interfaz de servicio (`I*Service`) más un `ViewModel` de overlay; la app híbrida reutiliza este patrón consolidando cada dispositivo bajo `LibApp/Devices/` (índice 08).
- **Puente WebView ↔ nativo por URL.** La app híbrida despacha acciones nativas interceptando la navegación del `WebView` y resolviendo el primer `IUrlCommandHandler` que coincide (índice 08).
- **CI centrado en iOS.** Los workflows (`cd-ios-<categoria>.<Ejemplo>.yml`) compilan/publican en `macos-15`, con firma ad-hoc y simulación por GIF; el arranque local (`scripts/*.bat`) despliega a **Android físico** (índice 09).
- **Secretos fuera del repo.** `**/Services/ApiKeys.cs` está en `.gitignore`; las claves (p.ej. Google Maps) se inyectan en build desde plantilla + secrets de CI. Excepción detectada: una API key de Google hardcodeada en `Maps/.../AndroidManifest.xml` (gotcha del índice 05).
- **iOS + QR requiere cuidado con el simulador.** Algunas librerías de QR (BSM, basada en ML Kit) exigen `iossimulator-x64` + Rosetta y `GoogleUtilities`; BSN (Apple Vision) evita ese requisito y es la opción recomendada (índice 02).

## 7. Cómo navegar esta base

1. Empezá por [README](../README.md) para el resumen ejecutivo y la tabla «Necesitas saber… → Lee este índice».
2. Cargá **este índice maestro** para ubicar el dominio.
3. Abrí **1–2 índices temáticos** (01–10) del tema puntual; cada uno referencia sus fuentes por ruta relativa al proyecto.
4. Solo si el índice resulta insuficiente, abrí el archivo fuente citado en `Ejemplos_Maui_Devices/`.

> Los índices **destilan**; no reemplazan al código. Toda afirmación es trazable a una fuente citada. Ante contradicción entre índice y código, **prevalece el código**: corregir el índice (ver `Actualizar-Indexado`).
