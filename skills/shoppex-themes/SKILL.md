---
name: shoppex-themes
description: "Edit a Shoppex hosted storefront theme with the theme tools. Use to read the block schema, change the ThemeDocument or Builder settings, save and publish."
---

# Shoppex theme editing

Hosted Shoppex storefronts render a ThemeDocument: pages made of blocks from
a fixed catalog. Saving writes a draft; buyers see a change only after
`publish_theme_document`.

## Pick the source of truth

`theme_list` shows each theme. Its provenance decides the edit path:

- `document_authoritative`: edit the ThemeDocument.
- `settings_derived`: edit Builder settings. `save_theme_document` answers
  409 for these themes by design.

## Document edits

1. `get_document_schema`: the block catalog, variants and settings. Use only
   blocks and values it lists.
2. `get_theme_document`: the full document and its `revision`.
3. Change only what the merchant asked for. Keep every other block, ID and
   setting as it was.
4. `save_theme_document` with the whole document and the revision from
   step 2. The result carries the new revision.
5. Describe the change and ask before publishing.
6. `publish_theme_document` with the revision from step 4.

## Settings edits

1. `theme_settings_get` returns the settings and their `revision`.
2. Change the fields, keep `revision` as read: the tool sends revision + 1.
3. `theme_settings_update`, then publish with the returned revision after
   the merchant agrees.

## Conflicts

A 409 means someone saved in between (often the merchant in the Builder).
Read again, apply the change to the new version, and save. Never overwrite a
newer draft and never publish a revision you have not read.

## Code storefronts

Themes in the Advanced lane (`source_mode: code`) are React projects, not
documents. See the `shoppex-code-storefront` skill.
