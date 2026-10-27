---
sidebar_position: 10
---

# Optimización y control (opcional)

## Caché de dependencias

En el paso `setup-node` de `ci.yml`, añade `cache`:

```yaml
      - uses: actions/setup-node@v4
        with:
          node-version: '22'
          cache: npm
```

La action usa el lockfile para identificar la caché de npm. Almacena descargas, no `node_modules`; conserva `npm ci`. La primera ejecución puede no encontrar caché y las siguientes pueden reutilizarla. Un acierto no garantiza menor duración total: también influyen el runner y los demás pasos.

Una caché acelera trabajo repetible; el artefacto del tema anterior conserva el resultado que se publicará.

## Ambientes y aprobaciones

Un **environment** agrupa reglas y configuración de un destino. El job `deploy` ya referencia `github-pages`.

En **Settings → Environments → github-pages**, configura **Required reviewers** si está disponible. Cuando llegue a ese job, el workflow esperará una aprobación antes de publicar. Eso introduce una decisión humana en la entrega.

La disponibilidad depende del plan y la visibilidad del repositorio. En planes Free, Pro y Team, los revisores obligatorios están disponibles para repositorios públicos. Si activas **Prevent self-review**, necesitarás otra persona para aprobar tu propia ejecución.

## Desafío

1. Añade la caché a CI y ejecuta dos veces con el mismo lockfile.
2. Compara el resultado de restauración de caché y la duración del paso de instalación.
3. Como extensión, configura un revisor en `github-pages`, publica un cambio y observa la espera antes de aprobarlo. Si la opción no está disponible, explica dónde se detendría el flujo.

**Entrega:** comparación breve de ambas ejecuciones y, si realizaste la extensión, evidencia de la aprobación.

Referencias: [caché con setup-node](https://github.com/actions/setup-node) y [administración de ambientes](https://docs.github.com/en/actions/how-tos/deploy/configure-and-manage-deployments/manage-environments).
