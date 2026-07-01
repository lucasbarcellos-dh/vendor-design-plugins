# Vendor Design Plugins

Claude Code plugin marketplace for Delivery Hero's Vendor design and product team.

## Plugins

### `vendor-canvas`

Bootstraps the [Vendor Canvas](https://github.com/deliveryhero/vendor-canvas) design prototyping hub. It ships one command:

- **`/onboard`** — clones the Canvas, installs dependencies, sets up your personal user folder, and starts the dev server. No manual GitHub clone or terminal setup required.

Once you've onboarded and are working inside the Canvas repo, the rest of the tooling (`/create-project`, `/run`, `/deploy`, `/figma`, and the Cape/Figma skills) is provided by the repo's own `.claude/` config — this plugin only covers the first-run bootstrap.

## Install

**From the Claude Code CLI:**

```
/plugin marketplace add lucasbarcellos-dh/vendor-design-plugins
/plugin install vendor-canvas@vendor-design-plugins
```

**From the desktop app:** Plugins → sync this repository, then install `vendor-canvas`.

> ℹ️ This repo currently lives under a personal account for testing. Once the team can publish under "Your organization", it will move to `deliveryhero/vendor-design-plugins` (GitHub preserves history and redirects the old URL).

Then run `/onboard` to get set up.

## Requirements

- Node 18+ and git installed locally.
- Read access to the private `deliveryhero/vendor-canvas` repo (for the `git clone` step). Delivery Hero uses SAML SSO on GitHub — you may need to authorize SSO for the org on your GitHub account.
- A GitHub Personal Access Token with `read:packages` scope (SSO-authorized) for the private `@deliveryhero` Cape packages. `/onboard` walks you through this if `npm ci` fails.

## Notes

This repo is intentionally **thin** — it carries only the bootstrap command and no internal design-system data, so it can stay public without leaking anything. The full command and skill set lives in the Canvas repo and is loaded from there once cloned.
