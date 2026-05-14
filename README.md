# Project Structure Blueprint

## src/

### api/

HTTP layer and route registration.

* **routes/**: Express/Fastify route definitions grouped by domain.
* **middlewares/**: Route-level middleware such as auth, validation, rate limiting, and request preprocessing.
* **controllers/**: Request handlers responsible only for orchestrating request → service → response flow.
* **index.ts**: Main API/router registration entry point.

### lib/

Third-party library configurations.

**database/** folder. Database connection and ORM/ODM configuration.

* **client.ts**: Database client initialization and connection handling.
* **models/**: ORM/ODM schemas and model definitions.
* **migrations/**: Database migration files if applicable.
* **seeders/**: Seed scripts for local or test environments.
* **types.ts**: Database-specific internal typings.

**logger/** folder. Logging infrastructure.

* **index.ts**: Logger configuration.
* **transports/**: Logger transport definitions.
* **types.ts**: Shared logger typings.

**cache/** folder. Cache configuration and integrations.

* **redis.ts**: Redis client setup and cache utilities.

### modules/

Business logic organized into horizontal modules. Each subdirectory represents a self-contained domain.

* **[module-name]/** (e.g., auth, users, billing)

* **controllers/**: Thin request handlers for this module.

* **services/**: Core business logic and orchestration.

* **db/**: Pure database interaction layer abstracted from business logic.

* **repositories/**: Optional abstraction layer for complex persistence logic.

* **validators/**: Zod/Joi/class-validator schemas.

* **dto/**: Request and response DTO definitions.

* **types/**: Module-local internal types.

* **constants/**: Domain-specific configuration.

* **utils/**: Local helper utilities.

* **middlewares/**: Module-specific middleware.

* **events/**: Domain events and event handlers if needed.

* **jobs/**: Background jobs, queues, and scheduled tasks.

* **index.ts**: Public module exports only.

### config/

Application-wide static configuration.

* **app.ts**: General application configuration.
* **env.ts**: Environment variable parsing and validation.
* **feature-flags.ts**: Runtime feature toggles.
* **constants.ts**: Global backend constants.

### shared/

Reusable cross-module logic.

* **utils/**: Shared helper functions.
* **types/**: Shared TypeScript types.
* **errors/**: Shared error classes and utilities.
* **constants/**: Shared application constants.
* **validators/**: Shared validation schemas.

### infrastructure/

Infrastructure and platform integrations.

* **email/**: Email providers and templates.
* **storage/**: File storage integrations (S3, local, etc.).
* **queue/**: Queue providers and workers.
* **payments/**: External payment integrations.
* **websocket/**: WebSocket server setup and event handling.

### auth/

Authentication and authorization infrastructure.

* **jwt/**: JWT generation and validation.
* **strategies/**: Passport/auth strategies if applicable.
* **permissions/**: RBAC and permission logic.
* **guards/**: Access control middleware.

### errors/

Centralized application error handling.

* **api-error.ts**: Base application error abstraction.
* **error-codes.ts**: Shared backend error codes.
* **handlers/**: Global error handling middleware.
* **mappers/**: Internal → API error mapping utilities.

### test/

Dedicated space for ensuring your application behaves as expected.

* **unit/**: Unit tests.
* **integration/**: Integration tests.
* **e2e/**: End-to-end tests.
* **fixtures/**: Mock data and factories.
* **setup/**: Global test setup.

### scripts/

Standalone scripts for development and maintenance.

* **seed.ts**: Database seeding.
* **cleanup.ts**: Maintenance scripts.
* **migrate.ts**: Migration runners.

### docs/

Technical documentation and architecture decisions.

* **adr/**: Architecture Decision Records.
* **openapi/**: Swagger/OpenAPI specifications.

### Entry Files

* **server.ts**: HTTP server bootstrap.
* **app.ts**: Application setup and middleware registration.
* **worker.ts**: Queue/background worker bootstrap if applicable.

## Optional Folders

Additional folders that may be introduced depending on project complexity, architecture, or business requirements.

### websocket/

Dedicated real-time communication layer.

### graphql/

GraphQL schemas, resolvers, and server setup.

### cron/

Scheduled jobs and recurring tasks.

### monitoring/

Observability and monitoring integrations.

* **metrics/**: Prometheus/OpenTelemetry metrics.
* **tracing/**: Distributed tracing setup.
* **health/**: Healthcheck endpoints.

### templates/

Server-rendered templates or email templates.

### uploads/

Temporary local uploaded files storage.

## Conventions

* Prefer feature-first architecture.
* Keep controllers thin.
* Services contain business logic only.
* Database implementation details must remain abstracted behind db/repository layers.
* Avoid direct ORM usage inside controllers.
* Shared logic must live in shared layers only.
* Prefer composition over inheritance.
* Prefer explicit exports through module `index.ts`.
* Validate all external input.
* Environment variables must be validated on startup.
* Keep infrastructure concerns isolated from business logic.

## Import Rules

Dependency direction should remain predictable.

Allowed flow:

```txt
api → modules → shared
api → infrastructure → shared
modules → shared
```

Disallowed:

* modules importing from unrelated modules
* shared importing from modules
* controllers directly accessing database models
* infrastructure leaking into business logic directly
