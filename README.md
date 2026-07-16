# Open Clone Marketplace

**English** · [Español](#español)

A catalog of open-source clone apps (Split-style app-style, Plan-style…) that anyone can deploy on their own account — your data, your instance, no vendor.

**Live catalog:** https://ficiverson.github.io/open-clone-marketplace/

## How it works

- Each app repo has a `clonefest.json` manifest at its root describing owner, stack, requirements and deploy steps ([spec](spec/SPEC.md)).
- This repo hosts the landing page (GitHub Pages), the app index (`apps.json`) and two Claude skills:
  - [`clone-deployer`](skill/clone-deployer.skill) — for **users**: reads a manifest and walks you through deployment, no coding needed.
  - [`clonefest-generator`](skill/clonefest-generator.skill) — for **authors**: inspects your repo and generates the manifest.

## Deploy an app (no coding needed)

1. Download and install the [`clone-deployer`](skill/clone-deployer.skill) skill in Claude.
2. Pick an app in the [catalog](https://ficiverson.github.io/open-clone-marketplace/) and copy its repo link.
3. Ask Claude: *"deploy this app: \<link\>"*.

## Publish your app

See [CONTRIBUTING.md](CONTRIBUTING.md). Short version: generate a `clonefest.json` with the [`clonefest-generator`](skill/clonefest-generator.skill) skill, then open a PR adding your manifest URL to `apps.json`. CI validates it; a maintainer reviews the deploy steps before merge.

## Disclaimer

Apps listed here are independent open-source projects *inspired by* well-known apps. They are not affiliated with, endorsed by, or connected to the original products or their trademark owners.

---

## Español

Catálogo de apps clónicas open source (estilo Split-style app, estilo Plan…) que cualquiera puede desplegar en su propia cuenta: tus datos, tu instancia, sin proveedor.

**Catálogo:** https://ficiverson.github.io/open-clone-marketplace/

Para **desplegar** una app sin saber programar: instala la skill [`clone-deployer`](skill/clone-deployer.skill) en Claude, copia el enlace del repo de la app y pídele *"despliega esta app: \<enlace\>"*.

Para **publicar** tu app: genera el `clonefest.json` con la skill [`clonefest-generator`](skill/clonefest-generator.skill) y abre un PR añadiendo tu manifest a `apps.json` (ver [CONTRIBUTING.md](CONTRIBUTING.md)).

Las apps listadas son proyectos independientes *inspirados en* apps conocidas, sin afiliación con los productos originales ni sus marcas.
