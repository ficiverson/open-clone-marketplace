# Publishing an app to the Open Clone Marketplace

## Requirements for listed apps

- Open-source repo on GitHub with an OSI-approved license (MIT, Apache-2.0, GPL…).
- A `clonefest.json` at the repo root, valid against [`spec/clonefest.schema.json`](spec/clonefest.schema.json). See the [spec](spec/SPEC.md).
- A README whose setup/deploy instructions match the manifest's `deploy.steps`.
- Self-contained deployments: each user deploys to **their own** backend account (Firebase, Supabase…). Never ship your own project IDs, API keys or config files pointing at your instance.

## Trademarks and naming

Your app must be an independent project *inspired by* the original — not pretend to be it. Use your own name and icon ("Share", not "Splitwise 2"), never the original's logo or assets, and describe it as "Splitwise-style" / "inspired by Splitwise". Apps violating this are removed.

## Steps

1. Install the [`clonefest-generator`](skill/clonefest-generator.skill) skill in Claude and ask it to generate the manifest for your repo, or copy [`clonefest.template.json`](clonefest.template.json) and fill it by hand.
2. Validate locally:

   ```bash
   npm i -g ajv-cli ajv-formats
   ajv validate -s spec/clonefest.schema.json -d clonefest.json --spec=draft2020 -c ajv-formats
   ```

3. Commit `clonefest.json` to your repo's default branch.
4. Open a PR against this repo adding your entry to `apps.json`, **pinned to a commit SHA** so the reviewed manifest can't change afterwards:

   ```json
   { "id": "your-app-id", "manifest": "https://raw.githubusercontent.com/<owner>/<repo>/<commit-sha>/clonefest.json" }
   ```

   To update your manifest later, open a new PR bumping the SHA.

## Review

CI validates your manifest against the schema and lints `deploy.steps` for dangerous commands. A maintainer then reviews the steps by hand — the `clone-deployer` skill executes them on users' machines, so we reject anything with piped downloads (`curl … | sh`), `sudo`, writes outside the repo directory, or opaque binaries. Keep steps minimal and auditable.
