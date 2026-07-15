# Índice 05 — Mapas

> **Propósito**: Control de mapa nativo de .NET MAUI (`Microsoft.Maui.Controls.Maps`) con pines, tipos de mapa, centrado por región y punto azul de usuario.
> **Fuente primaria**: `Ejemplos_Devices/Maps/`.
> **Entrada ia-db**: [README](../README.md) · [Índice maestro](00_MASTER-INDEX.md)

## Proyectos del dominio

| Proyecto | Rol | Ruta |
|---|---|---|
| `Ejemplo_Maui_Mapas` | App MAUI (solo code-behind, sin MVVM) que muestra un `Map` con pines | `Ejemplos_Devices/Maps/Ejemplo_Maui_Mapas/` |
| `Ejemplo_Docs_Maps` | Carpeta de documentación del dominio (su `Readme.md` está **vacío**) | `Ejemplos_Devices/Maps/Ejemplo_Docs_Maps/` |

> **Corrección (verificado `@fd6a1ed`):** la nota útil (link a docs MS del control `Map` + nombre del NuGet) vive en **`Ejemplo_Maui_Mapas/Readme.md`** (líneas 2–5), **no** en `Ejemplo_Docs_Maps/Readme.md`, que está vacío. La versión previa de este índice tenía los dos Readme invertidos.

## API MAUI y paquetes

| Elemento | Valor | Fuente |
|---|---|---|
| Paquete clave | `Microsoft.Maui.Controls.Maps` **10.0.40** | `Ejemplo_Maui_Mapas.csproj:79` |
| Inicialización | `.UseMauiMaps()` en el builder | `MauiProgram.cs:12` |
| Namespace XAML | `xmlns:maps="http://schemas.microsoft.com/dotnet/2021/maui/maps"` | `Pages/MainPage.xaml:4` |
| Control | `<maps:Map>` con `MapType`, `IsShowingUser`, `IsScrollEnabled`, `IsZoomEnabled` | `Pages/MainPage.xaml:17-20` |
| Tipos en code-behind | `Microsoft.Maui.Controls.Maps` (`Map`, `Pin`, `PinType`) + `Microsoft.Maui.Maps` (`MapSpan`, `MapType`, `Distance`, `Location`) | `Pages/MainPage.xaml.cs:1-2` |
| Targets | `net10.0-android` (+ `net10.0-ios` en macOS, + `net10.0-windows10.0.19041.0` en Windows) | `Ejemplo_Maui_Mapas.csproj:4-6` |

## Permisos y configuración de plataforma

| Plataforma | Requisito | Nota | Fuente |
|---|---|---|---|
| Android | `meta-data com.google.android.geo.API_KEY` | **Obligatorio**: Android usa Google Maps SDK y necesita API key | `Platforms/Android/AndroidManifest.xml:10-11` |
| Android | `meta-data com.google.android.gms.version` | Versión de Google Play Services | `Platforms/Android/AndroidManifest.xml:13-14` |
| Android | `INTERNET`, `ACCESS_NETWORK_STATE` | Descarga de tiles | `Platforms/Android/AndroidManifest.xml:17-18` |
| Android | `ACCESS_FINE_LOCATION`, `ACCESS_COARSE_LOCATION` | Opcionales; solo para `IsShowingUser` (punto azul) | `Platforms/Android/AndroidManifest.xml:23-24` |
| iOS | `NSLocationWhenInUseUsageDescription` | Para mostrar la ubicación del usuario (MapKit; **sin API key**) | `Platforms/iOS/Info.plist:32-33` |
| Windows | Sin token de mapa configurado en este ejemplo | El target existe pero no hay `MapServiceToken` | `Ejemplo_Maui_Mapas.csproj:6` |

## Funcionalidad de la página (code-behind)

| Acción | Implementación | Fuente |
|---|---|---|
| Pedir permiso de ubicación | `Permissions.LocationWhenInUse` en `OnAppearing`; si `Granted` → `MyMap.IsShowingUser = true` | `Pages/MainPage.xaml.cs:24-35` |
| Centrar mapa | `MapSpan.FromCenterAndRadius(Location, Distance.FromKilometers(r))` + `MoveToRegion` | `Pages/MainPage.xaml.cs:45-50` |
| Agregar pines | `new Pin { Label, Address, Location, Type = PinType.Place }` → `MyMap.Pins.Add` | `Pages/MainPage.xaml.cs:52-77` |
| Click en pin | evento `MarkerClicked` → `PinClickedEventArgs`; `e.HideInfoWindow = false` mantiene el tooltip nativo | `Pages/MainPage.xaml.cs:79-85` |
| Cambiar tipo | botones → `MyMap.MapType = MapType.Street` / `MapType.Satellite` | `Pages/MainPage.xaml.cs:87-89` |
| Ir a "Mi lugar" | `MoveToRegion` a coordenada fija | `Pages/MainPage.xaml.cs:91-97` |

## Ciclo de arranque

```
OnAppearing()  (MainPage.xaml.cs:15-22)
   ├─ await PedirPermisosAsync()   → IsShowingUser según permiso
   ├─ CentrarMapa(-31.749788, -60.520532)   → región inicial (~Paraná/Santa Fe)
   └─ AgregarPins()                → Obelisco + Casa Rosada (Buenos Aires)
```

## Objetos de dominio de mapa

| Tipo | Uso | Fuente |
|---|---|---|
| `Map` | Control principal (`x:Name="MyMap"`) | `Pages/MainPage.xaml:17` |
| `MapType` | `Street` (default) / `Satellite` | `Pages/MainPage.xaml.cs:87,89` |
| `Pin` + `PinType.Place` | Marcadores con `Label`/`Address`/`Location` | `Pages/MainPage.xaml.cs:56-69` |
| `MapSpan` + `Distance` | Región visible por centro y radio en km | `Pages/MainPage.xaml.cs:48,93-95` |
| `Location` | Coordenada (lat, lon) | `Pages/MainPage.xaml.cs:47` |

## Gotchas

- **API key hardcodeada y versionada** en `AndroidManifest.xml:11` (`com.google.android.geo.API_KEY`), lo que contradice la guía de secretos de `Ejemplos_Devices/GPS/Ejemplo_Docs_GPS/secret.md`. Debería restringirse/rotarse.
- **Android depende de Google Maps + Play Services**; iOS usa MapKit (no requiere API key); Windows requiere token propio no configurado aquí.
- `IsShowingUser` (punto azul) solo se activa si el permiso de ubicación fue concedido (`MainPage.xaml.cs:31-32`); sin permiso el mapa igual funciona.
- Proyecto **sin MVVM**: toda la lógica vive en el code-behind de la página; no hay servicios ni DI de dominio.
- La nota útil (link MS + NuGet) está en `Ejemplo_Maui_Mapas/Readme.md`; `Ejemplo_Docs_Maps/Readme.md` está vacío (corregido `@fd6a1ed`; antes figuraban invertidos).
