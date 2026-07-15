---
doc_id: API-BRIDGE-001
doc_type: api-contract
title: Contrato del puente de comandos por URL y endpoints del backend
version: 1.0.0
status: draft
origin: reverse-engineered
confidence: high
owner: fernandofilipuzzi
last_review: 2026-07-14
review_cycle_days: 90
audience: [dev, agentes-automaticos]
classification: uso-interno
traces: []
---

# Contrato del puente WebView ↔ nativo

> Especificación del **Canal B** de `Ejemplo_Maui_Hibrida`: los 7 comandos por URL que la web puede invocar (verificados uno a uno contra el código @24d611d), la convención de despacho, y los endpoints/páginas del backend `Ejemplo_ws_Blazor`. Un comando es una URL que contiene un **marcador** en el query string (convención: `clave=clave`); la app cancela esa navegación (nunca llega al servidor), ejecuta el efecto nativo y responde por una de tres vías: **JS inyectado al DOM**, **re-navegación con query params**, o **sólo efecto nativo + overlay**. Todas las rutas de fuente son relativas al repo `Ejemplos_Maui_Devices`, prefijo común `Ejemplos_Devices/Integrada/`.

## Catálogo de comandos por URL

Orden de la tabla = orden de evaluación (registro DI en `Ejemplo_Maui_Hibrida/MauiProgram.cs#L108–L114`). El matching es `url.Contains(marcador, OrdinalIgnoreCase)`: el marcador puede ir en cualquier parte de la URL y no importa el path.

| # | Patrón de URL (marcador + parámetros) | Handler (clase · fuente) | Parámetros | Efecto nativo | Respuesta hacia la web |
|---|---|---|---|---|---|
| 1 | `coordenadas=coordenadas` | `GpsCommandHandler` · `Ejemplo_Maui_Hibrida/LibApp/UrlCommands/Handlers/GpsCommandHandler.cs#L16–L33` | — (el marcador es literal; no toma parámetros) | Overlay GPS (`GpsOverlayViewModel.SolicitarGeolocalizacion`): permisos + fix de posición (`GpsService`: caché <1 min o `GetLocationAsync` Medium/10 s) | **Re-navegación**: éxito → `BridgeOutcome(true, url')` donde `url'` reemplaza el marcador por `Latitud={lat}&Longitud={lng}&` (`#L24–L28`). Error → overlay con acciones y `(true, null)` (se queda) |
| 2 | `phone=phone` | `CallCommandHandler` · `…/Handlers/CallCommandHandler.cs#L20–L26` | — (número **hardcodeado** `3434807427`, valor de prueba, `#L11`) | Llamada directa `CallMode.Direct` vía `CallOverlayViewModel.LlamarAsync` (permiso `CALL_PHONE` en runtime; overlay para denegado/error) | **Ninguna al DOM**: `(true, null)`; el feedback es el overlay nativo y la propia llamada |
| 3 | `photo=photo&param={idElemento}` | `CameraCommandHandler` · `…/Handlers/CameraCommandHandler.cs#L23–L68` | `param` = id del elemento DOM destino (**obligatorio**: si falta → no-op `(true, null)`, `#L29–L30`) | `Shell.GoToAsync(MyMediaPickerPage)` con callback; foto → `IImageService.ProcesarPhotoAsync` (normaliza rotación EXIF) → JPEG base64; borra el archivo temporal (`#L38–L49`) | **JS**: `document.getElementById('{id}').src = '{dataUri}'` y `.value = '{dataUri}'` con `data:image/jpeg;base64,…` (`#L56–L64`). Cancelación (path null) → sin inyección |
| 4 | `selfie=selfie&param={idElemento}` | `SelfieCommandHandler` · `…/Handlers/SelfieCommandHandler.cs#L22–L65` | Ídem `photo` | Ídem foto pero con `MyMediaSelfiePickerPage` (cámara frontal con máscara `SelfieMaskDrawable`) | Ídem foto: `src` + `.value` del elemento destino |
| 5 | `qr=qr&param={idElemento}` | `QrCommandHandler` · `…/Handlers/QrCommandHandler.cs#L20–L49` | `param` = id del elemento DOM destino (**obligatorio**) | `Navigation.PushAsync(new QRLectorPage())` (BarcodeScanning.Native.Maui); espera `ResultadoTask` (`TaskCompletionSource<List<QRContent>>`); "Volver" completa con lista vacía | **JS**: `#id.textContent` = lista completa serializada JSON, p. ej. `[{"Type":"QRCode","Value":"TICKET-DEMO-0001"}]`; id y payload escapados con `JsonSerializer` (`#L45–L47`). Al cancelar inyecta `[]` (ver Divergencias) |
| 6 | `sendApi=sendApi&httpMethod={Post\|Get}&url={urlEncoded}&param={callId}&body={jsonUrlEncoded}` | `SendApiCommandHandler` · `…/Handlers/SendApiCommandHandler.cs#L23–L47` | `httpMethod`: sólo `Post`/`Get` case-insensitive, otro → `Blocked` sin request (`#L50–L55`); `url`: destino absoluto url-encoded (**allowlist**); `param`: id del elemento destino; `body`: JSON url-encoded, sólo se envía en POST | `ApiRelayService.SendAsync`: allowlist de hosts = `{ geolocate.somee.com }` (`…/LibApp/Devices/GPS/ApiRelayService.cs#L14–L17,L30–L31`), timeout 30 s, `Content-Type: application/json` en POST | **JS**: `#callId.textContent` = `{"ok":bool,"status":int,"body":string}` serializado con `JsonSerializer` (`#L57–L85`). Mapeo: `Success`/`HttpError`(≥400)/`NetworkError`/`Timeout`/`Cancelled`/`Blocked` |
| 7 | `action=print` | `PrintCommandHandler` · `Ejemplo_Maui_Hibrida/LibApp/UrlCommands/Handlers/PrintCommandHandler.cs#L34–L67` | — (endpoint del documento **hardcodeado**: `https://aplicada.somee.com/api/Tikects/comprobante`, `#L16`; timeout 30 s `#L17`) | GET del `PrintDocument` JSON → valida el contrato deserializando (`#L82–L84`) → `IDocumentEngine.Render(json, DeviceProfile("58HB6", 32, "escpos-bitmap") + capacidades bitmap)` (`#L39–L49`) → si render OK, `PrinterOverlayViewModel.ImprimirAsync`: permisos BT → discover → default/selector → connect (memoriza `default_printer_id`) → envío ESC/POS | **Ninguna al DOM**: `(true, null)`; todo el feedback (permiso, sin impresoras, BT off, selector, conexión fallida, éxito) es el overlay nativo. Render con errores → overlay "No se pudo generar el documento", no imprime (`#L52–L56`) |

### Ejemplos de invocación (sintéticos, tal como los emite `Panel.razor`)

```text
/panel?coordenadas=coordenadas
    → la app re-navega a: /panel?Latitud=-31.6331&Longitud=-60.7002&        (ejemplo sintético)

/panel?phone=phone

/panel?photo=photo&param=imgFoto1
/panel?selfie=selfie&param=imgSelfie1

/panel?qr=qr&param=contenidoQR

/panel?sendAPI=sendAPI&httpMethod=Post&url=https%3a%2f%2fgeolocate.somee.com%2fapi%2fGeoReporter%2ftrack&param=contenidoSendAPI&body=%7b%22Latitude%22%3a-31.7496689%2c%22Longitude%22%3a-60.5213019%7d

/panel?action=print
```

> Procedencia de los ejemplos: `Ejemplo_ws_Blazor/Components/Pages/Panel.razor#L157–L298` (cada botón navega con `forceLoad: true`, que fuerza una navegación real del browser — condición necesaria para que el WebView dispare `Navigating`). Los mismos comandos pueden dispararse desde botones nativos sin web: `MainViewModel.cs#L43–L70` (`phone=phone`, `qr=qr&param=contenidoQR`, `coordenadas=coordenadas`).

### Contratos de datos de la respuesta

- **Foto/selfie**: string `data:image/jpeg;base64,{…}` asignado a `src` (para `<img>`) y `value` (para `<input hidden>`) del mismo id.
- **QR**: array JSON de `QRContent { Type: string, Value: string }` (`Ejemplo_Maui_Hibrida/LibApp/Devices/QRLector/Models/QRContent.cs`); `Type` es el tipo de código (`QRCode`, `Code39`, o `Text` si es desconocido, `QRLectorPage.xaml.cs#L51`).
- **sendApi**: objeto JSON `{ ok, status, body }`; `status = 0` cuando no hubo respuesta HTTP (bloqueado/timeout/red); `body` trae el cuerpo o el mensaje de error legible (`SendApiCommandHandler.cs#L59–L68`).
- **GPS**: no inyecta; la página lee `Latitud`/`Longitud` del query string de la re-navegación (en Blazor: `[SupplyParameterFromQuery]`, `Panel.razor#L164–L177`).

## Convención de despacho

Contrato (`Ejemplo_Maui_Hibrida/LibApp/UrlCommands/IUrlCommandHandler.cs#L5–L9`): `bool CanHandle(string url)` + `Task<BridgeOutcome> HandleAsync(string url)`. Resultado (`BridgeOutcome.cs#L5`): `record(bool CancelNavigation, string? NavigateTo = null)`.

Resolución **first-match-wins** en orden de registro DI:

> Fuente: `Ejemplos_Devices/Integrada/Ejemplo_Maui_Hibrida/LibApp/UrlCommands/UrlCommandDispatcher.cs#L14–L26` @24d611d · Demuestra: `IsCommand` sincrónico (para poder fijar `e.Cancel` antes del primer `await` del pipeline de `Navigating`) separado del `DispatchAsync` que ejecuta; el primer `CanHandle` que matchea gana; sin match, navegación normal.

Precondiciones: handlers registrados como `AddSingleton<IUrlCommandHandler, …>` en `MauiProgram.cs#L108–L114` (el orden de esas líneas ES la prioridad). Resultado: exactamente un handler ejecuta por URL de comando.

```csharp
// Permite cancelar la navegación de forma sincrónica, antes de cualquier await.
public bool IsCommand(string url) => _handlers.Any(h => h.CanHandle(url));

public async Task<BridgeOutcome> DispatchAsync(string url)
{
    foreach (var handler in _handlers)
    {
        if (handler.CanHandle(url)) return await handler.HandleAsync(url);
    }

    // Ningún comando matchea: navegación normal.
    return new BridgeOutcome(false, url);
}
```

Reglas derivadas (verificadas):

1. **Un solo comando por URL**: si conviven dos marcadores, ejecuta únicamente el de menor índice de registro. Caso documentado en la propia web: `phone=phone&sendAPI=…` sólo dispara la llamada, porque `CallCommandHandler` (2.º) se evalúa antes que `SendApiCommandHandler` (6.º) — `Panel.razor#L185–L194`.
2. **Matching por substring, case-insensitive**: no hay parsing de path ni de esquema; cualquier URL (incluso relativa, como las de los botones nativos) que contenga el marcador es comando.
3. **Parseo de parámetros**: helper local `GetQueryValue(url, key)` idéntico en cada handler — split por `&`/`=`, comparación de clave case-insensitive y `Uri.UnescapeDataString` del valor (p. ej. `CameraCommandHandler.cs#L70–L80`).
4. **Cancelación**: todos los handlers devuelven `CancelNavigation = true`; la cancelación efectiva ya ocurrió en `MainViewModel.Navigating` vía `IsCommand` (`ViewModels/MainViewModel.cs#L80–L81`). Sólo GPS usa `NavigateTo` para re-navegar (`MainViewModel.cs#L85–L86`).
5. **Vuelta al DOM**: siempre vía `IWebViewBridge.RunScript(js)` → `WebViewBridgeBehavior` → `EvaluateJavaScriptAsync` en `MainThread`, fire-and-forget (`LibApp/CustomWebView/Behaviors/WebViewBridgeBehavior.cs#L61–L64`). No hay confirmación de entrega ni valor de retorno hacia el handler.

## Endpoints/páginas del backend `Ejemplo_ws_Blazor`

Pipeline (`Ejemplo_ws_Blazor/Program.cs`): Razor Components Interactive Server con `DetailedErrors` (`#L8`), controllers (`#L11`), OpenAPI nativo (`#L14`), `ForwardedHeaders` con `KnownNetworks/KnownProxies` vaciados para confiar en el proxy TLS de somee (`#L21–L31`), `MapOpenApi()` + `MapScalarApiReference()` **sin** gate de entorno (`#L37–L42`), re-ejecución de 404 hacia `/not-found` (`#L49`), antiforgery (`#L54`), y `MapRazorComponents<App>().AddInteractiveServerRenderMode()` + `MapControllers()` (`#L57–L60`). Local: `http://localhost:5284` / `https://localhost:8080` (`Properties/launchSettings.json#L4–L21`). Publicado (externo, no verificable desde el repo): `aplicada.somee.com` y `geolocate.somee.com`.

| Ruta | Método / tipo | Propósito | Datos de ejemplo (sintéticos) | Fuente |
|---|---|---|---|---|
| `/` y `/datos` | Página Blazor (InteractiveServer) | Prueba mínima de interactividad del Canal A: `InputText` + botón que refleja el contenido | entrada `"prueba"` → muestra `"prueba"` | `Components/Pages/Datos.razor#L1–L34` |
| `/panel` | Página Blazor (InteractiveServer) | **Panel demo del puente**: botones que emiten TODOS los comandos del catálogo, elementos DOM destino (`imgFoto1`, `inputFoto1`, `imgSelfie1`, `contenidoQR`, `contenidoSendAPI`, `contenidoTicket`) y recepción de `Latitud`/`Longitud` por query | ver "Ejemplos de invocación" | `Components/Pages/Panel.razor#L2,L133–L299` |
| `/redirigir` | Página Blazor (InteractiveServer) | Pruebas de redirección a host externo desde el WebView (botón, `href`, `_blank`, JS) — reproducción del salto a Safari en iOS | destino `https://www.google.com` | `Components/Pages/Redirigir.razor#L1–L32` |
| `/Error` | Página Blazor | Página de error estándar | — | `Components/Pages/Error.razor#L1` |
| `/not-found` | Página Blazor (+ re-execute de status 404) | Página de 404 propia | — | `Components/Pages/NotFound.razor#L1`, `Program.cs#L49` |
| `GET /api/Tikects/comprobante` | API REST (JSON) | Devuelve un `PrintDocument` **hardcodeado** (comprobante de ticket municipal de demo: logo bitmap base64, texto normal/negrita/centrado, separadores, contenedores anidados y un nodo `qrcode` al detalle del ticket). Es lo que consume `PrintCommandHandler` y lo que MotorDsl renderiza a ESC/POS. *(Sí: "Tikects" está así escrito en la ruta real.)* | `{"id":"comprobante-ticket-48213","version":"1.0","format":"integrated","root":{"type":"container","children":[…]}}` | `Controllers/TikectsController.cs#L12–L14,L27–L44` (builder `#L51–L160`) |
| `POST /api/GeoReporter/track` | API REST (JSON → string) | Recibe una posición y responde la concatenación `"{lat}-{lng}"`; destino de demo del relay `sendApi` | request `{"Latitude":-31.7496689,"Longitude":-60.5213019}` → response `"-31,7496689--60,5213019"` (formato según cultura del server) | `Controllers/GeoReporterController.cs#L17–L41`, DTO `DTOs/LocationDto.cs#L3–L7` |
| `POST /api/pagofake/pago` | API REST (form, sin antiforgery) | Prueba A del flujo de pago: 302 a host externo (`https://httpbin.org/anything`) para aislar el comportamiento del WebView ante redirect cross-host | — | `Controllers/PagoFakeController.cs#L11–L13` |
| `GET /api/pagofake/pago-form` | API REST (devuelve HTML) | Prueba B: HTML con `<form>` auto-submit POST a otro host (imita el form de pasarela de pago real) | hidden `monto=500000` → POST a `https://httpbin.org/post` | `Controllers/PagoFakeController.cs#L17–L26` |
| `GET /openapi/v1.json` | Documento OpenAPI | Especificación generada de los controllers | — | `Program.cs#L39` |
| `GET /scalar` | UI (Scalar) | Explorador interactivo de la API | — | `Program.cs#L41` (paquete `Scalar.AspNetCore 2.14.14`, `csproj#L12`) |
| `/pago-fake.html` · `/pago-fake-web.html` | Estáticos | Páginas de prueba del flujo de pago cross-host | — | `wwwroot/` |
| `/ejemplos/qr.ejemplo.png` | Estático | Imagen QR de ejemplo para probar el lector | — | `wwwroot/ejemplos/qr.ejemplo.png` |

**DTOs de impresión** (`DTOs/Print/`): `PrintDocument { Id, Version="1.0", Format="integrated", Root }`, `PrintNode` (tipos `text` | `image` bitmap/qrcode | `container`), `PrintStyle { align, bold }` y la fábrica `N` (`Text/Separator/Image/QrCode/Container/Empty`). Están **duplicados a propósito** (idénticos) en el backend (`Ejemplo_ws_Blazor/DTOs/Print/`) y en la app (`Ejemplo_Maui_Hibrida/LibApp/Devices/MotorDSL/DTOs/Print/`) para desacoplar los proyectos: el JSON serializado de ese árbol **es** el contrato que MotorDsl renderiza.

## Divergencias y observaciones

Todo lo listado abajo está verificado contra el código @24d611d salvo indicación de confidence menor. Nada de lo no verificable se omitió: se marca explícitamente.

| # | Observación | Evidencia | Confidence |
|---|---|---|---|
| 1 | **Doc-comment desactualizado en sendApi**: el comentario del handler describe `tipoRequest=Post\|Get`, pero el parámetro que realmente se lee es `httpMethod` | `SendApiCommandHandler.cs#L9` (comentario) vs `#L29` (código) | alta |
| 2 | **QR al cancelar inyecta `[]`**: el comentario del handler dice « "Volver" o lista vacía = cancelado (no inyecta) », pero el chequeo de lista vacía está comentado; al volver sin leer, se inyecta el array vacío serializado en el destino | `QrCommandHandler.cs#L10` (comentario) vs `#L36–L37` (chequeo comentado) y `#L45–L47` (inyección incondicional) | alta |
| 3 | **Hook `window.recibirRespuestaApi` es código muerto**: la página lo registra (`Panel.razor#L145–L150`) y el handler conserva la llamada comentada, pero la implementación vigente escribe `textContent` directo sobre `#callId`. Lo mismo con `window.recibirFoto` en cámara/selfie (comentado) | `SendApiCommandHandler.cs#L71–L77`, `CameraCommandHandler.cs#L59–L64` | alta |
| 4 | **Escapado asimétrico en las inyecciones**: QR serializa id **y** payload con `JsonSerializer` (estricto); sendApi serializa el payload pero lo interpola dentro de comillas simples y el `callId` va sin escapar; foto/selfie interpolan id y dataURI crudos (el dataURI base64 es seguro por construcción; el id depende de la página). Un `body` con comilla simple podría romper el script de sendApi. No probado en runtime | `QrCommandHandler.cs#L46` vs `SendApiCommandHandler.cs#L75–L77` vs `CameraCommandHandler.cs#L61–L62` | media (análisis estático) |
| 5 | **Perfil de render inline vs perfiles de DI**: `MauiProgram` registra `thermal_58mm`/`a4-pdf`/`pdf` con `AddProfiles`, pero `PrintCommandHandler` construye su propio `DeviceProfile("58HB6", 32, "escpos-bitmap")`; los perfiles registrados no participan del flujo del puente | `MauiProgram.cs#L54–L60` vs `PrintCommandHandler.cs#L39–L42` | alta |
| 6 | **Ruta Shell de QR sin uso**: `AppShell` registra la ruta `QRLectorPage`, pero el handler instancia y pushea la página directo (`PushAsync`), a diferencia de foto/selfie que sí van por `Shell.GoToAsync` | `AppShell.xaml.cs#L14` vs `QrCommandHandler.cs#L38–L39` | alta |
| 7 | **Hosts somee duales**: web + comprobante en `aplicada.somee.com`; allowlist del relay y destino del sendApi de demo en `geolocate.somee.com`. Qué despliegue vive en cada host es información externa al repo | `MainPage.xaml.cs#L17`, `PrintCommandHandler.cs#L16` vs `ApiRelayService.cs#L16`, `Panel.razor#L252` | **no verificado** (despliegue externo) |
| 8 | **Valores de prueba hardcodeados**: número de llamada `3434807427` (`CallCommandHandler.cs#L11`), endpoint del comprobante, allowlist de un solo host. El comando `phone` no acepta número por URL | citas en el catálogo | alta |
| 9 | **`OnGenerarTicket` vacío**: el botón "Generar Ticket" de `/panel` no hace nada; sólo "Imprimir" dispara `action=print` | `Panel.razor#L293–L298` | alta |
| 10 | **Índice ia-db 08 §4.2 dice "inyección segura … (QR y sendAPI)"**: exacto para QR; para sendApi vale con el matiz del punto 4. No es contradicción dura, se deja constancia | índice `ia-db/indexes/08_App-Hibrida-Integrada.md` §4.2/§8 | alta |
| 11 | **Respuesta real de `/api/GeoReporter/track`**: interpolación `$"{lat}-{lng}"` — con longitudes negativas produce doble guion y el separador decimal depende de la cultura del servidor. El índice la resume como `"lat-lng"` (coincide) | `GeoReporterController.cs#L31` | alta |
| 12 | **Comportamiento en producción de somee** (TLS del proxy, forwarded headers) y el **bug de iOS del Canal A** (circuito SignalR muere en WKWebView) se citan de `Docs/web-hibrida/maui-hibrido.md` y del comentario de `Program.cs#L16–L20`; no se reprodujeron en este relevamiento | — | **no verificado** (runtime externo) |

Referencias: [README de la pieza](README.md) · índice [08_App-Hibrida-Integrada](../../../../ia-db/indexes/08_App-Hibrida-Integrada.md) · docs de dominio `Ejemplos_Devices/Docs/web-hibrida/` (`maui-hibrido.md`, `lectura-qr.md`, `captura-foto.md`, `llamada.md`, `envio-api.md`).
