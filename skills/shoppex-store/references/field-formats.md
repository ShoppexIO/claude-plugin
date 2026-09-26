# Field formats

Input rules the Shoppex tools enforce. A field marked required must be sent;
everything else may be left out.

## Money

- Tool inputs take amounts as numbers in major units of the currency:
  `price: 4.99`, `discount_value: 10`. Not cents, not strings.
- `currency` is an ISO code such as `"USD"` or `"EUR"`. Left out, a product
  uses the store currency.
- Responses may carry amounts as strings; read them as decimals.

## Identifiers

- Products, coupons, invoices and most resources have an `id` and a
  `uniqid`. Tools named `*_get` accept either; pass what a `*_list` returned.
- Never build an ID yourself.

## Products — `products_create`

| Field | Rule |
|---|---|
| `title` | required |
| `price` | required, number ≥ 0 |
| `type` | required: `SERVICE` (manual or text delivery), `SERIALS` (license keys), `DYNAMIC` (delivered by the merchant's webhook), `FILE` (download) |
| `serials` | for `SERIALS`: one key per array entry |
| `stock` | integer; `-1` (the default) means unlimited |
| `gateways` | leave out to use the store's payment methods; set it only to restrict a product |
| `unlisted` | hidden from listings, the product link still works |
| `private` | only the merchant and staff see it |
| `service_text`, `delivery_text` | text the buyer sees after paying |

`products_update` takes the same fields plus `on_hold` (stop sales without
hiding the product).

## Coupons — `coupons_create`

| Field | Rule |
|---|---|
| `code` | required, 1–64 characters |
| `discount_type` | `PERCENTAGE` (default) or `FIXED` |
| `discount_value` | required, positive number: percent for `PERCENTAGE`, amount for `FIXED` |
| `max_uses` | positive integer, or leave out for unlimited |
| `product_ids` | limit to these products; leave out for all |
| `min_order_amount`, `max_discount` | numbers ≥ 0 |
| `valid_from`, `valid_until` | ISO 8601 date or date-time |

## Payment links — `payment_links_create`

Required: `name`, `type`, `price`, `gateways` (at least one). `type` is one
of `PRODUCT`, `SUBSCRIPTION`, `SUBSCRIPTION_V2`, `LICENSE`,
`PAY_WHAT_YOU_WANT`, `FIXED_PRICE`. `cta` is `PAY` (default), `BOOK`,
`DONATE` or `SUBSCRIBE`. Link products with `product_ids`.

## Filters on lists

| Tool | Filter values |
|---|---|
| `orders_list` | `status` such as `COMPLETED`, `PENDING`, `DISPUTED`; `customer_email` |
| `invoices_list` | `status`: `pending`, `paid`, `cancelled`, `refunded` |
| `disputes_list` | `status`: `open`, `won`, `lost`, `warning` |
| `tickets_list` | `status` such as `open`, `closed` |
| `customers_list` | `filters` expression, for example `email:john@example.com` |
| `blacklist_list` / `blacklist_add` | `type`: `email`, `ip`, `country`, `domain`, `phone` |
| `licenses_update` | `status`: `ACTIVE`, `SUSPENDED`, `REVOKED`, `EXPIRED` |

`limit` is 1–50 on list tools; leave it out for the API default.

## Dates

`analytics_revenue`, coupon validity and run filters take ISO 8601 dates
(`2026-09-01` or `2026-09-01T00:00:00Z`). Say which timezone you assumed.
