
- Con Firebase haremos el Cutom Login
- Firebase permite las autenticaciones a nuestras aplicaciones
- Permite hostear nuestras aplicaciones y hacer integraciones entre los diferentes tipos de Storage que manejan, como Storage Account (parecido a Azure Storage Acccoun o S3).

- También maneja bases de datos en tiempo real
- Es un servicio de Google
- Se pueden hacer integraciones entre Firebase y Google Cloud.
- **Tiene generación de autenticaciones con Microsoft 365, Gmail, etc.**

- para poder hacer las conexiones con Gmail o Mocrisoft 365, siempre requiere la creación las aplicaciones que le dan los Secret Values para tener acceso a la API y poder comunicar este servicio de Firebase con nuestra aplicación.

# Crear nuevo Proyecto

- Tiene que crear un nuevo proyecto desde la UI de Firebase.
- Deshabilite la opción de Google Analytics

- Tarda unos segundos en crear el proyecto con los recursos de capa gratuita que ofrece Firebase.
- Las capas gratuitas comúnmente son para aprendizaje, realización de releases, vinculación con Terraform, etc.

- Vamos a hacer las configuraciones necesarias para poder hacer la conexión de nuestro proyecto de FastAPI con las llaves y configuraciones necesarias que hacemos en Firebase.

> [!NOTA]
> Instalar en el proyecto con pip las siguientes librerías:
> - pyrebase
> - firebase-admin

- pyrebase -> Deja crear usuarios y contraseñas
- firebase-admin -> Va a dar la administración para conectarnos con el servicio y usar la API.

![[Pasted image 20250708102311.png]]

## Habilitar Firebase Authentication

- En la sección Authenticacion, habilita el proveedor de correo electrónico y contraseña.
- Queremos implementar el Custom Login, pidiendo como datos una cuenta de correo y contraseña.

- **Busquemos el servicio Authentication dentro del proyecto de Firebase**
- La configuración de los datos que queremos solicitar para la autenticación es muy fácil e intuitiva dentro del proyecto de Firebase.

![[Pasted image 20250708102606.png]]

- Una vez que ya hemos hecho las configuraciones iniciales del servicio de autenticaciones e instalado las dependencias necesarias en nuestro proyecto, **necesitamos descargar un JSON que nos hará la conexión por medio del Firebase Admin.**

En esta sección debe generar una clave privada

![[Pasted image 20250708102828.png]]

- **Esta clave no solo es para el servicio de Login, esta clave sirve para conectarnos con los servicios que se encuentran en el proyecto de Firebase desde el que se generó esta clave de acceso.**

> [!DANGER]
> Este JSON (clave de acceso) no es recomendable publicarlo en repositorios. Lo recomendable es guardar este tipo de datos en una carpeta (secrets) y agregar la carpeta y su contenido en el .gitignore
> **Agregue la información sensible en una carpeta y deshabilite su versionamiento.**

- Ya hechas las configuraciones anteriores, hay que agregar una App para conectarnos con el servicio de autenticación.

- **Diríjase nuevamente a configuraciones de proyecto y busque la sección de aplicaciones.**
- Ahora todos los siguientes pasos para crear la app de autenticación son fáciles e intuitivos.

- **Esta App nos dará las contraseñas para poder conectar la aplicación con nuestra API y poder hacer el almacenamiento de correos y contraseñas.**

Después de generar la App, nos genera estos datos que tenemos que agregar en nuestro proyecto (.env)

![[Pasted image 20250708105324.png]]

![[Pasted image 20250708110309.png]]

- Entre estos datos se encuentra el "**Storage Bucket**", es parecido al BLOB Storage o S3 para almacenar los Logs generados por la App.

> [!IMPORTANT]
> Ya hechas todas las configuraciones anteriores, queda hacer la lógica en la API para registrar cuentas.

# Crear Endpoints de Registro y Login

> [!NOTA]
> Solo he copiado el código de Github, pero se tiene que crear una función para el Registro y el Login.

Registro:

![[Pasted image 20250708123953.png]]

![[Pasted image 20250708123939.png]]

Login

![[Pasted image 20250708124556.png]]

![[Pasted image 20250708124609.png]]

Usuarios en Firebase

![[Pasted image 20250708124648.png]]

> [!WARNING]
> Los Sign In generan un ID único para los nuevos usuarios
> Los Login generan un Token para la sesión del usuario.














