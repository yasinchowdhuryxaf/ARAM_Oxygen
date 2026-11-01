# Verification and limitations

## Latest focused checks

Before the source publication on 1 November 2026, the complete local application passed TypeScript checking and seven unit tests across validation, delivery checkout, and inventory summaries. Its health endpoint reported connected PostgreSQL.

The portfolio conversion changes public presentation only. The complete application, business database, credentials, and uploads remain separate from this repository.

## Historical development checks

During October 2026, development verification covered:

| Area | Scenarios checked |
| --- | --- |
| Order persistence | Guest checkout saved in PostgreSQL and displayed in admin |
| Server totals | Catalog price, delivery charge, and order snapshot consistency |
| Authorization | Guest/USER denial for admin APIs and protected images |
| Privacy | Private tracking tokens and customer ownership restrictions |
| Duplicate requests | Same-key replay, concurrent identical submissions, and lost-response recovery |
| Concurrency | Multiple guest orders and stock-reservation conflicts |
| Inventory | Cancellation release, exchange returns, and refill ownership separation |
| Payments | Balance checks, duplicate references, and blocked invalid state changes |
| Admin | Search/pagination, delete/restore, messages, quote replies, and credential changes |
| Images | Invalid upload rejection, cover selection, draft privacy, and removal |
| Recovery | Docker persistence and application reconnection after database restart |
| Browser | Guest/admin separation, checkout, dashboard refresh, and responsive layouts |

Integration checks used disposable records in an isolated test database, not synthetic orders in the business database. The source, executable tests, and test credentials are intentionally absent from this portfolio.

## Practical limits

Historical checks are development evidence, not an external audit or an uptime guarantee. They must be repeated after relevant changes. Production deployment still needs HTTPS/session verification, persistent uploads, monitoring, suitable load testing, and a successful database/image restore drill.

The project does not claim online payment/refund processing, automatic notifications, verified medical credentials, or serialized-cylinder inspection records.
