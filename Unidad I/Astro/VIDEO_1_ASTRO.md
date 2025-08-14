
- WARP -> Consola con IA para facilitar el ingreso de comandos.
- Al realizar desarrollo Front-End es un error encasillarse con un solo navegador web, ya que los proyectos deben ser testeados en diferentes tipos de navegadores.

- **Del lado del Front-End, los proyectos deben ser testeados desde diferentes tipos de navegadores.**

- Los websites tienden a ser muy informativos, estáticos y cambian con menos frecuencia.
- En los websites la lógica del lado del backend no es muy prioritaria.
- Astro también sirve para simular la experiencia de un BackOffice.

- Astro ha ganado popularidad porque se centra en la componentización y reusabilidad de distintos bloques o diseños en un website.
- Estos componentes pueden ser basados en diferentes tipos de tecnologías web (astro, react, vue, etc).

```
npm create astro@latest {nombre_proyecto}
```

- astro.config.mjs -> Configuraciones o extensiones usadas en el proyectos (tailwind).
- astro está basado en TS.
- package.json -> Tiene las dependencias de NodeJs

- **^5.2.3** 
	- El último número refiere a los problemas de seguridad o bugs encontrados.
	- El segundo es cuando se agregan nuevos features al core principal.
	- El primer número refiere a las actualizaciones más importantes (migraciones, implementación de webComponents y buenas prácticas).
	- "^" La instalación de dependencias deben compatibles con la version de astro 5 como mínimo.
	- Puede quitar el símbolo "^" si desea las dependencias específicas para esa versión de astro.

- **scripts de package.json**
	- Son la manera que interactuamos con nuestro proyecto .astro
	- astro dev -> es lo entendido por npm run dev, permite la interacción con el **hot reload** para visualizar los cambios en el navegador,
	- astro build -> Se usa cuando ingresamos "npm run build", y estamos listos para publicar y generar todo el contenido estático de cada uno de los webComponents.
		- Las extensiones que nosotros usamos es .astro, y el navegador no entiende un archivo con este formato.
		- build generará todo el conjunto de archivos HTML que tomaremos para subirlos al servidor.
	- preview nos permite ver todo el mimificado del proyecto.
	- astro es para la línea de comandos.
















