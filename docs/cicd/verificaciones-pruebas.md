---
sidebar_position: 6
---

# Verificaciones y pruebas automáticas

## Qué comprueba cada paso

- **Tipos:** `npm run typecheck` detecta usos incompatibles en TypeScript.
- **Pruebas:** ejecutan ejemplos y comparan el resultado con lo esperado.
- **Build:** verifica que el sitio pueda compilarse, incluidos sus documentos.

Este repositorio todavía no define `npm test`. Para practicar sin instalar dependencias, crea `tests/curso.test.mjs` con el ejecutor integrado de Node.js:

```javascript
import test from 'node:test';
import assert from 'node:assert/strict';
import {readFileSync} from 'node:fs';

test('la bienvenida conserva el enlace a CI/CD', () => {
  const texto = readFileSync('docs/bienvenida.md', 'utf8');
  assert.match(texto, /\.\/cicd\/introduccion\.md/);
});
```

Esta prueba comprueba una regla editorial concreta. No sustituye las pruebas funcionales de una aplicación.

En `ci.yml`, después de instalar dependencias y antes de compilar, añade:

```yaml
      - name: Comprobar tipos
        run: npm run typecheck
      - name: Ejecutar pruebas
        run: node --test
```

Un comando que termina con código distinto de cero marca el paso como fallido; normalmente los pasos siguientes no se ejecutan. No uses `continue-on-error` para ocultar una validación obligatoria.

## Desafío

1. Añade la prueba y ejecuta `node --test` localmente.
2. En una rama, elimina temporalmente el enlace a la introducción de CI/CD de `docs/bienvenida.md`.
3. Abre un PR y comprueba que CI falla en la prueba.
4. Restaura el enlace y sube otro commit al mismo PR.

**Entrega:** una ejecución fallida y otra exitosa, con la causa explicada.

Para impedir que se integre un PR con CI fallido, configura el check como obligatorio en una regla de protección de `main`, si tu repositorio lo permite. Ejecutar CI por sí solo no bloquea el botón de merge.
