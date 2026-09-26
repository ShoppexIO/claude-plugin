---
name: shoppex-code-storefront
description: "Build and deploy a Shoppex code storefront (Advanced lane, React and Vite) with the shoppex CLI. Use to pull, edit, push and deploy a code theme."
---

# Shoppex code storefronts

A code storefront is a Vite + React project that Shoppex builds in a sandbox
and serves from its edge with the shop's data injected. This skill is for
Claude Code, where you can run the `shoppex` CLI in the merchant's project.

## Setup

```bash
npx @shoppexio/cli auth login --api-key <shop API key>
npx @shoppexio/cli storefront list
npx @shoppexio/cli storefront pull --theme <id> --dir ./storefront
```

The merchant creates the API key under Settings · Developer · API Keys. Never
print the key back or write it into project files.

## Edit loop

1. Read `AGENTS.md` in the project first. It maps which files are safe to
   change and which rules the build enforces.
2. Keep the rules it lists, most importantly:
   - never edit `package.json` or the lockfile; the build reinstalls a fixed
     dependency set;
   - keep `base: './'` in `vite.config.ts`;
   - import commerce logic from `@shoppexio/storefront-react`, never copy it;
   - no literal colours in sections, pages or layout; use the tokens;
   - keep `data-sx-config` attributes on content-driven elements.
3. Run the project's tests (`bun test`) and `bun run build` locally.
4. `npx @shoppexio/cli storefront push --dir ./storefront` uploads the
   source and starts a build. `storefront status` shows the build.
5. `npx @shoppexio/cli storefront deploy --dir ./storefront` makes the
   built artifact live. Ask the merchant before deploying.

## Copy and content

Shop copy (hero text, section order, theme values) belongs in the saved
content draft that the dashboard Design tab edits, not in
`content-defaults.json`. Editing the defaults does not change an existing
store.

## When a build fails

`storefront status` prints the build log. Fix the first error it names. A
dependency error almost always means `package.json` or the lockfile changed;
restore them from the pulled version.
