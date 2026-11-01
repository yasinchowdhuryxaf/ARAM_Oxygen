# Project case study

## Problem

A cylinder business needs more than a product catalog. A customer may request a refill, exchange an empty cylinder, or buy a cylinder. Staff need to see the order, confirm eligibility, handle the correct inventory movement, record payments, and track delivery.

## Implemented application

The project connects a guest storefront and a restricted administration panel to the same PostgreSQL database. Customers do not need to register. Administrative information and login links are absent from storefront navigation; server-side authorization protects the staff operations.

## Customer features

- Product cards and detail pages with cylinder images and capacity options.
- Service selection for configured refill, exchange, or purchase offers.
- Cart updates and guest checkout with contact and delivery information.
- Delivery upazila selection and server-calculated charges.
- Private order tracking using an order number and access token.
- Quote requests and private staff-response retrieval.
- Contact enquiry submission.

## Nine administrator sections

| Section | Implemented controls |
| --- | --- |
| Products | Product details, variants, service/customer offers, prices, availability, publishing, and image management |
| Orders | Search, filters, pagination, review, status progression, payments, refill/exchange actions, deletion/restoration |
| Customers | Contact editing, verification, and blocking |
| Delivery zones | Coverage, fees, minimum subtotals, and enable/disable controls |
| Inventory | Actual count entry, audited corrections, movement history, and capacity/product totals |
| Quotes | Staff responses and request status |
| Messages | Enquiry review and NEW/READ/RESOLVED status |
| Settings | Home-service eligibility and current-password-protected admin login changes |
| Audit | Administrator action history without credential values |

## Technical problems addressed

### Duplicate orders after retry

A customer may submit again after a slow connection or a lost response. The implementation binds an idempotency key to the checkout identity and request contents. A valid replay recovers the existing order instead of creating another one.

### Concurrent inventory updates

Checkout and stock transitions use serializable database transactions with bounded conflict retries. Reservations must not exceed filled stock, and a stock correction cannot invalidate existing reservations.

### Different cylinder ownership

Customer-owned refill jobs are separate from ARAM-owned aggregate inventory. Exchange returns are tied to customer holdings. This prevents a customer refill from incorrectly reducing ARAM's warehouse count.

### Complete order visibility

Orders have a dedicated paginated endpoint, search, status filters, global counts, and automatic refresh. Staff are not limited to the most recent 100 orders.

### Safe deletion

Admin deletion is recoverable. It hides an order from active views and public tracking while preserving payments, stock records, history, and idempotency references. It does not perform a refund or release stock automatically.

## Remaining scope

Online payments/refunds, automatic email/SMS, password recovery, two-factor authentication, serialized-cylinder inspections, and multi-warehouse allocation remain future improvements. Payments currently require manual verification. Production hosting and recovery testing must be performed for the chosen environment.
