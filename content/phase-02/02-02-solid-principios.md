# SOLID: Los 5 Principios de Diseno Orientado a Objetos

**Tiempo estimado:** ~1.5 horas
**Prerequisitos:** Conocimiento de OOP en C# (Phase 2, Plan 01: herencia, polimorfismo, abstraccion)
**Target:** .NET 10 LTS (todos los snippets compilan en .NET 10)

---

Los principios SOLID son cinco guias de diseno orientado a objetos definidas por Robert Martin ("Uncle Bob"). No son reglas absolutas: son herramientas para escribir codigo mantenible y testeable. Una clave para destacar en entrevistas es saber no solo *como* aplicar cada principio, sino tambien *cuando* aplicarlo excesivamente es contraproducente.

---

## SRP — Single Responsibility Principle

Una clase debe tener **una sola razon para cambiar**. Esto significa que cada clase debe encargarse de un unico aspecto del comportamiento del sistema. Si una clase cambia cuando cambian las reglas de negocio Y cuando cambia la base de datos Y cuando cambia el formato de notificaciones, entonces tiene tres razones para cambiar — viola SRP.

### Aplicacion correcta

El siguiente ejemplo muestra un `OrderProcessor` que originalmente hace demasiado. Despues del refactor, cada clase tiene una sola responsabilidad:

**Antes (viola SRP — una clase, multiples razones para cambiar):**

```csharp
// Orden procesadora "dios" — cambia si cambia validacion, logica de cobro O notificaciones
public class OrderProcessor
{
    public bool ProcessOrder(Order order)
    {
        // Responsabilidad 1: Validacion
        if (order.Items.Count == 0) return false;
        if (order.CustomerId <= 0) return false;

        // Responsabilidad 2: Logica de negocio (calculos)
        decimal total = order.Items.Sum(i => i.Price * i.Quantity);
        order.Total = total;

        // Responsabilidad 3: Acceso a datos
        using var db = new SqlConnection("...");
        db.Execute("INSERT INTO Orders ...", order);

        // Responsabilidad 4: Notificacion
        var smtp = new SmtpClient("smtp.example.com");
        smtp.Send("orders@company.com", order.CustomerEmail, "Order confirmed", $"Total: {total:C}");

        return true;
    }
}
```

**Despues (SRP aplicado — cada clase con una sola responsabilidad):**

```csharp
// Responsabilidad 1: Solo valida
public class OrderValidator
{
    public ValidationResult Validate(Order order)
    {
        if (order.Items.Count == 0)
            return ValidationResult.Fail("Order must have at least one item");
        if (order.CustomerId <= 0)
            return ValidationResult.Fail("Invalid customer ID");
        return ValidationResult.Ok();
    }
}

// Responsabilidad 2: Solo calcula totales
public class OrderCalculator
{
    public decimal CalculateTotal(Order order) =>
        order.Items.Sum(i => i.Price * i.Quantity);
}

// Responsabilidad 3: Solo persiste
public class OrderRepository
{
    private readonly string _connectionString;
    public OrderRepository(string connectionString) => _connectionString = connectionString;

    public void Save(Order order)
    {
        using var db = new SqlConnection(_connectionString);
        db.Execute("INSERT INTO Orders ...", order);
    }
}

// Responsabilidad 4: Solo notifica
public class OrderNotifier
{
    private readonly SmtpClient _smtp;
    public OrderNotifier(SmtpClient smtp) => _smtp = smtp;

    public void NotifyCustomer(Order order) =>
        _smtp.Send("orders@company.com", order.CustomerEmail, "Order confirmed",
            $"Your order total: {order.Total:C}");
}

// Orquestador liviano: une las piezas
public class OrderService(
    OrderValidator validator,
    OrderCalculator calculator,
    OrderRepository repository,
    OrderNotifier notifier)
{
    public bool ProcessOrder(Order order)
    {
        var validation = validator.Validate(order);
        if (!validation.IsValid) return false;

        order.Total = calculator.CalculateTotal(order);
        repository.Save(order);
        notifier.NotifyCustomer(order);
        return true;
    }
}
```

> **Nota (.NET 6+):** `SmtpClient` esta marcado como obsoleto en .NET 6+. En proyectos reales usa
> `MailKit` (`MimeMessage` + `SmtpClient` de MailKit). Para los ejemplos de este modulo
> se usa `SmtpClient` de `System.Net.Mail` por simplicidad conceptual; en produccion
> usa MailKit o los SDK de proveedores (SendGrid, Mailgun).

### Challenge 1: Refactorizar PaymentService

El siguiente `PaymentService` viola SRP al mezclar validacion, cobro, logging y envio de recibo en un solo metodo. Tu tarea: identifica las responsabilidades y separa la clase en unidades con una sola razon para cambiar.

```csharp
public class PaymentService
{
    public bool ProcessPayment(PaymentRequest request)
    {
        // Validacion
        if (request.Amount <= 0) return false;
        if (string.IsNullOrEmpty(request.CardNumber)) return false;
        if (request.CardNumber.Length != 16) return false;

        // Cobro via gateway externo
        var gateway = new PaymentGatewayClient("https://api.payment.com", "api-key-123");
        var result = gateway.Charge(request.CardNumber, request.Amount, request.Currency);

        // Logging
        File.AppendAllText("payments.log",
            $"{DateTime.Now}: Payment {(result.Success ? "OK" : "FAILED")} - Amount: {request.Amount}");

        // Envio de recibo por email
        if (result.Success)
        {
            var smtp = new SmtpClient("smtp.company.com");
            smtp.Send("billing@company.com", request.CustomerEmail,
                "Payment receipt", $"Charged: {request.Amount:C} {request.Currency}");
        }

        return result.Success;
    }
}

// Modelos para referencia
public record PaymentRequest(decimal Amount, string CardNumber, string Currency, string CustomerEmail);
public record ChargeResult(bool Success, string TransactionId);
```

<details>
<summary>Ver solucion</summary>

```csharp
// Responsabilidad 1: Validacion del request
public class PaymentValidator
{
    public ValidationResult Validate(PaymentRequest request)
    {
        if (request.Amount <= 0)
            return ValidationResult.Fail("Amount must be positive");
        if (string.IsNullOrEmpty(request.CardNumber) || request.CardNumber.Length != 16)
            return ValidationResult.Fail("Card number must be 16 digits");
        return ValidationResult.Ok();
    }
}

// Responsabilidad 2: Cobro via gateway
public class PaymentGateway(string apiUrl, string apiKey)
{
    public ChargeResult Charge(string cardNumber, decimal amount, string currency)
    {
        var client = new PaymentGatewayClient(apiUrl, apiKey);
        return client.Charge(cardNumber, amount, currency);
    }
}

// Responsabilidad 3: Logging de pagos
public class PaymentLogger
{
    public void Log(PaymentRequest request, ChargeResult result)
    {
        File.AppendAllText("payments.log",
            $"{DateTime.Now}: Payment {(result.Success ? "OK" : "FAILED")} - Amount: {request.Amount}");
    }
}

// Responsabilidad 4: Notificacion al cliente
public class ReceiptSender(SmtpClient smtp)
{
    public void SendReceipt(PaymentRequest request)
    {
        smtp.Send("billing@company.com", request.CustomerEmail,
            "Payment receipt", $"Charged: {request.Amount:C} {request.Currency}");
    }
}

// Orquestador: coordina el flujo
public class PaymentService(
    PaymentValidator validator,
    PaymentGateway gateway,
    PaymentLogger logger,
    ReceiptSender receiptSender)
{
    public bool ProcessPayment(PaymentRequest request)
    {
        var validation = validator.Validate(request);
        if (!validation.IsValid) return false;

        var result = gateway.Charge(request.CardNumber, request.Amount, request.Currency);
        logger.Log(request, result);

        if (result.Success)
            receiptSender.SendReceipt(request);

        return result.Success;
    }
}
```

**Puntos clave:**
- Cada clase ahora tiene exactamente una razon para cambiar: `PaymentValidator` cambia si cambian las reglas de validacion, `PaymentGateway` cambia si cambia el proveedor de pagos, etc.
- El `PaymentService` se convierte en un *orquestador* liviano — no contiene logica de negocio, solo coordina el flujo.
- Las primary constructors de C# 12 (`PaymentGateway(string apiUrl, string apiKey)`) hacen el codigo mas conciso sin perder claridad.
- Cada clase es ahora testeable de forma independiente — puedes mockear `PaymentGateway` sin tocar el logger.

</details>

### Cuando va demasiado lejos

El anti-pattern de SRP llevado al extremo es el **anemic domain model**: entidades que son solo bolsas de propiedades sin ninguna logica propia, con toda la logica esparcida en servicios.

```csharp
// Anemic domain model — Order no tiene ningun comportamiento propio
public class Order
{
    public int Id { get; set; }
    public List<OrderItem> Items { get; set; } = [];
    public decimal Total { get; set; }
    public OrderStatus Status { get; set; }
    public DateTime CreatedAt { get; set; }
    // Solo propiedades. Cero logica de negocio.
}

// La logica esta dispersa en multiples servicios que el consumidor debe orquestar
public class OrderTotalCalculator { public decimal Calculate(Order order) => /* ... */; }
public class OrderStatusValidator { public bool CanShip(Order order) => /* ... */; }
public class OrderDiscountApplier { public void Apply(Order order, string code) => /* ... */; }
public class OrderItemAdder { public void Add(Order order, OrderItem item) => /* ... */; }

// El consumidor debe saber en que orden llamarlos:
orderItemAdder.Add(order, item);           // paso 1
var total = orderTotalCalculator.Calculate(order);  // paso 2
orderDiscountApplier.Apply(order, "SAVE10");        // paso 3 — pero despues del calculo de total? antes?
```

La fragmentacion crea acoplamiento implicito: los consumidores necesitan conocer el orden correcto de llamadas. La entidad `Order` deberia contener logica que le pertenece naturalmente.

> **Respuesta de entrevista:** "No aplicaria SRP cuando la separacion crea clases anemicas sin comportamiento propio y fuerza a los consumidores a orquestar multiples servicios para una operacion atomica. En CQRS es intencional y valido; en apps CRUD tradicionales con una capa de dominio, suele ser over-engineering accidental."

---

## OCP — Open/Closed Principle

**Abierto a extension, cerrado a modificacion.** Una vez que un modulo funciona correctamente, no deberia necesitar cambiar para soportar nuevos casos de uso — deberia ser posible extenderlo sin tocar el codigo existente. El mecanismo principal en C# es el polimorfismo: nuevas clases que implementan interfaces o heredan de clases abstractas.

### Aplicacion correcta

Un calculador de descuentos que crece sin modificar el codigo existente:

**Antes (viola OCP — cada nuevo tipo de descuento requiere modificar `DiscountCalculator`):**

```csharp
public class DiscountCalculator
{
    public decimal ApplyDiscount(Order order, string discountType)
    {
        return discountType switch
        {
            "percentage" => order.Total * 0.90m,  // 10% off
            "fixed"      => order.Total - 50m,
            // Para agregar "buyonegetone" hay que modificar esta clase
            _ => order.Total
        };
    }
}
```

**Despues (OCP aplicado — nuevos descuentos sin modificar codigo existente):**

```csharp
// Contrato inmutable
public interface IDiscountStrategy
{
    decimal Apply(decimal originalTotal);
    string Description { get; }
}

// Implementaciones existentes — no se modifican
public class PercentageDiscount(decimal percent) : IDiscountStrategy
{
    public decimal Apply(decimal total) => total * (1 - percent / 100);
    public string Description => $"{percent}% off";
}

public class FixedAmountDiscount(decimal amount) : IDiscountStrategy
{
    public decimal Apply(decimal total) => Math.Max(0, total - amount);
    public string Description => $"${amount} off";
}

// Nueva extension — sin modificar ninguna clase existente
public class BuyOneGetOneDiscount : IDiscountStrategy
{
    public decimal Apply(decimal total) => total / 2;
    public string Description => "Buy one get one free";
}

// El calculador nunca cambia
public class DiscountCalculator
{
    public decimal Apply(decimal total, IDiscountStrategy strategy) =>
        strategy.Apply(total);
}

// Uso
var calculator = new DiscountCalculator();
var strategies = new IDiscountStrategy[]
{
    new PercentageDiscount(10),
    new FixedAmountDiscount(50),
    new BuyOneGetOneDiscount()
};

foreach (var s in strategies)
    Console.WriteLine($"{s.Description}: {calculator.Apply(1000m, s):C}");
```

### Challenge 2: Refactorizar NotificationSender

El siguiente `NotificationSender` viola OCP: cada nuevo canal de notificacion requiere modificar el metodo `Send`. Refactorizalo usando polimorfismo para que agregar Push notifications (o cualquier otro canal futuro) no requiera modificar el codigo existente.

```csharp
public class NotificationSender
{
    public void Send(string channel, string recipient, string message)
    {
        if (channel == "email")
        {
            var smtp = new SmtpClient("smtp.company.com");
            smtp.Send("noreply@company.com", recipient, "Notification", message);
            Console.WriteLine($"Email sent to {recipient}");
        }
        else if (channel == "sms")
        {
            var smsClient = new TwilioClient("account-sid", "auth-token");
            smsClient.SendMessage("+1234567890", recipient, message);
            Console.WriteLine($"SMS sent to {recipient}");
        }
        else if (channel == "push")
        {
            // TODO: implementar push notifications
            throw new NotImplementedException("Push not supported yet");
        }
    }
}
```

<details>
<summary>Ver solucion</summary>

```csharp
// Contrato para cualquier canal de notificacion
public interface INotificationChannel
{
    string ChannelName { get; }
    void Send(string recipient, string message);
}

// Canal email — no cambia cuando se agrega SMS o Push
public class EmailNotificationChannel(SmtpClient smtp) : INotificationChannel
{
    public string ChannelName => "email";

    public void Send(string recipient, string message)
    {
        smtp.Send("noreply@company.com", recipient, "Notification", message);
        Console.WriteLine($"Email sent to {recipient}");
    }
}

// Canal SMS — no cambia cuando se agrega Push
public class SmsNotificationChannel(string accountSid, string authToken) : INotificationChannel
{
    private readonly TwilioClient _client = new(accountSid, authToken);
    public string ChannelName => "sms";

    public void Send(string recipient, string message)
    {
        _client.SendMessage("+1234567890", recipient, message);
        Console.WriteLine($"SMS sent to {recipient}");
    }
}

// Nuevo canal — sin modificar nada existente
public class PushNotificationChannel(string firebaseKey) : INotificationChannel
{
    public string ChannelName => "push";

    public void Send(string recipient, string message)
    {
        // Enviar via Firebase Cloud Messaging
        Console.WriteLine($"Push sent to device {recipient}: {message}");
    }
}

// NotificationSender nunca cambia
public class NotificationSender(IEnumerable<INotificationChannel> channels)
{
    private readonly Dictionary<string, INotificationChannel> _channels =
        channels.ToDictionary(c => c.ChannelName);

    public void Send(string channelName, string recipient, string message)
    {
        if (!_channels.TryGetValue(channelName, out var channel))
            throw new InvalidOperationException($"Unknown channel: {channelName}");

        channel.Send(recipient, message);
    }
}
```

**Puntos clave:**
- `NotificationSender` esta cerrado a modificacion: su codigo nunca cambia cuando se agrega un nuevo canal.
- El sistema esta abierto a extension: agregar `SlackNotificationChannel` o `WhatsAppNotificationChannel` no requiere tocar ninguna clase existente.
- `IEnumerable<INotificationChannel>` en el constructor permite registrar los canales via DI en ASP.NET Core con `services.AddScoped<INotificationChannel, EmailNotificationChannel>()`.
- El diccionario interno permite lookup por nombre de canal en O(1).

</details>

### Cuando va demasiado lejos

El anti-pattern de OCP es la **abstraction overkill**: crear interfaces y factories para clases que *nunca tendran una segunda implementacion*.

```csharp
// OVER-ENGINEERING en una utilidad de 50 lineas que solo usa DateTime.UtcNow
// Esta interfaz existe solo para OCP "por si acaso"
public interface IDateTimeProvider
{
    DateTime UtcNow { get; }
}

public class SystemDateTimeProvider : IDateTimeProvider
{
    public DateTime UtcNow => DateTime.UtcNow;
}

// En una herramienta de consola simple, esto es ceremonial sin beneficio:
public class InvoiceNumberGenerator(IDateTimeProvider dateTimeProvider)
{
    public string Generate() => $"INV-{dateTimeProvider.UtcNow:yyyyMMdd-HHmmss}";
}

// Registro en DI, factory, etc. — todo para una clase que nadie va a reemplazar.
```

> **Nota:** En codigo de produccion testeable (APIs, servicios), `IDateTimeProvider` ES valido porque permite mockear el tiempo en tests. El problema es crearlo en utilidades standalone que nunca se testean de esa manera.

> **Respuesta de entrevista:** "No aplicaria OCP cuando el codigo no tiene historia real de cambio ni necesidad de testabilidad. Crear abstracciones para extension futura que nunca llega viola YAGNI y agrega indirection sin beneficio. OCP se justifica cuando hay evidencia de que el punto de variacion existira — no por especulacion."

---

## LSP — Liskov Substitution Principle

**Los subtipos deben ser sustituibles por sus tipos base sin alterar el comportamiento correcto del programa.** En terminos practicos: si usas una referencia del tipo base, sustituirla por cualquier subtipo no debe romper el programa ni sorprender al consumidor. La senal clasica de violacion es `NotImplementedException` en metodos heredados.

### Aplicacion correcta

El siguiente ejemplo muestra una violacion de LSP en un repositorio de cache de solo lectura que implementa una interfaz de lectura/escritura:

**Violacion de LSP:**

```csharp
public interface IReadWriteRepository<T>
{
    T GetById(int id);
    IEnumerable<T> GetAll();
    void Add(T entity);
    void Delete(int id);
}

// LSP VIOLADO: ReadOnlyCache "es un" IReadWriteRepository pero no puede cumplir el contrato
public class ReadOnlyCache<T> : IReadWriteRepository<T>
{
    private readonly Dictionary<int, T> _cache = [];

    public T GetById(int id) => _cache.TryGetValue(id, out var item) ? item : default!;
    public IEnumerable<T> GetAll() => _cache.Values;
    public void Add(T entity) => throw new NotImplementedException();    // VIOLA LSP
    public void Delete(int id) => throw new NotImplementedException();   // VIOLA LSP
}

// Problema: el consumidor no puede confiar en que cualquier IReadWriteRepository<T> funciona
public class ProductService(IReadWriteRepository<Product> repository)
{
    public void AddProduct(Product p) => repository.Add(p);  // Explota en runtime si es ReadOnlyCache
}
```

**Fix: Segregar las interfaces (ISP ayuda a LSP):**

```csharp
// Interfaces segregadas — cada tipo solo implementa lo que puede cumplir
public interface IReadRepository<T>
{
    T GetById(int id);
    IEnumerable<T> GetAll();
}

public interface IWriteRepository<T>
{
    void Add(T entity);
    void Delete(int id);
}

public interface IReadWriteRepository<T> : IReadRepository<T>, IWriteRepository<T> { }

// ReadOnlyCache cumple su contrato completamente — sin excepciones
public class ReadOnlyCache<T> : IReadRepository<T>
{
    private readonly Dictionary<int, T> _cache = [];

    public T GetById(int id) => _cache.TryGetValue(id, out var item) ? item : default!;
    public IEnumerable<T> GetAll() => _cache.Values;
    // No hay Add/Delete — no los promete, no los viola
}

// Implementacion completa para base de datos
public class SqlProductRepository : IReadWriteRepository<Product>
{
    public Product GetById(int id) => /* query DB */;
    public IEnumerable<Product> GetAll() => /* query DB */;
    public void Add(Product entity) => /* insert DB */;
    public void Delete(int id) => /* delete DB */;
}

// El consumidor ahora depende solo de lo que necesita
public class ProductDashboard(IReadRepository<Product> repository)
{
    public IEnumerable<Product> GetAll() => repository.GetAll();  // Solo lee — nunca explota
}
```

### Challenge 3: Arreglar la jerarquia Employee

El siguiente codigo viola LSP: `Contractor` hereda de `Employee` pero lanza una excepcion en `CalculateOvertime()` porque los contratistas no tienen horas extra segun la ley. Tu tarea: redisena la jerarquia para que sea LSP-compliant.

```csharp
public class Employee
{
    public string Name { get; set; } = "";
    public decimal HourlyRate { get; set; }

    public virtual decimal CalculatePay(int hours) => HourlyRate * hours;

    // Horas extra: tiempo y medio por encima de 40 horas
    public virtual decimal CalculateOvertime(int extraHours) => HourlyRate * 1.5m * extraHours;
}

public class FullTimeEmployee : Employee
{
    // Hereda todo correctamente
}

public class Contractor : Employee
{
    // Los contratistas NO tienen overtime segun la ley
    public override decimal CalculateOvertime(int extraHours) =>
        throw new InvalidOperationException("Contractors are not eligible for overtime");
}

// El siguiente servicio de nomina explota si recibe un Contractor:
public class PayrollService
{
    public decimal CalculateTotalPayWithOvertime(Employee employee, int regularHours, int overtimeHours)
    {
        return employee.CalculatePay(regularHours) + employee.CalculateOvertime(overtimeHours);
    }
}
```

<details>
<summary>Ver solucion</summary>

```csharp
// Abstraccion base: solo lo que TODOS los empleados pueden hacer
public abstract class Employee
{
    public string Name { get; init; } = "";
    public decimal HourlyRate { get; init; }

    public abstract decimal CalculatePay(int hours);
}

// Empleados con derecho a overtime — el contrato incluye CalculateOvertime
public abstract class OvertimeEligibleEmployee : Employee
{
    public virtual decimal CalculateOvertime(int extraHours) => HourlyRate * 1.5m * extraHours;
}

// Empleado full-time — cumple ambos contratos completamente
public class FullTimeEmployee : OvertimeEligibleEmployee
{
    public override decimal CalculatePay(int hours) => HourlyRate * hours;
    // Hereda CalculateOvertime sin problemas
}

// Contratista — solo implementa lo que puede cumplir
public class Contractor : Employee
{
    public override decimal CalculatePay(int hours) => HourlyRate * hours;
    // No hereda CalculateOvertime porque no lo puede cumplir — y no lo promete
}

// Nomina base: solo operaciones validas para todos
public class PayrollService
{
    public decimal CalculatePay(Employee employee, int hours) =>
        employee.CalculatePay(hours);
}

// Nomina con overtime: solo para quienes tienen derecho
public class OvertimePayrollService
{
    public decimal CalculateTotalPayWithOvertime(
        OvertimeEligibleEmployee employee, int regularHours, int overtimeHours) =>
        employee.CalculatePay(regularHours) + employee.CalculateOvertime(overtimeHours);
}

// Uso type-safe — el compilador previene llamar overtime en un Contractor
var fullTime = new FullTimeEmployee { Name = "Alice", HourlyRate = 50m };
var contractor = new Contractor { Name = "Bob", HourlyRate = 75m };

var payroll = new PayrollService();
Console.WriteLine(payroll.CalculatePay(fullTime, 40));     // OK
Console.WriteLine(payroll.CalculatePay(contractor, 40));   // OK

var overtimePayroll = new OvertimePayrollService();
Console.WriteLine(overtimePayroll.CalculateTotalPayWithOvertime(fullTime, 40, 5));   // OK
// overtimePayroll.CalculateTotalPayWithOvertime(contractor, 40, 5);  // COMPILE ERROR — Contractor no es OvertimeEligibleEmployee
```

**Puntos clave:**
- La solucion mueve la restriccion del *runtime* al *compile time*: el compilador previene llamar `CalculateOvertime` en un `Contractor`.
- La jerarquia refleja la realidad del negocio: no todos los empleados tienen overtime, solo un subconjunto.
- `OvertimeEligibleEmployee` es un tipo de contrato mas especifico — los consumidores que necesitan overtime lo piden explicitamente.
- Cuando un subtipo necesita lanzar una excepcion en un metodo heredado, esa es la senal de que la jerarquia esta mal disenada.

</details>

### Cuando va demasiado lejos

LSP dice que los subtipos no deben violar el contrato. Pero hay casos donde un metodo con implementacion "no-op" es perfectamente valido.

```csharp
// IDisposable con Dispose() vacio — NO viola LSP
// El contrato de IDisposable NO dice que siempre debe liberar recursos no administrados.
// Dice que el consumidor PUEDE llamar Dispose() de forma segura.
public class InMemoryCache<T> : IDisposable
{
    private readonly Dictionary<int, T> _cache = [];

    public T? Get(int id) => _cache.TryGetValue(id, out var item) ? item : default;

    // No-op Dispose — completamente valido porque:
    // 1. No hay recursos no administrados que liberar
    // 2. El consumidor puede llamarlo sin sorpresas
    // 3. El contrato de IDisposable se cumple
    public void Dispose() { }
}

// El patron using funciona correctamente:
using var cache = new InMemoryCache<Product>();
// ... usar cache ...
// Dispose() se llama — sin excepcion, sin sorpresa
```

La diferencia entre una violacion de LSP y una implementacion vacia valida: la implementacion vacia *cumple el contrato* (el consumidor puede llamarla sin error), la violacion con `NotImplementedException` *rompe el contrato* sorprendiendo al consumidor.

> **Respuesta de entrevista:** "LSP se viola cuando un subtipo no puede cumplir el contrato del tipo base — la senal clasica es NotImplementedException en un metodo heredado. La solucion es segregar interfaces o redisenar la jerarquia para que cada tipo solo prometa lo que puede cumplir. Un Dispose() vacio no viola LSP; un Add() que lanza NotImplementedException si lo viola."

---

## ISP — Interface Segregation Principle

**Los clientes no deben depender de interfaces que no usan.** Una interfaz "gorda" que agrupa metodos no relacionados fuerza a los implementadores a firmar un contrato que no pueden cumplir completamente, y a los consumidores a depender de cosas que no necesitan.

### Aplicacion correcta

Un servicio de ordenes separado en interfaces de lectura y escritura para dos tipos de consumidores diferentes:

**Antes (viola ISP — una interfaz para todos):**

```csharp
// Una sola interfaz que todos deben implementar completa
public interface IOrderService
{
    // Operaciones de lectura (necesitadas por el dashboard de reportes)
    Order GetById(int id);
    IEnumerable<Order> GetByCustomer(int customerId);
    IEnumerable<Order> GetByDateRange(DateTime from, DateTime to);
    decimal GetTotalRevenue(DateTime from, DateTime to);

    // Operaciones de escritura (necesitadas por el panel admin)
    Order CreateOrder(CreateOrderRequest request);
    void UpdateStatus(int orderId, OrderStatus status);
    void CancelOrder(int orderId, string reason);
    void ProcessRefund(int orderId, decimal amount);
}

// Dashboard de solo lectura — obligado a "implementar" operaciones de escritura que no usa
public class ReportingDashboard(IOrderService orderService)
{
    public decimal GetMonthlyRevenue() =>
        orderService.GetTotalRevenue(DateTime.Today.AddMonths(-1), DateTime.Today);

    // Tiene acceso a CreateOrder, CancelOrder, ProcessRefund — que no deberia poder llamar
}
```

**Despues (ISP aplicado — interfaces por rol de consumidor):**

```csharp
// Interfaz para consumidores de solo lectura
public interface IOrderReader
{
    Order GetById(int id);
    IEnumerable<Order> GetByCustomer(int customerId);
    IEnumerable<Order> GetByDateRange(DateTime from, DateTime to);
    decimal GetTotalRevenue(DateTime from, DateTime to);
}

// Interfaz para consumidores que modifican estado
public interface IOrderWriter
{
    Order CreateOrder(CreateOrderRequest request);
    void UpdateStatus(int orderId, OrderStatus status);
    void CancelOrder(int orderId, string reason);
    void ProcessRefund(int orderId, decimal amount);
}

// La implementacion concreta implementa ambas
public class OrderService : IOrderReader, IOrderWriter
{
    public Order GetById(int id) => /* query DB */;
    public IEnumerable<Order> GetByCustomer(int customerId) => /* query DB */;
    // ... todos los metodos implementados
}

// Dashboard solo depende de lo que usa — ISP satisfecho
public class ReportingDashboard(IOrderReader orderReader)
{
    public decimal GetMonthlyRevenue() =>
        orderReader.GetTotalRevenue(DateTime.Today.AddMonths(-1), DateTime.Today);
    // No tiene acceso a operaciones de escritura
}

// Panel admin usa la interfaz de escritura
public class AdminPanel(IOrderWriter orderWriter)
{
    public void CancelOrder(int id, string reason) => orderWriter.CancelOrder(id, reason);
}
```

### Challenge 4: Segregar IInventoryManager

La siguiente interfaz `IInventoryManager` mezcla operaciones que distintos tipos de consumidores necesitan. Un trabajador de almacen necesita recibir mercancia y moverla entre ubicaciones. Un sistema de reportes solo necesita consultar niveles y generar alertas. Un sistema de ventas solo necesita reservar y reducir stock. Segrega la interfaz en 2-3 interfaces cohesivas.

```csharp
// Interfaz "gorda" — todos los consumidores deben depender de todo
public interface IInventoryManager
{
    // Operaciones de consulta
    int GetStockLevel(int productId);
    IEnumerable<Product> GetLowStockProducts(int threshold);
    decimal GetInventoryValue();
    IEnumerable<StockMovement> GetMovementHistory(int productId, int days);

    // Operaciones de recepcion (almacen)
    void ReceiveShipment(int productId, int quantity, string supplierReference);
    void MoveToLocation(int productId, string fromLocation, string toLocation);
    void AdjustStock(int productId, int adjustment, string reason);

    // Operaciones de ventas
    bool ReserveStock(int productId, int quantity, string orderId);
    void ReleaseReservation(string orderId);
    void ReduceStock(int productId, int quantity);
}
```

<details>
<summary>Ver solucion</summary>

```csharp
// Interfaz 1: Consulta y reportes — para dashboards y sistemas de monitoreo
public interface IInventoryReader
{
    int GetStockLevel(int productId);
    IEnumerable<Product> GetLowStockProducts(int threshold);
    decimal GetInventoryValue();
    IEnumerable<StockMovement> GetMovementHistory(int productId, int days);
}

// Interfaz 2: Operaciones de almacen — para workers de recepcion y ajuste
public interface IInventoryReceiver
{
    void ReceiveShipment(int productId, int quantity, string supplierReference);
    void MoveToLocation(int productId, string fromLocation, string toLocation);
    void AdjustStock(int productId, int adjustment, string reason);
}

// Interfaz 3: Operaciones de ventas — para el sistema de pedidos
public interface IInventorySalesManager
{
    bool ReserveStock(int productId, int quantity, string orderId);
    void ReleaseReservation(string orderId);
    void ReduceStock(int productId, int quantity);
}

// La implementacion concreta implementa las tres interfaces
public class InventoryService : IInventoryReader, IInventoryReceiver, IInventorySalesManager
{
    public int GetStockLevel(int productId) => /* query DB */;
    public IEnumerable<Product> GetLowStockProducts(int threshold) => /* query DB */;
    public decimal GetInventoryValue() => /* calcular */;
    public IEnumerable<StockMovement> GetMovementHistory(int productId, int days) => /* query DB */;

    public void ReceiveShipment(int productId, int quantity, string supplierReference)
        => /* insert movement record */;
    public void MoveToLocation(int productId, string fromLocation, string toLocation)
        => /* update location */;
    public void AdjustStock(int productId, int adjustment, string reason)
        => /* insert adjustment */;

    public bool ReserveStock(int productId, int quantity, string orderId)
        => /* reserve and return success */;
    public void ReleaseReservation(string orderId) => /* release */;
    public void ReduceStock(int productId, int quantity) => /* decrement */;
}

// Cada consumidor depende solo de su interfaz
public class InventoryReportService(IInventoryReader reader)
{
    public void AlertLowStock(int threshold)
    {
        var products = reader.GetLowStockProducts(threshold);
        foreach (var p in products)
            Console.WriteLine($"LOW STOCK: {p.Name} - Level: {reader.GetStockLevel(p.Id)}");
    }
}

public class WarehouseApp(IInventoryReceiver receiver)
{
    public void ProcessShipment(int productId, int qty, string reference) =>
        receiver.ReceiveShipment(productId, qty, reference);
}

public class OrderFulfillmentService(IInventorySalesManager salesManager)
{
    public bool TryFulfillOrder(int productId, int qty, string orderId) =>
        salesManager.ReserveStock(productId, qty, orderId);
}
```

**Puntos clave:**
- La segregacion esta guiada por los *consumidores*, no por las operaciones: se crean tres interfaces porque hay tres tipos de actores con necesidades distintas.
- `InventoryService` implementa las tres interfaces — la segregacion no fragmenta la implementacion, solo limita lo que cada consumidor puede ver.
- El sistema de reportes (`InventoryReportService`) no tiene acceso a `ReduceStock` — si alguien intenta llamarlo, el compilador lo previene.
- En ASP.NET Core, puedes registrar `InventoryService` como implementacion de las tres interfaces en el contenedor DI.

</details>

### Cuando va demasiado lejos

El anti-pattern de ISP es crear interfaces de un solo metodo que *siempre se usan juntas*, resultando en mas complejidad que una sola interfaz cohesiva.

```csharp
// OVER-GRANULAR — cuatro interfaces de un metodo que SIEMPRE se inyectan juntas
public interface ICanRead<T>   { T GetById(int id); }
public interface ICanCreate<T> { void Add(T entity); }
public interface ICanUpdate<T> { void Update(T entity); }
public interface ICanDelete    { void Delete(int id); }

// Ningun consumidor usa solo uno — todos necesitan los cuatro
public class ProductService(
    ICanRead<Product> reader,
    ICanCreate<Product> creator,
    ICanUpdate<Product> updater,
    ICanDelete deleter)  // Cuatro inyecciones donde una bastaba
{
    public void Transfer(int fromId, Product newProduct)
    {
        var existing = reader.GetById(fromId);
        creator.Add(newProduct);
        deleter.Delete(fromId);
    }
}

// Una interfaz cohesiva es mas clara cuando los consumidores siempre usan todo
public interface IProductRepository
{
    Product GetById(int id);
    void Add(Product entity);
    void Update(Product entity);
    void Delete(int id);
}
// ProductService(IProductRepository repo) — mucho mas limpio
```

> **Respuesta de entrevista:** "No aplicaria ISP si todos los consumidores siempre usan todos los metodos. Interfaces de un solo metodo que siempre se inyectan juntas crean ceremony sin beneficio — una interfaz cohesiva es preferible. ISP tiene valor cuando diferentes consumidores necesitan diferentes subconjuntos de operaciones."

---

## DIP — Dependency Inversion Principle

**Los modulos de alto nivel no deben depender de modulos de bajo nivel; ambos deben depender de abstracciones.** Y ademas: **las abstracciones no deben depender de detalles; los detalles deben depender de abstracciones**. En la practica esto significa que la logica de negocio (alto nivel) no debe conocer directamente la base de datos, el SMTP server o cualquier infraestructura especifica (bajo nivel). Ambos dependen de una interfaz.

### Aplicacion correcta

Un generador de reportes que depende de una abstraccion de fuente de datos, no de SQL Server directamente. Se usan primary constructors de C# 12 para DI conciso:

**Antes (viola DIP — modulo de alto nivel depende del detalle de bajo nivel):**

```csharp
public class ReportGenerator
{
    // Dependencia directa del modulo de bajo nivel (SqlServer)
    private readonly SqlConnection _connection = new("Server=.;Database=Reports;...");

    public SalesReport GenerateMonthlySales(int month, int year)
    {
        var data = _connection.Query<SalesData>(
            "SELECT ProductId, SUM(Amount) FROM Sales WHERE MONTH(Date) = @month ...",
            new { month, year });

        return new SalesReport(data.ToList());
    }
}
// Problema: no se puede testear sin una base de datos real.
// Problema: cambiar a CosmosDB requiere modificar ReportGenerator.
```

**Despues (DIP aplicado — ambos dependen de la abstraccion):**

```csharp
// Abstraccion — define el contrato sin detalles de implementacion
public interface IDataSource
{
    IEnumerable<SalesData> GetMonthlySales(int month, int year);
}

// Detalle de bajo nivel — implementacion SQL
public class SqlDataSource(string connectionString) : IDataSource
{
    public IEnumerable<SalesData> GetMonthlySales(int month, int year)
    {
        using var connection = new SqlConnection(connectionString);
        return connection.Query<SalesData>(
            "SELECT ProductId, SUM(Amount) as Total FROM Sales WHERE MONTH(Date) = @month AND YEAR(Date) = @year GROUP BY ProductId",
            new { month, year });
    }
}

// Modulo de alto nivel — depende de la abstraccion via primary constructor (C# 12)
public class ReportGenerator(IDataSource dataSource)
{
    public SalesReport GenerateMonthlySales(int month, int year)
    {
        var data = dataSource.GetMonthlySales(month, year);
        return new SalesReport(data.ToList());
    }
}

// En tests — implementacion en memoria sin SQL Server
public class InMemoryDataSource(IEnumerable<SalesData> data) : IDataSource
{
    public IEnumerable<SalesData> GetMonthlySales(int month, int year) =>
        data.Where(d => d.Date.Month == month && d.Date.Year == year);
}

// Registro en ASP.NET Core
builder.Services.AddScoped<IDataSource, SqlDataSource>();
builder.Services.AddScoped<ReportGenerator>();
```

### Challenge 5: Inyectar dependencias en OrderService

El siguiente `OrderService` viola DIP: instancia directamente `SqlOrderRepository` y `SmtpEmailSender`. Refactorizalo para que dependa de abstracciones inyectadas via constructor (usa primary constructors de C# 12).

```csharp
public class OrderService
{
    // Dependencias hardcodeadas — no se pueden sustituir en tests
    private readonly SqlOrderRepository _repository = new SqlOrderRepository("Server=.;Database=Orders;...");
    private readonly SmtpEmailSender _emailSender = new SmtpEmailSender("smtp.company.com", 587);

    public Order CreateOrder(CreateOrderRequest request)
    {
        var order = new Order
        {
            CustomerId = request.CustomerId,
            Items = request.Items,
            Total = request.Items.Sum(i => i.Price * i.Quantity),
            Status = OrderStatus.Pending
        };

        _repository.Save(order);
        _emailSender.SendOrderConfirmation(order.CustomerEmail, order.Id, order.Total);

        return order;
    }

    public Order? GetOrder(int id) => _repository.GetById(id);

    public void UpdateStatus(int orderId, OrderStatus newStatus)
    {
        var order = _repository.GetById(orderId)
            ?? throw new KeyNotFoundException($"Order {orderId} not found");
        order.Status = newStatus;
        _repository.Save(order);
    }
}
```

<details>
<summary>Ver solucion</summary>

```csharp
// Abstraccion de persistencia
public interface IOrderRepository
{
    void Save(Order order);
    Order? GetById(int id);
}

// Abstraccion de notificaciones
public interface INotificationService
{
    void SendOrderConfirmation(string email, int orderId, decimal total);
}

// Implementaciones concretas (detalles de bajo nivel)
public class SqlOrderRepository(string connectionString) : IOrderRepository
{
    public void Save(Order order)
    {
        using var db = new SqlConnection(connectionString);
        // INSERT OR UPDATE segun si order.Id es 0
        db.Execute("...", order);
    }

    public Order? GetById(int id)
    {
        using var db = new SqlConnection(connectionString);
        return db.QueryFirstOrDefault<Order>("SELECT * FROM Orders WHERE Id = @id", new { id });
    }
}

public class SmtpNotificationService(string smtpServer, int port) : INotificationService
{
    public void SendOrderConfirmation(string email, int orderId, decimal total)
    {
        using var smtp = new SmtpClient(smtpServer, port);
        smtp.Send("orders@company.com", email,
            $"Order #{orderId} confirmed", $"Your total: {total:C}");
    }
}

// Modulo de alto nivel — solo conoce abstracciones, usa primary constructor (C# 12)
public class OrderService(IOrderRepository repository, INotificationService notificationService)
{
    public Order CreateOrder(CreateOrderRequest request)
    {
        var order = new Order
        {
            CustomerId = request.CustomerId,
            Items = request.Items,
            Total = request.Items.Sum(i => i.Price * i.Quantity),
            Status = OrderStatus.Pending
        };

        repository.Save(order);
        notificationService.SendOrderConfirmation(order.CustomerEmail, order.Id, order.Total);

        return order;
    }

    public Order? GetOrder(int id) => repository.GetById(id);

    public void UpdateStatus(int orderId, OrderStatus newStatus)
    {
        var order = repository.GetById(orderId)
            ?? throw new KeyNotFoundException($"Order {orderId} not found");
        order.Status = newStatus;
        repository.Save(order);
    }
}

// Registro en ASP.NET Core — Program.cs
builder.Services.AddScoped<IOrderRepository>(sp =>
    new SqlOrderRepository(builder.Configuration.GetConnectionString("Orders")!));
builder.Services.AddScoped<INotificationService>(sp =>
    new SmtpNotificationService(
        builder.Configuration["Smtp:Server"]!,
        builder.Configuration.GetValue<int>("Smtp:Port")));
builder.Services.AddScoped<OrderService>();

// Test con implementaciones en memoria
public class InMemoryOrderRepository : IOrderRepository
{
    private readonly List<Order> _orders = [];
    private int _nextId = 1;

    public void Save(Order order)
    {
        if (order.Id == 0) order.Id = _nextId++;
        var existing = _orders.FindIndex(o => o.Id == order.Id);
        if (existing >= 0) _orders[existing] = order;
        else _orders.Add(order);
    }

    public Order? GetById(int id) => _orders.FirstOrDefault(o => o.Id == id);
}

public class FakeNotificationService : INotificationService
{
    public List<string> SentEmails { get; } = [];
    public void SendOrderConfirmation(string email, int orderId, decimal total) =>
        SentEmails.Add(email);
}
```

**Puntos clave:**
- El primary constructor `OrderService(IOrderRepository repository, INotificationService notificationService)` es sintaxis C# 12 — equivalente a declarar campos privados readonly e inicializarlos en el constructor, pero mas conciso.
- `OrderService` ahora es completamente testeable sin SQL Server ni SMTP: basta con pasar `InMemoryOrderRepository` y `FakeNotificationService`.
- Las interfaces `IOrderRepository` e `INotificationService` son abstracciones de negocio — no fugan detalles de SQL o SMTP.
- En ASP.NET Core, el contenedor de DI elimina el overhead de construir los objetos manualmente.

</details>

### Cuando va demasiado lejos

DIP en proyectos pequenos puede triplicar la complejidad para cero beneficio real:

```csharp
// Herramienta de consola de 3 clases — agregar DI es over-engineering
// Antes (simple, correcto para su contexto):
var parser = new CsvParser();
var report = new ReportBuilder();
var result = report.Build(parser.Parse("data.csv"));
Console.WriteLine(result);

// Despues de aplicar DIP "por las dudas":
public interface ICsvParser { IEnumerable<Record> Parse(string path); }
public interface IReportBuilder { string Build(IEnumerable<Record> data); }

// Registro en contenedor...
var services = new ServiceCollection();
services.AddScoped<ICsvParser, CsvParser>();
services.AddScoped<IReportBuilder, ReportBuilder>();
var provider = services.BuildServiceProvider();

var parser = provider.GetRequiredService<ICsvParser>();
var report = provider.GetRequiredService<IReportBuilder>();
var result = report.Build(parser.Parse("data.csv"));
Console.WriteLine(result);

// Mismo resultado. Tres veces el codigo. Sin un test que se beneficie de ello.
```

La diferencia clave: en ASP.NET Core el contenedor DI ya existe y el overhead es cerca de cero — registrar un servicio es una linea. En una herramienta standalone, el overhead ES significativo en relacion al beneficio.

> **Respuesta de entrevista:** "No aplicaria DIP en proyectos pequenos donde las abstracciones no tienen consumidores alternativos y el costo de la indirection supera el beneficio. En un script de consola simple, `new CsvParser()` es perfecto. Cuando el proyecto crece o necesita ser testeable, refactorizar a DI es directo — no hay que adivinar el futuro."

---

## Resumen: Puntos Clave para la Entrevista

1. **Que significa SRP en terminos practicos?** Una clase debe tener una sola razon para cambiar. En la practica: si describes la clase con "y" (valida Y cobra Y notifica), tiene demasiadas responsabilidades.

2. **Como aplicas OCP sin over-engineering?** Usar polimorfismo para los puntos de variacion que ya existen o que tienen historia real de cambio. No abstraer especulativamente — YAGNI aplica. En .NET el Strategy pattern y las interfaces son el mecanismo principal.

3. **Cual es la senal mas comun de violacion de LSP?** `NotImplementedException` en un metodo heredado. Si un subtipo no puede cumplir el contrato del tipo base, la jerarquia esta mal disenada — segregar interfaces o redisenar.

4. **Cuando es ISP contraproducente?** Cuando las interfaces segregadas siempre se consumen juntas. Cuatro interfaces de un metodo que se inyectan juntas en el mismo constructor son peores que una interfaz cohesiva con cuatro metodos.

5. **Necesitas siempre DI en proyectos C#?** No. En herramientas pequenas sin necesidad de testabilidad, `new` directo es correcto. En ASP.NET Core, el contenedor ya existe y el overhead es cero — usa DIP por defecto. La pregunta es si la abstraccion tiene valor real.

6. **Cual principio SOLID se viola mas frecuentemente y por que?** SRP es el mas violado en codebases reales. Las clases crecen organicamente absorbiendo responsabilidades relacionadas hasta convertirse en "god classes". La senal temprana es un nombre vago (`Manager`, `Handler`, `Helper`, `Service`).

7. **Como decides el nivel correcto de abstraccion?** Pregunta: "Tiene este punto de variacion mas de un consumidor potencial o historia real de cambio?" Si si → abstrae. Si no → YAGNI. Los principios SOLID son guias para codigo con expectativa de crecimiento, no para scripts utilitarios.
