<properties
	pageTitle="Plantilla del Administrador de recursos del clúster de Redis"
	description="Aprenda a implementar fácilmente un nuevo clúster de Redis en máquinas virtuales de Ubuntu con Azure PowerShell o CLI de Azure y una plantilla del Administrador de recursos"
	services="virtual-machines"
	documentationCenter=""
	authors="timwieman"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2015"
	ms.author="twieman"/>

# Clúster de Redis con una plantilla del Administrador de recursos

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.


Redis es un almacén y memoria caché de pares clave-valor de código abierto, donde las claves pueden contener estructuras de datos tales como cadenas, hash, listas, conjuntos y conjuntos ordenados. Redis admite un conjunto de operaciones atómicas con estos tipos de datos. Con el lanzamiento de Redis versión 3.0, el clúster de Redis ahora está disponible en la versión estable más reciente de Redis. El clúster de Redis es una implementación distribuida de Redis donde los datos se particionan automáticamente en varios nodos de Redis, con la posibilidad de continuar las operaciones cuando un subconjunto de nodos experimenta errores.

La Caché en Redis de Microsoft Azure es un servicio dedicado de caché en Redis administrado por Microsoft, pero es posible que no todos los clientes de Microsoft Azure quieran usar la Caché en Redis de Azure. Algunos preferieren mantener su caché en Redis detrás de una subred dentro de sus propias implementaciones de Azure, mientras que a otros les resultará más cómodo hospedar sus propios servidores de Redis en máquinas virtuales de Linux para aprovechar al máximo todas las características de Redis.

Este tutorial le guiará a través del uso de una plantilla de ejemplo del Administrador de recursos de Azure para implementar un clúster de Redis en máquinas virtuales de Ubuntu dentro de una subred de un grupo de recursos de Microsoft Azure. Además del clúster de Redis 3.0, esta plantilla también admite la implementación de Redis 2.8 con Redis Sentinel. Tenga en cuenta que este tutorial se centrará en la implementación del clúster de Redis 3.0.

El clúster de Redis se crea detrás de una subred, de modo que no hay acceso de IP pública al clúster de Redis. Como parte de la implementación, puede implementar un “JumpBox” opcional. Este “JumpBox” es una máquina virtual de Ubuntu que también se implementa en la subred, pero que *expone* una dirección IP pública con un puerto SSH abierto al que se puede conectar mediante SSH. A continuación, desde el “JumpBox”, puede conectarse mediante SSH a todas las máquinas virtuales de Redis de la subred.

Esta plantilla usa el concepto de “tamaño de camiseta” para especificar una configuración del clúster de Redis “pequeña”, “mediana” o “grande”. Cuando el idioma de la plantilla de Administrador de recursos de Azure admite un tamaño de plantilla más dinámico, este puede cambiarse para especificar el número de nodos maestros del clúster de Redis, los nodos subordinados, el tamaño de máquina virtual, etc. Por el momento, puede ver el tamaño de la máquina virtual y el número de nodos maestros y subordinados definidos en el archivo azuredeploy.json en las variables `tshirtSizeSmall`, `tshirtSizeMedium` y `tshirtSizeLarge`.

La plantilla de clúster de Redis para el tamaño “mediano” crea esta configuración:

![cluster-architecture](media/virtual-machines-redis-template/cluster-architecture.png)

Antes de entrar en más detalles relacionados con el Administrador de recursos de Azure y la plantilla que usaremos para esta implementación, asegúrese de que tiene Azure PowerShell o CLI de Azure configurados correctamente.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Implementar un clúster de Redis con una plantilla del Administrador de recursos

Siga estos pasos para crear un clúster de Redis mediante una plantilla del Administrador de recursos desde el repositorio de plantillas de GitHub. Cada paso incluye instrucciones para Azure PowerShell y CLI de Azure.

### Paso 1-a: Descargar los archivos de plantilla mediante Azure PowerShell

Cree una carpeta local para la plantilla JSON y otros archivos asociados (por ejemplo, C:\\Azure\\Templates\\RedisCluster).

Sustituya el nombre de carpeta de la carpeta local y ejecute estos comandos:

```powershell
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy-parameters.json"
$filePath = $folderName + "\azuredeploy-parameters.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/empty-resources.json"
$filePath = $folderName + "\empty-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/jumpbox-resources.json"
$filePath = $folderName + "\jumpbox-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/node-resources.json"
$filePath = $folderName + "\node-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-cluster-install.sh"
$filePath = $folderName + "\redis-cluster-install.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-cluster-setup.sh"
$filePath = $folderName + "\redis-cluster-setup.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-sentinel-startup.sh"
$filePath = $folderName + "\redis-sentinel-startup.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/shared-resources.json"
$filePath = $folderName + "\shared-resources.json"
$webclient.DownloadFile($url,$filePath)
```

### Paso 1-b: Descargar los archivos de plantilla con CLI de Azure

Clone todo el repositorio de plantillas mediante un cliente Git de su elección, por ejemplo:

```
git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates
```

Cuando haya finalizado la clonación, busque la carpeta **redis-high-availability** en el directorio C:\\Azure\\Templates.

### Paso 2 (opcional): Comprender los parámetros de plantilla

Cuando se crea un clúster de Redis con una plantilla, debe especificar un conjunto de parámetros de configuración. Para ver los parámetros que se deben especificar para la plantilla en un archivo JSON local antes de ejecutar el comando para crear el clúster de Redis, abra el archivo JSON en el editor de texto o la herramienta de su elección.

Busque la sección “parameters” en la parte superior del archivo, que muestra el conjunto de parámetros que necesita la plantilla para configurar el clúster de Redis. Esta es la sección “parameters” de la plantilla azuredeploy.json:

```json
"parameters": {
	"adminUsername": {
		"type": "string",
		"metadata": {
			"Description": "Administrator user name used when provisioning virtual machines"
		}
	},
	"adminPassword": {
		"type": "securestring",
		"metadata": {
			"Description": "Administrator password used when provisioning virtual machines"
		}
	},
	"storageAccountName": {
		"type": "string",
		"defaultValue": "",
		"metadata": {
			"Description": "Unique namespace for the Storage account where the virtual machine's disks will be placed"
		}
	},
	"location": {
		"type": "string",
		"metadata": {
			"Description": "Location where resources will be provisioned"
		}
	},
	"virtualNetworkName": {
		"type": "string",
		"defaultValue": "redisVirtNet",
		"metadata": {
			"Description": "The arbitrary name of the virtual network provisioned for the Redis cluster"
		}
	},
	"addressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/16",
		"metadata": {
			"Description": "The network address space for the virtual network"
		}
	},
	"subnetName": {
		"type": "string",
		"defaultValue": "redisSubnet1",
		"metadata": {
			"Description": "Subnet name for the virtual network that resources will be provisioned into"
		}
	},
	"subnetPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/24",
		"metadata": {
			"Description": "Address space for the virtual network subnet"
		}
	},
	"nodeAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.1",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"jumpbox": {
		"type": "string",
		"defaultValue": "Disabled",
		"allowedValues": [
			"Enabled",
			"Disabled"
		],
		"metadata": {
			"Description": "The flag allowing to enable or disable provisioning of the jump-box VM that can be used to access the Redis nodes"
		}
	},
	"tshirtSize": {
		"type": "string",
		"defaultValue": "Small",
		"allowedValues": [
			"Small",
			"Medium",
			"Large"
		],
		"metadata": {
			"Description": "T-shirt size of the Redis deployment"
		}
	},
	"redisVersion": {
		"type": "string",
		"defaultValue": "stable",
		"metadata": {
			"Description": "The version of the Redis package to be deployed on the cluster (or use 'stable' to pull in the latest and greatest)"
		}
	},
	"redisClusterName": {
		"type": "string",
		"defaultValue": "redis-cluster",
		"metadata": {
			"Description": "The arbitrary name of the Redis cluster"
		}
	}
},
```

Cada parámetro incluye detalles como el tipo de datos y los valores permitidos. Esto permite la validación de parámetros pasados durante la ejecución de la plantilla en un modo interactivo (por ejemplo, Azure PowerShell o CLI de Azure), así como una interfaz de usuario de detección automática que se podría compilar dinámicamente mediante el análisis de la lista de parámetros necesarios y sus descripciones.

### Paso 3-a: Implementar un clúster de Redis con una plantilla mediante Azure PowerShell

Prepare un archivo de parámetros para la implementación creando un archivo JSON que contenga los valores de tiempo de ejecución para todos los parámetros. Este archivo se pasará como una sola entidad al comando de implementación. Si no incluye un archivo de parámetros, Azure PowerShell usará los valores predeterminados especificados en la plantilla y le pedirá que rellene los valores restantes.

A continuación se muestra un ejemplo que puede encontrar en el archivo azuredeploy-parameters.json: Tenga en cuenta que deberá proporcionar valores válidos para los parámetros `storageAccountName`, `adminUsername` y `adminPassword`, además de las personalizaciones realizadas en los demás parámetros:

```json
{
	"storageAccountName": {
		"value": "redisdeploy1"
	},
	"adminUsername": {
		"value": ""
	},
	"adminPassword": {
		"value": ""
	},
	"location": {
		"value": "West US"
	},
	"virtualNetworkName": {
		"value": "redisClustVnet"
	},
	"subnetName": {
		"value": "Subnet1"
	},
	"addressPrefix": {
		"value": "10.0.0.0/16"
	},
	"subnetPrefix": {
		"value": "10.0.0.0/24"
	},
	"nodeAddressPrefix": {
		"value": "10.0.0.1"
	},
	"redisVersion": {
		"value": "3.0.0"
	},
	"redisClusterName": {
		"value": "redis-arm-cluster"
	},
	"jumpbox": {
		"value": "Enabled"
	},
	"tshirtSize":  {
		"value": "Small"
	}
}
```

>[AZURE.NOTE]El parámetro `storageAccountName` debe ser un nombre de cuenta de almacenamiento único e inexistente que cumpla los requisitos de nomenclatura de una cuenta de almacenamiento de Microsoft Azure (solo letras minúsculas y números). Esta cuenta de almacenamiento se creará como parte del proceso de implementación.

Rellene un nombre de implementación de Azure, un nombre de grupo de recursos, una ubicación de Azure y la carpeta de los archivos JSON guardados. A continuación, ejecute estos comandos:

```powershell
$deployName="<deployment name>, such as TestDeployment"
$RGName="<resource group name>, such as TestRG"
$locName="<Azure location, such as West US>"
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$templateFile= $folderName + "\azuredeploy.json"
$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

New-AzureResourceGroup –Name $RGName –Location $locName

New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
```

>[AZURE.NOTE]`$RGName` debe ser único dentro de su suscripción.

Al ejecutar el comando **New-AzureResourceGroupDeployment**, se extraerán los valores de parámetros desde el archivo de parámetros JSON (azuredeploy-parameters.json) y se empezará a ejecutar la plantilla según corresponda. Al definir y usar varios archivos de parámetros con sus distintos entornos (por ejemplo, prueba, producción, etc.), se promoverá la reutilización de plantillas y se simplificarán las soluciones complejas de entornos múltiples.

Durante la implementación, recuerde que debe crearse una nueva cuenta de almacenamiento de Azure para que el nombre proporcionado como parámetro de cuenta de almacenamiento sea único y cumpla todos los requisitos de una cuenta de almacenamiento de Azure (solo letras minúsculas y números).

Durante la implementación, verá algo parecido a esto:

	PS C:\> New-AzureResourceGroup –Name $RGName –Location $locName

	ResourceGroupName : TestRG
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

	ResourceId        : /subscriptions/1234abc1-abc1-1234-12a1-ab1ab12345ab/resourceGroups/TestRG

	PS C:\> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
	VERBOSE: 2:39:10 PM - Template is valid.
	VERBOSE: 2:39:14 PM - Create template deployment 'TestDeployment'.
	VERBOSE: 2:39:25 PM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is running
	VERBOSE: 2:40:04 PM - Resource Microsoft.Resources/deployments 'node-resources0' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'node-resources1' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is succeeded
	VERBOSE: 2:42:23 PM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is succeeded
	VERBOSE: 2:47:44 PM - Resource Microsoft.Resources/deployments 'node-resources1' provisioning status is succeeded
	VERBOSE: 2:47:57 PM - Resource Microsoft.Resources/deployments 'node-resources0' provisioning status is succeeded
	VERBOSE: 2:48:01 PM - Resource Microsoft.Resources/deployments 'lastnode-resources' provisioning status is running
	VERBOSE: 2:58:24 PM - Resource Microsoft.Resources/deployments 'lastnode-resources' provisioning status is succeeded

	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 7:58:23 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     myadmin
                    adminPassword    SecureString
                    storageAccountName  String                     myuniqstgacct87
                    location         String                     West US
                    virtualNetworkName  String                     redisClustVnet
                    addressPrefix    String                     10.0.0.0/16
                    subnetName       String                     Subnet1
                    subnetPrefix     String                     10.0.0.0/24
                    nodeAddressPrefix  String                     10.0.0.1
                    jumpbox          String                     Enabled
                    tshirtSize       String                     Small
                    redisVersion     String                     3.0.0
                    redisClusterName  String                     redis-arm-cluster

	Outputs           :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    installCommand   String                     bash redis-cluster-install.sh -n redis-arm-cluster -v
                    3.0.0 -c 3 -m 3 -s 0 -p 10.0.0.1
                    setupCommand     String                     bash redis-cluster-install.sh -n redis-arm-cluster -v
                    3.0.0 -c 3 -m 3 -s 0 -p 10.0.0.1 -l
	...

Durante la implementación y después de ella, puede comprobar todas las solicitudes realizadas durante el aprovisionamiento, incluidos todos los errores producidos.

Para ello, vaya al [Portal de Azure](https://portal.azure.com) y haga lo siguiente:

- Haga clic en **Examinar** en la barra de navegación de la izquierda y, a continuación, desplácese hacia abajo y haga clic en **Grupos de recursos**.
- Seleccione el grupo de recursos que acaba de crear para abrir la hoja “Grupo de recursos”.
- En la sección **Supervisión**, seleccione el gráfico de barras “Eventos”. Esto mostrará los eventos para la implementación.
- Al hacer clic en eventos individuales, puede profundizar más en los detalles de cada operación que se realiza en nombre de la plantilla.

Si desea quitar este grupo de recursos y todos sus recursos (cuenta de almacenamiento, máquina virtual y red virtual) después de realizar las pruebas, use este comando:

```powershell
Remove-AzureResourceGroup –Name "<resource group name>"
```

### Paso 3-b: Implementar un clúster de Redis con una plantilla mediante CLI de Azure

Para implementar un clúster de Redis a través de CLI de Azure, primero debe crear un grupo de recursos especificando un nombre y una ubicación:

```powershell
azure group create TestRG "West US"
```

Pase este nombre del grupo de recursos, la ubicación del archivo de plantilla JSON y la ubicación del archivo de parámetros (consulte la sección anterior de Azure PowerShell para obtener detalles) en el siguiente comando:

```powershell
azure group deployment create TestRG -f .\azuredeploy.json -e .\azuredeploy-parameters.json
```

Puede comprobar el estado de las implementaciones de recursos individuales con el comando siguiente:

```powershell
azure group deployment list TestRG
```

## Recorrido por la estructura de plantilla y la organización de archivos del clúster de Redis

Si desea crear un enfoque sólido y reutilizable para el diseño de plantillas del Administrador de recursos, hay que pensar aún más en cómo organizar la serie de tareas complejas e interrelacionadas necesarias durante la implementación de una solución compleja como el clúster de Redis. Al aprovechar las capacidades de vinculación de plantillas del Administrador de recursos, los bucles de recursos y la ejecución de scripts mediante extensiones relacionadas, es posible implementar un enfoque modular que se puede reutilizar con prácticamente cualquier implementación compleja basada en plantillas.

En este diagrama se describen las relaciones entre todos los archivos descargados de GitHub para esta implementación:

![redis-files](media/virtual-machines-redis-template/redis-files.png)

Esta sección le guía a través de la estructura de la plantilla azuredeploy.json para el clúster de Redis.

Si no ha descargado una copia del archivo de plantilla, designe una carpeta local como ubicación del archivo y créela (por ejemplo, C:\\Azure\\Templates\\RedisCluster). Rellene el nombre de la carpeta y ejecute estos comandos:

```powershell
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)
```

Abra la plantilla azuredeploy.json en un editor de texto o una herramienta de su elección. En la siguiente información se describe la estructura del archivo de plantilla y el propósito de cada sección. Como alternativa, puede ver el contenido de esta plantilla en el explorador desde [aquí](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json).

### Sección “parameters”

Ya se mencionó el rol de azuredeploy-parameters.json, que se usará para pasar un conjunto determinado de valores de parámetros durante la ejecución de la plantilla. La sección “parameters” de azuredeploy.json especifica parámetros que se usan para introducir datos en esta plantilla.

Este es un ejemplo de un parámetro para el “tamaño de camiseta”:

```json
"tshirtSize": {
	"type": "string",
	"defaultValue": "Small",
	"allowedValues": [
		"Small",
		"Medium",
		"Large"
	],
	"metadata": {
		"Description": "T-shirt size of the Redis deployment"
	}
},
```

>[AZURE.NOTE]Tenga en cuenta que puede especificarse un `defaultValue`, así como `allowedValues`.

### Sección "variables"

La sección “variables” especifica las variables que se pueden usar en esta plantilla. Básicamente, contiene cierto número de campos (tipos de datos o fragmentos JSON) que se establecerán en constantes o valores calculados en tiempo de ejecución. Estos son algunos ejemplos, del más sencillo al más complejo:

```json
"vmStorageAccountContainerName": "vhd-redis",
"vmStorageAccountDomain": ".blob.core.windows.net",
"vnetID": "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
...
"machineSettings": {
	"adminUsername": "[parameters('adminUsername')]",
	"machineNamePrefix": "redisnode-",
	"osImageReference": {
		"publisher": "[variables('osFamilyUbuntu').imagePublisher]",
		"offer": "[variables('osFamilyUbuntu').imageOffer]",
		"sku": "[variables('osFamilyUbuntu').imageSKU]",
		"version": "latest"
	}
},
...
"vmScripts": {
	"scriptsToDownload": [
		"[concat(variables('scriptUrl'), 'redis-cluster-install.sh')]",
		"[concat(variables('scriptUrl'), 'redis-cluster-setup.sh')]",
		"[concat(variables('scriptUrl'), 'redis-sentinel-startup.sh')]"
	],
	"installCommand": "[concat('bash ', variables('installCommand'))]",
	"setupCommand": "[concat('bash ', variables('installCommand'), ' -l')]"
}
```

Las variables `vmStorageAccountContainerName` y `vmStorageAccountDomain` son ejemplos de variables de nombre/valor simples. `vnetID` es un ejemplo de una variable que se calcula en tiempo de ejecución mediante las funciones `resourceId` y `parameters`. `machineSettings` se basa en estos conceptos todavía más al anidar el objeto JSON `osImageReference` en la variable `machineSettings`. `vmScripts` contiene una matriz JSON, `scriptsToDownload`, que se calcula en tiempo de ejecución usando las funciones `concat` y `variables`.

Si desea personalizar el tamaño de la implementación del clúster de Redis, puede cambiar las propiedades de las variables `tshirtSizeSmall`, `tshirtSizeMedium` y `tshirtSizeLarge` en la plantilla azuredeploy.json.

```json
"tshirtSizeSmall": {
	"vmSizeMember": "Standard_A1",
	"numberOfMasters": 3,
	"numberOfSlaves": 0,
	"totalMemberCount": 3,
	"totalMemberCountExcludingLast": 2,
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
"tshirtSizeMedium": {
	"vmSizeMember": "Standard_A2",
	"numberOfMasters": 3,
	"numberOfSlaves": 3,
	"totalMemberCount": 6,
	"totalMemberCountExcludingLast": 5,
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
"tshirtSizeLarge": {
	"vmSizeMember": "Standard_A5",
	"numberOfMasters": 3,
	"numberOfSlaves": 6,
	"totalMemberCount": 9,
	"totalMemberCountExcludingLast": 8,
	"arbiter": "Enabled",
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
```

Nota: Las propiedades `totalMemberCountExcludingLast` y `totalMemberCount` son necesarias porque el idioma de la plantilla actualmente no contiene operaciones “matemáticas”.

Encontrará más información sobre el idioma de la plantilla en MSDN en [Idioma de la plantilla del Administrador de recursos de Azure](../resource-group-authoring-templates.md).

### Sección "resources"

La sección “resources” es donde ocurre la mayor parte de la acción. Analizando detenidamente esta sección, puede identificar inmediatamente dos casos diferentes. El primero es un elemento definido de tipo `Microsoft.Resources/deployments` que básicamente invoca una implementación anidada dentro de la principal. El segundo es la propiedad `templateLink` (y la propiedad `contentVersion` relacionada) que hace posible especificar un archivo de plantilla vinculada que se invocará, pasando un conjunto de parámetros como entrada. Pueden verse en este fragmento de plantilla:

```json
{
    "name": "shared-resources",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "[variables('sharedTemplateUrl')]",
            "contentVersion": "1.0.0.0"
        },
        "parameters": {
            "commonSettings": {
                "value": "[variables('commonSettings')]"
            },
            "storageSettings": {
                "value": "[variables('storageSettings')]"
            },
            "networkSettings": {
                "value": "[variables('networkSettings')]"
            }
        }
    }
},
```

A partir de este primer ejemplo está claro que azuredeploy.json se organizó en este escenario como un mecanismo de coordinación, al invocar cierto número de otros archivos de plantillas. Cada archivo es responsable de una parte de las actividades de implementación necesarias.

En particular, las siguientes plantillas vinculadas se usarán para esta implementación:

- **shared-resource.json**: contiene la definición de todos los recursos que se van a compartir en toda la implementación. Algunos ejemplos son las cuentas de almacenamiento que se usan para almacenar discos del sistema operativo de la máquina virtual, redes virtuales y conjuntos de disponibilidad.
- **jumpbox resources.json**: implementa la máquina virtual de “JumpBox” y todos los recursos relacionados, como la interfaz de red, la dirección IP pública y el extremo de entrada usado para conectarse mediante SSH al entorno.
- **node-resources.json**: implementa todas las máquinas virtuales de nodo del clúster de Redis y los recursos conectados (por ejemplo, adaptadores de red, IP privadas, etc.). Esta plantilla también implementa extensiones de máquinas virtuales (scripts personalizados para Linux) e invoca un script Bash para instalar físicamente y configurar Redis en cada nodo. El script que se va a invocar se pasa a esta plantilla en el parámetro `machineSettings` de la propiedad `commandToExecute`. Todos los nodos del clúster de Redis excepto uno se pueden implementar y generar por scripts en paralelo. Es necesario reservar un nodo hasta el final porque la configuración del clúster de Redis solo puede ejecutarse en un nodo y debe hacerse una vez que todos los nodos estén ejecutando el servidor de Redis. Por esta razón, el script que se va a ejecutar se pasa a esta plantilla; el último nodo debe ejecutar un script ligeramente diferente que no solo instalará el servidor de Redis, sino que configurará el clúster de Redis.

Analicemos *cómo* se usa esta última plantilla, node-resources.json, ya que es una de las más interesantes desde el punto de vista del desarrollo de una plantilla. Un concepto importante que se debe resaltar es cómo puede un solo archivo de plantilla implementar varias copias de un tipo de recurso único y puede establecer para cada instancia valores únicos para la configuración requerida. Este concepto se conoce como **bucle de recursos**.

Cuando node-resources.json se invoca desde el archivo azuredeploy.json principal, se invoca desde dentro de un recurso que usa el elemento `copy` para crear un bucle de tipos. Un recurso que usa el elemento `copy` “se copiará” a sí mismo el número de veces especificado en el parámetro `count` del elemento `copy`. Para todas las configuraciones donde sea necesario especificar valores únicos entre diferentes instancias del recurso implementado, se puede usar la función **copyindex()** para obtener un valor numérico que indica el índice actual en esa creación de bucle de recurso en particular. En el fragmento siguiente de azuredeploy.json se puede ver este concepto aplicado a la creación de varias máquinas virtuales para los nodos del clúster de Redis:

```json
{
    "type": "Microsoft.Resources/deployments",
    "name": "[concat('node-resources', copyindex())]",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
    ],
    "copy": {
        "name": "memberNodesLoop",
        "count": "[variables('clusterSpec').totalMemberCountExcludingLast]"
    },
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "[variables('clusterSpec').vmTemplate]",
            "contentVersion": "1.0.0.0"
        },
        "parameters": {
            "commonSettings": {
                "value": "[variables('commonSettings')]"
            },
            "storageSettings": {
                "value": "[variables('storageSettings')]"
            },
            "networkSettings": {
                "value": "[variables('networkSettings')]"
            },
            "machineSettings": {
                "value": {
                    "adminUsername": "[variables('machineSettings').adminUsername]",
                    "machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
                    "osImageReference": "[variables('machineSettings').osImageReference]",
                    "vmSize": "[variables('clusterSpec').vmSizeMember]",
                    "machineIndex": "[copyindex()]",
                    "vmScripts": "[variables('vmScripts').scriptsToDownload]",
                    "commandToExecute": "[concat(variables('vmScripts').installCommand, ' -i ', copyindex())]"
                }
            },
            "adminPassword": {
                "value": "[parameters('adminPassword')]"
            }
        }
    }
},
```

Otro concepto importante en la creación de recursos es la capacidad de especificar las dependencias y prioridades entre los recursos, tal como se puede observar en la matriz JSON `dependsOn`. En esta plantilla en particular, puede ver que los nodos del clúster de Redis dependen de los recursos compartidos que se crean en primer lugar.

Como se mencionó anteriormente, el último nodo debe esperar para su aprovisionamiento hasta que todos los demás nodos del clúster de Redis estén aprovisionados con el servidor de Redis que se ejecuta en ellos. Esto se logra en azuredeploy.json mediante un recurso llamado `lastnode-resources` que depende del bucle `copy`, llamado `memberNodesLoop`, del fragmento de plantilla anterior. Una vez completado el aprovisionamiento de `memberNodesLoop`, `lastnode-resources` se puede aprovisionar:

```json
{
	"name": "lastnode-resources",
	"type": "Microsoft.Resources/deployments",
	"apiVersion": "2015-01-01",
	"dependsOn": [
		"memberNodesLoop"
	],
	"properties": {
		"mode": "Incremental",
		"templateLink": {
			"uri": "[variables('clusterSpec').vmTemplate]",
			"contentVersion": "1.0.0.0"
		},
		"parameters": {
			"commonSettings": {
				"value": "[variables('commonSettings')]"
			},
			"storageSettings": {
				"value": "[variables('storageSettings')]"
			},
			"networkSettings": {
				"value": "[variables('networkSettings')]"
			},
			"machineSettings": {
				"value": {
					"adminUsername": "[variables('machineSettings').adminUsername]",
					"machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
					"osImageReference": "[variables('machineSettings').osImageReference]",
					"vmSize": "[variables('clusterSpec').vmSizeMember]",
					"machineIndex": "[variables('clusterSpec').totalMemberCountExcludingLast]",
					"vmScripts": "[variables('vmScripts').scriptsToDownload]",
					"commandToExecute": "[concat(variables('vmScripts').setupCommand, ' -i ', variables('clusterSpec').totalMemberCountExcludingLast)]"
				}
			},
			"adminPassword": {
				"value": "[parameters('adminPassword')]"
			}
		}
	}
}
```

Observe cómo el recurso `lastnode-resources` pasa un `machineSettings.commandToExecute` ligeramente diferente a la plantilla vinculada. Esto se debe a que, para el último nodo, además del servidor de Redis instalado, debe llamar a un script para configurar el clúster de Redis (lo que debe llevarse a cabo solo después de que todos los servidores de Redis estén en funcionamiento).

Otro fragmento interesante es el relacionado con las extensiones de máquinas virtuales de `CustomScriptForLinux`. Se instalan como un tipo de recurso independiente, con una dependencia en cada nodo del clúster. En este caso, esto se usa para instalar y configurar Redis en cada nodo de la máquina virtual. Echemos un vistazo a un fragmento de código de la plantilla node-resources.json que las usa:

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat('vmMember', parameters('machineSettings').machineIndex, '/installscript')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('commonSettings').location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', 'vmMember', parameters('machineSettings').machineIndex)]"
    ],
    "properties": {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.2",
        "settings": {
            "fileUris": "[parameters('machineSettings').vmScripts]",
            "commandToExecute": "[parameters('machineSettings').commandToExecute]"
        }
    }
}
```

Puede ver que este recurso depende de la máquina virtual del recurso que ya se está implementando (`Microsoft.Compute/virtualMachines/vmMember<X>`, donde `<X>` es el parámetro `machineSettings.machineIndex`, que es el índice de la máquina virtual que se pasó a este script mediante la función **copyindex()**).

Al familiarizarse con los demás archivos incluidos en esta implementación, podrá entender todos los detalles y los procedimientos recomendados necesarios para organizar y coordinar las complejas estrategias de implementación de las soluciones de múltiples nodos, basadas en cualquier tecnología, aprovechando las plantillas del Administrador de recursos de Azure. Aunque no es obligatorio, un enfoque recomendado consiste en estructurar los archivos de plantillas, tal como se muestra en el diagrama siguiente:

![redis-template-structure](media/virtual-machines-redis-template/redis-template-structure.png)

Básicamente, se sugiere este enfoque para:

- Definir el archivo de plantilla principal como punto central de coordinación para todas las actividades de implementación específicas y aprovechar la vinculación de las plantillas para invocar ejecuciones de subplantillas.
- Crear archivos de plantillas específicas que implementarán todos los recursos compartidos en todas las demás tareas de implementación específicas (cuentas de almacenamiento, configuración de red virtual, etc.). Esto se puede reutilizar en gran medida entre las implementaciones que tienen requisitos similares en cuanto a una infraestructura común.
- Incluir plantillas de recursos opcionales para identificar requisitos específicos de un recurso determinado.
- Para los miembros idénticos de un grupo de recursos (nodos de un clúster, etc.), crear plantillas específicas que aprovechen bucles de recursos a fin de implementar varias instancias con propiedades únicas.
- Para todas las tareas de implementación posteriores (instalación del producto, configuraciones, etc.), aprovechar las extensiones de implementación de scripts y crear scripts específicos de cada tecnología.

Para obtener más información, consulte [Idioma de la plantilla del Administrador de recursos de Azure](../resource-group-authoring-templates.md).

<!---HONumber=AcomDC_1203_2015-->