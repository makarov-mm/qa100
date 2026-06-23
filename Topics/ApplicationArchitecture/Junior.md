# Application Architecture Junior Interview Questions

Question-answer interview preparation file for Application Architecture at Junior level.

## Question 001

### Question
What is software architecture?

### Answer
Software architecture is the set of high-level structural decisions about a system: how it is divided into major parts, how those parts interact, and what responsibilities each has. These decisions are the ones that are expensive to change later.

## Question 002

### Question
Why does architecture matter?

### Answer
Good architecture makes a system easier to understand, change, test, and scale, and it reduces the cost of future work. Poor architecture causes tight coupling, fragility, and changes that ripple unpredictably across the codebase.

## Question 003

### Question
What is the difference between architecture and implementation?

### Answer
Architecture is the high-level structure and the decisions that are hard to reverse; implementation is the concrete code that fills in those structures. A class's internal logic is implementation; the choice of layers and how they depend on each other is architecture.

## Question 004

### Question
What is separation of concerns?

### Answer
Separation of concerns means dividing a system so that each part deals with one distinct aspect — UI, business rules, data access — and nothing else. It keeps changes local: touching the UI should not force changes in persistence.

## Question 005

### Question
What is coupling?

### Answer
Coupling is the degree to which one component depends on the details of another. Low (loose) coupling is desirable because it lets parts change independently; high coupling means a change in one place forces changes elsewhere.

## Question 006

### Question
What is cohesion?

### Answer
Cohesion is how closely the responsibilities inside a single component belong together. High cohesion (a class that does one well-defined thing) is good; low cohesion (a class that does many unrelated things) is a sign it should be split.

## Question 007

### Question
Why do we want low coupling and high cohesion together?

### Answer
High cohesion keeps each component focused and understandable, while low coupling keeps components independent so they can evolve separately. Together they make a system where changes stay contained and parts can be tested and reused in isolation.

## Question 008

### Question
What is a layer in architecture?

### Answer
A layer is a horizontal grouping of code by technical responsibility, such as presentation, business logic, and data access. Each layer typically depends only on the layer directly beneath it.

## Question 009

### Question
What is a typical three-layer architecture?

### Answer
A common structure is presentation (UI), business logic (rules and workflows), and data access (talking to the database). The UI calls business logic, which calls data access, keeping responsibilities separated.

## Question 010

### Question
What is the difference between a layer and a tier?

### Answer
A layer is a logical separation of code; a tier is a physical separation across processes or machines. Three layers can all run in one process (one tier), or be deployed as separate tiers (client, server, database).

## Question 011

### Question
What is the presentation layer?

### Answer
The presentation layer is responsible for interacting with the user or caller: rendering UI, handling input, and formatting output. It should contain no business rules, only the logic needed to present and collect data.

## Question 012

### Question
What is the business logic layer?

### Answer
The business logic layer holds the application's rules, workflows, and domain behavior — the "what the system actually does." It should not know about UI details or the specifics of the database technology.

## Question 013

### Question
What is the data access layer?

### Answer
The data access layer is responsible for reading and writing persistent data, translating between the database and in-memory objects. It isolates the rest of the application from the details of the storage technology.

## Question 014

### Question
What is a DTO?

### Answer
A Data Transfer Object is a simple object used to carry data between layers or across a boundary, with no behavior. It decouples internal models from what is exposed to callers, for example shaping an API response.

## Question 015

### Question
Why separate domain models from DTOs?

### Answer
Keeping them separate prevents external contracts (API shapes) from constraining your internal model, and stops internal changes from accidentally breaking clients. It also avoids exposing fields that callers should not see.

## Question 016

### Question
What is a dependency?

### Answer
A dependency is when one component needs another to do its work. Managing the direction and number of dependencies is central to architecture, because every dependency is a path along which change and failure can propagate.

## Question 017

### Question
What is an interface in the architectural sense?

### Answer
An interface is a contract that describes what a component does without specifying how. Depending on interfaces rather than concrete classes lets you swap implementations and test components in isolation.

## Question 018

### Question
What is abstraction?

### Answer
Abstraction is hiding details behind a simpler concept so callers can use something without knowing how it works internally. It reduces cognitive load and decouples users of a component from its implementation.

## Question 019

### Question
What is encapsulation?

### Answer
Encapsulation means bundling data with the behavior that operates on it and hiding internal state behind a controlled interface. It protects invariants by preventing outside code from putting an object into an invalid state.

## Question 020

### Question
What is the dependency direction in layered architecture?

### Answer
Dependencies normally point downward: presentation depends on business logic, which depends on data access. The key architectural goal is to avoid lower layers depending on higher ones, which would create cycles and tight coupling.

## Question 021

### Question
What is a cyclic dependency and why is it bad?

### Answer
A cyclic dependency is when components depend on each other directly or indirectly, forming a loop. It is bad because it makes the parts impossible to understand, build, or change independently, and it usually signals a missing abstraction.

## Question 022

### Question
What is the single responsibility principle?

### Answer
A class or module should have one reason to change — one responsibility. When a class does several unrelated things, changes to one concern risk breaking the others, so the principle pushes toward focused, cohesive units.

## Question 023

### Question
What is the open/closed principle?

### Answer
Software entities should be open for extension but closed for modification: you should be able to add behavior without editing existing, tested code. It is usually achieved through abstraction and polymorphism rather than rewriting.

## Question 024

### Question
What is the Liskov substitution principle?

### Answer
Subtypes must be usable anywhere their base type is expected without breaking the program's correctness. Violations happen when a subclass weakens guarantees or throws where the base type would not, surprising callers.

## Question 025

### Question
What is the interface segregation principle?

### Answer
Clients should not be forced to depend on methods they do not use. Many small, focused interfaces are better than one large one, because they reduce unnecessary coupling and let implementers provide only what is relevant.

## Question 026

### Question
What is the dependency inversion principle?

### Answer
High-level modules should not depend on low-level details; both should depend on abstractions. In practice this means business logic depends on interfaces, and concrete implementations (like a specific database) are plugged in from the outside.

## Question 027

### Question
What does SOLID stand for?

### Answer
SOLID is the five principles: Single responsibility, Open/closed, Liskov substitution, Interface segregation, and Dependency inversion. Together they guide object-oriented designs toward loose coupling and maintainability.

## Question 028

### Question
What is the DRY principle?

### Answer
Don't Repeat Yourself: every piece of knowledge should have a single, authoritative representation. Duplication causes bugs when one copy is updated and others are forgotten, though premature deduplication of things that merely look alike is its own pitfall.

## Question 029

### Question
What is the KISS principle?

### Answer
Keep It Simple: prefer the simplest solution that meets the requirement, avoiding unnecessary complexity. Simple designs are easier to understand, test, and change than clever ones.

## Question 030

### Question
What is YAGNI?

### Answer
You Aren't Gonna Need It: do not build features or flexibility for speculative future needs. Speculative generality adds complexity now for benefits that often never materialize.

## Question 031

### Question
What is technical debt?

### Answer
Technical debt is the implied future cost of choosing a quick or suboptimal solution now instead of a better one. Like financial debt it accrues "interest" — extra effort on every future change — until it is paid down through refactoring.

## Question 032

### Question
What is maintainability?

### Answer
Maintainability is how easily a system can be understood, modified, and extended over time. It is driven by clear structure, low coupling, good naming, and tests, and it is often the dominant long-term cost of software.

## Question 033

### Question
What is scalability?

### Answer
Scalability is a system's ability to handle increased load by adding resources. It includes vertical scaling (a bigger machine) and horizontal scaling (more machines), and architecture decisions strongly affect which is possible.

## Question 034

### Question
What is the difference between vertical and horizontal scaling?

### Answer
Vertical scaling adds more power (CPU, RAM) to a single machine; horizontal scaling adds more machines and distributes load across them. Horizontal scaling is more elastic but requires the application to be designed for it (e.g. stateless).

## Question 035

### Question
What is a stateless component?

### Answer
A stateless component keeps no client-specific data between requests, so any instance can handle any request. Statelessness makes horizontal scaling and failover simple because requests can be routed to any node.

## Question 036

### Question
What is a stateful component?

### Answer
A stateful component retains data between interactions, such as an in-memory session. It can be simpler for some workflows but complicates scaling and resilience because that state must be preserved, replicated, or externalized.

## Question 037

### Question
What is a monolithic architecture?

### Answer
A monolith is a system built and deployed as a single unit, with all functionality in one codebase and process. It is simple to develop and deploy early on, but can become hard to scale and change as it grows large.

## Question 038

### Question
What is a distributed system?

### Answer
A distributed system runs across multiple processes or machines that communicate over a network to act as one system. It enables scale and resilience but adds the hard problems of network failure, latency, and partial failure.

## Question 039

### Question
What is client-server architecture?

### Answer
In client-server architecture, clients send requests and a server provides responses and shared resources. It centralizes data and logic on the server while clients handle interaction, and it underpins most web and networked applications.

## Question 040

### Question
What is an API?

### Answer
An Application Programming Interface is a defined contract through which one piece of software exposes functionality to another. It hides internal implementation and gives callers a stable way to interact with a component or service.

## Question 041

### Question
What is REST at a basic level?

### Answer
REST is an architectural style for web APIs that models resources addressed by URLs and manipulated with standard HTTP verbs (GET, POST, PUT, DELETE). It favors statelessness and uses standard status codes to express outcomes.

## Question 042

### Question
What are common HTTP methods and their intent?

### Answer
GET reads a resource, POST creates or triggers, PUT replaces, PATCH partially updates, and DELETE removes. Using them according to intent keeps an API predictable and lets infrastructure (caches, proxies) behave correctly.

## Question 043

### Question
What is idempotency at a basic level?

### Answer
An idempotent operation produces the same result whether performed once or many times. GET, PUT, and DELETE are expected to be idempotent, while POST usually is not — which matters when requests may be retried.

## Question 044

### Question
What is the model in MVC?

### Answer
In MVC the model represents the application's data and business rules. It is independent of how it is displayed, so the same model can be presented by different views.

## Question 045

### Question
What is the view in MVC?

### Answer
The view is responsible for presenting the model to the user. It should contain only display logic and read from the model, not contain business rules.

## Question 046

### Question
What is the controller in MVC?

### Answer
The controller handles input, coordinates the model, and selects the view to render. It acts as the mediator between user actions and the rest of the application.

## Question 047

### Question
What problem does MVC solve?

### Answer
MVC separates presentation from data and control flow, so each can change independently and be tested in isolation. It prevents UI code and business logic from becoming tangled together.

## Question 048

### Question
What is MVVM at a basic level?

### Answer
Model-View-ViewModel separates the view from the model via a view model that exposes data and commands the view binds to. It is common in XAML UI frameworks like WPF, where data binding does the wiring between view and view model.

## Question 049

### Question
What is data binding?

### Answer
Data binding is a mechanism that automatically synchronizes UI elements with underlying data, so changes in one update the other. It removes repetitive glue code that copies values between the model and the screen.

## Question 050

### Question
What is a component in architecture?

### Answer
A component is a self-contained unit with a defined responsibility and interface that can be developed and reasoned about independently. Good architecture composes a system from components with clear boundaries.

## Question 051

### Question
What is a module?

### Answer
A module is a named grouping of related code that can be developed and understood as a unit, exposing a public surface and hiding internals. Modularity is how large systems stay comprehensible.

## Question 052

### Question
What is a service in application terms?

### Answer
A service is a component that performs a coherent set of operations behind an interface, often coordinating other components. It encapsulates a capability (e.g. payment, notification) so callers depend on the capability, not its implementation.

## Question 053

### Question
What is a repository in basic terms?

### Answer
A repository is an abstraction that provides collection-like access to stored objects, hiding the details of how they are persisted. It lets business logic work with domain objects without knowing about SQL or the database.

## Question 054

### Question
Why hide the database behind an abstraction?

### Answer
Hiding it lets business logic stay focused on rules instead of storage details, makes the code testable with fakes, and allows the storage technology to change with minimal ripple. It enforces the separation between "what" and "how it is stored."

## Question 055

### Question
What is configuration in an application?

### Answer
Configuration is the set of values that control behavior without code changes — connection strings, feature flags, endpoints. Keeping configuration separate from code lets the same build run in different environments.

## Question 056

### Question
Why should configuration be separate from code?

### Answer
Separating it lets you change behavior per environment (dev, test, production) without rebuilding, and keeps secrets out of source control. It follows the principle that build artifacts should be environment-agnostic.

## Question 057

### Question
What is logging and why is it architecturally relevant?

### Answer
Logging records what the system does so you can diagnose behavior in production where you cannot debug interactively. It is a cross-cutting concern that should be applied consistently across components rather than ad hoc.

## Question 058

### Question
What is a cross-cutting concern?

### Answer
A cross-cutting concern is a responsibility that spans many parts of the system, such as logging, security, validation, or error handling. Architecture must decide how to apply these consistently without scattering duplicated code everywhere.

## Question 059

### Question
Where should business logic live?

### Answer
Business logic belongs in the business/domain layer, not in the UI or the data access code. Putting rules in controllers or stored procedures scatters them and makes the system hard to test and change. A common junior mistake is writing rules directly in UI event handlers or controllers.

## Question 060

### Question
What is an anti-pattern?

### Answer
An anti-pattern is a common solution that looks reasonable but causes more problems than it solves. Recognizing anti-patterns (like the God object or spaghetti code) helps you avoid repeating known mistakes.

## Question 061

### Question
What is a God object?

### Answer
A God object is a class that knows or does too much, accumulating many unrelated responsibilities. It violates single responsibility and high cohesion, becoming a bottleneck for changes and a magnet for bugs.

## Question 062

### Question
What is spaghetti code?

### Answer
Spaghetti code is code with tangled, unstructured control flow and dependencies, where everything connects to everything. It is the result of missing architecture and makes any change risky and unpredictable.

## Question 063

### Question
What is a design pattern?

### Answer
A design pattern is a named, reusable solution to a recurring design problem. Patterns give teams shared vocabulary and proven structures, though applying them where they are not needed adds unnecessary complexity.

## Question 064

### Question
What is the factory pattern at a basic level?

### Answer
A factory is a component whose job is to create objects, hiding the construction details from callers. It centralizes creation logic and lets the concrete type vary without changing the calling code.

## Question 065

### Question
What is the singleton pattern and what is its risk?

### Answer
A singleton ensures a class has a single shared instance with global access. Its risk is hidden global state that complicates testing and creates coupling, so it should be used sparingly and often replaced by dependency injection.

## Question 066

### Question
What is the strategy pattern at a basic level?

### Answer
The strategy pattern defines a family of interchangeable algorithms behind a common interface, selected at runtime. It replaces conditional branching over types with polymorphism, following open/closed.

## Question 067

### Question
What is the adapter pattern?

### Answer
An adapter wraps one interface to make it look like another that a client expects. It lets incompatible components work together without changing either, often used to integrate third-party or legacy code.

## Question 068

### Question
What is composition over inheritance?

### Answer
It is the guideline to prefer building behavior by combining objects (composition) rather than extending base classes (inheritance). Composition is more flexible and avoids the rigidity and fragility of deep inheritance hierarchies.

## Question 069

### Question
What is the difference between "is-a" and "has-a" relationships?

### Answer
"Is-a" describes inheritance (a Car is a Vehicle); "has-a" describes composition (a Car has an Engine). Choosing the right one prevents incorrect hierarchies, which are a common source of design problems.

## Question 070

### Question
What is a contract in software design?

### Answer
A contract is the agreed expectations between a caller and a component: inputs, outputs, and behavior. Programming to contracts (interfaces) rather than implementations is what enables loose coupling and substitution.

## Question 071

### Question
What is validation and where does it belong?

### Answer
Validation checks that data meets required rules. Basic input/format validation belongs at the boundary (e.g. the API), while business-rule validation belongs in the domain where the rules live, so invalid data cannot corrupt state.

## Question 072

### Question
What is error handling as an architectural concern?

### Answer
Error handling is the consistent strategy for detecting, reporting, and recovering from failures across the system. Done architecturally, it centralizes how errors are translated and surfaced rather than scattering ad-hoc try/catch everywhere.

## Question 073

### Question
What is the difference between an exception and an error code?

### Answer
An exception interrupts normal flow and propagates up until handled, while an error code is a returned value the caller must check. Exceptions suit truly exceptional cases; overusing them for normal control flow harms readability and performance.

## Question 074

### Question
What is a boundary in architecture?

### Answer
A boundary is a line across which the form or ownership of data and control changes, such as between the UI and the domain, or between two services. Well-placed boundaries are where you put abstractions to keep parts independent.

## Question 075

### Question
What is the difference between internal and public API?

### Answer
An internal API is used within a codebase and can change freely; a public API is consumed by external parties and must remain stable. The distinction drives how carefully you must manage changes and versioning.

## Question 076

### Question
What is versioning of an API?

### Answer
Versioning lets an API evolve without breaking existing clients by offering distinct versions of the contract. It is necessary because once others depend on a contract, changing it carelessly breaks them.

## Question 077

### Question
What is backward compatibility?

### Answer
Backward compatibility means new versions still work with clients built for older versions. Preserving it (e.g. by only adding, never removing or changing existing fields) avoids forcing every consumer to update at once.

## Question 078

### Question
What is a database schema?

### Answer
A schema is the structural definition of a database: its tables, columns, types, and relationships. The schema is an architectural asset because changing it affects everything that reads or writes the data.

## Question 079

### Question
What is the difference between a relational and a NoSQL database at a basic level?

### Answer
A relational database stores structured data in tables with enforced relationships and strong consistency; NoSQL databases trade some of that structure or consistency for flexibility and scale. The choice depends on the data shape and access patterns.

## Question 080

### Question
What is caching at a basic level?

### Answer
Caching stores the result of expensive work so repeated requests can be served faster from the cache instead of recomputing. It improves performance but introduces the problem of keeping cached data fresh.

## Question 081

### Question
What is the main difficulty with caching?

### Answer
The main difficulty is invalidation: knowing when cached data is stale and must be refreshed or removed. Stale caches serve incorrect data, so caching is a trade-off between speed and freshness.

## Question 082

### Question
What is asynchronous communication at a basic level?

### Answer
Asynchronous communication lets a sender continue without waiting for the receiver to respond, often via a queue or event. It decouples components in time, improving responsiveness and resilience compared to blocking synchronous calls.

## Question 083

### Question
What is a message queue at a basic level?

### Answer
A message queue is a buffer that holds messages between a producer and a consumer so they can work at their own pace and stay decoupled. It smooths load spikes and lets the consumer process work reliably even if it was offline briefly.

## Question 084

### Question
What is synchronous versus asynchronous processing?

### Answer
Synchronous processing makes the caller wait for completion; asynchronous processing returns immediately and completes the work later. Asynchronous suits long or unreliable operations, at the cost of more complex coordination.

## Question 085

### Question
What is a deployment unit?

### Answer
A deployment unit is the package that is released and run together, such as a single executable, container, or service. Architecture decisions (monolith vs services) determine how many deployment units a system has and how independently they release.

## Question 086

### Question
What is the difference between build time and run time?

### Answer
Build time is when source is compiled and packaged; run time is when the program executes. Some decisions (dependencies, configuration validation) can shift between the two, affecting how early errors are caught.

## Question 087

### Question
What is dependency injection at a basic level?

### Answer
Dependency injection means a component receives its dependencies from outside rather than creating them itself. This decouples the component from concrete implementations and makes it easy to test with substitutes.

## Question 088

### Question
What is inversion of control at a basic level?

### Answer
Inversion of control means the framework or container, rather than your code, controls the flow and the creation of objects. Dependency injection is one common form of it, where wiring is handed to a container.

## Question 089

### Question
Why is testability an architectural goal?

### Answer
Testable code is structured so behavior can be verified in isolation, which requires loose coupling and clear seams. Architecture that resists testing usually also resists change, so testability is a proxy for good structure.

## Question 090

### Question
What is a seam in code?

### Answer
A seam is a place where you can alter behavior without editing the code there, typically by injecting a different implementation. Seams (often interfaces) are what make code testable and extensible.

## Question 091

### Question
What is a mock or fake at a basic level?

### Answer
A mock or fake is a stand-in implementation used in tests to replace a real dependency like a database or network. It lets you test a component's logic deterministically without its external collaborators.

## Question 092

### Question
What is the difference between unit and integration testing architecturally?

### Answer
Unit tests verify a single component in isolation using fakes; integration tests verify that components work together, including real dependencies. A good architecture supports fast unit tests by keeping components decoupled.

## Question 093

### Question
What is a layered architecture's main weakness?

### Answer
Strict layering can lead to "pass-through" layers that add little value and to business logic leaking into the wrong layer. It can also encourage a database-centric design where the domain becomes anemic.

## Question 094

### Question
What is an anemic domain model?

### Answer
An anemic domain model is one where classes hold only data with no behavior, and all logic lives in separate service classes. It is often criticized because it scatters rules and loses the benefits of encapsulation.

## Question 095

### Question
What is a rich domain model?

### Answer
A rich domain model puts behavior and the rules that protect invariants inside the domain objects themselves. It keeps related data and logic together, improving cohesion and making invalid states harder to reach.

## Question 096

### Question
What is the difference between a framework and a library?

### Answer
You call a library, but a framework calls you — it controls the overall flow and you fill in specific parts. This "inversion of control" is the defining difference and affects how your code is structured around it.

## Question 097

### Question
What is a dependency graph?

### Answer
A dependency graph maps which components depend on which. Inspecting it reveals coupling, cycles, and layering violations, making it a practical tool for understanding and improving architecture.

## Question 098

### Question
Why avoid putting logic in the UI event handlers?

### Answer
Logic in UI handlers cannot be reused or tested without the UI and tends to duplicate and drift. Moving it into the domain or a service keeps the UI thin and the rules centralized and verifiable.

## Question 099

### Question
What does "program to an interface, not an implementation" mean?

### Answer
It means callers should depend on an abstraction rather than a concrete class, so the implementation can change or be substituted freely. This is the practical root of loose coupling and testability.

## Question 100

### Question
What should a junior developer focus on first in architecture?

### Answer
Focus on the fundamentals that apply everywhere: separation of concerns, low coupling and high cohesion, keeping business logic out of the UI and database, and depending on abstractions. Mastering these makes the more advanced patterns much easier to understand later.
