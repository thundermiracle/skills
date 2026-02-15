# Rust Clean Architecture Reference (language-agnostic wording)

## Layer Structure

- `src/domain`: `aggregates`, `entities`, `value_objects`, `services`, `events`, `error.rs`
- `src/application`: `commands/models + handlers`, `queries/models + handlers`, `dto`, `repositories`, `services`, `dispatcher.rs`, `error.rs`
- `src/infrastructure`: `database` (`db`, `migrations`, `seed`, `repositories_impl`), `di/container.rs`
- `src/presentation`: per-entity `controllers`, `presenters`, `requests`, `responses`, `routes`, `mod.rs`

## Dependency Rules

- Dependencies always point inward: `domain <- application <- infrastructure/presentation`
- `domain` depends only on core/standard libraries
- `application` depends on `domain` and interfaces
- `infrastructure` implements `application` interfaces

## Business Logic Placement

- Prefer `Entity First`: business rules live in aggregates/entities
- Use Domain Services only for cross-aggregate/domain-wide rules
- Application Services/Handlers orchestrate only; they do not own business rules

## Repository Placement

- Place repository interfaces in `application`
- Provide concrete implementations in `infrastructure`
- Do not place DB-access interfaces in `domain`

## CQRS Endpoint Pattern

Command (state change):
- Command model
- Command handler
- Dispatcher
- DI container
- Presentation: `Request -> Controller -> Presenter -> Response`
- Route and OpenAPI registration

Query (read):
- Application DTOs
- Repository interface
- Query handler
- Dispatcher
- Presentation: `Controller -> Presenter -> Response`
- Route and OpenAPI registration

## Architecture Style Selection

- For large domains, multiple teams, and long-term maintainability, prefer full layered structure (`domain/application/infrastructure/presentation`).
- For smaller scope or fast-delivery products, vertical slices are acceptable if dependency direction and domain purity remain enforced.
- Re-evaluate this choice when complexity grows (feature count, integration points, team size).

## Read/Write Model Strategy

- Keep write paths domain-centric (aggregates/entities + repository persistence).
- Keep read paths DTO-centric and optimize independently when needed (read-specific query services, raw SQL, database views).
- Do not force repositories/specifications on read paths when a simpler query path is clearer and faster.

## Use-Case Boundary Policies

- Apply cross-cutting concerns at command/query boundaries, not inside domain entities.
- Typical policies: validation, logging, authorization, performance timing, unhandled exception mapping.
- Use pipeline/chain-of-responsibility style composition to keep handlers focused on business orchestration.

## Domain Events, Integration, and Caching

- Use domain events to decouple side effects from aggregate methods.
- When external integration delivery must be reliable, use an outbox strategy.
- For read-heavy endpoints, apply cache-aside selectively and keep invalidation explicit.

## Flow of Control Pattern

- Keep API adapters thin: controller accepts request, invokes use case, presenter maps output.
- Keep use cases delivery-independent and aligned with business vocabulary.

## Presentation Module Structure

- `src/presentation/{entity}/controllers`
- `src/presentation/{entity}/presenters`
- `src/presentation/{entity}/requests` (command or parameterized query)
- `src/presentation/{entity}/responses`
- `src/presentation/{entity}/routes.rs`
- `src/presentation/{entity}/mod.rs`
- Use explicit re-exports; avoid wildcard exports

## Naming Conventions (language-agnostic)

- Command model: `{action}_{entity}_command`
- Handler: `{action}_{entity}_handler`
- Controller: `{action}_{entity}_controller`
- Presenter: `{action}_{entity}_presenter`
- Request: `{action}_{entity}_request`
- Response: `{action}_{entity}_response`
- Query list: `get_{entity}_list_*` for simple lists, `find_{entities}_*` for parameterized queries

## Validation and Error Handling

- Validate at Request DTO, Handler, and Domain invariant levels
- Wrap lower-level errors in Application-level errors for cross-layer consistency

## Mandatory Project Rules

- Query paths return Application DTOs directly and do not traverse Domain.
- Command paths must create Domain entities and execute Domain business logic before repository persistence.
- Application reads through repositories and passes data into Domain logic.
- Extract duplicated logic in Application into Application Services.
- Require 100% unit-test coverage for Domain logic, except simple data-holder entities.

## Testing and Quality Checks

- Unit tests for request validation and presenter mapping
- Unit tests for handlers/use cases
- Integration tests for repository implementations
- Require build/lint/format/test checks before release

## Database Schema Signals

- Normalized schema with categories, products, SKUs, colors, tags, and junction tables
- Common aggregate centers: product/SKU/category/cart/order
- Keep DB details in infrastructure and preserve Domain model independence
