---
sidebar_position: 8
---

# Despliegue en GitHub Pages

## Preparación

En tu copia del proyecto:

1. Abre **Settings → Pages → Build and deployment → Source** y selecciona **GitHub Actions**.
2. En `docusaurus.config.ts`, ajusta `url` a `https://TU-USUARIO.github.io` y `baseUrl` a `/NOMBRE-REPO/` para un sitio de proyecto.
3. Ajusta `organizationName` y `projectName`. Usa el nombre real de tu repositorio, respetando mayúsculas y minúsculas.

GitHub Pages sirve archivos estáticos; no ejecuta una API Node.js ni una base de datos.

## Workflow completo

Reemplaza `.github/workflows/deploy.yml` en tu copia con lo siguiente. No crees un segundo workflow de publicación para el mismo sitio.

```yaml
name: Publicar sitio
on:
  push:
    branches: [main]
  workflow_dispatch:
permissions:
  contents: read
concurrency:
  group: pages
  cancel-in-progress: false
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '22'
      - run: npm ci
      - run: npm run typecheck
      - run: node --test
      - run: npm run build
      - name: Guardar sitio para publicar
        uses: actions/upload-pages-artifact@v3
        with:
          path: build
  deploy:
    needs: build
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Publicar
        id: deployment
        uses: actions/deploy-pages@v4
```

Conserva la prueba del tema 6. `needs: build` impide publicar si falla la validación. El **artefacto** guarda el directorio `build/` para que el job de publicación pueda usarlo. Los permisos de escritura se limitan a `deploy`; el filtro de rama también impide publicar desde otra rama al ejecutar manualmente.

El workflow de CI valida PRs. Este workflow vuelve a validar el commit de `main` que realmente publicará.

## Desafío

1. Cambia una frase visible del sitio mediante un PR.
2. Comprueba CI e integra el cambio en `main`.
3. Espera el resultado de **Publicar sitio** y abre la URL del deployment.
4. Verifica que la frase nueva y la navegación funcionen.

**Entrega:** URL pública, enlace al workflow exitoso y una captura del cambio publicado.

[Referencia: workflows personalizados de GitHub Pages](https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages).
