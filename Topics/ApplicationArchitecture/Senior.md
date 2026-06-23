# Application Architecture Senior Interview Questions

Question-answer interview preparation file for Application Architecture at Senior level.

## Question 001

### Question
How do you approach designing a large system for long-term maintainability?

### Answer
Start from the business capabilities and qualities that matter, define clear boundaries aligned to those capabilities, and keep the domain independent of delivery and infrastructure. Make the costly decisions explicit and reversible where possible, defer the rest, and put structural rules under automated enforcement so the design does not erode. Maintainability is won by managing dependencies and boundaries, not by adding patterns.

## Question 002

### Question
How do you decide between a monolith, modular monolith, and microservices?

### Answer
Default to the simplest option that meets current needs, usually a modular monolith, because it preserves clean boundaries without distribution costs. Move to services only when independent deployment, scaling, or team autonomy provides value that outweighs the operational and consistency tax. The decision is organizational as much as technical, and boundaries should be proven before extraction.

## Question 003

### Question
How do you identify bounded contexts in a complex domain?

### Answer
Look for places where language, rules, and the lifecycle of concepts shift — where the same word means different things or where different teams own different concerns. Techniques like event storming surface these seams by mapping the business flow and its events. The contexts follow the business's natural divisions of meaning and responsibility, not the database tables.

## Question 004

### Question
What is strategic design in DDD and why does it dominate outcomes?

### Answer
Strategic design concerns the boundaries between contexts and the relationships among them, deciding where models split and how they integrate. It dominates because boundary mistakes create coupling and conflicting models that no amount of clean code inside a context can fix, and they are extremely expensive to correct later.

## Question 005

### Question
How do you prevent a system from degrading into a distributed monolith?

### Answer
Enforce data ownership per service, communicate through stable contracts and events rather than shared schemas, and verify that services can be deployed independently. Watch for synchronous call chains and shared libraries that force coordinated releases; if changing one service routinely requires changing others, the boundaries are wrong and should be redrawn or recombined.

## Question 006

### Question
What is evolutionary architecture?

### Answer
Evolutionary architecture is designed to support guided, incremental change across multiple dimensions, treating change as expected rather than exceptional. It relies on fitness functions to protect important qualities as the system evolves, so the architecture can adapt to new requirements without losing its essential properties.

## Question 007

### Question
What is a fitness function?

### Answer
A fitness function is an automated, objective check that the architecture still meets a desired quality — performance budgets, dependency rules, security constraints. It turns architectural intent into continuously verified guardrails, so violations are caught by the pipeline instead of by slow decay.

## Question 008

### Question
How do you keep architecture from eroding over time?

### Answer
Encode the important constraints as executable checks (architecture tests, fitness functions) in the build, make boundaries visible in the code structure, and review significant decisions with ADRs. Erosion happens through many small unchecked exceptions, so the defense is automated, continuous enforcement rather than periodic manual review.

## Question 009

### Question
How does Conway's law shape your architecture choices?

### Answer
Because systems mirror team communication structures, you align team boundaries with the desired service or module boundaries — the inverse Conway maneuver. Trying to impose an architecture that fights the org chart usually fails, so changing team structure is often a prerequisite for changing architecture.

## Question 010

### Question
What are team topologies and how do they relate to architecture?

### Answer
Team topologies describe team types (stream-aligned, platform, enabling, complicated-subsystem) and interaction modes designed to reduce cognitive load and coupling. They matter because a team's cognitive load limits how much architecture it can own well, so boundaries should give each team a comprehensible, autonomous slice.

## Question 011

### Question
How do you manage cognitive load when designing service boundaries?

### Answer
Size each boundary so one team can fully own and reason about it, including its data and operations, without needing deep knowledge of others. When a boundary exceeds a team's capacity, it produces shortcuts and coupling; splitting or providing platform support restores ownership and quality.

## Question 012

### Question
How do you approach a large legacy modernization?

### Answer
Avoid a big-bang rewrite; instead use the strangler pattern to carve out capabilities incrementally behind a façade, prioritizing by business value and risk. Establish characterization tests to lock current behavior, extract seams, and migrate one bounded slice at a time so value flows continuously and rollback stays cheap.

## Question 013

### Question
Why are big-bang rewrites usually a mistake?

### Answer
They stop delivering value for a long time, must reproduce undocumented behavior accurately, and accumulate risk that lands all at once at cutover. Meanwhile the old system keeps changing. Incremental replacement delivers value continuously and validates assumptions in small, recoverable steps.

## Question 014

### Question
How do you decide what to extract first from a monolith?

### Answer
Choose a bounded context with clear seams, meaningful independent value (its own scaling or deployment needs), and manageable data coupling. Starting with a low-risk, high-clarity slice proves the extraction machinery and builds confidence before tackling the tangled core.

## Question 015

### Question
How do you split a shared database during decomposition?

### Answer
Identify each table's owner by context, stop cross-context direct access in favor of APIs or events, then physically separate the data, often using the database-view or change-data-capture bridge during transition. The hardest part is breaking shared writes and reporting joins, which usually requires new read models fed by events.

## Question 016

### Question
How do you design for multi-tenancy?

### Answer
Choose an isolation model — separate database per tenant (strong isolation, higher cost), shared database with separate schemas, or shared schema with a tenant discriminator (cheapest, weakest isolation) — based on security, scale, and noisy-neighbor needs. Tenant context must be propagated and enforced consistently to prevent data leakage across tenants.

## Question 017

### Question
What are the trade-offs of the multi-tenancy isolation models?

### Answer
Database-per-tenant maximizes isolation and per-tenant customization but is costly and hard to operate at scale; shared-schema maximizes density and operational simplicity but risks cross-tenant leakage and noisy neighbors. Many systems tier the model, isolating large or sensitive tenants while pooling small ones.

## Question 018

### Question
How do you prevent noisy-neighbor problems in shared infrastructure?

### Answer
Apply per-tenant quotas, rate limits, and resource isolation (bulkheads, separate pools), and monitor per-tenant usage to detect outliers. Without isolation, one tenant's spike degrades everyone; the architecture must bound each tenant's resource consumption.

## Question 019

### Question
How do you approach capacity planning and scaling strategy?

### Answer
Identify the scaling dimension that will bind first (CPU, memory, IO, a hot dataset), design so that dimension can scale horizontally and statelessly, and remove single bottlenecks like a shared write database via sharding or CQRS read models. Validate with load testing against realistic patterns rather than guessing.

## Question 020

### Question
How do you find and address the real bottleneck in a system?

### Answer
Measure end-to-end with tracing and metrics to locate where latency and contention actually accumulate, rather than optimizing by intuition. The bottleneck is often a shared resource (a database, a lock, a queue), and addressing it may require architectural change, not just tuning, while watching that the bottleneck does not simply move.

## Question 021

### Question
How do you design for high availability?

### Answer
Eliminate single points of failure through redundancy across instances and zones, make components stateless or replicate state, add health checks with automated failover, and degrade gracefully when dependencies fail. Crucially, test failure (chaos experiments) so the failover paths are known to work, not assumed.

## Question 022

### Question
What is the difference between designing for failure and avoiding failure?

### Answer
Avoiding failure assumes components stay up and breaks badly when they do not; designing for failure assumes everything eventually fails and builds in timeouts, retries, circuit breakers, fallbacks, and isolation. At scale, partial failure is constant, so resilience must be a first-class design property.

## Question 023

### Question
How do you prevent cascading failures across services?

### Answer
Use timeouts everywhere, circuit breakers to stop calling failing dependencies, bulkheads to isolate resource pools, load shedding to protect critical paths, and backpressure to limit queue growth. The goal is to contain a failure to its origin instead of letting it propagate through synchronous dependency chains.

## Question 024

### Question
How do you reason about consistency requirements across a system?

### Answer
Identify which operations truly need strong consistency (money, inventory decrements) and keep those within a single aggregate or transaction, while letting the rest be eventually consistent with compensation. Treating everything as strongly consistent kills scalability; treating everything as eventual creates correctness bugs, so you map requirements per operation.

## Question 025

### Question
How do you handle a workflow that must touch several services consistently?

### Answer
Model it as a saga with local transactions and compensating actions, choosing orchestration for visibility or choreography for decoupling, and make each step idempotent. Use the outbox pattern to publish events reliably, and design the business process to tolerate intermediate states and compensation.

## Question 026

### Question
How do you design event schemas and contracts to evolve safely?

### Answer
Treat published contracts as long-lived public commitments: additive changes only, tolerant readers, explicit versioning when breaking, and consumer-driven contract tests to catch regressions. For event-sourced data, plan upcasting so old events remain replayable as the model evolves.

## Question 027

### Question
What governance do you put around shared contracts and APIs?

### Answer
Establish a schema registry or contract repository, compatibility rules enforced in CI, deprecation policies with clear windows, and ownership for each contract. Lightweight governance prevents the chaos of uncoordinated breaking changes while avoiding a central bottleneck that slows every team.

## Question 028

### Question
How do you balance autonomy with consistency across many teams?

### Answer
Give teams autonomy within their bounded context and standardize only at the seams — contracts, observability, security, deployment — through a platform and "paved roads" rather than mandates. Over-standardizing kills autonomy; under-standardizing creates an unoperable zoo, so you standardize the interfaces, not the internals.

## Question 029

### Question
What is platform thinking and a paved road?

### Answer
Platform thinking provides internal self-service capabilities (CI/CD, observability, infrastructure) so product teams focus on business value. A paved road is the well-supported default path that makes the right thing the easy thing, reducing cognitive load and divergence without forcing compliance.

## Question 030

### Question
How do you incorporate security into architecture (security by design)?

### Answer
Build in least privilege, defense in depth, secure defaults, and trust boundaries from the start, and threat-model significant designs to find attack surfaces early. Authentication and authorization are enforced at boundaries, secrets are managed centrally, and data is protected in transit and at rest, because bolting security on later leaves gaps.

## Question 031

### Question
What is threat modeling and when do you do it?

### Answer
Threat modeling systematically identifies assets, trust boundaries, and possible attacks (e.g. via STRIDE) so mitigations are designed in. You do it for new systems and significant changes, focusing on where data crosses trust boundaries, because that is where most vulnerabilities live.

## Question 032

### Question
What is a zero-trust architecture?

### Answer
Zero trust assumes no implicit trust based on network location, requiring every request to be authenticated, authorized, and encrypted regardless of origin. It replaces the perimeter model, where being "inside" granted access, which fails once an attacker breaches the perimeter.

## Question 033

### Question
How do you handle authorization in a distributed system?

### Answer
Authenticate once at the edge, propagate a verifiable identity (e.g. signed token) to downstream services, and enforce authorization locally where the resource and its rules live. Centralizing all authorization creates a bottleneck and couples services; the policy can be central while enforcement is distributed.

## Question 034

### Question
How do you manage secrets at scale?

### Answer
Keep secrets out of code and config files, store them in a dedicated secrets manager with access control and rotation, and inject them at runtime. Short-lived, automatically rotated credentials reduce the blast radius of leaks compared to long-lived static secrets.

## Question 035

### Question
How do you design data architecture for both transactional and analytical needs?

### Answer
Separate concerns: optimize operational stores for transactions, and feed analytical stores (warehouse/lake) via event streams or CDC rather than querying production databases directly. This avoids analytical load harming transactional performance and lets each store be modeled for its workload.

## Question 036

### Question
What is the difference between OLTP and OLAP and why separate them?

### Answer
OLTP handles many small concurrent transactions with strong consistency; OLAP handles large analytical scans and aggregations. Their workloads conflict on the same store, so separating them — with data flowing from OLTP to OLAP — keeps transactions fast and analytics scalable.

## Question 037

### Question
What is a data mesh at a high level?

### Answer
Data mesh treats analytical data as a product owned by the domain teams that produce it, with self-serve platform infrastructure and federated governance. It counters the bottleneck of a central data team by distributing ownership, mirroring microservices principles for analytics.

## Question 038

### Question
How do you decide build versus buy for a capability?

### Answer
Build what is core differentiating business logic; buy or use managed services for commodity capabilities (auth, messaging, search) where a vendor does it better and cheaper. The analysis weighs total cost of ownership, control, lock-in, and opportunity cost of engineering time, not just license price.

## Question 039

### Question
How do you reason about vendor lock-in?

### Answer
Assess the cost and likelihood of needing to switch against the value of using a provider's higher-level services fully. Abstracting everything to stay portable often forfeits the very benefits you are paying for; a pragmatic stance isolates only the parts likely to change and accepts deliberate lock-in elsewhere.

## Question 040

### Question
How do you account for cost in architecture?

### Answer
Treat cost as a quality attribute: model the cost of data egress, storage tiers, compute scaling, and managed services, and design for efficiency where volume makes it matter. Cost-aware architecture chooses where to spend (e.g. caching to cut database load) and surfaces per-feature cost so trade-offs are visible.

## Question 041

### Question
How do you handle a hot partition or hotspot at scale?

### Answer
Diagnose the skew, then redistribute load by choosing a better partition key, adding a hashing/salting prefix, caching the hot key, or splitting the hot entity. Hotspots come from access patterns concentrating on one key, so the fix is to spread that concentration without breaking required access paths.

## Question 042

### Question
How do you design idempotent, exactly-once-effect processing at scale?

### Answer
Use at-least-once delivery with idempotent handlers keyed on a stable id, deduplicating via a processed-id store or natural upserts, and the outbox to avoid lost or duplicated publishes. True exactly-once delivery is impractical, so you engineer exactly-once effects through idempotency.

## Question 043

### Question
How do you approach observability for a large distributed system?

### Answer
Standardize structured logging, metrics, and distributed tracing with consistent correlation ids across all services, and define SLOs with alerting on symptoms (user-facing impact) rather than every cause. The aim is to ask new questions about behavior in production, so instrument for the unknown, not only known dashboards.

## Question 044

### Question
What are SLOs, SLIs, and error budgets?

### Answer
An SLI is a measured indicator (e.g. latency, success rate); an SLO is the target for it; the error budget is the allowed shortfall from 100%. Error budgets turn reliability into a quantified trade-off: when the budget is spent, the team prioritizes stability over new features.

## Question 045

### Question
How do error budgets influence architectural and delivery decisions?

### Answer
They give an objective basis for balancing velocity against reliability: a healthy budget permits riskier changes and faster shipping, an exhausted one forces hardening and slows risky work. This replaces arguments about "how reliable is reliable enough" with a measured, shared policy.

## Question 046

### Question
How do you design a system to be testable end to end without brittle tests?

### Answer
Push verification down the pyramid: heavy unit and contract testing, modest integration testing, and a thin layer of end-to-end tests for critical journeys only. Provide deterministic test environments, isolate external dependencies behind seams, and use consumer-driven contracts so services can be verified independently.

## Question 047

### Question
How do you make a large architectural change with low risk?

### Answer
Decompose it into small, independently shippable steps behind abstractions or feature flags (branch by abstraction, expand-and-contract), keep the system releasable throughout, and verify each step in production with metrics. Avoid long-lived branches and coordinated big-bang cutovers, which concentrate and hide risk.

## Question 048

### Question
What is the expand-and-contract (parallel change) pattern?

### Answer
You first add the new form alongside the old (expand), migrate consumers and producers to it, then remove the old (contract). It allows backward-incompatible changes to databases, APIs, and events to be rolled out incrementally without a flag-day break.

## Question 049

### Question
How do you handle backward-incompatible database migrations safely?

### Answer
Use multi-phase migrations: add new columns/tables, dual-write and backfill, switch reads, then remove the old, each phase deployable independently and reversible. This keeps the application and schema compatible at every step, avoiding downtime and lockstep deploys.

## Question 050

### Question
How do you evaluate whether to adopt a new technology or pattern?

### Answer
Weigh the concrete problem it solves against its cost: operational burden, team learning curve, maturity, and the complexity it adds for everyone. Prefer boring, proven technology for foundations, reserve novelty for where it provides decisive advantage, and pilot before committing broadly.

## Question 051

### Question
How do you avoid over-engineering?

### Answer
Solve the problem in front of you with the simplest design that keeps obvious future options open, and defer decisions until you have the knowledge to make them well (the last responsible moment). Speculative flexibility is itself a cost; YAGNI and reversibility analysis keep complexity proportional to real need.

## Question 052

### Question
What is the "last responsible moment" for a decision?

### Answer
It is the latest point at which you can defer a decision without foreclosing important options or incurring excessive cost. Deferring until then maximizes the information you have while avoiding paralysis, and it is paired with keeping decisions reversible so deferral stays cheap.

## Question 053

### Question
How do you distinguish reversible from irreversible decisions, and why does it matter?

### Answer
Reversible (two-way-door) decisions can be undone cheaply; irreversible (one-way-door) ones are costly to reverse. You can move fast and delegate reversible decisions, but irreversible ones — public contracts, data models, core boundaries — deserve heavy scrutiny. Misclassifying them wastes time or creates lasting mistakes.

## Question 054

### Question
How do you handle a design where requirements are uncertain?

### Answer
Keep the architecture optionable: establish stable boundaries and contracts, but make internal choices easy to change, and validate assumptions with thin end-to-end slices early. You design for change rather than for a guessed final state, investing in reversibility instead of premature specificity.

## Question 055

### Question
How do you balance delivering features against paying down technical debt?

### Answer
Treat debt as a managed portfolio: track it, quantify its drag on delivery, and allocate a steady fraction of capacity to the debt that most slows current and near-term work. Framing it in terms of business impact (slower delivery, more incidents) wins prioritization that abstract "cleanliness" arguments do not.

## Question 056

### Question
When is taking on technical debt the right decision?

### Answer
Deliberate debt is justified to validate a hypothesis quickly, hit a critical deadline, or learn before committing, provided it is conscious, documented, and has a repayment plan. The danger is unintentional or unmanaged debt that compounds silently; intentional debt with a payback intent is a legitimate tool.

## Question 057

### Question
How do you communicate architecture to different audiences?

### Answer
Tailor the view: business stakeholders need capabilities and trade-offs in their terms, engineers need component and dependency detail, operators need deployment and failure behavior. Using consistent models (e.g. C4 levels of zoom) lets you show the right altitude to each audience without losing coherence.

## Question 058

### Question
What is the C4 model and what is it good for?

### Answer
C4 describes architecture at four zoom levels — context, container, component, code — so you can show the appropriate detail for the audience and question. Its value is a simple, consistent vocabulary for diagrams that stay maintainable, avoiding the ambiguity of ad-hoc box-and-line drawings.

## Question 059

### Question
How do you document architecture so it stays useful?

### Answer
Capture decisions as ADRs, keep a small set of high-level diagrams that explain structure and rationale, and generate or test what you can so docs do not drift. Documentation should explain the why and the boundaries; exhaustive low-level docs rot quickly and are better expressed as enforced code structure.

## Question 060

### Question
How do you evaluate and compare candidate architectures?

### Answer
Define the driving quality attributes and scenarios, then assess each candidate against them, surfacing trade-offs and risks rather than declaring a winner abstractly (the spirit of methods like ATAM). The output is an informed decision with known sensitivities and risks, recorded so it can be revisited.

## Question 061

### Question
How do you run an architecture review effectively?

### Answer
Anchor it on concrete quality-attribute scenarios and risks, invite the people who own the affected areas, and focus on trade-offs and assumptions rather than style preferences. The goal is to find risks and align on decisions, producing recorded outcomes, not to gatekeep or bikeshed.

## Question 062

### Question
How do you handle disagreement on an architectural decision within a team?

### Answer
Make the criteria and trade-offs explicit so the debate is about evidence, not opinion, time-box it, and use "disagree and commit" once a decision is made and recorded in an ADR. For reversible decisions, bias toward trying and learning; reserve prolonged debate for the genuinely irreversible.

## Question 063

### Question
How do you ensure non-functional requirements are met, not just functional ones?

### Answer
Turn them into measurable targets (SLOs, performance budgets, security controls) and verify them continuously with load tests, fitness functions, and monitoring, the same way you test features. Qualities that are not measured and enforced quietly degrade, so they need executable accountability.

## Question 064

### Question
How do you design for graceful degradation across a product?

### Answer
Classify features by criticality and design fallbacks for each dependency — cached or default data, queued deferral, reduced functionality — so the core experience survives partial outages. The architecture explicitly distinguishes must-work paths from nice-to-have ones and shields the former.

## Question 065

### Question
How do you handle a critical dependency that is unreliable?

### Answer
Isolate it behind an adapter with timeouts, circuit breaking, and a fallback, cache its results where staleness is acceptable, and make calls asynchronous or optional if the workflow allows. If it is both critical and unreliable, the architecture must either harden around it or reduce reliance on it.

## Question 066

### Question
How do you approach a system that must scale to very high write volume?

### Answer
Partition writes by key to spread load, avoid global contention points and distributed transactions, use append-friendly models (event logs) and asynchronous processing, and separate reads via CQRS so writes are not slowed by query load. The constraint is the single hottest write path, which must be decomposed.

## Question 067

### Question
How do you handle read scaling separately from write scaling?

### Answer
Add read replicas and caches, build denormalized read models tailored to queries (CQRS), and accept the resulting eventual consistency where tolerable. Decoupling read and write scaling lets you grow the far more common reads cheaply without overprovisioning the write path.

## Question 068

### Question
How do you design for global, low-latency access?

### Answer
Replicate data and serve from regions close to users, push static and cacheable content to a CDN, and choose per-data consistency models that tolerate cross-region replication lag. Global low latency forces explicit decisions about where the source of truth lives and how conflicts are resolved.

## Question 069

### Question
How do you resolve conflicts in multi-region or multi-primary writes?

### Answer
Use strategies suited to the data: last-write-wins with caution, application-level merge rules, or conflict-free replicated data types for commutative operations. The right choice depends on the business meaning of a conflict, since blindly overwriting can silently lose data.

## Question 070

### Question
How do you think about data residency and compliance in architecture?

### Answer
Treat regulatory constraints (where data may be stored and processed, retention, right-to-erasure) as hard architectural requirements that shape partitioning, replication, and data flows. Designing them in is essential because retrofitting data-locality and deletion guarantees onto a system that ignored them is extremely costly.

## Question 071

### Question
How do you implement the right-to-be-forgotten in an event-sourced system?

### Answer
Since events are immutable, you separate personal data from the event stream — storing it externally and keeping only a reference, or using crypto-shredding where deleting the key renders the data unrecoverable. This reconciles immutability with deletion requirements without rewriting history.

## Question 072

### Question
How do you decide what belongs in shared platform versus product teams?

### Answer
Put undifferentiated, broadly needed capabilities in the platform as self-service, and keep business-differentiating logic in product teams. The test is whether centralizing reduces total cognitive load and duplication without becoming a bottleneck; platform exists to accelerate product teams, not to gate them.

## Question 073

### Question
How do you avoid a central platform team becoming a bottleneck?

### Answer
Provide self-service, well-documented paved roads rather than ticket-based gatekeeping, treat internal capabilities as products with the teams as customers, and let teams opt in. The platform succeeds when teams use it because it is the easiest path, not because they are forced to wait on it.

## Question 074

### Question
How do you migrate from synchronous integration to event-driven without big risk?

### Answer
Introduce events alongside existing synchronous calls, build and validate the asynchronous consumers in parallel, then cut over per flow once proven, keeping the ability to fall back. You change one interaction at a time, verifying behavior with monitoring, rather than flipping the whole integration style at once.

## Question 075

### Question
How do you handle ordering and consistency when moving to async processing?

### Answer
Partition by the entity that needs ordering so a single consumer preserves per-key order, design handlers to be idempotent and tolerant of out-of-order or duplicate delivery, and rely on eventual consistency with reconciliation where needed. Global ordering is avoided because it does not scale.

## Question 076

### Question
How do you design a system to be debuggable in production?

### Answer
Ensure every request carries a correlation id through logs and traces, emit meaningful structured events at boundaries, expose internal state via metrics and health endpoints, and keep deployable, queryable history. The aim is that an on-call engineer can reconstruct what happened from the telemetry alone.

## Question 077

### Question
How do you approach chaos engineering?

### Answer
Form a hypothesis about steady-state behavior, inject realistic failures (latency, instance loss, dependency outage) in a controlled way, and verify the system degrades and recovers as designed. It validates resilience assumptions that otherwise only get tested during real incidents, and it should start small with safeguards.

## Question 078

### Question
How do you conduct and use blameless postmortems architecturally?

### Answer
Focus on systemic causes and contributing factors rather than individual fault, and feed the findings back into architectural and process improvements — added guardrails, fitness functions, or boundary changes. Recurrent incident patterns are signals about structural weaknesses the architecture must address.

## Question 079

### Question
How do you balance standardization and innovation across many teams?

### Answer
Standardize the seams and operational concerns to keep the system coherent and operable, while leaving room for teams to innovate inside their boundaries and to propose new paved roads. Too much standardization ossifies; too little fragments. The line sits at what must interoperate versus what is internal.

## Question 080

### Question
How do you handle a situation where the ideal architecture conflicts with delivery timelines?

### Answer
Identify which decisions are irreversible and must be right now, protect those, and take conscious, documented shortcuts on the reversible rest with a repayment plan. You sequence the architecture so the expensive-to-change parts are sound while non-critical polish is deferred, communicating the trade-off explicitly.

## Question 081

### Question
How do you design APIs that many external consumers depend on?

### Answer
Treat the contract as a long-term commitment: design for evolution (additive, tolerant readers, versioning), provide clear deprecation policies, and verify compatibility in CI. Because you cannot force all consumers to upgrade in lockstep, the architecture must support running multiple versions and changing the implementation behind a stable surface.

## Question 082

### Question
How do you manage shared libraries across services without recreating coupling?

### Answer
Limit shared code to stable, generic concerns (logging, contracts) and version it independently so services upgrade on their own schedule; avoid sharing domain logic, which recouples services. A shared library that forces coordinated releases is a distributed-monolith smell.

## Question 083

### Question
How do you decide between a library and a service for shared functionality?

### Answer
A library is simpler and faster but couples consumers to its version and runs in their process; a service centralizes logic and can evolve independently but adds a network call and operational cost. Choose a service when the logic must be consistent and centrally controlled, a library when it is stable and local.

## Question 084

### Question
How do you architect for cost-efficiency at scale?

### Answer
Make cost a measured quality: right-size resources, use autoscaling and spot/elastic capacity, cache to cut expensive backend calls, manage data lifecycle and storage tiers, and attribute cost per service/feature so trade-offs are visible. At scale, architectural choices about data movement and compute density dominate the bill.

## Question 085

### Question
How do you handle the trade-off between latency and consistency in a user-facing flow?

### Answer
Map each interaction to what the user actually needs: strong consistency where correctness is visible and critical, and faster eventual or cached responses elsewhere, often with optimistic UI that reconciles later. The decision is per-flow, guided by user expectations rather than a blanket policy.

## Question 086

### Question
How do you prevent the "big ball of mud" in a growing codebase?

### Answer
Maintain explicit boundaries with enforced dependency rules, keep cohesion high by packaging by capability, refactor continuously toward clearer seams, and review significant structural changes. Mud accumulates through unchecked shortcuts, so the defense is continuous, enforced discipline rather than a one-time cleanup.

## Question 087

### Question
How do you approach security architecture for sensitive data end to end?

### Answer
Classify data, minimize what you collect and retain, encrypt in transit and at rest, enforce least-privilege access with auditing, isolate sensitive workloads, and tokenize or pseudonymize where possible. Security is layered through the whole flow, and the architecture limits the blast radius of any single compromise.

## Question 088

### Question
How do you design audit and compliance into a system?

### Answer
Capture immutable, tamper-evident records of significant actions with who/what/when, separate audit storage from operational data, and align retention with regulation. Audit must be built in as a first-class concern because reconstructing it after the fact is unreliable and often impossible.

## Question 089

### Question
How do you evaluate the resilience of a design before it is built?

### Answer
Walk through failure scenarios for each dependency and component — what happens if it is slow, down, or returns errors — and confirm the design has a defined behavior (timeout, fallback, isolation) for each. Resilience gaps found on paper are far cheaper than those found in an incident.

## Question 090

### Question
How do you handle stateful workloads that resist horizontal scaling?

### Answer
Externalize state to a scalable store, partition the stateful work by key so each unit is independently ownable, or use consistent hashing to distribute and rebalance. Where state is intrinsic (e.g. real-time sessions), you scale by sharding the state and routing accordingly, accepting rebalancing complexity.

## Question 091

### Question
How do you reason about the blast radius of a change or failure?

### Answer
Map what depends on the component and what it can affect, then design boundaries (bulkheads, separate deployments, isolation) so a failure or bad change is contained. Minimizing blast radius is a core resilience goal, achieved by limiting coupling and isolating critical paths.

## Question 092

### Question
How do you decide the granularity of services?

### Answer
Size services to align with bounded contexts and team ownership, coarse enough to avoid chatty cross-service workflows and distributed transactions, fine enough for independent deployment and scaling. Both extremes hurt: nano-services explode coupling and overhead; mega-services lose autonomy. Granularity follows cohesion and ownership.

## Question 093

### Question
How do you handle cross-cutting concerns consistently across many services?

### Answer
Provide them through shared infrastructure and paved roads — a service mesh or middleware for retries, mTLS, tracing, and rate limiting — so each team gets them by default rather than reimplementing. Centralizing the mechanism while keeping policy configurable avoids both duplication and divergence.

## Question 094

### Question
What is a service mesh and what problem does it solve?

### Answer
A service mesh moves cross-cutting network concerns (mutual TLS, retries, timeouts, traffic shaping, telemetry) into a sidecar infrastructure layer, out of application code. It gives consistent, language-agnostic handling of service-to-service communication, at the cost of operational complexity that must be justified by scale.

## Question 095

### Question
How do you keep a long-lived system adaptable to changing business strategy?

### Answer
Align boundaries to durable business capabilities rather than transient features, keep contracts stable and implementations replaceable, and invest in the ability to change (tests, fitness functions, modularity) over speculative features. Adaptability comes from clean seams and reversibility, so the system bends with strategy instead of resisting it.

## Question 096

### Question
How do you measure whether an architecture is "good"?

### Answer
Judge it by outcomes: how cheaply and safely the team can deliver change, how well it meets its quality attributes under real load, and how contained failures and changes are. A good architecture is one whose costly decisions hold up and whose boundaries keep change local; aesthetics are secondary to these measurable effects.

## Question 097

### Question
How do you handle the tension between domain purity and pragmatic performance?

### Answer
Keep the domain model clean by default, and introduce targeted, well-documented compromises (read models, denormalization, bypassing the domain for bulk operations) only where profiling shows real need. Purity serves maintainability; you trade slices of it deliberately for measured performance, not preemptively.

## Question 098

### Question
How do you onboard a new team to an existing complex architecture?

### Answer
Provide high-level context and container diagrams, ADRs explaining key decisions, clear ownership and contract documentation, and a paved-road dev environment. Reducing the cognitive load to become productive is itself an architectural responsibility, and gaps in it signal where boundaries or docs are weak.

## Question 099

### Question
How do you know when to stop investing in architecture?

### Answer
Stop when further structural investment no longer reduces the cost of likely change or risk meaningfully — when added flexibility is speculative (YAGNI) and complexity would outweigh benefit. Architecture serves delivery and qualities; once those are adequately met, additional sophistication is waste.

## Question 100

### Question
What ultimately distinguishes senior architectural judgment?

### Answer
A senior architect optimizes the whole over the long term: aligning structure with business capabilities and teams, making the expensive-to-reverse decisions deliberately while keeping the rest flexible, designing for failure and change, and justifying trade-offs in terms of measurable outcomes and cost. The mark of seniority is knowing what not to build and which complexity is worth its price.
