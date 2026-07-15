# Índice 07 — Red y conectividad

> **Propósito**: API `Connectivity` de .NET MAUI — consultar el estado de red (`NetworkAccess`) y reaccionar a cambios (`ConnectivityChanged`) con los perfiles de conexión activos.
> **Fuente primaria**: `Ejemplos_Devices/Red/`.
> **Entrada ia-db**: [README](../README.md) · [Índice maestro](00_MASTER-INDEX.md)

## Proyectos del dominio

| Proyecto | Rol | Ruta |
|---|---|---|
| `Ejemplo_Maui_Connectivity` | App MAUI mínima; utilitario que escucha `ConnectivityChanged` | `Ejemplos_Devices/Red/Ejemplo_Maui_Connectivity/` |
| `Ejemplo_Docs_Red` | Nota: link a docs MS, tabla de `NetworkAccess` y snippet de referencia | `Ejemplos_Devices/Red/Ejemplo_Docs_Red/Readme.md` |

> Nota de namespace: el `RootNamespace` es `Ejemplo_Maui_Conexion` aunque la carpeta se llame `Ejemplo_Maui_Connectivity` (`Ejemplo_Maui_Connectivity.csproj:8`).

## API MAUI y paquetes

| Elemento | Valor | Fuente |
|---|---|---|
| Estado actual | `Connectivity.Current.NetworkAccess` | `Ejemplo_Docs_Red/Readme.md:7` |
| Evento de cambio | `Connectivity.ConnectivityChanged` (`+=` / `-=`) | `Utilities/ConnectivityTest.cs:10,13` |
| Args del evento | `ConnectivityChangedEventArgs` → `NetworkAccess` + `ConnectionProfiles` | `Utilities/ConnectivityTest.cs:15,17,26` |
| Paquetes NuGet | `Microsoft.Maui.Controls`, `Microsoft.Extensions.Logging.Debug 10.0.2` (sin NuGet extra: `Connectivity` es API integrada de Essentials) | `Ejemplo_Maui_Connectivity.csproj:80-81` |
| Targets | `net10.0-android` (+ `net10.0-ios` en macOS). El `.csproj` declara versión Windows pero **no** incluye el TFM `windows` | `Ejemplo_Maui_Connectivity.csproj:4-5,25-26` |

## Permisos de plataforma

| Plataforma | Permiso | Fuente |
|---|---|---|
| Android | `ACCESS_NETWORK_STATE` | `Platforms/Android/AndroidManifest.xml:10` |
| Android | `INTERNET` | `Platforms/Android/AndroidManifest.xml:11` |
| iOS | Ninguno específico (Info.plist sin claves de red) | `Platforms/iOS/Info.plist` |

## Enum `NetworkAccess`

| Valor | Significado | Fuente |
|---|---|---|
| `Internet` | Acceso local e Internet | `Ejemplo_Docs_Red/Readme.md:15` |
| `ConstrainedInternet` | Acceso limitado / portal cautivo (requiere autenticarse) | `Ejemplo_Docs_Red/Readme.md:16` |
| `Local` | Solo red local | `Ejemplo_Docs_Red/Readme.md:17` |
| `None` | Sin conectividad | `Ejemplo_Docs_Red/Readme.md:18` |
| `Unknown` | No se puede determinar | `Ejemplo_Docs_Red/Readme.md:19` |

## Enum `ConnectionProfile` (perfiles activos)

`Bluetooth`, `Cellular`, `Ethernet`, `WiFi` — iterados en `ConnectivityChangedEventArgs.ConnectionProfiles` (`Utilities/ConnectivityTest.cs:26-45`).

## Utilitario de ejemplo — `ConnectivityTest`

```
ctor         → Connectivity.ConnectivityChanged += handler   (línea 10)
~finalizer   → Connectivity.ConnectivityChanged -= handler   (línea 13)
handler(e):
   ├─ e.NetworkAccess == ConstrainedInternet → "acceso limitado"
   ├─ e.NetworkAccess != Internet            → "acceso perdido"
   └─ foreach e.ConnectionProfiles → switch (Bluetooth/Cellular/Ethernet/WiFi) → Console.Write
```
Fuente: `Utilities/ConnectivityTest.cs:9-48`. El mismo snippet está documentado en `Ejemplo_Docs_Red/Readme.md:21-65`.

## Estado de la app

- `Pages/MainPage.xaml` contiene solo un `ScrollView` vacío (`:6-8`); el code-behind solo hace `InitializeComponent()` (`Pages/MainPage.xaml.cs`).
- `MauiProgram.cs` no registra `ConnectivityTest` ni servicios de dominio (solo fuentes y logging) — `MauiProgram.cs:9-23`.
- Es un ejemplo **de referencia de API**, no una app funcional cableada a la UI.

## Gotchas

- **`ConnectivityTest` no está instanciado en ningún lado**: ni en DI ni en la página; el handler nunca llega a suscribirse en tiempo de ejecución (`MainPage` está vacía).
- **Desuscripción vía finalizador (`~ConnectivityTest`) es poco fiable**: mientras `Connectivity` mantenga la referencia al handler, el objeto no es recolectado, así que el finalizador podría no ejecutarse de forma determinista. Lo correcto es `IDisposable`/desuscribir explícitamente en `OnDisappearing`.
- La firma del handler usa `object sender` no-nullable con `Nullable` habilitado (`ConnectivityTest.cs:15`), lo que puede emitir warnings de nulabilidad.
- El `.csproj` referencia condiciones de Windows (`SupportedOSPlatformVersion`/`TargetPlatformMinVersion`) pero **no** agrega el TFM `net10.0-windows`, por lo que esas condiciones quedan inertes (`Ejemplo_Maui_Connectivity.csproj:4-6,25-26`).
- `Connectivity` reporta *capacidad* de red, no *alcanzabilidad real* de un host; para confirmar acceso a un servicio hay que hacer la petición.
