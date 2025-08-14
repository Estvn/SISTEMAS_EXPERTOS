
# Terraform y Variables de Entorno

- ¿Que pasa si queremos modificar nuestro grupo de recursos?
- **Una buena práctica al crear nuestro grupo de recursos, es agregar tags.**

- Es bueno definir para que fue creado, en que fecha se creó, quien lo creó y otro tipo de información.
- Incluso puede servir para realizar un análisis de costo de lo que está costando tener un grupo de recursos.

> [!NOTA]
> Revise el proyecto de terraform para revisar como se define las tags.

- **Hay data como el id de suscripción y metadata que es mala práctica dejarla expuesta/quemada en el archivo main.tf**
- La solución es la creación de las variables de entorno por medio del archivo **variables.tf** que las gestiona.

- En el archivo **variables.tf** puede definir el nombre de la variable, pero no su valor.
- Cuando ejecute `terraform plan`, se le pedirá el valor de las variables incógnitas que ha dejado.
- **Esto se realiza para garantizar una mejor seguridad.**

- **local.tfvars** es el archivo que contendrá todas las variables que no deseamos tener quemadas en los archivos públicos.

### Usar el archivo local.tfvars

- Si hemos definido variables en el archivo **local.tfvars** y se están usando en los archivos públicos del proyecto, en la terminal al ejecutar el comando **terraform plan** tenemos que indicar el siguiente comando:

```
terraform plan -var-file="local.tfvars"
```

- El nombre del grupo de recurso tiene un convenio `rg-nombreproyecto-entorno`

> [!IMPORTANT]
> Los tags nos pueden servir para identificar los grupos de recursos y realizar planes de costos.




















