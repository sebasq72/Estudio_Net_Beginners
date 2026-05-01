# LINQ, Generics, Delegates y Programacion Funcional en C#

**Tiempo estimado:** ~1.5 horas
**Prerequisitos:** C# basico, familiaridad con colecciones (`List<T>`, `Dictionary<TKey, TValue>`)
**Target:** .NET 10 LTS (todos los snippets compilan en .NET 10)

---

## LINQ — Deferred Execution e IQueryable vs IEnumerable

LINQ aparece en **cada entrevista .NET**. No es solo una herramienta de consulta — es el idioma en el que se expresa la logica de transformacion de datos en C# moderno. Esta seccion cubre los conceptos que los entrevistadores esperan que domines: deferred execution, la diferencia entre `IQueryable` e `IEnumerable`, y los operadores avanzados que separan al junior del mid-level.

### Deferred Execution

La ejecucion diferida es el concepto mas importante de LINQ. Una query LINQ **no se ejecuta cuando se define** — se ejecuta cuando se itera sobre ella. Esto significa que `Where`, `Select`, `OrderBy` y otros operadores solo construyen una descripcion de la query. La ejecucion real ocurre al materializar con `ToList()`, `ToArray()`, `Count()`, `First()` o un `foreach`.

```csharp
// Deferred execution: la query NO se ejecuta aqui
List<int> numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

var evenNumbers = numbers.Where(n => n % 2 == 0); // no ejecuta nada
// evenNumbers es un IEnumerable<int> — una "promesa" de filtrar

numbers.Add(12); // agregamos un elemento DESPUES de definir la query

var result = evenNumbers.ToList(); // AQUI se ejecuta: [2, 4, 6, 8, 10, 12]
// El 12 aparece porque la query se ejecuta ahora, no cuando se definio
```

```csharp
// Multiples materializaciones = multiples ejecuciones
var query = numbers.Where(n =>
{
    Console.WriteLine($"Evaluating {n}"); // se imprime cada vez que se itera
    return n > 5;
});

var count = query.Count();    // ejecuta la query completa (imprime todos)
var list = query.ToList();    // ejecuta la query OTRA VEZ (imprime todos de nuevo)
// Si la fuente de datos cambia entre ambas llamadas, los resultados pueden diferir
```

### IQueryable vs IEnumerable — La Diferencia que Importa

`IEnumerable<T>` ejecuta la logica de filtrado **en memoria** (LINQ to Objects). `IQueryable<T>` construye un **expression tree** que se traduce a SQL (o al lenguaje de la fuente de datos). La diferencia en performance puede ser catastrofica:

```csharp
// --- INCORRECTO: materializa TODO antes de filtrar ---
var wrong = _context.Products
    .ToList()                           // SELECT * FROM Products — trae TODOS a memoria
    .Where(p => p.Price > 100);         // filtra en C#, en memoria
// Si hay 1 millon de productos, todos se cargan a memoria para filtrar unos pocos

// --- CORRECTO: filtra en la base de datos ---
var right = _context.Products           // IQueryable<Product> — no ejecuta nada
    .Where(p => p.Price > 100)          // agrega WHERE al expression tree
    .OrderBy(p => p.Name)               // agrega ORDER BY al expression tree
    .ToList();                          // AQUI ejecuta la query SQL completa
```

> **Query SQL del caso correcto:** `SELECT * FROM Products WHERE Price > 100 ORDER BY Name`
> **Query SQL del caso incorrecto:** `SELECT * FROM Products` (luego filtra y ordena en memoria)

**Regla practica:** Mantente en `IQueryable` todo lo posible cuando trabajas con EF Core. Solo materializa (`ToList()`, `ToArray()`) cuando ya aplicaste todos los filtros, ordenamiento y proyecciones. En .NET 8 y .NET 10, EF Core es lo suficientemente inteligente para traducir la mayoria de expresiones LINQ a SQL eficiente.

### Operadores Clave

```csharp
var products = new List<Product>
{
    new("Laptop", 1200m, "Electronics"),
    new("Mouse", 25m, "Electronics"),
    new("Desk", 350m, "Furniture"),
    new("Chair", 450m, "Furniture"),
    new("Keyboard", 75m, "Electronics"),
    new("Lamp", 40m, "Furniture")
};

public record Product(string Name, decimal Price, string Category);

// Select — proyeccion (transforma cada elemento)
var names = products.Select(p => p.Name);
// ["Laptop", "Mouse", "Desk", "Chair", "Keyboard", "Lamp"]

// Where — filtro
var expensive = products.Where(p => p.Price > 100);
// [Laptop(1200), Desk(350), Chair(450)]

// GroupBy — agrupa por clave
var byCategory = products.GroupBy(p => p.Category);
// Group "Electronics": [Laptop, Mouse, Keyboard]
// Group "Furniture": [Desk, Chair, Lamp]

foreach (var group in byCategory)
{
    Console.WriteLine($"{group.Key}: {group.Count()} products, total ${group.Sum(p => p.Price)}");
}
// Electronics: 3 products, total $1300
// Furniture: 3 products, total $840

// SelectMany — aplana colecciones anidadas
var orders = new List<Order>
{
    new("Order1", ["Laptop", "Mouse"]),
    new("Order2", ["Desk", "Keyboard", "Lamp"])
};
public record Order(string Id, List<string> Items);

var allItems = orders.SelectMany(o => o.Items);
// ["Laptop", "Mouse", "Desk", "Keyboard", "Lamp"]
// Sin SelectMany tendrias IEnumerable<List<string>> — coleccion de colecciones

// Aggregate — reduce la coleccion a un solo valor
var totalValue = products.Aggregate(0m, (sum, p) => sum + p.Price);
// 2140 (suma de todos los precios)

var mostExpensive = products.Aggregate((max, p) => p.Price > max.Price ? p : max);
// Laptop(1200)

// Any / All — verificacion booleana
bool hasExpensive = products.Any(p => p.Price > 1000);  // true
bool allCheap = products.All(p => p.Price < 500);       // false
```

### Challenge 1: Comprension de Deferred Execution

Analiza el siguiente codigo y responde las tres preguntas sin ejecutarlo:

```csharp
var numbers = new List<int> { 1, 2, 3, 4, 5 };

var query = numbers
    .Where(n => n > 2)
    .Select(n => n * 10)
    .Where(n => n > 35);

numbers.Add(6);
numbers.Add(7);

var result = query.ToList();
Console.WriteLine(string.Join(", ", result));

var count = query.Count();
```

**Preguntas:**
1. Cuantas veces se ejecuta la query completa?
2. En que linea se materializa por primera vez?
3. Que valores contiene `result`? Incluye el 6 y el 7?

<details>
<summary>Ver solucion</summary>

```csharp
// La query se ejecuta DOS veces:
// 1. En query.ToList() — linea del ToList()
// 2. En query.Count() — Count() materializa internamente

// result contiene: [40, 50, 60, 70]
// Explicacion:
// numbers al momento de ToList() = [1, 2, 3, 4, 5, 6, 7]
// .Where(n => n > 2)    → [3, 4, 5, 6, 7]
// .Select(n => n * 10)  → [30, 40, 50, 60, 70]
// .Where(n => n > 35)   → [40, 50, 60, 70]

// SI, incluye el 6 y el 7 porque la query se ejecuta DESPUES de agregarlos
```

**Puntos clave:**
- La query se define en las lineas de `Where` y `Select`, pero no se ejecuta hasta `ToList()` o `Count()`.
- Cada materializacion (`ToList()`, `Count()`) recorre la fuente de datos completa de nuevo.
- Si la fuente cambia entre la definicion y la materializacion, los resultados reflejan el estado actual.
- En produccion, si necesitas el resultado varias veces, materializa una vez y reutiliza la lista.

</details>

### Challenge 2: Analisis de Ventas con GroupBy, SelectMany y Aggregate

Dado el siguiente modelo de datos de ventas, escribe queries LINQ para responder cada pregunta:

```csharp
public record Sale(string Product, decimal Amount, string Category, DateTime Date);

var sales = new List<Sale>
{
    new("Laptop", 1200m, "Electronics", new DateTime(2025, 1, 15)),
    new("Mouse", 25m, "Electronics", new DateTime(2025, 1, 20)),
    new("Desk", 350m, "Furniture", new DateTime(2025, 2, 10)),
    new("Laptop", 1200m, "Electronics", new DateTime(2025, 2, 14)),
    new("Chair", 450m, "Furniture", new DateTime(2025, 2, 20)),
    new("Mouse", 25m, "Electronics", new DateTime(2025, 3, 5)),
    new("Lamp", 40m, "Furniture", new DateTime(2025, 3, 10)),
    new("Keyboard", 75m, "Electronics", new DateTime(2025, 3, 15))
};
```

**(a)** Total de ventas por categoria, ordenado de mayor a menor.
**(b)** Productos que se vendieron en mas de un mes distinto.
**(c)** Promedio acumulado (running average) del monto de todas las ventas en orden cronologico.

<details>
<summary>Ver solucion</summary>

```csharp
// (a) Total por categoria, ordenado de mayor a menor
var totalByCategory = sales
    .GroupBy(s => s.Category)
    .Select(g => new { Category = g.Key, Total = g.Sum(s => s.Amount) })
    .OrderByDescending(x => x.Total);

foreach (var item in totalByCategory)
    Console.WriteLine($"{item.Category}: ${item.Total}");
// Electronics: $2525
// Furniture: $840

// (b) Productos vendidos en mas de un mes distinto
var multiMonthProducts = sales
    .GroupBy(s => s.Product)
    .Where(g => g.Select(s => s.Date.Month).Distinct().Count() > 1)
    .Select(g => g.Key);

Console.WriteLine(string.Join(", ", multiMonthProducts));
// Laptop, Mouse

// (c) Promedio acumulado (running average) en orden cronologico
var orderedSales = sales.OrderBy(s => s.Date).ToList();

var runningAverage = orderedSales
    .Select((sale, index) =>
    {
        var runningSum = orderedSales.Take(index + 1).Sum(s => s.Amount);
        return new
        {
            sale.Product,
            sale.Amount,
            RunningAvg = runningSum / (index + 1)
        };
    });

foreach (var item in runningAverage)
    Console.WriteLine($"{item.Product}: ${item.Amount} (avg: ${item.RunningAvg:F2})");
// Laptop: $1200 (avg: $1200.00)
// Mouse: $25 (avg: $612.50)
// Desk: $350 (avg: $525.00)
// Laptop: $1200 (avg: $693.75)
// Chair: $450 (avg: $645.00)
// Mouse: $25 (avg: $541.67)
// Lamp: $40 (avg: $470.00)
// Keyboard: $75 (avg: $420.63)
```

**Puntos clave:**
- `GroupBy` retorna `IEnumerable<IGrouping<TKey, TElement>>` — cada grupo tiene una `Key` y es iterable.
- Para `(b)`, la combinacion de `GroupBy` + `Select` + `Distinct` + `Count` es un patron comun para encontrar elementos que aparecen en multiples categorias.
- El promedio acumulado con `Select` y su overload con indice `(item, index)` es un patron funcional potente. En produccion con EF Core, esta logica se haria en memoria despues de materializar.
- `Aggregate` es una alternativa para `(c)`: `orderedSales.Aggregate(new List<...>(), (acc, sale) => ...)` pero el overload de `Select` con indice es mas legible aqui.

</details>

---

## Generics y Constraints

Los generics permiten escribir codigo que funciona con cualquier tipo sin perder type safety. Son la base de las colecciones (`List<T>`, `Dictionary<TKey, TValue>`), de LINQ, y de muchos design patterns. En entrevistas, espera preguntas sobre constraints y covarianza/contravarianza.

### Generic Classes, Methods e Interfaces

```csharp
// Generic class
public class Cache<TKey, TValue> where TKey : notnull
{
    private readonly Dictionary<TKey, TValue> _store = new();

    public void Set(TKey key, TValue value) => _store[key] = value;
    public bool TryGet(TKey key, out TValue? value) => _store.TryGetValue(key, out value);
}

// Generic method
public T Max<T>(T a, T b) where T : IComparable<T>
    => a.CompareTo(b) >= 0 ? a : b;

var result = Max(10, 20);       // T se infiere como int
var name = Max("Alice", "Bob"); // T se infiere como string

// Generic interface
public interface IRepository<T> where T : class
{
    T? GetById(int id);
    IEnumerable<T> GetAll();
    void Add(T entity);
}
```

### Constraints

Las constraints restringen que tipos se pueden usar como argumento generico. El compilador las usa para garantizar que las operaciones son validas:

```csharp
// Constraint de tipo referencia — T debe ser una clase
public class Store<T> where T : class { }

// Constraint de tipo valor — T debe ser un struct
public void Process<T>(T value) where T : struct { }

// Constraint de constructor — T debe tener constructor sin parametros
public T Create<T>() where T : new() => new T();

// Constraint de interfaz — T debe implementar la interfaz
public void Sort<T>(List<T> items) where T : IComparable<T>
    => items.Sort();

// Constraint notnull — T no puede ser null (valor o referencia non-nullable)
public class Registry<T> where T : notnull { }

// Multiples constraints (ORDEN: class/struct primero, luego interfaces, new() al final)
public class Repository<T> where T : class, IEntity, new()
{
    public T CreateDefault() => new T();
}
```

### Covarianza y Contravarianza

Estos conceptos definen como se relacionan los generics con la herencia. En entrevistas, la pregunta tipica es: "que es covarianza?"

```csharp
// COVARIANZA (out) — permite usar un tipo mas derivado donde se espera uno menos derivado
// IEnumerable<out T> es covariante: si Dog hereda de Animal,
// entonces IEnumerable<Dog> es asignable a IEnumerable<Animal>

public class Animal { public string Name { get; init; } = ""; }
public class Dog : Animal { public string Breed { get; init; } = ""; }

IEnumerable<Dog> dogs = new List<Dog> { new() { Name = "Rex", Breed = "Lab" } };
IEnumerable<Animal> animals = dogs; // VALIDO — covarianza con 'out'
// Funciona porque IEnumerable solo PRODUCE (retorna) T, nunca lo consume

// CONTRAVARIANZA (in) — permite usar un tipo menos derivado donde se espera uno mas derivado
// Action<in T> es contravariante: si Dog hereda de Animal,
// entonces Action<Animal> es asignable a Action<Dog>

Action<Animal> printAnimal = a => Console.WriteLine(a.Name);
Action<Dog> printDog = printAnimal; // VALIDO — contravarianza con 'in'
printDog(new Dog { Name = "Rex" }); // imprime "Rex"
// Funciona porque Action solo CONSUME (recibe) T, nunca lo produce

// REGLA MNEMOTECNICA:
// out = PRODUCE T (retorno) → covariante (mas especifico a menos especifico)
// in  = CONSUME T (parametro) → contravariante (menos especifico a mas especifico)
```

> **Pregunta de entrevista:** "Que es covarianza?" Respuesta: es la capacidad de usar un tipo generico mas derivado donde se espera uno menos derivado, habilitada por el keyword `out` en la declaracion del type parameter. Ejemplo clasico: `IEnumerable<Dog>` se asigna a `IEnumerable<Animal>`.

### Challenge 3: Implementar GenericRepository<T>

Implementa una clase `GenericRepository<T>` que cumpla estas condiciones:
- `T` debe ser un tipo referencia que implemente `IEntity`
- Metodos: `GetById(int id)`, `Add(T entity)`, `Update(T entity)`, `Delete(int id)`, `GetAll(Func<T, bool> predicate)`
- Usa una `List<T>` como almacen interno
- `Update` reemplaza la entidad existente con el mismo `Id`
- `GetAll` con predicate filtra las entidades que cumplen la condicion

```csharp
public interface IEntity
{
    int Id { get; }
}

// Tu implementacion aqui
public class GenericRepository<T> where T : ???
{
    public T? GetById(int id) => throw new NotImplementedException();
    public void Add(T entity) => throw new NotImplementedException();
    public void Update(T entity) => throw new NotImplementedException();
    public bool Delete(int id) => throw new NotImplementedException();
    public IEnumerable<T> GetAll(Func<T, bool>? predicate = null) => throw new NotImplementedException();
}
```

<details>
<summary>Ver solucion</summary>

```csharp
public interface IEntity
{
    int Id { get; }
}

public class GenericRepository<T> where T : class, IEntity
{
    private readonly List<T> _store = new();

    public T? GetById(int id)
        => _store.FirstOrDefault(e => e.Id == id);

    public void Add(T entity)
    {
        if (_store.Any(e => e.Id == entity.Id))
            throw new InvalidOperationException($"Entity with Id {entity.Id} already exists.");

        _store.Add(entity);
    }

    public void Update(T entity)
    {
        var index = _store.FindIndex(e => e.Id == entity.Id);
        if (index == -1)
            throw new KeyNotFoundException($"Entity with Id {entity.Id} not found.");

        _store[index] = entity;
    }

    public bool Delete(int id)
    {
        var entity = GetById(id);
        return entity is not null && _store.Remove(entity);
    }

    public IEnumerable<T> GetAll(Func<T, bool>? predicate = null)
        => predicate is null ? _store.AsEnumerable() : _store.Where(predicate);
}

// Uso:
public class Product : IEntity
{
    public int Id { get; init; }
    public string Name { get; set; } = "";
    public decimal Price { get; set; }
}

var repo = new GenericRepository<Product>();
repo.Add(new Product { Id = 1, Name = "Laptop", Price = 1200m });
repo.Add(new Product { Id = 2, Name = "Mouse", Price = 25m });

var expensive = repo.GetAll(p => p.Price > 100);
// [Product { Id = 1, Name = "Laptop", Price = 1200 }]

repo.Update(new Product { Id = 2, Name = "Gaming Mouse", Price = 75m });
var mouse = repo.GetById(2);
// Product { Id = 2, Name = "Gaming Mouse", Price = 75 }
```

**Puntos clave:**
- `where T : class` es necesario para que `FirstOrDefault` pueda retornar `null` (tipo referencia). Sin este constraint, `T?` no seria nullable para value types.
- `where T : IEntity` garantiza acceso a `e.Id` sin casting. Sin este constraint, el compilador no conoce la propiedad `Id`.
- El orden de constraints importa: `class` siempre antes de interfaces.
- `GetAll` usa `Func<T, bool>` como predicate, que es equivalente a `Predicate<T>` pero mas idiomatico con LINQ (LINQ usa `Func<T, bool>` internamente en `Where`).
- La validacion en `Add` y `Update` previene estados inconsistentes — un patron comun en repositorios reales.

</details>

---

## Delegates, Func, Action, Predicate

Un delegate es un tipo que representa una referencia a un metodo con una firma especifica. Son la base de LINQ, los eventos, y los callbacks en C#. Entender delegates es entender como C# soporta programacion funcional.

### Delegate como Tipo

```csharp
// Declarar un delegate type: define la firma que deben tener los metodos compatibles
public delegate int MathOperation(int a, int b);

// Asignar metodos que coincidan con la firma
MathOperation add = (a, b) => a + b;
MathOperation multiply = (a, b) => a * b;

int result1 = add(3, 4);       // 7
int result2 = multiply(3, 4);  // 12

// Un delegate puede referir a metodos con nombre tambien
int Subtract(int a, int b) => a - b;
MathOperation sub = Subtract;
```

### Func, Action y Predicate — Delegates Predefinidos

El framework incluye delegates genericos predefinidos para que no tengas que declarar tipos custom cada vez:

```csharp
// Func<T, TResult> — delegate que RETORNA un valor
// El ultimo type parameter es siempre el tipo de retorno
Func<int, int> square = x => x * x;
Func<string, string, string> concat = (a, b) => $"{a} {b}";
Func<int, bool> isPositive = n => n > 0;

Console.WriteLine(square(5));          // 25
Console.WriteLine(concat("Hello", "World")); // "Hello World"

// Action<T> — delegate que NO retorna valor (void)
Action<string> log = message => Console.WriteLine($"[LOG] {message}");
Action<string, int> repeat = (text, times) =>
{
    for (int i = 0; i < times; i++) Console.Write(text);
    Console.WriteLine();
};

log("Starting process");   // [LOG] Starting process
repeat("*", 5);            // *****

// Predicate<T> — delegate que retorna bool (equivalente a Func<T, bool>)
Predicate<int> isEven = n => n % 2 == 0;
Predicate<string> isLong = s => s.Length > 10;

var numbers = new List<int> { 1, 2, 3, 4, 5, 6 };
var evenNumbers = numbers.FindAll(isEven); // List<T>.FindAll acepta Predicate<T>
// [2, 4, 6]

// Nota: LINQ usa Func<T, bool> en vez de Predicate<T>
var sameResult = numbers.Where(n => isEven(n)).ToList();
```

### Lambdas como Shorthand de Delegates

```csharp
// Lambda expression — forma concisa de definir un delegate inline
Func<int, int, int> add = (a, b) => a + b;

// Lambda statement — cuando necesitas multiples lineas
Func<int, string> classify = n =>
{
    if (n > 0) return "positive";
    if (n < 0) return "negative";
    return "zero";
};

// Lambdas en LINQ — el uso mas comun
var products = new List<string> { "Laptop", "Mouse", "Keyboard" };
var sorted = products
    .Where(p => p.Length > 5)        // lambda como Func<string, bool>
    .OrderBy(p => p)                 // lambda como Func<string, string>
    .Select(p => p.ToUpper());       // lambda como Func<string, string>
// ["KEYBOARD", "LAPTOP"]
```

### Multicast Delegates

Un delegate puede encadenar multiples metodos. Al invocarlo, todos se ejecutan en orden:

```csharp
Action<string> consoleHandler = message => Console.WriteLine($"[Console] {message}");
Action<string> fileHandler   = message => Console.WriteLine($"[File] {message}");
Action<string> dbHandler     = message => Console.WriteLine($"[DB] {message}");

// Guardar referencias ANTES de agregar — son necesarias para poder remover despues
Action<string> handler = consoleHandler;
handler += fileHandler;
handler += dbHandler;

handler("Order received");
// [Console] Order received
// [File] Order received
// [DB] Order received

// Remover un handler: DEBE ser la misma instancia que se uso con +=
handler -= fileHandler; // ✓ funciona — fileHandler apunta al mismo objeto delegate

// Multicast con Func: solo retorna el resultado del ULTIMO metodo
Func<int, int> pipeline = x => x + 1;
pipeline += x => x * 2;
var result = pipeline(5); // 10 (solo el resultado de x * 2)
// Para encadenar resultados de Func, usa composicion (ver Challenge 4)
```

### Challenge 4: Pipeline de Transformacion con Func

Implementa un metodo `Compose` que reciba un array de funciones `Func<T, T>` y retorne una sola funcion que las ejecute en secuencia (el output de una es el input de la siguiente). Luego aplica el pipeline a una coleccion.

```csharp
// Implementa Compose:
public static Func<T, T> Compose<T>(params Func<T, T>[] functions)
{
    // Tu implementacion aqui
}

// Debe funcionar asi:
var pipeline = Compose<int>(
    x => x * 2,      // paso 1: duplicar
    x => x + 10,     // paso 2: sumar 10
    x => x * x       // paso 3: elevar al cuadrado
);

Console.WriteLine(pipeline(3)); // (3*2 + 10)^2 = 16^2 = 256

// Aplicar a una coleccion:
var numbers = new List<int> { 1, 2, 3, 4, 5 };
var transformed = numbers.Select(pipeline).ToList();
// [144, 196, 256, 324, 400]
```

<details>
<summary>Ver solucion</summary>

```csharp
public static Func<T, T> Compose<T>(params Func<T, T>[] functions)
{
    return input => functions.Aggregate(input, (current, func) => func(current));
}

// Uso:
var pipeline = Compose<int>(
    x => x * 2,
    x => x + 10,
    x => x * x
);

Console.WriteLine(pipeline(3)); // 256
// Paso a paso: 3 → 6 → 16 → 256

// Aplicar a una coleccion con Select:
var numbers = new List<int> { 1, 2, 3, 4, 5 };
var transformed = numbers.Select(pipeline).ToList();
// 1 → 2 → 12 → 144
// 2 → 4 → 14 → 196
// 3 → 6 → 16 → 256
// 4 → 8 → 18 → 324
// 5 → 10 → 20 → 400

Console.WriteLine(string.Join(", ", transformed));
// 144, 196, 256, 324, 400

// Version alternativa sin Aggregate (mas explicita):
public static Func<T, T> ComposeExplicit<T>(params Func<T, T>[] functions)
{
    return input =>
    {
        var result = input;
        foreach (var func in functions)
            result = func(result);
        return result;
    };
}
```

**Puntos clave:**
- `Aggregate` es el operador LINQ de reduccion — aplica una funcion acumuladora a cada elemento, pasando el resultado parcial al siguiente. Aqui reduce un array de funciones a una sola funcion compuesta.
- `Compose` es un patron de programacion funcional fundamental. En C# se implementa con `Func<T, T>` y `Aggregate`.
- La composicion de funciones permite crear pipelines reutilizables que se pasan como parametro a `Select`, `Where`, etc.
- En produccion, este patron se usa en middleware pipelines (ASP.NET Core), validacion encadenada y ETL pipelines.

</details>

---

## Eventos

Los eventos son delegates con restricciones de acceso. Solo la clase que declara el evento puede invocarlo (raise). Los suscriptores externos solo pueden agregar o quitar handlers con `+=` y `-=`. Este patron encapsula la comunicacion entre objetos sin acoplamiento directo.

### Event Keyword y EventHandler

```csharp
// EventArgs personalizado — contiene los datos del evento
public class OrderPlacedEventArgs : EventArgs
{
    public int OrderId { get; init; }
    public string CustomerId { get; init; } = "";
    public decimal Amount { get; init; }
}

// Publisher — la clase que declara y dispara el evento
public class OrderService
{
    // Declaracion del evento con EventHandler<T> (convencion .NET)
    public event EventHandler<OrderPlacedEventArgs>? OrderPlaced;

    public void PlaceOrder(int orderId, string customerId, decimal amount)
    {
        // ... logica de negocio ...
        Console.WriteLine($"Order {orderId} placed for ${amount}");

        // Disparar el evento (solo el owner puede invocar)
        OrderPlaced?.Invoke(this, new OrderPlacedEventArgs
        {
            OrderId = orderId,
            CustomerId = customerId,
            Amount = amount
        });
    }
}

// Subscriber — se suscribe al evento
var service = new OrderService();
service.OrderPlaced += (sender, e) =>
    Console.WriteLine($"[Email] Sending confirmation for order {e.OrderId} to {e.CustomerId}");
service.OrderPlaced += (sender, e) =>
    Console.WriteLine($"[Analytics] Logged order {e.OrderId}: ${e.Amount}");

service.PlaceOrder(1, "CUST-42", 99.99m);
// Order 1 placed for $99.99
// [Email] Sending confirmation for order 1 to CUST-42
// [Analytics] Logged order 1: $99.99
```

### Delegate vs Event — Pregunta de Entrevista

```csharp
// Con delegate publico (SIN event keyword):
public class UnsafePublisher
{
    public Action<string>? OnMessage; // cualquiera puede invocar o reasignar

    public void Send(string msg) => OnMessage?.Invoke(msg);
}

var pub = new UnsafePublisher();
pub.OnMessage += msg => Console.WriteLine(msg);

// PELIGRO: un consumidor puede hacer esto:
pub.OnMessage = null;        // borra TODOS los suscriptores
pub.OnMessage("hijacked!");  // invoca el delegate desde fuera — rompe encapsulacion

// Con event keyword:
public class SafePublisher
{
    public event Action<string>? OnMessage;

    public void Send(string msg) => OnMessage?.Invoke(msg);
}

var safe = new SafePublisher();
safe.OnMessage += msg => Console.WriteLine(msg);

// safe.OnMessage = null;       // ERROR DE COMPILACION — solo += y -=
// safe.OnMessage("hijacked!"); // ERROR DE COMPILACION — solo el owner puede invocar
```

> **Pregunta de entrevista:** "Por que no usar un delegate publico directamente?" Respuesta: porque cualquier consumidor podria reasignar el delegate (borrando todos los suscriptores) o invocarlo directamente. El keyword `event` restringe el acceso externo a solo `+=` y `-=`, protegiendo la encapsulacion del publisher.

### Challenge 5: Sistema de Notificaciones E-commerce

Implementa un sistema de notificaciones para un e-commerce con los siguientes requisitos:

- Un `OrderPlacedEventArgs` con propiedades: `OrderId` (int), `CustomerId` (string), `Amount` (decimal)
- Un `OrderProcessor` que:
  - Tiene un evento `OrderPlaced` usando `EventHandler<OrderPlacedEventArgs>`
  - Tiene un metodo `ProcessOrder(int orderId, string customerId, decimal amount)` que dispara el evento
- Tres handlers independientes (pueden ser clases o lambdas):
  - `EmailNotifier`: imprime `"[Email] Order {OrderId} confirmation sent to {CustomerId}"`
  - `InventoryManager`: imprime `"[Inventory] Updating stock for order {OrderId}"`
  - `AnalyticsTracker`: imprime `"[Analytics] Order {OrderId} - ${Amount} tracked"`

Bonus: implementa un mecanismo para desuscribir el `AnalyticsTracker` despues de la primera orden.

```csharp
// Tu implementacion aqui
```

<details>
<summary>Ver solucion</summary>

```csharp
// EventArgs personalizado
public class OrderPlacedEventArgs : EventArgs
{
    public int OrderId { get; init; }
    public string CustomerId { get; init; } = "";
    public decimal Amount { get; init; }
}

// Publisher
public class OrderProcessor
{
    public event EventHandler<OrderPlacedEventArgs>? OrderPlaced;

    public void ProcessOrder(int orderId, string customerId, decimal amount)
    {
        Console.WriteLine($"Processing order {orderId}...");

        // Dispara el evento — el patron ?.Invoke es null-safe
        OrderPlaced?.Invoke(this, new OrderPlacedEventArgs
        {
            OrderId = orderId,
            CustomerId = customerId,
            Amount = amount
        });
    }
}

// Handlers como clases (mas organizado para produccion)
public class EmailNotifier
{
    public void OnOrderPlaced(object? sender, OrderPlacedEventArgs e)
        => Console.WriteLine($"[Email] Order {e.OrderId} confirmation sent to {e.CustomerId}");
}

public class InventoryManager
{
    public void OnOrderPlaced(object? sender, OrderPlacedEventArgs e)
        => Console.WriteLine($"[Inventory] Updating stock for order {e.OrderId}");
}

public class AnalyticsTracker
{
    public void OnOrderPlaced(object? sender, OrderPlacedEventArgs e)
        => Console.WriteLine($"[Analytics] Order {e.OrderId} - ${e.Amount} tracked");
}

// Wiring
var processor = new OrderProcessor();
var emailNotifier = new EmailNotifier();
var inventoryManager = new InventoryManager();
var analyticsTracker = new AnalyticsTracker();

processor.OrderPlaced += emailNotifier.OnOrderPlaced;
processor.OrderPlaced += inventoryManager.OnOrderPlaced;
processor.OrderPlaced += analyticsTracker.OnOrderPlaced;

processor.ProcessOrder(1, "CUST-42", 199.99m);
// Processing order 1...
// [Email] Order 1 confirmation sent to CUST-42
// [Inventory] Updating stock for order 1
// [Analytics] Order 1 - $199.99 tracked

// Bonus: desuscribir analytics despues de la primera orden
processor.OrderPlaced -= analyticsTracker.OnOrderPlaced;

processor.ProcessOrder(2, "CUST-99", 49.99m);
// Processing order 2...
// [Email] Order 2 confirmation sent to CUST-99
// [Inventory] Updating stock for order 2
// (Analytics ya no se ejecuta)
```

**Puntos clave:**
- `EventHandler<TEventArgs>` es la convencion de .NET para eventos. La firma es `(object? sender, TEventArgs e)`.
- `?.Invoke(...)` es null-safe — si no hay suscriptores, no lanza `NullReferenceException`.
- Los handlers son metodos de instancia de clases separadas — esto permite que cada handler tenga su propio estado y dependencias (en produccion, se inyectan via DI).
- **Cuidado con memory leaks:** si el publisher vive mas tiempo que el subscriber, el subscriber no sera recolectado por el GC porque el evento mantiene una referencia. En aplicaciones de larga vida, siempre desuscribirse con `-=` cuando ya no se necesita el handler.

</details>

---

## Resumen: Puntos Clave para la Entrevista

- **Que es deferred execution?** Las queries LINQ no se ejecutan cuando se definen, sino cuando se iteran (`ToList()`, `foreach`, `Count()`). Esto permite componer queries sin overhead y refleja cambios en la fuente de datos.

- **IQueryable vs IEnumerable?** `IQueryable` construye expression trees que se traducen a SQL (filtro en la DB). `IEnumerable` ejecuta todo en memoria. Usar `IQueryable` con EF Core y materializar solo al final.

- **Que son los generic constraints?** Restricciones que limitan los tipos validos para un type parameter: `class`, `struct`, `new()`, interfaces y tipos base. Garantizan type safety en tiempo de compilacion.

- **Covarianza vs contravarianza?** Covarianza (`out`) permite usar un tipo mas derivado donde se espera uno menos derivado (ej. `IEnumerable<Dog>` a `IEnumerable<Animal>`). Contravarianza (`in`) es lo opuesto (ej. `Action<Animal>` a `Action<Dog>`).

- **Func vs Action vs Predicate?** `Func<T, TResult>` retorna un valor, `Action<T>` no retorna nada (void), `Predicate<T>` retorna bool. Los tres son delegates genericos predefinidos. LINQ usa `Func<T, bool>` en vez de `Predicate<T>`.

- **Delegate vs event?** Un delegate publico permite a cualquiera reasignar (`=`) o invocar directamente. Un `event` restringe el acceso externo a `+=` y `-=`, y solo el owner puede invocarlo. Siempre usar `event` para comunicacion publisher-subscriber.

- **SelectMany vs Select?** `Select` transforma cada elemento (1:1). `SelectMany` transforma cada elemento en una coleccion y las aplana en una sola secuencia (1:N). Usar `SelectMany` para "aplanar" colecciones anidadas.
