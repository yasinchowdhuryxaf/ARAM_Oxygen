# Selected code walkthrough

These short excerpts illustrate decisions in the complete application. Imports, services, schemas, entry points, and build configuration are intentionally omitted. They cannot recreate or run the full application.

## Avoid counting reserved cylinders twice

Reservations are a subset of filled stock. Physical totals include stock in the warehouse, in transit, and with customers; available stock subtracts reservations.

Excerpt from the inventory summary helper:

```typescript
recorded:
  total.recorded +
  row.filled +
  row.empty +
  row.awaitingRefill +
  row.maintenance +
  row.inTransit +
  row.withCustomer,
available: total.available + row.filled - row.reserved,
reserved: total.reserved + row.reserved,
```

The associated unit test checks a synthetic example: 50 filled, 10 reserved, 5 empty, 4 awaiting refill, 3 in maintenance, 2 in transit, and 1 with a customer. Expected totals are 65 recorded, 40 available, and 10 reserved. These are test values, not business inventory.

## Authorize on the server

Excerpt from the authentication helper:

```typescript
export async function requireUser(admin = false) {
  const user = await currentUser();
  if (!user) throw new HttpError(401, 'Please sign in.');
  if (admin && user.role !== 'ADMIN')
    throw new HttpError(403, 'Administrator access required.');
  return user;
}
```

Administrative APIs call this server-side guard. Removing a navigation link is not an authorization mechanism. The omitted session service reads a token from an HttpOnly cookie, resolves its stored digest, and checks expiry.

## Reject cross-origin mutations

Excerpt from the HTTP helper:

```typescript
const expected = new URL(process.env.APP_URL || 'http://localhost:3000').origin;
if (r.headers.get('origin') !== expected)
  throw new HttpError(403, 'Invalid request origin.');
```

This is one part of the request controls. It works alongside role checks, bounded validation, cookie settings, and rate limiting.

## Checkout consistency

The following is explanatory pseudocode, not copied application code:

```text
validate input and identify checkout owner
look up idempotency key and compare request fingerprint
load enabled offers, current prices, and delivery rule
begin serializable transaction
    recheck eligibility and required stock
    save customer snapshot, order lines, totals, and history
    apply required reservation or custody records
commit
return existing or newly created order reference
```

The browser retries a recoverable checkout using the same request and key. Conflicting request contents are rejected, and bounded transaction retries address rolled-back database conflicts.
