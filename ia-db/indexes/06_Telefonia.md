# Índice 06 — Telefonía

> **Propósito**: Dos enfoques de llamada telefónica en .NET MAUI — abrir el marcador del SO (`PhoneDialer`) vs. iniciar la llamada directa nativa (`Intent.ActionCall` en Android).
> **Fuente primaria**: `Ejemplos_Devices/Phone/`.
> **Entrada ia-db**: [README](../README.md) · [Índice maestro](00_MASTER-INDEX.md)

## Proyectos del dominio

| Proyecto | Enfoque | Arquitectura | Ruta |
|---|---|---|---|
| `Ejemplo_Maui_Dialer` | Abre el **marcador** con el número precargado (usuario pulsa "llamar") | Code-behind, `BindingContext = this` | `Ejemplos_Devices/Phone/Ejemplo_Maui_Dialer/` |
| `Ejemplo_Maui_DirectCall` | **Llamada directa** nativa (Android) con permiso runtime; iOS cae al marcador | MVVM: Coordinator + overlay + `CallResult` tipado | `Ejemplos_Devices/Phone/Ejemplo_Maui_DirectCall/` |

## Comparación Dialer vs. DirectCall

| Aspecto | `Ejemplo_Maui_Dialer` | `Ejemplo_Maui_DirectCall` |
|---|---|---|
| API MAUI | `PhoneDialer.Default.Open()` / `.IsSupported` | Android: `Intent.ActionCall`; iOS/MacCatalyst: `PhoneDialer.Default.Open()`; otros: NotSupported |
| Inicia la llamada | No: abre el marcador, el usuario confirma | Sí en Android (marca directo); en iOS abre marcador |
| Permiso runtime | **Ninguno** | Android `CALL_PHONE` vía `Permissions.Phone` |
| Permisos Android (manifest) | `INTERNET`, `ACCESS_NETWORK_STATE` | + `CALL_PHONE`, `READ_PHONE_STATE` |
| `<queries>` Android | `action.DIAL` + `data scheme=tel` | `action.VIEW` + `data scheme=tel` |
| minSdk Android | 25 | 21 |
| Detección de soporte | `PhoneDialer.Default.IsSupported` | `Permissions.Phone` + `PhoneDialer.IsSupported` (iOS) |
| Manejo de errores | `try/catch` + `DisplayAlert` | `CallResult` (record) + overlay reutilizable |
| Código nativo | No | `#if ANDROID` con `Intent`/`AndroidUri` |
| Fuente clave | `Pages/MainPage.xaml.cs:36-52` | `Services/PhoneDialerDevice.cs:20-101` |

## API MAUI y paquetes

| Proyecto | Paquete | API principal | Fuente |
|---|---|---|---|
| Dialer | `Microsoft.Maui.Controls` **10.0.60**, `Logging.Debug 10.0.8` | `PhoneDialer` (Essentials) | `Ejemplo_Maui_Dialer.csproj:78-79` |
| DirectCall | `Microsoft.Maui.Controls` (`$(MauiVersion)`), `Logging.Debug 10.0.0` | `Permissions.Phone`, `Intent.ActionCall`, `PhoneDialer` | `Ejemplo_Maui_DirectCall.csproj:80-81` |

Ambos: `net10.0-android` (+ `net10.0-ios` en macOS); sin Windows. Ambos usan `MauiXamlInflator=SourceGen` (`Dialer.csproj:16`, `DirectCall.csproj:14`).

## Permisos de plataforma

### Dialer
| Plataforma | Elemento | Fuente |
|---|---|---|
| Android | `<queries>` intent `ACTION_DIAL` + `scheme=tel` (necesario para que `IsSupported` detecte la app de teléfono) | `Ejemplo_Maui_Dialer/Platforms/Android/AndroidManifest.xml:14-19` |
| iOS | `LSApplicationQueriesSchemes` = `whatsapp`, `tg` (no incluye `tel`) | `Ejemplo_Maui_Dialer/Platforms/iOS/Info.plist:63-67` |

### DirectCall
| Plataforma | Elemento | Fuente |
|---|---|---|
| Android | `CALL_PHONE`, `READ_PHONE_STATE` (runtime) | `Ejemplo_Maui_DirectCall/Platforms/Android/AndroidManifest.xml:14-15` |
| Android | `<queries>` intent `ACTION_VIEW` + `scheme=tel` | `Ejemplo_Maui_DirectCall/Platforms/Android/AndroidManifest.xml:17-22` |
| iOS | Info.plist **sin** `LSApplicationQueriesSchemes` con `tel` | `Ejemplo_Maui_DirectCall/Platforms/iOS/Info.plist` |

## Flujo — Dialer

```
btnAbrirMarcador_Clicked (MainPage.xaml.cs:26)
   ├─ valida Telefono no vacío
   ├─ if PhoneDialer.Default.IsSupported → PhoneDialer.Default.Open(Telefono)
   └─ catch FeatureNotSupportedException / Exception → DisplayAlert
```
Binding manual bidireccional del `Entry` a `Telefono` (`Pages/MainPage.xaml.cs:6-18`; `BindingContext = this`).

## Flujo — DirectCall (MVVM)

```
MainPageViewModel.RealizarLLamadaAsync
        │  (MainPageViewModel.cs:36-42)
CallCoordinator.CallAsync(numero, ct)       ← singleton, dueño del overlay + CTS + último número
        │  (Services/CallCoordinator.cs:31-73)
PhoneDialerDevice.CallPhoneAsync(numero, ct)
        │  #if ANDROID  → Permissions.Phone → Intent.ActionCall
        │  #elif IOS    → PhoneDialer.Default.Open (usuario confirma)
        │  #else        → CallResult.NotSupported
        ▼
CallResult  → Coordinator.Aplicar() → CallOverlayViewModel (overlay)
```

### `CallResult` (record sellado) — `Services/CallResult.cs:6-28`
| Caso | Significado |
|---|---|
| `Success(Numero)` | Llamada iniciada (Android) o marcador abierto (iOS) |
| `PermissionDenied` | Android rationale `true`: reintentar permiso |
| `PermissionDeniedPermanent` | "No volver a preguntar": ir a ajustes |
| `PermissionRestricted` | Política del dispositivo (MDM/parental) |
| `NotSupported` | Sin hardware de teléfono (tablet, Windows, simulador) |
| `Cancelled` | `CancellationToken` |
| `Failure(Message)` | Error inesperado |

### Overlay reutilizable (DirectCall)
- `Controls/CallPermissionOverlayView.xaml`: panel de espera (`IsBusy`) + panel denegado/restringido/no-soportado/error (`IsDenied`) con botones "Pedir permiso" / "Reintentar" / "Abrir configuración" / "Cerrar" (`:47-74`); backdrop `#E6000000` que bloquea input (`CascadeInputTransparent=False`, `:27`).
- `ViewModels/CallOverlayViewModel.cs`: métodos `ShowBusy`/`ShowPermissionDenied`/`ShowRestricted`/`ShowNotSupported`/`ShowFailure`/`Hide`; flags `CanRetry`/`CanRetryAction`/`CanOpenSettings` (`:95-157`). Es el espejo del `GpsOverlayViewModel` (ver Índice 04).
- Página: `BindingContext="{Binding CallOverlay}"` sobre el control (`Pages/MainPage.xaml:24`).
- DI: `PhoneDialerDevice` transient, `CallCoordinator` singleton, VM/Page transient (`MauiProgram.cs:34-43`).

## Semántica de permiso de teléfono (DirectCall / Android)

| Estado tras `RequestAsync<Permissions.Phone>` | Resultado | Fuente |
|---|---|---|
| `Granted` | continúa a `Intent.ActionCall` | `PhoneDialerDevice.cs:78` |
| `Restricted` | `PermissionRestricted` | `PhoneDialerDevice.cs:79` |
| `ShouldShowRationale == true` | `PermissionDenied` (reintentable) | `PhoneDialerDevice.cs:83-85` |
| `ShouldShowRationale == false` | `PermissionDeniedPermanent` (ajustes) | `PhoneDialerDevice.cs:86` |

## Gotchas

- **iOS no expone API de llamada directa**: `PhoneDialer.Open` solo abre el marcador; en DirectCall un `Success` en iOS significa "marcador abierto", no "llamada iniciada" (`PhoneDialerDevice.cs:48-51`).
- **`CALL_PHONE` es permiso sensible**: implica revisión de política en Google Play; el proyecto Dialer lo evita a propósito usando `ACTION_DIAL`.
- **Discrepancia comentario/plist en DirectCall**: el comentario de `PhoneDialerDevice.cs:16,42` afirma que `tel` está en `LSApplicationQueriesSchemes` "ya declarado", pero el `Info.plist` de DirectCall no lo incluye. (En iOS moderno `PhoneDialer` con esquema `tel` no suele requerirlo, pero el comentario está desactualizado.)
- El botón "Reintentar" del overlay reutiliza el **último número** guardado por el coordinador, permitiendo reintentar sin que el caller original siga vivo (`CallCoordinator.cs:40,77-86`).
- Toda I/O de overlay se marshala a `MainThread.InvokeOnMainThreadAsync` (`CallCoordinator.cs:48,53`).
