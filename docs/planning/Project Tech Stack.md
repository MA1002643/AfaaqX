# afaaqX Project Tech Stack

Authoritative list of every technology, library, and external service the project commits to. Aligned with the design packs under [`../dissertation/architecture/`](../dissertation/architecture/), [`../dissertation/database/`](../dissertation/database/), [`../dissertation/auth/`](../dissertation/auth/), and the API contract in [`../api/openapi.yaml`](../api/openapi.yaml).

---

## Backend

| Concern                  | Choice                                                                                                                   | Notes                                                                    |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------ |
| Framework                | **Fastify**                                                                                                              | Native schema validation, first-class TypeScript, faster than Express    |
| Runtime                  | **Node.js 22 LTS**                                                                                                       | version 20 is the lest acceptable fallback                               |
| Language                 | **TypeScript (strict mode)**                                                                                             | `tsconfig` with `exactOptionalPropertyTypes`, `noUncheckedIndexedAccess` |
| API documentation        | **OpenAPI 3.1** (`docs/api/openapi.yaml`)                                                                                | Linted with Redocly + Spectral; previewed with Swagger UI / Redoc        |
| Realtime hub             | **Socket.io server**                                                                                                     | Rooms per user / role for push events                                    |
| Type / validator codegen | **`openapi-typescript`** (client types) + **Zod** schemas (server validation, hand-mirrored or via `openapi-zod-client`) | Generated in CI, never edited by hand                                    |
| Logging                  | **Pino** (structured JSON) + **Sentry** (errors + APM) + hosted log drain                                                | Pino chosen over Winston for Fastify integration and performance         |
| Background jobs          | **BullMQ** (Redis-backed queue)                                                                                          | Workers for notifications, webhooks, calendar sync, top-up reminders     |

---

## Frontend

| Concern              | Choice                                                       | Notes                                                     |
| -------------------- | ------------------------------------------------------------ | --------------------------------------------------------- |
| Framework            | **Next.js (App Router) + TypeScript**                        | Hosted on Vercel                                          |
| UI components        | **shadcn/ui**                                                | Built on Radix primitives; copy-in components             |
| Styling              | **Tailwind CSS**                                             |                                                           |
| Server state         | **TanStack Query (React Query) v5**                          | Cache invalidation around API mutations                   |
| Internationalisation | **next-intl**                                                | App Router-native; English (`en`) + Urdu (`ur`).          |
| Forms                | **React Hook Form + Zod**                                    | Same Zod schemas mirror the OpenAPI request bodies        |
| Theming              | **next-themes**                                              | Light / dark / system                                     |
| Calendar UI          | **FullCalendar (React adapter)**                             | Used for staff appointment dashboard and customer booking |
| Realtime             | **socket.io-client**                                         | Connects to backend Socket.io hub                         |
| Maps                 | **Google Maps JavaScript API** (`@vis.gl/react-google-maps`) | Place search + directions on contact / pickup pages       |
| Accessibility        | **WCAG 2.1 AA**                                              | Verified with `eslint-plugin-jsx-a11y` + Axe in tests     |
| Image / asset CDN    | **Cloudinary**                                               | Direct signed uploads from browser                        |

---

### Authentication & Security

| Concern                         | Choice                                                                                                       | Notes                                                               |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------- |
| Customer auth                   | Email + password (bcrypt cost 12)                                                                            |                                                                     |
| Identity provider (staff/admin) | **Microsoft Entra ID** via `msal-node` (OIDC + Authorization Code + PKCE)                                    | Group-claim → role mapping                                          |
| JWT library                     | **`jose`**                                                                                                   | Ed25519 (EdDSA) signing; JWKS published at `/.well-known/jwks.json` |
| 2FA — TOTP                      | **`speakeasy`**                                                                                              | Microsoft Authenticator compatible                                  |
| Refresh tokens                  | Server-side rotating, SHA-256 hashed, `HttpOnly`+`Secure`+`SameSite=Strict` cookie scoped to `/auth/refresh` | Family tracking + reuse detection                                   |
| 2FA — SMS                       | **Twilio**                                                                                                   |                                                                     |
| Security headers                | **`@fastify/helmet`**                                                                                        | CSP, HSTS, X-Frame-Options                                          |
| 2FA — Email                     | **SendGrid**                                                                                                 |                                                                     |
| CORS                            | **`@fastify/cors`**                                                                                          | Strict allow-list                                                   |
| Idempotency                     | Custom middleware backed by Redis                                                                            | `Idempotency-Key` header on mutating endpoints                      |
| Rate limiting                   | **`@fastify/rate-limit`** (Redis store)                                                                      | Tighter limits on `/auth/*`                                         |
| Webhook signing                 | Provider HMAC verification (Stripe, PayPal, Twilio, SendGrid)                                                | Replay-guarded in `payment_event` table                             |
| Secret encryption               | AES-256-GCM via Node `crypto`; key from `TWO_FA_ENCRYPTION_KEY`                                              | Used for `two_factor_device.secret_encrypted`                       |
| Password hashing                | **bcrypt** (cost 12)                                                                                         |                                                                     |

---

## Database & Cache

| Concern                                             | Choice                                                                                                                                              | Notes                                                                    |
| --------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| Primary DB                                          | **PostgreSQL 16**                                                                                                                                   | Managed                                                                  |
| ORM                                                 | **Prisma**                                                                                                                                          | Migrates via `prisma migrate`; mainly used for the client on the backend |
| sessions / rate limit / Cache / MFA challenge store | **Redis 7**                                                                                                                                         | Hosted via Upstash (HTTP) or Railway Redis                               |
| Postgres extensions                                 | **`pgcrypto`** (UUIDs), **`citext`** (case-insensitive email/slug), **`pg_trgm`** (catalogue search), **`btree_gist`** (appointment slot exclusion) | Written in `prisma/schema.prisma`                                        |
| Read replica                                        | Optional async replica for reporting / failover                                                                                                     | Provider-managed                                                         |
| DB hosting (TBD)                                    | Supabase or Railway                                                                                                                                 | Decide before submission                                                 |
| Backups                                             | Point-In-Time Recovery (PITR), Daily snapshots + 30-day retention                                                                                   | Provider-managed                                                         |

---

## External Services

| Capability            | Provider                                             | Notes                                             |
| --------------------- | ---------------------------------------------------- | ------------------------------------------------- |
| Card payments         | **Stripe**                                           | Payment Intents + webhooks                        |
| SSO / SAML / 2FA push | **Microsoft Entra ID** + **Microsoft Authenticator** | Staff/admin                                       |
| Transactional SMS     | **Twilio**                                           | Notifications + 2FA SMS                           |
| Alternative payments  | **PayPal**                                           | Checkout API + IPN webhooks                       |
| Media                 | **Cloudinary**                                       | Signed direct uploads, CDN, image transformations |

| Transactional Email | **SendGrid** | Notifications + 2FA email |
| Maps & geocoding | **Google Maps Platform** | Place autocomplete + directions |

| Calendar sync | **Google Calendar API** | Appointment events + free/busy |
| Error / APM | **Sentry** | Frontend + backend |
| AI chat fallback (TBD) | **OpenAI** _or_ **Anthropic** | Out-of-hours auto-reply in live chat |

---

## Hosting & Infrastructure

| Layer                | Choice                                            | Notes                      |
| -------------------- | ------------------------------------------------- | -------------------------- |
| CDN / edge / TLS     | **Vercel Edge Network**                           |                            |
| Frontend hosting     | **Vercel**                                        | Per-PR preview deployments |
| Backend hosting      | Railway **or** Render                             | Still to be decided        |
| WAF / bot mitigation | Vercel built-in rules + provider-managed          |                            |
| Secrets              | GitHub Secrets (CI) + platform env vars (runtime) | No secrets in repo         |

---

## DevOps & CI/CD

| Concern                 | Choice                                                       | Notes                                                                                            |
| ----------------------- | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------ |
| SAST                    | **CodeQL**                                                   |                                                                                                  |
| VCS                     | **GitHub**                                                   | `main` protected; feature branches per issue                                                     |
| CI/CD                   | **GitHub Actions**                                           | Quality gates: lint → typecheck → unit → SAST → deps audit → build → SBOM/sign                   |
| Secret scanning         | **Gitleaks** + GitHub native scanning                        |                                                                                                  |
| SBOM + artefact signing | **Syft** + **Cosign**                                        |                                                                                                  |
| Dependency audit        | **Dependabot** + `npm audit`                                 |                                                                                                  |
| Promotion path          | Preview (per-PR) → Staging → Production with manual gate     | Canary rollout + automatic rollback on SLO breach                                                |
| IaC (database / Redis)  | Provider-managed via dashboard or Terraform when scope grows |                                                                                                  |
| Migrations              | **Prisma Migrate** (`migrate deploy` in CI)                  | Expand → migrate data → contract; never drop columns in the same release that stops writing them |

---

## Testing & Quality

| Layer                 | Tool                                                              | Notes                                                                |
| --------------------- | ----------------------------------------------------------------- | -------------------------------------------------------------------- |
| Unit                  | **Vitest**                                                        | Fast, ESM-native, TS-friendly                                        |
| API integration       | **Supertest** + ephemeral Postgres (`@testcontainers/postgresql`) | Hits real DB, not mocks                                              |
| E2E                   | **Playwright**                                                    | Auth flows, booking, checkout                                        |
| Performance smoke     | **k6**                                                            | During deploy gate Run against staging                               |
| API contract          | **Spectral** + **Redocly** lint in CI                             | Enforces consistency on `openapi.yaml`                               |
| Mock API for frontend | **Stoplight Prism**                                               | Spins up a mock from `openapi.yaml`                                  |
| Accessibility         | **axe-core** + `@axe-core/playwright`                             | Run inside E2E                                                       |
| Lint / format         | **ESLint** + **Prettier**                                         | `eslint-plugin-jsx-a11y`, `@typescript-eslint`, `simple-import-sort` |
| Commit standard       | **Conventional Commits** + **Husky** + **lint-staged**            | Enables automatic changelog later                                    |
| Coverage target       | ≥ 80% on domain services; smaller on glue code                    | Reported by Vitest + uploaded to CI                                  |

---

## Observability

| Concern                   | Tool                                             |
| ------------------------- | ------------------------------------------------ |
| Errors + APM              | **Sentry**                                       |
| Web analytics             | **Vercel Analytics** + **Vercel Speed Insights** |
| Structured logs           | **Pino** → hosted log drain                      |
| Uptime / synthetic checks | Provider built-in                                |

---

## Compliance

| Standard        | How addressed                                                                                                                                            |
| --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **GDPR**        | Data-export endpoint (`GET /users/{id}/export`); erasure consent on signup; cookie disclosures; workflow with anonymisation job                          |
| **WCAG 2.1 AA** | Verified in CI with axe-core; manual checklist before submission                                                                                         |
| **PCI DSS**     | Out-of-scope — card data never reaches the server; Stripe Elements + PayPal Checkout tokenise on the client; only `provider_reference` + metadata stored |

---

## Outstanding Decisions (TBD)

These need to be locked before the build phase:

1. **Backend hosting:** Railway vs Render.
2. **Database hosting:** Supabase vs Railway.
3. **Redis hosting:** Upstash vs Railway.
4. **AI chat provider:** OpenAI vs Anthropic.
5. **Locale enum naming:** keep `ENG`/`PKR` (market labels) or switch to `en`/`ur` (language codes). Affects `User.locale`, the `Locale` Prisma enum, notification templates, and `Accept-Language` headers.

Each decision is referenced from the design packs as an alternation (e.g. "Railway/Render"); fixing them here removes that ambiguity everywhere.
