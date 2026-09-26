# Workflow definition format

`workflows_catalog` is the source of truth for trigger types, condition
fields, action keys and their params. This file shows the shape they fit
into and one complete example.

## Shape

```json
{
  "trigger": { "type": "<catalog trigger>", "config": {} },
  "conditions": { "match": "all", "conditions": [] },
  "steps": [],
  "settings": { "run_limit": "every_time", "max_runs_per_hour": 100 }
}
```

- `conditions.match`: `all` or `any`. Each condition is
  `{ "id", "field", "operator", "value" }`; `field` is a dotted path from
  the catalog (`order.total`, `customer.paid_orders`).
- Operators: `eq`, `neq`, `gt`, `gte`, `lt`, `lte`, `contains`,
  `not_contains`, `starts_with`, `ends_with`, `in`, `not_in`,
  `includes_any`, `includes_none`, `is_set`, `is_not_set`, `is_true`,
  `is_false`.
- Step kinds:
  - `action`: `{ "id", "kind": "action", "action", "params", "continue_on_error" }`
  - `delay`: `{ "id", "kind": "delay", "amount", "unit" }` with `unit`
    `minutes`, `hours` or `days` (at most 90 days)
  - `check`: `{ "id", "kind": "check", "conditions": { ... } }` — only
    continue if the conditions still hold after a delay
- `settings.run_limit`: `every_time`, `once_per_customer` or
  `once_per_order`. `max_runs_per_hour` defaults to 100 (ceiling 1000).
- At most 20 steps. Step `id`s are unique within the definition.
- Text params accept `{{variables}}` from the catalog (`{{customer.email}}`,
  `{{shop.name}}`, `{{coupon.code}}` after a `create_coupon` step).

## Example: reward loyal buyers

From the third paid order, tag the buyer as VIP and email a 15% coupon, once
per buyer. This is the `vip_reward` template.

```json
{
  "trigger": { "type": "order:paid", "config": {} },
  "conditions": {
    "match": "all",
    "conditions": [{ "id": "third", "field": "customer.paid_orders", "operator": "gte", "value": 3 }]
  },
  "steps": [
    { "id": "tag", "kind": "action", "action": "tag_customer", "params": { "tag": "vip" }, "continue_on_error": true },
    {
      "id": "coupon", "kind": "action", "action": "create_coupon",
      "params": { "discount_type": "PERCENTAGE", "amount": 15, "expires_in_days": 30, "product_ids": [], "prefix": "VIP" },
      "continue_on_error": false
    },
    {
      "id": "email", "kind": "action", "action": "email_customer",
      "params": {
        "subject": "A thank-you from {{shop.name}}",
        "body": "Hi,\n\nHere is 15% off your next order: {{coupon.code}}\nIt is valid for 30 days.\n\n{{shop.name}}"
      },
      "continue_on_error": false
    }
  ],
  "settings": { "run_limit": "once_per_customer", "max_runs_per_hour": 100 }
}
```

## Delay then check

To act later only if nothing changed, put a `check` after the `delay`:

```json
[
  { "id": "wait", "kind": "delay", "amount": 30, "unit": "days" },
  {
    "id": "lapsed", "kind": "check",
    "conditions": { "match": "all", "conditions": [{ "id": "no-newer-order", "field": "customer.days_since_last_order", "operator": "gte", "value": 30 }] }
  }
]
```

## Templates that need setup

Some templates leave params `null` (a Discord `role_id`, a `channel_id`).
`workflows_validate` reports them by field path; ask the merchant for the
value instead of guessing.
