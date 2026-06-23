# Application Architecture JuniorPlus Interview Questions

Question-answer interview preparation file for Application Architecture at JuniorPlus level.

## Question 001

### Question
How do you decide where a piece of logic belongs?

### Answer
Ask what kind of concern it is: presentation formatting goes to the UI, business rules go to the domain, and persistence details go to data access. The guiding test is "what would force this code to change," and you place it with the concern that owns that reason.

## Question 002

### Question
How does dependency inversion actually invert dependencies?

### Answer
Instead of high-level code referencing a concrete low-level class, you define the abstraction in or near the high-level module and have the low-level class implement it. The arrow flips: the detail now depends on the policy's interface rather than the policy depending on the detail.

## Question 003

### Question
What is the difference between dependency inversion and dependency injection?

### Answer
Dependency inversion is the principle of depending on abstractions; dependency injection is one technique for supplying those abstractions at runtime. You can follow the principle by passing interfaces, with or without a DI container.

## Question 004

### Question
What are the three forms of dependency injection?

### Answer
Constructor injection (dependencies passed to the constructor), property/setter injection (set after construction), and method injection (passed per call). Constructor injection is preferred because it makes dependencies explicit and guarantees the object is fully initialized.

## Question 005

### Question
Why is constructor injection preferred?

### Answer
It makes all required dependencies visible and mandatory, prevents objects from existing in a half-configured state, and supports immutability. A long constructor parameter list is also a useful smell that signals a class doing too much.

## Question 006

### Question
What is a DI container?

### Answer
A DI container is a framework that registers how to create dependencies and resolves whole object graphs automatically. It removes manual wiring but adds a layer of indirection, so the registrations themselves become something to manage.

## Question 007

### Question
What are common service lifetimes in a DI container?

### Answer
Typically singleton (one instance for the app), scoped (one per logical operation such as a web request), and transient (a new instance each time). Choosing wrong — like a singleton capturing a scoped dependency — causes subtle state and concurrency bugs.

## Question 008

### Question
What is a captive dependency?

### Answer
A captive dependency occurs when a longer-lived service holds a reference to a shorter-lived one, such as a singleton injecting a scoped service. The short-lived dependency is then kept alive too long, leading to stale state or thread-safety problems.

## Question 009

### Question
What is the repository pattern's responsibility?

### Answer
A repository mediates between the domain and data mapping, exposing a collection-like interface for aggregate retrieval and storage. It lets the domain ignore persistence concerns and provides a seam for testing with in-memory implementations.

## Question 010

### Question
What is the unit of work pattern?

### Answer
A unit of work tracks the objects changed during a business transaction and coordinates writing them out as a single, consistent commit. It ensures multiple repository operations either all succeed or all roll back together.

## Question 011

### Question
How do repository and unit of work relate?

### Answer
Repositories handle retrieval and registration of individual aggregates, while the unit of work coordinates the overall transaction across them. In many ORMs the unit of work is the context/session, and repositories operate on top of it.

## Question 012

### Question
What is the risk of a generic repository over an ORM?

### Answer
A generic `Repository<T>` over an ORM often just re-wraps the ORM's own abstraction, adding indirection without value and hiding useful query capabilities. It can also leak `IQueryable`, defeating the encapsulation it was meant to provide.

## Question 013

### Question
What is the service layer's role?

### Answer
A service (application) layer orchestrates use cases: it coordinates domain objects, transactions, and infrastructure to fulfill a request. It holds workflow and coordination logic, not the core business rules, which belong in the domain.

## Question 014

### Question
What is the difference between application services and domain services?

### Answer
Application services orchestrate a use case and manage transactions and external concerns; domain services hold business logic that does not naturally belong to a single entity. Application services are thin coordinators; domain services contain real domain rules.

## Question 015

### Question
How do you keep controllers thin?

### Answer
Controllers should only translate transport (HTTP) to and from an application service call and return the result. Validation of shape, mapping, and invoking one service method is fine; business logic and orchestration belong behind the controller.

## Question 016

### Question
What is mapping between layers and why do it?

### Answer
Mapping converts between representations — entity to DTO, request to command — so each layer can have a model suited to its purpose. It prevents internal models from leaking outward and external contracts from constraining the domain.

## Question 017

### Question
What are the downsides of automatic mappers?

### Answer
Automatic mappers can hide mapping bugs behind convention, make refactoring riskier because the compiler does not catch mismatches, and obscure expensive or lossy conversions. For complex mappings, explicit code is often clearer and safer.

## Question 018

### Question
Where should validation live in a layered design?

### Answer
Surface-level validation (required fields, formats) belongs at the boundary so bad input is rejected early; invariants and business rules belong in the domain so they hold regardless of entry point. Duplicating only the cheap checks at the edge is acceptable.

## Question 019

### Question
How should exceptions be handled across layers?

### Answer
Let exceptions propagate to a centralized handler that translates them into appropriate responses (e.g. an HTTP status), rather than catching and swallowing them in each layer. Lower layers throw meaningful exceptions; the boundary decides how to present them.

## Question 020

### Question
What is the difference between checked-style and unchecked error handling philosophies?

### Answer
One philosophy forces callers to handle declared errors explicitly; the other lets exceptions propagate until something handles them. In .NET, exceptions are unchecked, so discipline and documentation, not the compiler, ensure errors are handled at the right level.

## Question 021

### Question
What does "fail fast" mean architecturally?

### Answer
Fail fast means detecting invalid state or configuration as early as possible and stopping, rather than continuing in a corrupt state. Validating configuration at startup and guarding invariants in constructors are typical applications.

## Question 022

### Question
What is defensive programming and its trade-off?

### Answer
Defensive programming guards against invalid inputs and states with checks throughout the code. It increases robustness but, taken too far, clutters code and hides where responsibility for validity actually lies; clear boundaries reduce the need for it everywhere.

## Question 023

### Question
What is the difference between organizing code by layer and by feature?

### Answer
Layer-based organization groups all controllers, services, and repositories separately; feature-based groups everything for one feature together. Feature-based (vertical slices) keeps related changes in one place and scales better in large codebases.

## Question 024

### Question
What is a vertical slice?

### Answer
A vertical slice contains everything needed for one feature — endpoint, logic, data access — grouped together rather than spread across horizontal layers. It localizes change and reduces the coupling that horizontal layering can encourage.

## Question 025

### Question
What is package by feature versus package by layer?

### Answer
Package by layer groups types by technical role; package by feature groups by business capability. Feature packaging improves cohesion and makes it obvious what the system does, while layer packaging can scatter a single change across many folders.

## Question 026

### Question
What is the dependency rule in clean architecture terms?

### Answer
Source-code dependencies must point inward, toward higher-level policy, never outward toward details. Inner layers (domain) know nothing about outer layers (UI, database); outer layers depend on inner abstractions.

## Question 027

### Question
What is the role of interfaces at layer boundaries?

### Answer
Interfaces define what an inner layer needs without depending on how an outer layer provides it, allowing dependencies to be inverted. The domain declares an `IOrderRepository`; the infrastructure implements it, keeping the dependency arrow pointing inward.

## Question 028

### Question
What is a port and adapter at a basic level?

### Answer
A port is an interface defined by the application expressing a need (e.g. "send email"); an adapter is a concrete implementation that fulfills it (e.g. an SMTP client). This keeps the core independent of specific technologies.

## Question 029

### Question
What is the difference between a primary and secondary adapter?

### Answer
Primary (driving) adapters initiate calls into the application, like a web controller; secondary (driven) adapters are called by the application, like a database or message client. The application core sits in the middle, depending on neither directly.

## Question 030

### Question
Why should the domain not reference the database technology?

### Answer
Keeping the domain free of database types means business rules can be tested without a database and the storage choice can change without rewriting logic. It enforces that persistence is a detail serving the domain, not the other way around.

## Question 031

### Question
What is a leaky abstraction?

### Answer
A leaky abstraction is one whose underlying details show through and force callers to know about them, such as a repository that exposes `IQueryable` with provider-specific quirks. Leaks undermine the decoupling the abstraction was supposed to provide.

## Question 032

### Question
How do you handle cross-cutting concerns without scattering code?

### Answer
Apply them through shared mechanisms: middleware/pipeline, decorators, aspect-oriented interception, or base behaviors, so the concern is defined once and applied uniformly. This keeps logging, validation, and retries out of every method body.

## Question 033

### Question
What is the decorator pattern's architectural value?

### Answer
A decorator wraps a component to add behavior (logging, caching, retries) while preserving its interface, letting you compose cross-cutting concerns without modifying the original. It follows open/closed and keeps each concern isolated.

## Question 034

### Question
What is middleware in a request pipeline?

### Answer
Middleware are pipeline stages that each can act before and after the next, used for cross-cutting concerns like auth, logging, and error handling. Their order matters because each wraps the rest, and misordering changes behavior.

## Question 035

### Question
What is the command pattern's basic idea?

### Answer
The command pattern represents a request as an object containing the action and its data, decoupling the sender from the handler. It enables queuing, logging, undo, and a uniform way to dispatch operations.

## Question 036

### Question
What is the difference between a command and a query conceptually?

### Answer
A command changes state and ideally returns nothing meaningful; a query returns data and changes nothing. Separating them clarifies intent and is the basis of command-query separation.

## Question 037

### Question
What is command-query separation (CQS)?

### Answer
CQS is the principle that a method should either change state (command) or return data (query), but not both. Following it makes code easier to reason about because queries are safe to call freely and have no side effects.

## Question 038

### Question
What is an event in application architecture?

### Answer
An event is a notification that something happened, allowing interested components to react without the source knowing about them. Events decouple producers from consumers and enable extensible, reactive designs.

## Question 039

### Question
What is the observer pattern?

### Answer
The observer pattern lets subjects notify a list of observers when their state changes, without the subject depending on concrete observer types. It is the foundation of event systems and data binding.

## Question 040

### Question
What is the publish-subscribe pattern at a basic level?

### Answer
Publishers send messages to a channel and subscribers receive them, without either knowing the other directly. It generalizes observer across process boundaries and supports many-to-many, time-decoupled communication.

## Question 041

### Question
What is the difference between observer and pub/sub?

### Answer
Observer is usually in-process with subjects directly holding observers; pub/sub introduces a broker/channel that fully decouples publishers and subscribers, often across processes. The broker adds flexibility at the cost of an extra moving part.

## Question 042

### Question
What is the mediator pattern?

### Answer
A mediator centralizes communication between components so they talk to the mediator instead of each other, reducing direct coupling. It is useful when many components interact, though an overgrown mediator can itself become a God object.

## Question 043

### Question
What is a domain event?

### Answer
A domain event records something significant that happened in the domain (e.g. OrderPlaced), expressed in business language. It lets side effects and other parts of the system react without the originating aggregate depending on them.

## Question 044

### Question
What is eventual consistency at an introductory level?

### Answer
Eventual consistency means different parts of a system may be briefly out of sync but converge to a consistent state over time. It is a trade-off accepted in distributed systems to gain availability and scalability.

## Question 045

### Question
What is the difference between strong and eventual consistency?

### Answer
Strong consistency guarantees every read sees the latest write immediately; eventual consistency allows temporary staleness with convergence later. Strong is simpler to reason about; eventual scales better and tolerates partitions.

## Question 046

### Question
What is idempotency and why does it matter for retries?

### Answer
An idempotent operation can be applied multiple times with the same effect as once, so retries after uncertain failures are safe. Without idempotency, a retried "create payment" could charge twice, which is why it is central to reliable messaging.

## Question 047

### Question
How can you make an operation idempotent?

### Answer
Use a unique request/idempotency key the server records, so duplicate requests are recognized and not re-applied, or design the operation to be naturally repeatable (set to a value rather than increment). The key approach is the most general.

## Question 048

### Question
What is a transaction boundary?

### Answer
A transaction boundary defines the scope within which a set of changes is committed atomically. Choosing it well — usually around a single business operation/aggregate — keeps data consistent without holding locks longer than necessary.

## Question 049

### Question
Why keep transactions short?

### Answer
Long transactions hold locks and resources, increasing contention, deadlocks, and the chance of conflicts, which hurts throughput. Short transactions touching the minimum data keep the system responsive under load.

## Question 050

### Question
What is optimistic versus pessimistic concurrency at a basic level?

### Answer
Optimistic concurrency assumes conflicts are rare and detects them at commit (e.g. via a version column), retrying if needed; pessimistic concurrency locks data up front to prevent conflicts. Optimistic scales better; pessimistic suits high-contention hotspots.

## Question 051

### Question
What is the role of an aggregate at an introductory level?

### Answer
An aggregate is a cluster of related objects treated as one unit for changes, with a single root that guards its invariants. External code references only the root, which keeps the data consistent within a clear boundary.

## Question 052

### Question
What is an aggregate root?

### Answer
The aggregate root is the single entry point through which the rest of the aggregate is accessed and modified, enforcing the aggregate's rules. Repositories deal in aggregate roots, not in the internal members.

## Question 053

### Question
What is a value object at an introductory level?

### Answer
A value object is defined entirely by its attributes, has no identity, and is immutable (e.g. Money, DateRange). Equality is by value, which makes it safe to share and easy to reason about.

## Question 054

### Question
What is the difference between an entity and a value object?

### Answer
An entity has a distinct identity that persists through changes (a Customer with an Id); a value object is interchangeable when its values match and has no identity. Modeling something as the wrong one leads to awkward code and bugs.

## Question 055

### Question
What is a bounded context at an introductory level?

### Answer
A bounded context is a boundary within which a model and its terms have a single, consistent meaning. The same word (e.g. "Account") can mean different things in different contexts, and the boundary prevents those meanings from clashing.

## Question 056

### Question
Why can the same concept differ across contexts?

### Answer
Different parts of a business care about different aspects of a concept, so forcing one shared model creates a bloated, conflicting design. Separate bounded contexts let each model what it needs and translate at the edges.

## Question 057

### Question
What is the ubiquitous language?

### Answer
The ubiquitous language is a shared vocabulary used consistently by developers and domain experts within a bounded context, reflected directly in the code. It reduces translation errors between business intent and implementation.

## Question 058

### Question
What is a use case in clean architecture?

### Answer
A use case (interactor) encapsulates one application-specific operation, orchestrating domain objects to achieve a goal. It expresses what the application does in terms independent of UI and frameworks.

## Question 059

### Question
What is the difference between domain logic and application logic?

### Answer
Domain logic is the business rules that are true regardless of the application (how a discount is calculated); application logic is the orchestration specific to a use case (load, apply, save, notify). Keeping them separate keeps the domain reusable.

## Question 060

### Question
What is a façade and when is it useful?

### Answer
A façade provides a simplified, unified interface over a complex subsystem so callers do not deal with its parts directly. It reduces coupling to the subsystem and gives a clear entry point, useful when integrating complicated or legacy components.

## Question 061

### Question
What is the difference between orchestration and choreography at an introductory level?

### Answer
Orchestration has a central coordinator directing steps; choreography has components react to events without a central controller. Orchestration is easier to follow; choreography is more decoupled but harder to trace.

## Question 062

### Question
What is coupling between modules versus within a module?

### Answer
Coupling within a module (between closely related parts) is expected and fine; coupling between modules should be minimized and go through clear interfaces. The goal is loose coupling across boundaries and high cohesion inside them.

## Question 063

### Question
What is afferent versus efferent coupling?

### Answer
Afferent coupling counts how many things depend on a module (incoming); efferent counts how many things it depends on (outgoing). High afferent means a module is widely used and should be stable; high efferent means it is sensitive to others' changes.

## Question 064

### Question
What is the stable dependencies principle?

### Answer
Modules should depend in the direction of greater stability: volatile modules may depend on stable ones, not vice versa. Otherwise a frequently changing module forces churn on everything that depends on it.

## Question 065

### Question
What is the stable abstractions principle?

### Answer
The more stable a module, the more abstract it should be, so it can be extended without modification. Stable but concrete modules are rigid because they are hard to change yet depended upon by many.

## Question 066

### Question
What is the difference between a DTO and a domain entity?

### Answer
A DTO is a flat data carrier shaped for transfer with no behavior or invariants; a domain entity has identity, behavior, and rules protecting its state. Returning entities directly to clients couples the API to the domain and risks exposing internals.

## Question 067

### Question
What is over-fetching and under-fetching?

### Answer
Over-fetching returns more data than the caller needs; under-fetching returns too little, forcing extra requests. Both hurt performance, and they motivate tailoring responses (e.g. with projections or GraphQL) to actual needs.

## Question 068

### Question
What is the N+1 problem at an architectural level?

### Answer
The N+1 problem is loading a list and then issuing one extra query per item to fetch related data, multiplying round trips. It is an abstraction leak where lazy loading hides the cost of access patterns and degrades performance under scale.

## Question 069

### Question
What is a projection?

### Answer
A projection reads exactly the fields a use case needs into a purpose-built shape, rather than loading whole entities. It reduces data transfer and decouples read models from the write model.

## Question 070

### Question
What is the read model versus write model distinction?

### Answer
A write model is optimized to enforce invariants when changing state; a read model is optimized for querying and display. Separating them lets each be shaped for its job, which is the seed of CQRS.

## Question 071

### Question
What is CQRS at an introductory level?

### Answer
Command Query Responsibility Segregation separates the model that handles writes from the model that handles reads, so each can be designed and scaled independently. It adds complexity, so it is justified when read and write needs genuinely diverge.

## Question 072

### Question
When is CQRS not worth it?

### Answer
For simple CRUD where reads and writes share the same shape and load, CQRS adds models, synchronization, and cognitive overhead without payoff. It earns its keep only when query and command requirements truly differ.

## Question 073

### Question
What is a layering violation and how do you detect it?

### Answer
A layering violation is a dependency that skips or reverses the intended direction, such as the domain referencing the UI. You detect it by inspecting the dependency graph or enforcing rules with architecture tests that fail the build.

## Question 074

### Question
What is an architecture test?

### Answer
An architecture test is an automated check that the code conforms to structural rules, like "domain must not reference infrastructure." It turns architectural intent into something the build enforces, preventing slow erosion.

## Question 075

### Question
What is the strangler approach at an introductory level?

### Answer
The strangler approach incrementally replaces an old system by routing functionality to new code piece by piece until the old system can be retired. It avoids the risk of a big-bang rewrite by delivering and validating in small steps.

## Question 076

### Question
What is a feature flag and its architectural use?

### Answer
A feature flag is a runtime switch that turns functionality on or off without redeploying. Architecturally it decouples deployment from release, enabling gradual rollouts, A/B testing, and quick disabling of problematic features.

## Question 077

### Question
What is configuration drift?

### Answer
Configuration drift is when environments diverge over time because changes were applied inconsistently. It causes "works on my machine" failures, and is countered by treating configuration as code and provisioning environments reproducibly.

## Question 078

### Question
What is the twelve-factor idea of config in the environment?

### Answer
It states that configuration that varies between deployments should come from the environment, not be baked into the build. This keeps one immutable artifact deployable everywhere, with behavior driven by external settings and secrets.

## Question 079

### Question
What is a smoke test versus a full test suite architecturally?

### Answer
A smoke test quickly verifies that the system starts and core paths work; the full suite verifies detailed behavior. Smoke tests give fast confidence after deployment, while the full suite guards correctness during development.

## Question 080

### Question
What is the test pyramid?

### Answer
The test pyramid recommends many fast unit tests, fewer integration tests, and few slow end-to-end tests. Its shape reflects that lower tests are cheaper and more stable, and an architecture that enables it is usually well-decoupled.

## Question 081

### Question
What is an integration seam between your code and a third party?

### Answer
It is the abstraction (interface/adapter) you place between your code and an external dependency so the rest of the system depends on your contract, not theirs. It isolates change and lets you fake the dependency in tests.

## Question 082

### Question
Why wrap third-party libraries behind your own interface?

### Answer
Wrapping limits the blast radius if the library changes or is replaced, lets you adapt its API to your needs, and makes your code testable. The cost is an extra layer, so it is most worthwhile for volatile or central dependencies.

## Question 083

### Question
What is the difference between a hard and soft dependency?

### Answer
A hard dependency is required for the component to function; a soft dependency is optional or can degrade gracefully if absent. Distinguishing them informs resilience design — soft dependencies should not take the system down.

## Question 084

### Question
What is graceful degradation?

### Answer
Graceful degradation means continuing to provide reduced functionality when a dependency fails, rather than failing entirely. For example, serving cached data when a downstream service is down keeps the core experience working.

## Question 085

### Question
What is a timeout and why is it essential?

### Answer
A timeout caps how long you wait for an operation before giving up. Without timeouts, a slow or hung dependency can exhaust threads and cascade into a full outage, so every remote call should have one.

## Question 086

### Question
What is a retry and its danger?

### Answer
A retry re-attempts a failed operation to recover from transient errors. Its danger is amplifying load on an already struggling dependency, so retries need backoff, limits, and idempotency to be safe.

## Question 087

### Question
What is exponential backoff?

### Answer
Exponential backoff increases the wait between retries (often with jitter) so clients do not hammer a recovering service in lockstep. It spreads retry load over time and reduces the chance of a retry storm.

## Question 088

### Question
What is a circuit breaker at an introductory level?

### Answer
A circuit breaker stops calling a failing dependency after repeated failures, "opening" to fail fast and giving the dependency time to recover before testing it again. It prevents wasted calls and cascading failure.

## Question 089

### Question
What is the bulkhead pattern at an introductory level?

### Answer
The bulkhead pattern isolates resources (e.g. separate thread pools or connection limits per dependency) so a failure in one area cannot consume all resources and sink the whole system. It contains blast radius like compartments in a ship.

## Question 090

### Question
What is the difference between availability and reliability?

### Answer
Availability is the fraction of time a system is up and serving; reliability is whether it performs correctly without failure over time. A system can be highly available yet unreliable if it returns wrong results.

## Question 091

### Question
What is the difference between latency and throughput?

### Answer
Latency is the time to handle one request; throughput is how many requests are handled per unit time. They can trade off — batching improves throughput but raises latency — so designs must target the relevant one.

## Question 092

### Question
What is the difference between scaling up and scaling out, with trade-offs?

### Answer
Scaling up (bigger machine) is simple but has a ceiling and a single point of failure; scaling out (more machines) is elastic and resilient but requires statelessness and coordination. Most scalable systems favor scaling out.

## Question 093

### Question
What makes a service horizontally scalable?

### Answer
Statelessness (no per-instance session in memory), externalized shared state (database, cache), and idempotent, independent request handling. Then a load balancer can route requests to any instance freely.

## Question 094

### Question
What is a load balancer's architectural role?

### Answer
A load balancer distributes incoming requests across multiple instances, enabling horizontal scale and removing single points of failure. It also supports health checks, so unhealthy instances stop receiving traffic.

## Question 095

### Question
What is a health check and how is it used?

### Answer
A health check is an endpoint reporting whether an instance is fit to serve, separating liveness (process alive) from readiness (able to handle traffic). Orchestrators and load balancers use it to route around or restart unhealthy instances.

## Question 096

### Question
What is the difference between a session and a token for state?

### Answer
A server session stores client state server-side keyed by an id, requiring sticky routing or shared session storage; a token (e.g. JWT) carries state with the request, enabling stateless servers. Tokens scale more easily but are harder to revoke.

## Question 097

### Question
Why are sticky sessions problematic for scaling?

### Answer
Sticky sessions tie a client to one instance, so losing that instance loses the session and load cannot be balanced freely. Externalizing session state or using stateless tokens removes the stickiness and improves resilience.

## Question 098

### Question
What is a dependency cycle between assemblies and how do you break it?

### Answer
It is two assemblies referencing each other, which prevents independent build and reuse and signals tangled responsibilities. You break it by extracting a shared abstraction into a third assembly that both depend on, inverting one direction.

## Question 099

### Question
What is the difference between a logical and physical architecture view?

### Answer
The logical view describes components and their relationships conceptually; the physical view describes how they map to processes, machines, and deployments. Confusing them leads to assuming logical separation implies physical separation, which it does not.

## Question 100

### Question
What separates a JuniorPlus from a Junior in architectural thinking?

### Answer
A JuniorPlus not only knows the principles but applies them with judgment: inverting dependencies deliberately, placing logic by reason-to-change, handling cross-cutting concerns systematically, and weighing trade-offs like consistency, retries, and scaling instead of following rules blindly.
