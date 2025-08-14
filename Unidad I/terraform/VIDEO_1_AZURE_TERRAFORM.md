
# Creación de Resource Group desde Terraform

- Es curso no tendrá explicaciones sobre configuración de redes.

- Terraform es una herramienta que nos ayuda a definir mediante código toda nuestra infraestructura.
- Podemos seleccionar el provider con el cual haremos toda la configuración (Azure).
- https://developer.hashicorp.com/terraform/install

- **Usaremos el Azure CLI para hacer la conexión de nuestra terminal con la plataforma de Azure.**
- https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows?view=azure-cli-latest&pivots=winget

> [!NOTA]
> Necesita tener una suscripción activa de Azure para realizar estas configuraciones.

- Comando para terraform `terraform`
- Comando para Azure CLI `az --version`

# Azure CLI

- Log desde la terminal de Azure

 ```
 az login
```

- En una suscripción también podemos usar configuraciones globales, como el presupuesto a nuestra suscripción, alarmas, etc.

- En el ejemplo vamos a desplegar lo más común:
	- Un servidor web con dos aplicaciones (UI y API)
	- Un Storage Account donde vamos a tener el BLOB Service para almacenar información no estructurada, 
	- Queue Storage para tener una programación más orientada a eventos, donde metemos un mensaje en una cola y una function App va a poder tomar ese mensaje y realizar una operación (conocidos como procesos backgrounds).
	- Configurar un Azure Container Registry donde almacenaremos todas las imágenes de Docker que vamos a estar configurando.
	- Una base de datos relacional donde si tenemos una lógica transaccional o integridad, almacenarlo en la base de datos.

# Iniciando con Terraform


> [!DANGER]
> Revise el proyecto para ver las codificaciones en terraform

- Podemos usar el comando `terraform init` para vincular mi proyecto de terraform con la plataforma de Azure.
- **`terraform init` se hace una sola vez, cuando creamos nuestro proyecto o queremos hacer la primera prueba.**

```
terraform init
```

- El comando `terraform plan` nos sirve para verificar:.
	1. Si no hay errores en la codificación realizada.
	2. Si podremos hacer el release de lo que se acaba de definir en nuestra plataforma.

```
terraform plan
```

- El comando `terraform apply` le damos la instrucción a nuestra plataforma de que empiece a crear todos nuestro recursos y todos los elementos especificados en el proyecto de terraform hacia **Azure**.
- Como esto es una operación delicada, en la CLI aparecerán medidas de confirmación

```
terraform apply
```

> [!NOTA]
> - Un grupo de recursos es lo que hará el contenedor que guardará todos elementos que se irán definiendo, como una DB, una DB caché, servidor web, etc.
> - Se agrupa en un grupo de recursos dependiendo de la funcionalidad o responsabilidad que se quiere dar.
> - Se usa para organizar todos los elementos en un grupo de recursos, o para definir todos los recursos a usar en un proyecto.































