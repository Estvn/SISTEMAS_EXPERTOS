
# Configuración de Servidor de Base de Datos

- Usaremos **Azure SQL** que se basa de la familia de SQL Server.

- Hay una diferencia entre las bases de datos con el servidor web y las webapps con su app service plan.
- El app service contiene todas las webapps que van a venir dentro del servidor web.
- Al app service plan le definimos el **tier**, a diferencia de la configuración del servidor de bases de datos.

- El servicio de bases de datos de Azure se comporta más como un contenedor, y todas las bases de datos que creamos van a estar bajo un DNS.
- **Cada una de las bases de datos podremos definir su tamaño.**
- **Las bases de datos se podrán acceder desde el mismo DNS, y serán administradas desde el mismo admin que se define en el código de configuración de terraform.**
- Puedo tener bases de datos más grandes que otras.

Herramienta/Cliente a utilizar para poder hacer conexión con la base de datos:

- **SQL Management Studio.**
- **Azure Data Studio.** Tiene mejor sinergia con Azure, presenta estadísticas interesantes y tiene herramientas como notebooks para poder interactuar con la base de datos.

### Agregar una Firewall Rule en el Servidor de DB de Azure

- Diríjase a "Show Network Settings"
- Diríjase al apartado de Firewall
- Agregue la dirección IPv4 del cliente en el que está conectado actualmente.

![[Pasted image 20250609233916.png]]

> [!WARNING]
> Si intenta ingresar al servidor de base de datos con una IP que no está ingresada en el Firewall no se podrá dar acceso.

- La IP depende del proveedor de internet, si cambia de proveedor u otra razón tendrá que cambiar la IP en el Firewall del servidor de base de datos.

- **Dar check a "Permitir que los servicios y recursos de Azure accedan a este servidor". Porque yo quiero que la webapp de la API, cuando intente hacer conexión con el servicio de base de datos lo permita.**
- Como ambos pertenecen al mismo grupo de recursos, no quiero que surja algún tipo de error.

- Cuando se intente conectar al servicio de bases de datos desde **Azure Data Studio** tiene que
	- Seleccionar el tipo de autenticación como: "SQL Login"
	- Tiene que seleccionar la database "Master", esta contiene la información de todos los objetos de seguridad que se crean en esta base de datos.

![[Pasted image 20250609235004.png]]

- Con Azure Data Studio se instala un cliente de SQL que lo hace conectarse con un servidor de forma remota.
- **Al hacer conexión con el servidor, nos hemos conectado al servicio pero no a una base de datos.**
- Tenemos que crear bases de datos desde terraform.

 - **A la base de datos se le pone el tier o procesamiento que va a estar consumiendo, al servicio no se le agrega el tier.**

> [!DANGER]
> Las bases de datos heredan las configuraciones de Firewall del servidor de base de datos.

#### Programa para conectarse al servicio de Azure Storage

- **Azure Storage Explorer**
- https://azure.microsoft.com/en-us/products/storage/storage-explorer

- En el Storage Account se pueden crear tablas de SQL, pero el servicio no proporciona restricciones ni relaciones entre tablas.
- En el apartado de BLOB Service se pueden crear contenedores (no los de Docker) para empezar a seccionar partes del servicio para almacenar imágenes.
- **Al crear contenedores tienen que quedar privados.**

- BLOB Service y los contenedores tienen que ser creados dentro de terraform.
- Se pueden crear colas para configurar los Serverless.

**Para conectar el Storage Account al programa Azure Data Studio tiene que tomar una de las siguientes keys:**

![[Pasted image 20250610092542.png]]

- Ahora registre la clave, conectándose al programa por medio de conexión por recurso.
- **Una vez conectado puede configurar los contenedores o colas en terraform.**

> [!DANGER]
>  - Las QUEUE STORAGE **no son para almacenar información delicada**, porque los **Queue Storage** es un tipo de **almacenamiento volátil**.
>  - El almacenamiento no es permanente, pero puede indicar cuanto durará un dato en la cola



































