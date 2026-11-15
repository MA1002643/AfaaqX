# afaaqX API Specification

The afaaqX backend's authoritative HTTP contract is located in this folder. The frontend client, automated tests, and the dissertation all use it as the source of truth for "what does the API look like."

## Files

- [`error-catalog.md`](error-catalog.md) — RFC 7807 problem envelope, HTTP status conventions, and every stable `code` value the API may return.
- [`openapi.yaml`](openapi.yaml) — OpenAPI 3.1 specification. Covers every route across `auth`, `sessions`, `users`, `staff`, `roles`, `categories`, `products`, `cart`, `orders`, `services`, `appointments`, `payments`, `webhooks`, `assistant`, `files`, and `meta`.

## Coverage Against Acceptance Criteria (Issue #35)

| Acceptance criterion                  | Where                                                                                                                                                |
| ------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| All API endpoints documented          | [`openapi.yaml`](openapi.yaml) `paths:` — 67 operations across 16 tags                                                                               |
| Authentication requirements specified | [`openapi.yaml`](openapi.yaml) `components.securitySchemes` (`bearerAuth`, `refreshCookie`, `webhookSignature`) + per-operation `security` overrides |
| OpenAPI spec file created             | [`openapi.yaml`](openapi.yaml)                                                                                                                       |
| Error codes and messages documented   | [`error-catalog.md`](error-catalog.md) — 61 codes grouped by domain, with HTTP status mapping and worked examples                                    |
| Request/response schemas defined      | [`openapi.yaml`](openapi.yaml) `components.schemas` — all entities, requests, and responses                                                          |

## Companion Designs

- Architecture: [`../dissertation/architecture/`](../dissertation/architecture/)
- Authentication: [`../dissertation/auth/`](../dissertation/auth/)
- Database: [`../dissertation/database/`](../dissertation/database/) and [`../../prisma/schema.prisma`](../../prisma/schema.prisma)

In order to facilitate the synchronisation of DTOs and persistence shapes, the OpenAPI schemas in `components.schemas` are purposefully aligned with the Prisma models.

## Conventions Summary

- **Wire format:** JSON; ISO 8601 UTC for datetimes; `{ amount, currency }` objects for money; UUID v4 for ids.
- **Authentication:** `Authorization: Bearer <accessJwt>` for normal API traffic; `afx_rt` `HttpOnly` cookie for `/auth/refresh`; provider signatures for `/webhooks/*`; `/assistant/messages` works signed-in or anonymous.
- **Localisation:** English only; i18n is out of scope for this version.
- **Rate limits:** Enforced at the gateway; the spec documents `429` everywhere it can fire and includes `Retry-After` headers.
- **Pagination:** Cursor-based — `?cursor=&limit=` in, `{ items, nextCursor }` out.
- **Idempotency:** Mutating endpoints honour an optional `Idempotency-Key: <uuid>` header; replays return the original response.
- **Errors:** Always `application/problem+json` per RFC 7807. Stable, machine-readable `code` field. `traceId` correlates with Sentry/log entries.

## Viewing the Spec

Pick whichever you prefer:

```bash
# Lint / validate
npx @redocly/cli lint docs/api/openapi.yaml
npx @stoplight/spectral-cli lint docs/api/openapi.yaml

# Swagger UI (Docker)
docker run --rm -p 8080:8080 -v "$PWD/docs/api:/spec" -e SWAGGER_JSON=/spec/openapi.yaml swaggerapi/swagger-ui

# Redoc (single static HTML)
npx @redocly/cli build-docs docs/api/openapi.yaml -o docs/api/redoc.html
```

Install the 42Crunch "OpenAPI (Swagger) Editor" plugin for Visual Studio Code to see a side-by-side Swagger UI preview with inline validation.

## Code Generation

Plan when the build phase begins:

- **Client types (frontend):** `npx openapi-typescript docs/api/openapi.yaml -o apps/web/src/api/schema.ts`
- **Mock server (testing):** `npx @stoplight/prism-cli mock docs/api/openapi.yaml`
- **Server types / validators (backend):** `npx openapi-zod-client docs/api/openapi.yaml -o apps/api/src/generated/zod-client.ts` or hand-write Zod schemas mirrored to the spec.

The spec is the source of truth; The code is written and `openapi.yaml` is updated **in the same pull request** as the route handler each time an endpoint changes.

## Change Management

- Changes that break the schema need either (a) a new versioned route (`/v2/...`) or (b) a sunset date in the `Sunset` response headers and a description period recorded in the operation `description`.
- Before merging, all changes need to be linted in CI using the commands "redocly lint" and "spectral lint."
- It is non-breaking to add a field or endpoint, and it can ship at any moment.
