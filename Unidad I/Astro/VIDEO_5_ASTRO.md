
# Despliegue a Cloudflare


```
npm run build
```

- Este comando lee la función **getStaticPaths** y genera páginas estáticas de forma dinámica.
- Este comando realiza un proceso de mimificación, donde los archivos HTML tiene todo su código en una sola línea.
	- Esto deja el archivo más robusto y la lectura es más rápida para el navegador.

- El mimificado hace carpetas para cada página generada, esto lo realiza por temas de seguridad, **para que no se pueda ver la extensión de los archivos de las páginas públicas.**

**Para hacer el despliegue necesitará dos cuentas:**
1. Cuenta de Github
2. Cuenta de Cloudflare

- Workers > Pages > Import an Git project
