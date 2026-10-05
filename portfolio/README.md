# Portafolio

Sitio personal de Christian Nuñez en un único archivo: `index.html` (HTML, CSS y JavaScript sin dependencias, salvo las fuentes de Google Fonts).

## Ver localmente

Abre `index.html` en el navegador, o sirve la carpeta:

```bash
python3 -m http.server 8000 --directory portfolio
```

y visita <http://localhost:8000>.

## Publicar con GitHub Pages

1. En GitHub: **Settings → Pages**.
2. En **Source**, elige **GitHub Actions** y usa el flujo *Static HTML* apuntando a la carpeta `portfolio/`,
   o mueve `index.html` a un repositorio propio (por ejemplo `CFDev-Bridge.github.io`).

## Personalizar

- **Proyectos:** duplica el bloque `<article class="project">` de la sección `#proyectos`.
- **Contacto:** en la sección `#contacto` hay un comentario con un ejemplo para agregar LinkedIn o correo.
- **Colores y fuentes:** están definidos como variables en el bloque `:root` al inicio del `<style>` (incluye modo oscuro).
