# Clone Marketplace — Backlog

Prioridad: P0 = imprescindible para lanzar · P1 = primera iteración · P2 = después.

## P0 — Lanzamiento

- [x] **MKT-1** Crear repo `github.com/ficiverson/open-clone-marketplace` y subir el contenido de `marketplace-kit/` (catalog/ en la raíz o en /docs, spec/, skill/).
- [x] **MKT-2** Activar GitHub Pages (Settings → Pages → Deploy from branch → main). Verificar que `https://ficiverson.github.io/open-clone-marketplace/` carga.
- [x] **MKT-3** Commit + push del `clonefest.json` en el repo `share` (rama main) para que la landing pueda leerlo.
- [x] **MKT-4** Colocar `clone-deployer.skill` en `catalog/skill/` del repo marketplace (la landing enlaza a esa ruta).
- [ ] **MKT-5** Prueba end-to-end: instalar la skill en Claude, pedir "despliega esta app: github.com/ficiverson/share" y llegar a una instancia funcionando en una cuenta Firebase limpia.

## P0.5 — Seguridad y confianza (antes de aceptar apps de terceros)

- [x] **MKT-18** Política de seguridad de manifests: clone-deployer ejecuta los comandos `shell` del manifest, así que todo manifest de terceros se revisa a mano antes del merge + CI que lintea comandos peligrosos (`curl|sh`, `sudo`, `rm -rf`, escritura fuera del repo, descargas de binarios).
- [x] **MKT-19** Pinear manifests: en `apps.json` referenciar commit SHA en vez de `main` (o cachear copia en el repo del catálogo) para que un manifest no pueda cambiar después de revisado. La actualización requiere nuevo PR.
- [x] **MKT-20** Guía legal/marcas en CONTRIBUTING: nombre propio distinto al original ("inspired by Split-style app", nunca "Split-style app clone oficial"), prohibido usar logos/assets/nombre de la app original, licencia OSI obligatoria, disclaimer en la landing de no afiliación.

## P1 — Robustez y contribución

- [x] **MKT-6** GitHub Action de validación: en cada PR que toque `apps.json`, descargar los manifests y validarlos con ajv contra `clonefest.schema.json`.
- [x] **MKT-7** `CONTRIBUTING.md` + plantilla `clonefest.template.json` con comentarios para autores de apps.
- [x] **MKT-8** Buscador y filtros en la landing (categoría, plataforma, stack).
- [x] **MKT-9** Sección "requisitos" visible en cada ficha (cuentas necesarias y coste) antes de que el usuario se lance.
- [ ] **MKT-10** Mejorar la skill con lo aprendido en MKT-5 (errores típicos de flutterfire/firebase login, detección de versión de Flutter).
- [x] **MKT-11** Añadir screenshots del clon de Share al manifest y a la ficha.
- [x] **MKT-21** Sección "Publica tu app" en la landing: enlaza la skill `clonefest-generator` (descargable como clone-deployer) + los 3 pasos de autor (generar manifest → validar → PR a apps.json).
- [x] **MKT-22** Colocar `clonefest-generator.skill` junto a `clone-deployer.skill` en `catalog/skill/` del repo marketplace.

## P2 — Crecimiento

- [ ] **MKT-12** Segunda app clónica (idealmente stack distinto, ej. Supabase o backend-less) para validar que la spec generaliza.
- [ ] **MKT-13** Badge "verified": Action programada que despliega cada app en un proyecto de prueba y marca las que funcionan.
- [x] **MKT-14** Cache de manifests: Action nocturna que copia los manifests al repo del catálogo (evita rate-limit de raw.githubusercontent.com).
- [ ] **MKT-15** Dominio propio (CNAME) y analytics ligeras (Plausible/GoatCounter).
- [ ] **MKT-16** Spec v1.1: soporte multi-backend, variables de entorno declarativas, hooks post-deploy.
- [x] **MKT-17** Ficha de detalle por app (modal estilo App Store) con enlace directo compartible `#app/<id>`. README embebido: descartado por ahora.
- [ ] **MKT-23** Flujo de actualización de instancias desplegadas: campo `changelog`/`min_redeploy_steps` en spec v1.1 y soporte en clone-deployer para "actualiza mi app" (git pull + rebuild + redeploy).
- [x] **MKT-24** Licencia y README del propio repo open-clone-marketplace. (HECHO)

## Definition of done (por app publicada)

`clonefest.json` válido contra el schema, en la raíz del repo, sin secretos; README con pasos que coinciden con el manifest; despliegue verificado por alguien distinto del autor; entrada en `apps.json` mergeada con CI en verde.
