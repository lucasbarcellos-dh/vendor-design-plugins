# Vendor Design Plugins

Claude Code plugin marketplace for Delivery Hero's Vendor design and product team.

## `vendor-canvas`

Bootstraps the [Vendor Canvas](https://github.com/deliveryhero/vendor-canvas) design prototyping hub. Ships one command:

- **`/onboard`** — clones the Canvas, installs dependencies, sets up your user folder, and starts the dev server. No manual GitHub clone or terminal setup required.

The rest of the tooling (`/create-project`, `/run`, `/deploy`, `/figma`, and the Cape/Figma skills) comes from the repo's own `.claude/` config once cloned.

## Install

```
/plugin marketplace add lucasbarcellos-dh/vendor-design-plugins
/plugin install vendor-canvas@vendor-design-plugins
```

Or, from the desktop app: Plugins → sync this repository, then install `vendor-canvas`. Then run `/onboard`.

## Requirements

- Node 18+ and git installed locally.
- Read access to the private `deliveryhero/vendor-canvas` repo (you may need to authorize SAML SSO for the org on GitHub).
- A GitHub PAT with `read:packages` scope (SSO-authorized) for the private `@deliveryhero` Cape packages. `/onboard` walks you through this if `npm ci` fails.
