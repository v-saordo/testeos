<properties 
   pageTitle="Información general del Proveedor de recursos de red | Microsoft Azure"
   description="Más información sobre el nuevo Proveedor de recursos de red del Administrador de recursos de Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# Proveedor de recursos de red
Una necesidad que sustenta el éxito de un negocio hoy en día es la capacidad de crear y administrar aplicaciones compatibles con redes de gran escala de una forma ágil, flexible, segura y repetible. El Administrador de recursos de Azure (ARM) permite crear aplicaciones, como una única colección de recursos en grupos de recursos. Estos recursos se administran a través de diversos proveedores de recursos en el Administrador de recursos de Azure.

El Administrador de recursos de Azure utiliza distintos proveedores de recursos para proporcionar acceso a los recursos. Hay tres proveedores de recursos principales: redes, almacenamiento y proceso. En este documento, se describen las características y ventajas del proveedor de recursos de red, entre otros:

- **Metadatos**: puede agregar información a los recursos mediante etiquetas. Estas etiquetas pueden utilizarse para realizar el seguimiento de la utilización de recursos en todas las suscripciones y los grupos de recursos.
- **Mayor control de la red**: los recursos de red están acoplados holgadamente y los puede controlar de forma más detallada. Esto significa que tendrá una mayor flexibilidad en la administración de los recursos de red.
- **Configuración más rápida**: dado que los recursos de red están acoplados holgadamente, puede crear y organizar los recursos de red en paralelo. Esto ha reducido drásticamente el tiempo de configuración.
- **Control de acceso basado en rol (RBAC)**: RBAC proporciona roles predeterminados, con un ámbito de seguridad específico, además de permitir la creación de roles personalizados para una administración segura. 
- **Administración e implementación más sencillas**: resulta más fácil implementar y administrar aplicaciones, ya que puede crear una pila de toda la aplicación como una única colección de recursos en un grupo de recursos. Y más rápido de implementar, ya que es posible hacerlo con solo proporcionar una carga JSON de la plantilla.
- **Personalización rápida**: puede utilizar las plantillas de estilo declarativo para habilitar la personalización repetible y rápida de las implementaciones. 
- **Personalización repetible**: puede utilizar las plantillas de estilo declarativo para habilitar la personalización repetible y rápida de las implementaciones.
- **Interfaces de administración**: puede utilizar cualquiera de las siguientes interfaces para administrar los recursos:
	- API basadas en REST
	- PowerShell
	- .NET SDK
	- SDK de Node.JS
	- SDK de Java
	- Azure CLI
	- Portal de vista previa
	- Lenguaje de la plantilla del Administrador de recursos de Azure

## Recursos de red 
Ahora puede administrar los recursos de red de forma independiente, en lugar de tener hacerlo a través de un recurso de proceso único (una máquina virtual). Esto garantiza un mayor grado de flexibilidad y agilidad a la hora de componer una infraestructura compleja y de gran escala en un grupo de recursos.

A continuación se presenta una vista conceptual de una implementación de ejemplo que implica una aplicación de varios niveles. Todos los recursos que vea, por ejemplo, las NIC, las direcciones IP públicas y las máquinas virtuales, pueden administrarse de forma independiente.

![Modelo de recursos de red](./media/resource-groups-networking/Figure2.png)

Cada recurso contiene un conjunto común de propiedades y un conjunto de propiedades individual. Las propiedades comunes son:

|Propiedad|Descripción|Valores de ejemplo|
|---|---|---|
|**name**|Nombre de recurso único. Cada tipo de recurso tiene sus propias restricciones de nomenclatura.|PIP01, VM01, NIC01|
|**ubicación**|Región de Azure en la que reside el recurso.|westus, eastus|
|**id**|URI único basado en la identificación|/subscriptions/<subGUID>/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP|

Puede comprobar las propiedades individuales de los recursos en las secciones siguientes.

[AZURE.INCLUDE [virtual-networks-nrp-pip-include](../../includes/virtual-networks-nrp-pip-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nic-include](../../includes/virtual-networks-nrp-nic-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nsg-include](../../includes/virtual-networks-nrp-nsg-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-udr-include](../../includes/virtual-networks-nrp-udr-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vnet-include](../../includes/virtual-networks-nrp-vnet-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-dns-include](../../includes/virtual-networks-nrp-dns-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-lb-include](../../includes/virtual-networks-nrp-lb-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-appgw-include](../../includes/virtual-networks-nrp-appgw-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vpn-include](../../includes/virtual-networks-nrp-vpn-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-tm-include](../../includes/virtual-networks-nrp-tm-include.md)]

## Interfaces de administración
Puede administrar los recursos de redes de Azure mediante distintas interfaces. En este documento, nos centraremos en las interfaces API de REST y las plantillas.

### API de REST 
Como se mencionó anteriormente, se pueden administrar los recursos de red a través de una variedad de interfaces, incluidas API de REST, .NET SDK, SDK de Node.JS, SDK de Java, PowerShell, CLI, Portal de Azure y plantillas.

Las API de REST se ajustan a la especificación del protocolo HTTP 1.1. A continuación se presenta la estructura general de URI de la API:

	https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

Y los parámetros entre llaves representan los elementos siguientes:

- **subscription-id**: identificador de su suscripción a Azure.
- **resource-provider-namespace**: espacio de nombres para el proveedor que se utiliza. El valor para el proveedor de recursos de red es *Microsoft.Network*.
- **region-name**: el nombre de la región de Azure.

Se admiten los siguientes métodos HTTP cuando se realizan llamadas a la API de REST:

- **PUT**: se utiliza para crear un recurso de un tipo determinado, modificar una propiedad de recurso o cambiar una asociación entre recursos. 
- **GET**: se utiliza para recuperar información para un recurso aprovisionado.
- **DELETE**: se utiliza para eliminar un recurso existente.

La solicitud y la respuesta se ajustan a un formato de carga JSON. Para obtener más información, consulte [API de administración de recursos de Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx).

### Lenguaje de la plantilla del Administrador de recursos de Azure
Además de administrar recursos de forma imperativa (a través de API o SDK), también puede utilizar un estilo de programación declarativo para crear y administrar recursos de red mediante el lenguaje de la plantilla del Administrador de recursos de Azure.

A continuación se proporciona una representación de una plantilla de ejemplo:

	{
	  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	  "contentVersion": "<version-number-of-template>",
	  "parameters": { <parameter-definitions-of-template> },
	  "variables": { <variable-definitions-of-template> },
	  "resources": [ { <definition-of-resource-to-deploy> } ],
	  "outputs": { <output-of-template> }    
	}

La plantilla es principalmente una descripción de JSON de los recursos y los valores de instancia insertados a través de parámetros. El ejemplo siguiente puede utilizarse para crear una red virtual con dos subredes.

	{
	    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
	    "contentVersion": "1.0.0.0",
	    "parameters" : {
	      "location": {
	        "type": "String",
	        "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
	        "metadata" : {
	          "Description" : "Deployment location"
	        }
	      },
	      "virtualNetworkName":{
	        "type" : "string",
	        "defaultValue":"myVNET",
	        "metadata" : {
	          "Description" : "VNET name"
	        }
	      },
	      "addressPrefix":{
	        "type" : "string",
	        "defaultValue" : "10.0.0.0/16",
	        "metadata" : {
	          "Description" : "Address prefix"
	        }
	
	      },
	      "subnet1Name": {
	        "type" : "string",
	        "defaultValue" : "Subnet-1",
	        "metadata" : {
	          "Description" : "Subnet 1 Name"
	        }
	      },
	      "subnet2Name": {
	        "type" : "string",
	        "defaultValue" : "Subnet-2",
	        "metadata" : {
	          "Description" : "Subnet 2 name"
	        }
	      },
	      "subnet1Prefix" : {
	        "type" : "string",
	        "defaultValue" : "10.0.0.0/24",
	        "metadata" : {
	          "Description" : "Subnet 1 Prefix"
	        }
	      },
	      "subnet2Prefix" : {
	        "type" : "string",
	        "defaultValue" : "10.0.1.0/24",
	        "metadata" : {
	          "Description" : "Subnet 2 Prefix"
	        }
	      }
	    },
	    "resources": [
	    {
	      "apiVersion": "2015-05-01-preview",
	      "type": "Microsoft.Network/virtualNetworks",
	      "name": "[parameters('virtualNetworkName')]",
	      "location": "[parameters('location')]",
	      "properties": {
	        "addressSpace": {
	          "addressPrefixes": [
	            "[parameters('addressPrefix')]"
	          ]
	        },
	        "subnets": [
	          {
	            "name": "[parameters('subnet1Name')]",
	            "properties" : {
	              "addressPrefix": "[parameters('subnet1Prefix')]"
	            }
	          },
	          {
	            "name": "[parameters('subnet2Name')]",
	            "properties" : {
	              "addressPrefix": "[parameters('subnet2Prefix')]"
	            }
	          }
	        ]
	      }
	    }
	    ]
	}

Tiene la opción de proporcionar los valores de los parámetros manualmente cuando se utiliza una plantilla, o bien puede usar un archivo de parámetros. El ejemplo siguiente muestra un posible conjunto de valores de parámetros para usar con la plantilla anterior:

	{
	  "location": {
	      "value": "East US"
	  },
	  "virtualNetworkName": {
	      "value": "VNET1"
	  },
	  "subnet1Name": {
	      "value": "Subnet1"
	  },
	  "subnet2Name": {
	      "value": "Subnet2"
	  },
	  "addressPrefix": {
	      "value": "192.168.0.0/16"
	  },
	  "subnet1Prefix": {
	      "value": "192.168.1.0/24"
	  },
	  "subnet2Prefix": {
	      "value": "192.168.2.0/24"
	  }
	}


Las principales ventajas derivadas del uso de plantillas son las siguientes:

- Puede crear una infraestructura compleja en un grupo de recursos de estilo declarativo. La coordinación de la creación de recursos, incluida la administración de dependencias, se controla mediante el Administrador de recursos de Azure. 
- La infraestructura se puede crear de una manera repetible en diferentes regiones y dentro de una misma región cambiando simplemente parámetros. 
- El estilo declarativo permite reducir el tiempo necesario para generar las plantillas y distribuir la infraestructura. 

Para ver plantillas de ejemplo, consulte [Plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates).

Para obtener más información sobre el lenguaje de la plantilla del Administrador de recursos de Azure, vea [Idioma de la plantilla del administrador de recursos de Azure](../resource-group-authoring-templates.md).

La plantilla del ejemplo anterior utiliza la red virtual y los recursos de la subred. Hay otros recursos de red que se pueden utilizar, como se muestra a continuación:

### Uso de una plantilla

Puede implementar servicios en Azure desde una plantilla mediante PowerShell, CLI de Azure o mediante un clic para implementar desde GitHub. Para implementar servicios de una plantilla en GitHub, ejecute los siguientes pasos:

1. Abra el archivo de plantilla 3 desde GitHub. Como ejemplo, abra [Red virtual con dos subredes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network).
2. Haga clic en **Implementación en Azure**, y, a continuación, inicie sesión en el Portal de Azure con sus credenciales.
3. Compruebe la plantilla y, a continuación, haga clic en **Guardar**.
4. Haga clic en **Editar parámetros** y seleccione una ubicación, como *West US*, para la red virtual y las subredes.
5. Si es necesario, cambie los parámetros **ADDRESSPREFIX** y **SUBNETPREFIX** y, a continuación, haga clic en **Aceptar**.
6. Haga clic en **Seleccionar un grupo de recursos** y, a continuación, haga clic en el grupo de recursos al que desea agregar la red virtual y las subredes. Como alternativa, puede crear un nuevo grupo de recursos haciendo clic en **Crear nuevo**.
3. Haga clic en **Crear**. Observe el icono que muestra **Implementación de plantillas de aprovisionamiento**. Una vez que se realiza la implementación, verá una pantalla similar a la siguiente.

![Implementación de plantilla de ejemplo](./media/resource-groups-networking/Figure6.png)


## Otras referencias

[Referencia de API de redes de Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)

[Referencia de Azure PowerShell para redes](https://msdn.microsoft.com/library/azure/mt163510.aspx)

[Idioma de la plantilla del Administrador de recursos de Azure](../resource-group-authoring-templates.md)

[Redes de Azure: plantillas utilizadas habitualmente](https://github.com/Azure/azure-quickstart-templates)

[Proveedor de recursos de proceso](../virtual-machines-azurerm-versus-azuresm)

[Información general del Administrador de recursos de Azure](../resource-group-overview)

[Control de acceso basado en rol en el Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn906885.aspx)

[Uso de etiquetas en el Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn848368.aspx)

[Implementaciones de plantilla](https://msdn.microsoft.com/library/azure/dn790549.aspx)

de hoy

<!---HONumber=AcomDC_1217_2015-->