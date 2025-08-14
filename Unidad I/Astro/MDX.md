
# MDX en Astro

MDX es una extensión poderosa que puedes usar en Astro para combinar Markdown con componentes interactivos de React (u otros frameworks). Es una evolución del formato Markdown tradicional que te permite incrustar componentes directamente en tus archivos de contenido.

## Características principales de MDX en Astro

1. **Markdown mejorado**: Escribe contenido en sintaxis Markdown normal
    
2. **Componentes interactivos**: Incrusta componentes React, Vue, Svelte, etc. directamente en tus documentos
    
3. **Soporte de JavaScript/JSX**: Puedes usar expresiones JavaScript y sintaxis JSX dentro del MDX
    
4. **Frontmatter**: Soporta metadatos en formato YAML al inicio del archivo
    

## Cómo usar MDX en Astro

1. Primero, instala la integración oficial:
    

bash

npx astro add mdx

2. Crea un archivo `.mdx` en tu proyecto, por ejemplo `pagina.mdx`
    

mdx

---
// Frontmatter (metadatos)
title: Mi Página MDX
layout: '../layouts/Layout.astro'
---

# Hola Mundo MDX

Este es un contenido **Markdown** normal.

<MiComponenteReact /> {/* Componente React incrustado */}

Puedes usar variables: {1 + 1} {/* Expresión JavaScript */}

## Ventajas de usar MDX en Astro

- **Contenido más dinámico**: Combina la simplicidad de Markdown con la potencia de los componentes
    
- **Reutilización**: Usa tus componentes Astro/React/Vue en tus documentos
    
- **SEO-friendly**: Astro procesa el MDX a HTML estático para mejor SEO
    
- **Sintaxis familiar**: Si ya conoces Markdown, MDX es fácil de aprender
    

Astro procesa los archivos MDX durante la compilación, convirtiéndolos en componentes Astro optimizados, manteniendo el rendimiento rápido característico de Astro.