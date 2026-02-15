# TypeScript + NestJS Clean Architecture Reference

## Layer Structure

- `src/domain`: aggregates, entities, value objects, domain services, domain errors
- `src/application`: commands, queries, DTOs, repository interfaces, application errors
- `src/infrastructure`: DB entities and repository implementations
- `src/presentation`: controllers, requests, responses, presenters, filters
- `src/app.module.ts`: module wiring
- `src/main.ts`: bootstrap

## Dependency Rules

- Domain has no framework dependency
- Application depends on Domain and repository interfaces
- Infrastructure implements repository interfaces and hides ORM details
- Presentation depends on application DTOs and CQRS bus, never directly on infrastructure

## CQRS and Module Wiring

- `ApplicationModule` registers command/query handlers with `@nestjs/cqrs`
- `PresentationModule` registers controllers and global exception filter
- `AppModule` wires `ConfigModule`, `TypeOrmModule`, `CqrsModule`, `DatabaseModule`, `ApplicationModule`, `PresentationModule`

## Commands (state change)

- Models: `src/application/commands/models/*.command.ts`
- Handlers: `src/application/commands/handlers/*.handler.ts`
- Controllers use `CommandBus.execute(command)`
- Request DTOs use `class-validator` + `class-transformer` and implement `toCommand()`
- Presenters map application DTOs to response DTOs

## Queries (read)

- Models: `src/application/queries/models/*.query.ts`
- Handlers: `src/application/queries/handlers/*.handler.ts`
- Controllers use `QueryBus.execute(query)`
- If parameters are needed, request DTOs implement `toQuery()`
- Presenters map application DTOs to response DTOs

## Architecture Style Selection

- For large systems and strict boundaries, keep full layered separation across Domain/Application/Infrastructure/Presentation.
- For smaller products, vertical slices are acceptable if dependency direction and Domain purity are still enforced.
- Revisit this decision when team size, domain complexity, or integration count increases.

## Read/Write Model Strategy

- Keep command/write paths Domain-centric (entities/aggregates + repository persistence).
- Keep query/read paths DTO-centric and optimize independently.
- For read-heavy endpoints, allow dedicated query services (including raw SQL/views) when they improve clarity or performance.

## Repository Interfaces and DI

- Interfaces live in `src/application/repositories/*.interface.ts`
- Implementations live in `src/infrastructure/database/repositories/*.repository.ts`
- DI tokens are string-based (example: `'IProductRepository'`)
- `DatabaseModule` binds tokens to implementations and exports providers
- Handlers inject repositories via `@Inject('ITokenName')`
- Do not define DB-access interfaces in Domain

## Presentation Structure

- Split by entity under `src/presentation/{entity}`
- Keep `controllers`, `presenters`, `requests`, `responses` separated
- Requests convert external input to command/query objects
- Presenters convert application DTOs to API responses
- Add Swagger decorators on controllers (`@ApiTags`, `@ApiOperation`, `@ApiResponse`)

## Validation and Errors

- Validate input via `class-validator` on request DTOs
- Perform business-rule validation and parsing in command/query handlers
- Use Domain errors for invariant violations
- Map application/domain errors to HTTP responses via global `HttpExceptionFilter`

## Use-Case Boundary Policies

- Apply cross-cutting concerns at request/use-case boundaries, not inside Domain entities.
- Typical concerns: validation, logging, authorization, performance timing, exception mapping.
- In NestJS, combine `ValidationPipe`/custom pipes, interceptors, and filters so handlers remain focused on orchestration.

## Controller Request Validation Policy (mandatory)

- Do not write and call manual controller-side `validate()` functions.
- Prefer built-in NestJS pipes for request validation (`ValidationPipe`, `ParseIntPipe`, `ParseUUIDPipe`, etc.).
- Create custom pipes only when built-in pipes are insufficient.
- Keep controller responsibility limited to receive, delegate, and map response.

Good example (built-in + custom pipe):

```ts
// main.ts
app.useGlobalPipes(
  new ValidationPipe({
    transform: true,
    whitelist: true,
    forbidNonWhitelisted: true,
  }),
)

// presentation/variants/pipes/parse-sku-ids.pipe.ts
import { BadRequestException, Injectable, PipeTransform } from '@nestjs/common'

@Injectable()
export class ParseSkuIdsPipe implements PipeTransform<string[], string[]> {
  transform(value: string[]): string[] {
    if (!Array.isArray(value) || value.length === 0) {
      throw new BadRequestException('skuIds must be a non-empty array')
    }
    return value.map((v) => v.trim())
  }
}

// presentation/variants/controllers/find-variants.controller.ts
@Post()
async execute(
  @Body('skuIds', ParseSkuIdsPipe) skuIds: string[],
): Promise<FindVariantsResponse> {
  const result = await this.queryBus.execute(new FindVariantsQuery(skuIds))
  return VariantsPresenter.toFindVariantsResponse(result)
}
```

Bad example (manual validation in controller):

```ts
@Post()
async execute(@Body() request: FindVariantsRequest): Promise<FindVariantsResponse> {
  // NG: manual validation and normalization in controller
  if (!request.skuIds || !Array.isArray(request.skuIds) || request.skuIds.length === 0) {
    throw new BadRequestException('skuIds is required')
  }

  const skuIds = request.skuIds.map((v) => {
    const trimmed = v?.trim()
    if (!trimmed) {
      throw new BadRequestException('invalid skuId')
    }
    return trimmed
  })

  const result = await this.queryBus.execute(new FindVariantsQuery(skuIds))
  return VariantsPresenter.toFindVariantsResponse(result)
}
```

## Domain Events, Integration, and Caching

- Use domain events to decouple side effects from command handlers/entities.
- When integration delivery reliability matters, adopt an outbox strategy.
- For read-heavy scenarios, apply cache-aside with explicit invalidation rules.

## Flow of Control Pattern

- Keep flow explicit: controller receives request -> use case/handler executes -> presenter maps response.
- Keep use cases delivery-independent and aligned with business vocabulary.

## Mandatory Project Rules

- Query paths return Application DTOs directly and do not traverse Domain.
- Command paths must create Domain entities and execute Domain business logic before repository persistence.
- Application reads through repositories and passes data into Domain logic.
- Extract duplicated logic in Application into Application Services.
- Require 100% unit-test coverage for Domain logic, except simple data-holder entities.

## Naming Conventions (observed)

- Commands: `{action}-{entity}.command.ts`
- Command handlers: `{action}-{entity}.handler.ts`
- Queries: `get-{entity}.query.ts`, `get-{entity}-list.query.ts`, `find-{entity}s.query.ts`
- Query handlers: `get-{entity}-list.handler.ts`, `find-{entity}s.handler.ts`
- Controllers: `{action}-{entity}.controller.ts`
- Requests: `{action}-{entity}.request.ts`
- Responses: `{action}-{entity}.response.ts`
- Presenters: `{entity}.presenter.ts`
- DTOs: `*.dto.ts`
- Repository interfaces: `{entity}.repository.interface.ts`
- ORM entities: `{entity}.entity.ts`

## Testing and Checks

- Unit tests for request validation and presenter mapping
- Unit tests for handlers (command/query)
- Integration tests for repository implementations
- NestJS standard scripts: `test`, `test:e2e`, `test:cov`
