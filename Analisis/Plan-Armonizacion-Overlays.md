# Plan de armonización — patrón de overlays de dispositivo

> **Objetivo**: llevar los cuatro overlays de `Ejemplo_Maui_Hibrida` (GPS, Red, Telefonía, Impresión) a un mismo conjunto de reglas verificables, corrigiendo los defectos relevados en [08-pantallas-por-dispositivo §7](../Ejemplos_Maui_Devices-docs/docs/01-architecture/08-pantallas-por-dispositivo.md#7-observaciones).
>
> **Estado (2026-07-17)**: **ejecutado**. Los cuatro overlays cumplen los cinco invariantes;
> la suite de **116 tests pasa completa** y los tres proyectos compilan sin errores.
> Decisiones resueltas: [§6.1](#61-decisión-que-bloquea-el-lote-3) → opción B (no generalizar);
> [§6.2](#62-decisión-cancelled-en-gps-y-telefonía) → `Cancelled` recibe pantalla propia sin
> depender de D-1. **Pendiente**: la matriz de prueba en dispositivo ([§5.5](#55-matriz-de-prueba-manual-en-dispositivo)).
> Resultado detallado en [§9](#8-resultado-de-la-ejecución).
>
> **Tesis**: el patrón no necesita rediseño. Necesita que **las cuatro implementaciones cumplan las mismas reglas**, que hoy sólo cumple una.
>
> **Método**: cada defecto se verificó contra el código; cada cambio propuesto se acompaña de la traza de sus consumidores reales ([§5](#5-verificación-de-no-regresión)). Fuente: `Ejemplos_Maui_Devices @fd6a1ed` + las correcciones de impresión del 2026-07-16.

---

## Tabla de contenidos

1. [Diagnóstico](#1-diagnóstico)
2. [Los cinco invariantes](#2-los-cinco-invariantes)
3. [Matriz de conformidad](#3-matriz-de-conformidad)
4. [Plan de cambios](#4-plan-de-cambios)
5. [Verificación de no-regresión](#5-verificación-de-no-regresión)
6. [Riesgos y decisiones abiertas](#6-riesgos-y-decisiones-abiertas)
7. [Estrategia de test](#7-estrategia-de-test)
8. [Resultado de la ejecución](#8-resultado-de-la-ejecución)

---

## 1. Diagnóstico

### 1.1 La raíz común

Los nueve defectos relevados no son nueve problemas: son **tres**, repetidos en dominios distintos.

| Raíz | Manifestaciones | Por qué pasó |
|---|---|---|
| **La información no llega a la pantalla** | GPS: 5 variantes silenciosas · Telefonía: mensaje crudo · GPS: colapso a `Failure("")` | Modelar el caso se confundió con comunicarlo |
| **Hay código que no se puede ejecutar** | `case Success` muerto (GPS, Telefonía) · `Cancelled` inalcanzable · `NotSupported` inalcanzable en Android | Un tipo cerrado da ilusión de exhaustividad; C# no verifica que cada variante se produzca |
| **La UI no guía** | Pantallas sin botón primario (GPS, Telefonía) | `Primary` es «el trigger no disparó»: omitirlo no da error |

**Impresión sufría las tres y hoy no sufre ninguna.** No porque su código sea mejor, sino porque **fue el único auditado contra un dispositivo real**. La compilación no revela nada de esto: un `case` inalcanzable compila perfecto, y una propiedad que nadie lee tampoco molesta a nadie.

Esa es la verdadera conclusión del relevamiento: **la disciplina no se sostiene sola**. Por eso este plan no propone sólo parches, sino [invariantes verificables](#2-los-cinco-invariantes).

### 1.2 Dos hallazgos nuevos de esta verificación

**N-1 · `Coordenadas` y `Estado` no tienen ningún consumidor.** Se verificó por búsqueda exhaustiva sobre `.cs` y `.xaml` del proyecto: **cero bindings, cero lectores**. Son propiedades de solo escritura.

Esto agrava P-1: las cinco ramas silenciosas de GPS no escriben «en otro lado» — escriben en un canal **que no existe**. El texto «El GPS está desactivado. Activalo desde ajustes.» no se muestra en ningún lugar de la aplicación, hoy, en ninguna circunstancia.

**`Estado` (Telefonía) es un caso extremo y vale nombrarlo aparte**: tiene exactamente **dos** asignaciones, y **las dos están en ramas inalcanzables** — el `case Success` que el guard nunca deja correr (`:94`) y el `case Cancelled` que ningún token puede disparar (`:119`). Es una propiedad declarada, escrita sólo desde código muerto, y leída por nadie. No hay forma de que tome un valor ni de que alguien lo vea.

**N-2 · Otro comentario que documenta la intención, no el código.** `GpsCommandHandler.cs:31` afirma:

```csharp
// El overlay ya muestra el error; se queda en la página.
return new BridgeOutcome(true, null);
```

Es falso para 5 de las 8 variantes. Es el mismo género de comentario que ocultó H-1 en impresión durante toda la vida del PoC ([07 §8.5](../Ejemplos_Maui_Devices-docs/docs/01-architecture/07-overlays-dispositivos.md#85-el-comentario-que-documenta-la-intención-no-el-código)). Un auditor que lea comentarios concluye que no hay bug.

### 1.3 Restricción del contexto

**No hay tests en la solución.** Se verificó: no existe ningún `.csproj` de test. La verificación de no-regresión se apoya en tres patas — traza de consumidores, compilación y prueba en dispositivo — y ninguna es una suite automatizada. Es la limitación central de este plan ([§6](#6-riesgos-y-decisiones-abiertas)).

---

## 2. Los cinco invariantes

Armonizar no es «que se parezcan». Es que las cuatro implementaciones cumplan **las mismas reglas comprobables**. Cada invariante nace de un defecto real y es verificable por lectura del código.

| # | Invariante | Nace de | Cómo se verifica |
|---|---|---|---|
| **I-1** | Toda variante no-`Success` produce **exactamente una pantalla** | GPS: 5 ramas silenciosas | Cada `case` termina en un `ShowError`. Ninguno termina en `Hide()` solo |
| **I-2** | Toda variante **se construye** en un punto alcanzable | `Cancelled`, `BluetoothOff` | Por cada variante, existe un `new` y ese punto se alcanza |
| **I-3** | **Ningún mensaje crudo del sistema llega al usuario**; el original se conserva | Telefonía: `f.Message` · Impresión: `paper out` | La UI recibe `UserMessage`; `TechnicalMessage`/`Exception` quedan en el resultado |
| **I-4** | Toda pantalla de error tiene **exactamente un botón primario** | GPS y Telefonía sin primario | Un `OverlayAction` sin `Secondary` por `ShowError` |
| **I-5** | El resultado tipado **no se colapsa** al cruzar la frontera del VM | GPS: `Failure("")` | El VM devuelve la variante que recibió |

**I-1 es el más importante y el más barato de verificar**: un `case` que no llama a `ShowError` es, por definición, una situación que el usuario no ve.

> **I-2 no se puede delegar al compilador.** Es la lección más cara del patrón y no tiene solución automática en C#: un `abstract record` sellado no obliga a construir sus variantes. La única defensa es trazar cada `new` al agregar una variante. Por eso es un invariante y no una regla de estilo.

---

## 3. Matriz de conformidad

Estado actual, verificado contra el código:

| Invariante | Impresión | GPS | Telefonía | Red |
|---|---|---|---|---|
| **I-1** · toda variante muestra algo | ✔ | ✘ **5 silenciosas** | ✘ 1 (`Cancelled`) | ✔ |
| **I-2** · toda variante es alcanzable | ✔ ¹ | ✘ 2 | ✘ 2 | ✔ |
| **I-3** · nada crudo al usuario | ✔ | ✘ (`Failure`) | ✘ (`Failure`) | ✔ |
| **I-4** · un botón primario | ✔ | ✘ 1 pantalla | ✘ 1 pantalla | ✔ |
| **I-5** · no colapsa el resultado | ✔ | ✘ `Failure("")` | n/a ² | ✔ |

¹ Con una inalcanzabilidad deliberada y documentada: el `catch` de `DiscoverAsync` como red de seguridad.
² `CallCommandHandler` descarta el resultado (`_ = await`), así que no hay frontera que preservar.

**Red cumple los cinco.** Sus defectos (P-2 `url` ignorado, P-8 carrera de la sonda) son de otra naturaleza y se tratan aparte.

**Impresión cumple los cinco** desde el 2026-07-16.

**GPS incumple los cinco.** Es el foco del plan.

---

## 4. Plan de cambios

Ordenado por impacto en el usuario. Los lotes 1 y 2 son independientes; el 3 toca la base y el 4 es opcional.

### Lote 1 — GPS deja de ser mudo (I-1, I-3, I-5)

El más urgente: hoy apagar el GPS no produce ningún mensaje.

| # | Tarea | Archivo |
|---|---|---|
| A-1 | `GpsErrorCatalog` con los códigos `GPS-*` y los mensajes que **ya están escritos** en las ramas silenciosas | `GPS/Models/` (nuevo) |
| A-2 | Las 5 ramas silenciosas pasan a `ShowError` con botonera | `GpsOverlayViewModel.cs:95-118` |
| A-3 | Eliminar `Coordenadas` (sin consumidores, ver [N-1](#12-dos-hallazgos-nuevos-de-esta-verificación)) | `GpsOverlayViewModel.cs:15` |
| A-4 | Devolver la variante real en lugar de `Failure("")` | `GpsOverlayViewModel.cs:38` |
| A-5 | Corregir el comentario de `GpsCommandHandler` ([N-2](#12-dos-hallazgos-nuevos-de-esta-verificación)) | `GpsCommandHandler.cs:31` |

Catálogo propuesto — **los mensajes ya existen**, sólo hay que mostrarlos y darles una salida:

| Código | Título | Mensaje (el que ya está en el código) | Botonera |
|---|---|---|---|
| `GPS-OFF` | GPS desactivado | «El GPS está desactivado. Activalo desde ajustes.» | **Abrir ajustes de ubicación** · Reintentar · Cerrar |
| `GPS-UNSUPPORTED` | GPS no disponible | «Este dispositivo no soporta GPS.» | Cerrar |
| `GPS-NO-SIGNAL` | Sin señal GPS | «No se pudo obtener ubicación. Salí a un lugar despejado y reintentá.» | **Reintentar** · Cerrar |
| `GPS-CANCELLED` | *(ver [§6](#6-riesgos-y-decisiones-abiertas))* | — | — |
| `GPS-UNKNOWN` | No se pudo obtener la ubicación | «Ocurrió un problema al leer el GPS. Reintentá.» | **Reintentar** · Cerrar |

> `GPS-NO-SIGNAL` es el único mensaje que conviene **reescribir**: «(GPS sin señal)» describe la causa técnica; «Salí a un lugar despejado» describe la acción. Es el criterio de [07 §6](../Ejemplos_Maui_Devices-docs/docs/01-architecture/07-overlays-dispositivos.md#6-receta-agregar-un-dispositivo).
>
> `GPS-OFF` necesita un intent propio (`ActionLocationSourceSettings`), igual que «Activar Bluetooth» en impresión, y con la misma limitación: **abre el panel, no enciende el GPS**. Requiere verificación en dispositivo.

### Lote 2 — Telefonía deja de mostrar excepciones (I-1, I-3, I-4)

| # | Tarea | Archivo |
|---|---|---|
| B-1 | `CallErrorCatalog` con códigos `TEL-*` | `Phone/Models/` (nuevo) |
| B-2 | `Failure` clasificado en lugar de `f.Message` crudo | `CallOverlayViewModel.cs:123-127` |
| B-3 | Botón primario en la pantalla de permiso denegado | `CallOverlayViewModel.cs:73-75` |
| B-4 | Resolver `Cancelled`: pasar el token, o eliminar la variante ([§6](#6-riesgos-y-decisiones-abiertas)) | `CallOverlayViewModel.cs:34` |
| B-5 | Eliminar `Estado` (sin consumidores) | `CallOverlayViewModel.cs:19` |
| B-6 | Unificar `PedirPermiso` y `Reintentar`: hoy tienen **cuerpo idéntico** | `CallOverlayViewModel.cs:52-62` |

Y en GPS, el mismo I-4: botón primario en permiso denegado (`GpsOverlayViewModel.cs:62-64`).

### Lote 3 — Eliminar el código muerto estructural (I-2)

| # | Tarea | Archivos |
|---|---|---|
| C-1 | Eliminar el split `guard` + `MostrarResultado`: que el `switch` maneje `Success` y sea alcanzable | `GpsOverlayViewModel.cs:31-37`, `CallOverlayViewModel.cs:36-42` |
| C-2 | `DeviceFailure` en `Common/Models/`, generalizando `PrintFailure` | `Common/Models/` (nuevo) |

**C-2 es el único cambio de "armonización estructural"** y tiene un costo real: obliga a renombrar `PrintFailure` → `DeviceFailure` en impresión, que es **código que funciona y ya se probó en dispositivo**. Ver el trade-off en [§6](#6-riesgos-y-decisiones-abiertas).

### Lote 4 — Extensiones de la base (opcional)

| # | Tarea | Beneficio | Costo |
|---|---|---|---|
| D-1 | Sobrecarga `ShowBusy(..., params OverlayAction[])` + `BindableLayout` en la capa Busy del XAML | **Habilita cancelar una espera.** Impresión encadena 5 esperas no cancelables | Toca la base y el XAML: los 4 overlays |
| D-2 | `OverlayGlyph` con constantes en lugar de strings mágicos | Un glyph mal escrito hoy se ve como texto a 80 px | Bajo, mecánico |
| D-3 | Red: usar el `url` recibido o documentar por qué se sondea un host fijo (P-2) | El mensaje de DNS deja de nombrar un host que el usuario nunca visitó | Bajo |

> **D-1 es la extensión más valiosa pendiente del patrón** y la única que resuelve la causa de la deriva de Cámara/QR ([07 §8.7](../Ejemplos_Maui_Devices-docs/docs/01-architecture/07-overlays-dispositivos.md#87-la-deriva-de-los-que-quedaron-afuera)). También es la de mayor superficie. Se propone **por separado y al final**, no mezclada con las correcciones.

### 4.1 Fuera de alcance

| Qué | Por qué |
|---|---|
| Los overlays de **1ª generación** (`Ejemplo_Maui_GPS`, `Ejemplo_Maui_DirectCall`) | Son ejemplos aislados autocontenidos ([ADR-0001](../Ejemplos_Maui_Devices-docs/docs/04-decisions/0001-un-proyecto-por-tecnica.md)); su duplicación es deliberada |
| Migrar Cámara/QR al patrón | No aplica: necesitan hospedar contenido ([07 §7](../Ejemplos_Maui_Devices-docs/docs/01-architecture/07-overlays-dispositivos.md#7-los-límites-del-patrón)). Su deriva sólo se ataca con D-1 |
| Unificar el overlay manual de `MyMediaPickerPage` | Depende de D-1 |
| Suscribir `ErrorOccurred`/telemetría | Requiere definir destino de log; sin decidir |

---

## 5. Verificación de no-regresión

**Sin tests, la verificación es traza de consumidores + compilación + dispositivo.** Esta sección es el trabajo de verificación pedido: por cada superficie que el plan toca, quién depende de ella y por qué el cambio no la rompe.

### 5.1 Traza de consumidores — verificada, no supuesta

| Superficie que se toca | Consumidores reales | Evidencia | Veredicto |
|---|---|---|---|
| `GpsOverlayViewModel.Coordenadas` | **ninguno** | 0 lectores en `.cs`, 0 bindings en `.xaml` | **Eliminar es seguro** |
| `CallOverlayViewModel.Estado` | **ninguno** | ídem | **Eliminar es seguro** |
| `GpsOverlayViewModel.SolicitarGeolocalizacion()` | `GpsCommandHandler.cs:20` | Sólo evalúa `result is GpsResult.Success s` | **A-4 es seguro**: devolver la variante real no altera esa condición |
| `CallOverlayViewModel.LlamarAsync()` | `CallCommandHandler.cs:24` | Descarta el resultado (`_ = await`) | Cualquier cambio de retorno es inocuo |
| `PrinterOverlayViewModel.ImprimirAsync()` | `PrintCommandHandler.cs:86` | No inspecciona retorno (`Task`) | C-2 sólo afecta compilación interna |
| `NetworkOverlayViewModel.Notify*` | `MainViewModel.cs:102,104` | El plan **no toca** su firma | Sin impacto |
| `StatusOverlayViewModel.ShowBusy/ShowError` | 4 VMs de la híbrida + copia del Dialog | `grep` sobre la solución | D-1 es **aditivo** (ver 5.2) |
| `StatusOverlayView.xaml` | `MainPage.xaml` ×4 (`BindingContext`) | El plan no cambia propiedades bindeadas existentes | Sin impacto |

**Los overlays de 1ª generación tienen su propia base**: `Ejemplo_Maui_GPS` y `Ejemplo_Maui_DirectCall` declaran sus propios `ShowBusy`/`ShowError`. **No heredan de `Common`** y quedan inmunes a cualquier cambio de este plan. Verificado.

### 5.2 Por qué D-1 no rompe nada (el cambio de mayor superficie)

Es el único que toca la clase base y merece el argumento explícito.

1. **La sobrecarga es aditiva.** `ShowBusy(title, subtitle, image)` sigue existiendo con la misma firma y el mismo cuerpo. Las **9** llamadas actuales de la híbrida compilan y se comportan idéntico.
2. **El `Actions.Clear()` se conserva** en la sobrecarga sin acciones. Las esperas actuales siguen limpiando la botonera: sin cambio de comportamiento.
3. **El `BindableLayout` nuevo en la capa Busy renderiza `Actions`, que las llamadas actuales dejan vacía.** Una colección vacía no pinta nada: **cero cambio visual** en los 9 sitios existentes.
4. **La capa Busy hoy no tiene ningún `Button` ni `BindableLayout`** (verificado en `StatusOverlayView.xaml:21-28`): no hay nada que colisione.

El riesgo real de D-1 no es romper: es que **`StatusOverlayViewModel` está triplicado** (Common de la híbrida, copia del Dialog, y la 1ª generación). La copia del Dialog es **byte a byte idéntica** a la de Common — verificado con `diff` ignorando namespace. Si D-1 se aplica sólo en la híbrida, las dos divergen.

### 5.3 Cambios que sí alteran comportamiento observable — y es el objetivo

No todo cambio es neutro. Estos **cambian lo que el usuario ve**, deliberadamente:

| Cambio | Antes | Después | ¿Regresión? |
|---|---|---|---|
| A-2 | Apagar el GPS: overlay gira y desaparece, **sin mensaje** | Pantalla «GPS desactivado» con salida | **No**: es la corrección |
| B-2 | «No se pudo realizar la llamada» + texto de excepción | Mensaje en español + código `TEL-*` | **No** |
| A-4 | Devuelve `Failure("")` | Devuelve la variante real | **No**: el único consumidor sólo mira `Success` |
| C-1 | `case Success` inalcanzable escribe `Coordenadas` y llama `Hide()` | El `switch` maneja `Success` con `Hide()` | **No**: `Hide()` ya ocurría por el guard; la escritura no la leía nadie. **Comportamiento neto idéntico** |
| A-3 / B-5 | Propiedades observables sin lectores | No existen | **No**: nada las consumía |

**C-1 merece precisión**, porque es el que parece más riesgoso y no lo es. Hoy:

```csharp
if (result is GpsResult.Success) { Hide(); return result; }   // ← Hide() ocurre acá
MostrarResultado(result);                                     // ← el 'case Success' nunca corre
```

El `case Success:` muerto hace `Coordenadas = ...` (nadie lo lee) y `Hide()` (ya ocurrió). Eliminando el guard y dejando que el `switch` maneje `Success` con un `Hide()`, **el efecto observable es exactamente el mismo**: el overlay se oculta. La diferencia es que deja de haber código que miente.

### 5.4 Procedimiento de verificación

| # | Paso | Qué prueba | Qué NO prueba |
|---|---|---|---|
| 1 | `dotnet build -f net10.0-android -t:CoreCompile` en la híbrida y en el Dialog | Que compila | Nada del comportamiento |
| 2 | **Traza de I-2**: por cada variante, localizar su `new` y confirmar que se alcanza | Que no se agregó código muerto | — |
| 3 | **Traza de I-1**: por cada `case`, confirmar que termina en `ShowError` | Que nada quedó silencioso | — |
| 4 | **Prueba en dispositivo Android** de la matriz de 5.5 | El comportamiento real | — |

> El paso 2 no es burocracia: en la corrección de impresión **encontró dos defectos en el código nuevo** antes de llegar al dispositivo — un «Reintentar» que degradaba el diagnóstico y un arm inalcanzable en el catálogo. Es el paso con mejor relación costo/beneficio del procedimiento.
>
> **El paso 1 no prueba nada de esto.** Todos los defectos de este plan compilan sin una advertencia.

### 5.5 Matriz de prueba manual en dispositivo

Lo que la compilación no puede decir. **Es la única verificación real** y requiere hardware.

| Dominio | Escenario | Esperado |
|---|---|---|
| GPS | Apagar el GPS del teléfono → pedir ubicación | Pantalla «GPS desactivado» con «Abrir ajustes de ubicación» |
| GPS | Denegar el permiso definitivamente | «Abrir configuración» **destacado** frente a «Cerrar» |
| GPS | Interior sin señal | «Sin señal GPS» con «Reintentar» |
| GPS | **Camino feliz** | Overlay aparece, se oculta, la URL se reescribe con lat/lng |
| Telefonía | Denegar `CALL_PHONE` definitivamente | Primario destacado |
| Telefonía | Forzar un fallo de llamada | Mensaje en español + código, **sin texto de excepción** |
| Telefonía | **Camino feliz** | Marca sin overlay residual |
| Impresión | **Regresión completa**: imprimir OK, sin papel, BT apagado, predeterminada ausente | Idéntico a hoy — este plan no debe alterar impresión salvo por C-2 |
| Red | Apagar Wi-Fi con página cargada | Overlay «Sin conexión»; al volver, se destapa **sin recargar** |
| Red | Apagar Wi-Fi y navegar | Overlay; al volver, **recarga sola** |

> **La fila de impresión es la más importante del cuadro.** Impresión es el único dominio ya validado en dispositivo: si C-2 se aplica, hay que **reprobarlo entero**. Es el argumento central del trade-off de [§6](#6-riesgos-y-decisiones-abiertas).

---

## 6. Riesgos y decisiones abiertas

### 6.1 Decisión que bloquea el Lote 3

**¿Se generaliza `PrintFailure` → `DeviceFailure` en `Common` (C-2)?**

| Opción | Ventajas | Desventajas |
|---|---|---|
| **A. Generalizar** | Un solo tipo y una sola regla de `DisplayMessage` para los 4 dominios. Armonización real, no convención | Obliga a **tocar impresión, que ya funciona y se probó en dispositivo** → hay que reprobarla entera. Además **desincroniza** la híbrida del `Ejemplo_MotorDSL_Dialog`, que no tiene carpeta `Common` |
| **B. No generalizar** (recomendada) | No se toca impresión. Cada dominio tiene su catálogo; la armonización es de **convención** (mismo shape de 5 campos, mismos invariantes) | Tres records con la misma forma. Duplicación deliberada — coherente con [ADR-0001](../Ejemplos_Maui_Devices-docs/docs/04-decisions/0001-un-proyecto-por-tecnica.md) |

**Recomiendo B.** El valor de compartir un record de 5 campos no compensa reabrir el único dominio validado en hardware. Los invariantes de [§2](#2-los-cinco-invariantes) —que es lo que de verdad armoniza— **no requieren un tipo común**.

### 6.2 Decisión: `Cancelled` en GPS y Telefonía

`CallResult.Cancelled` y `GpsResult.Cancelled` son inalcanzables porque nadie pasa un `CancellationToken`. Dos salidas opuestas:

| Opción | Consecuencia |
|---|---|
| **Eliminar la variante** | Cumple I-2 hoy. Simple. Pierde el modelo de cancelación |
| **Hacerla alcanzable** | Exige exponer el token en el VM **y un botón «Cancelar» en la capa Busy → depende de D-1** |

Están acopladas: **hacer alcanzable `Cancelled` sin D-1 es imposible** — no hay dónde poner el botón. Si D-1 no se hace, la única opción coherente es eliminar la variante.

### 6.3 Riesgos

| # | Tipo | Riesgo |
|---|---|---|
| **R-1** | Hecho | **No hay tests.** Ninguna red de seguridad automatizada. La única verificación real es 5.5, en hardware, manual |
| **R-2** | Hecho | `StatusOverlayViewModel` está **triplicado** (Common híbrida, Dialog, 1ª generación). D-1 aplicado sólo en la híbrida los desincroniza |
| **R-3** | Hecho | `GPS-OFF` y el intent `ActionLocationSourceSettings` **no se pueden verificar desde el repositorio**; abre el panel pero no enciende el GPS. Mismo caso que «Activar Bluetooth» en impresión, que sigue **sin verificar en dispositivo** |
| **R-4** | Interpretación | Los códigos son un contrato con soporte: una vez que un usuario reporta un `GPS-OFF`, renombrarlo rompe el historial. Conviene fijarlos antes de publicar |
| **R-5** | Interpretación | Este plan corrige síntomas en tres dominios, pero **la causa es que nadie los probó en un teléfono**. Sin la matriz 5.5 ejecutada, es razonable esperar que reaparezcan |

---

## 7. Estrategia de test

Resuelve [R-1](#63-riesgos) («no hay tests»), que era el riesgo central del plan.

### 7.1 Viabilidad — verificada con un spike, no supuesta

La duda de fondo era si se puede testear este código **sin emulador ni dispositivo**. Tres hechos verificados:

| # | Hecho | Cómo se verificó |
|---|---|---|
| 1 | Los **5 ViewModels no tienen una sola directiva `#if`**: son platform-free. Todo el código de plataforma vive en los servicios | `grep -c "#if"` sobre los 5 archivos → 0 |
| 2 | `MotorDsl.Core` y `MotorDsl.Printing.Abstractions` son **`net10.0` plano**; `CommunityToolkit.Mvvm` es netstandard | `lib/` de los paquetes en la caché NuGet |
| 3 | Un proyecto `net10.0` + `<UseMaui>true</UseMaui>` **compila y ejecuta** contra `Location`, `PrinterDevice` y `PrintError` | Spike xUnit: 2/2 tests en verde |

El punto 3 era el riesgo real: los reference assemblies de MAUI en `net10.0` suelen lanzar `NotImplementedInReferenceAssembly` en runtime. **No aplica a `Location`**, que es un tipo de datos y no una llamada de plataforma. `Preferences`, `AppInfo` y `Permissions` **sí** lo harían — pero viven en los servicios, detrás de las interfaces del Lote 0, así que ningún test los toca.

### 7.2 Diseño

| Decisión | Elección | Por qué |
|---|---|---|
| Ubicación | `Ejemplos_Devices/Integrada/Ejemplo_Maui_Hibrida.Tests/` | Junto a la app que prueba |
| Target | `net10.0` + `UseMaui` | Corre en el runner de escritorio; sin emulador |
| Acceso al código | **Linkeo de fuentes** (`<Compile Include="..." />`) | La app es `net10.0-android`: un proyecto `net10.0` **no puede referenciarla**. Linkear evita extraer `LibApp/` a una librería, que contradiría [ADR-0001](../Ejemplos_Maui_Devices-docs/docs/04-decisions/0001-un-proyecto-por-tecnica.md) |
| Qué se linkea | Base + Models + ViewModels + interfaces | Lo platform-free |
| Qué **no** se linkea | Los servicios concretos | Tienen `#if ANDROID` y estáticos de MAUI |
| Dobles | Fakes escritos a mano | Sin librería de mocking: las interfaces son de 3-6 métodos |

**El Lote 0 (interfaces) es prerequisito de todo.** Hoy los VMs dependen de servicios **concretos** (`PrinterService`, `GpsService`, `CallService`, `NetworkService`), así que no hay costura por donde inyectar un doble. La excepción es `NetworkOverlayViewModel`, que ya recibe un `IWebViewBridge`: la costura que falta es la del servicio.

### 7.3 Qué se testea — los invariantes, ejecutables

La suite es **la versión automatizada de la traza manual** de [§5](#5-verificación-de-no-regresión). Cada invariante de [§2](#2-los-cinco-invariantes) se vuelve un test:

| Invariante | Test |
|---|---|
| **I-1** | Por cada variante no-`Success`: tras procesarla, `Mode == Error` y `Actions` no vacía. **Un `case` que sólo hace `Hide()` falla el test** |
| **I-2** | Recorrido por reflexión de las variantes anidadas del `abstract record`: cada una debe tener su pantalla. **Agregar una variante sin pantalla rompe la suite** |
| **I-3** | El `Message` que llega a la UI **no contiene** el texto técnico de origen (`paper out`, el `ex.Message`) |
| **I-4** | Por cada `ShowError`: **exactamente un** `OverlayAction` con `Style == Primary` |
| **I-5** | El VM devuelve **la misma variante** que le dio el servicio |

Más el catálogo (`Describe(PrinterHardwareException("paper out")) → PRN-HW-PAPER`, etc.) y la base (`ShowBusy` limpia `Actions`; `Hide` deja `None`).

> **I-2 por reflexión es el test más valioso de la suite.** Es la única defensa automatizable contra el defecto que costó más caro: modelar una variante y no producirla. C# no lo verifica; el test sí.

### 7.4 Orden de trabajo — los tests primero

**Se escriben los tests antes de las correcciones.** Consecuencia buscada:

| Dominio | Al escribir la suite | Después de los lotes 1-3 |
|---|---|---|
| Impresión | **Verde** | Verde ← *red de no-regresión* |
| Red | **Verde** | Verde |
| GPS | **Rojo** (I-1 ×5, I-3, I-4, I-5) | Verde |
| Telefonía | **Rojo** (I-1, I-3, I-4) | Verde |

Los rojos iniciales **son el entregable**: convierten los defectos de [§3](#3-matriz-de-conformidad) de una tabla en un documento a una falla ejecutable. Y los verdes de impresión son exactamente la verificación de no-regresión que [§5.5](#55-matriz-de-prueba-manual-en-dispositivo) sólo podía dar a mano.

### 7.5 Qué esta suite NO prueba

Honestidad sobre el alcance: **no reemplaza la matriz de [§5.5](#55-matriz-de-prueba-manual-en-dispositivo)**.

| No cubre | Por qué | Sigue necesitando |
|---|---|---|
| Los servicios concretos | Tienen `#if ANDROID` y estáticos de MAUI | Dispositivo |
| Los intents (`ActionBluetoothSettings`, `ActionLocationSourceSettings`) | Sólo existen en Android | Dispositivo ([R-3](#63-riesgos)) |
| El binding XAML → que el mensaje se **vea** | El test verifica el estado del VM, no el píxel | Dispositivo |
| Que un glyph exista en la fuente | Un glyph inválido se ve como texto | Dispositivo ([O-2](../Ejemplos_Maui_Devices-docs/docs/01-architecture/07-overlays-dispositivos.md#9-observaciones)) |

La suite prueba que **el ViewModel decide bien**. Que eso llegue a la pantalla sigue siendo verificación manual.

---

## 8. Resultado de la ejecución

### 8.1 Matriz de conformidad — después

| Invariante | Impresión | GPS | Telefonía | Red |
|---|---|---|---|---|
| **I-1** · toda variante muestra algo | ✔ | ✔ | ✔ | ✔ |
| **I-2** · toda variante es alcanzable | ✔ | ✔ | ✔ | ✔ |
| **I-3** · nada crudo al usuario | ✔ | ✔ | ✔ | ✔ |
| **I-4** · un botón primario | ✔ | ✔ | ✔ | ✔ |
| **I-5** · no colapsa el resultado | ✔ | ✔ | ✔ | ✔ |

**Suite: 116/116.** Arrancó en 34 rojos (GPS 21, Telefonía 7, Impresión 3, Red 3).

### 8.2 Lo que la ejecución corrigió del propio plan

Tres cosas que el plan afirmaba y resultaron falsas. Las tres las encontró la suite, no la lectura.

| # | El plan decía | La realidad |
|---|---|---|
| **E-1** | «Impresión cumple los cinco invariantes» ([§3](#3-matriz-de-conformidad)) | **Falso para I-4.** Sus pantallas de acción única declaraban «Cerrar» como `Secondary`: 0 primarios. Igual en GPS, Telefonía y el `Ejemplo_MotorDSL_Dialog`. La matriz hecha a mano no lo vio; el test sí |
| **E-2** | «Los 5 ViewModels son platform-free» ([§7.1](#71-viabilidad--verificada-con-un-spike-no-supuesta)) | **Incompleto.** El chequeo fue `grep -c "#if"` → 0, pero `NetworkOverlayViewModel` llamaba a `MainThread.BeginInvokeOnMainThread` y a `AppInfo.ShowSettingsUI()`. **`MainThread` es un estático de plataforma sin `#if`**: lanzaba `NotImplementedInReferenceAssemblyException` en el runner. El spike validó `Location` y no esto |
| **E-3** | `Cancelled` sólo podía resolverse con D-1 ([§6.2](#62-decisión-cancelled-en-gps-y-telefonía)) | **Falso.** Se resolvió dándole pantalla propia con «Reintentar». D-1 haría falta sólo para *disparar* la cancelación desde la UI, no para comunicarla |

**E-2 es la más instructiva**: «no tiene `#if`» no equivale a «no toca la plataforma». Los estáticos de MAUI (`MainThread`, `AppInfo`, `Preferences`, `Permissions`) compilan en cualquier target y fallan en runtime. El criterio correcto es el que quedó verificado en 9.4.

### 8.3 Archivos tocados

| Archivo | Cambio |
|---|---|
| `{GPS,Phone,Networks,MotorDSL}/Services/I*Service.cs` | **Nuevos** — costuras. Prerequisito de la suite |
| `Common/Services/IUiDispatcher.cs` | **Nuevo** — abstrae `MainThread` (E-2) |
| `GPS/Models/GpsFailure.cs` · `GpsErrorCatalog.cs` | **Nuevos** — códigos `GPS-*` |
| `Phone/Models/CallFailure.cs` (+ `CallErrorCatalog`) | **Nuevo** — códigos `TEL-*` |
| `GPS/ViewModels/GpsOverlayViewModel.cs` | Reescrito: 5 ramas mudas → pantallas; sin guard muerto; sin `Coordenadas`; devuelve la variante real |
| `Phone/ViewModels/CallOverlayViewModel.cs` | Reescrito: `Failure` clasificado; sin guard muerto; sin `Estado` |
| `Networks/ViewModels/NetworkOverlayViewModel.cs` | `IUiDispatcher`; ajustes vía servicio; guarda la URL |
| `Networks/Services/NetworkService.cs` | **P-2 corregido**: reporta el host del sitio, no el de la sonda |
| `GPS/Services/GpsService.cs` | `OpenLocationSettings()`; eliminados `CheckAsync`/`Map` (sin llamadores) |
| `MotorDSL/ViewModels/PrinterOverlayViewModel.cs` | I-4: helper `Cerrar(unico)` |
| `Printer/Ejemplo_MotorDSL_Dialog/ViewModels/PrinterOverlayViewModel.cs` | I-4, replicado |
| `MauiProgram.cs` | DI por interfaz |
| `Ejemplo_Maui_Hibrida.Tests/` | **Nuevo proyecto** — 116 tests |

### 8.4 Verificación ejecutada

| # | Verificación | Resultado |
|---|---|---|
| 1 | `dotnet test` | **116/116** |
| 2 | `CoreCompile` de la híbrida y del Dialog | 0 errores |
| 3 | Regresión de impresión (28 tests: predeterminada, homónimas, papel, H-6) | Verde antes y después |
| 4 | `Coordenadas` / `Estado` eliminadas | Sin ocurrencias |
| 5 | Estáticos de MAUI en los ViewModels | Ninguno (sólo una mención en un comentario) |

### 8.5 Lo que sigue pendiente

| # | Pendiente |
|---|---|
| **V-1** | **La matriz de [§5.5](#55-matriz-de-prueba-manual-en-dispositivo) no se ejecutó.** La suite prueba que el ViewModel decide bien; que eso llegue a la pantalla requiere un teléfono |
| **V-2** | `ActionLocationSourceSettings` (GPS-OFF) y `ActionBluetoothSettings` **siguen sin verificar en dispositivo** |
| **V-3** | Los glyphs nuevos no se verificaron contra la fuente: uno inválido se ve como texto a 80 px |
| **V-4** | **D-1 no se hizo**: la capa Busy sigue sin admitir botones y toda espera sigue siendo no-cancelable. Es la deuda estructural del patrón |
| **V-5** | El `Ejemplo_MotorDSL_Dialog` recibió sólo la corrección de I-4: su VM diverge más de la híbrida (usa el tipo concreto, sin costura) |

---

## 9. Referencias

- Fundamento del patrón y anti-patrones: [07-overlays-dispositivos](../Ejemplos_Maui_Devices-docs/docs/01-architecture/07-overlays-dispositivos.md)
- Catálogo de pantallas y los defectos P-1…P-9: [08-pantallas-por-dispositivo](../Ejemplos_Maui_Devices-docs/docs/01-architecture/08-pantallas-por-dispositivo.md)
- Precedente: la corrección equivalente en impresión — `Librerias/PrintThermal_Motor_Maui.Documentacion/Analisis/Plan-Correcciones-UX-Impresion.md`
- ADRs: [0001](../Ejemplos_Maui_Devices-docs/docs/04-decisions/0001-un-proyecto-por-tecnica.md) · [0002](../Ejemplos_Maui_Devices-docs/docs/04-decisions/0002-servicio-tipado-overlay-mvvm.md)
- Fuentes verificadas — `Ejemplos_Devices/Integrada/Ejemplo_Maui_Hibrida/`:
  - `LibApp/Devices/GPS/ViewModels/GpsOverlayViewModel.cs` · `LibApp/UrlCommands/Handlers/GpsCommandHandler.cs`
  - `LibApp/Devices/Phone/ViewModels/CallOverlayViewModel.cs` · `LibApp/UrlCommands/Handlers/CallCommandHandler.cs`
  - `LibApp/Devices/Common/ViewModels/StatusOverlayViewModel.cs` · `LibApp/Devices/Common/Controls/StatusOverlayView.xaml`
