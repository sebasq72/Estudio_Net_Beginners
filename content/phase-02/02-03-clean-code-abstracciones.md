# Clean Code y Abstracciones: Clase Abstracta vs Interfaz

**Tiempo estimado:** ~1.5 horas
**Prerequisitos:** Conocimiento de OOP y SOLID (Phase 2, Plans 01-02)
**Target:** .NET 10 LTS (todos los snippets compilan en .NET 10)

---

## Clean Code: Nombres Descriptivos

El codigo se lee 10 veces mas de lo que se escribe. Los nombres son la primera capa de documentacion — si un nombre no revela la intencion del codigo, cualquier lector (incluyendo tu yo futuro) necesitara descifrar el contexto antes de poder entender lo que hace.

### Convenciones de nomenclatura en C# (Microsoft)

| Elemento | Convencion | Ejemplo |
|----------|------------|---------|
| Clases, metodos, propiedades publicas | `PascalCase` | `OrderCalculator`, `CalculateTotal` |
| Variables locales, parametros | `camelCase` | `orderItems`, `shippingRegion` |
| Campos privados | `_camelCase` | `_balance`, `_repository` |
| Constantes | `PascalCase` | `MaxRetryCount`, `DefaultTimeout` |
| Interfaces | `IPascalCase` | `IOrderService`, `IPaymentGateway` |

Un buen nombre hace que el codigo sea auto-documentado. Si necesitas un comentario para explicar que hace una variable o metodo, el nombre probablemente es malo.

### Antes y despues: nombres que revelan intencion

**Antes (code smell — ❌):**

```csharp
public class Proc
{
    public decimal Calc(List<Item> items, string t)
    {
        decimal tot = 0;
        foreach (var i in items)
        {
            if (t == "d")
                tot += i.Price * 1.16m;
            else if (t == "i")
                tot += i.Price * 1.21m;
            else
                tot += i.Price;
        }
        if (tot > 1000)
            tot *= 0.95m;
        return tot;
    }
}

public class Mgr
{
    private List<Emp> lst = new();
    public void Handle(Emp e) => lst.Add(e);
    public List<Emp> GetAll() => lst;
}
```

**Despues (clean code — ✓):**

```csharp
public enum ShippingRegion { Domestic, International, Local }

public class OrderCalculator
{
    private const decimal DomesticTaxRate = 0.16m;
    private const decimal InternationalTaxRate = 0.21m;
    private const decimal BulkDiscountThreshold = 1000m;
    private const decimal BulkDiscountRate = 0.05m;

    public decimal CalculateTotal(List<OrderItem> items, ShippingRegion region)
    {
        decimal subtotal = items.Sum(item => ApplyTax(item.Price, region));
        return ApplyBulkDiscount(subtotal);
    }

    private decimal ApplyTax(decimal price, ShippingRegion region) => region switch
    {
        ShippingRegion.Domestic => price * (1 + DomesticTaxRate),
        ShippingRegion.International => price * (1 + InternationalTaxRate),
        _ => price
    };

    private decimal ApplyBulkDiscount(decimal total) =>
        total > BulkDiscountThreshold ? total * (1 - BulkDiscountRate) : total;
}

public class EmployeeScheduleManager
{
    private readonly List<Employee> _scheduledEmployees = new();

    public void ScheduleEmployee(Employee employee) 
        => _scheduledEmployees.Add(employee);

    public IReadOnlyList<Employee> GetScheduledEmployees() 
        => _scheduledEmployees.AsReadOnly();
}
```

Las mejoras clave en el refactoring:
- `Proc` → `OrderCalculator`: el nombre revela el dominio y proposito
- `Calc(items, "d")` → `CalculateTotal(items, ShippingRegion.Domestic)`: un enum elimina magic strings
- `Mgr` → `EmployeeScheduleManager`: contexto completo sin abreviaciones
- `lst`, `tot`, `e`, `i` → nombres que describen lo que representan

### Challenge 1: Renombra para revelar intencion

El siguiente codigo pertenece a un sistema de gestion de turnos para empleados. Contiene **8 problemas de nomenclatura**: nombres crípticos, magic strings, abreviaciones sin contexto y comentarios que compensan nombres malos. Tu tarea: renombra todo para que el codigo sea auto-documentado.

```csharp
public class Sched
{
    private List<object[]> data = new();
    private Dictionary<string, int> cnt = new();

    // Process employee shift
    public bool DoStuff(string empId, string d, string st, string et)
    {
        // Check if valid type
        if (d != "M" && d != "E" && d != "N")
            return false;

        // Check hours
        if (st == et)
            return false;

        var entry = new object[] { empId, d, st, et };
        data.Add(entry);

        if (!cnt.ContainsKey(empId))
            cnt[empId] = 0;
        cnt[empId]++;

        return true;
    }

    // Get count for employee
    public int GetN(string id) => cnt.ContainsKey(id) ? cnt[id] : 0;

    // Get all data
    public List<object[]> GetAll() => data;
}
```

<details>
<summary>Ver solucion</summary>

```csharp
public enum ShiftType { Morning, Evening, Night }

public record EmployeeShift(string EmployeeId, ShiftType Type, TimeOnly StartTime, TimeOnly EndTime);

public class EmployeeScheduler
{
    private readonly List<EmployeeShift> _scheduledShifts = new();
    private readonly Dictionary<string, int> _shiftCountByEmployee = new();

    public bool ScheduleShift(string employeeId, ShiftType shiftType, TimeOnly startTime, TimeOnly endTime)
    {
        if (startTime == endTime)
            return false;

        var shift = new EmployeeShift(employeeId, shiftType, startTime, endTime);
        _scheduledShifts.Add(shift);

        _shiftCountByEmployee.TryGetValue(employeeId, out int currentCount);
        _shiftCountByEmployee[employeeId] = currentCount + 1;

        return true;
    }

    public int GetShiftCountForEmployee(string employeeId) 
        => _shiftCountByEmployee.GetValueOrDefault(employeeId, 0);

    public IReadOnlyList<EmployeeShift> GetAllScheduledShifts() 
        => _scheduledShifts.AsReadOnly();
}
```

**Puntos clave:**
- `ShiftType` enum reemplaza los magic strings `"M"`, `"E"`, `"N"` — el compilador ahora previene valores invalidos y el IDE ofrece autocompletado
- `EmployeeShift` record hace explicitas las propiedades de un turno; `object[]` ocultaba la estructura de datos
- El prefijo `_` en los campos privados (`_scheduledShifts`, `_shiftCountByEmployee`) sigue la convencion de C# y distingue de variables locales
- `GetValueOrDefault` es mas idiomatico que el patron `ContainsKey` + acceso de dos pasos
- Los comentarios desaparecieron porque ya no son necesarios — los nombres explican todo

</details>

---

## Clean Code: Metodos Pequenos y Guard Clauses

Un metodo deberia hacer **una sola cosa**. Cuando un metodo hace multiples cosas — validar, calcular, persistir, notificar — se convierte en un SRP violation a nivel de metodo. Los indicadores de que un metodo hace demasiado: mas de 20 lineas, multiples niveles de anidamiento, o que su nombre use "y" (`ValidateAndSave`, `ProcessAndNotify`).

### Guard clauses: eliminar anidamiento con early returns

Los **guard clauses** son validaciones al inicio del metodo que retornan (o lanzan) inmediatamente si las precondiciones no se cumplen. Eliminan el anidamiento piramidal y hacen el *happy path* obvio.

> **Nota pragmatica:** "Prefer small methods" NO significa que cada metodo debe tener 5 lineas. Extrae un metodo cuando mejora la legibilidad — no mecánicamente por tamano. Si un metodo privado se llama solo una vez y hace el flujo mas dificil de seguir, probablemente no vale la pena extraerlo.

### Antes y despues: guard clauses y extraccion de metodos

**Antes (code smell — ❌): piramide de anidamiento**

```csharp
public string ProcessPayment(Order order, CreditCard card, Customer customer)
{
    if (order != null)
    {
        if (card != null)
        {
            if (customer != null)
            {
                if (order.Total > 0)
                {
                    if (card.ExpiryDate > DateTime.Today)
                    {
                        if (customer.CreditLimit >= order.Total)
                        {
                            // El "happy path" esta enterrado en el nivel 7 de anidamiento
                            decimal fee = order.Total * 0.02m;
                            decimal finalAmount = order.Total + fee;
                            // ... 20 lineas mas de logica de pago
                            customer.CreditLimit -= finalAmount;
                            return $"Payment of {finalAmount:C} processed for order {order.Id}";
                        }
                        else
                            return "Insufficient credit limit";
                    }
                    else
                        return "Card expired";
                }
                else
                    return "Order total must be greater than zero";
            }
            else
                return "Customer is required";
        }
        else
            return "Credit card is required";
    }
    return "Order is required";
}
```

**Despues (clean code — ✓): guard clauses + metodos extraidos**

```csharp
public string ProcessPayment(Order order, CreditCard card, Customer customer)
{
    // Guard clauses — validaciones arriba, happy path abajo
    if (order is null) return "Order is required";
    if (card is null) return "Credit card is required";
    if (customer is null) return "Customer is required";
    if (order.Total <= 0) return "Order total must be greater than zero";
    if (card.ExpiryDate <= DateTime.Today) return "Card expired";
    if (customer.CreditLimit < order.Total) return "Insufficient credit limit";

    decimal finalAmount = CalculateFinalAmount(order.Total);
    DeductFromCreditLimit(customer, finalAmount);

    return $"Payment of {finalAmount:C} processed for order {order.Id}";
}

private decimal CalculateFinalAmount(decimal orderTotal)
{
    const decimal ProcessingFeeRate = 0.02m;
    return orderTotal * (1 + ProcessingFeeRate);
}

private void DeductFromCreditLimit(Customer customer, decimal amount)
{
    customer.CreditLimit -= amount;
}
```

### Challenge 2: Refactoriza este metodo

El siguiente metodo `ProcessEmployeeTimeOff` hace demasiadas cosas y tiene 4 niveles de anidamiento. Tu tarea: aplica guard clauses para aplanar el metodo y extrae 3-4 metodos privados con nombres descriptivos.

```csharp
public class TimeOffProcessor
{
    private readonly List<TimeOffRequest> _approvedRequests = new();
    private readonly Dictionary<string, int> _usedDays = new();

    public string ProcessEmployeeTimeOff(string employeeId, DateTime startDate, 
        DateTime endDate, string reason)
    {
        if (employeeId != null && employeeId.Length > 0)
        {
            if (startDate < endDate)
            {
                if (reason != null && reason.Trim().Length > 0)
                {
                    int requestedDays = (int)(endDate - startDate).TotalDays;
                    if (requestedDays > 0 && requestedDays <= 30)
                    {
                        int usedSoFar = 0;
                        if (_usedDays.ContainsKey(employeeId))
                            usedSoFar = _usedDays[employeeId];

                        int remaining = 20 - usedSoFar;  // 20 days per year
                        if (requestedDays <= remaining)
                        {
                            var request = new TimeOffRequest(employeeId, startDate, endDate, reason);
                            _approvedRequests.Add(request);

                            if (!_usedDays.ContainsKey(employeeId))
                                _usedDays[employeeId] = 0;
                            _usedDays[employeeId] += requestedDays;

                            return $"Time off approved: {requestedDays} days from {startDate:d} to {endDate:d}";
                        }
                        else
                            return $"Insufficient balance. Requested: {requestedDays}, Remaining: {remaining}";
                    }
                    else
                        return "Request must be between 1 and 30 days";
                }
                else
                    return "Reason is required";
            }
            else
                return "End date must be after start date";
        }
        return "Employee ID is required";
    }
}

public record TimeOffRequest(string EmployeeId, DateTime StartDate, DateTime EndDate, string Reason);
```

<details>
<summary>Ver solucion</summary>

```csharp
public class TimeOffProcessor
{
    private const int MaxAnnualTimeOffDays = 20;
    private const int MaxSingleRequestDays = 30;

    private readonly List<TimeOffRequest> _approvedRequests = new();
    private readonly Dictionary<string, int> _usedDays = new();

    public string ProcessEmployeeTimeOff(string employeeId, DateTime startDate, 
        DateTime endDate, string reason)
    {
        // Guard clauses — all preconditions up front
        if (string.IsNullOrWhiteSpace(employeeId)) return "Employee ID is required";
        if (endDate <= startDate) return "End date must be after start date";
        if (string.IsNullOrWhiteSpace(reason)) return "Reason is required";

        int requestedDays = CalculateRequestedDays(startDate, endDate);
        if (requestedDays is < 1 or > MaxSingleRequestDays) 
            return "Request must be between 1 and 30 days";

        int remainingDays = GetRemainingDaysForEmployee(employeeId);
        if (requestedDays > remainingDays)
            return $"Insufficient balance. Requested: {requestedDays}, Remaining: {remainingDays}";

        ApproveAndRecordRequest(employeeId, startDate, endDate, reason, requestedDays);

        return $"Time off approved: {requestedDays} days from {startDate:d} to {endDate:d}";
    }

    private static int CalculateRequestedDays(DateTime startDate, DateTime endDate)
        => (int)(endDate - startDate).TotalDays;

    private int GetRemainingDaysForEmployee(string employeeId)
    {
        int usedDays = _usedDays.GetValueOrDefault(employeeId, 0);
        return MaxAnnualTimeOffDays - usedDays;
    }

    private void ApproveAndRecordRequest(string employeeId, DateTime startDate, 
        DateTime endDate, string reason, int requestedDays)
    {
        var request = new TimeOffRequest(employeeId, startDate, endDate, reason);
        _approvedRequests.Add(request);
        _usedDays[employeeId] = _usedDays.GetValueOrDefault(employeeId, 0) + requestedDays;
    }
}
```

**Puntos clave:**
- Las guard clauses eliminaron 6 niveles de anidamiento — el happy path ahora es lineal y legible
- `CalculateRequestedDays`, `GetRemainingDaysForEmployee`, `ApproveAndRecordRequest` revelan la estructura del algoritmo solo con leer el metodo principal
- El magic number `20` se convirtio en `MaxAnnualTimeOffDays` — si la regla cambia, se cambia en un lugar
- `string.IsNullOrWhiteSpace` en lugar de `!= null && Length > 0` es la forma idiomatica de validar strings en C#
- `GetValueOrDefault` simplifica el patron repetitivo de `ContainsKey` + acceso

</details>

---

## Clean Code: DRY (Don't Repeat Yourself)

DRY significa que cada pieza de **conocimiento** del sistema debe tener una representacion unica. Duplication = maintenance burden: cuando una regla de negocio cambia, debes encontrar y actualizar todos los lugares donde esta duplicada — y si fallas uno, introduces un bug sutil.

**Pero cuidado: DRY aplica a conocimiento, no a codigo que solo parece similar.**

### Cuando NO aplicar DRY: la trampa de la abstraccion prematura

Dos metodos que se ven identicos hoy pueden representar reglas de negocio distintas:

```csharp
// Estos dos metodos se ven IGUALES ahora — pero representan conocimiento DIFERENTE:

public decimal CalculateSeasonalDiscount(decimal price, int quantityPurchased)
{
    // Descuento estacional: 10% si compras 5+ unidades en temporada alta
    if (quantityPurchased >= 5)
        return price * 0.90m;
    return price;
}

public decimal CalculateLoyaltyDiscount(decimal price, int purchaseCount)
{
    // Descuento por lealtad: 10% si el cliente tiene 5+ compras previas
    if (purchaseCount >= 5)
        return price * 0.90m;
    return price;
}

// Si los "merges" en un solo metodo, cuando cambies la regla estacional
// (ej: 15% si compras 3+ en diciembre), afectas accidentalmente el descuento de lealtad.
// AHA — Avoid Hasty Abstractions: el codigo PARECE duplicado, pero el CONOCIMIENTO es diferente.
```

### Cuando SÍ aplicar DRY: duplicacion real de conocimiento

```csharp
// ANTES: La misma regla de calculo de IVA copy-pasted en 3 lugares — ❌
public class InvoiceService
{
    public decimal CalculateInvoiceTotal(decimal subtotal)
    {
        decimal tax = subtotal * 0.16m;  // IVA duplicado
        return subtotal + tax;
    }
}

public class QuoteService
{
    public decimal CalculateQuoteTotal(decimal subtotal)
    {
        decimal tax = subtotal * 0.16m;  // IVA duplicado
        return subtotal + tax;
    }
}

public class OrderService
{
    public decimal CalculateOrderTotal(decimal subtotal)
    {
        decimal tax = subtotal * 0.16m;  // IVA duplicado — si cambia a 0.19m, hay que cambiar en 3 lugares
        return subtotal + tax;
    }
}

// DESPUES: La regla de IVA en un solo lugar — ✓
public static class TaxCalculator
{
    private const decimal VatRate = 0.16m;

    public static decimal CalculateWithVat(decimal subtotal) => subtotal * (1 + VatRate);
}

// Todos los servicios usan TaxCalculator.CalculateWithVat(subtotal)
// Si el IVA cambia, se cambia en un solo lugar.
```

> **Regla practica:** Antes de aplicar DRY, preguntate: "Si esta regla de negocio cambia, ¿deberia cambiar AMBOS lugares?" Si la respuesta es NO, probablemente son conocimiento diferente y no deberias fusionarlos.

Este concepto se integrara en el Challenge Integrador de la seccion 6.

---

## Decision Tree: Clase Abstracta vs Interfaz

Elegir entre `abstract class` e `interface` es una de las preguntas mas frecuentes en entrevistas .NET. La respuesta correcta depende del contexto — aqui hay un arbol de decision para llegar rapido a la eleccion correcta:

| Pregunta | Si | No |
|----------|----|----|
| Necesitas compartir estado (fields) entre clases derivadas? | **Abstract class** | Siguiente pregunta |
| Las clases necesitan heredar de otra clase base? | **Interface** (C# no tiene herencia multiple de clases) | Siguiente pregunta |
| Necesitas que tipos no relacionados implementen el contrato? | **Interface** | Siguiente pregunta |
| Necesitas proveer implementacion comun que las derivadas pueden override? | **Abstract class** (o interface con default method si no hay estado) | **Interface** pura |

### Escenarios mas comunes en la practica

- En la mayoria de los casos usaras **interfaces para contratos y DI** — `IOrderService`, `IPaymentGateway`, `IRepository<T>`. El contenedor de DI registra la implementacion, el codigo depende de la interfaz.
- Las **abstract classes brillan cuando hay estado compartido + logica parcial** — el patron Template Method es el ejemplo clasico: defines el algoritmo en la clase base, las subclases implementan los pasos especificos.
- Desde C# 8, la linea se difumina con default interface methods — pero con limitaciones importantes. Ve la seccion siguiente.

### Challenge 3: Abstract class o interface?

Se te presentan dos escenarios. En cada uno, identifica si el diseno actual es correcto o si deberia cambiarse, y justifica tu respuesta usando el decision tree.

**Escenario A:** Una `abstract class` usada como contrato puro sin estado ni implementacion compartida.

```csharp
// Escenario A: Sistema de pagos con multiples gateways
public abstract class IPaymentGateway  // nombre con 'I' sugiere que deberia ser interface
{
    public abstract bool ProcessPayment(decimal amount, string cardToken);
    public abstract bool RefundPayment(string transactionId, decimal amount);
    public abstract PaymentStatus GetPaymentStatus(string transactionId);
    // No hay fields, no hay implementacion compartida
}

public class StripeGateway : IPaymentGateway
{
    public override bool ProcessPayment(decimal amount, string cardToken) => /* Stripe API */;
    public override bool RefundPayment(string transactionId, decimal amount) => /* Stripe API */;
    public override PaymentStatus GetPaymentStatus(string transactionId) => /* Stripe API */;
}

public class PayPalGateway : IPaymentGateway
{
    public override bool ProcessPayment(decimal amount, string cardToken) => /* PayPal API */;
    public override bool RefundPayment(string transactionId, decimal amount) => /* PayPal API */;
    public override PaymentStatus GetPaymentStatus(string transactionId) => /* PayPal API */;
}
```

**Escenario B:** Una `interface` usada donde todas las implementaciones duplican la misma logica de validacion.

```csharp
// Escenario B: Procesadores de reportes
public interface IReportGenerator
{
    Report Generate(ReportParameters parameters);
}

public class SalesReportGenerator : IReportGenerator
{
    public Report Generate(ReportParameters parameters)
    {
        // Validacion duplicada — igual en todos los generadores:
        if (parameters is null) throw new ArgumentNullException(nameof(parameters));
        if (parameters.StartDate >= parameters.EndDate)
            throw new ArgumentException("StartDate must be before EndDate");
        if (string.IsNullOrWhiteSpace(parameters.RequestedBy))
            throw new ArgumentException("RequestedBy is required");
        // ... logica especifica de ventas
    }
}

public class InventoryReportGenerator : IReportGenerator
{
    public Report Generate(ReportParameters parameters)
    {
        // La MISMA validacion copy-pasted:
        if (parameters is null) throw new ArgumentNullException(nameof(parameters));
        if (parameters.StartDate >= parameters.EndDate)
            throw new ArgumentException("StartDate must be before EndDate");
        if (string.IsNullOrWhiteSpace(parameters.RequestedBy))
            throw new ArgumentException("RequestedBy is required");
        // ... logica especifica de inventario
    }
}
```

<details>
<summary>Ver solucion</summary>

**Escenario A — Refactoring: abstract class → interface**

```csharp
// Decision tree: 
// ¿Necesita estado compartido? NO. ¿Herencia de otra clase? NO. 
// ¿Tipos no relacionados implementan el contrato? SI (Stripe y PayPal son empresas distintas).
// → Interface.

public interface IPaymentGateway
{
    bool ProcessPayment(decimal amount, string cardToken);
    bool RefundPayment(string transactionId, decimal amount);
    PaymentStatus GetPaymentStatus(string transactionId);
}

public class StripeGateway : IPaymentGateway
{
    public bool ProcessPayment(decimal amount, string cardToken) => /* Stripe API */;
    public bool RefundPayment(string transactionId, decimal amount) => /* Stripe API */;
    public PaymentStatus GetPaymentStatus(string transactionId) => /* Stripe API */;
}

public class PayPalGateway : IPaymentGateway
{
    public bool ProcessPayment(decimal amount, string cardToken) => /* PayPal API */;
    public bool RefundPayment(string transactionId, decimal amount) => /* PayPal API */;
    public PaymentStatus GetPaymentStatus(string transactionId) => /* PayPal API */;
}
```

**Escenario B — Refactoring: interface → abstract class con logica compartida**

```csharp
// Decision tree:
// ¿Necesita estado compartido? NO (no hay fields).
// ¿Herencia de otra clase? NO.
// ¿Necesita proveer implementacion comun que las derivadas pueden override? SI — la validacion.
// → Abstract class (o interface con default method, pero aqui no hay estado asi que DIMs tambien funcionarian).
// Elegimos abstract class por ser mas explicita sobre la intencion de herencia.

public abstract class ReportGeneratorBase
{
    public Report Generate(ReportParameters parameters)
    {
        ValidateParameters(parameters);  // logica compartida en la clase base
        return GenerateReport(parameters);
    }

    protected abstract Report GenerateReport(ReportParameters parameters);  // Template Method

    private static void ValidateParameters(ReportParameters parameters)
    {
        if (parameters is null) throw new ArgumentNullException(nameof(parameters));
        if (parameters.StartDate >= parameters.EndDate)
            throw new ArgumentException("StartDate must be before EndDate");
        if (string.IsNullOrWhiteSpace(parameters.RequestedBy))
            throw new ArgumentException("RequestedBy is required");
    }
}

public class SalesReportGenerator : ReportGeneratorBase
{
    protected override Report GenerateReport(ReportParameters parameters)
    {
        // Solo logica especifica de ventas — sin validacion duplicada
        return new Report(/* ... */);
    }
}

public class InventoryReportGenerator : ReportGeneratorBase
{
    protected override Report GenerateReport(ReportParameters parameters)
    {
        // Solo logica especifica de inventario — sin validacion duplicada
        return new Report(/* ... */);
    }
}
```

**Puntos clave:**
- En el Escenario A, la `abstract class` nombrada `IPaymentGateway` es una senal de diseno confuso — la convencion de nombrar con `I` implica interfaz, y si no hay estado ni implementacion compartida, debe ser interfaz
- En el Escenario B, la duplicacion de validacion es una violacion DRY que afecta conocimiento real (si cambian las reglas de validacion, hay que actualizar todos los generadores)
- El patron Template Method (`Generate` en la clase base define el algoritmo, `GenerateReport` lo implementan las subclases) es el caso clasico donde abstract class gana sobre interface
- Si en el futuro el `ReportGeneratorBase` necesita inyectar un `ILogger`, puede agregar un field privado — algo imposible en una interface

</details>

---

## C# 8 Default Interface Implementations

Desde C# 8, las interfaces pueden tener implementaciones por defecto (*default interface methods*, DIMs). Esto permite agregar nuevos metodos a una interfaz sin romper las implementaciones existentes — util para evolucionar librerias publicas.

```csharp
public interface INotificationChannel
{
    void Send(string message);  // Contrato obligatorio — implementaciones deben proveerlo
    
    // Default implementation — disponible desde C# 8
    void SendUrgent(string message)
    {
        Send($"[URGENT] {message}");  // Puede llamar otros miembros de la interfaz
        // NO puede acceder: fields, estado de instancia, constructores
    }
}

public class EmailChannel : INotificationChannel
{
    private readonly string _smtpServer;  // La interfaz NO puede tener esto
    
    public EmailChannel(string smtpServer) => _smtpServer = smtpServer;

    public void Send(string message) 
        => Console.WriteLine($"Email via {_smtpServer}: {message}");
    
    // SendUrgent se hereda de la interfaz — pero solo accesible via referencia de interfaz:
}
```

### Limitaciones criticas de los DIMs

1. **No pueden tener fields** — ninguna variable de instancia en la interfaz
2. **No pueden acceder al estado de instancia de la clase implementadora** — solo pueden llamar otros miembros declarados en la misma interfaz
3. **Solo son accesibles via la referencia de interfaz, NO via la referencia de clase**

```csharp
var email = new EmailChannel("smtp.example.com");

// email.SendUrgent("test");  // COMPILE ERROR — SendUrgent no es accesible via EmailChannel

INotificationChannel channel = email;
channel.SendUrgent("test");             // OK — accesible via INotificationChannel
```

> **Nota importante:** Default interface methods NO hacen obsoletas a las abstract classes. Si necesitas estado compartido (fields), abstract class sigue siendo la unica opcion. DIMs son utiles para evolucionar interfaces sin breaking changes en librerias. Para uso interno en aplicaciones, abstract class con logica compartida suele ser mas clara y menos sorprendente.

---

## Challenge Integrador: Refactoring Completo

Este challenge combina todo lo aprendido: clean code (nombres, metodos pequenos, DRY) + decision de abstraccion (abstract vs interface). Es el tipo de ejercicio que aparece en entrevistas de posiciones senior.

**Escenario:** El siguiente `ReportingSystem` fue escrito rapidamente para cumplir un deadline. Contiene multiples problemas que debes identificar y corregir:

> **Nota:** En .NET Core / .NET 5+, usa el paquete `Microsoft.Data.SqlClient` (NuGet),
> no `System.Data.SqlClient` (legacy in-box). La API es compatible; solo cambia el
> namespace y el origen del paquete.

```csharp
using Microsoft.Data.SqlClient;  // NuGet: Microsoft.Data.SqlClient

public class ReportingSystem
{
    private SqlDataReader rdr;  // Dependencia concreta — imposible de testear

    public ReportingSystem(SqlDataReader r)
    {
        rdr = r;
    }

    // Generate report
    public string Gen(string rpt, DateTime dt)
    {
        if (rpt == "s")
        {
            // Sales report
            var lines = new List<string>();
            while (rdr.Read())
            {
                string ln = rdr["ProductName"].ToString().PadRight(30) 
                    + rdr["Amount"].ToString().PadLeft(15) 
                    + rdr["Date"].ToString().PadLeft(15);
                lines.Add(ln);
            }
            string header = "SALES REPORT - " + dt.ToString("yyyy-MM-dd");
            string separator = new string('-', 60);
            return header + "\n" + separator + "\n" + string.Join("\n", lines);
        }
        else if (rpt == "i")
        {
            // Inventory report
            var lines = new List<string>();
            while (rdr.Read())
            {
                string ln = rdr["ProductName"].ToString().PadRight(30) 
                    + rdr["Quantity"].ToString().PadLeft(15) 
                    + rdr["Location"].ToString().PadLeft(15);
                lines.Add(ln);
            }
            // DUPLICATED: same header/separator logic as sales report
            string header = "INVENTORY REPORT - " + dt.ToString("yyyy-MM-dd");
            string separator = new string('-', 60);
            return header + "\n" + separator + "\n" + string.Join("\n", lines);
        }
        return "Unknown report type";
    }
}

// Se usa en produccion asi:
var sqlReader = GetSqlDataReaderFromSomewhere();
var system = new ReportingSystem(sqlReader);
string output = system.Gen("s", DateTime.Today);
```

**Tu tarea — aplica los 4 arreglos:**
1. **Nombres**: Renombra la clase, metodo, parametros y campo para revelar intencion
2. **Metodos pequenos**: El metodo `Gen` hace demasiado — extrae metodos helper
3. **DRY**: Identifica y elimina la duplicacion de la logica de formato de encabezado/separador
4. **Abstract vs Interface**: La dependencia en `SqlDataReader` hace imposible el testing. Introduce una abstraccion — decide si debe ser `interface` o `abstract class` y justifica con el decision tree

<details>
<summary>Ver solucion</summary>

```csharp
// 1. Abstraccion de datos — Interface (no hay estado compartido, es un contrato puro)
//    Decision tree: ¿Necesita estado? NO. ¿Tipos no relacionados? SI (SQL, CSV, mock para tests).
//    → Interface.
public interface IReportDataReader
{
    bool Read();
    string GetString(string columnName);
}

// 2. Clase reporta con nombres descriptivos y metodos pequenos
public class ReportGenerator
{
    private const int ReportLineWidth = 60;
    private readonly IReportDataReader _dataReader;  // Dependencia en abstraccion, no en SqlDataReader

    public ReportGenerator(IReportDataReader dataReader)
    {
        _dataReader = dataReader;
    }

    public string GenerateReport(ReportType reportType, DateTime reportDate)
    {
        string title = GetReportTitle(reportType, reportDate);
        List<string> dataLines = ReadDataLines(reportType);
        return FormatReport(title, dataLines);
    }

    private string GetReportTitle(ReportType reportType, DateTime reportDate) =>
        reportType switch
        {
            ReportType.Sales => $"SALES REPORT - {reportDate:yyyy-MM-dd}",
            ReportType.Inventory => $"INVENTORY REPORT - {reportDate:yyyy-MM-dd}",
            _ => throw new ArgumentOutOfRangeException(nameof(reportType))
        };

    private List<string> ReadDataLines(ReportType reportType)
    {
        var lines = new List<string>();
        while (_dataReader.Read())
        {
            string line = reportType switch
            {
                ReportType.Sales => FormatSalesLine(
                    _dataReader.GetString("ProductName"),
                    _dataReader.GetString("Amount"),
                    _dataReader.GetString("Date")),
                ReportType.Inventory => FormatInventoryLine(
                    _dataReader.GetString("ProductName"),
                    _dataReader.GetString("Quantity"),
                    _dataReader.GetString("Location")),
                _ => throw new ArgumentOutOfRangeException(nameof(reportType))
            };
            lines.Add(line);
        }
        return lines;
    }

    // DRY: formato de encabezado/separador en un solo lugar
    private static string FormatReport(string title, List<string> dataLines)
    {
        string separator = new string('-', ReportLineWidth);
        return title + "\n" + separator + "\n" + string.Join("\n", dataLines);
    }

    private static string FormatSalesLine(string productName, string amount, string date)
        => productName.PadRight(30) + amount.PadLeft(15) + date.PadLeft(15);

    private static string FormatInventoryLine(string productName, string quantity, string location)
        => productName.PadRight(30) + quantity.PadLeft(15) + location.PadLeft(15);
}

public enum ReportType { Sales, Inventory }

// Adaptador para SqlDataReader (en produccion)
public class SqlReportDataReader : IReportDataReader
{
    private readonly SqlDataReader _reader;
    public SqlReportDataReader(SqlDataReader reader) => _reader = reader;
    public bool Read() => _reader.Read();
    public string GetString(string columnName) => _reader[columnName].ToString() ?? string.Empty;
}

// Mock para testing (ahora posible gracias a la interfaz)
public class MockReportDataReader : IReportDataReader
{
    private readonly List<Dictionary<string, string>> _rows;
    private int _currentIndex = -1;

    public MockReportDataReader(List<Dictionary<string, string>> rows) => _rows = rows;
    public bool Read() => ++_currentIndex < _rows.Count;
    public string GetString(string columnName) => _rows[_currentIndex][columnName];
}
```

**Puntos clave:**
- `SqlDataReader` como dependencia directa es un *design smell* clasico — acopla la logica de negocio a la infraestructura de datos. `IReportDataReader` lo desacopla y habilita el testing
- La logica de formato (`FormatReport`) era identica en ambos branches — violacion DRY real porque si el formato cambia (ej: separador de 80 caracteres), debia cambiarse en 2 lugares
- `ReportType` enum reemplaza los magic strings `"s"` e `"i"` — el compilador ahora detecta tipos invalidos en tiempo de compilacion
- El metodo `Gen` de 40 lineas ahora es `GenerateReport` de 5 lineas — su estructura revela el algoritmo: obtener titulo, leer datos, formatear
- `FormatSalesLine` y `FormatInventoryLine` son metodos privados separados porque representan formatos de conocimiento distinto (diferentes columnas) — aqui NO aplicamos DRY aunque el codigo se vea similar

</details>

---

## Resumen: Puntos Clave para la Entrevista

1. **Cuales son las 3 practicas fundamentales de clean code?** Nombres descriptivos que revelan intencion (evitar abreviaciones y magic strings), metodos pequenos que hacen una sola cosa (SRP a nivel de metodo), y DRY para eliminar duplicacion de conocimiento.

2. **Cuando NO aplicarias DRY?** Cuando dos fragmentos de codigo se ven iguales pero representan reglas de negocio diferentes. Si cambia una regla no deberia cambiar la otra — fusionarlos crea una abstraccion incorrecta. Principio AHA: "Avoid Hasty Abstractions".

3. **Como decides entre abstract class e interface en C#?** Si necesitas compartir estado (fields) → abstract class. Si los tipos que implementan el contrato no estan relacionados o el cliente ya hereda de otra clase → interface. En la practica, la mayoria de los contratos para DI son interfaces.

4. **Que son los default interface methods y cuales son sus limitaciones?** DIMs son implementaciones en interfaces disponibles desde C# 8. Limitaciones: (1) no pueden tener fields, (2) no pueden acceder al estado de instancia de la clase implementadora, (3) solo son accesibles via referencia de interfaz, no via la clase concreta directamente.

5. **Que diferencia hay entre una abstract class sin estado y una interface?** Funcionalmente son similares, pero la abstract class no permite que la clase implementadora herede de otra clase base (C# no tiene herencia multiple de clases). Si es un contrato puro sin estado, la interface es la opcion correcta.

6. **Cuando es mejor una abstract class que una interface?** Cuando necesitas el patron Template Method — la clase base define el algoritmo general y las subclases implementan los pasos especificos. Tambien cuando hay logica compartida que depende de estado (fields) que todas las subclases deben tener.

7. **Que es un guard clause y por que reduce complejidad?** Un guard clause es una validacion al inicio del metodo que retorna o lanza inmediatamente si una precondicion falla. Elimina el anidamiento piramidal y hace que el happy path sea lineal y legible — reduciendo la complejidad ciclomatica del metodo.
