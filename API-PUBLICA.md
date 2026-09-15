# API pública — catálogo + landing

Este documento es para el desarrollador que en algún momento arme una landing
propia para este negocio (o para cualquier otro negocio dado de alta en Edgy
Gestión). No hace falta acceso al repo de gestión ni ningún login: es un
endpoint público de solo lectura, pensado exactamente para este caso.

Con un solo llamado se obtiene: los datos del negocio (nombre, logo, color,
horario), el catálogo completo (categorías, productos con precio real e
imagen, combos), y lo que esté cargado en el panel "Landing" (foto de
portada, contraste, promo activa).

## Endpoint

```
POST https://ipnufyqwbjbocsezdkiw.supabase.co/rest/v1/rpc/conector_landing
```

### Headers

| Header | Valor |
|---|---|
| `Content-Type` | `application/json` |
| `Content-Profile` | `edgy_gestion` |
| `apikey` | `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImlwbnVmeXF3Ympib2NzZXpka2l3Iiwicm9sZSI6ImFub24iLCJpYXQiOjE3NzIyODg2ODgsImV4cCI6MjA4Nzg2NDY4OH0.MOqvcNbA4AbGxJgrag2tOj_rH7cOJTWU2k8qARYVhT8` |
| `Authorization` | `Bearer <mismo valor que apikey>` |

Esta `apikey` es la clave pública ("anon") del proyecto -- está pensada para
usarse desde el navegador de cualquiera, sin login. No da acceso a nada
privado: `conector_landing` solo puede leer, y solo devuelve los campos que
ves más abajo (nunca datos internos como costos, stock, CUIT, teléfonos de
clientes, etc.).

`Content-Profile: edgy_gestion` es obligatorio -- sin ese header, Supabase
busca la función en el esquema por defecto (`public`) y devuelve 404. Es el
único detalle "no obvio" de este endpoint.

### Body

```json
{ "p_slug": "la-charcuteria-express" }
```

`p_slug` es el identificador público del negocio (no cambia, es estable).
Si el negocio tiene más de un local con link propio, se puede pasar también
`p_punto_venta_slug` para traer el catálogo de ESE local en particular.

## Ejemplo (JavaScript, listo para copiar)

```js
async function traerCatalogo(slug) {
  const res = await fetch(
    'https://ipnufyqwbjbocsezdkiw.supabase.co/rest/v1/rpc/conector_landing',
    {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Content-Profile': 'edgy_gestion',
        apikey: 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImlwbnVmeXF3Ympib2NzZXpka2l3Iiwicm9sZSI6ImFub24iLCJpYXQiOjE3NzIyODg2ODgsImV4cCI6MjA4Nzg2NDY4OH0.MOqvcNbA4AbGxJgrag2tOj_rH7cOJTWU2k8qARYVhT8',
        Authorization:
          'Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6ImlwbnVmeXF3Ympib2NzZXpka2l3Iiwicm9sZSI6ImFub24iLCJpYXQiOjE3NzIyODg2ODgsImV4cCI6MjA4Nzg2NDY4OH0.MOqvcNbA4AbGxJgrag2tOj_rH7cOJTWU2k8qARYVhT8',
      },
      body: JSON.stringify({ p_slug: slug }),
    },
  );
  if (!res.ok) return null;
  return res.json();
}

traerCatalogo('la-charcuteria-express').then(console.log);
```

## Forma de la respuesta

```jsonc
{
  "negocio": {
    "nombre": "La Charcutería",
    "slug": "la-charcuteria-express",
    "logoUrl": "https://.../logos-clientes/....png",
    "isotipoUrl": "https://.../logos-clientes/....png", // versión cuadrada, o null
    "colorMarca": "#fbe6d5",
    "horarioActivo": false,       // true si el negocio carga horario de atención
    "horarioApertura": "09:00:00",
    "horarioCierre": "23:00:00",
    "horarioDias": [0, 1, 2, 3, 4, 5, 6], // 0 = domingo
    "pagoOnlineHabilitado": true,
    "puntoVenta": null             // datos del local, si se pasó p_punto_venta_slug
  },
  "categorias": [
    {
      "id": "uuid",
      "nombre": "Fiambres Curados",
      "productos": [
        {
          "id": "uuid",
          "nombre": "Bondiola Salada",
          "descripcion": "",
          "precio": 17078.1,
          "imagen": "https://.../productos-imagenes/....jpg",
          "tipo": "unico",
          "unidadVenta": "unidad"
        }
      ]
    }
  ],
  "combos": [
    { "id": "uuid", "nombre": "...", "descripcion": "...", "precio": 0, "imagen": "...", "etiqueta": "..." }
  ],
  "landing": {
    // null si el negocio no tiene el módulo Landing activado o nunca
    // guardó nada todavía -- en ese caso, no mostrar nada de esta parte.
    "heroImagenUrl": "https://.../landing-imagenes/....jpg",
    "heroContraste": 100,
    "promoActiva": false,
    "promoTitulo": null,
    "promoTexto": null
  }
}
```

Notas:

- Las categorías vacías (sin productos disponibles) igual aparecen en la
  lista -- filtralas del lado del cliente si no las querés mostrar.
- Los precios ya vienen calculados (con la lista de precios que corresponda)
  -- no hace falta ningún cálculo adicional.
- Si `promoActiva` es `false`, `promoTitulo` y `promoTexto` vienen en `null`
  aunque el negocio los tenga cargados -- es intencional, así una landing
  externa nunca muestra una promo que el dueño desactivó.
- Esta API es de **solo lectura**. Para recibir pedidos desde una landing
  propia existe un endpoint aparte (`crear_orden_venta_publica`) -- si hace
  falta, se documenta en un momento aparte cuando haya un caso real.
