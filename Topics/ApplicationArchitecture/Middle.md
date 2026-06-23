# Application Architecture Middle Interview Questions

Question-answer interview preparation file for Application Architecture at Middle level.

## Question 001

### Question
Compare layered, hexagonal, and clean architecture.

### Answer
All three aim to isolate the domain from infrastructure; they differ in framing. Layered stacks concerns top to bottom with downward dependencies; hexagonal (ports/adapters) centers the application and surrounds it with interchangeable adapters; clean architecture adds explicit concentric layers with a strict inward dependency rule. In practice they converge on the same goal: a framework-independent core.

## Question 002

### Question
What is onion architecture and how does it differ from clean?

### Answer
Onion architecture arranges the domain at the center with application, then infrastructure and UI as outer rings, dependencies pointing inward. It is essentially the same idea as clean architecture, which formalizes the rings as entities, use cases, interface adapters, and frameworks, with the dependency rule made explicit.

## Question 003

### Question
What problem do all "ports and adapters" styles solve?

### Answer
They solve the tendency for business logic to become entangled with frameworks, databases, and delivery mechanisms, which makes it hard to test and change. By depending only on abstractions the core defines, the technology choices become pluggable details.

## Question 004

### Question
When is a strict clean architecture overkill?

### Answer
For small CRUD applications or short-lived services, the layers, interfaces, and mapping add ceremony that outweighs the benefit. The discipline pays off when the domain is complex, long-lived, or must survive technology changes; otherwise it is accidental complexity.

## Question 005

### Question
What is a modular monolith?

### Answer
A modular monolith is a single deployable application internally divided into well-bounded modules with explicit interfaces and minimal cross-module coupling. It keeps the operational simplicity of a monolith while gaining much of the maintainability and independent evolution usually attributed to services.

## Question 006

### Question
Why choose a modular monolith over microservices initially?

### Answer
It avoids the distributed-systems tax — network failures, eventual consistency, deployment complexity — while you are still discovering the right boundaries. Modules can later be extracted into services once boundaries are proven, making it a lower-risk starting point.

## Question 007

### Question
How do you enforce module boundaries in a monolith?

### Answer
Expose each module only through a public contract, forbid reaching into another module's internals, and enforce this with project structure, access modifiers, and architecture tests. Communication crosses boundaries via interfaces or in-process messages, not shared internal types.

## Question 008

### Question
What is the difference between a module and a microservice?

### Answer
Both are units of bounded responsibility, but a microservice is independently deployable with its own process and usually its own data store, communicating over the network. A module is logical and in-process. The microservice adds autonomy at the cost of distribution complexity.

## Question 009

### Question
What are the main costs of microservices?

### Answer
You inherit distributed-systems problems: network latency and failure, eventual consistency, distributed tracing, deployment and versioning across services, and data duplication. These costs are justified mainly by independent scaling and deployment of teams and components at significant scale.

## Question 010

### Question
What is a distributed monolith and why is it the worst outcome?

### Answer
A distributed monolith is services that are deployed separately but so tightly coupled they must be changed and released together. It pays the full cost of distribution while keeping the coupling of a monolith, getting the downsides of both with the benefits of neither.

## Question 011

### Question
How do you decide service boundaries?

### Answer
Align boundaries with bounded contexts and business capabilities, so each service owns a cohesive area and its data, and changes tend to stay within one service. Boundaries that cut across a single workflow create chatty coupling and distributed transactions, which is a warning sign.

## Question 012

### Question
What is data ownership in a microservices context?

### Answer
Each service should own its data exclusively, with no other service reading or writing its database directly. Shared databases recreate hidden coupling; instead, services expose data through APIs or events, preserving autonomy.

## Question 013

### Question
What is the shared-database anti-pattern?

### Answer
Multiple services reading and writing the same database couple them through the schema, so a change for one can break others and deployments must coordinate. It defeats the autonomy that justifies separate services and is a common cause of distributed monoliths.

## Question 014

### Question
What is the saga pattern?

### Answer
A saga manages a business transaction spanning multiple services as a sequence of local transactions, each with a compensating action to undo it if a later step fails. It replaces a distributed ACID transaction with eventual consistency plus compensation.

## Question 015

### Question
What is the difference between orchestrated and choreographed sagas?

### Answer
An orchestrated saga has a central coordinator telling each service what to do next; a choreographed saga has services react to each other's events without a coordinator. Orchestration is easier to monitor and reason about; choreography is more decoupled but harder to trace.

## Question 016

### Question
What is a compensating transaction?

### Answer
A compensating transaction semantically undoes the effect of a previously committed step when a later step fails, since you cannot roll back across services. For example, refunding a charge rather than rolling back a payment; the undo is a business operation, not a database rollback.

## Question 017

### Question
What is the outbox pattern and what problem does it solve?

### Answer
The outbox pattern writes a domain change and its outgoing message into the same database transaction, then a separate process publishes the message from the outbox. It solves the dual-write problem: guaranteeing the database update and the message are not lost or duplicated inconsistently.

## Question 018

### Question
What is the dual-write problem?

### Answer
The dual-write problem is needing to update a database and publish a message as one atomic action, but the two systems cannot share a transaction. If one succeeds and the other fails, state and notifications diverge; the outbox (or change data capture) is the standard remedy.

## Question 019

### Question
What is change data capture and how does it relate to the outbox?

### Answer
Change data capture reads the database's transaction log to emit events for committed changes. Like the outbox it guarantees events reflect committed state, but without an explicit outbox table, at the cost of coupling to the database's log mechanism.

## Question 020

### Question
What are at-least-once, at-most-once, and exactly-once delivery?

### Answer
At-least-once may deliver duplicates but never loses messages; at-most-once never duplicates but may lose; exactly-once is the ideal but is impractical end-to-end. Real systems choose at-least-once and make consumers idempotent to simulate exactly-once effects.

## Question 021

### Question
Why is exactly-once delivery effectively impossible across a network?

### Answer
Because a sender cannot distinguish a lost message from a lost acknowledgment, it must either risk losing or risk duplicating. The practical answer is at-least-once delivery with idempotent processing, which yields exactly-once effects without true exactly-once delivery.

## Question 022

### Question
What is idempotent consumption and how do you implement it?

### Answer
Idempotent consumption means processing the same message twice has the same effect as once. You implement it by recording processed message ids and skipping duplicates, or by designing handlers whose operations are naturally idempotent (upserts, set-to-value).

## Question 023

### Question
What is a dead-letter queue?

### Answer
A dead-letter queue holds messages that repeatedly fail processing so they do not block the main queue and can be inspected or replayed later. It prevents a single poison message from stalling the consumer and provides a recovery path.

## Question 024

### Question
What is message ordering and why is it hard in distributed systems?

### Answer
Ordering guarantees that messages are processed in the sequence they were produced. It is hard because partitioning, retries, and multiple consumers reorder delivery; preserving order usually requires partitioning by key and single-consumer-per-partition, which limits parallelism.

## Question 025

### Question
What is a message broker's role?

### Answer
A broker decouples producers and consumers by accepting, storing, routing, and delivering messages, often with guarantees about durability and delivery. It absorbs load spikes, enables asynchronous workflows, and lets components scale and fail independently.

## Question 026

### Question
What is the difference between a queue and a topic?

### Answer
A queue delivers each message to one consumer (work distribution); a topic delivers each message to all interested subscribers (broadcast). Queues suit competing-consumer load balancing; topics suit publish-subscribe fan-out.

## Question 027

### Question
What is the competing consumers pattern?

### Answer
Multiple consumer instances read from the same queue, each taking different messages, so processing scales horizontally. The broker ensures a message goes to only one consumer, providing automatic load distribution and resilience.

## Question 028

### Question
What is event-driven architecture?

### Answer
Event-driven architecture has components communicate primarily by producing and reacting to events rather than direct calls. It maximizes decoupling and extensibility but makes end-to-end flows implicit and harder to trace and reason about.

## Question 029

### Question
What is the difference between an event and a command in messaging?

### Answer
A command expresses intent directed at one handler ("place order") and may be rejected; an event states a fact that already happened ("order placed") to any interested party. Commands couple sender to a handler's expectation; events decouple and invert that relationship.

## Question 030

### Question
What is event sourcing at an introductory-to-intermediate level?

### Answer
Event sourcing persists state as an ordered log of events rather than current state, rebuilding state by replaying them. It gives a full audit trail and temporal queries, but adds complexity in versioning events, rebuilding projections, and handling schema evolution.

## Question 031

### Question
What are the trade-offs of event sourcing?

### Answer
Benefits include auditability, the ability to derive new read models, and temporal debugging; costs include event-schema evolution, eventual consistency of projections, replay performance, and a steeper learning curve. It is justified where history and auditability are first-class needs.

## Question 032

### Question
How does CQRS relate to event sourcing?

### Answer
They are independent but complementary: CQRS separates read and write models, and event sourcing is one way to implement the write side, with projections building the read side from events. You can use CQRS without event sourcing, and vice versa.

## Question 033

### Question
What is a projection in CQRS/event sourcing?

### Answer
A projection is a read model built by processing events into a shape optimized for queries. Multiple projections can serve different views from the same event stream, and they are eventually consistent with the writes that produced the events.

## Question 034

### Question
What caching strategies do you know and when do you use each?

### Answer
Cache-aside (load on miss, app manages the cache) is the common default; read-through/write-through have the cache sit in front and stay in sync; write-behind defers writes for throughput at the risk of loss. The choice depends on consistency needs and read/write ratio.

## Question 035

### Question
What is cache-aside (lazy loading)?

### Answer
The application checks the cache, and on a miss loads from the source and populates the cache. It is simple and resilient (the cache failing just means slower reads), but the first request after expiry is slow and stale data is possible until invalidation.

## Question 036

### Question
What is the difference between write-through and write-behind caching?

### Answer
Write-through writes to the cache and the store synchronously, keeping them consistent at the cost of write latency; write-behind writes to the cache and persists asynchronously, improving latency but risking data loss if the cache fails before flush.

## Question 037

### Question
What is a cache stampede and how do you prevent it?

### Answer
A cache stampede happens when a popular key expires and many requests simultaneously miss and recompute it, overloading the source. Mitigations include locking so one request recomputes while others wait, staggered expiration, and serving stale-while-revalidate.

## Question 038

### Question
What are cache eviction policies?

### Answer
Eviction policies decide what to drop when the cache is full, such as LRU (least recently used), LFU (least frequently used), or TTL-based expiry. The right policy depends on access patterns; LRU is a common general-purpose default.

## Question 039

### Question
What is the difference between a local and distributed cache?

### Answer
A local cache lives in the process (fast, but per-instance and inconsistent across instances); a distributed cache is shared across instances (consistent, but a network hop and a dependency). Many systems combine both in a tiered cache.

## Question 040

### Question
What API versioning strategies exist?

### Answer
Common strategies are URI versioning (/v2/...), header/media-type versioning, and query-parameter versioning. URI is the most visible and cache-friendly; header/media-type is cleaner but less obvious. The deeper goal is to minimize breaking changes so versioning is rarely needed.

## Question 041

### Question
How do you evolve an API without breaking clients?

### Answer
Make additive, backward-compatible changes: add optional fields and new endpoints, never remove or repurpose existing ones, and tolerate unknown fields. When a breaking change is unavoidable, introduce a new version and run both during a deprecation window.

## Question 042

### Question
What is tolerant reader and why does it help?

### Answer
A tolerant reader ignores unknown fields and only reads what it needs, so producers can add data without breaking consumers. It is a key practice for evolving message and API schemas independently across services.

## Question 043

### Question
What is the difference between REST and gRPC at an intermediate level?

### Answer
REST uses HTTP and typically JSON, is human-readable and broadly compatible; gRPC uses HTTP/2 and binary protobuf, offering lower latency, streaming, and strong contracts via code generation. gRPC suits internal service-to-service calls; REST suits public and browser-facing APIs.

## Question 044

### Question
When would you choose messaging over synchronous calls between services?

### Answer
Choose messaging when you want temporal decoupling, load leveling, and resilience to downstream outages, or when the operation is naturally fire-and-forget. Choose synchronous calls when the caller needs an immediate result and the workflow is simple and reliable.

## Question 045

### Question
What is backpressure?

### Answer
Backpressure is a mechanism for a consumer to signal it cannot keep up so producers slow down, preventing unbounded queues and memory exhaustion. Without it, a fast producer can overwhelm a slow consumer and crash the system.

## Question 046

### Question
What is the difference between push and pull consumption?

### Answer
In push, the broker delivers messages to consumers as they arrive; in pull, consumers fetch when ready. Pull gives consumers natural backpressure control; push can be lower latency but risks overwhelming a slow consumer.

## Question 047

### Question
What is a correlation id and why is it important?

### Answer
A correlation id is a unique identifier propagated through all the calls and messages of a single logical request, letting you trace its path across services. It is essential for debugging and observability in distributed systems where one request spans many components.

## Question 048

### Question
What is distributed tracing?

### Answer
Distributed tracing records the path of a request across services as a tree of timed spans linked by trace and span ids. It reveals where latency and failures occur in a multi-service flow that no single service's logs could show.

## Question 049

### Question
What are the three pillars of observability?

### Answer
Logs (discrete events), metrics (aggregated numeric measurements over time), and traces (request paths across services). Together they let you detect, diagnose, and understand behavior in production rather than guessing.

## Question 050

### Question
What is the difference between monitoring and observability?

### Answer
Monitoring watches known indicators and alerts on predefined conditions; observability is the property of being able to ask new questions about a system's internal state from its outputs. Monitoring tells you something is wrong; observability helps you understand why, including for unforeseen problems.

## Question 051

### Question
What is the CAP theorem?

### Answer
CAP states that a distributed data store can guarantee at most two of consistency, availability, and partition tolerance simultaneously. Since network partitions are unavoidable, the real choice during a partition is between consistency and availability.

## Question 052

### Question
Why is partition tolerance effectively mandatory?

### Answer
Networks inevitably drop or delay messages, so a real distributed system must keep functioning despite partitions. That reduces CAP to a practical choice between staying consistent (rejecting requests) or staying available (serving possibly stale data) when a partition happens.

## Question 053

### Question
What is the difference between CAP and PACELC?

### Answer
PACELC extends CAP by adding that, even without a partition (Else), there is a trade-off between latency and consistency. It captures that consistency costs latency in normal operation, not only during partitions, giving a fuller picture of distributed trade-offs.

## Question 054

### Question
What consistency models exist beyond strong and eventual?

### Answer
Between them lie read-your-writes, monotonic reads, causal consistency, and session consistency, each guaranteeing specific orderings without full strong consistency. Choosing a model that matches user expectations (e.g. seeing your own edits) is often enough without paying for global strong consistency.

## Question 055

### Question
What is read-your-writes consistency?

### Answer
Read-your-writes guarantees a client always sees its own prior writes, even if others might see them later. It is often the minimum a user expects, and can be achieved by routing a user's reads to where their writes landed or by tracking versions.

## Question 056

### Question
What is the difference between data partitioning (sharding) and replication?

### Answer
Sharding splits data across nodes so each holds a subset, increasing capacity and write throughput; replication copies the same data to multiple nodes for availability and read scaling. They are complementary and usually combined.

## Question 057

### Question
What is a shard key and why does its choice matter?

### Answer
A shard key determines which shard a record lives on. A poor key creates hotspots (uneven load) or forces cross-shard queries and transactions, so it should distribute load evenly and align with common access patterns.

## Question 058

### Question
What problems arise from cross-shard queries and transactions?

### Answer
They require coordinating multiple nodes, which adds latency, complexity, and weak or expensive transactional guarantees. Good sharding minimizes them by colocating data accessed together, because scatter-gather queries scale poorly.

## Question 059

### Question
What is the difference between primary-replica and multi-primary replication?

### Answer
Primary-replica routes writes to one node and replicates to read-only replicas, simplifying consistency but limiting write scale and creating a failover point. Multi-primary accepts writes on several nodes, scaling writes but requiring conflict resolution.

## Question 060

### Question
What is replication lag and what does it cause?

### Answer
Replication lag is the delay before a replica reflects the primary's latest writes. It causes stale reads from replicas, which can break read-your-writes expectations, so writes-then-reads sometimes must target the primary.

## Question 061

### Question
What is a quorum in distributed reads/writes?

### Answer
A quorum requires a minimum number of nodes to acknowledge a read or write. Choosing read and write quorums so they overlap (R + W > N) guarantees reads see the latest write, tuning the consistency/availability balance.

## Question 062

### Question
What is the two-phase commit and its weakness?

### Answer
Two-phase commit coordinates an atomic commit across resources via a prepare then commit phase. Its weakness is blocking: if the coordinator fails after prepare, participants are stuck holding locks, making it fragile and a poor fit for scalable microservices.

## Question 063

### Question
Why are distributed transactions avoided in microservices?

### Answer
They couple services, hold locks across the network, scale poorly, and create availability risk if any participant or the coordinator fails. Sagas with eventual consistency and compensation are preferred because they keep services autonomous.

## Question 064

### Question
What is an anti-corruption layer?

### Answer
An anti-corruption layer translates between your model and an external or legacy model so the foreign concepts do not leak into and corrupt your domain. It isolates your bounded context from another's design and lets you integrate without adopting its quirks.

## Question 065

### Question
What is context mapping in DDD?

### Answer
Context mapping describes the relationships between bounded contexts — shared kernel, customer-supplier, conformist, anti-corruption layer — clarifying how teams and models integrate. It makes integration politics and technical coupling explicit instead of accidental.

## Question 066

### Question
What is the difference between a shared kernel and a customer-supplier relationship?

### Answer
A shared kernel is a small model both contexts share and co-own, requiring tight coordination; customer-supplier is an upstream/downstream relationship where the supplier accommodates the customer's needs. Each implies different team coupling and change processes.

## Question 067

### Question
What is a conformist relationship and when is it acceptable?

### Answer
A conformist downstream simply adopts the upstream model as-is without translation, accepting its terms because translating is not worth it. It is acceptable when the upstream is stable and authoritative and you lack leverage to influence it.

## Question 068

### Question
How do you keep aggregates small and why?

### Answer
Keep aggregates to the minimum needed to enforce a single invariant boundary, referencing other aggregates by id rather than embedding them. Small aggregates reduce contention, lock scope, and load size, and they keep transactions short.

## Question 069

### Question
Why reference other aggregates by id instead of by object?

### Answer
Referencing by id keeps aggregate boundaries clear, avoids loading large object graphs, and prevents one transaction from spanning multiple aggregates. It enforces that each aggregate is its own consistency and transaction unit.

## Question 070

### Question
What is the rule about one transaction per aggregate?

### Answer
A common DDD guideline is to modify only one aggregate per transaction, and update others asynchronously via domain events. This keeps transactions small and avoids distributed-transaction-like contention within a single store.

## Question 071

### Question
What is a domain event versus an integration event?

### Answer
A domain event is internal to a bounded context, expressed in its language and consumed in-process; an integration event is published across context or service boundaries with a stable, versioned contract. Mixing them leaks internal models across boundaries.

## Question 072

### Question
How do you version events and messages?

### Answer
Favor additive, backward-compatible changes and tolerant readers; when breaking, introduce a new event version and support both during transition, or upcast old events to the new shape. Event-sourced systems especially need a deliberate schema-evolution strategy.

## Question 073

### Question
What is schema-on-read versus schema-on-write?

### Answer
Schema-on-write validates and structures data when stored (relational databases); schema-on-read stores raw data and interprets it when read (data lakes). Schema-on-write gives integrity and query performance; schema-on-read gives flexibility for evolving or varied data.

## Question 074

### Question
What is the strangler fig pattern in detail?

### Answer
You place a façade in front of a legacy system and incrementally route specific functions to new implementations, growing the new system around the old until the old can be removed. It delivers value continuously and de-risks migration compared to a rewrite.

## Question 075

### Question
What is a branch by abstraction?

### Answer
Branch by abstraction introduces an abstraction over a component you want to replace, builds the new implementation behind it, switches over, then removes the old. It lets large changes happen incrementally on the mainline without long-lived branches.

## Question 076

### Question
What is the difference between blue-green and canary deployment?

### Answer
Blue-green keeps two production environments and switches all traffic at once for instant rollback; canary releases to a small subset of users first and gradually expands while watching metrics. Canary limits blast radius; blue-green optimizes for fast cutover and rollback.

## Question 077

### Question
What is a feature toggle's lifecycle concern?

### Answer
Toggles accumulate as technical debt: stale flags complicate code paths and testing. A disciplined lifecycle removes flags promptly after rollout, and distinguishes short-lived release toggles from long-lived operational or permission toggles.

## Question 078

### Question
What is idempotency at the API boundary and how is it implemented?

### Answer
An idempotent API returns the same result for repeated identical requests, typically via a client-supplied idempotency key the server stores with the result. This makes safe retries possible over unreliable networks without duplicate side effects like double charges.

## Question 079

### Question
What is rate limiting and what algorithms are used?

### Answer
Rate limiting caps request volume to protect resources, using algorithms like fixed window, sliding window, token bucket, or leaky bucket. Token bucket allows bursts up to a limit while enforcing an average rate, which suits most APIs.

## Question 080

### Question
What is the difference between throttling and load shedding?

### Answer
Throttling slows or rejects requests above a rate to stay within capacity; load shedding deliberately drops lower-priority work under overload to protect critical functions. Both trade some requests to keep the system alive, but shedding prioritizes by importance.

## Question 081

### Question
What is graceful shutdown and why does it matter?

### Answer
Graceful shutdown stops accepting new work, finishes or safely abandons in-flight work, and releases resources before exiting. It prevents data loss and dropped requests during deployments and scaling, which are routine events in modern systems.

## Question 082

### Question
What is the difference between liveness and readiness probes?

### Answer
A liveness probe checks whether the process should be restarted (it is hung); a readiness probe checks whether it should receive traffic (dependencies ready). Conflating them causes needless restarts or routing traffic to instances that cannot serve.

## Question 083

### Question
What is the thundering herd problem?

### Answer
The thundering herd is many clients simultaneously retrying or recovering at once — after a cache expiry or service restart — and overwhelming a resource. Jittered backoff, request coalescing, and staggered expiry spread the load to prevent it.

## Question 084

### Question
What is connection pooling and why is it architecturally important?

### Answer
Connection pooling reuses a limited set of expensive connections (e.g. to a database) across requests instead of opening one per request. It bounds resource use and latency, but the pool size becomes a scaling constraint that must be tuned against the backend's limits.

## Question 085

### Question
How can connection pool exhaustion cascade into an outage?

### Answer
If requests hold connections too long (slow queries, missing timeouts), the pool empties and new requests block, threads pile up, and the service stops responding even though the database is fine. Timeouts and short transactions prevent this cascade.

## Question 086

### Question
What is the difference between a gateway and a load balancer?

### Answer
A load balancer distributes traffic across instances; an API gateway is an application-aware entry point adding routing, auth, rate limiting, aggregation, and protocol translation. A gateway often sits in front of and uses load balancing internally.

## Question 087

### Question
What is the backend-for-frontend pattern?

### Answer
A backend-for-frontend is a dedicated API tailored to one client type (web, mobile), aggregating and shaping data so each client gets exactly what it needs. It avoids a one-size-fits-all API that over- or under-serves different clients.

## Question 088

### Question
What is API composition versus a materialized view for cross-service queries?

### Answer
API composition queries multiple services at request time and joins in the gateway, which is simple but adds latency and coupling; a materialized view pre-joins data via events into a read store for fast queries at the cost of eventual consistency and storage. The choice trades latency against freshness.

## Question 089

### Question
What is the difference between a synchronous and an asynchronous request-reply?

### Answer
Synchronous request-reply blocks the caller until a response returns; asynchronous request-reply sends a request and receives the reply later via a callback or correlated message. Async suits long-running work and resilience but needs correlation and state to match replies to requests.

## Question 090

### Question
What is a poison message and how do you handle it?

### Answer
A poison message is one that repeatedly fails processing, potentially blocking a queue or causing infinite retries. Handle it with a retry limit and routing to a dead-letter queue for inspection, so one bad message does not stall the consumer.

## Question 091

### Question
What is the difference between coupling in time, space, and format?

### Answer
Temporal coupling requires both parties to be available simultaneously (sync calls); spatial coupling requires knowing each other's location; format coupling requires sharing a data schema. Messaging reduces temporal and spatial coupling, while contracts/schemas manage format coupling.

## Question 092

### Question
What is contract testing and why is it useful between services?

### Answer
Contract testing verifies that a provider's API and a consumer's expectations agree, without full integration tests. It catches breaking changes early and lets services be developed and deployed independently with confidence that their interactions still hold.

## Question 093

### Question
What is the difference between a smoke test, contract test, and end-to-end test in a microservices pipeline?

### Answer
Smoke tests check basic startup/health, contract tests verify pairwise service agreements, and end-to-end tests exercise full workflows across services. Relying mostly on end-to-end tests is brittle and slow; contract tests give most of the confidence far cheaper.

## Question 094

### Question
What is an architecture decision record (ADR)?

### Answer
An ADR is a short document capturing one significant decision, its context, the options, and the consequences. It preserves the reasoning behind choices so future teams understand why, and so decisions can be revisited deliberately rather than forgotten.

## Question 095

### Question
What is Conway's law and its practical implication?

### Answer
Conway's law observes that systems mirror the communication structure of the organizations that build them. The implication is that to get a desired architecture (e.g. autonomous services), you often must shape the teams accordingly — the "inverse Conway maneuver."

## Question 096

### Question
What is the difference between essential and accidental complexity?

### Answer
Essential complexity is inherent to the problem; accidental complexity comes from the tools, approaches, or designs we choose. Good architecture minimizes accidental complexity so the team's effort goes toward the problem, not fighting self-imposed difficulty.

## Question 097

### Question
What is a quality attribute (non-functional requirement) and why model it early?

### Answer
Quality attributes — performance, scalability, security, availability, maintainability — describe how well the system must do its job, not what it does. They drive structural decisions that are hard to retrofit, so they must shape architecture from the start.

## Question 098

### Question
How do you handle conflicting quality attributes?

### Answer
You make explicit trade-offs prioritized by business value, since attributes like consistency vs availability or latency vs throughput often conflict. Architecture is largely the discipline of choosing which qualities to favor where, and recording why.

## Question 099

### Question
What is the difference between a tactical and strategic design decision?

### Answer
Tactical decisions concern building blocks within a context (aggregates, patterns); strategic decisions concern boundaries and relationships between contexts and the overall structure. Strategic mistakes are far costlier, so they deserve the most scrutiny.

## Question 100

### Question
What distinguishes Middle-level architectural judgment?

### Answer
A Middle engineer chooses among real architectural styles and patterns based on concrete trade-offs — consistency vs availability, coupling vs autonomy, simplicity vs flexibility — and understands the failure modes of distributed systems. They design within and across boundaries deliberately, justify decisions, and avoid both under- and over-engineering.
