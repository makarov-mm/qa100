# C# JuniorPlus Interview Questions

Question-answer interview preparation file for C# at JuniorPlus level.

## Question 001

### Question
Explain the difference between IEnumerable<T> and IQueryable<T>.

### Answer
`IEnumerable<T>` represents in-memory iteration: LINQ operators run as compiled delegates over objects already in the process. `IQueryable<T>` builds an expression tree that a provider (e.g. EF Core) translates into another language such as SQL, so filtering and projection happen at the source. The classic failure is calling `.AsEnumerable()` or a non-translatable method too early, which pulls the whole table into memory and then filters client-side. Rule of thumb: keep the query as `IQueryable` until the database has done all the work it can, then materialize.

## Question 002

### Question
What problems can deferred LINQ execution cause?

### Answer
LINQ queries are not executed when defined but when enumerated, so the same query variable can run multiple times and hit the database or re-evaluate side effects each time. This causes duplicated work, inconsistent results if the underlying data changed between enumerations, and "captured variable" surprises inside loops. With EF Core it can also throw because the `DbContext` was disposed before enumeration. Materialize with `ToList()`/`ToArray()` once when you need a stable snapshot.

## Question 003

### Question
How does async/await transform code conceptually?

### Answer
The compiler rewrites an `async` method into a state machine: each `await` becomes a suspension point where the method returns to its caller and registers a continuation to resume when the awaited operation completes. No thread is blocked while waiting on I/O; the continuation is scheduled later, possibly on a different thread. The mental model is "cooperative resumption," not "run on a background thread" — `async` by itself adds no parallelism, only the ability to yield while waiting.

## Question 004

### Question
What is synchronization context?

### Answer
A `SynchronizationContext` abstracts "where should continuations run." UI frameworks (WPF, WinForms) install one that marshals work back to the single UI thread; ASP.NET Core has none. By default `await` captures the current context and resumes on it, which is why UI updates after an `await` are safe. It is also the root cause of context-related deadlocks and of unnecessary thread hops in library code.

## Question 005

### Question
Why can .Result or .Wait() cause deadlocks?

### Answer
On a thread with a single-threaded synchronization context (UI thread, legacy ASP.NET), blocking on `.Result`/`.Wait()` holds that thread, while the awaited continuation is queued to resume on the very same thread — which is now blocked. Neither can proceed, so it deadlocks. The fix is to be async all the way up (`await` instead of blocking), and in library code use `ConfigureAwait(false)` so continuations do not require the captured context.

## Question 006

### Question
What is ConfigureAwait(false)?

### Answer
`ConfigureAwait(false)` tells the awaiter not to capture and resume on the current synchronization context, allowing the continuation to run on any thread-pool thread. It avoids the deadlock above and removes a context-marshaling hop, so it is recommended in library code that does not touch UI. In app-level code that must touch the UI after the await, you omit it (or use `true`). In ASP.NET Core it is largely a no-op because there is no context, but it remains good library hygiene.

## Question 007

### Question
What is cancellation in .NET?

### Answer
Cancellation is cooperative: a `CancellationTokenSource` produces a `CancellationToken`, and code voluntarily checks `token.ThrowIfCancellationRequested()` or passes the token into async APIs that observe it. Nothing is forcibly aborted — a method that ignores the token cannot be cancelled. Cancellation surfaces as `OperationCanceledException`, which should generally propagate rather than be swallowed, so callers can distinguish cancellation from failure.

## Question 008

### Question
How should CancellationToken be propagated?

### Answer
Accept a `CancellationToken` parameter (conventionally last, often defaulted to `default`) and pass it down to every async call you make, all the way to the I/O boundary. Do not create a new token internally that ignores the caller's; that breaks the chain. For combined deadlines use `CancellationTokenSource.CreateLinkedTokenSource`. The common bug is accepting the token and then never forwarding it, so cancellation silently does nothing.

## Question 009

### Question
What is the difference between throw and throw ex?

### Answer
`throw;` re-throws the current exception while preserving its original stack trace. `throw ex;` throws the same object but resets the stack trace to the current line, destroying the information about where the error actually originated. Always use bare `throw;` in a catch block when rethrowing. `throw ex;` is one of the most common debugging-hostile mistakes in production code.

## Question 010

### Question
What is exception filtering?

### Answer
Exception filters use `catch (Exception e) when (condition)` to decide whether a catch block applies without entering it. Because the filter runs before the stack unwinds, it preserves more context and avoids catching-then-rethrowing. It is useful for conditional handling (e.g. only handle `SqlException` with a specific error number) and for logging via a filter that always returns false as a side-effect-only inspection point.

## Question 011

### Question
What is a custom exception and when should it be used?

### Answer
A custom exception is a class deriving from `Exception` (usually directly, or from a domain base) that conveys a specific failure with meaningful type and properties. Use one when callers need to handle a condition distinctly from generic failures, or to attach structured data. Avoid creating custom types for situations where a built-in like `ArgumentException` or `InvalidOperationException` already fits — over-proliferation of exception types adds noise without value. Always provide the standard constructors and keep them serializable if they may cross boundaries.

## Question 012

### Question
What is pattern matching with property patterns?

### Answer
Property patterns match an object's shape by inspecting members: `if (order is { Status: OrderStatus.Paid, Total: > 100 })`. They compose with `switch` expressions, positional patterns, and `and`/`or`/`not` to express complex conditions declaratively. They reduce nested null checks and casts, but overuse can hide branching complexity, so keep arms readable. Combined with records they make a concise alternative to visitor-style dispatch.

## Question 013

### Question
What is a nullable reference warning?

### Answer
With nullable reference types enabled, the compiler tracks whether a reference may be null and warns when you dereference a possibly-null value or assign null to a non-nullable reference. The warnings are static analysis only — they do not change runtime behavior and can be silenced, so discipline matters. Treating them as errors (`<WarningsAsErrors>`) is how teams actually get the safety benefit; ignoring them leaves the annotations as misleading documentation.

## Question 014

### Question
What does the null-forgiving operator ! do?

### Answer
The `!` (null-forgiving) operator tells the compiler "trust me, this is not null here," suppressing the nullable warning without any runtime check. It does not prevent a `NullReferenceException`; it only silences analysis. It is appropriate when you have invariant knowledge the compiler cannot see (e.g. after a `TryGetValue` that you know succeeded), but scattered `!` usage usually signals weak nullability modeling that should be fixed at the source instead.

## Question 015

### Question
What is covariance in C# generics?

### Answer
Covariance, declared with `out`, lets you use a more-derived type argument where a less-derived one is expected: `IEnumerable<string>` is assignable to `IEnumerable<object>`. It is safe only for output positions (return values), because you can only take items out, never put incompatible items in. This is why `IEnumerable<out T>` is covariant but `IList<T>` is not.

## Question 016

### Question
What is contravariance in C# generics?

### Answer
Contravariance, declared with `in`, lets a generic type accept a less-derived type argument where a more-derived one is expected: an `IComparer<object>` can be used as an `IComparer<string>`. It is safe only for input positions (parameters), because the type is consumed, not produced. `Action<in T>` is the canonical example.

## Question 017

### Question
What is invariance?

### Answer
Invariance means a generic type with type argument `T` is compatible only with exactly `T`, no variance in either direction. `List<string>` is not a `List<object>` even though `string` derives from `object`, because lists allow both reading and writing, so neither covariance nor contravariance is type-safe. Most concrete generic types are invariant; variance is opt-in on interfaces and delegates only.

## Question 018

### Question
What is the difference between IEnumerable<out T> and IList<T>?

### Answer
`IEnumerable<out T>` is a read-only, forward-only, covariant sequence — you can iterate but not index, count, or modify in O(1). `IList<T>` is invariant and adds indexed access, `Count`, and mutation. Exposing `IEnumerable<T>` from APIs keeps callers from mutating your collection and enables lazy/streamed implementations; returning `IList<T>` commits you to an in-memory, mutable contract. Choose the narrowest type that satisfies the consumer.

## Question 019

### Question
What is an iterator method using yield return?

### Answer
A method that uses `yield return` is compiled into a state machine producing an `IEnumerable<T>` lazily: each `MoveNext()` runs the method up to the next `yield`, then suspends. This streams values without materializing the whole sequence, which is memory-efficient for large or infinite sequences. The body does not execute at all until enumeration begins, which surprises people expecting eager side effects.

## Question 020

### Question
What are the pitfalls of yield return?

### Answer
Because execution is deferred, argument validation in the method body runs lazily, not at call time — so guard clauses should be in a non-iterator wrapper. Enumerating twice re-runs the whole pipeline, which can double side effects or database hits. Exceptions surface during enumeration, not at the call site, complicating error handling. And `finally`/`using` cleanup runs only if the consumer fully enumerates or disposes the enumerator.

## Question 021

### Question
What is Span<T>?

### Answer
`Span<T>` is a `ref struct` that represents a contiguous region of memory — a slice of an array, stack memory, or unmanaged memory — without copying. It enables high-performance, allocation-free slicing and parsing. Because it is a stack-only type it cannot escape to the heap, which constrains where it can be used but guarantees the referenced memory stays valid.

## Question 022

### Question
What is ReadOnlySpan<T>?

### Answer
`ReadOnlySpan<T>` is the immutable counterpart of `Span<T>`, giving read-only access to a contiguous memory region. It is ideal for parsing and slicing strings (`string` is implicitly convertible to `ReadOnlySpan<char>`) without allocating substrings. Like `Span<T>` it is a `ref struct` with the same escape restrictions.

## Question 023

### Question
Why can Span<T> not be stored in a normal class field?

### Answer
`Span<T>` is a `ref struct`, which the runtime guarantees lives only on the stack. Storing it in a class field, boxing it, capturing it in a closure, or using it across an `await`/`yield` boundary would let it outlive the stack frame whose memory it references, breaking memory safety. The compiler forbids all of these. When you need span-like access on the heap, use `Memory<T>` and convert to a span only within a synchronous scope.

## Question 024

### Question
What is Memory<T>?

### Answer
`Memory<T>` is a heap-storable representation of a contiguous memory region; unlike `Span<T>` it is not a `ref struct`, so it can be a class field, captured in async methods, and passed across `await`. You obtain a `Span<T>` from it via `.Span` only when you need to do the actual synchronous work. It is the type async APIs use (e.g. `ReadAsync(Memory<byte>)`) because spans cannot cross await points.

## Question 025

### Question
What is the difference between Span<T> and Memory<T>?

### Answer
`Span<T>` is stack-only (`ref struct`), zero-overhead, but cannot be stored on the heap or used across async boundaries; `Memory<T>` is heap-friendly and async-friendly but you must call `.Span` to operate on the data. Use `Span<T>` for tight synchronous hot paths and `Memory<T>` to carry buffers through async pipelines or store them in fields. They are designed to be used together: `Memory<T>` for storage/flow, `Span<T>` for the actual access.

## Question 026

### Question
What is a record struct?

### Answer
A `record struct` is a value type with the record features: value-based equality, `with` expressions, and a generated `ToString`. It combines struct copy semantics with the concise immutable-data ergonomics of records. By default its members are mutable unless you use `readonly record struct`, which is usually the safer choice to get true value semantics and avoid defensive-copy surprises.

## Question 027

### Question
What are init-only properties?

### Answer
An `init` accessor allows a property to be set during object initialization (constructor or object initializer) but makes it immutable afterward. This gives you immutable objects that still support the readable object-initializer syntax, without requiring a constructor parameter for every property. It is the foundation of records and of "set once, then read-only" data models.

## Question 028

### Question
What is required member syntax?

### Answer
The `required` modifier forces callers to set a property during initialization, enforced at compile time, so you get mandatory members without writing a constructor. It pairs with `init` to express "must be provided, then immutable." It solves the long-standing tension where object initializers were convenient but could leave mandatory fields unset; the `[SetsRequiredMembers]` attribute lets a constructor satisfy the requirement.

## Question 029

### Question
What is object initializer syntax?

### Answer
Object initializer syntax sets properties or fields directly after construction: `new Point { X = 1, Y = 2 }`. It runs the parameterless (or chosen) constructor first, then assigns each member. It improves readability for configuration-style objects but can leave an object in a partially-valid intermediate state between construction and the last assignment, which `required`/`init` help mitigate.

## Question 030

### Question
What is collection initializer syntax?

### Answer
Collection initializer syntax populates a collection inline: `new List<int> { 1, 2, 3 }`, which the compiler lowers to repeated `Add` calls. It requires the type to implement `IEnumerable` and have an accessible `Add` method. C# 12 collection expressions (`[1, 2, 3]`) generalize this with a unified, target-typed syntax across arrays, lists, and spans.

## Question 031

### Question
What is expression-bodied member syntax?

### Answer
Expression-bodied members use `=>` to define a method, property, or accessor as a single expression: `public int Square => x * x;`. It is purely syntactic sugar for concise one-liners and improves readability for trivial members. It should be avoided when the body is non-trivial, because cramming logic into an expression hurts debuggability and readability.

## Question 032

### Question
What is local function?

### Answer
A local function is a method declared inside another method, with access to the enclosing scope's variables. Unlike a lambda assigned to a delegate, it does not necessarily allocate (no delegate/closure object unless captured by reference), supports recursion and `out` parameters, and reads more clearly for helper logic used only within one method. It is the preferred way to factor out a private helper that no other method needs, and to host argument validation in front of an iterator body.

## Question 033

### Question
What is closure?

### Answer
A closure is a function together with the captured environment of variables from its defining scope. When a lambda or local function references an outer variable, the compiler hoists that variable into a generated class so it outlives the original stack frame. This is powerful but means captured variables are shared by reference, which causes the classic "loop variable captured" bug and can extend object lifetimes unexpectedly.

## Question 034

### Question
What allocation risks can closures introduce?

### Answer
Capturing a variable forces the compiler to allocate a display class on the heap to hold it, plus a delegate allocation. In hot paths (per-request, per-frame, per-element) this produces GC pressure that is easy to miss. Capturing `this` implicitly (by referencing an instance member) can also keep the whole object alive longer than intended. Static lambdas (`static () => ...`) and avoiding capture in hot loops mitigate this.

## Question 035

### Question
What is delegate multicast behavior?

### Answer
A delegate can hold an invocation list of multiple methods; invoking it calls them in order. For a delegate with a return value, only the last invoked method's result is returned, so multicast with non-void returns is usually a design smell. If one subscriber throws, the remaining ones are not invoked unless you enumerate `GetInvocationList()` and call each in a try/catch. This is the underpinning of events.

## Question 036

### Question
What is event memory leak risk?

### Answer
When a long-lived publisher holds a delegate to a subscriber's method, the publisher keeps the subscriber alive through that reference; if the subscriber forgets to unsubscribe, it cannot be garbage-collected. This is the most common managed memory leak in .NET, especially in UI code where short-lived views subscribe to long-lived view models or static events. Symptoms are growing memory and duplicate handler invocations.

## Question 037

### Question
How do you unsubscribe from events safely?

### Answer
Keep a reference to the exact delegate instance you subscribed (anonymous lambdas cannot be unsubscribed because you no longer have the same instance), and detach with `-=` in `Dispose`/teardown. Tie the lifetime explicitly: subscribe when the component activates, unsubscribe when it deactivates. For framework UI, prefer built-in weak patterns or the framework's lifecycle hooks rather than ad-hoc subscription.

## Question 038

### Question
What is weak event pattern conceptually?

### Answer
The weak event pattern lets a publisher reference subscribers through weak references, so a subscriber that is otherwise unreferenced can still be collected even if it never unsubscribed. It breaks the strong publisher→subscriber link that causes leaks. WPF provides `WeakEventManager` for this. The trade-off is added indirection and complexity, so it is used where lifetimes are genuinely hard to manage, not by default.

## Question 039

### Question
What is equality implementation for custom types?

### Answer
Correct equality means overriding `Equals(object)` and `GetHashCode()` consistently (equal objects must return equal hash codes), and usually implementing `IEquatable<T>` to avoid boxing. For value-like types, equality compares the meaningful fields; for entities, it is often identity (an Id). Records generate all of this for you. The classic bug is overriding `Equals` but not `GetHashCode`, which silently breaks dictionaries and hash sets.

## Question 040

### Question
What is IEquatable<T>?

### Answer
`IEquatable<T>` provides a strongly typed `Equals(T)` that avoids the boxing and casting of `Equals(object)`, which matters for value types used as dictionary keys or in hot loops. Collections like `Dictionary<TKey,TValue>` and LINQ's `Contains` use it when available. You should still override `object.Equals` and `GetHashCode` to stay consistent across both call paths.

## Question 041

### Question
What is IComparable<T>?

### Answer
`IComparable<T>` defines `CompareTo` for ordering, returning negative/zero/positive to indicate less/equal/greater. It enables `Array.Sort`, `List.Sort`, and ordered collections to order your type without a custom comparer. The contract must be consistent and total (transitive, antisymmetric); inconsistency between `CompareTo` and `Equals` can produce surprising results in sorted sets. For multiple sort orders, prefer separate `IComparer<T>` implementations.

## Question 042

### Question
What is the difference between shallow copy and deep copy?

### Answer
A shallow copy duplicates the top-level object but shares references to the same nested objects, so mutating a nested object affects both copies. A deep copy recursively duplicates the entire object graph so the copies are fully independent. `MemberwiseClone` is shallow; deep copy requires explicit cloning, serialization round-tripping, or records with `with` plus manual handling of reference members. The choice depends on whether shared mutable state is acceptable.

## Question 043

### Question
What is defensive copying?

### Answer
Defensive copying means copying input or output data at a boundary so external code cannot mutate your internal state through a shared reference. For example, returning a copy of an internal list instead of the list itself, or copying a mutable struct before storing it. It trades allocation cost for invariant safety. With readonly structs and immutable collections you can avoid the copies while keeping the guarantee.

## Question 044

### Question
What is immutability and why is it useful?

### Answer
An immutable object cannot change after construction, so its state is safe to share across threads without locks, safe to use as a dictionary key, and easy to reason about because no one can mutate it behind your back. It eliminates a whole class of aliasing and concurrency bugs. The cost is allocating new objects to represent changes (`with` expressions), which is usually a worthwhile trade for correctness, mitigated by structural sharing in persistent data structures.

## Question 045

### Question
What is thread safety?

### Answer
A type or operation is thread-safe if it behaves correctly when accessed concurrently from multiple threads without external synchronization. Achieving it means protecting shared mutable state (locks, immutability, or atomic operations) so that no thread observes a torn or inconsistent state. Thread safety is a property of a specific usage contract, not a binary label — document whether instance methods, statics, or specific operations are safe.

## Question 046

### Question
What is lock statement?

### Answer
`lock (obj) { ... }` is syntactic sugar over `Monitor.Enter`/`Monitor.Exit` in a try/finally, providing mutual exclusion so only one thread executes the protected region at a time. The lock object should be a private, dedicated, reference-type instance — never `this`, a type, or a string, all of which can be locked by unrelated code and cause deadlocks. Locks do not support async; never `await` inside a `lock`.

## Question 047

### Question
What is Monitor?

### Answer
`Monitor` is the underlying mechanism the `lock` statement uses, exposing `Enter`, `Exit`, `TryEnter` (with timeout), and condition-variable methods `Wait`/`Pulse`/`PulseAll`. You use it directly when you need a timeout on acquisition or condition signaling that `lock` cannot express. It is reentrant per thread, and like `lock` it is synchronous only.

## Question 048

### Question
What is ReaderWriterLockSlim?

### Answer
`ReaderWriterLockSlim` allows many concurrent readers but exclusive writers, which improves throughput for read-heavy shared data compared to a plain lock. It supports upgradeable read locks to avoid the read-then-write race. The trade-offs are higher overhead than a simple lock for low contention, risk of writer starvation, and the need to always release in `finally`; for small critical sections a plain `lock` is often faster and simpler.

## Question 049

### Question
What is concurrent collection?

### Answer
Concurrent collections (`ConcurrentDictionary`, `ConcurrentQueue`, `ConcurrentBag`, `BlockingCollection`) are designed for safe lock-free or fine-grained-locked access from multiple threads, removing the need to wrap a normal collection in your own lock. They provide atomic compound operations (e.g. `GetOrAdd`) that a plain collection plus external lock would race on. They are not automatically faster for single-threaded use, and iterating them yields a moment-in-time snapshot, not a consistent transaction.

## Question 050

### Question
What is ConcurrentDictionary?

### Answer
`ConcurrentDictionary<TKey,TValue>` is a thread-safe dictionary supporting atomic operations like `GetOrAdd`, `AddOrUpdate`, and `TryUpdate` without external locking. A key subtlety: the value factory passed to `GetOrAdd` may run more than once under contention and is not part of the atomic guarantee, so it must be side-effect-free or use the `Lazy<T>` trick. `Count` and enumeration are snapshots and relatively expensive, so avoid using them in hot paths.

## Question 051

### Question
What is race condition?

### Answer
A race condition is a defect where program correctness depends on the nondeterministic timing or interleaving of concurrent operations on shared state. The classic example is check-then-act (e.g. `if (!dict.ContainsKey(k)) dict.Add(k, v)`), where another thread acts between the check and the act. Fixes are atomic operations, locking the whole compound action, or immutable data. Races are intermittent and timing-dependent, which makes them notoriously hard to reproduce.

## Question 052

### Question
What is deadlock?

### Answer
A deadlock occurs when two or more threads each hold a resource the other needs and wait forever. The standard prevention is consistent lock ordering (always acquire locks in the same global order), minimizing lock scope, using timeouts (`Monitor.TryEnter`), and avoiding blocking on async code. In async code, blocking on `.Result` with a captured synchronization context is a frequent cause.

## Question 053

### Question
What is async locking problem?

### Answer
The `lock` statement cannot span an `await` because the monitor is thread-affine and the continuation may resume on a different thread, which would try to release a lock it never acquired. So you cannot hold a `lock` across asynchronous work. The solution is an async-aware primitive like `SemaphoreSlim` with `WaitAsync`/`Release`, which is not thread-affine and can be safely awaited.

## Question 054

### Question
What is SemaphoreSlim?

### Answer
`SemaphoreSlim` limits the number of threads/tasks that can enter a region concurrently, and crucially offers `WaitAsync` for non-blocking, await-friendly acquisition — making it the standard async mutex (count of 1) or throttle (count of N). You must always `Release` in a `finally`, and balance acquire/release counts exactly. Unlike `lock`, it is not reentrant, so re-entering with the same task will deadlock.

## Question 055

### Question
What is Task.WhenAll?

### Answer
`Task.WhenAll` returns a task that completes when all supplied tasks complete, letting you run independent operations concurrently and await them together. If any task faults, the returned task faults; awaiting it surfaces the first exception, but all exceptions are available on its `Exception` (an `AggregateException`). A common mistake is starting the tasks lazily inside the array, or awaiting them sequentially before passing them in, which serializes the work.

## Question 056

### Question
What is Task.WhenAny?

### Answer
`Task.WhenAny` completes when the first of the supplied tasks completes, returning that task so you can inspect it. It is used for timeouts (race a work task against a delay), first-response-wins patterns, and progress on whichever completes first. Watch out: the remaining tasks keep running, so you must handle or cancel them to avoid leaks and unobserved exceptions.

## Question 057

### Question
What is ValueTask?

### Answer
`ValueTask`/`ValueTask<T>` is a struct that can wrap either a synchronously available result or a `Task`, avoiding a heap allocation when an async method often completes synchronously (e.g. data already buffered). It is an optimization for high-throughput hot paths. The cost is strict usage rules: a `ValueTask` may be awaited only once, must not be awaited concurrently, and `.Result` must not be read before completion.

## Question 058

### Question
When should ValueTask be avoided?

### Answer
Avoid `ValueTask` when the result will be awaited multiple times, stored, passed around, combined with `WhenAll`, or accessed concurrently — its single-consumption contract makes those misuses, and the safe workaround (`.AsTask()`) reallocates, defeating the purpose. Default to `Task`/`Task<T>` for ordinary code; reach for `ValueTask` only in measured hot paths where synchronous completion is common and the value is consumed exactly once.

## Question 059

### Question
What is Task.Run used for?

### Answer
`Task.Run` offloads a piece of work to the thread pool, returning a task you can await — appropriate for moving CPU-bound work off the current thread (e.g. off the UI thread). It should not be used to "make" I/O asynchronous; wrapping a synchronous blocking call in `Task.Run` just burns a pool thread instead of using a truly async API. In ASP.NET it is usually an anti-pattern because it steals from the same pool serving requests.

## Question 060

### Question
Why should CPU-bound and I/O-bound work be treated differently?

### Answer
I/O-bound work spends its time waiting on external systems, so true async APIs free the thread entirely while waiting — no thread is consumed. CPU-bound work actually needs a thread to compute, so making it "async" only means choosing which thread runs it (e.g. via `Task.Run` to keep the UI responsive), and over-parallelizing it past the core count hurts. Confusing the two leads to thread-pool starvation (blocking on I/O) or pointless offloading.

## Question 061

### Question
What is IDisposable pattern?

### Answer
`IDisposable` provides deterministic cleanup of unmanaged or expensive resources via `Dispose`. The full pattern uses a `protected virtual void Dispose(bool disposing)` so derived classes can extend it, releases managed resources only when `disposing` is true, and suppresses finalization with `GC.SuppressFinalize(this)`. Most types only wrap other `IDisposable`s and need only a simple `Dispose` without a finalizer. The `using` statement guarantees `Dispose` runs even on exceptions.

## Question 062

### Question
What is IAsyncDisposable?

### Answer
`IAsyncDisposable` provides `DisposeAsync` for cleanup that itself involves asynchronous work, such as flushing a buffered stream or completing a network handshake, so you do not block a thread during teardown. It is consumed with `await using`. Implement both `IDisposable` and `IAsyncDisposable` when callers may use either path, and avoid doing real blocking work in `DisposeAsync`.

## Question 063

### Question
What is using declaration?

### Answer
A using declaration (`using var stream = ...;`) disposes the resource at the end of the enclosing scope rather than requiring an explicit block, reducing nesting. It is convenient but ties disposal to scope exit, which can hold a resource longer than a tight `using` block would; when you need to release earlier, use the block form. Multiple using declarations dispose in reverse order of declaration.

## Question 064

### Question
What is finalizer?

### Answer
A finalizer (`~Type()`) is a last-resort safety net that the GC calls before reclaiming an object that holds unmanaged resources and was not properly disposed. You should rarely write one — only when directly owning unmanaged handles, and even then `SafeHandle` is preferred. Finalizers run on a dedicated thread at a nondeterministic time, so they are not a substitute for `Dispose`.

## Question 065

### Question
Why are finalizers expensive?

### Answer
An object with a finalizer survives at least one extra GC: it is promoted to the finalization queue, processed by the finalizer thread, then collected on a later GC, so it occupies memory longer and adds two-phase overhead. They also delay reclamation and can resurrect objects if poorly written. This is why you call `GC.SuppressFinalize(this)` after a successful `Dispose` and avoid finalizers unless owning raw unmanaged resources.

## Question 066

### Question
What is GC generation?

### Answer
The .NET GC is generational: gen 0 holds newly allocated short-lived objects and is collected frequently and cheaply; survivors are promoted to gen 1 and then gen 2, which is collected rarely and is more expensive. The design exploits the empirical fact that most objects die young. The practical implication is to keep allocations short-lived; objects that survive into gen 2 (or get pinned/leaked) make full collections costlier.

## Question 067

### Question
What is Large Object Heap?

### Answer
The Large Object Heap holds objects of ~85,000 bytes or more (e.g. big arrays). It is collected as part of gen 2 and historically was not compacted, so it is prone to fragmentation: free gaps that cannot satisfy new large allocations, raising memory usage. Mitigations are pooling large buffers (`ArrayPool<T>`), avoiding large transient allocations, and, when necessary, requesting compaction via `GCSettings.LargeObjectHeapCompactionMode`.

## Question 068

### Question
What is allocation pressure?

### Answer
Allocation pressure is the rate at which code allocates on the managed heap; high rates trigger frequent gen 0 collections and, if objects survive, expensive higher-gen collections, hurting throughput and latency. It is the dominant performance factor in many hot paths. You reduce it with structs where appropriate, `Span<T>`, pooling, avoiding boxing and unnecessary LINQ/closures in loops, and reusing buffers.

## Question 069

### Question
What is boxing in generic code?

### Answer
Boxing wraps a value type in a heap object so it can be treated as a reference type, allocating and copying. In generic code it sneaks in when a value type is passed where `object` or a non-generic interface is expected, or when calling a virtual method through a base reference. Generics with constraints largely avoid it, but pitfalls remain (e.g. calling `Equals(object)` instead of `IEquatable<T>.Equals`). Each box in a hot loop is wasted allocation.

## Question 070

### Question
How can LINQ cause hidden allocations?

### Answer
Each LINQ operator typically allocates an enumerator and often a closure/display class for captured variables and the delegate, and methods like `OrderBy`, `GroupBy`, or `ToList` buffer data. In per-request or per-frame hot paths these allocations add up and create GC pressure. The fix is to use plain loops or spans in measured hot paths, while keeping LINQ for readability in non-critical code.

## Question 071

### Question
What is reflection?

### Answer
Reflection is the runtime ability to inspect types, members, and attributes, and to invoke members or construct objects dynamically through the metadata in assemblies. It powers serializers, DI containers, ORMs, and test runners. The costs are significant: it bypasses compile-time safety, is much slower than direct calls, and breaks trimming/AOT. Cache `MemberInfo`/delegates or prefer source generators when reflection appears in hot paths.

## Question 072

### Question
When is reflection useful?

### Answer
Reflection is justified for genuinely open-ended scenarios: plugin discovery, serialization of arbitrary types, mapping configuration to objects, building DI graphs, and tooling that must work over types unknown at compile time. It is the wrong tool when the type set is known and a generic, interface, or source generator would give the same result with type safety and speed. The modern trend is to replace runtime reflection with compile-time source generators where possible.

## Question 073

### Question
What are attributes?

### Answer
Attributes attach declarative metadata to code elements, readable at runtime via reflection or at compile time by analyzers and source generators. They drive behavior without changing logic — `[Obsolete]`, `[Serializable]`, validation, routing, and DI markers are examples. They are inert by themselves; some component must read them. Overusing attributes for control flow can hide important behavior behind metadata.

## Question 074

### Question
What is source generator?

### Answer
A source generator is a compiler component that inspects the program during compilation and emits additional C# source, which is then compiled alongside your code. It replaces runtime reflection with generated, AOT-friendly, debuggable code — used by `System.Text.Json`, `LoggerMessage`, regex, and DI. The benefits are speed and trim/AOT compatibility; the cost is generator complexity and longer build times. Generators can only add code, not modify existing code.

## Question 075

### Question
What is analyzer?

### Answer
A Roslyn analyzer inspects code during compilation and reports diagnostics (warnings/errors), optionally paired with code-fix providers in the IDE. Analyzers enforce style, catch bug patterns, and codify team conventions automatically. Configured via `.editorconfig` with severities, they turn tribal knowledge into mechanical checks. The trade-off is build-time cost and the need to tune severity to avoid alert fatigue.

## Question 076

### Question
What is assembly binding?

### Answer
Assembly binding is how the runtime resolves and loads the correct assembly (by name, version, culture, and for the GAC, public key token) when a dependency is requested. In modern .NET this is governed mostly by the deps.json/runtime config and the `AssemblyLoadContext`. Binding failures (`FileLoadException`, version mismatch) typically come from inconsistent dependency versions or copies in different folders. Binding redirects were the old framework mechanism; modern .NET relies on the resolver and unification rules.

## Question 077

### Question
What is strong naming?

### Answer
Strong naming signs an assembly with a public/private key pair, giving it a unique identity (name + version + culture + public key token) and tamper detection. Historically it was required for the GAC and side-by-side versioning. In modern .NET it matters far less; it provides identity, not real security (the key is not secret), and it does not prevent decompilation. Teams adopt it mainly for compatibility with code that demands strong-named references.

## Question 078

### Question
What is versioning issue with public APIs?

### Answer
Once an API is public, changing a signature, removing a member, or altering observable behavior can break every consumer that compiled against it — a binary or source breaking change. Even adding an interface member or overload can break implementers or cause ambiguity. The disciplines are semantic versioning, additive-only changes within a major version, `[Obsolete]` deprecation cycles, and default interface methods to extend interfaces without breaking implementers.

## Question 079

### Question
What is NuGet transitive dependency?

### Answer
A transitive dependency is a package your direct dependencies pull in indirectly. NuGet resolves the whole graph and, by default, unifies to the lowest version satisfying all constraints, which can surface conflicts ("diamond dependency") or silently bring in vulnerable or unexpected packages. You can pin or override versions, audit with `dotnet list package --include-transitive` and `--vulnerable`, and centralize versions for consistency across a solution.

## Question 080

### Question
What is semantic versioning?

### Answer
Semantic versioning (MAJOR.MINOR.PATCH) communicates change impact: MAJOR for breaking changes, MINOR for backward-compatible features, PATCH for backward-compatible fixes, with optional pre-release/build suffixes. It lets consumers reason about safe upgrades and set version ranges. Its value depends on discipline — mislabeling a breaking change as a minor bump erodes the trust the scheme exists to provide.

## Question 081

### Question
What is logging level?

### Answer
Logging levels (Trace, Debug, Information, Warning, Error, Critical) classify message severity so you can filter verbosity per environment and category. Production typically runs at Information and above, with Debug/Trace enabled selectively for diagnosis. Choosing the right level matters: logging routine flow at Error creates noise, while logging real failures at Information hides them. Level checks also gate expensive message construction.

## Question 082

### Question
What is structured logging?

### Answer
Structured logging records events with named properties rather than interpolated strings — `logger.LogInformation("Order {OrderId} shipped to {Region}", id, region)` — so log systems can index and query by field. It makes logs searchable and machine-processable, unlike flat text. The key rule is to pass values as message-template arguments, not to pre-format them into the string, which would lose the structure.

## Question 083

### Question
What should not be logged?

### Answer
Never log secrets and sensitive data: passwords, tokens, API keys, connection strings, full payment-card numbers, and personal data subject to regulation (in the EU, GDPR-protected PII). Logging them creates security and compliance exposure that long outlives the request. Redact or hash where you need correlation, scrub exception data that may contain payloads, and review what third-party libraries log by default.

## Question 084

### Question
What is options pattern in ASP.NET Core?

### Answer
The options pattern binds configuration sections to strongly typed classes injected via `IOptions<T>`, `IOptionsSnapshot<T>`, or `IOptionsMonitor<T>`. It separates configuration shape from consumers, supports validation, and integrates with the configuration providers. `IOptions` is a singleton snapshot; `IOptionsSnapshot` is per-scope (reloads per request); `IOptionsMonitor` supports live change notifications — choosing the wrong one is a common source of "config not updating" confusion.

## Question 085

### Question
What is named options?

### Answer
Named options let you register multiple distinct configurations of the same options type under different names, retrieved via `IOptionsMonitor<T>.Get(name)` or `IOptionsSnapshot<T>.Get(name)`. This is how you configure several instances of the same component — for example, multiple `HttpClient`s or authentication schemes — each with its own settings. Without names, all registrations target the single default ("") instance.

## Question 086

### Question
What is hosted service?

### Answer
A hosted service implements `IHostedService` (`StartAsync`/`StopAsync`) and is managed by the generic host's lifetime, so it starts when the app starts and shuts down gracefully with it. It is the standard place for long-running or startup/shutdown work in ASP.NET Core and worker apps. Long blocking work in `StartAsync` delays app startup, so kick off background loops without blocking the start path.

## Question 087

### Question
What is background service?

### Answer
`BackgroundService` is the convenient base class for `IHostedService` that exposes a single `ExecuteAsync(CancellationToken)` for a long-running loop. You honor the token to stop cleanly on shutdown, and catch exceptions inside the loop because an unhandled exception can stop the service (and, depending on settings, the host). It is used for queue consumers, timers, and periodic jobs.

## Question 088

### Question
What is health check?

### Answer
Health checks expose endpoints reporting whether the app and its dependencies (database, cache, downstream services) are healthy, used by load balancers, orchestrators, and monitoring. They distinguish liveness (is the process up) from readiness (can it serve traffic). Keep checks fast and cheap; a slow or dependency-heavy health check can itself cause cascading restarts under load.

## Question 089

### Question
What is minimal API?

### Answer
Minimal APIs define HTTP endpoints with lightweight lambda handlers (`app.MapGet("/items", ...)`) instead of controller classes, reducing ceremony for small services. They support DI, model binding, filters, and route groups. The trade-off is that very large minimal-API codebases can sprawl without the structure controllers impose, so teams organize them into endpoint groups/modules as they grow.

## Question 090

### Question
What is controller-based API?

### Answer
Controller-based APIs group endpoints into controller classes with attribute routing, action methods, filters, and model binding, giving a conventional structure that scales for larger applications. They integrate with the full MVC pipeline (filters, model validation, conventions). Compared to minimal APIs they involve more boilerplate but offer familiar organization and a richer filter/convention ecosystem; both share the same underlying middleware and DI.

## Question 091

### Question
What is model binding?

### Answer
Model binding maps incoming HTTP data — route values, query string, headers, form fields, and the request body — onto action parameters and complex objects. It removes manual parsing and integrates with validation. Pitfalls include over-posting (binding fields the client should not set), ambiguous sources requiring explicit `[FromBody]`/`[FromQuery]` attributes, and culture-sensitive parsing of numbers and dates.

## Question 092

### Question
What is validation?

### Answer
Validation checks that bound input meets the rules before your logic runs, via data annotations, `IValidatableObject`, or a library like FluentValidation, surfacing errors as a 400 with details. Server-side validation is mandatory because client-side checks can be bypassed. Keep validation at the boundary and distinguish input validation (shape/format) from business-rule validation (domain invariants), which often belongs deeper in the model.

## Question 093

### Question
What is middleware ordering?

### Answer
ASP.NET Core middleware forms a pipeline where each component can act before and after the next, so order determines behavior: exception handling must be early to wrap everything, authentication must precede authorization, CORS and routing have required positions, and static files short-circuit before MVC. Misordering produces subtle bugs — e.g. authorization running before authentication sees no user. The pipeline is built in `Program.cs` and runs in registration order inbound, reverse outbound.

## Question 094

### Question
What is authentication versus authorization?

### Answer
Authentication establishes who the caller is (validating credentials/tokens into an identity); authorization decides what that identity is allowed to do (roles, policies, resource ownership). They are distinct stages and middleware: authentication first, then authorization. Conflating them — for instance treating a valid token as automatic permission — is a common security flaw; a known-but-unauthorized user should get 403, an unknown one 401.

## Question 095

### Question
What is JWT?

### Answer
A JSON Web Token is a signed (and optionally encrypted) token carrying claims, used for stateless authentication: the server validates the signature and trusts the claims without a session lookup. Benefits are statelessness and easy cross-service use; risks are that tokens cannot be easily revoked before expiry, the payload is readable (base64, not encrypted unless using JWE), and short lifetimes plus refresh tokens are needed. Always validate issuer, audience, expiry, and signature.

## Question 096

### Question
What is CORS?

### Answer
Cross-Origin Resource Sharing is a browser security mechanism where the server uses response headers to tell the browser which origins may call it; it is enforced by the browser, not a server-side firewall. You configure allowed origins, methods, and headers, and decide whether to allow credentials. The frequent mistake is allowing all origins with credentials (disallowed by spec) or treating CORS as authorization — it only relaxes the browser's same-origin policy.

## Question 097

### Question
What is rate limiting?

### Answer
Rate limiting caps how many requests a client may make in a window to protect against abuse, accidental floods, and resource exhaustion. .NET's built-in middleware offers algorithms like fixed window, sliding window, token bucket, and concurrency limits, with partitioning by client/key. Decisions include the limit dimension (per IP, per user, per API key), the response on rejection (429 with Retry-After), and where to enforce (edge vs app).

## Question 098

### Question
What is Entity Framework Core?

### Answer
EF Core is Microsoft's object-relational mapper: it maps classes to tables, translates LINQ to SQL, tracks changes, and persists them, plus provides migrations and a provider model for different databases. It accelerates data access but hides cost — naive use causes N+1 queries, over-fetching, and surprising SQL. Effective use means understanding change tracking, query translation limits, and when to drop to raw SQL.

## Question 099

### Question
What is DbContext lifetime?

### Answer
`DbContext` is a lightweight, non-thread-safe unit of work meant to be short-lived — typically scoped per request/operation. Sharing one instance across threads or keeping it alive too long causes concurrency exceptions, ever-growing change-tracker memory, and stale data. In ASP.NET Core it is registered as Scoped; in desktop/background code you create one per logical operation (often via `IDbContextFactory`).

## Question 100

### Question
What is migration?

### Answer
A migration is a versioned, code-generated description of incremental schema changes that EF Core applies to keep the database in sync with the model, with up (apply) and down (revert) operations. It gives you reproducible, source-controlled schema evolution. Pitfalls include editing applied migrations, data-loss operations the generator cannot infer (renames detected as drop+add), and applying migrations automatically at startup in multi-instance deployments, which can race.

## Question 101

### Question
What is tracking versus no-tracking query?

### Answer
By default EF Core tracks queried entities in the change tracker so it can detect and save modifications, which costs memory and CPU. A no-tracking query (`AsNoTracking()`) skips this, making read-only queries faster and lighter — the right default for queries you will not update. Use tracking only when you intend to modify and save the entities; tracking large read-only result sets is a common, avoidable performance drain.

## Question 102

### Question
What is N+1 query problem?

### Answer
The N+1 problem is issuing one query to fetch a list, then one additional query per item to load a related entity — N+1 round trips where one or two would do. It usually comes from lazy loading or accessing navigation properties inside a loop. Fixes are eager loading with `Include`, projecting only needed data with `Select`, or split queries; detecting it requires looking at the generated SQL, not just the C#.

## Question 103

### Question
What should a Junior+ C# developer be able to debug independently?

### Answer
A Junior+ developer should independently diagnose the everyday failures: `NullReferenceException`s by reading the stack trace to the source, async deadlocks and the `.Result`/`ConfigureAwait` causes, EF Core N+1 and tracking issues by inspecting generated SQL, memory growth from un-unsubscribed events, and incorrect dictionary/set behavior from broken `Equals`/`GetHashCode`. They should use the debugger (breakpoints, conditional breakpoints, watch, call stack), read logs and exception details critically, and reproduce a bug with a minimal case before changing code — rather than guessing or adding scattered try/catch.
