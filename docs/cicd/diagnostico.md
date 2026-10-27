---
sidebar_position: 9
---

# Diagnóstico de workflows (opcional)

## Método rápido

1. Abre **Actions**, selecciona la ejecución y el job fallido.
2. Busca el primer paso rojo y lee el mensaje del comando, no solo el resumen final.
3. Identifica si falló la instalación, la validación, la compilación o la publicación.
4. Reproduce el comando localmente cuando sea posible.
5. Corrige el archivo y sube un nuevo commit.

## Fallos habituales

- **Workflow inválido:** revisa indentación y estructura del YAML; puede fallar antes de iniciar un runner.
- **`npm ci` falla:** comprueba que el lockfile esté versionado y sincronizado con `package.json`.
- **Script inexistente:** consulta `scripts` en `package.json`; este curso usa `node --test`, no `npm test`.
- **Fallo de tipos o prueba:** lee el archivo y la línea reportados.
- **Pages devuelve 404 o pierde estilos:** revisa `url`, `baseUrl` y que se haya publicado `build/`.
- **Permiso denegado al publicar:** comprueba los permisos del job y que Pages use GitHub Actions.

**Re-run failed jobs** repite la ejecución sobre el mismo commit. Sirve ante fallos transitorios; un error en el código necesita un commit corregido.

## Desafío

En una rama, cambia temporalmente `npm run build` por `npm run buid` en `ci.yml`. Abre un PR, encuentra el mensaje que explica el fallo y corrige el comando.

**Entrega:** causa, corrección y enlaces a la ejecución fallida y la exitosa. No integres el error en `main`.
