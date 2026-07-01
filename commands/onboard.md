---
allowed-tools: Read, Write, Edit, Glob, Grep, AskUserQuestion, Bash(git:*), Bash(npm:*), Bash(gh api user*), Bash(gh auth status*), Bash(node:*), Bash(mkdir:*), Bash(cd:*), Bash(ls:*), Bash(pwd:*)
description: Zero-terminal onboarding — clone the Vendor Canvas, install, set up your folder, and run it (desktop app friendly)
---

# Onboard — Get the Vendor Canvas Running From Scratch

Welcome! This command sets up the **Vendor Canvas** on your machine from nothing — no need to manually clone from GitHub first. It's designed to be run from the Claude desktop app after installing the Vendor Canvas plugin.

If the repo is **already cloned and you're working inside it**, use `/initiate` instead — it skips the clone step. This command is for the first-ever setup.

---

## Phase 0: Locate or Clone the Repo

### Step 0.1: Are we already inside the repo?

Run `git remote get-url origin 2>/dev/null`.

- If it returns `https://github.com/deliveryhero/vendor-canvas` (or the SSH equivalent), we're already inside the Canvas repo. Tell the user: "Looks like you're already inside the Vendor Canvas repo — I'll pick up from here." Then skip to **Phase 1**.
- Otherwise, continue to Step 0.2.

### Step 0.2: Check prerequisites

Run these and confirm each is available:
- `git --version`
- `node --version` (need Node 18+; if older or missing, stop and tell the user to install Node 18+ from https://nodejs.org)
- `gh auth status` (GitHub CLI, used later for username; if missing it's non-fatal — note it and continue)

### Step 0.3: Choose where to clone

Use `AskUserQuestion` to ask where to put the project. Offer:
- "`~/Git/vendor-canvas` (recommended)"
- "`~/Developer/vendor-canvas`"
- "Current folder"

Store the chosen parent directory. The clone target is `{parent}/vendor-canvas`.

### Step 0.4: Clone

Run:
```
git clone https://github.com/deliveryhero/vendor-canvas.git {target}
```

If it fails with an authentication or "repository not found" error, the user likely hasn't authorized SSO / lacks repo access. Tell them:

> "I couldn't access `deliveryhero/vendor-canvas`. This usually means one of:
> 1. You need to authorize SAML SSO for the `deliveryhero` org on your GitHub account (GitHub → Settings → Applications, or the SSO prompt).
> 2. You're not yet a member of the GitHub team with read access — ping the hub maintainer.
>
> Once that's sorted, re-run `/onboard`."

Then stop.

After a successful clone, treat `{target}` as the working directory for all remaining steps (all `git`/`npm` commands run there).

Confirm: "✓ Vendor Canvas cloned to {target}"

---

## Phase 1: Sync to Latest Main

Run (inside the repo):
```
git checkout main && git pull origin main
```

If it fails because of uncommitted changes (only possible on a pre-existing clone), tell the user to `git stash` and re-run. Otherwise confirm: "✓ On the latest main."

---

## Phase 2: Install Dependencies

Run: `npm ci`

If it fails with an authentication error (`401 Unauthorized`, `ENEEDAUTH`, or anything mentioning `npm.pkg.github.com`), the private `@deliveryhero` packages need a GitHub token. **Walk the user through it interactively rather than just stopping:**

> "npm needs a GitHub token to install the private `@deliveryhero` Cape packages. Here's the one-time fix:
> 1. Create a Personal Access Token at https://github.com/settings/tokens with the **`read:packages`** scope.
> 2. **Authorize it for SSO**: on the token page, click **Configure SSO → Authorize** for the `deliveryhero` org (this is the step people miss).
> 3. Add it to your shell profile (`~/.zshrc` or `~/.bashrc`):
>    ```
>    export GITHUB_TOKEN=ghp_your_token_here
>    ```
> 4. Reload your shell: `source ~/.zshrc`
>
> Tell me once that's done and I'll retry the install."

When the user confirms, retry `npm ci`. Confirm: "✓ Dependencies installed."

---

## Phase 3: Set Up Your User Folder

This mirrors `/initiate`. Do the following:

1. **Identify the user**: `git config user.email` → username is the part before `@`. If it isn't a `@deliveryhero.com` address, ask them to set it (`git config user.email your.name@deliveryhero.com`) and re-run.
2. **Check for existing folder** under `src/users/{username}/`. If it exists, just set the dev identity (below) and welcome them back.
3. **Create the folder** `src/users/{username}/` with a `.gitkeep`.
4. **GitHub username**: `gh api user --jq '.login'` (if `gh` is unavailable, ask the user for their GitHub username).
5. **Avatar color**: pick one at random from yellow, orange, pink, red, purple, blue, teal, green.
6. **Display name**: use `AskUserQuestion` (shown on the hub home page).
7. **Write** `src/users/{username}/user.config.ts`:
   ```typescript
   import { UserConfig } from '@hub/hub/types';

   export const userConfig: UserConfig = {
     avatarColor: 'var(--hub-avatar-color-{color})',
     githubUsername: '{github-username}',
   };
   ```
8. **Dev identity**: add or update `.env.local` in the project root with `VITE_DEV_USERNAME={username}` (append/update just this line if the file exists — it's gitignored).

---

## Phase 4: Start the Dev Server

Start the local dev server in the background:
```
npm run dev
```

Tell the user the app is running at [http://localhost:5173](http://localhost:5173).

---

## Phase 5: Success

The Canvas commands (`/create-project`, `/run`, `/deploy`, `/figma`) and design skills are **project-scoped to the repo** — they load only when this session's working directory IS the cloned repo. This session was started elsewhere, so it must be pointed at the clone with `/cd`. **You (Claude) cannot run `/cd` yourself — it is user-invoked only by design.** So instruct the user to run it; do not claim the commands are available yet.

Tell the user (substitute `{target}` with the actual clone path):

> "You're all set, {displayName}! 🎉
>
> - Repo: `{target}`
> - Your folder: `src/users/{username}/`
> - Dev server: [http://localhost:5173](http://localhost:5173) (already running — no need to restart it)
>
> **One quick step to load the Canvas commands.** They live inside the repo, so point this session at it:
> 1. Run `/cd {target}`
> 2. Then run `/create-project` to start your first project.
>
> `/cd` keeps this same chat — no restart needed. If `/create-project` still doesn't appear right after, fully restart Claude Code from `{target}` and it'll load."

---

## Rules

- This command does NOT create a branch or commit — the user folder and `.gitkeep` are left unstaged. `/create-project` and `/deploy` handle branching/committing.
- Never commit directly to `main` — always a personal branch (`{username}/{project}`).
- Keep all messages friendly and clear — not everyone on the team uses git daily.
- If the repo is already cloned, defer to `/initiate` for the folder/identity setup instead of duplicating work.
