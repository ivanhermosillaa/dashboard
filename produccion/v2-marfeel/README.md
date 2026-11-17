# Producción editorial · Meganoticias, Mega y Megatiempo

Interfaz de producción editorial y audiencia. Datos de octubre de 2026.

## Estructura

```
produccion_web/
├── index.html              # interfaz (HTML, CSS y JavaScript en un archivo)
└── data/
    ├── produccion.json     # metadatos, portales y artículos (producción)
    ├── marfeel.json        # audiencia por URL (exportaciones de Marfeel)
    └── marfeel.example.json  # ejemplo de formato, no se carga
```

## Cómo publicar

Sube la carpeta completa al servidor, con `index.html` y `data/` en el mismo nivel. Para probar en local, sirve la carpeta con un servidor:

```bash
cd produccion_web
python3 -m http.server 8000
```

Luego abre `http://localhost:8000`. No funciona con doble clic, porque el navegador bloquea la lectura de JSON desde archivos locales.

## Secciones de la página

1. Filtro de sitios y periodo (mes, año o rango de fechas).
2. Comparativa de portales.
3. Tarjetas resumen.
4. Publicaciones por día y por sección.
5. Mapa de calor de publicación por hora.
6. Periodistas.
7. Audiencia · Marfeel (oculta; se activa con el botón "Audiencia Marfeel" junto al periodo).
8. Temas más utilizados.
9. Artículos.
10. Calidad de datos.

## Producción (`produccion.json`)

Contiene tres bloques: `meta` (versión, fecha de generación, zona horaria y meses), `portals` (id, nombre, color y dominio) y `articles` (portal, fecha y hora, sección, formato, título, autor, estado y tipo de contenido). Las métricas se calculan en el navegador a partir de `articles`.

Para actualizar, reemplaza el archivo con una nueva generación en el mismo formato.

## Audiencia (`marfeel.json`)

Cada fila es una URL de un portal con sus páginas vistas y usuarios únicos. Las filas no traen fecha: Marfeel entrega totales para el periodo indicado en `meta.period`.

```json
{
  "meta": {
    "source": "marfeel",
    "period": { "from": "2026-10-01", "to": "2026-10-31" },
    "scope": "Solo notas publicadas en octubre de 2026"
  },
  "rows": [
    { "portal": "meganoticias",
      "url": "https://www.meganoticias.cl/nacional/533238-nueva-moneda.html",
      "pageviews": 97382, "users": 84586 }
  ]
}
```

**Reglas de cruce.** Las URLs se normalizan antes de comparar: se quita el protocolo, el `www.`, los parámetros, el fragmento y la barra final, y se pasa a minúsculas.

**Métricas.** `users` es el usuario único por URL. No es sumable entre URLs: la suma de todas las URLs es mayor que el total de usuarios únicos del sitio. La tarjeta lo indica en su rótulo.

**Cobertura del periodo.** Si el periodo seleccionado queda fuera de `meta.period`, la tarjeta lo indica y no muestra cifras.

**Alcance.** `marfeel.json` contiene solo notas publicadas en octubre de 2026. Las URLs de Marfeel que no están en `produccion.json` (shorts, capítulos y otros formatos) se cuentan como "Sin registro en producción". Si un portal aparece en `meta.incomplete`, la tarjeta muestra un aviso de exportación incompleta.

## Métricas de Marfeel en la interfaz

Marfeel entrega totales por URL del mes. Se muestran en la comparativa de portales, en periodistas (con índice de audiencia), en artículos y en el mapa de calor (métricas de páginas vistas y usuarios por hora, más insights de horario).

Las cifras de Marfeel solo aparecen cuando el periodo seleccionado incluye todo el rango de `meta.period`. En periodos parciales, las columnas muestran "—" y la página lo explica.

## Tema

El botón "Modo oscuro", en la barra superior, cambia entre el tema claro y el oscuro. La preferencia se guarda en el navegador. Los colores están definidos como variables en `:root` y `html[data-theme=dark]`, así que se ajustan en un solo lugar.

## Informes

Se abren con el botón "Informes", junto a "Audiencia Marfeel". Usan el portal y el periodo seleccionados:

1. **Producción vs. audiencia por sección.** Compara el % de publicadas con el % de páginas vistas. Índice = % audiencia ÷ % producción.
2. **Patrocinados vs. editorial.** Publicadas, páginas vistas y promedio por nota, por portal.
3. **Concentración de audiencia.** Participación de las páginas vistas por tramo de notas y las diez notas con más tráfico.
4. **Notas sin audiencia.** Publicadas sin registro en Marfeel, por sección y periodista, con listado.
5. **Análisis de titulares.** Mediana de páginas vistas según longitud, número y pregunta en el titular.

Pendientes, porque requieren datos que aún no existen: vida útil de la nota y tendencia semanal (desglose diario de Marfeel), Score SEO vs. audiencia (módulo SEO del pipeline) y fuentes de tráfico (GA4).

## Calidad de datos

Al final de la página. Evalúa el sitio y el periodo seleccionados con once controles: títulos repetidos, fechas en URL, firmas, escapes, fechas futuras, tasa de no publicadas, días sin publicar y antigüedad de la carga. Cada control permite revisar los casos concretos.

## Requisitos

- Conexión a internet para Roboto (Google Fonts) y Chart.js (cdnjs).
- Navegador moderno.
