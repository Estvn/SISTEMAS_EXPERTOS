
- https://docs.astro.build/es/reference/directives-reference/#directivas-del-cliente
- https://docs.astro.build/es/guides/server-islands/

- Las publicaciones se realizan con los archivos Markdown (.md o .mdx)

> [!WARNING]
>-  https://www.markdownguide.org/cheat-sheet/
>- https://assemble.io/docs/YAML-front-matter.html

- https://developer.mozilla.org/es/docs/Learn_web_development/Howto/Tools_and_setup/What_are_browser_developer_tools

- **Secciones Javascript en Astro**
- https://docs.astro.build/es/reference/astro-syntax/#expresiones-similares-a-jsx

# Link para Investigar sobre Estilos en Astro

- https://docs.astro.build/es/reference/astro-syntax/#diferencias-entre-astro-y-jsx
- https://docs.astro.build/es/guides/styling/#estilando-en-astro
- https://docs.astro.build/es/guides/styling/#variables-de-css

# Componentes en Astro

> [!TIP]
> Cuando reestructuras tu código pero no cambias el aspecto de tu página en el navegador, estás **refactorizando**.

- https://docs.astro.build/es/basics/astro-components/
- https://refactoring.com/

- https://docs.astro.build/es/basics/astro-components/#props-de-componentes
- https://www.dofactory.com/html/semantics

### Scripts del lado del cliente en Astro

- https://docs.astro.build/es/guides/client-side-scripts/

> [!NOTE]
> La etiqueta `<slot />` permite inyectar (o “introducir”) **contenido hijo** escrito entre las etiquetas de apertura y cierre `<Component></Component>` en cualquier archivo `Component.astro`.

### Plantillas y slot

- https://docs.astro.build/es/basics/layouts/
- https://docs.astro.build/es/basics/astro-components/#slots

### Plantillas de Markdown en Astro

- https://docs.astro.build/es/guides/markdown-content/#layout-en-el-frontmatter
- https://docs.astro.build/es/basics/layouts/#props-de-plantillas-markdown
- https://dev.to/paulasantamaria/introduction-to-yaml-125f

###  Páginas Dinámicas en Astro

- Puedes crear conjuntos completos de páginas de forma dinámica utilizando archivos `.astro` que exporten una función `getStaticPaths()`.
- La función `getStaticPaths` devuelve un array de rutas de páginas, y todas las páginas en esas rutas usarán la misma plantilla definida en el fichero.

- https://docs.astro.build/es/guides/routing/#rutas-din%C3%A1micas
- https://docs.astro.build/es/reference/api-reference/#getstaticpaths

**Instalar una librería de notificaciones a los visitantes cuando hay nuevo contenido:**

```
npm install @astrojs/rss
```

![[Pasted image 20250611101109.png]]

```
Ver tu feed RSS en un lector

Descarga un lector de feeds, o regístrate en un servicio de lector de feeds online y suscríbete a tu sitio añadiendo tu propia URL de Netlify. También puedes compartir este enlace con otras personas para que puedan suscribirse a tus entradas y recibir una notificación cuando se publique una nueva.
```

https://docs.astro.build/es/recipes/rss/#usando-glob-imports

# Preact y Astro para Componentes tipo Islas


```
npx astro add preact
```

- https://docs.astro.build/es/guides/framework-components/#usando-componente-de-framework
- https://docs.astro.build/es/guides/integrations-guide/
- https://docs.astro.build/es/reference/directives-reference/#directivas-del-cliente

- https://docs.astro.build/es/guides/client-side-scripts/

