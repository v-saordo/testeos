<properties
	pageTitle="Prácticas recomendadas para controlar el estado en las plantillas del Administrador de recursos de Azure"
	description="Muestra los enfoques recomendados para usar objetos complejos a fin de compartir datos de estado con plantillas vinculadas y plantillas del Administrador de recursos de Azure"
	services="azure-resource-manager"
	documentationCenter=""
	authors="mmercuri"
	manager="georgem"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="mmercuri"/>

# Uso compartido del estado en las plantillas del Administrador de recursos de Azure

En este tema se muestran prácticas recomendadas para administrar y compartir el estado de una plantilla del Administrador de recursos de Azure y a través de plantillas vinculadas. Los parámetros y variables que se muestran en este tema son ejemplos del tipo de objetos que puede definir para organizar con facilidad sus requisitos de implementación. Desde estos ejemplos, puede implementar sus propios objetos con valores de propiedad que tengan sentido para su entorno.

Este tema forma parte de un artículo más extenso. Si desea leer el artículo completo, descargue [World Class ARM Templates Considerations and Proven Practices] (Consideraciones y prácticas comprobadas sobre plantillas ARM de clase mundial) (http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).


## Uso de objetos complejos para compartir el estado

En lugar de ofrecer una plantilla que proporcione flexibilidad total e incontables variaciones, un patrón común es proporcionar la posibilidad de seleccionar configuraciones conocidas, de hecho, tamaños de camiseta estándar como pequeña, mediana y grande. Otros ejemplos de tamaños de camiseta son ofertas de productos, como edición para la comunidad o edición para impresa. En otros casos, pueden ser configuraciones de una tecnología específicas de la carga de trabajo, como MapReduce o NoSQL.

Con objetos complejos, puede crear las variables que contengan colecciones de datos, a veces conocidas como "contenedores de propiedades" y usar esos datos para dirigir la declaración de recursos de tu plantilla. Este enfoque proporciona configuraciones bien conocidas de tamaños variables que están preconfiguradas para los clientes. Sin configuraciones conocidas, los clientes finales deben determinar el tamaño del clúster por su cuenta, tener en cuenta las restricciones de recursos de plataforma y realizar cálculos matemáticos para identificar el particionamiento resultante de las cuentas de almacenamiento y otros recursos (debido a restricciones en el tamaño del clúster y los recursos). Las configuraciones conocidas permiten a los clientes seleccionar fácilmente el tamaño correcto de camiseta, es decir, una implementación determinada. Además de crear una experiencia mejor para el cliente, un número pequeño de configuraciones conocidas es más fácil de mantener y puede ayudarle a ofrecer un nivel mayor de densidad.


En el ejemplo siguiente se muestra cómo definir variables que contienen objetos complejos para representar colecciones de datos. Las colecciones definen valores que se usan para el tamaño de la máquina virtual, la configuración de red, la configuración del sistema operativo y la configuración de disponibilidad.

    "variables": {
      "tshirtSizeLarge": {
        "vmSize": "Standard_A4",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
        "vmCount": 3,
        "slaveCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1,1],
          "jumpbox": 0
        }
      },
      "osSettings": {
        "scripts": [
          "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
          "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
        ],
        "imageReference": {
	  "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "14.04.2-LTS",
          "version": "latest"
        }
      },
      "networkSettings": {
        "vnetName": "[parameters('virtualNetworkName')]",
        "addressPrefix": "10.0.0.0/16",
        "subnets": {
          "dmz": {
            "name": "dmz",
            "prefix": "10.0.0.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          },
          "data": {
            "name": "data",
            "prefix": "10.0.1.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          }
        }
      },
      "availabilitySetSettings": {
        "name": "pgsqlAvailabilitySet",
        "fdCount": 3,
        "udCount": 5
      }
    }

Luego, puede hacer referencia a estas variables más adelante en la plantilla. La posibilidad de hacer referencia a variables con nombre y sus propiedades simplifica la sintaxis de la plantilla y facilita la comprensión del contexto. En el ejemplo siguiente se define un recurso para implementar mediante los objetos mostrados anteriormente para definir los valores. Por ejemplo, tenga en cuenta que el tamaño de la máquina virtual se establece al recuperar el valor de `variables('tshirtSize').vmSize` mientras que el valor del tamaño del disco se recupera de `variables('tshirtSize').diskSize`. Además, el URI de una plantilla vinculada se establece con el valor de `variables('tshirtSize').vmTemplate`.

    "name": "master-node",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared')]"
    ],
    "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('tshirtSize').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "value": "[parameters('adminPassword')]"
          },
          "replicatorPassword": {
            "value": "[parameters('replicatorPassword')]"
          },
          "osSettings": {
	    "value": "[variables('osSettings')]"
          },
          "subnet": {
            "value": "[variables('networkSettings').subnets.data]"
          },
          "commonSettings": {
            "value": {
              "region": "[parameters('region')]",
              "adminUsername": "[parameters('adminUsername')]",
              "namespace": "ms"
            }
          },
          "storageSettings": {
            "value":"[variables('tshirtSize').storage]"
          },
          "machineSettings": {
            "value": {
              "vmSize": "[variables('tshirtSize').vmSize]",
              "diskSize": "[variables('tshirtSize').diskSize]",
              "vmCount": 1,
              "availabilitySet": "[variables('availabilitySetSettings').name]"
            }
          },
          "masterIpAddress": {
            "value": "0"
          },
          "dbType": {
            "value": "MASTER"
          }
        }
      }
    }

## Paso del estado a una plantilla y sus plantillas vinculadas

Puede compartir información de estado en una plantilla y sus plantillas vinculadas mediante:

- Parámetros que se proporcionan directamente a la plantilla principal durante la implementación
- Parámetros, variables estáticas y variables generadas que la plantilla principal comparte con sus plantillas vinculadas

### Parámetros comunes proporcionados a la plantilla principal

En la tabla siguiente se enumeran los parámetros usados normalmente en las plantillas.

**Los parámetros usados con frecuencia se pasan a la plantilla principal.**

Nombre | Valor | Descripción
---- | ----- | -----------
location | Cadena de una lista restringida de regiones de Azure | La ubicación donde se implementarán los recursos.
storageAccountNamePrefix | String | Nombre DNS único de la cuenta de almacenamiento donde se colocarán los discos de la máquina virtual
domainName | String | Nombre de dominio de la VM de JumpBox accesible públicamente en el formato: **{domainName}.{location}.cloudapp.com** Por ejemplo: **mydomainname.westus.cloudapp.azure.com**
adminUsername | String | Nombre de usuario de las máquinas virtuales
adminPassword | String | Contraseña de las máquinas virtuales
tshirtSize | Cadena de una lista restringida de tamaños de camiseta ofrecidos | El tamaño de la unidad de escalado con nombre para aprovisionar. Por ejemplo, "Pequeña", "Mediana", "Grande"
virtualNetworkName | String | Nombre de la red virtual que el consumidor desea usar.
enableJumpbox | Cadena de una lista restringida (habilitada o deshabilitada) | Parámetro que identifica si se habilitará un JumpBox para el entorno. Valores: "habilitado", "deshabilitado"

### Parámetros enviados a plantillas vinculadas

Al conectarse a plantillas vinculadas, a menudo usará una combinación de variables estáticas y generadas.

#### Variables estáticas

Las variables estáticas se suelen usar para proporcionar valores base, como direcciones URL, que se utilizan en toda la plantilla o como valores que se usan para crear valores para variables dinámicas.

En el fragmento de la plantilla siguiente, *templateBaseUrl* especifica la ubicación de la raíz de la plantilla en GitHub. La línea siguiente crea una nueva variable *sharedTemplateUrl* que concatena el valor de *templateBaseUrl* con el nombre conocido de la plantilla de recursos compartidos. Debajo de esta, una variable de objeto complejo se usa para almacenar un tamaño de camiseta, donde *templateBaseUrl* se concatena para especificar la ubicación de la plantilla de configuración conocida almacenada en la propiedad *vmTemplate*.

La ventaja de este enfoque es que puede mover, bifurcar o usar fácilmente la plantilla como base para una nueva. Si cambia la ubicación de la plantilla, solo tendrá que cambiar la variable estática en el lugar, la plantilla principal, que la pasa en todas las plantillas.

    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "tshirtSizeSmall": {
      "vmSize": "Standard_A1",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
      "vmCount": 2,
      "slaveCount": 1,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 1,
        "pool": "db",
        "map": [0,0],
        "jumpbox": 0
      }
    }

#### Variables generadas

Además de las variables estáticas, hay una serie de variables que se generan dinámicamente. En esta sección se identifican algunos de los tipos comunes de variables generadas.

##### tshirtSize

Al llamar a la plantilla principal, puede seleccionar un tamaño de camiseta de un número fijo de opciones, que normalmente incluyen valores como *Pequeña*, *Mediana* y *Grande*.

En la plantilla principal, esta opción aparece como parámetro, por ejemplo *tshirtSize*:

    "tshirtSize": {
      "type": "string",
      "defaultValue": "Small",
      "allowedValues": [
        "Small",
        "Medium",
        "Large"
      ],
      "metadata": {
        "Description": "T-shirt size of the MongoDB deployment"
      }
    }

Dentro de la plantilla principal, las variables se corresponden con cada uno de los tamaños. Por ejemplo, si los tamaños disponibles son pequeña, mediana y grande, la sección de variables incluiría variables denominadas *tshirtSizeSmall*, *tshirtSizeMedium* y *tshirtSizeLarge*.

Como se muestra en el siguiente ejemplo, estas variables definen las propiedades de un determinado tamaño de camiseta. Cada uno de ellos identifica el tipo de máquina virtual, el tamaño del disco, la plantilla de recursos de unidad de escalado asociada a la que vincular, el número de instancias, los detalles de la cuenta de almacenamiento y el estado de JumpBox.

El prefijo del nombre de la cuenta de almacenamiento procede de un parámetro proporcionado por el usuario, y la plantilla vinculada es la concatenación de la dirección URL base de la plantilla y el nombre de archivo de una plantilla de recurso de unidad de escalado específica.

    "tshirtSizeSmall": {
      "vmSize": "Standard_A1",
			"diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
      "vmCount": 2,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 1,
        "pool": "db",
        "map": [0,0],
        "jumpbox": 0
      }
    },
    "tshirtSizeMedium": {
      "vmSize": "Standard_A3",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
      "vmCount": 2,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 2,
        "pool": "db",
        "map": [0,1],
        "jumpbox": 0
      }
    },
    "tshirtSizeLarge": {
      "vmSize": "Standard_A4",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
      "vmCount": 3,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 2,
        "pool": "db",
        "map": [0,1,1],
        "jumpbox": 0
      }
    }

La variable *tshirtSize* aparece más abajo en la sección de variables. El final del tamaño de camiseta que ha proporcionado (*Pequeña*, *Mediana*, *Grande*) se concatena con el texto *tshirtSize* para recuperar la variable de objeto complejo asociada con ese tamaño de camiseta:

    "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",

Esta variable se pasa a la plantilla de recursos de unidad de escalado vinculada.

##### networkSettings

En una plantilla de solución de capacidad o con ámbito completo, las plantillas vinculadas suele crean recursos que existen en una red. Un enfoque sencillo es usar un objeto complejo para almacenar la configuración de red y pasarla a las plantillas vinculadas.

A continuación puede verse un ejemplo de comunicación de la configuración de red.

    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    }

##### availabilitySettings

Los recursos creados en las plantillas vinculadas a menudo se colocan en un conjunto de disponibilidad. En el ejemplo siguiente, se especifica el nombre del conjunto de disponibilidad y también el número de dominios de error y dominios de actualización que se van a usar.

    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

Si necesita varios conjuntos de disponibilidad (por ejemplo, uno para los nodos principales y otro para los nodos de datos), puede usar un nombre como prefijo, especificar varios conjuntos de disponibilidad o seguir el modelo mostrado anteriormente para la creación de una variable para un tamaño de camiseta específico.

##### storageSettings

Los detalles de almacenamiento a menudo se comparten con las plantillas vinculadas. En el ejemplo siguiente, un objeto *storageSettings* proporciona detalles sobre los nombres del contenedor y la cuenta de almacenamiento.

    "storageSettings": {
        "vhdStorageAccountName": "[parameters('storageAccountName')]",
        "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
        "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
    }

##### osSettings

Con las plantillas vinculadas, tendrá que pasar la configuración del sistema operativo a varios tipos de nodos a través de diferentes tipos de configuración conocidos. Un objeto complejo es una forma sencilla de almacenar y compartir información del sistema operativo y también facilita la compatibilidad con varias opciones de sistema operativo en la implementación.

En el ejemplo siguiente se muestra un objeto de *osSettings*:

    "osSettings": {
      "imageReference": {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    }

##### machineSettings

Una variable generada, *machineSettings* es un objeto complejo que contiene una combinación de variables principales para crear una nueva máquina virtual: el nombre del usuario administrador y la contraseña, un prefijo para los nombres de máquina virtual y una referencia de imagen del sistema operativo, como se muestra a continuación:

    "machineSettings": {
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "machineNamePrefix": "mongodb-",
        "osImageReference": {
            "publisher": "[variables('osFamilySpec').imagePublisher]",
            "offer": "[variables('osFamilySpec').imageOffer]",
            "sku": "[variables('osFamilySpec').imageSKU]",
            "version": "latest"
        }
    },

Tenga en cuenta que *osImageReference* recupera los valores de la variable *osSettings* definida en la plantilla principal. Eso significa que puede cambiar fácilmente el sistema operativo de una máquina virtual, por completo, o en función de la preferencia de un consumidor de plantilla.

##### vmScripts

El objeto *vmScripts* contiene detalles sobre los scripts que se descargarán y ejecutarán en una instancia de máquina virtual, incluidas las referencias externas e internas. Las referencias externas incluyen la infraestructura. Las referencias internas incluyen el software instalado y la configuración.

La propiedad *scriptsToDownload* se usa para mostrar los scripts que se descargarán en la máquina virtual.

Como muestra el ejemplo siguiente, este objeto también contiene referencias a argumentos de línea de comandos para diferentes tipos de acciones. Estas acciones incluyen la ejecución de la instalación predeterminada de cada nodo individual, una instalación que se ejecuta después de que se implementan todos los nodos y los scripts adicionales que pueden ser específicos de una plantilla determinada.

Este ejemplo es de una plantilla que se usa para implementar MongoDB, lo que requiere un árbitro para proporcionar alta disponibilidad. *arbiterNodeInstallCommand* se ha agregado a *vmScripts* para instalar el árbitro.

En la sección de variables es donde encontrará las variables que definen el texto específico para ejecutar el script con los valores adecuados.

    "vmScripts": {
        "scriptsToDownload": [
            "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
            "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
        ],
        "regularNodeInstallCommand": "[variables('installCommand')]",
        "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
        "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
    },


## Devolución del estado de una plantilla

No solo puede pasar datos a una plantilla, también puede compartir datos de nuevo con la plantilla de llamada. En la sección **Salidas** de una plantilla vinculada, puede proporcionar pares de clave/valor que pueden usarse en la plantilla de origen.

En el ejemplo siguiente se muestra cómo pasar la dirección IP privada generada en una plantilla vinculada.

    "outputs": {
        "masterip": {
            "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].privateIPAddress]",
            "type": "string"
         }
    }

Dentro de la plantilla principal, puede usar esos datos con la sintaxis siguiente:

    "masterIpAddress": {
        "value": "[reference('master-node').outputs.masterip.value]"
    }

## Pasos siguientes
- [Creación de plantillas de Administrador de recursos de Azure](resource-group-authoring-templates.md)
- [Funciones de la plantilla del Administrador de recursos de Azure](resource-group-template-functions.md)

<!---HONumber=AcomDC_0204_2016-->