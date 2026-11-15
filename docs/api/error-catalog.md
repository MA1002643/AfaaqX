# afaaqX Error Catalogue

Every error response from the afaaqX API follows [RFC 7807 — Problem Details for HTTP APIs](https://www.rfc-editor.org/rfc/rfc7807). The `Content-Type` is always `application/problem+json`, and the body conforms to the `Problem` (or `ValidationProblem`) schema declared in [`openapi.yaml`](openapi.yaml).

## Conventions

- The `code` is the **stable** machine identifier; clients must never branch on `title` or `detail` (both may be localised).
- `traceId` is also surfaced in the `traceparent`/`X-Request-Id` response headers and is the same id stored on `audit_log.id`/Sentry events.
- `title` / `detail` are English only (localisation is out of scope).
- The `type` is a fully-qualified URI under `https://errors.afaaqx.com/<slug>`. Slug = lowercase form of `code` with `_` → `-`.

## HTTP Status Usage

| Status                       | Used for                                                                 |
| ---------------------------- | ------------------------------------------------------------------------ |
| `400 Bad Request`            | JSON is Malformed, type of content is wrong, required headers is missing |
| `401 Unauthorized`           | Invalid/access token is expired, refresh failures/missing                |
| `403 Forbidden`              | Authenticated but lacks the required permission                          |
| `404 Not Found`              | Resource are not visible to the caller or do not exist                   |
| `405 Method Not Allowed`     | Wrong HTTP verb for an existing path                                     |
| `409 Conflict`               | Uniqueness violation, double-booked slot, optimistic concurrency         |
| `410 Gone`                   | Permanently removed resources                                            |
| `415 Unsupported Media Type` | Request content type is not supported                                    |
| `422 Unprocessable Entity`   | business-rule violation (`ValidationProblem`), Validation failed         |
| `423 Locked`                 | After failed attempts account is locked                                  |
| `429 Too Many Requests`      | Rate limit exceeded; `Retry-After` header set                            |
| `500 Internal Server Error`  | Server error unhandled                                                   |
| `502 Bad Gateway`            | Upstream provider (Stripe, AI) returned a failure we cannot recover from |
| `503 Service Unavailable`    | Planned maintenance; health check failure                                |
| `504 Gateway Timeout`        | Upstream timeout                                                         |

## Envelope

```jsonc
{
  "type": "https://errors.afaaqx.com/forbidden", // Stable URI for this error class
  "title": "Forbidden", // Short human-readable summary
  "status": 403, // Matches HTTP status
  "detail": "You do not have permission to cancel this order.",
  "instance": "urn:afaaqx:request:0db7…", // Per-request URN; matches `traceId`
  "traceId": "0db7c2a6-9c2b-4f6a-9e90-bd14d1b2af3a", // Correlate with Sentry/logs
  "code": "AUTH_FORBIDDEN", // Stable afaaqX-specific code (see table)
}
```

`ValidationProblem` extends the envelope with an `errors[]` array of `{ field, message, code }` for field-level diagnostics.

## Error Codes

### Validation

| Code                   | HTTP | When                                       |
| ---------------------- | ---- | ------------------------------------------ |
| `VALIDATION_FAILED`    | 422  | See `errors[]`; one or more fields invalid |
| `FIELD_TOO_LONG`       | 422  | Above maximum length                       |
| `FIELD_INVALID_FORMAT` | 422  | Regex/format mismatch                      |
| `FIELD_OUT_OF_RANGE`   | 422  | Numeric out of range                       |
| `FIELD_REQUIRED`       | 422  | Required field missing                     |
| `FIELD_TOO_SHORT`      | 422  | Below minimum length                       |

### Generic

| Code                  | HTTP | When                                            |
| --------------------- | ---- | ----------------------------------------------- |
| `BAD_REQUEST`         | 400  | General malformed request                       |
| `INVALID_JSON`        | 400  | JSON Body is not valid                          |
| `MISSING_HEADER`      | 400  | Required header is absent                       |
| `INTERNAL_ERROR`      | 500  | Unhandled exception (always includes `traceId`) |
| `SERVICE_UNAVAILABLE` | 503  | Health probe failing                            |
| `UPSTREAM_TIMEOUT`    | 504  | Upstream provider exceeded SLA                  |
| `UPSTREAM_ERROR`      | 502  | Failed upstream provider                        |
| `UNSUPPORTED_MEDIA`   | 415  | Body content type not supported                 |
| `METHOD_NOT_ALLOWED`  | 405  | Verb on this path is not supported              |
| `NOT_FOUND`           | 404  | Resource not found / not visible                |

### Auth

| Code                       | HTTP | When                                               |
| -------------------------- | ---- | -------------------------------------------------- |
| `AUTH_REQUIRED`            | 401  | No access token presented                          |
| `AUTH_INVALID_TOKEN`       | 401  | `iss`, `aud`, or `exp` JWT signature failed        |
| `AUTH_REVOKED`             | 401  | `sid` revoked or `jti` in revocation list          |
| `AUTH_REFRESH_INVALID`     | 401  | Refresh cookie expired, mismatched, or missing     |
| `AUTH_ACCOUNT_SUSPENDED`   | 403  | `User.status = suspended`                          |
| `AUTH_EMAIL_NOT_VERIFIED`  | 403  | Email requires to be verified                      |
| `AUTH_ACCOUNT_LOCKED`      | 423  | locked for 15 mins due to 5+ failed login attempts |
| `AUTH_INVALID_CREDENTIALS` | 401  | Email or password is mismatch                      |
| `AUTH_REFRESH_REUSED`      | 401  | Reuse of revoked refresh token; family revoked     |
| `AUTH_FORBIDDEN`           | 403  | Authenticated but lacks the required permission    |

### Users & Staff

| Code                         | HTTP | When                               |
| ---------------------------- | ---- | ---------------------------------- |
| `USER_EMAIL_TAKEN`           | 409  | Already registered email           |
| `USER_SELF_DELETE_FORBIDDEN` | 403  | Admin cannot delete it own account |
| `STAFF_NUMBER_TAKEN`         | 409  | `staff_number` is a duplicate      |
| `USER_NOT_FOUND`             | 404  | Miss lookup                        |

### Orders

| Code                       | HTTP | When                                     |
| -------------------------- | ---- | ---------------------------------------- |
| `CART_EMPTY`               | 422  | With an empty cart cannot place an order |
| `ORDER_NOT_FOUND`          | 404  | Lookup miss                              |
| `ORDER_INVALID_TRANSITION` | 409  | E.g. cancel after `fulfilled`            |

### Catalogue

| Code                      | HTTP | When                                                   |
| ------------------------- | ---- | ------------------------------------------------------ |
| `PRODUCT_NOT_FOUND`       | 404  | Product is not available                               |
| `PRODUCT_SLUG_TAKEN`      | 409  | Slug collision                                         |
| `CATEGORY_HAS_PRODUCTS`   | 409  | Attempted to delete the category while it is not empty |
| `PRODUCT_SKU_TAKEN`       | 409  | SKU collision                                          |
| `PRODUCT_OUT_OF_STOCK`    | 409  | Available stock is lower than the requested quantity   |
| `PRODUCT_IMAGE_LIMIT`     | 409  | Image limit                                            |
| `PRODUCT_NOT_PUBLISHABLE` | 422  | Mandatory fields required to publish the product       |
| `CATEGORY_NOT_FOUND`      | 404  | The category the user is searching for is not found    |

### Services & Appointments

| Code                        | HTTP | When                                                                     |
| --------------------------- | ---- | ------------------------------------------------------------------------ |
| `SERVICE_NOT_FOUND`         | 404  | Lookup miss                                                              |
| `SERVICE_INACTIVE`          | 422  | Try to book a service that is not available                              |
| `APPT_SLOT_TAKEN`           | 409  | Excisting appointment conflics with the appointment slot                 |
| `APPT_OUTSIDE_HOURS`        | 422  | Appointment outside business hours                                       |
| `APPT_TOO_SHORT_NOTICE`     | 422  | Appointment booked at a very short notice                                |
| `APPT_INVALID_TRANSITION`   | 409  | E.g. accepting an appointment that is already cancelled                  |
| `APPT_RESCHEDULE_FORBIDDEN` | 403  | Customer trying to reschedule the appointment after accepted by business |

### AI Assistant

| Code                    | HTTP | When                                         |
| ----------------------- | ---- | -------------------------------------------- |
| `ASSISTANT_UNAVAILABLE` | 503  | AI provider is down or did not reply in time |

### Payments

| Code                        | HTTP | When                                                   |
| --------------------------- | ---- | ------------------------------------------------------ |
| `PAYMENT_NOT_FOUND`         | 404  | Payment history not found                              |
| `PAYMENT_INTENT_EXPIRED`    | 410  | Checkout session expired before completing the payment |
| `WEBHOOK_SIGNATURE_INVALID` | 400  | Verification failed for the provider signature         |
| `WEBHOOK_DUPLICATE`         | 200  | Duplicate result found                                 |
| `PAYMENT_PROVIDER_ERROR`    | 502  | Unrecoverable failure by the Provider                  |
| `PAYMENT_ALREADY_SETTLED`   | 409  | Cannot retake a successful payment                     |

### Rate Limiting & Idempotency

| Code                      | HTTP | When                                    |
| ------------------------- | ---- | --------------------------------------- |
| `RATE_LIMITED`            | 429  | Bucket exhausted; `Retry-After` set     |
| `IDEMPOTENCY_IN_PROGRESS` | 409  | Same key still processing               |
| `IDEMPOTENCY_KEY_REUSED`  | 409  | Different request body but the same key |

### Files

| Code                    | HTTP | When                         |
| ----------------------- | ---- | ---------------------------- |
| `FILE_TOO_LARGE`        | 422  | Exceeds 10 MB per file limit |
| `FILE_NOT_FOUND`        | 404  | File can't be found          |
| `FILE_TYPE_NOT_ALLOWED` | 422  | File type outside allow-list |

## Examples

### 422 validation failure

```json
{
  "type": "https://errors.afaaqx.com/validation-failed",
  "title": "Validation failed",
  "status": 422,
  "code": "VALIDATION_FAILED",
  "errors": [
    {
      "field": "email",
      "code": "FIELD_INVALID_FORMAT",
      "message": "Must be a valid email"
    },
    {
      "field": "password",
      "code": "FIELD_TOO_SHORT",
      "message": "Minimum length is 10"
    }
  ],
  "traceId": "f1a2…"
}
```

### 429 rate limited

```http
HTTP/1.1 429 Too Many Requests
Retry-After: 30
Content-Type: application/problem+json

{
  "type":   "https://errors.afaaqx.com/rate-limited",
  "title":  "Too many requests at once",
  "status": 429,
  "code":   "RATE_LIMITED",
  "detail": "Login Blocked. Try again in 30 seconds."
}
```

### 409 appointment slot taken

```json
{
  "type": "https://errors.afaaqx.com/appt-slot-taken",
  "traceId": "b32a…",
  "title": "Slot not available",
  "status": 409,
  "code": "APPT_SLOT_TAKEN",
  "detail": "Another appointment overlaps the requested time."
}
```

## Adding a new error code

1. Add the row to the rection section above.
2. Note the `type` URI (no resource needs to be in place; the URI is mainlty a stable identifier).
3. Add the code to the application's `ErrorCode` enum.
4. Mention the code in the correct route's description in `openapi.yaml`. If it will be handled by clients programmatically.
