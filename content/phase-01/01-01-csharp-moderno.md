# C# Moderno: Features C# 8-14 y Puente .NET Framework

**Tiempo estimado:** ~1.5 horas
**Prerequisitos:** Experiencia previa con .NET Framework (cualquier version)
**Target:** .NET 10 LTS (todos los snippets modernos compilan en .NET 10)

---

## Puente .NET Framework a .NET Moderno

Los conceptos de .NET Framework no desaparecieron — cambiaron de ubicacion y se simplificaron. Esta tabla resume las 8 diferencias clave:

| Concepto | .NET Framework | .NET Moderno |
|----------|---------------|--------------|
| Entry point de la app | `Global.asax` + `Startup.cs` | `Program.cs` (minimal hosting, top-level statements) |
| Contenedor DI | Unity, Autofac (terceros) | Built-in `IServiceCollection` / `IServiceProvider` |
| Configuracion | `web.config` / `ConfigurationManager` | `appsettings.json` + `IConfiguration` |
| Pipeline HTTP | HTTP Modules / Handlers | Middleware pipeline (`app.Use...`) |
| Serializacion JSON | `Newtonsoft.Json` (tercero, default de facto) | `System.Text.Json` (built-in, comportamiento diferente) |
| Cliente HTTP | `WebClient` / `HttpWebRequest` | `IHttpClientFactory` + `HttpClient` |
| Hosting model | IIS-only (`System.Web`) | Kestrel cross-platform (IIS como reverse proxy) |
| Background tasks | Windows Services / `Thread` | `IHostedService` / `BackgroundService` |

**Antes (.NET Framework — ❌ no usar en codigo moderno):**

```csharp
// Global.asax.cs
public class MvcApplication : System.Web.HttpApplication
{
    protected void Application_Start()
    {
        AreaRegistration.RegisterAllAreas();
        GlobalConfiguration.Configure(WebApiConfig.Register);
    }
}
// Web.config — leer connection string
var connStr = ConfigurationManager.ConnectionStrings["MyDb"].ConnectionString;
var user = HttpContext.Current.User.Identity.Name;
```

**Ahora (.NET moderno — ✓ compilable en .NET 10):**

```csharp
// Program.cs — todo el bootstrap en un archivo
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddScoped<IOrderService, OrderService>();
var connStr = builder.Configuration.GetConnectionString("MyDb");
var app = builder.Build();
app.UseHttpsRedirection();
app.UseAuthorization();
app.MapControllers();
app.Run();
```

> **Tecnologias obsoletas que debes reconocer inmediatamente:**
> - `BinaryFormatter`: eliminado en .NET 9 por vulnerabilidades de seguridad
> - `System.Web` namespace: no existe en .NET moderno — cualquier `using System.Web;` es codigo Framework
> - `.edmx` designer: no existe en EF Core — solo Code-First es practico

### Challenge 1: Identifica los patrones obsoletos

El siguiente codigo fue escrito para .NET Framework. Contiene **4 patrones que ya no se usan** en .NET moderno. Identifica cada uno y describe como se reemplaza.

```csharp
using System.Web;
using System.Configuration;
using Newtonsoft.Json;

public class LegacyUserService
{
    public string GetCurrentUser()
    {
        // Obtener el usuario del contexto HTTP
        return HttpContext.Current.User.Identity.Name;
    }

    public string GetApiKey()
    {
        // Leer configuracion de web.config
        return ConfigurationManager.AppSettings["ApiKey"];
    }

    public string SerializeUser(User user)
    {
        // Serializar con Newtonsoft
        return JsonConvert.SerializeObject(user);
    }

    public void SendNotification(string message)
    {
        // Crear un HttpClient nuevo cada vez
        var client = new WebClient();
        client.DownloadString($"https://api.notify.com?msg={message}");
    }
}
```

<details>
<summary>Ver solucion</summary>

```csharp
public class ModernUserService(
    IHttpContextAccessor httpContextAccessor,
    IConfiguration configuration,
    HttpClient httpClient) // Inyectado via IHttpClientFactory
{
    public string GetCurrentUser() =>
        httpContextAccessor.HttpContext?.User.Identity?.Name ?? "anonymous";

    public string GetApiKey() =>
        configuration["ApiKey"] ?? throw new InvalidOperationException("ApiKey not configured");

    public string SerializeUser(User user) =>
        JsonSerializer.Serialize(user); // System.Text.Json, no Newtonsoft

    public async Task SendNotificationAsync(string message) =>
        await httpClient.GetAsync(
            $"https://api.notify.com?msg={Uri.EscapeDataString(message)}");
}
```

**Puntos clave:**
- `HttpContext.Current` no existe en .NET moderno — usa `IHttpContextAccessor` via DI.
- `ConfigurationManager` -> `IConfiguration` (lee `appsettings.json`, env vars, y mas).
- `System.Text.Json` es el default built-in. Ojo: case-sensitive por defecto, diferente a Newtonsoft.
- `WebClient` obsoleto -> `HttpClient` via `IHttpClientFactory` (evita socket exhaustion).
- Siempre codifica valores de usuario en URLs con `Uri.EscapeDataString()` para evitar inyeccion en query string. Un `message` con `&` o `=` corromperia la URL sin este encoding.

</details>

---

## Nullable Reference Types — C# 8

Desde C# 8, con `#nullable enable` (habilitado por defecto en proyectos .NET 6+), el compilador trackea nullability. Los tipos referencia son **non-null por defecto** — debes marcar con `?` los que pueden ser null. Esto convierte `NullReferenceException` de un error de runtime a un warning de compilacion.

### Los operadores de nullability

```csharp
#nullable enable

// string (non-nullable): el compilador garantiza que nunca es null
string name = "Sebastian";
// name = null; // CS8600: Converting null literal to non-nullable reference type

// string? (nullable): puede ser null, el compilador te obliga a verificar
string? nickname = GetNickname(); // podria retornar null

// Null-conditional: evalua solo si no es null
int? length = nickname?.Length; // null si nickname es null, Length si no

// Null-coalescing: valor por defecto si es null
string displayName = nickname ?? "Sin apodo";

// Null-coalescing assignment: asigna solo si es null
nickname ??= "Default";

// Null-forgiving: "yo se que no es null" — silencia el warning
string definitelyNotNull = nickname!; // Usa con precaucion
```

> **Anotaciones avanzadas (awareness):** `[NotNull]` y `[MaybeNull]` de `System.Diagnostics.CodeAnalysis` permiten afinar el analisis del compilador cuando las reglas por defecto no alcanzan. Usarlas en APIs publicas de librerias.

### Challenge 2: Corrige las firmas de un servicio de pedidos

El siguiente servicio tiene problemas de nullability. Las firmas no reflejan correctamente que valores pueden ser null, y faltan verificaciones. Corrige las firmas y agrega las verificaciones necesarias.

```csharp
#nullable enable

public class OrderService
{
    private readonly List<Order> _orders = new();

    public Order GetOrder(int id)
    {
        return _orders.FirstOrDefault(o => o.Id == id);
    }

    public decimal GetOrderTotal(Order order)
    {
        return order.Items.Sum(i => i.Price * i.Quantity);
    }

    public string GetCustomerEmail(Order order)
    {
        return order.Customer.Email;
    }

    public void ProcessOrder(Order order)
    {
        var discount = GetDiscount(order.Customer.MembershipLevel);
        order.Total = GetOrderTotal(order) * (1 - discount);
    }
}

public record Order(int Id, List<OrderItem> Items, Customer? Customer, decimal Total);
public record OrderItem(string Name, decimal Price, int Quantity);
public record Customer(string Name, string? Email, string? MembershipLevel);
```

<details>
<summary>Ver solucion</summary>

```csharp
#nullable enable

public class OrderService
{
    private readonly List<Order> _orders = new();

    public Order? GetOrder(int id) =>          // Retorno nullable: FirstOrDefault puede ser null
        _orders.FirstOrDefault(o => o.Id == id);

    public decimal GetOrderTotal(Order order)
    {
        ArgumentNullException.ThrowIfNull(order);
        if (order.Items.Count == 0) return 0m; // Items es non-nullable: no checar null
        return order.Items.Sum(i => i.Price * i.Quantity);
    }

    public string? GetCustomerEmail(Order order) // Retorno nullable: Customer y Email pueden ser null
    {
        ArgumentNullException.ThrowIfNull(order);
        return order.Customer?.Email;
    }

    public Order ProcessOrder(Order order)
    {
        ArgumentNullException.ThrowIfNull(order);
        var discount = GetDiscount(order.Customer?.MembershipLevel);
        return order with { Total = GetOrderTotal(order) * (1 - discount) };
    }

    private decimal GetDiscount(string? level) => level switch
    {
        "Gold" => 0.15m, "Silver" => 0.10m, "Bronze" => 0.05m, null or _ => 0m
    };
}
```

**Puntos clave:**
- `GetOrder` retorna `Order?` porque `FirstOrDefault` puede retornar null.
- `GetCustomerEmail` retorna `string?` — el operador `?.` propaga la nullability a traves de la cadena.
- `ArgumentNullException.ThrowIfNull()` (.NET 6+) es el patron moderno para guard clauses.
- `ProcessOrder` retorna `Order` — el record es inmutable, `with` produce un **nuevo objeto**. El caller debe usar el valor retornado: `var updated = service.ProcessOrder(order);`. Asignar el resultado a la variable local sin retornarlo seria un no-op silencioso.

</details>

---

## Records y Value Equality — C# 9-10

Un `record` es un tipo referencia con **value equality** por defecto — dos records son iguales si todos sus valores son iguales, sin importar si son la misma instancia en memoria. Pregunta frecuente de entrevista: *"Cual es la diferencia entre record y class?"*

### record vs class

```csharp
// CLASS: reference equality por defecto
var c1 = new ProductClass { Name = "Widget", Price = 9.99m };
var c2 = new ProductClass { Name = "Widget", Price = 9.99m };
Console.WriteLine(c1 == c2);  // False — objetos diferentes en memoria

// RECORD: value equality por defecto
public record Product(string Name, decimal Price, string Category);
var r1 = new Product("Widget", 9.99m, "Tools");
var r2 = new Product("Widget", 9.99m, "Tools");
Console.WriteLine(r1 == r2);  // True — mismos valores = iguales
```

### With-expressions, record struct y cuando usar cada uno

```csharp
var original = new Product("Widget", 9.99m, "Tools");
var discounted = original with { Price = 7.99m }; // copia con propiedad modificada
Console.WriteLine(original.Price);   // 9.99 — el original no cambia
Console.WriteLine(original == discounted); // False — precios diferentes

// record struct (C# 10): tipo valor (stack) + value equality
public record struct Point(double X, double Y);
// Nota: record struct es mutable por defecto. Usa readonly record struct para inmutabilidad.
```

| Usar `record` cuando... | Usar `class` cuando... |
|--------------------------|------------------------|
| DTOs y data transfer objects | Entidades con identidad (ej: `User` con `Id`) |
| Value objects de dominio (Money, Address) | Objetos con comportamiento complejo y estado mutable |
| Snapshots de estado inmutables | Servicios, repositorios, controllers |
| Claves de cache o diccionario | Objetos con ciclo de vida largo |

### Challenge 3: Implementa un value object Money como record

Implementa un value object `Money` usando `record` que represente una cantidad monetaria con moneda. Debe cumplir:

- Dos `Money` con mismo monto y moneda son iguales
- Soporta operador `+` para sumar montos de la misma moneda (lanza excepcion si monedas distintas)
- Se puede crear una copia con monto diferente via `with`
- Validacion: el monto no puede ser negativo y la moneda no puede ser vacia

```csharp
// Tu implementacion aqui
public record Money(/* ... */)
{
    // ...
}
```

<details>
<summary>Ver solucion</summary>

```csharp
public record Money
{
    public decimal Amount { get; init; }
    public string Currency { get; init; }

    public Money(decimal Amount, string Currency)
    {
        ArgumentOutOfRangeException.ThrowIfNegative(Amount);
        ArgumentException.ThrowIfNullOrWhiteSpace(Currency);
        this.Amount = Amount;
        this.Currency = Currency.ToUpperInvariant();
    }

    public static Money operator +(Money left, Money right)
    {
        if (left.Currency != right.Currency)
            throw new InvalidOperationException($"Cannot add {left.Currency} and {right.Currency}");
        return left with { Amount = left.Amount + right.Amount };
    }

    public override string ToString() => $"{Amount:F2} {Currency}";
}

// Uso:
var total = new Money(29.99m, "USD") + new Money(2.40m, "USD"); // 32.39 USD
Console.WriteLine(new Money(10m, "usd") == new Money(10m, "USD")); // True
var discounted = total with { Amount = total.Amount * 0.9m };
```

**Puntos clave:**
- Constructor explicito (no positional syntax) para agregar validacion — el positional syntax no permite validar.
- `Currency.ToUpperInvariant()` normaliza para que la equality de records funcione correctamente (`"usd"` == `"USD"`).
- El operador `+` retorna un nuevo `Money` (inmutable). `with` expression genera copia shallow via `Clone()` automatico.

</details>

---

## Pattern Matching — C# 8-12

Pattern matching transforma cadenas de `if-else` verbosas en expresiones declarativas. Aparece en casi todas las entrevistas .NET.

### Switch expression (C# 8)

```csharp
// Switch expression — reemplaza if-else chains
string GetDiscountLevel(decimal total) => total switch
{
    >= 1000 => "Platinum",
    >= 500  => "Gold",
    >= 100  => "Silver",
    _       => "None"  // _ = discard, captura todo lo demas
};
```

### Type patterns, property patterns y list patterns

```csharp
// Type patterns + property patterns: verifica tipo y propiedades en un paso
string DescribeShape(object shape) => shape switch
{
    Circle { Radius: > 10 } c  => $"Large circle (r={c.Radius})",
    Circle c                    => $"Small circle (r={c.Radius})",
    Rectangle { Width: var w, Height: var h } when w == h => $"Square {w}x{h}",
    Rectangle { Width: var w, Height: var h } => $"Rectangle {w}x{h}",
    null => "No shape provided",
    _    => $"Unknown: {shape.GetType().Name}"
};

// List patterns (C# 11): match contra elementos de una coleccion
string ClassifyArray(int[] numbers) => numbers switch
{
    []           => "Empty",
    [var single] => $"Single: {single}",
    [0, ..]      => "Starts with zero",       // .. = slice (cero o mas elementos)
    [.., > 100]  => "Ends with large number",
    _            => "Other"
};
```

### Challenge 4: Refactoriza if-else a switch expression

El siguiente metodo usa una cadena de `if-else` con 6 condiciones para calcular el precio de envio de un paquete. Refactorizalo usando switch expression con type patterns y property patterns.

```csharp
public abstract record Shipment(double WeightKg, string Destination);
public record DomesticShipment(double WeightKg, string Destination, bool IsExpress) : Shipment(WeightKg, Destination);
public record InternationalShipment(double WeightKg, string Destination, string Zone) : Shipment(WeightKg, Destination);

public decimal CalculateShipping(Shipment shipment)
{
    if (shipment == null) throw new ArgumentNullException(nameof(shipment));
    if (shipment is DomesticShipment domestic)
    {
        if (domestic.IsExpress)
            return domestic.WeightKg > 20 ? 35.00m : 15.00m;
        else
            return domestic.WeightKg > 20 ? 20.00m : 8.00m;
    }
    else if (shipment is InternationalShipment intl)
    {
        return intl.Zone == "EU"
            ? 25.00m + (decimal)intl.WeightKg * 0.50m
            : 45.00m + (decimal)intl.WeightKg * 1.20m;
    }
    throw new ArgumentException("Unknown shipment type");
}
```

<details>
<summary>Ver solucion</summary>

```csharp
public decimal CalculateShipping(Shipment shipment) => shipment switch
{
    null => throw new ArgumentNullException(nameof(shipment)),
    DomesticShipment { IsExpress: true, WeightKg: > 20 } => 35.00m,
    DomesticShipment { IsExpress: true }                  => 15.00m,
    DomesticShipment { WeightKg: > 20 }                   => 20.00m,
    DomesticShipment                                      => 8.00m,
    InternationalShipment { Zone: "EU" } intl => 25.00m + (decimal)intl.WeightKg * 0.50m,
    InternationalShipment intl                => 45.00m + (decimal)intl.WeightKg * 1.20m,
    _ => throw new ArgumentException($"Unknown shipment type: {shipment.GetType().Name}")
};
```

**Puntos clave:**
- El orden importa: patrones mas especificos primero (express+heavy antes de solo express).
- `null` se verifica directamente en el switch expression — sin `if` separado.
- Property patterns (`{ IsExpress: true, WeightKg: > 20 }`) reemplazan ifs anidados de forma declarativa.
- `_` al final es necesario: el compilador no garantiza exhaustividad con subtipos abiertos.

</details>

---

## Primary Constructors y Collection Expressions — C# 12

C# 12 introdujo **primary constructors** en clases y **collection expressions** con sintaxis `[]`.

### Primary constructors en clases

```csharp
// Antes: constructor clasico con campo privado
public class UserService
{
    private readonly ILogger<UserService> _logger;
    private readonly IUserRepository _repository;
    public UserService(ILogger<UserService> logger, IUserRepository repository)
    {
        _logger = logger;
        _repository = repository;
    }
    public User? GetUser(int id) => _repository.GetById(id);
}

// Ahora: primary constructor — parametros en la declaracion de clase
public class UserService(ILogger<UserService> logger, IUserRepository repository)
{
    public User? GetUser(int id) => repository.GetById(id);
}
```

> **Nota:** los parametros de un primary constructor NO son campos `readonly` — son parametros capturados como closures.

### Collection expressions

```csharp
List<int> list = [1, 2, 3];          // reemplaza new List<int> { 1, 2, 3 }
int[] array = [1, 2, 3];             // reemplaza new int[] { 1, 2, 3 }
int[] combined = [..first, ..second]; // spread operator combina colecciones
List<string> empty = [];              // coleccion vacia
```

### Challenge 5: Convierte a primary constructor y collection expressions

Reescribe la siguiente clase usando primary constructor y collection expressions donde sea posible.

```csharp
public class NotificationDispatcher
{
    private readonly IEmailSender _emailSender;
    private readonly ISmsSender _smsSender;
    private readonly ILogger<NotificationDispatcher> _logger;
    private readonly List<string> _supportedChannels;

    public NotificationDispatcher(IEmailSender emailSender, ISmsSender smsSender,
        ILogger<NotificationDispatcher> logger)
    {
        _emailSender = emailSender; _smsSender = smsSender; _logger = logger;
        _supportedChannels = new List<string> { "email", "sms", "push" };
    }

    public List<string> GetChannels()
    {
        var extra = new List<string> { "webhook", "slack" };
        var all = new List<string>(); all.AddRange(_supportedChannels); all.AddRange(extra);
        return all;
    }

    public void Send(string channel, string message)
    {
        _logger.LogInformation("Sending via {Channel}", channel);
        if (channel == "email") _emailSender.Send(message);
        else if (channel == "sms") _smsSender.Send(message);
    }
}
```

<details>
<summary>Ver solucion</summary>

```csharp
public class NotificationDispatcher(
    IEmailSender emailSender,
    ISmsSender smsSender,
    ILogger<NotificationDispatcher> logger)
{
    private readonly List<string> _supportedChannels = ["email", "sms", "push"];

    public List<string> GetChannels()
    {
        List<string> extra = ["webhook", "slack"];
        return [.._supportedChannels, ..extra];
    }

    public void Send(string channel, string message)
    {
        logger.LogInformation("Sending via {Channel}", channel);
        if (channel == "email") emailSender.Send(message);
        else if (channel == "sms") smsSender.Send(message);
    }
}
```

**Puntos clave:**
- Primary constructor elimina constructor + campos. Pero `_supportedChannels` se mantiene como field (es estado, no DI).
- `["email", "sms"]` reemplaza `new List<string> { ... }` — el compilador elige la implementacion optima.
- `[.._supportedChannels, ..extra]` reemplaza `AddRange` + `AddRange` en una sola expresion.

</details>

---

## C# 13-14: Nivel Awareness

Features recientes — saber que existen y cuando usarlas es suficiente para entrevistas en 2026.

| Feature | Version | Que es | Cuando usar |
|---------|---------|--------|-------------|
| params collections | C# 13 | `params` acepta `IEnumerable<T>`, `Span<T>`, no solo arrays | APIs flexibles que reciben multiples items |
| new lock type | C# 13 | `System.Threading.Lock` reemplaza `object` para `lock` | Codigo multithreaded nuevo (mejor performance) |
| field-backed properties | C# 14 | Keyword `field` accede al backing field auto-generado | Properties con logica custom sin declarar campo explicito |
| extension blocks | C# 14 | Bloque `extension` agrupa metodos de extension | Organizar multiples extension methods relacionados |
| null-conditional assignment | C# 14 | `obj?.Property = value` asigna solo si `obj` no es null | Asignacion condicional concisa |

```csharp
// params collections (C# 13)
public void Log(params IEnumerable<string> messages)
{
    foreach (var msg in messages) Console.WriteLine(msg);
}

// new lock type (C# 13)
private readonly System.Threading.Lock _lock = new();
public void SafeIncrement() { lock (_lock) { _counter++; } }

// field-backed properties (C# 14)
public decimal Price { get => field; set => field = value >= 0 ? value
    : throw new ArgumentOutOfRangeException(nameof(value)); }

// extension blocks (C# 14)
extension(string s) { public string Truncate(int max) => s.Length <= max ? s : s[..max] + "..."; }

// null-conditional assignment (C# 14)
order?.Status = "Shipped"; // solo asigna si order no es null
```

> **Referencia oficial:** https://learn.microsoft.com/dotnet/csharp/whats-new/

---

## Resumen: Puntos Clave para la Entrevista

1. **.NET Framework vs moderno?** `Program.cs` vs `Global.asax`, DI built-in, `appsettings.json` vs `web.config`, `System.Text.Json` vs Newtonsoft, Kestrel cross-platform.
2. **Nullable reference types?** Desde C# 8, tipos referencia son non-null por defecto. Marca con `?` los nullable. El compilador emite warnings para asignaciones inseguras.
3. **record vs class?** Records: value equality automatica, `with` expressions, deconstruction. Clases: reference equality. Records para DTOs/value objects; clases para entidades con identidad.
4. **Cuando pattern matching?** Reemplaza `if-else` chains con switch expressions declarativas. Combina type checking + property matching. El compilador avisa si falta un caso.
5. **Primary constructor?** C# 12: parametros en la declaracion de clase, elimina boilerplate de campos + constructor. Capturados como closures, no campos `readonly`.
6. **Collection expressions?** Sintaxis `[]` unificada para arrays, listas, spans. Spread `..` combina colecciones. C# 12.
7. **C# 13-14?** `params` con cualquier coleccion, `Lock` tipo, `field` keyword, extension blocks, null-conditional assignment. Mejoras incrementales.
