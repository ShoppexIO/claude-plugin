# Request recipes

Typical merchant requests and the tool order that answers them. `(write)`
marks a step that changes live data: say what will change and wait for a yes.

## Sales and reporting

**"How much did I make last week?"**
1. `analytics_revenue` with `from` / `to` as ISO dates and the shop `currency`.
2. Answer with the total, the period and the currency. Add one line of
   context (for example the order count from `orders_list`) only if asked.

**"What sold best this month?"**
1. `orders_list` (newest first, `limit` up to 50) and page through the month.
2. Group by product, sum the totals, name the top three.

**"Give me a daily brief."** Use the `shoppex_daily_brief` prompt: revenue,
orders, invoices, open disputes and tickets in one pass.

## Products and stock

**"Add a product: Netflix 1 month, 4.99, keys attached."**
1. `products_create` (write) with `title`, `price: 4.99`, `type: "SERIALS"`,
   `serials: [...]`. Leave `gateways` out to use the store's payment methods.
2. Return the product `uniqid` and ask whether it should stay visible or be
   created `unlisted` (link only) or `private` (merchant and staff only).

**"Restock product X with these 50 keys."**
1. `products_list` with `search` to find the product; confirm it is the
   right one when the title is ambiguous.
2. `products_upload_serials` (write) with `serials` and
   `remove_duplicates: true`.

**"Hide product X for now."**
1. `products_list` → `products_update` (write) with `unlisted: true` (hidden
   from listings, link still works), `private: true` (merchant and staff
   only) or `on_hold: true` (visible, no new purchases).

## Discounts and links

**"20% coupon LAUNCH20 for 100 uses, valid until Sunday."**
1. `coupons_create` (write) with `code`, `discount_type: "PERCENTAGE"`,
   `discount_value: 20`, `max_uses: 100`, `valid_until` as an ISO date.

**"Make a checkout link for product X."**
1. `products_get` for the price and `uniqid`.
2. `payment_links_create` (write) with `name`, `type: "PRODUCT"`, `price`,
   `product_ids: [uniqid]`, `gateways` (copy the product's gateways).
3. Return the link URL from the response.

## Orders and buyers

**"Did mia@example.com get her order?"**
1. `orders_list` with `customer_email`.
2. `orders_get` for the latest order: status, delivered items, gateway.
3. If it is paid but not delivered and it is a service product,
   `orders_fulfill` (write) with a short `message` after the merchant agrees.

**"Who is this buyer?"** `customers_get` with `email` (or `customer_id`),
then `orders_list` with `customer_email` for their history.

**"Block this scammer."**
1. `blacklist_add` (write) with `type` (`email`, `ip`, `country`, `domain`
   or `phone`), `value` and a `reason`.

## Support

**"Answer my open tickets."**
1. `tickets_list` with `status: "open"`.
2. `tickets_get` per ticket, then draft a reply for each.
3. Show the drafts; send each approved one with `tickets_reply` (write).
   Close with `tickets_close` (write) only when the merchant says so.

## Disputes

**"Any chargebacks?"** `disputes_list` with `status: "open"`, then
`disputes_get` for the evidence deadline. Evidence is submitted in the
dashboard, not through these tools.

## Not available here

Refunds, payouts, wallet credit and gateway settings are dashboard-only.
Say so and link the merchant to https://dashboard.shoppex.io.
