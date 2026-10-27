---
sidebar_position: 3
---

# Mi primer workflow

## YAML esencial

YAML representa claves, valores y listas. Usa espacios, no tabulaciones, y conserva la indentación. `uses` ejecuta una action; `run` ejecuta comandos. `with` entrega parámetros a una action.

## Archivo completo

Crea `.github/workflows/hola.yml`:

```yaml
name: Hola Actions
on: [push]
permissions:
  contents: read
jobs:
  saludar:
    runs-on: ubuntu-latest
    steps:
      - name: Preparar Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '22'
      - name: Saludar
        run: echo "Hola desde GitHub Actions"
      - name: Consultar versión
        run: node --version
```

Aquí no necesitamos `checkout` porque no leemos archivos del repositorio. La versión de una action (`@v4`) es distinta de la versión de Node.js (`22`).

Guarda, crea un commit y haz push a tu repositorio. Abre **Actions → Hola Actions → ejecución → saludar** y despliega cada paso.

## Desafío

1. Crea el archivo y personaliza el saludo con el nombre de tu proyecto.
2. Sube el cambio.
3. Comprueba que la versión mostrada empieza con `v22`.

**Entrega:** enlace a la ejecución exitosa y al archivo YAML.
