# Clonefest spec v1.0

**English** · [Español](#español)

A `clonefest.json` at the root of a repo makes an app publishable in the Clone Marketplace. It serves two consumers:

1. **The catalog** (GitHub Pages) reads it to render the app card: name, clone_of, description, owner, platforms, published date, repo link.
2. **The clone-deployer skill** reads it to walk a non-technical user through deployment: required tools/accounts, then ordered `deploy.steps` (`shell` steps the skill runs, `manual` steps it explains and waits for).

## Rules

- File name is exactly `clonefest.json`, at repo root, valid against `clonefest.schema.json`.
- `id` must be unique across the catalog (it's the key in `apps.json`).
- All user-facing text uses i18n objects: `{"en": "...", "es": "..."}`. English is mandatory.
- `deploy.steps` must be executable top-to-bottom by someone with zero prior context. If a step needs the Firebase/AWS/etc. console, mark it `manual` and give a `url` and/or `doc` anchor.
- `deploy.verify` tells the user how to confirm the deployment worked.
- Never include secrets, API keys, or project IDs of the author's own instance in the manifest.

## Validation

```bash
npm i -g ajv-cli ajv-formats
ajv validate -s clonefest.schema.json -d clonefest.json --spec=draft2020 -c ajv-formats
```

## Publishing to the catalog

Open a PR against the `open-clone-marketplace` repo adding your entry to `apps.json`:

```json
{ "id": "share", "manifest": "https://raw.githubusercontent.com/ficiverson/share/main/clonefest.json" }
```

CI validates the manifest against the schema before merge.

---

## Español

Un `clonefest.json` en la raíz del repo hace que la app se pueda publicar en el Clone Marketplace. Tiene dos consumidores: el **catálogo** (GitHub Pages), que lo lee para pintar la ficha de la app, y la **skill clone-deployer**, que lo lee para guiar el despliegue a un usuario no técnico (los pasos `shell` los ejecuta la skill; los `manual` se explican y se espera al usuario).

Reglas: nombre exacto `clonefest.json` en la raíz, válido contra el schema; `id` único en el catálogo; textos con objetos i18n (`en` obligatorio, `es` opcional); los `deploy.steps` deben poder ejecutarse en orden sin contexto previo; nunca incluyas secretos ni IDs de tu propia instancia.

Para publicar: PR al repo `open-clone-marketplace` añadiendo tu entrada en `apps.json`. La CI valida el manifest antes del merge.
