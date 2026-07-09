---
name: clone-deployer
description: >
  Deploys any app from the Clone Marketplace for a non-technical user.
  Triggers when the user pastes a GitHub repo URL from the marketplace, mentions
  "deploy this clone", "instalar app del catálogo", "clone marketplace", or shares
  a clonefest.json. Reads the repo's clonefest.json manifest and walks the user
  through tools, accounts, and every deploy step until the app is live.
---

# Clone Deployer

You are deploying a self-hosted clone app for a user who may have **zero technical background**. The single source of truth is the `clonefest.json` manifest at the root of the app's repo.

## Workflow

### 1. Get the manifest

- If the user gave a GitHub repo URL, fetch `https://raw.githubusercontent.com/<owner>/<repo>/<default-branch>/clonefest.json` (try `main`, then `master`).
- If there is no `clonefest.json`, stop and tell the user this app isn't marketplace-ready; point them to the repo README instead.
- Validate it minimally: it must have `deploy.steps`. Detect the user's language and use the `es` texts if they write in Spanish, otherwise `en`.

### 2. Brief the user (before touching anything)

Summarize in plain language: what the app is (`name`, `clone_of`, `description`), what they'll end up with (`deploy.verify`), estimated time (`deploy.estimated_minutes`), and what it costs (`requirements.accounts[].cost`). Ask for confirmation to proceed.

### 3. Check requirements

For each entry in `requirements.tools`:

- Check if it's installed (`<tool> --version` or equivalent) and meets `min_version`.
- If missing: install it yourself when the `install` field is a command; when it's a URL, give the user the link and exact instructions, then wait and re-check.

For each `requirements.accounts`: ask the user if they have one; if not, guide them to create it at the given `url`. Never ask the user to share passwords with you.

If the user's OS can't satisfy `requirements.platform_extras` for a platform (e.g. iOS without a Mac), tell them early and continue with the platforms that work.

### 4. Execute deploy.steps in order

For each step in `deploy.steps`:

- **`type: shell`** — explain in one sentence what the command does, then run it (`run` field) in the cloned repo directory. If it fails, read the error, fix the obvious (missing dep, not logged in, wrong dir) and retry; only surface the raw error if you're stuck, and translate it to plain language.
- **`type: manual`** — the user must do this themselves (usually in a web console). Give numbered, click-by-click instructions using `title`, `url`, and the doc anchor in `doc` (fetch the repo doc for details). Wait for the user to confirm before continuing.
- Skip steps marked `optional: true` unless the user wants them; say you're skipping and why.
- Interactive CLIs (e.g. `firebase login`, `flutterfire configure`) may need the user's input or a browser login — warn them what prompts to expect and what to choose.

### 5. Verify and hand over

- Run/explain `deploy.verify`. Make sure the user actually sees their app working.
- Give them: the live URL, where their data lives (their own backend project — nobody else can see it), the repo's `support.issues` link, and how to redeploy after pulling updates (re-run the build + deploy steps).

## Rules

- One step at a time; never dump the whole plan as a wall of commands.
- Plain language always: say "your app's address on the internet", not "hosting endpoint". No jargon without a one-line explanation.
- Never put secrets, tokens, or passwords in files or chat.
- The user's instance is theirs: always `flutterfire configure`-style reconfiguration must point at THEIR backend project, never the author's. If a config file still references the author's project ID after configuration, regenerate it.
- If the manifest and the repo README conflict, trust the README (it's maintained closer to the code) and note the mismatch.
