# Software Design Patterns Catalog

## Purpose

This catalog defines the software design patterns to detect during Architecture Discovery (Phase 13, Step 3).

For each pattern the review must produce evidence from the actual codebase. Do not label ordinary code as a design pattern without structural justification.

Patterns are grouped into four standard categories: Creational, Structural, Behavioural, and Architectural.

---

## Detection Rules

A pattern is **confirmed** when:

- The structural intent of the pattern (not just its superficial shape) is demonstrably present.
- The pattern solves an identifiable problem in this codebase.
- The implementation appears in more than one location, or occupies a clearly central role.

A pattern is **candidate** when:

- The structure is present but only in a single, isolated location.
- The intent is plausible but not conclusively provable from the available evidence.

A pattern is **absent** when:

- Superficially similar code exists but the underlying intent and structural benefit are not present.

Always record whether a pattern is Confirmed, Candidate, or Absent.

---

## 1. Creational Patterns

### 1.1 Factory / Factory Method

**Problem**: Creating objects without specifying their concrete classes at the call site, enabling substitution and testability.

**Detection signals**:
- A function or class method named `create*`, `build*`, `make*`, or `*Factory` whose job is constructing and returning another object.
- The factory hides constructor arguments, applies defaults, or selects between multiple concrete types.
- Callers depend on the returned interface/type rather than a specific class.

**Common locations in SaaS codebases**: API client instantiation, database connection factories, notification service factories (email vs SMS vs push), payment provider factories.

**Evidence required**: At least one factory function/class returning objects of varying concrete types based on input configuration.

---

### 1.2 Abstract Factory

**Problem**: Creating families of related objects without specifying concrete classes, so that entire families can be swapped.

**Detection signals**:
- An interface or abstract class defining multiple `create*` methods.
- Two or more concrete implementations of that interface, each providing a different family of objects.

**Common locations**: Storage adapters (local vs S3 vs GCS), email providers (SendGrid vs Resend vs SMTP), feature flag providers.

**Evidence required**: An abstraction with at least two distinct concrete implementations that can be substituted without changing call sites.

---

### 1.3 Singleton

**Problem**: Ensuring a single shared instance of a resource (database connection, config, logger) across the application.

**Detection signals**:
- A module-level cached instance returned from a `getInstance()` function or exported as a module constant.
- A class with a private/protected constructor and a static `instance` property.
- A Node.js module-level variable that holds a lazily initialized connection (e.g. a Prisma client, Redis client, or config object) and is exported for reuse.

**Common locations**: Database clients (`prisma`, `db`), Redis clients, logger instances, config singletons, Supabase admin client.

**Evidence required**: The instance is constructed once and the same reference is shared across multiple importers.

**Risk to note**: Singletons can impede unit testing if not injectable. Flag when the singleton is not easily mockable.

---

### 1.4 Builder

**Problem**: Constructing complex objects step-by-step, separating construction from representation.

**Detection signals**:
- A class or fluent interface with chainable `.set*()` / `.with*()` / `.add*()` methods terminating in a `.build()` or `.create()` call.
- Used where the target object has many optional fields or multiple construction paths.

**Common locations**: Query builders, email template builders, API request builders, test fixture builders.

**Evidence required**: A fluent API with at least three distinct configuration steps before a terminal construction call.

---

## 2. Structural Patterns

### 2.1 Adapter

**Problem**: Making an incompatible interface work with a consumer that expects a different interface.

**Detection signals**:
- A wrapper class or function that translates one API's shape into another.
- Typically found when integrating a third-party library and isolating its API from the application's internal contracts.

**Common locations**: Payment provider adapters (Stripe → internal `PaymentService` interface), storage adapters, email provider adapters, analytics adapters.

**Evidence required**: A wrapper that translates input/output shapes between two incompatible interfaces and is not simply a pass-through.

---

### 2.2 Facade

**Problem**: Providing a simplified, unified interface to a complex subsystem or set of services.

**Detection signals**:
- A service class or module that orchestrates several internal services/repositories behind a single method.
- Callers interact only with the facade and are shielded from the internal complexity.

**Common locations**: `UserService` orchestrating auth + profile + billing, `OrderService` orchestrating inventory + payment + notifications, `NotificationService` abstracting email/SMS/push.

**Evidence required**: A public interface that simplifies access to two or more internal subsystems the caller would otherwise need to coordinate directly.

---

### 2.3 Proxy

**Problem**: Controlling access to an object, adding behaviour (logging, caching, auth checks) transparently.

**Detection signals**:
- A wrapper object/function that intercepts calls to the real implementation and adds cross-cutting behaviour before or after forwarding the call.
- Middleware chains that intercept requests before they reach a handler.

**Common locations**: Auth middleware (proxy for route handlers), caching layers wrapping database calls, logging decorators, rate-limit wrappers.

**Evidence required**: An interceptor that forwards to a real implementation after performing its own logic, with the caller unaware of the interception.

---

### 2.4 Decorator

**Problem**: Adding behaviour to an object dynamically without subclassing.

**Detection signals**:
- A wrapper that accepts an instance of an interface and adds behaviour while delegating core work to the wrapped instance.
- In functional code: higher-order functions that wrap another function.
- In TypeScript/NestJS: `@Decorator()` annotations that add cross-cutting concerns (guards, interceptors, pipes).

**Common locations**: Auth guards, logging interceptors, caching wrappers, validation pipes, HOCs in React.

**Note**: Framework decorators (e.g. NestJS `@Controller()`) are framework conventions, not instances of the GoF Decorator pattern. Distinguish clearly.

---

### 2.5 Composite

**Problem**: Treating individual objects and groups of objects uniformly through a common interface.

**Detection signals**:
- A tree-like component structure where leaves and composites share the same interface.

**Common locations**: UI component trees, permission/policy trees, nested route definitions.

---

## 3. Behavioural Patterns

### 3.1 Strategy

**Problem**: Defining a family of algorithms and making them interchangeable at runtime.

**Detection signals**:
- A function or class that accepts a handler/algorithm as a parameter or configuration.
- The selection of a concrete algorithm is deferred to the caller or configuration.

**Common locations**: Sorting and filtering strategies, payment processor selection, authentication strategy (Passport.js strategies), export format handlers (CSV vs JSON vs PDF).

**Evidence required**: At least two interchangeable concrete implementations of the same interface/function signature, selected at runtime or by configuration.

---

### 3.2 Observer / Publisher–Subscriber

**Problem**: Notifying multiple consumers of state changes without tight coupling.

**Detection signals**:
- Event emitters, message brokers, or pub/sub channels where producers do not call consumers directly.
- `EventEmitter` usage, domain events, webhook dispatch, message queue publishing.
- React Context or state management subscriptions are framework-level and should be noted separately.

**Common locations**: Domain event dispatch after a business operation, webhook fanout, notification triggers, audit log writes triggered by events.

**Evidence required**: A producer that emits/publishes to a channel, with one or more consumers subscribed independently.

---

### 3.3 Command

**Problem**: Encapsulating a request as an object, enabling queuing, logging, undo, and retry.

**Detection signals**:
- Command objects with an `execute()` method representing a discrete operation.
- Job queue payloads (BullMQ, Inngest, Temporal) representing discrete units of work.
- CQRS command handlers.

**Common locations**: Background job processors, CQRS command buses, undo/redo implementations.

---

### 3.4 Template Method

**Problem**: Defining the skeleton of an algorithm in a base class and deferring specific steps to subclasses or callbacks.

**Detection signals**:
- A base class or function that calls abstract/overridable steps in a fixed sequence.
- Framework lifecycle hooks (e.g. `onModuleInit`, `beforeEach`, `afterEach`) that define a fixed extension point.

**Common locations**: Base CRUD controllers, base repository classes, data import pipelines with customizable parsing steps.

---

### 3.5 State

**Problem**: Allowing an object to change its behaviour when its internal state changes.

**Detection signals**:
- An entity with a `status` or `state` field that determines valid transitions and available operations.
- State-specific methods or conditional branching organized around an enumerated status.

**Common locations**: Order status machines (PENDING → PROCESSING → SHIPPED → DELIVERED), subscription states, workflow approval statuses.

**Risk to note**: Inline `if/switch` on status fields scattered across multiple files is a symptom of an implicit, unmanaged state machine — flag this as technical debt.

---

### 3.6 Chain of Responsibility

**Problem**: Passing a request through a chain of handlers, each deciding to handle or pass on.

**Detection signals**:
- Middleware pipelines where each handler calls `next()`.
- Validation pipelines with ordered rules.

**Common locations**: Express/Fastify/Hono middleware, NestJS interceptor/guard/pipe chains, validation middleware stacks.

---

## 4. Architectural / Application-Level Patterns

### 4.1 Repository Pattern

**Problem**: Abstracting data access behind an interface so business logic does not depend on the persistence technology.

**Detection signals**:
- A class or module named `*Repository` or `*Store` that encapsulates all database queries for an entity.
- Business logic calling `repository.find*()` / `repository.save()` rather than directly calling the ORM/query builder.

**Evidence required**: At least one repository class with multiple query methods consumed by a service layer that does not directly import the ORM.

---

### 4.2 Service Layer

**Problem**: Encapsulating business logic in a dedicated layer between the request-handling layer and the data access layer.

**Detection signals**:
- Files or classes named `*Service` containing business logic and orchestrating repository calls.
- Controllers or route handlers that delegate to services rather than containing business logic directly.

**Note**: The presence of files named `service` does not guarantee the pattern is correctly applied. Verify that business logic is not leaking into controllers or repositories.

---

### 4.3 Dependency Injection (DI)

**Problem**: Decoupling components by providing their dependencies externally rather than having them construct their own.

**Detection signals**:
- Constructor injection: dependencies passed as constructor parameters.
- Framework DI: NestJS `@Injectable()` / `@Inject()`, inversify containers.
- Manual DI: dependencies passed into factory functions or React Context used to provide shared services.

**Evidence required**: At least one component whose dependencies are provided by a caller or framework rather than constructed internally.

---

### 4.4 CQRS (Command Query Responsibility Segregation)

**Problem**: Separating read and write operations to optimize each independently.

**Detection signals**:
- Distinct command handlers and query handlers.
- Separate read models / projections from write models.
- Explicit `Command` and `Query` objects routing to dedicated handlers.

**Note**: Do not confuse simple separation of `GET` and `POST` routes with CQRS. Require explicit Command/Query typing or dedicated handler classes.

---

### 4.5 Unit of Work

**Problem**: Coordinating multiple repository operations within a single transaction boundary.

**Detection signals**:
- A transaction wrapper that groups multiple repository calls and commits or rolls back atomically.
- Prisma `$transaction()`, Drizzle `db.transaction()`, TypeORM `QueryRunner`, explicit database transaction objects passed through service methods.

---

### 4.6 Middleware Pipeline

**Problem**: Processing a request through an ordered chain of composable steps before it reaches the handler.

**Detection signals**:
- Ordered middleware array or pipeline constructor.
- Each step has a consistent signature (e.g. `(req, res, next)` or `(ctx, next)`).

**Note**: This is a structural pattern at the application level, not a GoF pattern. Document it when the middleware chain is significant enough to affect correctness (auth order, error handler placement, rate limiter scope).

---

## Output Format

For each detected pattern, document:

```
### [Pattern Name] — [Confirmed / Candidate / Absent]

**Category**: [Creational / Structural / Behavioural / Architectural]
**Problem solved**: [One sentence]
**Implementation**:
  - File(s): [paths]
  - Description: [how this specific codebase implements it]
**Code example**: [concise snippet from the codebase]
**Consistency**: [Consistent / Isolated / Fragmented]
**Risks / Improvements**: [if any]
```
