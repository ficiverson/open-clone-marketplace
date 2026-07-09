---
name: clonefest-generator
description: >
  Generates a clonefest.json manifest for the Clone Marketplace by inspecting a
  repository (GitHub URL or local folder). Use whenever the user wants to publish
  an app to the clone marketplace, says "genera el clonefest", "create a manifest
  for this repo", "make this app marketplace-ready", or shares a repo asking to
  prepare it for the catalog. Also use to refresh an outdated clonefest.json.
---

# Clonefest Generator

Produce a valid `clonefest.json` at the root of an app repo so it can be listed in the Clone Marketplace and deployed by the `clone-deployer` skill. The manifest has two consumers with different needs: the catalog renders marketing-ish metadata (name, description, owner, dates), and the deployer executes `deploy.steps` literally — so every fact you write must be **extracted from the repo, not invented**. A wrong deploy step strands a non-technical user halfway through a deployment.

The full schema is in `references/clonefest.schema.json`. Read it before writing the manifest.

## Workflow

### 1. Get the repo

- GitHub URL → shallow clone with full history for dates: `git clone --filter=blob:none <url> <tmp-dir>`.
- Local folder → work in place. If it's not a git repo, ask the user for publication date and owner instead of inferring them.

### 2. Extract facts (in this order, cheapest first)

**Identity & ownership**

| Field | Source |
|---|---|
| `repo` | `git remote get-url origin` (normalize to https URL) |
| `owner.github` | owner segment of the remote URL |
| `owner.name`, `owner.email` | `git log --format='%an %ae'` — most frequent author |
| `published_at` | first commit: `git log --reverse --format=%ad --date=short \| head -1` |
| `updated_at` | last commit date |
| `license` | LICENSE file first line / SPDX id |
| `version` | pubspec.yaml `version`, package.json `version`, or tag |
| `icon` | icon.png / assets/icon* at root, if present |

**Stack detection** — check for marker files:

- `pubspec.yaml` → Flutter (frontend.framework=flutter, language=dart; min_version from `environment.sdk` or README)
- `package.json` → inspect dependencies: react/next/vue/express…
- `firebase.json` → backend.provider=firebase; services from its keys (`hosting`, `firestore`, `functions`) plus dependencies (firebase_auth → auth, cloud_firestore → firestore)
- `supabase/` or `@supabase/*` dep → supabase
- Platforms: presence of `web/`, `android/`, `ios/`, `macos/`, `windows/`, `linux/` dirs (Flutter), or infer from framework

Auth providers: look for dependency names (google_sign_in, sign_in_with_apple) and README mentions of enabled providers. Parse real dependency entries, not comments — a naive grep of pubspec.yaml/package.json will match packages that are only mentioned in comments.

Platform dirs can overstate support (Flutter scaffolds all six): trust the README's stated platforms over directory presence when they disagree.

**Requirements & deploy steps — the README is the primary source.** A well-maintained README's setup section is closer to the truth than any convention. Read it fully. Map its prerequisite table to `requirements.tools` (name, min_version, install URL/command) and its numbered setup/deploy sections to `deploy.steps`:

- Commands in code blocks → `type: shell` steps with the exact command in `run`.
- Console/dashboard actions (Firebase Console, app stores) → `type: manual` with `url` and a `doc` anchor pointing to the README section (e.g. `README.md#6-firestore-security-rules`).
- Local-run / emulator steps → mark `optional: true`.
- Keep the README's order. If the README has no deploy docs, derive minimal conventional steps for the detected stack and tell the user the repo's docs should be improved.

### 3. Fill the gaps with the user, don't invent

Ask (one short round) for anything not extractable: `clone_of` (which famous app is this a clone of — guess from the README and confirm), `category`, missing `owner.email`, `deploy.estimated_minutes` if the README gives no hint. Write `tagline` and `description` yourself in **both English and Spanish** based on the README, and show them for approval along with the rest.

### 4. Write and validate

- Write `clonefest.json` at the repo root. `id` = repo name, lowercase kebab-case, unique in the catalog.
- All user-facing text as i18n objects `{"en": ..., "es": ...}` — `en` is mandatory.
- **Never copy the author's own instance identifiers into the manifest**: no Firebase project IDs, app IDs, API keys, or `google-services.json` values. The whole point is that each deployer creates their own backend.
- Validate:

```bash
npm i -g ajv-cli ajv-formats   # once
ajv validate -s references/clonefest.schema.json -d clonefest.json --spec=draft2020 -c ajv-formats
```

Fix until valid. Then sanity-check the deploy steps yourself: could someone with zero context run them top-to-bottom? Every `manual` step must say exactly where to click; every `shell` step must work from the repo root after the previous steps.

### 5. Hand over

Show the user the generated manifest, note anything you inferred with low confidence, and remind them of the publish step: PR adding `{"id": ..., "manifest": "https://raw.githubusercontent.com/<owner>/<repo>/main/clonefest.json"}` to `apps.json` (repo root) in the open-clone-marketplace repo.

## Example output

See a complete real manifest at https://raw.githubusercontent.com/ficiverson/share/main/clonefest.json (Flutter + Firebase Split-style app clone).
