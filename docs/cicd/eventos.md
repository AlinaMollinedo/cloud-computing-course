---
sidebar_position: 4
---

# Eventos que activan workflows

## Tres eventos suficientes

- `push`: cuando se suben commits a las ramas seleccionadas.
- `pull_request`: al abrir, reabrir o actualizar un PR, entre otras actividades configurables.
- `workflow_dispatch`: ejecución manual desde Actions.

Reemplaza el bloque `on` de `hola.yml` por:

```yaml
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  workflow_dispatch:
```

En `pull_request`, `branches` filtra la rama **destino**, no la rama del autor. Un push a `practica-ci` sin PR abierto no ejecuta este ejemplo. Al abrir un PR hacia `main`, sí se ejecuta.

Para usar el botón **Run workflow**, el archivo con `workflow_dispatch` debe existir en la rama predeterminada. Una vez allí, puedes seleccionar una rama para la ejecución manual.

## Desafío

1. Integra este cambio en `main` de tu copia.
2. Crea una rama `practica-eventos`, cambia una frase del README y abre un PR hacia `main`.
3. Comprueba la ejecución asociada al PR.
4. Ejecuta también el workflow manualmente desde Actions.

**Entrega:** enlaces a las ejecuciones e identificación del evento que disparó cada una.

[Referencia: eventos de GitHub Actions](https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows).
