# C# Junior Interview Questions

Question-answer interview preparation file for C# at Junior level.

## Question 001

### Question
What is C#?

### Answer
C# is a modern, statically typed, object-oriented programming language developed by Microsoft. It runs primarily on the .NET platform and is used for backend services, desktop applications, games, mobile apps, cloud applications, and tooling.

## Question 002

### Question
What is .NET?

### Answer
.NET is a development platform that includes a runtime, base class libraries, SDK tools, language compilers, and application frameworks. C# is one of the main languages used with .NET.

## Question 003

### Question
What is the CLR?

### Answer
The Common Language Runtime is the execution engine for .NET applications. It manages memory, loads assemblies, verifies code, performs JIT compilation, handles exceptions, and provides services such as garbage collection.

## Question 004

### Question
What is managed code?

### Answer
Managed code is code executed under the control of the CLR. The runtime manages memory, type safety, exception handling, and other execution services.

## Question 005

### Question
What is the difference between value types and reference types?

### Answer
Value types usually store their data directly and are commonly allocated inline or on the stack depending on context. Reference types store a reference to an object, usually on the managed heap. Structs, enums, and primitive numeric types are value types; classes, strings, arrays, and delegates are reference types.

## Question 006

### Question
What is a class?

### Answer
A class is a reference type that defines data and behavior through fields, properties, methods, constructors, events, and nested types. Instances of classes are objects.

## Question 007

### Question
What is an object?

### Answer
An object is an instance of a type, usually a class. It contains state and exposes behavior defined by its type.

## Question 008

### Question
What is a struct?

### Answer
A struct is a value type. It is useful for small, immutable data-like values. Structs should generally be kept small because copying them copies their contents.

## Question 009

### Question
What is an enum?

### Answer
An enum defines a named set of integral constants. It improves readability when a variable can take one of a known set of values.

## Question 010

### Question
What is a namespace?

### Answer
A namespace organizes types and helps avoid name collisions. It is not the same as an assembly or a folder, although projects often align folders and namespaces.

## Question 011

### Question
What is a variable?

### Answer
A variable is a named storage location with a type. In C#, local variables must be definitely assigned before use.

## Question 012

### Question
What is type inference with var?

### Answer
`var` tells the compiler to infer the static type from the initializer. It does not make the variable dynamic.

## Question 013

### Question
What is the difference between var and dynamic?

### Answer
`var` is resolved at compile time and keeps static typing. `dynamic` defers member binding to runtime and bypasses many compile-time checks.

## Question 014

### Question
What is a method?

### Answer
A method is a function that belongs to a type. It can have parameters, a return type, access modifiers, and can be static or instance-based.

## Question 015

### Question
What is a constructor?

### Answer
A constructor initializes a new instance of a type. It has the same name as the type and no return type.

## Question 016

### Question
What is method overloading?

### Answer
Method overloading means defining multiple methods with the same name but different parameter lists. The compiler chooses the best overload at compile time.

## Question 017

### Question
What is method overriding?

### Answer
Method overriding means replacing a virtual or abstract base class method implementation in a derived class using the `override` keyword.

## Question 018

### Question
What is inheritance?

### Answer
Inheritance allows a class to derive from another class and reuse or extend its behavior. C# supports single class inheritance but multiple interface implementation.

## Question 019

### Question
What is an interface?

### Answer
An interface defines a contract that implementing types must satisfy. It can define methods, properties, events, indexers, and in modern C#, default implementations.

## Question 020

### Question
What is polymorphism?

### Answer
Polymorphism allows code to work with objects through a common base type or interface while executing the actual runtime implementation.

## Question 021

### Question
What is encapsulation?

### Answer
Encapsulation means hiding internal implementation details and exposing a controlled public API.

## Question 022

### Question
What are access modifiers?

### Answer
Access modifiers control visibility. Common modifiers are `public`, `private`, `protected`, `internal`, `protected internal`, and `private protected`.

## Question 023

### Question
What is a property?

### Answer
A property exposes data through get and set accessors. It looks like a field to callers but can contain logic.

## Question 024

### Question
What is an auto-property?

### Answer
An auto-property is a property where the compiler generates the backing field automatically, for example `public string Name { get; set; }`.

## Question 025

### Question
What is a field?

### Answer
A field is a variable declared directly in a type. Fields are usually kept private and exposed through properties when needed.

## Question 026

### Question
What is static?

### Answer
`static` means a member belongs to the type itself rather than to a specific instance.

## Question 027

### Question
What is readonly?

### Answer
`readonly` means a field can be assigned only during declaration or in a constructor. It prevents reassignment after construction.

## Question 028

### Question
What is const?

### Answer
`const` defines a compile-time constant. Its value is embedded at compile time into consuming assemblies.

## Question 029

### Question
What is the difference between const and readonly?

### Answer
`const` is compile-time and implicitly static. `readonly` is runtime and can be assigned in a constructor. Use `readonly` when the value may be calculated or versioning matters.

## Question 030

### Question
What is nullable reference type syntax?

### Answer
Nullable reference types allow annotations such as `string?` to express that a reference may be null. The compiler uses this information for warnings.

## Question 031

### Question
What is null?

### Answer
`null` means a reference does not point to an object. Dereferencing null causes a `NullReferenceException`.

## Question 032

### Question
How do you avoid NullReferenceException?

### Answer
Use proper initialization, nullable annotations, guard clauses, pattern matching, null-coalescing operators, and avoid returning null when an empty object or collection is clearer.

## Question 033

### Question
What is an array?

### Answer
An array is a fixed-size indexed collection of elements of the same type.

## Question 034

### Question
What is List<T>?

### Answer
`List<T>` is a resizable generic collection backed by an array. It is commonly used when the number of items changes.

## Question 035

### Question
What is Dictionary<TKey,TValue>?

### Answer
`Dictionary<TKey,TValue>` stores key-value pairs and provides fast lookup by key on average.

## Question 036

### Question
What is a generic type?

### Answer
A generic type uses type parameters, such as `List<T>`, so the same code can work with different concrete types while preserving type safety.

## Question 037

### Question
What is boxing?

### Answer
Boxing converts a value type to an object or interface reference by wrapping it in a heap object.

## Question 038

### Question
What is unboxing?

### Answer
Unboxing extracts the value type from a boxed object. It requires the correct target type and may throw an exception if the type is wrong.

## Question 039

### Question
What is an exception?

### Answer
An exception represents an error or exceptional condition during execution. It can be thrown, caught, filtered, and finally handled.

## Question 040

### Question
What is try/catch/finally?

### Answer
`try` contains code that may fail, `catch` handles exceptions, and `finally` runs cleanup code whether an exception occurs or not.

## Question 041

### Question
What is using statement?

### Answer
`using` ensures that an `IDisposable` object is disposed at the end of a scope, even when an exception occurs.

## Question 042

### Question
What is IDisposable?

### Answer
`IDisposable` defines a `Dispose` method for deterministic cleanup of resources such as files, streams, database connections, handles, or subscriptions.

## Question 043

### Question
What is garbage collection?

### Answer
Garbage collection automatically reclaims memory used by managed objects that are no longer reachable. It does not replace deterministic cleanup for external resources.

## Question 044

### Question
What is string immutability?

### Answer
Strings in C# are immutable. Operations that appear to modify a string create a new string instead.

## Question 045

### Question
When should you use StringBuilder?

### Answer
Use `StringBuilder` when constructing a string through many modifications, especially in loops, to avoid excessive intermediate string allocations.

## Question 046

### Question
What is LINQ?

### Answer
LINQ is a set of language and library features for querying collections, databases, XML, and other data sources using a consistent syntax.

## Question 047

### Question
What is deferred execution?

### Answer
Deferred execution means a LINQ query is not evaluated until it is enumerated. This can affect performance and behavior if the source changes.

## Question 048

### Question
What is IEnumerable<T>?

### Answer
`IEnumerable<T>` represents a sequence that can be enumerated. It is the basis for `foreach` and many LINQ operations.

## Question 049

### Question
What is IEnumerator<T>?

### Answer
`IEnumerator<T>` performs the actual iteration over a sequence and exposes `Current` and `MoveNext()`.

## Question 050

### Question
What is async/await?

### Answer
`async` and `await` simplify asynchronous programming. An async method can suspend while waiting for an operation and resume later without blocking the thread.

## Question 051

### Question
What is Task?

### Answer
`Task` represents an asynchronous operation. `Task<T>` represents an operation that eventually produces a value of type `T`.

## Question 052

### Question
What is the difference between Task and Thread?

### Answer
A thread is an OS execution resource. A task is a higher-level abstraction for asynchronous work and may or may not occupy a dedicated thread.

## Question 053

### Question
What is await doing?

### Answer
`await` waits asynchronously for an awaitable operation to complete. It does not block the calling thread in the same way as synchronous waiting.

## Question 054

### Question
What is a delegate?

### Answer
A delegate is a type-safe reference to a method with a specific signature.

## Question 055

### Question
What is an event?

### Answer
An event is a controlled delegate-based notification mechanism. It allows subscribers to be notified while preventing external code from invoking the event directly.

## Question 056

### Question
What is a lambda expression?

### Answer
A lambda expression is an inline anonymous function, for example `x => x * 2`.

## Question 057

### Question
What is Action?

### Answer
`Action` is a delegate type for methods that return void.

## Question 058

### Question
What is Func?

### Answer
`Func` is a delegate type for methods that return a value.

## Question 059

### Question
What is Predicate<T>?

### Answer
`Predicate<T>` is a delegate that takes a value of type `T` and returns a boolean.

## Question 060

### Question
What is pattern matching?

### Answer
Pattern matching checks whether a value has a certain shape, type, or property structure and can extract data from it.

## Question 061

### Question
What is switch expression?

### Answer
A switch expression is an expression-based form of switch that returns a value and works well with pattern matching.

## Question 062

### Question
What is record?

### Answer
A record is a type designed for immutable data models with value-based equality by default.

## Question 063

### Question
What is the difference between class and record?

### Answer
Classes usually use reference equality by default. Records use value-based equality by default and provide convenient syntax for immutable data.

## Question 064

### Question
What is the difference between == and Equals?

### Answer
`==` is an operator that can be overloaded. `Equals` is a virtual method used for equality comparison. Their behavior depends on the type.

## Question 065

### Question
What is GetHashCode?

### Answer
`GetHashCode` returns an integer hash used by hash-based collections. Equal objects must return the same hash code.

## Question 066

### Question
What is extension method?

### Answer
An extension method lets you add method-like syntax to an existing type without modifying that type.

## Question 067

### Question
What is partial class?

### Answer
A partial class allows a type definition to be split across multiple files. It is often used by generated code and designers.

## Question 068

### Question
What is async void?

### Answer
`async void` is an async method that cannot be awaited and propagates exceptions differently. It should generally be limited to event handlers.

## Question 069

### Question
What is a NuGet package?

### Answer
A NuGet package is a distributable unit of .NET code, metadata, and dependencies.

## Question 070

### Question
What is an assembly?

### Answer
An assembly is a compiled .NET unit, usually a DLL or EXE, containing IL code, metadata, and resources.

## Question 071

### Question
What is IL?

### Answer
Intermediate Language is the CPU-independent code emitted by .NET compilers and executed by the CLR after JIT compilation.

## Question 072

### Question
What is JIT compilation?

### Answer
JIT compilation converts IL into native machine code at runtime.

## Question 073

### Question
What is AOT compilation?

### Answer
Ahead-of-time compilation compiles code before runtime. In .NET, Native AOT can reduce startup overhead and remove the need for JIT in some scenarios.

## Question 074

### Question
What is a project file?

### Answer
A `.csproj` file describes a .NET project, including target frameworks, package references, build options, and included files.

## Question 075

### Question
What is target framework?

### Answer
A target framework specifies the .NET platform version or API surface the project is built against, such as `net8.0`.

## Question 076

### Question
What is dependency injection?

### Answer
Dependency injection is a design technique where dependencies are provided from outside instead of being created directly inside a class.

## Question 077

### Question
Why use dependency injection?

### Answer
It improves testability, separation of concerns, and configurability. It also reduces hard-coded coupling.

## Question 078

### Question
What is unit testing?

### Answer
Unit testing verifies small units of code in isolation. In C#, common frameworks include xUnit, NUnit, and MSTest.

## Question 079

### Question
What is mocking?

### Answer
Mocking replaces real dependencies with controlled test doubles to isolate the unit under test.

## Question 080

### Question
What is serialization?

### Answer
Serialization converts an object to a transferable or storable format such as JSON. Deserialization converts it back.

## Question 081

### Question
What is JSON?

### Answer
JSON is a text format commonly used for data exchange between services and clients.

## Question 082

### Question
What is ASP.NET Core?

### Answer
ASP.NET Core is a cross-platform framework for building web applications, APIs, and services on .NET.

## Question 083

### Question
What is middleware?

### Answer
Middleware is a component in the ASP.NET Core request pipeline that can inspect, modify, handle, or pass along HTTP requests and responses.

## Question 084

### Question
What is dependency lifetime?

### Answer
Dependency lifetime controls how long a service instance lives. Common lifetimes are transient, scoped, and singleton.

## Question 085

### Question
What is a singleton service?

### Answer
A singleton service has one instance for the application's lifetime. It must be thread-safe if used concurrently.

## Question 086

### Question
What is a scoped service?

### Answer
A scoped service is created once per scope, commonly once per HTTP request in ASP.NET Core.

## Question 087

### Question
What is a transient service?

### Answer
A transient service is created each time it is requested.

## Question 088

### Question
What is logging?

### Answer
Logging records diagnostic and operational information. Good logging helps debug and monitor applications without exposing sensitive data.

## Question 089

### Question
What is configuration?

### Answer
Configuration is external data controlling application behavior, such as appsettings files, environment variables, or command-line arguments.

## Question 090

### Question
What is appsettings.json?

### Answer
`appsettings.json` is a common configuration file in .NET applications.

## Question 091

### Question
What is environment variable configuration?

### Answer
Environment variables allow configuration values to be supplied by the operating system or hosting environment, useful for deployment-specific settings.

## Question 092

### Question
What is SOLID?

### Answer
SOLID is a set of object-oriented design principles: Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, and Dependency Inversion.

## Question 093

### Question
What is clean code?

### Answer
Clean code is readable, maintainable, understandable code with clear names, small focused units, minimal duplication, and explicit intent.

## Question 094

### Question
What should a junior C# developer know before working on production code?

### Answer
A junior should understand syntax, types, null handling, collections, exceptions, async basics, debugging, source control, tests, and the basic structure of the project they are changing.
