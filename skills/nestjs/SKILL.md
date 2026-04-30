---
name: nestjs
description: "NestJS (Nest.js) production patterns for modules, controllers, providers, guards, interceptors, pipes, middleware, JWT, ValidationPipe, microservices, GraphQL, Bull queues, Prisma, and TypeORM. Triggers: NestJS, Nest.js, Nest module, dependency injection, class-validator DTO, exception filter, testing module, GraphQL resolver, Bull queue, microservice client. Uses: Read, Grep, Glob, Bash, WebSearch. Outputs: tier-ordered review checklists and/or concrete code edits with cited rule filenames. Do NOT use for: non-Nest backends (Express/Fastify only with no Nest integration), frontend-only frameworks, generating AGENTS.md, or toolchain setup unrelated to Nest."
metadata:
  version: "1.0.1"
  tags: nestjs, backend, nest, dependency-injection, security, validation, api, microservices
---

# NestJS

Follow the **tier workflow** below, then deepen with files under [`rules/`](rules/) and the section map in [`rules/_sections.md`](rules/_sections.md).

## Non-negotiables

1. **`rules/<prefix>-*.md`** for anything beyond this file (transactions, serialization, queues, graceful shutdown).
2. **No invented Nest APIs, decorators, guards, or env keys** — verify in the repo or current Nest docs.
3. **Constructor injection** and explicit `Module` wiring; **DTOs + `ValidationPipe`** on inputs unless the project documents otherwise.
4. **Deliverables match the ask** (checklist vs patch) **and cite** `rules/<slug>.md`.

## Rules map (# = workflow priority; anchor = first file for tiers 1–4)

| # | Prefix | Focus | Anchor |
| - | ------ | ----- | ------ |
| 1 | `arch-` | Architecture | [`arch-avoid-circular-deps.md`](rules/arch-avoid-circular-deps.md) |
| 2 | `di-` | Dependency injection | [`di-prefer-constructor-injection.md`](rules/di-prefer-constructor-injection.md) |
| 3 | `error-` | Error handling | [`error-use-exception-filters.md`](rules/error-use-exception-filters.md) |
| 4 | `security-` | Security | [`security-validate-all-input.md`](rules/security-validate-all-input.md), [`security-use-guards.md`](rules/security-use-guards.md) |
| 5 | `perf-` | Performance | — |
| 6 | `test-` | Testing | — |
| 7 | `db-` | Database / ORM | — |
| 8 | `api-` | API design | — |
| 9 | `micro-` | Microservices | — |
| 10 | `devops-` | DevOps / deploy | — |

Full list (40+ slugs) and new-rule skeleton: [`rules/_sections.md`](rules/_sections.md), [`rules/_template.md`](rules/_template.md).

## Refactoring and review workflow

Apply rules in this order; stop reranking within a tier unless a higher tier introduces new breakage. After each tier, run **`nest build` and automated tests** when the toolchain is available before moving downward.

1. **CRITICAL — Architecture**: resolve circular dependencies, validate feature-module boundaries, verify `imports`/`exports`/`providers` correctness.
2. **CRITICAL — Dependency injection**: enforce constructor injection, correct provider scopes, remove service-locator patterns.
3. **HIGH — Error handling**: consolidate exception translation; replace raw opaque errors where HTTP semantics matter.
4. **HIGH — Security**: global validation, authentication/authorization guards, rate limiting when exposed publicly.
5. **HIGH — Performance**: caching hotspots, ORM N+1 risks, lazy loading where documented in rules.
6. **MEDIUM-HIGH and below**: testing harnesses, migrations/transactions, API shape, microservice boundaries, deployment concerns.

### Where to read next

[`rules/_sections.md`](rules/_sections.md) (ordering), `rules/<slug>.md` (detail), [`rules/_template.md`](rules/_template.md) (blank rule).

## Code snippets — common patterns

### DTO with `class-validator` and global `ValidationPipe`

```typescript
// create-order.dto.ts
import { IsString, IsInt, Min } from 'class-validator';

export class CreateOrderDto {
  @IsString()
  sku: string;

  @IsInt()
  @Min(1)
  qty: number;
}

// main.ts — register once globally
async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.useGlobalPipes(
    new ValidationPipe({ whitelist: true, forbidNonWhitelisted: true }),
  );
  await app.listen(3000);
}
```

### Applying a JWT guard to a route

```typescript
// orders.controller.ts
import { Controller, Post, Body, UseGuards } from '@nestjs/common';
import { JwtAuthGuard } from '../auth/jwt-auth.guard';
import { CreateOrderDto } from './dto/create-order.dto';
import { OrdersService } from './orders.service';

@Controller('orders')
export class OrdersController {
  constructor(private readonly ordersService: OrdersService) {}

  @Post()
  @UseGuards(JwtAuthGuard)
  create(@Body() dto: CreateOrderDto) {
    return this.ordersService.create(dto);
  }
}
```

## Integrated example

**Prompt:** `POST /orders` body `{ sku, qty }`, login required.

- **Modules** — avoid cycles (`arch-feature-modules`, `arch-module-sharing`).
- **Input** — `CreateOrderDto` + app `ValidationPipe` (`security-validate-all-input`).
- **Auth / errors** — JWT (`security-use-guards`, `security-auth-jwt`); HTTP errors + filters (`error-throw-http-exceptions`, `error-use-exception-filters`).
- **Output** — code diff vs checklist per ask; cite slugs.

## Anti-patterns (reject in reviews)

- Using `forwardRef` as the primary fix without first extracting shared boundaries or domain events (`arch-avoid-circular-deps`).
- `@Body()` typed as `any` or primitives without pipes where the codebase uses DTO validation elsewhere.
- Scattered `try/catch` returning ad-hoc JSON instead of centralized exception mapping.
- Property injection solely to avoid constructor parameters.
- Recommending Prisma/Bull/microservice patterns **without opening** the relevant `rules/db-*`, `rules/micro-*` files when specifics matter.
