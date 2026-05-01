# Async/Await Profundo: State Machine, Trampas y Buenas Practicas

**Tiempo estimado:** ~1.5 horas
**Prerequisitos:** C# basico, familiaridad con metodos y tipos de retorno
**Target:** .NET 10 LTS (todos los snippets compilan en .NET 10)

---

## El Compilador y la State Machine

Cuando escribes `async` y `await`, el compilador de C# transforma tu metodo en una **state machine** — una clase interna que implementa `IAsyncStateMachine`. Esta clase tiene un campo de estado (un `int`) que trackea en que punto del metodo te encuentras. Cada `await` es un **punto de suspension**: el hilo actual se libera y, cuando la operacion async completa, la ejecucion se reanuda desde el estado correcto.

Este modelo mental es fundamental. Sin el, respuestas como *"async/await no bloquea el hilo"* son correctas pero superficiales — y los entrevistadores profundizan.

### Lo que el desarrollador escribe

```csharp
public async Task<string> FetchDataAsync(string url)
{
    using var client = new HttpClient();
    string data = await client.GetStringAsync(url);  // punto de suspension 1
    string processed = await ProcessAsync(data);      // punto de suspension 2
    return processed.ToUpper();
}
```

### Lo que el compilador genera (pseudocodigo simplificado)

```csharp
// El compilador crea una struct interna que implementa IAsyncStateMachine
private struct FetchDataAsyncStateMachine : IAsyncStateMachine
{
    public int State; // -1: inicio, 0: esperando GetStringAsync, 1: esperando ProcessAsync
    public string Url; public HttpClient Client; public string Data;
    public AsyncTaskMethodBuilder<string> Builder;

    public void MoveNext()
    {
        switch (State)
        {
            case -1: // ejecuta hasta el primer await
                Client = new HttpClient();
                var task1 = Client.GetStringAsync(Url);
                if (!task1.IsCompleted)
                {
                    State = 0;
                    Builder.AwaitUnsafeOnCompleted(ref awaiter1, ref this);
                    return; // RETORNA AL CALLER — el hilo NO esta bloqueado
                }
                goto case 0;
            case 0: // GetStringAsync completo
                Data = awaiter1.GetResult();
                var task2 = ProcessAsync(Data);
                if (!task2.IsCompleted) { State = 1; Builder.AwaitUnsafeOnCompleted(ref awaiter2, ref this); return; }
                goto case 1;
            case 1: // ProcessAsync completo
                Builder.SetResult(awaiter2.GetResult().ToUpper()); return;
        }
    }
}
```

**Lo esencial:**

1. Cada `await` divide el metodo en estados (state 0, 1, 2...).
2. Si la operacion await-eada ya completo (`IsCompleted == true`), la state machine continua sin suspenderse — esto se llama el **hot path**.
3. Si la operacion NO completo, la state machine registra un callback y **retorna al caller**. El hilo queda libre para hacer otro trabajo.
4. Cuando la operacion completa, el callback invoca `MoveNext()` en el estado correcto, reanudando la ejecucion.

> **Herramienta:** pega un metodo async en [SharpLab.io](https://sharplab.io/) y selecciona "C# -> C#" para ver la state machine real que genera el compilador.

### Challenge 1: Identifica los puntos de suspension

Dado el siguiente metodo async, responde:
1. En que puntos exactos se suspende la ejecucion?
2. Cuantos estados tiene la state machine generada?
3. Si `ValidateOrderAsync` retorna un resultado cacheado (completa sincronamente), el hilo se suspende en ese punto?

```csharp
public async Task<OrderResult> ProcessOrderAsync(int orderId, CancellationToken ct)
{
    var order = await GetOrderAsync(orderId, ct);          // linea A
    var isValid = await ValidateOrderAsync(order, ct);     // linea B
    if (!isValid) return new OrderResult(false, "Invalid");

    var receipt = await ChargePaymentAsync(order, ct);     // linea C
    return new OrderResult(true, receipt.Id);
}
```

<details>
<summary>Ver solucion</summary>

**Puntos de suspension:** lineas A, B y C — cada `await` es un punto potencial de suspension.

**Estados de la state machine:** 4 estados:
- Estado -1: inicio, ejecuta hasta linea A
- Estado 0: esperando `GetOrderAsync`, al completar ejecuta hasta linea B
- Estado 1: esperando `ValidateOrderAsync`, al completar ejecuta hasta linea C (o retorna si `isValid` es false)
- Estado 2: esperando `ChargePaymentAsync`, al completar retorna el resultado

**Si `ValidateOrderAsync` completa sincronamente:** NO se suspende. La state machine verifica `task.IsCompleted` antes de suspender. Si el resultado ya esta disponible (por ejemplo, un cache en memoria), la ejecucion continua inmediatamente en el mismo hilo sin registrar callback. Este es el **hot path** — la state machine hace un `goto` al siguiente estado.

**Puntos clave:**
- "Punto de suspension" no significa que siempre se suspende — solo que *puede* suspenderse.
- El hilo que reanuda la ejecucion despues de suspenderse puede ser diferente al original (en ASP.NET Core, cualquier thread pool thread).
- La state machine tiene N+1 estados, donde N es el numero de `await` en el metodo.

</details>

---

## Task vs ValueTask

`Task<T>` y `ValueTask<T>` son los dos tipos de retorno para metodos async. La diferencia es como se asignan en memoria y cuando conviene cada uno.

### Task: allocacion en heap

```csharp
// Task<T> siempre crea un objeto en el heap
public async Task<int> GetCountAsync()
{
    return await _repository.CountAsync(); // allocates un Task<int> en heap
}
```

Cada llamada a un metodo que retorna `Task<T>` crea un objeto en el heap, incluso si el resultado ya estaba disponible. En la mayoria de los casos esto no importa — el costo es minimo. Pero en **hot paths** que se ejecutan miles de veces por segundo, la presion en el garbage collector puede ser notable.

### ValueTask: struct, sin allocacion en el sync path

```csharp
// ValueTask<T> es un struct — evita allocacion cuando el resultado ya esta disponible
public ValueTask<int> GetCachedCountAsync()
{
    if (_cache.TryGetValue("count", out int cached))
        return new ValueTask<int>(cached);  // sin allocacion: retorna el valor directamente

    return new ValueTask<int>(FetchCountFromDbAsync()); // solo alloca si realmente es async
}
```

### Cuando usar cada uno

| Criterio | `Task<T>` | `ValueTask<T>` |
|----------|-----------|-----------------|
| El resultado casi siempre requiere I/O | Si | No |
| El resultado a menudo esta en cache | No | Si |
| Se va a await mas de una vez | Si | NUNCA |
| Interfaz publica de libreria | Seguro | Requiere cuidado |
| Default para metodos async | Si | Solo para optimizacion |

**Regla critica:** NUNCA hagas await de un `ValueTask<T>` mas de una vez. No esta disenado para eso — el comportamiento es indefinido.

```csharp
// INCORRECTO: await de ValueTask mas de una vez
var valueTask = GetCachedCountAsync();
var a = await valueTask;  // primera vez: ok
var b = await valueTask;  // SEGUNDA VEZ: comportamiento indefinido!

// CORRECTO: si necesitas el valor multiples veces
var result = await GetCachedCountAsync();
// usa 'result' las veces que necesites
```

> **Pregunta de entrevista:** *"Cuando usarias ValueTask en vez de Task?"*
> Cuando el metodo completa sincronamente con frecuencia (ej: cache hit) y esta en un hot path. El default es Task; ValueTask es una optimizacion con restricciones.

---

## ConfigureAwait(false)

Cuando haces `await`, por defecto la ejecucion intenta reanudar en el mismo **SynchronizationContext** (contexto de sincronizacion) que tenia antes del `await`. En .NET Framework con ASP.NET, esto significaba volver al mismo hilo de la request — y ahi es donde nacian los deadlocks.

### La diferencia clave: ASP.NET Core no tiene contexto de sincronizacion

En **ASP.NET Core**, Microsoft elimino ese contexto. Despues de un `await`, la ejecucion se reanuda en cualquier thread pool thread disponible. Por eso `.Result` no causa deadlock en ASP.NET Core (aunque sigue siendo mala practica).

En **librerias** que no saben en que contexto seran usadas, la regla es clara:

```csharp
// En una libreria: siempre ConfigureAwait(false)
public async Task<WeatherData> GetWeatherAsync(string city)
{
    using var client = new HttpClient();
    var json = await client.GetStringAsync($"https://api.example.com/weather/{city}")
        .ConfigureAwait(false);  // no captura el contexto de sincronizacion

    var parsed = await JsonSerializer.DeserializeAsync<WeatherData>(
        new MemoryStream(Encoding.UTF8.GetBytes(json)))
        .ConfigureAwait(false);  // en CADA await de la libreria

    return parsed!;
}
```

### Regla practica

| Contexto | Usar ConfigureAwait(false)? |
|----------|----------------------------|
| Codigo de libreria (NuGet package) | Si, siempre en cada await |
| Aplicacion ASP.NET Core | No es necesario (no hay contexto de sincronizacion) |
| Aplicacion WPF / WinForms | Solo en codigo que no toca UI |
| Codigo compartido libreria + app | Si, para ser seguro |

> **Nota .NET 8+:** Microsoft recomienda que las librerias sigan usando `ConfigureAwait(false)` incluso en .NET 10, porque la libreria no controla quien la consume — podria ser una app WPF que si tiene contexto de sincronizacion.

### Challenge 2: Prepara un servicio para publicarlo como NuGet

El siguiente servicio de notificaciones funciona perfectamente en tu app ASP.NET Core. Pero ahora necesitas extraerlo a un paquete NuGet para que otros equipos lo usen (podrian estar en WPF, WinForms, o ASP.NET Core). Agrega `ConfigureAwait(false)` donde corresponda y explica por que en cada caso.

```csharp
public class NotificationService
{
    private readonly HttpClient _httpClient;

    public NotificationService(HttpClient httpClient) => _httpClient = httpClient;

    public async Task SendEmailAsync(string to, string subject, string body)
    {
        var payload = new { to, subject, body };
        var content = new StringContent(
            JsonSerializer.Serialize(payload), Encoding.UTF8, "application/json");

        var response = await _httpClient.PostAsync("https://api.example.com/email", content);
        response.EnsureSuccessStatusCode();

        var log = await response.Content.ReadAsStringAsync();
        Console.WriteLine($"Email sent: {log}");
    }

    public async Task<bool> SendSmsAsync(string phone, string message)
    {
        var response = await _httpClient.GetAsync(
            $"https://api.example.com/sms?phone={phone}&msg={Uri.EscapeDataString(message)}");

        return response.IsSuccessStatusCode;
    }

    public async Task SendBulkAsync(IEnumerable<string> recipients, string message)
    {
        foreach (var recipient in recipients)
        {
            await SendEmailAsync(recipient, "Notification", message);
        }
    }
}
```

<details>
<summary>Ver solucion</summary>

```csharp
public class NotificationService
{
    private readonly HttpClient _httpClient;

    public NotificationService(HttpClient httpClient) => _httpClient = httpClient;

    public async Task SendEmailAsync(string to, string subject, string body)
    {
        var payload = new { to, subject, body };
        var content = new StringContent(
            JsonSerializer.Serialize(payload), Encoding.UTF8, "application/json");

        var response = await _httpClient.PostAsync("https://api.example.com/email", content)
            .ConfigureAwait(false);   // 1. Llamada HTTP — el punto mas critico
        response.EnsureSuccessStatusCode();

        var log = await response.Content.ReadAsStringAsync()
            .ConfigureAwait(false);   // 2. Lectura del body — tambien es I/O
        Console.WriteLine($"Email sent: {log}");
    }

    public async Task<bool> SendSmsAsync(string phone, string message)
    {
        var response = await _httpClient.GetAsync(
            $"https://api.example.com/sms?phone={phone}&msg={Uri.EscapeDataString(message)}")
            .ConfigureAwait(false);   // 3. Unica llamada async del metodo

        return response.IsSuccessStatusCode;
    }

    public async Task SendBulkAsync(IEnumerable<string> recipients, string message)
    {
        foreach (var recipient in recipients)
        {
            await SendEmailAsync(recipient, "Notification", message)
                .ConfigureAwait(false);   // 4. Llamada a otro metodo async de la misma clase
        }
    }
}
```

**Puntos clave:**
- **Cada** `await` en una libreria necesita `ConfigureAwait(false)` — no solo el primero. Despues del primer `await` sin `ConfigureAwait(false)`, ya capturaste el contexto.
- El punto 4 es facil de olvidar: incluso llamadas a metodos propios dentro de la misma clase necesitan `ConfigureAwait(false)` porque el caller de `SendBulkAsync` podria estar en un contexto con sincronizacion (WPF, WinForms).
- En tu app ASP.NET Core esto funcionaba sin `ConfigureAwait(false)` porque no tiene contexto de sincronizacion. Pero como NuGet package, no sabes quien lo consume.

</details>

---

## CancellationToken

`CancellationToken` es el mecanismo estandar de .NET para **cancelar operaciones async cooperativamente**. "Cooperativamente" significa que el codigo que se cancela debe verificar activamente si se pidio la cancelacion — no se mata el hilo por la fuerza.

### El patron basico

```csharp
// 1. Crear un CancellationTokenSource (el "control remoto" para cancelar)
using var cts = new CancellationTokenSource(TimeSpan.FromSeconds(10)); // timeout de 10s

// 2. Obtener el token (la "senal" que reciben los metodos)
CancellationToken token = cts.Token;

// 3. Pasar el token a toda la cadena de llamadas
try
{
    var result = await ProcessOrderAsync(orderId, token);
    Console.WriteLine($"Order processed: {result}");
}
catch (OperationCanceledException)
{
    // 4. Manejar la cancelacion — no es un "error", es un flujo esperado
    Console.WriteLine("Operation cancelled (timeout or manual cancellation)");
}
```

### Propagar el token a toda la cadena

La regla mas importante: **si un metodo recibe `CancellationToken`, debe pasarlo a cada llamada async interna**. Ignorar el token silenciosamente es un bug sutil — la operacion no se cancela cuando deberia.

```csharp
public async Task<OrderReceipt> ProcessOrderAsync(int orderId, CancellationToken ct)
{
    // Pasar el token a cada operacion async
    var order = await _repository.GetByIdAsync(orderId, ct);

    // Verificar cancelacion entre operaciones CPU-bound
    ct.ThrowIfCancellationRequested();

    var validated = ValidateOrder(order); // operacion sincrona

    // Pasar a HttpClient, EF Core, etc. — todos aceptan CancellationToken
    var receipt = await _paymentGateway.ChargeAsync(order.Total, ct);

    await _notifier.SendConfirmationAsync(order.CustomerEmail, receipt, ct);

    return receipt;
}
```

### OperationCanceledException vs otras excepciones

```csharp
try { await ProcessOrderAsync(orderId, ct); }
catch (OperationCanceledException) when (!ct.IsCancellationRequested)
{
    _logger.LogWarning("Internal timeout");  // timeout propio, no del caller
    throw;
}
catch (OperationCanceledException)
{
    _logger.LogInformation("Cancelled by user"); // flujo normal, no error
}
catch (Exception ex)
{
    _logger.LogError(ex, "Processing failed"); // error real
    throw;
}
```

### Challenge 3: Servicio de reporte con timeout y cancelacion

Implementa un servicio que genera un reporte descargando datos de 3 APIs externas. Si alguna API tarda mas de 5 segundos, debe cancelar todas las operaciones pendientes y retornar un resultado parcial con los datos que ya tenia.

Requisitos:
- Acepta un `CancellationToken` externo (del caller)
- Crea su propio timeout de 5 segundos usando `CancellationTokenSource.CreateLinkedTokenSource`
- Descarga de las 3 APIs en paralelo con `Task.WhenAll`
- Si una falla por timeout, las otras se cancelan tambien
- Retorna los resultados parciales que si completaron

```csharp
public record ReportData(string Source, string Data);
public record ReportResult(List<ReportData> Completed, List<string> Failed);

// Simula llamadas a APIs externas
private async Task<ReportData> FetchFromApiAsync(string apiName, string url, CancellationToken ct)
{
    using var client = new HttpClient();
    var response = await client.GetStringAsync(url, ct);
    return new ReportData(apiName, response);
}

// Tu implementacion aqui
public async Task<ReportResult> GenerateReportAsync(CancellationToken callerToken)
{
    // ...
}
```

<details>
<summary>Ver solucion</summary>

```csharp
public async Task<ReportResult> GenerateReportAsync(CancellationToken callerToken)
{
    // Linked token: se cancela si el caller cancela O si pasan 5 segundos
    using var timeoutCts = new CancellationTokenSource(TimeSpan.FromSeconds(5));
    using var linkedCts = CancellationTokenSource.CreateLinkedTokenSource(
        callerToken, timeoutCts.Token);
    var ct = linkedCts.Token;

    var apis = new[]
    {
        ("Sales", "https://api.example.com/sales"),
        ("Inventory", "https://api.example.com/inventory"),
        ("Customers", "https://api.example.com/customers")
    };

    // Lanzar las 3 descargas en paralelo
    var tasks = apis.Select(api =>
        FetchWithFallbackAsync(api.Item1, api.Item2, ct)).ToList();

    // Esperar a que TODAS completen (exitosas o fallidas)
    var results = await Task.WhenAll(tasks);

    var completed = results.Where(r => r.Data is not null).ToList();
    var failed = results.Where(r => r.Data is null).Select(r => r.Source).ToList();

    return new ReportResult(completed, failed);
}

private async Task<ReportData> FetchWithFallbackAsync(
    string apiName, string url, CancellationToken ct)
{
    try
    {
        return await FetchFromApiAsync(apiName, url, ct);
    }
    catch (OperationCanceledException) when (!ct.IsCancellationRequested)
    {
        return new ReportData(apiName, null!); // timeout interno — retornar resultado parcial
    }
    // Si ct fue cancelado por el caller, la excepcion se propaga (no se captura)
    catch (HttpRequestException)
    {
        return new ReportData(apiName, null!); // error de red, tambien fallido
    }
}
```

**Puntos clave:**
- `CreateLinkedTokenSource` combina multiples tokens — se cancela si *cualquiera* de los tokens se activa. Ideal para combinar timeout propio + cancelacion del caller.
- `Task.WhenAll` espera a que todas las tasks completen, pero si el token se cancela, las tasks pendientes reciben la senal de cancelacion y lanzan `OperationCanceledException`.
- El wrapper `FetchWithFallbackAsync` captura la excepcion de cancelacion por cada API individualmente, permitiendo retornar resultados parciales en vez de fallar completamente.
- Siempre hacer `Dispose` de `CancellationTokenSource` (via `using`) para liberar los timers internos.

</details>

---

## Trampas Comunes

Las tres trampas async mas frecuentes en entrevistas y codigo de produccion. Cada una parece inocua pero puede causar bugs severos.

### Trampa 1: async void

Un metodo `async void` no retorna `Task`, por lo que **no se puede hacer await** y las excepciones **se pierden silenciosamente** (o crashean el proceso).

```csharp
// INCORRECTO: async void — excepciones se pierden
public async void SendWelcomeEmail(string email)
{
    await _emailService.SendAsync(email, "Welcome!"); // si esto falla...
    // la excepcion se propaga al contexto de sincronizacion, no al caller
    // en ASP.NET Core: crash del proceso. El caller NUNCA se entera.
}

// CORRECTO: async Task — el caller puede hacer await y capturar excepciones
public async Task SendWelcomeEmailAsync(string email)
{
    await _emailService.SendAsync(email, "Welcome!");
}
```

**Unico uso valido de `async void`:** event handlers en WPF/WinForms, porque la firma del evento es `void`:

```csharp
// Event handler: async void es aceptable aqui (no hay opcion)
private async void Button_Click(object sender, EventArgs e)
{
    await LoadDataAsync();
}
```

### Trampa 2: .Result y .Wait()

Llamar `.Result` o `.Wait()` en un Task **bloquea el hilo actual** hasta que el Task complete. En contextos con sincronizacion (legacy ASP.NET, WPF, WinForms), esto causa **deadlock**: el hilo bloqueado es el mismo que el Task necesita para reanudar.

```csharp
// INCORRECTO: .Result bloquea el hilo
// En legacy ASP.NET: DEADLOCK (el hilo bloqueado es el que el Task necesita para reanudar)
// En ASP.NET Core: funciona, pero desperdicia un thread pool thread
public IActionResult GetData()
{
    var data = _service.GetDataAsync().Result; // bloquea hasta que complete
    return Ok(data);
}

// CORRECTO: siempre usar await
public async Task<IActionResult> GetData()
{
    var data = await _service.GetDataAsync(); // no bloquea ningun hilo
    return Ok(data);
}
```

### Trampa 3: Task.Run innecesario

`Task.Run` ejecuta codigo en un thread pool thread. Si el codigo que envuelves **ya es async**, estas consumiendo un hilo adicional sin ningun beneficio.

```csharp
// INCORRECTO: Task.Run wrapping codigo ya async — presion innecesaria en thread pool
public async Task SendNotificationAsync(string message)
{
    await Task.Run(async () => await _emailService.SendAsync(message)); // ya es async!
}

// CORRECTO: llamar directamente sin Task.Run
public async Task SendNotificationAsync(string message)
{
    await _emailService.SendAsync(message); // sin Task.Run: no consume thread extra
}

// CUANDO SI usar Task.Run: trabajo CPU-bound pesado
public async Task<byte[]> ResizeImageAsync(byte[] original, int w, int h)
{
    return await Task.Run(() => ImageProcessor.Resize(original, w, h));
}
```

### Challenge 4: Encuentra y corrige los 3 problemas async

El siguiente servicio de notificaciones tiene **tres problemas async clasicos**. Identificalos y corrige cada uno explicando por que es un problema.

```csharp
public class AlertService
{
    private readonly IEmailSender _emailSender;
    private readonly ISmsSender _smsSender;
    private readonly ITemplateEngine _templateEngine;

    public AlertService(IEmailSender emailSender, ISmsSender smsSender,
        ITemplateEngine templateEngine)
    {
        _emailSender = emailSender;
        _smsSender = smsSender;
        _templateEngine = templateEngine;
    }

    public async void SendAlertToAll(IEnumerable<string> recipients, string alertType)
    {
        foreach (var recipient in recipients)
        {
            var template = _templateEngine.RenderAsync(alertType).Result;
            await Task.Run(() => _emailSender.SendAsync(recipient, template));
        }
    }

    public async Task NotifyAsync(string recipient, string message)
    {
        await _emailSender.SendAsync(recipient, message);
        await _smsSender.SendAsync(recipient, message);
    }
}
```

<details>
<summary>Ver solucion</summary>

```csharp
// Solo se muestra el metodo corregido:
public async Task SendAlertToAllAsync(IEnumerable<string> recipients, string alertType)
{
    foreach (var recipient in recipients)
    {
        var template = await _templateEngine.RenderAsync(alertType);  // FIX 2: await
        await _emailSender.SendAsync(recipient, template);            // FIX 3: sin Task.Run
    }
}
// FIX 1: firma cambiada de async void a async Task + sufijo Async
```

**Puntos clave:**
- **Bug 1 — `async void`:** el caller no puede hacer `await` ni capturar excepciones. La excepcion se pierde o crashea el proceso. Solucion: `async Task`.
- **Bug 2 — `.Result`:** bloquea el hilo. En legacy ASP.NET causa deadlock. Solucion: `await`.
- **Bug 3 — `Task.Run`:** `SendAsync` ya es async; wrapearlo consume un thread pool thread extra sin beneficio. Solucion: `await` directo.

</details>

### Challenge 5: Descarga paralela con limite de concurrencia

Implementa un servicio que descarga N archivos en paralelo con un limite de concurrencia usando `SemaphoreSlim`. El servicio debe:

1. Aceptar `CancellationToken` y propagarlo a todas las operaciones
2. Usar `Task<T>` (no `ValueTask`) como tipo de retorno
3. Reportar progreso via `Action<int>` (porcentaje completado)
4. Si una descarga falla, continuar con las demas y reportar errores parciales al final
5. Limitar la concurrencia a un maximo configurable (ej: 3 descargas simultaneas)

```csharp
public record DownloadResult(string Url, bool Success, string? FilePath, string? Error);

// Tu implementacion aqui
public async Task<List<DownloadResult>> DownloadFilesAsync(
    IReadOnlyList<string> urls,
    string outputDirectory,
    int maxConcurrency,
    Action<int>? onProgress,
    CancellationToken ct)
{
    // ...
}
```

<details>
<summary>Ver solucion</summary>

```csharp
public async Task<List<DownloadResult>> DownloadFilesAsync(
    IReadOnlyList<string> urls, string outputDirectory,
    int maxConcurrency, Action<int>? onProgress, CancellationToken ct)
{
    using var semaphore = new SemaphoreSlim(maxConcurrency, maxConcurrency);
    var completedCount = 0;

    async Task<DownloadResult> DownloadOneAsync(string url)
    {
        await semaphore.WaitAsync(ct); // espera slot disponible (respeta cancelacion)
        try
        {
            ct.ThrowIfCancellationRequested();
            // En produccion: inyectar IHttpClientFactory y usar CreateClient()
            // Para este ejercicio usamos un cliente compartido pasado por parametro
            using var client = new HttpClient(); // ⚠ ver nota abajo
            var data = await client.GetByteArrayAsync(url, ct);
            var fileName = Path.GetFileName(new Uri(url).AbsolutePath);
            var filePath = Path.Combine(outputDirectory, fileName);
            await File.WriteAllBytesAsync(filePath, data, ct);
            return new DownloadResult(url, true, filePath, null);
        }
        catch (OperationCanceledException) when (ct.IsCancellationRequested) { throw; }
        catch (Exception ex) { return new DownloadResult(url, false, null, ex.Message); }
        finally
        {
            semaphore.Release();
            var percent = (int)((double)Interlocked.Increment(ref completedCount) / urls.Count * 100);
            onProgress?.Invoke(percent);
        }
    }

    var results = await Task.WhenAll(urls.Select(DownloadOneAsync));
    return results.ToList();
}
```

**Puntos clave:**
- `SemaphoreSlim` es el mecanismo idomiatico de .NET para limitar concurrencia async. `WaitAsync(ct)` respeta cancelacion.
- El `finally` garantiza que el semaphore se libera incluso si la descarga falla. Sin `Release()`, los slots se agotan.
- `Interlocked.Increment` es thread-safe para el contador de progreso — multiples tasks pueden incrementarlo simultaneamente.
- La cancelacion del caller se propaga (re-throw); los errores individuales se capturan y retornan como `DownloadResult` con `Success = false`.
- ⚠ **En produccion nunca usar `new HttpClient()` dentro de un loop o task paralela** — agota sockets (socket exhaustion). Inyectar `IHttpClientFactory` via DI y llamar `factory.CreateClient()` para cada descarga.

</details>

---

## Resumen: Puntos Clave para la Entrevista

1. **Que hace async/await realmente?** El compilador transforma el metodo en una state machine con estados por cada `await`. En cada punto de suspension, el hilo se libera y se registra un callback para reanudar cuando el Task complete.
2. **Task vs ValueTask?** `Task` siempre crea objeto en heap; `ValueTask` es struct que evita allocacion cuando el resultado ya esta disponible. Default: usar `Task`. Usar `ValueTask` solo para hot paths con sync completion frecuente. Nunca await un `ValueTask` mas de una vez.
3. **Que es un deadlock con .Result?** En contextos con sincronizacion (legacy ASP.NET, WPF), `.Result` bloquea el hilo que el Task necesita para reanudar. En ASP.NET Core no hay deadlock, pero bloquea un thread pool thread innecesariamente.
4. **Cuando usar ConfigureAwait(false)?** Siempre en codigo de libreria, en cada `await`. No necesario en aplicaciones ASP.NET Core (no hay contexto de sincronizacion). El proposito: no capturar ese contexto para que la continuacion se ejecute en cualquier hilo.
5. **Por que async void es peligroso?** Las excepciones no se pueden capturar por el caller, el metodo no se puede await, y en muchos contextos la excepcion crashea el proceso. Unico uso valido: event handlers.
6. **Como cancelar operaciones async?** Crear `CancellationTokenSource`, obtener el `Token`, pasarlo a toda la cadena de llamadas async. Manejar `OperationCanceledException` como flujo normal, no como error.
7. **Que pasa si no propagas CancellationToken?** La operacion ignora la cancelacion — sigue corriendo hasta completar. Es un bug silencioso: el caller cree que cancelo, pero los recursos siguen consumidos (hilos, conexiones HTTP, queries a la DB).
