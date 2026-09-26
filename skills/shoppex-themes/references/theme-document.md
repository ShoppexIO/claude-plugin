# ThemeDocument

`get_theme_document` returns:

```json
{
  "theme_id": "…",
  "document": { "version": 2, "name": "Default", "pages": {}, "globalBlocks": [], "theme": {} },
  "revision": 14,
  "provenance": "document_authoritative",
  "publish_state": "DRAFT",
  "published_revision": 12
}
```

`revision` goes into `save_theme_document` as `expected_revision`; the save
returns the next revision, which `publish_theme_document` needs.

## Structure

- `pages`: a map from page id (`home`, `product`, `cart`, `terms`, …) to
  `{ slug, title, blocks, seo }`.
- `globalBlocks`: blocks on every page (navbar, cart drawer, footer).
- A block: `{ id, type, variant, visible, settings, children }`.
  - `type` and `variant` come from `get_document_schema`.
  - `settings` keys are namespaced by block (`hero.title`, `hero.subtitle`).
  - `children` are element nodes (heading, text, button, image) with their
    own `id` and `type`.
- `theme`: `scheme`, `style_slots` (colours and component tokens), `fonts`,
  `presets`, `custom_css`.
- Text values are a plain string or a locale map:
  `{ "en": "Summer Sale", "de": "Sommerschlussverkauf" }`. Keep the form the
  document already uses; when it is a map, change every locale you can or
  ask for the missing translations.

## Example: change the home hero headline

Before (in `document.pages.home.blocks`):

```json
{
  "id": "block-hero",
  "type": "hero",
  "visible": true,
  "settings": { "hero.title": "Premium accounts", "hero.subtitle": "Instant delivery" },
  "children": []
}
```

After:

```json
{
  "id": "block-hero",
  "type": "hero",
  "visible": true,
  "settings": { "hero.title": "Summer Sale: 20% off everything", "hero.subtitle": "Instant delivery" },
  "children": []
}
```

Send the whole document with only this value changed:

1. `save_theme_document` with `theme_id`, `document`, `expected_revision: 14`
   → returns `revision: 15`.
2. Show the change, ask, then `publish_theme_document` with
   `expected_revision: 15`.

## Rules

- Keep every block `id`; the Builder and the preview address blocks by id.
  A new block needs a new unique id.
- Do not drop fields you did not touch. Unknown keys are rejected, and
  missing ones fall back to defaults the merchant did not choose.
- Limits: 2000 nodes per page including global blocks, 200 children per
  node, nesting depth 12, 2 MB per document.
- To hide a block, set `visible: false` instead of deleting it.
