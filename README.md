# La Charcutería — Landing

Sitio institucional/vidriera de La Charcutería, más una página de catálogo
pensada para linkear desde la bio de Instagram. HTML/CSS estático, sin
build ni dependencias -- pensado para servirse directo con GitHub Pages
desde este mismo repo (un solo lugar para código y hosting).

## Estructura

```
index.html        página principal (institucional + vidriera)
catalogo.html      catálogo (link desde Instagram)
assets/css/        estilos
assets/img/        fotos y logos
```

## Ver en local

No hace falta ningún servidor: abrí `index.html` directo en el navegador.
(Si preferís servirlo por http, cualquier servidor estático simple sirve,
por ejemplo `npx serve .` o `python3 -m http.server`.)

## Publicar con GitHub Pages

1. Crear el repo en GitHub (público, para que Pages sea gratis) y subir
   este contenido:

   ```
   git init
   git add .
   git commit -m "Landing La Charcuteria: version inicial"
   git branch -M main
   git remote add origin <URL-del-repo-que-crees-en-GitHub>
   git push -u origin main
   ```

2. En GitHub: **Settings → Pages → Build and deployment → Source:
   Deploy from a branch → Branch: `main` / `(root)`** → Save.

3. A los pocos minutos el sitio queda publicado en
   `https://<tu-usuario>.github.io/<nombre-del-repo>/`
   (ese es el dominio de prueba de concepto -- el día que haya un dominio
   propio, se apunta un CNAME desde la misma pantalla de Settings → Pages,
   sin tocar el código).

## Pendientes conocidos

- Dirección física, horarios y redes sociales: no están cargados todavía
  (no hay dato real que mostrar) -- agregarlos en `index.html` (sección
  Contacto) y en el footer cuando existan.
- `catalogo.html` es un esqueleto con los 3 productos conocidos + picada a
  medida, todos con "Consultar precio" -- sumar precios/variedades reales
  a medida que estén definidos, copiando el mismo patrón de `.catalogo-card`.
- El número de WhatsApp usado en todos los botones es el del dueño
  (5492954367009, mismo que la instancia `charcuteria` del agente) --
  cambiarlo acá y en todos los `wa.me/...` si en algún momento se separa un
  número de atención al público.
