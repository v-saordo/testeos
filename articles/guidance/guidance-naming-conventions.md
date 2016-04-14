<properties
   pageTitle="Convenciones de nomenclatura recomendadas para los recursos de Azure | Guía | Microsoft Azure"
   description="Convenciones de nomenclatura recomendadas para los recursos de Azure Asignación de nombres a máquinas virtuales, cuentas de almacenamiento, redes, redes virtuales, subredes y otras entidades de Azure"
   services=""
   documentationCenter="na"
   authors="masimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/01/2016"
   ms.author="masimms"/>
   
# Convenciones de nomenclatura recomendadas para los recursos de Azure

La elección del nombre de cualquier recurso de Microsoft Azure es importante porque:

- Es difícil cambiar el nombre posteriormente.
- Los nombres deben cumplir los requisitos de su tipo de recurso concreto.

Igualmente, la coherencia de las convenciones de nomenclatura facilitan la búsqueda de recursos. También ayudan a conocer el rol de un recurso en una solución.

Este artículo no solo proporciona un resumen de las reglas y restricciones de la nomenclatura de los recursos de Azure, sino también un conjunto de recomendaciones que son la línea base de las convenciones de nomenclatura. Dichas recomendaciones puede usarlas como punto de partida para la creación de convenciones propias específicas para sus necesidades.

La clave para una correcta convención de nomenclatura es establecerla y posteriormente seguirla en todas las aplicaciones y organizaciones, y, si es preciso, adaptarla a medida que se implementan más aplicaciones y servicios a través de la plataforma Azure.

## Nomenclatura de suscripciones

Al asignar nombres a las suscripciones de Azure, los nombres detallados facilitan la comprensión del contexto y propósito de cada suscripción. Si trabaja en un entorno que pueda contener varias suscripciones, seguir una convención de nomenclatura compartida puede mejorar considerablemente la claridad.

Este es un patrón recomendado para la asignación de nombres a suscripciones:

`<Company> <Department (optional)> <Product Line (optional)> <Environment>`

- En la mayoría de los casos, la compañía será la misma en todas las suscripciones. Sin embargo, algunas compañías pueden tener compañías secundarios en su estructura organizativa. Estas compañías las puede administrar un grupo de TI central, en cuyo caso, se pueden diferenciar mediante la especificación del nombre de la compañía principal (*Contoso*) y el nombre de la compañía secundaria (*North Wind*).

- El departamento es un nombre de la organización donde trabaja un grupo de individuos. Este elemento del espacio de nombres es opcional. Esto se debe a que algunas compañías puede que no necesiten profundizar en tanto detalle debido a su tamaño.

- La línea de productos es un nombre específico de un producto o una función que se realiza dentro del departamento. Aunque normalmente es opcional para los servicios y aplicaciones internos, se recomienda encarecidamente usarla para aquellos servicios de acceso público que requieran una sencilla separación e identificación (como una separación clara de los registros de facturación).

- El entorno es el nombre que describe el ciclo de vida de la implementación de las aplicaciones o servicios, como Desarrollo, Control de calidad o Producción.

| Compañía | Departamento | Línea de producto o servicio | Environment | Nombre completo |
----------| ---------- | ----------------------- | ----------- | ---------- |
| Contoso | SocialGaming | AwesomeService | Producción | Producción de AwesomeService de SocialGaming de Contoso |
| Contoso | SocialGaming | AwesomeService | Desarrollo | Desarrollo de AwesomeService de SocialGaming de Contoso |
| Contoso | IT | InternalApps | Producción | Producción de InternalApps de TI de Contoso |
| Contoso | IT | InternalApps | Desarrollo | Producción de InternalApps de TI de Contoso |

<!-- TODO; include more information about organizing subscriptions for application deployment, pods, etc -->

## Uso de afijos para evitar la ambigüedad

Al asignar nombres a recursos de Azure, se recomienda usar prefijos o sufijos comunes para identificar el tipo de recurso y su contexto. Aunque toda la información acerca de su tipo, sus metadatos y su contexto está disponible mediante programación, el uso de afijos comunes simplifica la identificación visual. Al incorporar afijos a una convención de nomenclatura, es importante especificar con claridad si el afijo estará al principio del nombre (prefijo) o al final (sufijo).

Por ejemplo, a continuación se muestran dos nombres posibles para un servicio que hospeda un motor de cálculo:

- SvcCalculationEngine (prefijo)
- CalculationEngineSvc (sufijo)

Los afijos pueden hacer referencia a distintos aspectos que describen los recursos concretos. En la tabla siguiente se muestran algunos ejemplos que suelen usarse.

| Aspecto | Ejemplo | Notas |
| ------ | ------- | ----- |
| Environment | desarrollo, producción, control de calidad | Identifica el entorno del recurso |
| Ubicación | UW (oeste de EE. UU.), ue (este de EE. UU.) | Identifica la región en que se implementa el recurso |
| Instance | 01, 02 | Para recursos que tienen tener más de una instancia con nombre (servidores web, etc.). |
| Producto o servicio | Azure | Identifica el producto, la aplicación o el servicio que admite el recurso |
| Rol | sql, web, mensajería | Identifica el rol del recurso asociado |

Al desarrollar una convención de nomenclatura específica para una compañía o proyecto, es importante elegir un conjunto común de afijos, así como su posición (prefijo o sufijo).

## Reglas y restricciones de nomenclatura

Cada tipo de recurso o servicio de Azure exige un conjunto de restricciones y un ámbito de nomenclatura; todas las convenciones de nomenclatura o patrones deben adherirse a los requisitos de las reglas de nomenclaturas, así como a su ámbito. Por ejemplo, mientras que el nombre de una máquina virtual se asigna a un nombre de DNS (y, por consiguiente, se requiere que sea único en todo Azure), el ámbito del nombre de una red virtual se sitúa en el grupo de recursos que se crea dentro.

En general, evite usar caracteres especiales (`-` o `_`) en el primer o último carácter de los nombres, ya que producirán errores en la mayor parte de las reglas de validación.

| Categoría | Servicio o entidad | Scope | Length | Uso de mayúsculas y minúsculas | Caracteres válidos | Patrón sugerido | Ejemplo |
| ------------- | ----------------- | ----- | ------ | ------ | ---------------- | ----------------- | ------- |
| El grupos de recursos | El grupos de recursos | Global | 1-64 | No distingue mayúsculas de minúsculas | Alfanuméricos, carácter de subrayado y guión | `<service short name>-<environment>-rg` | `profx-prod-rg` |
| El grupos de recursos | Conjunto de disponibilidad | El grupos de recursos | 1-80 | No distingue mayúsculas de minúsculas | Alfanuméricos, carácter de subrayado y guión | `<service-short-name>-<context>-as` | `profx-sql-as` |
| General | Etiqueta | Entidad asociada | 512 (nombre), 256 (valor) | No distingue mayúsculas de minúsculas | Alfanuméricas | `"key" : "value"` | `"department" : "Central IT"` |
| Proceso | Máquina virtual | El grupos de recursos | 1-15 | No distingue mayúsculas de minúsculas | Alfanuméricos, carácter de subrayado y guión | `<name>-<role>-<instance>` | `profx-sql-001` |
| Almacenamiento | Nombre de cuenta de almacenamiento (datos) | Global | 3-24 | Minúscula | Alfanuméricas | `<service short name><type><number>` | `profxdata001` |
| Almacenamiento | Nombre de cuenta de almacenamiento (discos) | Global | 3-24 | Minúscula | Alfanuméricas | `<vm name without dashes>st<number>` | `profxsql001st0` |
| Almacenamiento | Nombre del contenedor | Cuenta de almacenamiento | 3-63 |	Minúscula | Alfanuméricos y guión | `<context>` | `logs` |
| Almacenamiento | Nombre de blob | Contenedor | 1-1024 | Distingue mayúsculas de minúsculas | Cualquier carácter de dirección URL | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Almacenamiento | Nombre de cola | Cuenta de almacenamiento | 3-63 | Minúscula | Alfanuméricos y guión | `<service short name>-<context>-<num>` | `awesomeservice-messages-001` |
| Almacenamiento | Nombre de tabla | Cuenta de almacenamiento | 3-63 |No distingue mayúsculas de minúsculas | Alfanuméricas | `<service short name>-<context>` | `awesomeservice-logs` |
| Almacenamiento | Nombre de archivo | Cuenta de almacenamiento | 3-63 | Minúscula | Alfanuméricas | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Redes | Red virtual | El grupos de recursos | 2-80 | No distingue mayúsculas de minúsculas | Alfanuméricos, guión, carácter de subrayado y punto | `<service short name>-[section]-vnet` | `profx-vnet` |
| Redes | Subred | Red virtual principal | 2-80 | No distingue mayúsculas de minúsculas | Alfanuméricos, carácter de subrayado, guión y punto | `<role>-subnet` | `gateway-subnet` |
| Redes | Interfaz de red | El grupos de recursos | 1-80 | No distingue mayúsculas de minúsculas | Alfanuméricos, guión, carácter de subrayado y punto | `<vmname>-<num>nic` | `profx-sql1-1nic` |
| Redes | Grupo de seguridad de red (NSG) | El grupos de recursos | 1-80 | No distingue mayúsculas de minúsculas | Alfanuméricos, guión, carácter de subrayado y punto | `<service short name>-<context>-nsg` | `profx-app-nsg` |
| Redes | Regla de grupo de seguridad de red | El grupos de recursos | 1-80 | No distingue mayúsculas de minúsculas | Alfanuméricos, guión, carácter de subrayado y punto | `<descriptive context>` | `sql-allow` |
| Redes | Dirección IP pública | El grupos de recursos | 1-80 | No distingue mayúsculas de minúsculas | Alfanuméricos, guión, carácter de subrayado y punto | `<vm or service name>-pip` | `profx-sql1-pip` |
| Redes | Equilibrador de carga | El grupos de recursos | 1-80 | No distingue mayúsculas de minúsculas | Alfanuméricos, guión, carácter de subrayado y punto | `<service or role>-lb` | `profx-lb` |
| Redes | Configuración de reglas de carga equilibrada | Equilibrador de carga | 1-80 | No distingue mayúsculas de minúsculas | Alfanuméricos, guión, carácter de subrayado y punto | `descriptive context` | `http` |

<!-- TODO fill in the rest of these resources
| Networking | Azure Application Gateway | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | `<service or role>-aag` | `profx-aag`
| Networking | Azure Application Gateway Connection | Azure Application Gateway | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | `` | TODO
| Networking | Traffic Manager Profile | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | TODO | TODO
-->

## Organización de recursos mediante etiquetas

El Administrador de recursos de Azure admite entidades de etiquetado con cadenas de texto arbitrarias para identificar el contexto y simplificar la automatización. Por ejemplo, una etiqueta como `"sqlVersion: "sql2014ee"` puede identificar todas las máquinas virtuales de una implementación que ejecutan SQL Server 2014 Enterprise Edition con el fin de ejecutar un script automático en ellas. Las etiquetas se deben utilizar para aumentar y mejorar el contexto en las convenciones de nomenclatura elegidas.

> [AZURE.TIP] Otra ventaja de las etiquetas es que abarcan grupos de recursos, lo que permite vincular y poner en correlación entidades de implementaciones dispares.

Cada recurso o grupo de recursos pueden tener un máximo de **15** etiquetas. El nombre de etiqueta está limitado a 512 caracteres y el valor de la etiqueta, a 256.

Para más información acerca del uso de las etiquetas de recursos, consulte [Uso de etiquetas para organizar los recursos de Azure](../resource-group-using-tags.md).

Algunos de los casos de uso de etiquetado comunes son:

- **Facturación**; agrupación de recursos y su asociación con códigos de facturación o de contracargo.
- **Identificación de contexto de servicio**; identificar los grupos de recursos en Grupos de recursos para las operaciones comunes y la agrupación
- **Control de acceso y contexto de seguridad**; identificación de rol administrativo basado en cartera, sistema, servicio, aplicación, instancia, etc. 

> [AZURE.TIP] Marcar pronto - marcar a menudo. Es mejor tener un esquema de etiquetado de línea de base en vigor y ajustarlo con el paso del tiempo, en lugar de tener que actualizarlo a posteriori.

Un ejemplo de varios enfoques de etiquetado comunes:

| Nombre de etiqueta | Clave | Ejemplo | Comentario |
| -------- | --- | ------- | ------- |
| Id. de facturación/contracargo interno | billTo | `IT-Chargeback-1234` | Un código de facturación o de E/S internos |
| Operador o individuo directamente responsable (DRI) | managedBy | `joe@contoso.com` | Alias o dirección de correo electrónico |
| Nombre de proyecto | project-name | `myproject` | Nombre del proyecto o de la línea de producto |
| Versión de proyecto | project-version | `3.4` | Versión del proyecto o de la línea de producto |
| Environment | environment | `<Production, Staging, QA >` | Identificador de entorno | 
| Nivel: | tier | `Front End, Back End, Data` | Identificación de nivel o rol/contexto |
| Perfil de datos | dataProfile | `Public, Confidential, Restricted, Internal` | Confidencialidad de los datos almacenados en el recurso |
 
## Trucos y sugerencias

En función del tipo de aplicación, ciertos tipos de recursos pueden requerir más atención en la asignación de nombres y en las convenciones. A continuación se enumeran más detalles de estos, así como su contexto.

### Máquinas virtuales

Especialmente en topologías grandes, la asignación meticulosa de nombres a las máquinas virtuales simplificará considerablemente la identificación del rol y del propósito de cada máquina, así como la habilitación de un scripting más predecible.

> [AZURE.WARNING] Tenga en cuenta que todas las máquinas virtuales de Azure tienen un nombre de recurso de Azure y un nombre de host del sistema operativo. Si el nombre de recurso y el nombre de host son diferentes, la administración de estas máquinas virtuales puede suponer un reto (por ejemplo, si la máquina virtual se crea a partir de un archivo .vhd que ya contiene un sistema operativo configurado con un nombre de host) y debe evitarse.

- [Convenciones de nomenclatura para máquinas virtuales con Windows Server](https://support.microsoft.com/es-ES/kb/188997)

<!-- TODO - recommendations on naming VMs. -->

###	Cuentas de almacenamiento y entidades de almacenamiento

Hay dos casos de uso principales para las cuentas de almacenamiento: respaldo de discos para máquinas virtuales y almacenamiento de datos en blobs, colas y tablas. Las cuentas de almacenamiento que se usan para los discos de las máquinas virtuales deben seguir la convención de nomenclatura de su asociación con el nombre de la máquina virtual principal (y con la necesidad potencial de varias cuentas de almacenamiento para las SKU de máquina virtual de gama alta, también puede sacar provecho de un sufijo de número).

> [AZURE.TIP] Las cuentas de almacenamiento, tanto para datos como para discos, deben seguir una convención de nomenclatura que permita sacar provecho de varias cuentas de almacenamiento (es decir, siempre con un sufijo numérico).

En la cuenta de almacenamiento de Azure se puede configurar un nombre de dominio personalizado para acceder a los datos del blob. El punto de conexión predeterminado del servicio Blob es `https://mystorage.blob.core.windows.net`.

Pero si asigna un dominio personalizado (como www.contoso.com) al punto de conexión del blob para su cuenta de almacenamiento, también puede acceder a los datos del blob en su cuenta de almacenamiento mediante dicho dominio. Por ejemplo, con un nombre de dominio personalizado, se podría acceder a `http://mystorage.blob.core.windows.net/mycontainer/myblob` como `http://www.contoso.com/mycontainer/myblob`.

Para más información acerca de cómo configurar esta característica, consulte [Configurar un nombre de dominio personalizado para el punto de conexión de Almacenamiento de blobs](../storage/storage-custom-domain-name.md).

Para más información acerca de la asignación de nombres a blobs, contenedores y tablas:

- [Asignar nombres y hacer referencia a contenedores, blobs y metadatos](https://msdn.microsoft.com/library/dd135715.aspx)
- [Asignar nombres a colas y metadatos](https://msdn.microsoft.com/library/dd179349.aspx)
- [Introducción al modelo de datos del servicio Tabla](https://msdn.microsoft.com/library/azure/dd179338.aspx)

Los nombres de blob pueden contener cualquier combinación de caracteres, pero a los caracteres de URL reservados se les debe aplicar la secuencia de escape correcta. Evite los nombres de blob que terminen en un punto (.), una barra diagonal (/), o una secuencia o combinación de ambos. Por convención, la barra diagonal es el separador de directorios **virtuales**. No use la barra invertida () en los nombres de los blobs. Las API de cliente pueden permitirlo, pero el algoritmo hash no funcionará correctamente y las firmas no coincidirán.

Los nombres de las cuentas de almacenamiento o de los contenedores no se pueden modificar después de que se hayan creado. Si desea usar un nombre nuevo, debe eliminar el nombre anterior y crear uno nuevo.

> [AZURE.TIP] Se recomienda establecer una única convención de nomenclatura para todas las cuentas y tipos de almacenamiento antes de comenzar el desarrollo de un servicio o aplicación nuevos.

## Ejemplo: implementación de un servicio de N niveles

En este ejemplo, definiremos la configuración de un servicio de N niveles, que consta de servidores front-end IIS (hospedados en máquinas virtuales con Windows Server), con SQL Server (hospedados en dos máquinas virtuales de Windows Server), un clúster ElasticSearch (hospedado en seis máquinas virtuales con Linux) y las cuentas de almacenamiento asociadas, las redes virtuales, el grupo de recursos y el equilibrador de carga.

Comenzaremos por definir las convenciones contextuales para esta aplicación:

| Entidad | Convención | Descripción |
| ------ | ---------- | ------------ |  
| Nombre de servicio | `profx` | El nombre corto de la aplicación o servicio que se van a implementar |
| Environment | `prod` | Es para la implementación de producción (en lugar de control de calidad, pruebas, etc.) |

A partir de esa línea de base se pueden asignar a las convenciones de cada uno de los tipos de recursos:

| Tipo de recurso | Base de convención | Ejemplo |
| ------------- | --------------- | ------- |
| La suscripción | `<Company> <Department (optional)> <Product Line (optional)> <Environment>` | `Contoso IT InternalApps Profx Production` |
| El grupos de recursos | `servicename-rg` | `profx-rg` |
| Red virtual | `servicename-vnet` | `profx-vnet` |
| Subred | `role-subnet` | `sql-vnet` |
| Equilibrador de carga | `servicename-lb` | `profx-lb` |
| Máquina virtual | `servicename-role[number]` | `profx-sql0` |
| Cuenta de almacenamiento | `<vmnamenodashes>st<num>` | `profxsql0st0` |

Como se ve en el diagrama siguiente:

![diagrama de topología de aplicación](media/guidance-naming-conventions/guidance-naming-convention-example.png "Topología de aplicación de ejemplo")

## Ejemplo: script de la CLI de Azure para implementar el ejemplo anterior

```bash
#!/bin/sh

#####################################################################
# Sample script using the Azure CLI to build out an application 
# demonstrating naming conventions.  
#
# Note; this script is not intended for production deployment, as it does 
# not create availability sets, configure network security rules, etc.
#####################################################################

# Set up variables to build out the naming conventions for deploying
# the cluster  
LOCATION=eastus2
APP_NAME=profx
ENVIRONMENT=prod
USERNAME=testuser
PASSWORD="testpass"

# Set up the tags to associate with items in the application
TAG_BILLTO="InternalApp-ProFX-12345"
TAGS="billTo=${TAG_BILLTO}"

# Explicitly set the subscription to avoid confusion as to which subscription
# is active/default
SUBSCRIPTION=3e9c25fc-55b3-4837-9bba-02b6eb204331

# Set up the names of things using recommended conventions
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
VNET_NAME="${APP_NAME}-vnet"

# Set up the postfix variables attached to most CLI commands
POSTFIX="--resource-group ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}"

##########################################################################################
# Set up the VM conventions for Linux and Windows images

# For Windows, get the list of URN's via 
# azure vm image list ${LOCATION} MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

# For Linux, get the list or URN's via 
# azure vm image list ${LOCATION} canonical ubuntuserver
LINUX_BASE_IMAGE=canonical:ubuntuserver:16.04.0-DAILY-LTS:16.04.201602130

#########################################################################################
## Define functions 
create_vm ()
{
    vm_name=$1
    vnet_name=$2
    subnet_name=$3
    os_type=$4
    vhd_path=$5
    vm_size=$6
    diagnostics_storage=$7

	# Create the network interface card for this VM
	azure network nic create --name "${vm_name}-0nic" --subnet-name ${subnet_name} --subnet-vnet-name ${vnet_name} \
        --tags="${TAGS}" ${POSTFIX}

	# Create the storage account for this vm's disks (premium locally redundant storage -> PLRS)
    # Note the ${var//-/} syntax to remove dashes from the vm name
    storage_account_name=${vm_name//-/}st01
	azure storage account create --type=PLRS --tags "${TAGS}" ${POSTFIX} "${storage_account_name}"

    # Map the name of the diagnostics storage account to a blob URI for boot diagnostics
    # This is (currently) required when deploying with a named premium storage account 
    diag_blob="https://${diagnostics_storage}.blob.core.windows.net/"

    # Create the VM
    azure vm create --name ${vm_name} --nic-name "${vm_name}-0nic" --os-type ${os_type} \
        --image-urn ${vhd_path} --vm-size ${vm_size} --vnet-name ${vnet_name} --vnet-subnet-name ${subnet_name} \
        --storage-account-name "${storage_account_name}" --storage-account-container-name vhds --os-disk-vhd "${vm_name}-osdisk.vhd" \
        --admin-username "${USERNAME}" --admin-password "${PASSWORD}" \
		--boot-diagnostics-storage-uri "${diag_blob}" \
        --tags="${TAGS}" ${POSTFIX} 
}

###################################################################################################
# Create resources

# Step 1 - create the enclosing resource group
azure group create --name "${RESOURCE_GROUP}" --location "${LOCATION}" --tags "${TAGS}" --subscription "${SUBSCRIPTION}"

# Step 2 - create the network security groups

# Step 3 - create the networks (VNet and subnets)
azure network vnet create --name "${VNET_NAME}" --address-prefixes="10.0.0.0/8" --tags "${TAGS}" ${POSTFIX}
# TODO - does subnet support tagging?
azure network vnet subnet create --name gateway-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.1.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-master-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.2.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-data-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.3.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name sql-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.4.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}

# Step 4 - define the load balancer and network security rules
azure network lb create --name "${APP_NAME}-lb" ${POSTFIX}
# In a production deployment script, we'd create load balancer rules and 
# network security groups here

# Step 5 - create a diagnostics storage account
diagnostics_storage_account=${APP_NAME//-/}diag
azure storage account create --type=LRS --tags "${TAGS}" ${POSTFIX} "${diagnostics_storage_account}"

# Step 6.1 - Create the gateway VMs
for i in `seq 1 4`;
do
	create_vm "${APP_NAME}-gw${i}" "${APP_NAME}-vnet" "gateway-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}" 
done    

# Step 6.2 - Create the ElasticSearch master and data VMs
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-master${i}" "${APP_NAME}-vnet" "es-master-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-data${i}" "${APP_NAME}-vnet" "es-data-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done

# Step 6.3 - Create the SQL VMs
create_vm "${APP_NAME}-sql0" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
create_vm "${APP_NAME}-sql1" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
```

<!---HONumber=AcomDC_0302_2016-->