---
doc_id: ARCH-CROSS-001
doc_type: architecture
title: Conceptos transversales — seguridad, permisos y convenciones MVVM
version: 1.0.0
status: draft
origin: reverse-engineered
confidence: high
owner: fernandofilipuzzi
last_review: 2026-07-15
review_cycle_days: 180
audience: [dev, arquitectos]
classification: uso-interno
traces: [ADR-0007, ADR-0008]
---

# Conceptos transversales

> **Resumen ejecutivo.** Preocupaciones que cruzan todos los dominios de la solución: gestión de secretos de build, confianza TLS en el WebView, filosofía de permisos y convenciones MVVM observadas. Cada tema se documenta una sola vez acá (o en su ADR) y las piezas lo referencian.

## 1. Seguridad

### 1.1 Secretos de build

Las claves de servicios externos (Google Maps/Geocoding) **no se versionan**: `**/Services/ApiKeys.cs` está en `.gitignore`, se genera desde plantilla en local y desde secrets en CI. Decisión y deriva detectada (una key hardcodeada en Maps) en [ADR-0007](../04-decisions/0007-secretos-fuera-del-repo.md); procedimiento en el [runbook](../07-operations/build-and-run.md).

### 1.2 Confianza TLS en el WebView (híbrida)

En Android antiguos, la raíz `ISRG Root X2` (Let's Encrypt) no está en el trust store y el WebView rechaza el backend HTTPS. La solución adoptada —embeber raíz + intermedios como `trust-anchors` por dominio vía `network_security_config.xml`— y sus trade-offs de mantenimiento están en [ADR-0008](../04-decisions/0008-trust-anchors-embebidos-webview.md). Guía completa, comandos de diagnóstico y glosario PKI en el origen: `Ejemplos_Devices/Docs/Certificados-SSL/`.

**Regla transversal**: nunca `src="user"` en producción (riesgo MITM) y nunca deshabilitar la validación TLS.

### 1.3 Filosofía de permisos

Cada ejemplo declara **solo los permisos de su técnica** (consecuencia de [ADR-0001](../04-decisions/0001-un-proyecto-por-tecnica.md)); la app híbrida es la única que consolida permisos de varios dispositivos. El detalle por dominio, verificado contra los manifests, vive en la sección «Permisos y su justificación» de cada [pieza](../pieces/). Contraste didáctico clave: `Ejemplo_Maui_Dialer` no requiere permiso alguno, `Ejemplo_Maui_DirectCall` requiere `CALL_PHONE` — la elección de API define la superficie de permisos ([pieza phone](../pieces/phone/README.md)).

## 2. Convenciones MVVM observadas

| Convención | Detalle | Fuente |
|---|---|---|
| `CommunityToolkit.Mvvm` 8.4.2 | Patrón dominante: `[ObservableProperty]`, `[RelayCommand]` en ViewModels de overlay | `.csproj` de los ejemplos; [ADR-0002](../04-decisions/0002-servicio-tipado-overlay-mvvm.md) |
| `AsyncRelayCommand` didáctico | Implementación propia (~50 líneas, anti-reentrancia con `IsRunning`) usada en GPS y DirectCall para enseñar el mecanismo sin NuGet; límites: no captura excepciones (`async void`), sin `CancellationToken` | `Ejemplos_Devices/Docs/otros/AsyncRelayCommandOptions.md` |
| Regla práctica | Para cancelación fina o manejo estructurado de errores, migrar al `AsyncRelayCommand` de CommunityToolkit | mismo documento, §5 |

## 3. Gotcha transversal — `WebView` + `EventToCommandBehavior`

Con compiled bindings (`x:DataType`), los `Behavior` no heredan el `BindingContext` de forma fiable: el `Command` queda `null`; y `EventToCommandBehavior` (CommunityToolkit.Maui) **no pasa los `EventArgs` por defecto** (`e` llega `null`). Soluciones documentadas en el origen (por orden de preferencia): `Source={x:Reference page}`, `x:DataType` en el behavior, o code-behind; para los args, configurar un `EventArgsConverter`. Fuente: `Ejemplos_Devices/Docs/otros/Eventos.md` (aplica a la híbrida).

## 4. Observabilidad, i18n y resiliencia

No hay infraestructura transversal de logging, internacionalización ni resiliencia en la solución (colección didáctica): **omisión declarada** — no se documenta lo que no existe. Los ejemplos manejan errores localmente en cada ViewModel/servicio.

## Referencias

- ia-db: [índice 10 — Documentación transversal](../../../ia-db/indexes/10_Documentacion-Transversal.md)
- ADRs: [0007](../04-decisions/0007-secretos-fuera-del-repo.md) · [0008](../04-decisions/0008-trust-anchors-embebidos-webview.md)
