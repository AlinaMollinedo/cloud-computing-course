# Cloud Computing Course - Documentación

Sitio de documentación del curso de Computación en la Nube, construido con [Docusaurus](https://docusaurus.io/).

## Instalación

```bash
npm install
```

## Desarrollo local

```bash
npm run start
```

Abre http://localhost:3000. Los cambios se recargan automáticamente.

## Build

```bash
npm run build
```

Genera el sitio estático en la carpeta `build/`.

## Despliegue en GitHub Pages

El proyecto está configurado para publicarse en **GitHub Pages** mediante **GitHub Actions**.

### Pasos para activar GitHub Pages

1. **Sube el repositorio a GitHub** (si aún no lo has hecho):
   ```bash
   git remote add origin https://github.com/JuanJoseMirandaM/cloud-computing-course.git
   git push -u origin main
   ```
   Si tu rama principal se llama `master`, el workflow también se ejecuta en ese push.

2. **Activa GitHub Pages en el repositorio:**
   - Ve a **Settings** → **Pages**.
   - En **Build and deployment**, en **Source** elige **GitHub Actions**.

3. **Cada vez que hagas push a `main` (o `master`)**, el workflow construirá el sitio y lo desplegará. La documentación quedará en:
   - **https://juanjosemirandam.github.io/cloud-computing-course/**

### Si usas otro usuario u otro repositorio

Edita en `docusaurus.config.ts`:

- `url`: `https://TU-USUARIO.github.io`
- `baseUrl`: `'/NOMBRE-REPO/'`
- `organizationName`: tu usuario de GitHub
- `projectName`: nombre del repositorio

Así la URL final será `https://TU-USUARIO.github.io/NOMBRE-REPO/`.


Cambio random al README para practicar.