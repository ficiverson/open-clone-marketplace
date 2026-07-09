# Clone Marketplace — Plan de implementación

## Arquitectura

Tres piezas, tres repos (o repo + repos de apps):

1. **Repos de apps** (ej. `ficiverson/share`): cada uno lleva un `clonefest.json` en la raíz. Es la única fuente de verdad de la app: stack, dueño, fecha de publicación, requisitos y pasos de despliegue.
2. **Repo `open-clone-marketplace`**: contiene la landing (GitHub Pages), el índice `apps.json` (lista de URLs a los manifests), la spec + JSON Schema, y la skill descargable.
3. **Skill `clone-deployer`** (genérica, una para todas las apps): el usuario la instala en Claude, pega el link del repo, y la skill lee el `clonefest.json` y le guía/ejecuta el despliegue paso a paso.

Flujo del usuario no técnico: landing → elige app → descarga skill (una vez) → "despliega esta app: <link>" en Claude → app online en su propia cuenta de Firebase.

Flujo del autor de una app: escribe `clonefest.json` → valida contra el schema → PR a `apps.json` → CI valida → merge → aparece en el catálogo. La landing lee los manifests en runtime desde raw.githubusercontent.com, así que las fichas se actualizan solas sin tocar el catálogo.

## Fases

**Fase 1 — Fundación (esta semana).** Ya generado en `marketplace-kit/`: spec + schema, manifest real del clon de Splitwise, skill clone-deployer, landing bilingüe. Falta: crear repo `open-clone-marketplace`, subir contenido, activar GitHub Pages (Settings → Pages → branch main, carpeta `/` o `/docs`), commit del `clonefest.json` en el repo share.

**Fase 2 — Validación end-to-end.** Probar el flujo completo con un usuario real no técnico: instalar la skill, desplegar Share en una cuenta Firebase limpia, medir dónde se atasca. Ajustar manifest y skill con lo aprendido. Añadir CI (GitHub Action con ajv) que valide manifests en PRs.

**Fase 3 — Crecimiento.** Segunda y tercera app clónica para validar que la spec generaliza (ideal: una con backend distinto, ej. Supabase). Guía de contribución + plantilla de manifest. Buscador y filtros por categoría/stack en la landing.

**Fase 4 — Calidad.** Screenshots en fichas, badge "verified" (CI desplegó la app con éxito), versionado de la spec (clonefest 1.1), analytics de la landing.

## Decisiones tomadas

- Manifest en cada repo + skill genérica (escala: N apps, 1 skill).
- Landing bilingüe ES/EN; manifests y skill en inglés con textos i18n.
- GitHub Pages estático sin build: la landing hace fetch de los manifests en el navegador. Cero infraestructura, cero coste.

## Riesgos

- **Paso manual de Firebase Console**: es el punto de fricción para no técnicos. Mitigación: la skill da instrucciones click-a-click y el manifest enlaza a la sección exacta del README.
- **Manifests desactualizados vs. código**: mitigación: la skill confía en el README ante conflicto; CI puede comprobar que los comandos del manifest existen.
- **CORS/rate-limit de raw.githubusercontent.com**: funciona sin auth para tráfico normal; si crece, cachear manifests en el build del catálogo con una Action programada.
