---
sidebar_position: 7
---

# Variables, secretos y permisos

## Tres conceptos distintos

- `env`: variables de entorno definidas para un workflow, job o paso.
- `vars`: configuración guardada en GitHub, sin carácter secreto.
- `secrets`: valores sensibles almacenados en GitHub; evita imprimirlos.

`permissions` limita lo que puede hacer el `GITHUB_TOKEN` que GitHub proporciona a la ejecución. No necesitas crear un token personal para los ejemplos de este módulo.

## Ejemplo completo

En **Settings → Secrets and variables → Actions**, crea una variable `NOMBRE_CURSO` y un secreto `TOKEN_DEMO` con un valor ficticio. Crea `.github/workflows/configuracion.yml` e intégralo en la rama predeterminada para ejecutarlo manualmente:

```yaml
name: Configuración
on:
  workflow_dispatch:
permissions:
  contents: read
jobs:
  comprobar:
    runs-on: ubuntu-latest
    env:
      AMBIENTE: practica
    steps:
      - name: Leer configuración
        env:
          CURSO: ${{ vars.NOMBRE_CURSO }}
        run: echo "Curso=$CURSO; ambiente=$AMBIENTE"
      - name: Comprobar secreto sin mostrarlo
        env:
          TOKEN_DEMO: ${{ secrets.TOKEN_DEMO }}
        run: |
          if [ -z "$TOKEN_DEMO" ]; then
            echo "Falta TOKEN_DEMO"
            exit 1
          fi
          echo "Secreto configurado"
```

Pasamos los valores al shell mediante `env` y usamos variables entre comillas. Los secretos normalmente no se entregan a workflows de PR provenientes de forks; esta práctica usa ejecución manual en tu propio repositorio.

## Desafío

Ejecuta el workflow con la variable y el secreto configurados. Elimina el secreto ficticio y repite para observar el fallo controlado; luego restáuralo.

**Entrega:** el YAML y los estados de ambas ejecuciones, sin mostrar el valor del secreto.

[Referencia: secretos en GitHub Actions](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-secrets).
