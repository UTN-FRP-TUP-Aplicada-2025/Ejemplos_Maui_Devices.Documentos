---
doc_id: PIECE-CAM-001
doc_type: piece-readme
title: Camera — captura de foto en .NET MAUI (5 variantes comparadas)
version: 1.0.0
status: draft
origin: reverse-engineered
confidence: high
owner: fernandofilipuzzi
last_review: 2026-07-14
review_cycle_days: 180
audience: [dev]
classification: uso-interno
traces: []
---

# Camera — captura de foto

> **Resumen ejecutivo**: este dominio reúne 5 proyectos didácticos de .NET MAUI (net10.0-android; iOS se agrega solo compilando en macOS) que resuelven el mismo problema —tomar una foto y mostrarla en la página principal— con técnicas incrementales: diálogo nativo del SO (`MediaPicker`), picker propio con `CameraView` del CommunityToolkit (transferencia por callback o por `Task`), normalización de imagen (rotación EXIF + resize + recompresión JPEG con MetadataExtractor/SkiaSharp) y selfie con cámara frontal + máscara ovalada dibujada. Sirve como catálogo de decisión para devs que necesitan captura de foto en una app MAUI: eligen la variante según cuánto control de UI, robustez de transferencia y posprocesamiento requieran.

## Proyectos y técnica que ilustran

| # | Proyecto | Técnica | Cuándo elegirla |
|---|----------|---------|-----------------|
| 1 | `Ejemplo_Photo_MediaPicker` | Diálogo nativo del SO vía `MediaPicker.Default` (`CapturePhotoAsync`/`PickPhotosAsync`), encapsulado en un servicio `Task<Stream?>` registrado en DI | Solo necesitás tomar/elegir una foto, sin controlar el visor. Mínimo código y sin paquetes extra |
| 2 | `Ejemplo_Photo_MiMediaPicker_Callback` | Picker propio con `CameraView` (CommunityToolkit); resultado devuelto como **path** a archivo temporal vía `Action<string?>` pasado por `ShellNavigationQueryParameters` | Querés controlar visor, flash, permisos y orientación, con navegación Shell. Es el patrón de transferencia recomendado (path + `CacheDirectory`) |
| 3 | `Ejemplo_Photo_MiMediaPicker_Task` | Mismo picker, pero el resultado se espera con `await page.ResultadoTask.Task` (`TaskCompletionSource<Image>`); `CameraView` declarado en XAML | Preferís un flujo lineal `await` sin registrar rutas Shell. Ojo: pasa el `Stream` del toolkit entre páginas (anti-patrón, ver Observaciones) |
| 4 | `Ejemplo_Photo_MiMediaPicker_Callback_Normalizacion` | Variante 2 + servicio `IImageService` inyectado que corrige orientación EXIF, redimensiona y recomprime a JPEG (MetadataExtractor + SkiaSharp) | La foto debe subirse o mostrarse rotada correctamente y liviana (p. ej. adjuntos a un backend) |
| 5 | `Ejemplo_Photo_MiMediaSelfie_Callback_Normalizacion` | Variante 4 con cámara **frontal** (`CameraPosition.Front`) + máscara ovalada `IDrawable` sobre `GraphicsView`, sensible al tema Light/Dark | Foto de perfil/rostro con guía visual de encuadre |

Los proyectos viven en `Ejemplos_Devices/Camera/`; la carpeta hermana `Ejemplo_Docs_Photo/` contiene la documentación transversal de la cátedra (nativo vs. toolkit y guía de transferencia entre pantallas).

## Estructura y proceso clave

Todas las variantes comparten dos ideas estructurales:

1. **Servicio tipado registrado en DI** para la lógica no visual:
   - Ej. 1: `ICamaraService`/`CamaraService` (`AddSingleton` en `MauiProgram.cs#L29`) encapsula `MediaPicker` y la copia defensiva del `Stream` a memoria.
   - Ej. 4: `IImageService`/`ImageDeviceAutoRotateService` (`AddSingleton` en `MauiProgram.cs#L38`) encapsula la normalización. El ej. 5 usa la misma clase (renombrada `ImageDeviceAutoRotate : IImageDevice`) pero instanciada con `new` en la página, sin DI.
2. **Página picker con overlay de estado** (ej. 2–5): una `ContentPage` con `BindingContext = this` (binding del ícono de flash), que crea el `CameraView` por código dentro de un `ContentView` (`CameraContainer`) —salvo el ej. 3, que lo declara en XAML—, evalúa permisos con `Check → Request → Rationale` y muestra un overlay con "Pedir permiso" / "Abrir configuración" cuando no puede mostrar el visor. La misma página maneja flash (Off→On→Auto), orientación dinámica del `Grid` y liberación del `CameraView` en `OnDisappearing`.

El flujo de punta a punta, narrado con el caso concreto del **ejemplo 4** (el usuario toca "Abrir cámara", saca la foto con la cámara trasera y la ve normalizada en la página principal):

```mermaid
sequenceDiagram
    actor U as Usuario
    participant MP as MainPage (consumidor)
    participant PK as MyMediaPickerPage (productor)
    participant CV as CameraView
    participant IS as IImageService

    U->>MP: tap "Abrir cámara"
    MP->>PK: Shell.GoToAsync(nameof(MyMediaPickerPage), { OnPhotoCallback })
    Note over PK: [QueryProperty] inyecta el Action<string?>
    PK->>PK: EvaluarYMostrarEstadoPermisoAsync()
    alt permiso denegado / restringido / sin cámara
        PK-->>U: overlay: reintentar o abrir Ajustes (AppInfo.ShowSettingsUI)
    else Granted
        PK->>CV: crea CameraView + SelectedCamera = Rear
    end
    U->>PK: tap "Tomar foto"
    PK->>CV: CaptureImage(token)  [guard _isCapturingImage]
    CV-->>PK: OnMediaCaptured(e.Media : Stream)
    PK->>PK: CopyToAsync → photo_{guid}.jpg en CacheDirectory (fuera del UI thread)
    PK->>MP: OnPhotoCallback(tempPath) + GoToAsync("..")
    MP->>IS: ProcesarPhotoAsync(path)
    Note over IS: EXIF (MetadataExtractor) → rotar/espejar<br/>→ resize → JPEG 75 (SkiaSharp)
    IS-->>MP: photo_norm_{guid}.jpg
    MP->>MP: File.ReadAllBytes(outPath); finally: borra ambos temporales
    MP->>U: Dispatcher.Dispatch → ImgPhoto.Source = FromStream(new MemoryStream(bytes))
```

En el ej. 2 el flujo es idéntico sin el paso de `IImageService`; en el ej. 5 cambia la cámara (`Front`, seleccionada desde el evento `CameraView.Loaded`) y se superpone la máscara; en el ej. 3 el productor no materializa a archivo: completa un `TaskCompletionSource<Image>` y el consumidor hace `await` (con `TrySetCanceled()` en `OnDisappearing` para destrabar el `await` si el usuario vuelve atrás).

## Cómo ejecutar

Compilación, emuladores y despliegue: ver el runbook central [build-and-run](../../07-operations/build-and-run.md). Lo específico de este dominio:

- **Dispositivo físico recomendado**: `CameraView` no funciona en el simulador de iOS; los ej. 2 y 5 lo detectan (`DeviceType.Virtual`) y muestran overlay "no disponible". En emulador Android funciona con la cámara virtual.
- **Target**: los 5 proyectos compilan `net10.0-android` (minSdk 25, targetSdk 36); el target `net10.0-ios` solo se agrega si se compila en macOS (condición `IsOSPlatform('osx')` en cada `.csproj`, líneas 4–5).
- Los permisos de cámara se piden **en runtime** al entrar al picker; no hace falta preconcederlos, pero si se deniegan con "no volver a preguntar" hay que habilitarlos desde Ajustes (el overlay ofrece el atajo).
- Cada proyecto es una app independiente con su propio `ApplicationId`; se ejecutan una por una (`dotnet build -t:Run -f net10.0-android` desde la carpeta del proyecto).

## Permisos y su justificación

Verificado contra `Platforms/Android/AndroidManifest.xml` y `Platforms/iOS/Info.plist` de cada proyecto (los 5 declaran además `INTERNET` y `ACCESS_NETWORK_STATE`, herencia de la plantilla MAUI, sin uso en el dominio).

| Permiso / clave | Plataforma | Proyecto(s) | Por qué se necesita |
|-----------------|------------|-------------|---------------------|
| `android.permission.CAMERA` | Android | 1, 2, 3, 4, 5 | Único permiso realmente ejercitado: lo pide `Permissions.RequestAsync<Permissions.Camera>()` antes de mostrar visor o disparar `CapturePhotoAsync` |
| `android.permission.RECORD_AUDIO` | Android | 1, 3, 4, 5 | En 1 viene de la plantilla de `MediaPicker` ("foto/video"); como ningún ejemplo graba video ni audio, **está declarado pero no se usa** en los cuatro |
| `android.permission.WRITE_EXTERNAL_STORAGE` (`maxSdkVersion=32`) | Android | 1 | Plantilla de `MediaPicker` para persistir la captura en API ≤ 32; el código no escribe storage externo directamente — **sin uso observable** |
| `uses-feature camera` + `camera.autofocus` (`required=true`) | Android | 1 | Filtra en Play Store dispositivos sin cámara/autofoco (la app no tendría sentido sin cámara) |
| `uses-feature camera` (`required=false`) | Android | 2 | Declara uso opcional: la página maneja la ausencia de cámara con overlay en vez de excluir dispositivos |
| `<queries>` intent `IMAGE_CAPTURE` | Android | 3, 4, 5 | Visibilidad de paquetes (Android 11+) para que `MediaPicker.Default.IsCaptureSupported` pueda resolver actividades de cámara. Nota: el ej. 2 también llama `IsCaptureSupported` (`MyMediaPickerPage.xaml.cs#L104`) pero **no** declara `<queries>` |
| `NSCameraUsageDescription` | iOS | 1, 2, 3, 4, 5 | Texto obligatorio del diálogo de permiso de cámara |
| `NSMicrophoneUsageDescription` | iOS | 1, 2, 3 | Plantilla de `MediaPicker`/captura de medios; no se graba audio — en el ej. 3 el propio texto lo admite ("This app does not require access to the microphone") — **sin uso** |
| `NSPhotoLibraryUsageDescription` | iOS | 4, 5 | **Declarado sin uso**: ninguno de los dos accede a la galería |
| `NSPhotoLibraryAddUsageDescription` | iOS | 3, 4, 5 | **Declarado sin uso**: no se guarda nada en la galería (los temporales van a `CacheDirectory`) |

Dato lateral: el ej. 1 usa `PickPhotosAsync()` (galería) sin declarar `NSPhotoLibraryUsageDescription`; en iOS 14+ el picker del sistema (PHPicker) no requiere ese permiso, así que no es un error, pero conviene saberlo al portar el código.

## Snippets canónicos

### 1. Servicio sobre `MediaPicker` con copia defensiva a memoria (ej. 1)

> Fuente: `Ejemplos_Devices/Camera/Ejemplo_Photo_MediaPicker/Services/CameraService.cs#L6–L21` @24d611d · Demuestra: uso canónico de `MediaPicker.Default` encapsulado en servicio y el invariante "copiar el `Stream` a memoria antes de retornar" (solución B del bug Stream/Glide de `MEDIAPICKER_FIX.md`).

```csharp
public Task<Stream?> TomarFotoAsync() =>
    MainThread.InvokeOnMainThreadAsync(async () =>
    {
        if (!MediaPicker.Default.IsCaptureSupported) return null;

        var foto = await MediaPicker.Default.CapturePhotoAsync();
        if (foto is null) return (Stream?)null;

        // Copiar a memoria antes de salir — el archivo temporal puede
        // limpiarse apenas termine el camera intent
        await using var src = await foto.OpenReadAsync();
        var ms = new MemoryStream();
        await src.CopyToAsync(ms);
        ms.Position = 0;
        return (Stream?)ms;
    });
```

- **Precondiciones**: dispositivo con cámara; permiso de cámara concedido (el diálogo nativo lo gestiona). Puede invocarse desde cualquier thread: `InvokeOnMainThreadAsync` garantiza el salto al UI thread que exige el intent.
- **Resultado esperado**: `MemoryStream` posicionado en 0 con el JPEG capturado, o `null` si no hay cámara o el usuario canceló. El `Stream` es seguro de usar aunque el SO limpie el archivo temporal.

### 2. Productor callback: materializar `e.Media` a archivo y devolver el path (ej. 2)

> Fuente: `Ejemplos_Devices/Camera/Ejemplo_Photo_MiMediaPicker_Callback/Pages/MyMediaPickerPage.xaml.cs#L276–L315` @24d611d · Demuestra: el invariante central del patrón recomendado — el `Stream` del toolkit nunca cruza páginas; se vuelca a `CacheDirectory` fuera del UI thread y se entrega un `string` path.

```csharp
private async void OnMediaCaptured(object? sender, MediaCapturedEventArgs e)
{
    string? tempPath = null;

    if (e.Media != null)
    {
        tempPath = Path.Combine(
            FileSystem.CacheDirectory,
            $"photo_{Guid.NewGuid():N}.jpg");

        try
        {
            using var fs = File.Create(tempPath);
            await e.Media.CopyToAsync(fs);
        }
        // … (si falla: borra el archivo a medio escribir y tempPath = null)
    }

    await MainThread.InvokeOnMainThreadAsync(async () =>
    {
        if (_cameraView?.IsAvailable == true)
        {
            OnPhotoCallback?.Invoke(tempPath);
            await Shell.Current.GoToAsync("..");
        }
        // …
    });
}
```

- **Precondiciones**: `CameraView` creado y con cámara seleccionada; captura disparada por `CaptureImage(token)` (con guard `_isCapturingImage`, `#L239–L254`); `OnPhotoCallback` inyectado por `[QueryProperty]` desde `ShellNavigationQueryParameters`.
- **Resultado esperado**: archivo `photo_{guid}.jpg` en `CacheDirectory`; el callback recibe el path (o `null` si falló la escritura o el usuario canceló con "Volver", `#L207–L212`) y la navegación vuelve a la página anterior. El consumidor es responsable de borrar el temporal.

### 3. Consumidor callback + normalización y limpieza en `finally` (ej. 4)

> Fuente: `Ejemplos_Devices/Camera/Ejemplo_Photo_MiMediaPicker_Callback_Normalizacion/Pages/MainPage.xaml.cs#L21–L55` @24d611d · Demuestra: consumo canónico del path — normalizar con el servicio inyectado, leer bytes, borrar ambos temporales pase lo que pase, y volver al UI thread solo para asignar el `ImageSource`.

```csharp
Action<string?> resultadoCallback = async (path) =>
{
    string? outPath = null;
    try
    {
        if (string.IsNullOrEmpty(path) || !File.Exists(path)) return;

        outPath = await _imageService.ProcesarPhotoAsync(path);

        byte[] bytes = File.ReadAllBytes(outPath ?? "");
        // …
        Dispatcher.Dispatch(() =>
        {
            ImgPhoto.Source = ImageSource.FromStream(() => new MemoryStream(bytes));
        });
    }
    // … (catch: log)
    finally
    {
        try
        {
            if (path != null) File.Delete(path);
            if (outPath != null) File.Delete(outPath);
        }
        // …
    }
};
```

- **Precondiciones**: `IImageService` inyectado por constructor (registrado como singleton en `MauiProgram.cs#L38`); el callback llega con un path existente en `CacheDirectory` (o `null`/inexistente si se canceló).
- **Resultado esperado**: `ImgPhoto` muestra la foto rotada, reescalada y recomprimida; no quedan archivos `photo_*.jpg` ni `photo_norm_*.jpg` residuales. La lambda que pasa a `FromStream` crea un `MemoryStream` **nuevo** por evaluación, tolerando re-lecturas de MAUI (rotación, recycling).

### 4. Contraste: patrón Task con `Stream` directo (ej. 3, anti-patrón deliberado)

> Fuente: `Ejemplos_Devices/Camera/Ejemplo_Photo_MiMediaPicker_Task/Pages/MyMediaPickerPage.xaml.cs#L227–L245` @24d611d · Demuestra: cómo el patrón `TaskCompletionSource` entrega el resultado, y el anti-patrón "Stream directo" que la doc de la cátedra desaconseja (el `Stream` del toolkit cruza páginas dentro del `ImageSource`).

```csharp
private async void OnMediaCaptured(object? sender, MediaCapturedEventArgs e)
{
    await MainThread.InvokeOnMainThreadAsync(async () =>
    {
        if (Camera.IsAvailable == true)
        {
            if (e.Media != null)
            {
                var image = new Image { Source = ImageSource.FromStream(() => e.Media) };
                ResultadoTask.TrySetResult(image);
            }
            await Navigation.PopAsync();
        }
        // …
    });
}
```

- **Precondiciones**: página empujada con `Navigation.PushAsync` (sin ruta Shell); el consumidor está bloqueado en `await destinoPage.ResultadoTask.Task` (`Pages/MainPage.xaml.cs#L15–L27`).
- **Resultado esperado**: el `await` del consumidor se completa con el `Image` y lo asigna a `ImgPhoto.Source`. **Fragilidad conocida**: la lambda devuelve siempre el mismo `Stream` `e.Media`; si MAUI re-evalúa el `ImageSource` (rotación, memoria) el stream ya está consumido/dispuesto. Si el usuario vuelve atrás sin foto, `OnDisappearing` hace `ResultadoTask.TrySetCanceled()` (`#L347–L350`) y el `catch` vacío del consumidor traga la `TaskCanceledException`.

### 5. Máscara de selfie: óvalo recortado con `WindingMode.EvenOdd` (ej. 5)

> Fuente: `Ejemplos_Devices/Camera/Ejemplo_Photo_MiMediaSelfie_Callback_Normalizacion/Utilities/SelfieMaskDrawable.cs#L16–L49` @24d611d · Demuestra: punto de extensión visual — dibujar un overlay con "agujero" elíptico sin assets de imagen, sensible al tema.

```csharp
public void Draw(ICanvas canvas, RectF dirtyRect)
{
    // …
    bool isDark = (Application.Current?.RequestedTheme ?? AppTheme.Light) == AppTheme.Dark;
    Color overlay = OverlayColor ?? (isDark ? Colors.Black : Colors.White);
    // … (calcula ew/eh según WidthFraction=0.75 y HeightToWidthRatio=1.35,
    //     limitado a 0.9·alto, centrado en ex/ey)
    var maskPath = new PathF();
    maskPath.AppendRectangle(dirtyRect);
    maskPath.AppendEllipse(ex, ey, ew, eh);
    canvas.FillColor = overlay;
    canvas.FillPath(maskPath, WindingMode.EvenOdd);

    if (BorderThickness > 0f)
    {
        canvas.StrokeColor = border;
        canvas.StrokeSize = BorderThickness;
        canvas.DrawEllipse(ex, ey, ew, eh);
    }
}
```

- **Precondiciones**: instancia declarada como recurso XAML y asignada a un `GraphicsView` con `InputTransparent="True"` superpuesto al `CameraContainer` (`Pages/MyMediaSelfiePickerPage.xaml#L10, L26`); ante cambio de tema la página llama `MaskOverlay.Invalidate()` (`.xaml.cs#L49–L52`).
- **Resultado esperado**: overlay blanco (Light) o negro (Dark) que cubre todo el visor salvo un óvalo central transparente donde se ve la cámara; el relleno rectángulo+elipse con `EvenOdd` produce el agujero. La máscara es solo guía visual: **no** recorta la foto capturada.

## Puntos de extensión

- **Resolución / peso de salida (ej. 4 y 5)**: propiedades `MaxWidthHeight` (default 1000), `CustomPhotoSize` (50 %) y `CompressionQuality` (75) del servicio (`Services/ImageDeviceAutoRotateService.cs#L9–L11`). En el ej. 4 se ajustan al registrar/inyectar; en el ej. 5 se pasan en el inicializador de objeto (`Pages/MainPage.xaml.cs#L26–L31`).
- **Formato de salida**: cambiar `SKEncodedImageFormat.Jpeg` en `ImageDeviceAutoRotateService.cs#L56` por `Png`/`Webp` (ajustando la extensión `photo_norm_*.jpg` de `#L85`).
- **Cámara seleccionada**: `SeleccionarCamaraAsync` filtra por `CameraPosition.Rear` (ej. 2–4, p. ej. `Ejemplo_Photo_MiMediaPicker_Callback/Pages/MyMediaPickerPage.xaml.cs#L223`) o `Front` (ej. 5, `MyMediaSelfiePickerPage.xaml.cs#L231`), siempre con fallback al primer dispositivo disponible. Cambiar la posición es un cambio de una línea.
- **Espejado de selfie**: hoy la normalización solo aplica orientación EXIF; para que la foto guardada coincida con el preview espejado de la cámara frontal, agregar un flip horizontal en `AplicarOrientation` (`Utilities/ImageDeviceAutoRotate.cs#L91` en adelante; las sobrecargas `Rotate(bitmap, angle, ex, ey)` ya existen).
- **Geometría de la máscara**: `WidthFraction`, `HeightToWidthRatio`, `OverlayColor`, `BorderColor`, `BorderThickness` (`SelfieMaskDrawable.cs#L8–L14`), configurables desde el recurso XAML; o reemplazar el `IDrawable` completo manteniendo el `GraphicsView`.
- **Mecanismo de transferencia**: el productor solo conoce `Action<string?>`; se puede sustituir el callback por `WeakReferenceMessenger` o un repository singleton sin tocar la captura (alternativas catalogadas en `Ejemplo_Docs_Photo/Transferencia_Foto_Entre_Pantallas.md` §10–11).
- **Normalización con DI en el ej. 5**: la clase `ImageDeviceAutoRotate` es idéntica al servicio del ej. 4; registrarla contra `IImageDevice` en `MauiProgram.cs` (como hace el ej. 4 con `IImageService`) elimina el `new` directo en la página.

## Observaciones

**Hechos verificados en el código:**

- **Bug Stream/Glide documentado**: `Ejemplo_Photo_MediaPicker/MEDIAPICKER_FIX.md` explica el `ObjectDisposedException` de `using Stream + FromStream(() => sourceStream)` (Glide lee lazy tras cerrarse el `using`). El `MainPage.xaml.cs` del ej. 1 conserva el código buggy y ambas soluciones comentadas (`#L28–L58`); la solución B es la que implementa `CamaraService`.
- **Inyección por tipo concreto (ej. 1)**: `MainPage` recibe `CamaraService`, no `ICamaraService` (`Pages/MainPage.xaml.cs#L11`), aunque el registro es `AddSingleton<ICamaraService, CamaraService>()` (`MauiProgram.cs#L29`). Funciona de casualidad didáctica: obliga a registrar también el concreto o falla la resolución (interpretación: inconsistencia a corregir).
- **`using Android.Media;` sin `#if` (ej. 1)**: `Pages/MainPage.xaml.cs#L1` importa un namespace exclusivo de Android en un archivo compartido. Hecho: el using no se usa en el código activo. Interpretación: rompería la compilación del target `net10.0-ios` que el `.csproj` agrega en macOS.
- **Nombre archivo ≠ clase (ej. 1)**: el archivo es `CameraService.cs` pero la clase es `CamaraService` (con "a").
- **Namespaces copiados (ej. 5)**: `MyMediaSelfiePickerPage` y su `x:Class` declaran `Ejemplo_Photo_MiMediaPicker_Callback_Normalizacion.Pages` (`.xaml.cs#L5`, `.xaml#L5`) —namespace del ej. 4—, mientras `App`, utilities y el resto usan `Ejemplo_Photo_MiMediaSelfie_...`. Compila porque `AppShell` y `MainPage` importan el namespace copiado.
- **Namespace del servicio (ej. 4)**: `IImageService.cs` e `ImageDeviceAutoRotateService.cs` están en `Services/` pero declaran `namespace ...Utilities` (`Services/IImageService.cs#L1`, `Services/ImageDeviceAutoRotateService.cs#L5`).
- **Doble `base.OnDisappearing()` (ej. 3)**: se llama al inicio y al final del método (`Pages/MyMediaPickerPage.xaml.cs#L334, L363`).
- **`catch` vacío en el consumidor Task (ej. 3)**: `Pages/MainPage.xaml.cs#L30–L32` traga cualquier excepción, incluida la `TaskCanceledException` esperada al volver atrás; es el mecanismo (implícito) de cancelación.
- **`<queries>` inconsistente**: el ej. 2 llama `MediaPicker.Default.IsCaptureSupported` pero su manifest no declara el intent `IMAGE_CAPTURE`; los ej. 3–5 sí. Interpretación: en Android 11+ la comprobación del ej. 2 podría devolver falso negativo por visibilidad de paquetes.
- **Selfie sin espejado**: la normalización del ej. 5 no aplica flip horizontal; la foto guardada puede diferir del preview espejado (hecho: no hay rama de espejo fuera de las orientaciones EXIF 2/4/5/7).
- **Limpieza y threading disciplinados**: los temporales se borran siempre en `finally` del consumidor, y todo acceso a controles se encola con `Dispatcher.Dispatch`/`MainThread` desde los threads de cámara/IO.

**Discrepancias índice ia-db ↔ código** (el índice `01_Camara.md` §8 quedó desactualizado respecto de los manifests):

1. El índice afirma que los proyectos con `CameraView` (2–5) declaran solo `CAMERA` + `uses-feature camera required=false`. En el código, eso vale **solo para el ej. 2**; los ej. 3, 4 y 5 declaran además `RECORD_AUDIO`, no tienen `uses-feature` y agregan `<queries>` con el intent `IMAGE_CAPTURE`.
2. El índice afirma `NSCameraUsageDescription` + `NSMicrophoneUsageDescription` para todos en iOS. En el código, los ej. 4 y 5 **no** declaran micrófono y sí `NSPhotoLibraryUsageDescription` + `NSPhotoLibraryAddUsageDescription`; el ej. 3 agrega `NSPhotoLibraryAddUsageDescription`.
3. Menor: el índice omite el `uses-feature camera.autofocus required=true` del ej. 1 (`AndroidManifest.xml#L20`).

El resto de las afirmaciones del índice contrastadas (líneas del anti-patrón, selección de cámara, defaults de normalización, versiones de paquetes, anomalías de namespace) coinciden con el fuente.

## Preguntas guía

1. ¿Necesitás controlar el visor (flash, overlay de permisos, orientación, máscara) o te alcanza con el diálogo del SO? → Si alcanza el diálogo: ej. 1; si no: ej. 2–5.
2. ¿La foto va a cruzar pantallas o subirse a un backend? → Usá el patrón path + `CacheDirectory` del ej. 2 (el `Stream` directo del ej. 3 está para estudiar por qué **no** hacerlo).
3. ¿Importa que la foto llegue derecha y liviana (EXIF corregido, ≤1000 px, JPEG 75)? → ej. 4 (con DI) es la base; ajustá los tres parámetros del servicio.
4. ¿Es una foto de rostro/perfil con encuadre guiado? → ej. 5; y evaluá si necesitás agregar el espejado que hoy falta.

## Referencias

- Índice ia-db del dominio: [01_Camara.md](../../../../ia-db/indexes/01_Camara.md)
- Mapa del sistema: [system-map](../../00-overview/system-map.md)
- Runbook de ejecución: [build-and-run](../../07-operations/build-and-run.md)
- Proyectos fuente (repo `Ejemplos_Maui_Devices` @24d611d):
  - `Ejemplos_Devices/Camera/Ejemplo_Photo_MediaPicker/`
  - `Ejemplos_Devices/Camera/Ejemplo_Photo_MiMediaPicker_Callback/`
  - `Ejemplos_Devices/Camera/Ejemplo_Photo_MiMediaPicker_Task/`
  - `Ejemplos_Devices/Camera/Ejemplo_Photo_MiMediaPicker_Callback_Normalizacion/`
  - `Ejemplos_Devices/Camera/Ejemplo_Photo_MiMediaSelfie_Callback_Normalizacion/`
  - Documentación transversal de la cátedra: `Ejemplos_Devices/Camera/Ejemplo_Docs_Photo/` (`Readme.md`, `Transferencia_Foto_Entre_Pantallas.md`)
