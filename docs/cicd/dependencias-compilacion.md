---
sidebar_position: 5
---

# Dependencias y compilación

## Secuencia de CI

Primero descargamos el repositorio, luego preparamos Node.js, instalamos dependencias y compilamos. `npm ci` usa el lockfile y falla si no coincide con `package.json`; por eso ambos archivos deben estar versionados.

Crea `.github/workflows/ci.yml`:

```yaml
name: CI
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  workflow_dispatch:
permissions:
  contents: read
jobs:
  validar:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '22'
      - name: Instalar dependencias
        run: npm ci
      - name: Compilar sitio
        run: npm run build
```

Docusaurus genera `build/`. Compilar todavía **no publica**: los archivos quedan en el runner y se perderán al terminar si no se guardan o despliegan.

## Desafío

1. Ejecuta `npm ci` y `npm run build` localmente.
2. Añade el workflow en una rama y abre un PR.
3. Revisa el paso de compilación en Actions.

**Entrega:** enlace al PR con CI exitoso y una explicación de por qué se usa `npm ci`.

[Referencia: compilar y probar Node.js](https://docs.github.com/en/actions/tutorials/build-and-test-code/nodejs).
