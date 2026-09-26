---
name: shoppex-store
description: "Work in a merchant's Shoppex store with the Shoppex tools. Use for products, orders, customers, coupons, payment links, support tickets and sales analytics."
---

# Shoppex store operations

The Shoppex tools act on one store: the store the merchant picked when they
connected Shoppex. Every write is live for real buyers at once. There is no
draft or undo for products, coupons, orders or customers.

## Before a write

- Read first. Get the current record (`products_get`, `orders_get`,
  `coupons_get`, …) before you update it, and change only the fields the
  merchant asked for.
- Say what will change in one or two lines and wait for a clear yes before
  `*_create`, `*_update`, `*_delete`, `orders_fulfill`, `subscriptions_cancel`,
  `blacklist_add` or `tickets_close`.
- For a change to many records, show the list first and confirm the count.
- Never guess an ID. Look it up with a `*_list` tool and use the `id` or
  `uniqid` it returns.

## Money and stock

- Prices are decimal strings in the store currency (`"9.99"`), never floats.
- `available_stock` can be 0 while `orderable` is true: supplier-backed
  Dynamic Delivery products sell without local stock. Use `orderable` to
  decide whether a product can be bought.
- These tools do not issue refunds, move money or credit wallets. Send the
  merchant to the dashboard for that.

## Orders and support

- `orders_fulfill` marks an order delivered. For key products the keys
  already went out; fulfil only when the merchant says the buyer has what
  they paid for.
- Ticket replies go to the buyer by email. Draft the reply, show it, then
  send it with `tickets_reply` after the merchant approves the text.

## Reports

- `analytics_revenue` and `analytics_report_generate` answer questions about
  sales. Name the period and the currency in the answer.
- Keep answers short: the numbers the merchant asked for, then one line of
  context. Do not dump raw JSON.

## When a tool fails

- `auth_error`: the connection lost access or lacks a permission. Ask the
  merchant to reconnect Shoppex in Claude's connector settings.
- `rate_limited`: wait a moment and retry once.
- `not_found`: re-read the list; the record may be gone or the ID wrong.
- Validation errors name the field. Fix that field; do not retry blindly.
