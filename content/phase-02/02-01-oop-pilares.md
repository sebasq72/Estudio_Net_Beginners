# OOP: Los 4 Pilares de la Programacion Orientada a Objetos

**Tiempo estimado:** ~1.5 horas
**Prerequisitos:** Conocimiento de C# moderno (Phase 1: records, pattern matching, nullable refs)
**Target:** .NET 10 LTS (todos los snippets compilan en .NET 10)

---

## Encapsulacion

La encapsulacion es el pilar que protege el estado interno de un objeto: los datos se ocultan detras de una interfaz publica controlada. En C# esto se implementa con **access modifiers** (`private`, `protected`, `internal`, `public`, `private protected`) y **properties** que exponen datos con logica de validacion. Desde C# 9, `init`-only setters permiten propiedades inmutables despues de la construccion sin necesitar un constructor extra.

La regla practica: nunca expongas un campo publico directamente. Usa properties o metodos que controlen como el estado cambia.

### Access modifiers en C#

| Modifier | Accesible desde |
|----------|----------------|
| `public` | Cualquier codigo |
| `private` | Solo la clase actual |
| `protected` | La clase actual y sus subclases |
| `internal` | Cualquier clase dentro del mismo ensamblado |
| `private protected` | La clase actual y subclases dentro del mismo ensamblado |

### Modelo: BankAccount con encapsulacion moderna

```csharp
public class BankAccount
{
    private decimal _balance;                        // campo privado — estado protegido

    public string AccountNumber { get; init; }       // inmutable despues de construccion
    public decimal Balance => _balance;              // read-only: exposicion sin mutacion directa

    public BankAccount(string accountNumber, decimal initialBalance)
    {
        ArgumentException.ThrowIfNullOrWhiteSpace(accountNumber);
        ArgumentOutOfRangeException.ThrowIfNegative(initialBalance);

        AccountNumber = accountNumber;
        _balance = initialBalance;
    }

    public bool Withdraw(decimal amount)
    {
        if (amount <= 0 || amount > _balance)
            return false;

        _balance -= amount;
        return true;
    }

    public void Deposit(decimal amount)
    {
        ArgumentOutOfRangeException.ThrowIfNegativeOrZero(amount);
        _balance += amount;
    }
}

// Uso:
var account = new BankAccount("ACC-001", 500m);
// account._balance = 9999m;          // ERROR: campo privado
// account.AccountNumber = "HACK";    // ERROR: init-only, no asignable despues de construccion
Console.WriteLine(account.Balance);   // 500 — acceso de solo lectura via property
account.Withdraw(100m);
Console.WriteLine(account.Balance);   // 400
```

> **Nota:** `init`-only setters (C# 9) son ideales para entidades que no deben cambiar su identidad despues de la construccion. Combinan la conveniencia de object initializers con la seguridad de inmutabilidad.

### Challenge 1: Diseña una clase PaymentCard

Diseña una clase `PaymentCard` que encapsule correctamente los datos de una tarjeta de credito. Debe cumplir los siguientes requisitos:

- El numero de tarjeta se almacena completo internamente, pero solo se exponen los ultimos 4 digitos publicamente (`MaskedNumber`).
- La fecha de vencimiento no puede ser una fecha pasada al momento de la construccion.
- El saldo disponible solo se puede reducir mediante un metodo `Charge(decimal amount)` que retorna `bool` indicando si el cobro fue exitoso.
- El saldo disponible nunca puede ser negativo.

```csharp
public class PaymentCard
{
    // Tu implementacion aqui
}

// Uso esperado:
var card = new PaymentCard("4111111111111234", new DateOnly(2027, 12, 31), 1000m);
Console.WriteLine(card.MaskedNumber);       // "**** **** **** 1234"
Console.WriteLine(card.Charge(250m));       // True
Console.WriteLine(card.AvailableBalance);   // 750
Console.WriteLine(card.Charge(1000m));      // False — excede el saldo
```

<details>
<summary>Ver solucion</summary>

```csharp
public class PaymentCard
{
    private readonly string _cardNumber;
    private decimal _availableBalance;

    public string MaskedNumber { get; }
    public decimal AvailableBalance => _availableBalance;
    public DateOnly ExpirationDate { get; }

    public PaymentCard(string cardNumber, DateOnly expirationDate, decimal initialBalance)
    {
        ArgumentException.ThrowIfNullOrWhiteSpace(cardNumber);
        if (cardNumber.Length < 4)
            throw new ArgumentException("Card number must have at least 4 digits.", nameof(cardNumber));
        if (expirationDate < DateOnly.FromDateTime(DateTime.Today))
            throw new ArgumentException("Card is already expired.", nameof(expirationDate));
        ArgumentOutOfRangeException.ThrowIfNegative(initialBalance);

        _cardNumber = cardNumber;
        ExpirationDate = expirationDate;
        _availableBalance = initialBalance;

        var last4 = cardNumber[^4..];
        MaskedNumber = $"**** **** **** {last4}";
    }

    public bool Charge(decimal amount)
    {
        if (amount <= 0 || amount > _availableBalance)
            return false;

        _availableBalance -= amount;
        return true;
    }
}
```

**Puntos clave:**
- `_cardNumber` es `readonly` — solo se asigna una vez en el constructor. `MaskedNumber` se calcula una sola vez y se expone como property read-only, nunca el numero completo.
- `cardNumber[^4..]` usa *range syntax* de C# 8: `^4` significa "4 desde el final". Evita `Substring(length - 4)`.
- La validacion de `expirationDate` en el constructor es *guard clause*: falla rapido y con mensaje claro antes de que el objeto sea usable.
- `AvailableBalance => _availableBalance` es expression-bodied property read-only: expone el estado sin permitir asignacion directa desde afuera.

</details>

---

## Herencia

La herencia permite que una clase (`derived`) extienda el comportamiento de otra (`base`), reutilizando y especializando su logica. En C# toda herencia es de clase unica (una clase solo puede heredar de otra clase), pero puede implementar multiples interfaces.

La trampa clasica de entrevista es entender la diferencia entre `override` y el keyword `new` — comportan de forma diferente segun el tipo de la *referencia*, no del objeto real.

### virtual / override — dispatch en tiempo de ejecucion

Cuando un metodo se declara `virtual` en la clase base y la clase derivada lo declara `override`, el runtime siempre ejecuta la version del *tipo real del objeto*, sin importar el tipo de la referencia.

```csharp
public class Animal
{
    public virtual void Speak() => Console.WriteLine("...");
    public void Move()          => Console.WriteLine("Animal moves");  // NO virtual
}

public class Dog : Animal
{
    public override void Speak() => Console.WriteLine("Woof!");   // override — reemplaza
    public new void Move()       => Console.WriteLine("Dog runs"); // new — oculta (hiding)
}
```

### La trampa de entrevista: override vs new con referencia de tipo base

```csharp
// Referencia de tipo BASE apuntando a objeto DERIVADO
Animal a = new Dog();
a.Speak();  // "Woof!" — override: dispatch en runtime segun el tipo REAL (Dog)
a.Move();   // "Animal moves" — new: dispatch en COMPILE-TIME segun el tipo de REFERENCIA (Animal)

// Referencia de tipo DERIVADO apuntando a objeto DERIVADO
Dog d = new Dog();
d.Speak();  // "Woof!" — igual, override siempre despacha al tipo real
d.Move();   // "Dog runs" — la referencia ES Dog, por eso llama Dog.Move()
```

> **Regla de oro:** Con `override`, lo que importa es el *tipo real del objeto* (runtime). Con `new`, lo que importa es el *tipo de la referencia* (compile-time). Cuando la referencia es de tipo base, `new` llama el metodo del padre — aunque el objeto sea una subclase.

### sealed — bloquear la cadena de herencia

`sealed` en una clase impide que sea heredada. `sealed` en un metodo `override` impide que subclases posteriores lo vuelvan a hacer `override`.

```csharp
public sealed class SqlServerRepository : BaseRepository
{
    // Nadie puede heredar de SqlServerRepository
}

public class BaseProcessor
{
    public virtual void Process() => Console.WriteLine("Processing...");
}

public class OrderProcessor : BaseProcessor
{
    public sealed override void Process() => Console.WriteLine("Order processed");
    // OrderProcessor.Process no puede ser override-ado en subclases de OrderProcessor
}
```

**Cuando usar `sealed`:**
- Clases de framework que no deben extenderse (evita fragile base class problem).
- Clases de utilidad sin estado extensible (`static` no alcanza para instancias).
- Metodos override donde el comportamiento no debe poder modificarse mas.
- Performance: el compilador/JIT puede hacer *devirtualization* en clases o metodos sealed.

### Challenge 2: Sistema de empleados — predice el output

El siguiente codigo usa tanto `override` como `new`. Sin ejecutarlo, predice que imprime cada linea del bloque de uso. Luego verifica tu respuesta expandiendo la solucion.

```csharp
public class Employee
{
    public virtual decimal CalculateBonus() => 500m;
    public string GetRole() => "Employee";
}

public class Manager : Employee
{
    public override decimal CalculateBonus() => base.CalculateBonus() * 2;
    public new string GetRole() => "Manager";
}

public class Intern : Employee
{
    public override decimal CalculateBonus() => 100m;
    public new string GetRole() => "Intern";
}

// Bloque de uso:
Employee e1 = new Manager();
Employee e2 = new Intern();
Manager m = new Manager();

Console.WriteLine(e1.CalculateBonus()); // ?
Console.WriteLine(e2.CalculateBonus()); // ?
Console.WriteLine(e1.GetRole());        // ?
Console.WriteLine(m.GetRole());         // ?
Console.WriteLine(e1 is Manager);       // ?
```

<details>
<summary>Ver solucion</summary>

```
1000    — override: tipo real es Manager, Manager.CalculateBonus() = 500 * 2
100     — override: tipo real es Intern, Intern.CalculateBonus() = 100
Employee — new (hiding): referencia es Employee, llama Employee.GetRole()
Manager  — referencia es Manager (tipo derivado), llama Manager.GetRole()
True     — is operator verifica el tipo real del objeto, no el tipo de referencia
```

Codigo anotado:

```csharp
Employee e1 = new Manager();
Employee e2 = new Intern();
Manager m = new Manager();

Console.WriteLine(e1.CalculateBonus()); // 1000 — override despacha a Manager.CalculateBonus()
Console.WriteLine(e2.CalculateBonus()); // 100  — override despacha a Intern.CalculateBonus()
Console.WriteLine(e1.GetRole());        // "Employee" — new: referencia es Employee, no llega a Manager.GetRole()
Console.WriteLine(m.GetRole());         // "Manager"  — referencia ES Manager, llama Manager.GetRole()
Console.WriteLine(e1 is Manager);       // True — is verifica el tipo real (Manager), no el de la referencia
```

**Puntos clave:**
- `CalculateBonus` usa `override` — el runtime siempre llama la version del tipo real. `e1` es un `Manager` en memoria, asi que `Manager.CalculateBonus()` se ejecuta aunque la referencia sea `Employee`.
- `GetRole` usa `new` — el compilador decide en compile-time segun el tipo de referencia. `e1` es declarada como `Employee`, por eso `Employee.GetRole()` se llama, ignorando la version de `Manager`.
- `is` verifica el tipo real del objeto, no el tipo de la referencia. Esto es consistente con el comportamiento de `override`.
- `base.CalculateBonus()` en `Manager` llama explicitamente la implementacion del padre — util para extender (no reemplazar) el calculo.

</details>

---

## Polimorfismo

El polimorfismo permite que distintos tipos respondan al mismo mensaje de maneras diferentes. En C# hay dos formas principales:

- **Polimorfismo en tiempo de ejecucion** (*runtime polymorphism*): via `virtual`/`override` — el tipo real del objeto determina el comportamiento. Ya lo vimos en Herencia.
- **Polimorfismo en tiempo de compilacion** (*compile-time polymorphism*): via *method overloading* — multiples metodos con el mismo nombre pero diferentes firmas.

### Overloading — polimorfismo en tiempo de compilacion

```csharp
public class Formatter
{
    public string Format(decimal amount) =>
        $"{amount:C2}";

    public string Format(decimal amount, string currencyCode) =>
        $"{amount:F2} {currencyCode}";

    public string Format(decimal amount, string currencyCode, int decimals) =>
        $"{amount.ToString($"F{decimals}")} {currencyCode}";
}

// El compilador elige el metodo correcto segun los argumentos:
var f = new Formatter();
Console.WriteLine(f.Format(99.99m));              // "$99.99"
Console.WriteLine(f.Format(99.99m, "EUR"));       // "99.99 EUR"
Console.WriteLine(f.Format(99.99m, "EUR", 4));    // "99.9900 EUR"
```

### Casting, is y as

Para trabajar con jerarquias de tipos, C# ofrece tres mecanismos de *casting*:

```csharp
public abstract class Shape
{
    public abstract double CalculateArea();
}

public class Circle : Shape
{
    public double Radius { get; }
    public Circle(double radius) => Radius = radius;
    public override double CalculateArea() => Math.PI * Radius * Radius;
}

public class Rectangle : Shape
{
    public double Width { get; }
    public double Height { get; }
    public Rectangle(double width, double height) { Width = width; Height = height; }
    public override double CalculateArea() => Width * Height;
}

List<Shape> shapes = [new Circle(5), new Rectangle(4, 6), new Circle(3)];

// Polimorfismo runtime: cada Shape llama SU CalculateArea
foreach (var shape in shapes)
    Console.WriteLine($"Area: {shape.CalculateArea():F2}");

// is — verifica tipo sin lanzar excepcion, puede declarar variable
foreach (var shape in shapes)
{
    if (shape is Circle circle)
        Console.WriteLine($"Circle radius: {circle.Radius}");
}

// as — intenta cast, retorna null si falla (no lanza excepcion)
Shape someShape = new Rectangle(3, 4);
var rect = someShape as Rectangle;  // rect es Rectangle si funciona, null si no
var circ = someShape as Circle;     // null — someShape no es Circle

// Cast explicito — lanza InvalidCastException si falla
var r = (Rectangle)someShape;       // OK
// var c = (Circle)someShape;       // lanza InvalidCastException en runtime
```

### Pattern matching con tipos (conecta con Phase 1)

El `switch` expression con *type patterns* es la forma moderna de despachar por tipo:

```csharp
string DescribeShape(Shape shape) => shape switch
{
    Circle { Radius: > 10 } c  => $"Large circle with radius {c.Radius:F1}",
    Circle c                    => $"Small circle with radius {c.Radius:F1}",
    Rectangle { Width: var w, Height: var h } when w == h => $"Square of {w:F1}",
    Rectangle { Width: var w, Height: var h } => $"Rectangle {w:F1}x{h:F1}",
    null                       => throw new ArgumentNullException(nameof(shape)),
    _                          => $"Unknown shape: {shape.GetType().Name}"
};
```

### Challenge 3: Procesador de notificaciones

Tienes una lista de notificaciones de distintos tipos. Implementa el metodo `ProcessAll` que procese cada una polimorficamente. Cada tipo de notificacion tiene una logica de entrega diferente.

```csharp
public abstract class Notification
{
    public string Recipient { get; }
    public string Message { get; }

    protected Notification(string recipient, string message)
    {
        Recipient = recipient;
        Message = message;
    }

    public abstract string Deliver();
}

public class EmailNotification : Notification
{
    public string Subject { get; }
    public EmailNotification(string to, string subject, string body)
        : base(to, body) => Subject = subject;

    // Tu implementacion de Deliver()
}

public class SmsNotification : Notification
{
    public int MaxChars => 160;
    public SmsNotification(string phone, string message) : base(phone, message) { }

    // Tu implementacion de Deliver()
}

public class PushNotification : Notification
{
    public string DeviceToken { get; }
    public PushNotification(string deviceToken, string message)
        : base(deviceToken, message) => DeviceToken = deviceToken;

    // Tu implementacion de Deliver()
}

public class NotificationProcessor
{
    public IEnumerable<string> ProcessAll(IEnumerable<Notification> notifications)
    {
        // Implementa: itera la lista, llama Deliver() polimorficamente
        // Para SmsNotification, trunca el mensaje si supera MaxChars
    }
}
```

<details>
<summary>Ver solucion</summary>

```csharp
public class EmailNotification : Notification
{
    public string Subject { get; }
    public EmailNotification(string to, string subject, string body)
        : base(to, body) => Subject = subject;

    public override string Deliver() =>
        $"[EMAIL] To: {Recipient} | Subject: {Subject} | Body: {Message}";
}

public class SmsNotification : Notification
{
    public int MaxChars => 160;
    public SmsNotification(string phone, string message) : base(phone, message) { }

    public override string Deliver()
    {
        var truncated = Message.Length > MaxChars
            ? Message[..MaxChars] + "..."
            : Message;
        return $"[SMS] To: {Recipient} | {truncated}";
    }
}

public class PushNotification : Notification
{
    public string DeviceToken { get; }
    public PushNotification(string deviceToken, string message)
        : base(deviceToken, message) => DeviceToken = deviceToken;

    public override string Deliver() =>
        $"[PUSH] Device: {DeviceToken[^8..]} | {Message}";
}

public class NotificationProcessor
{
    public IEnumerable<string> ProcessAll(IEnumerable<Notification> notifications)
    {
        ArgumentNullException.ThrowIfNull(notifications);
        return notifications.Select(n => n.Deliver());
    }
}

// Uso:
var processor = new NotificationProcessor();
List<Notification> queue =
[
    new EmailNotification("user@example.com", "Welcome", "Thanks for joining!"),
    new SmsNotification("+1234567890", "Your OTP is 8472"),
    new PushNotification("device-token-abc123xyz", "You have a new message"),
];

foreach (var result in processor.ProcessAll(queue))
    Console.WriteLine(result);
```

**Puntos clave:**
- `ProcessAll` recibe `IEnumerable<Notification>` — trabaja con el tipo base. No necesita saber que tipos concretos hay. Cuando llama `n.Deliver()`, el runtime despacha al `override` correcto para cada instancia. Esto es polimorfismo puro.
- El truncamiento de `SmsNotification` es logica especifica del tipo: vive en la clase, no en el procesador. Si el procesador necesitara hacer el truncamiento, violaria encapsulacion (necesitaria saber el limite de SMS).
- `Message[..MaxChars]` usa *range syntax*: toma los primeros `MaxChars` caracteres. Equivale a `Message.Substring(0, MaxChars)`.
- `DeviceToken[^8..]` expone solo los ultimos 8 caracteres del token — patron de seguridad similar al `MaskedNumber` del Challenge 1.

</details>

---

## Abstraccion

La abstraccion es el proceso de definir un contrato sin especificar la implementacion: "que hace" sin "como lo hace". En C# se implementa mediante **clases abstractas** (`abstract class`) con **metodos abstractos** (`abstract`) que las subclases deben implementar.

Una clase abstracta puede tener:
- Metodos abstractos (sin cuerpo — contrato puro)
- Metodos concretos (con implementacion compartida por todas las subclases)
- Campos y propiedades (estado compartido)

> **Nota:** La comparacion completa entre `abstract class` e `interface` se cubre en el Plan 02-03. Por ahora, entiende `abstract class` como la herramienta para cuando tienes **comportamiento parcialmente implementado** que las subclases deben completar.

### Modelo: PaymentProcessor abstracto

```csharp
public abstract class PaymentProcessor
{
    // Contrato: cada subclase DEBE implementar este metodo
    public abstract bool ProcessPayment(decimal amount, string currency);

    // Implementacion concreta compartida: todas las subclases usan este log
    public void LogTransaction(decimal amount, string currency, bool success)
    {
        var status = success ? "SUCCESS" : "FAILED";
        Console.WriteLine($"[{DateTime.UtcNow:u}] {status} | {amount:F2} {currency}");
    }

    // Metodo template: define el flujo, las subclases implementan los pasos
    public bool Execute(decimal amount, string currency)
    {
        if (amount <= 0) return false;
        bool result = ProcessPayment(amount, currency);
        LogTransaction(amount, currency, result);
        return result;
    }
}

public class StripeProcessor : PaymentProcessor
{
    private readonly string _apiKey;
    public StripeProcessor(string apiKey) => _apiKey = apiKey;

    public override bool ProcessPayment(decimal amount, string currency)
    {
        // Llamaria a Stripe API en una implementacion real
        Console.WriteLine($"Stripe: charging {amount} {currency}");
        return true;
    }
}

public class PayPalProcessor : PaymentProcessor
{
    private readonly string _clientId;
    public PayPalProcessor(string clientId) => _clientId = clientId;

    public override bool ProcessPayment(decimal amount, string currency)
    {
        Console.WriteLine($"PayPal: processing {amount} {currency}");
        return true;
    }
}

// Uso — polimorfismo + abstraccion:
List<PaymentProcessor> processors =
[
    new StripeProcessor("sk_test_abc"),
    new PayPalProcessor("client_xyz"),
];

foreach (var processor in processors)
    processor.Execute(99.99m, "USD");  // Cada uno usa SU ProcessPayment, pero el mismo log
```

> **Preview:** El patron que usa `Execute()` — un metodo concreto que llama metodos abstractos definidos por las subclases — se llama **Template Method**. Es uno de los design patterns de comportamiento que se cubre en Phase 3.

### Challenge 4: Sistema de reportes

Diseña un sistema de generacion de reportes usando una clase abstracta. Cada tipo de reporte (ventas, inventario, usuarios) recopila datos y formatea la salida diferente, pero todos siguen el mismo flujo de generacion.

```csharp
public abstract class ReportGenerator
{
    // Tu implementacion: define el contrato y el flujo
    // El metodo publico GenerateReport() debe llamar GatherData() y FormatOutput() en orden
    // GatherData() y FormatOutput() son abstractos — cada subclase los implementa
}

public class SalesReport : ReportGenerator
{
    // Recopila: total de ventas del mes (simulado con datos dummy)
    // Formato: "Sales Report\n-----------\nTotal: $X,XXX.XX"
}

public class InventoryReport : ReportGenerator
{
    // Recopila: lista de productos con stock bajo (simulado con datos dummy)
    // Formato: "Inventory Alert\n---------------\n[lista de items]"
}

// Uso esperado:
ReportGenerator report = new SalesReport();
Console.WriteLine(report.GenerateReport());
```

<details>
<summary>Ver solucion</summary>

```csharp
public abstract class ReportGenerator
{
    // Contrato: subclases implementan estos pasos
    protected abstract void GatherData();
    protected abstract string FormatOutput();

    // Flujo fijo: siempre gather → format → return
    public string GenerateReport()
    {
        GatherData();
        return FormatOutput();
    }
}

public class SalesReport : ReportGenerator
{
    private decimal _totalSales;
    private int _transactionCount;

    protected override void GatherData()
    {
        // Simulacion: en produccion consultaria la DB
        _totalSales = 48_250.75m;
        _transactionCount = 312;
    }

    protected override string FormatOutput() =>
        $"""
        Sales Report
        ------------
        Total:        {_totalSales:C2}
        Transactions: {_transactionCount}
        Avg per sale: {_totalSales / _transactionCount:C2}
        """;
}

public class InventoryReport : ReportGenerator
{
    private List<(string Product, int Stock)> _lowStockItems = [];

    protected override void GatherData()
    {
        // Simulacion: items con menos de 10 unidades
        _lowStockItems =
        [
            ("Laptop Pro 15", 3),
            ("USB-C Hub 7-port", 7),
            ("Mechanical Keyboard", 2),
        ];
    }

    protected override string FormatOutput()
    {
        var items = string.Join("\n", _lowStockItems
            .Select(i => $"  - {i.Product}: {i.Stock} units left"));
        return $"Inventory Alert\n---------------\n{items}";
    }
}

// Uso:
List<ReportGenerator> reports = [new SalesReport(), new InventoryReport()];
foreach (var report in reports)
{
    Console.WriteLine(report.GenerateReport());
    Console.WriteLine();
}
```

**Puntos clave:**
- `GatherData()` y `FormatOutput()` son `protected abstract` — el contrato es interno a la jerarquia, no expuesto publicamente. `GenerateReport()` es el unico punto de entrada publico.
- `GenerateReport()` es un metodo *template*: define el algoritmo (orden de pasos), las subclases implementan cada paso. Si el flujo necesita cambiar (ej. agregar caching), se cambia en un solo lugar.
- `protected override` es el modificador correcto para metodos abstractos que son parte del contrato de la clase base pero no deben exponerse fuera de la jerarquia.
- Los raw string literals (`"""..."""`) de C# 11 simplifican strings multilínea sin escape. Se pueden usar directamente en `FormatOutput()`.

</details>

---

## Resumen: Puntos Clave para la Entrevista

1. **Que es encapsulacion y como se implementa en C#?** Es ocultar el estado interno y exponer solo una interfaz controlada. Se implementa con `private`/`protected` fields, properties con getters/setters, e `init`-only setters para inmutabilidad post-construccion.

2. **Cual es la diferencia entre `override` y `new` en C#?** `override` reemplaza el metodo del padre en la cadena de dispatch virtual — el runtime llama la version del tipo real del objeto. `new` oculta el metodo del padre (*method hiding*) — el compilador decide que version llamar segun el tipo de la *referencia*, no del objeto real.

3. **Que pasa con `Base b = new Derived(); b.Method();` con `new` vs `override`?** Con `override`, se llama `Derived.Method()` — el runtime despacha al tipo real. Con `new`, se llama `Base.Method()` — el compilador usa el tipo de referencia (`Base`). Esta es la trampa clasica de entrevista.

4. **Para que sirve `sealed`?** Impide que una clase sea heredada o que un metodo `override` sea sobreescrito por subclases posteriores. Se usa para expresar intencion de diseno (clase final), prevenir el *fragile base class problem*, y habilitar optimizaciones del JIT (*devirtualization*).

5. **Diferencia entre polimorfismo en tiempo de compilacion y de ejecucion?** En *compile-time* (method overloading): el compilador elige el metodo correcto segun los tipos de los argumentos. En *runtime* (virtual dispatch via `override`): el CLR llama la implementacion del tipo real del objeto en memoria. Ambos usan el mismo nombre de metodo — la diferencia es cuando se resuelve.

6. **Cuando usarias una clase abstracta?** Cuando tienes comportamiento parcialmente implementado que las subclases deben completar, Y necesitas compartir estado (campos) entre ellas. Si solo defines un contrato sin implementacion ni estado, la respuesta suele ser una interfaz. La comparacion completa (con default interface methods de C# 8) se cubre en el Plan 02-03.
