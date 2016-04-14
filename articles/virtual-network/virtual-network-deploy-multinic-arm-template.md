<properties 
   pageTitle="Implementación de varias máquinas virtuales de NIC con una plantilla en el Administrador de recursos | Microsoft Azure"
   description="Aprenda a implementar varias máquinas virtuales de NIC con la plantilla en el Administrador de recursos"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos" />

# Implementación de varias máquinas virtuales de NIC con una plantilla

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-deploy-multinic-classic-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Puesto que en este momento no puede tener máquinas virtuales con una sola NIC y las máquinas virtuales con varias NIC en el mismo grupo de recursos, se implementarán los servidores back-end en un grupo de recursos y todos los demás componentes en otro grupo de seguridad. Los pasos siguientes usan un grupo de recursos denominado *IaaSStory* para el grupo de recursos principal y *IaaSStory-back-end* para los servidores back-end.

## Requisitos previos

Antes de implementar los servidores back-end, debe implementar el grupo de recursos principal con todos los recursos necesarios para este escenario. Para implementar estos recursos, siga estos pasos.

1. Vaya a [la página de plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. En la página de la plantilla, a la derecha de **Grupo de recursos primarios**, haga clic en **Implementar en Azure**.
3. Si es necesario, cambie los valores de parámetro y siga los pasos en el Portal de vista previa de Azure para implementar el grupo de recursos.

> [AZURE.IMPORTANT] Asegúrese de que los nombres de cuenta de almacenamiento sean únicos. No puede tener nombres de cuenta de almacenamiento duplicados en Azure.

## Descripción de la plantilla de implementación

Antes de implementar la plantilla proporcionada con esta documentación, asegúrese de que entiende qué hace. Los pasos siguientes proporcionan una buena descripción general de la plantilla en cuestión.

1. Vaya a [la página de plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Haga clic en **azuredeploy.json** para abrir el archivo de plantilla.
3. Observe el parámetro *osType* mostrado a continuación. Este parámetro se utiliza para seleccionar qué imagen de máquina virtual usar para el servidor de base de datos, junto con varias configuraciones relacionadas del sistema operativo.

	    "osType": {
	      "type": "string",
	      "defaultValue": "Windows",
	      "allowedValues": [
	        "Windows",
	        "Ubuntu"
	      ],
	      "metadata": {
	        "description": "Type of OS to use for VMs: Windows or Ubuntu."
	      }
	    },

4. Desplácese hacia abajo hasta la lista de variables y compruebe la definición de las variables **dbVMSetting**, que aparecen a continuación. Recibe uno de los elementos de la matriz contenidos en la variable **dbVMSettings**. Si está familiarizado con la terminología de desarrollo de software, puede ver la variable **dbVMSettings** como una tabla hash o un diccionario.

		"dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"

5. Suponga que decide implementar máquinas virtuales de Windows que ejecuten SQL en el back-end. Entonces, el valor de **osType** sería *Windows*, y la variable **dbVMSetting** contendría el elemento que se muestra a continuación, que representa el primer valor de la variable **dbVMSettings**.

	      "Windows": {
	        "vmSize": "Standard_DS3",
	        "publisher": "MicrosoftSQLServer",
	        "offer": "SQL2014SP1-WS2012R2",
	        "sku": "Standard",
	        "version": "latest",
	        "vmName": "DB",
	        "osdisk": "osdiskdb",
	        "datadisk": "datadiskdb",
	        "nicName": "NICDB",
	        "ipAddress": "192.168.2.",
	        "extensionDeployment": "",
	        "avsetName": "ASDB",
	        "remotePort": 3389,
	        "dbPort": 1433
	      },

6. Observe que **vmSize** contiene el valor *Standard\_DS3*. Permiten solo ciertos tamaños de máquina virtual para el uso de varias tarjetas NIC. Puede comprobar los tamaños de máquina virtual que tienen habilitado varias NIC visitando [Introducción a varios NIC](virtual-networks-multiple-nics.md).
7. Desplácese hacia abajo hasta **recursos** y observe el primer elemento. Describe una cuenta de almacenamiento. Esta cuenta de almacenamiento se usará para mantener los discos de datos usados por cada máquina virtual de la base de datos. En este escenario, cada máquina virtual de base de datos tiene un disco de sistema operativo almacenado en almacenamiento normal y dos discos de datos almacenados en el almacenamiento SSD (Premium).

	    {
	      "apiVersion": "2015-05-01-preview",
	      "type": "Microsoft.Storage/storageAccounts",
	      "name": "[parameters('prmStorageName')]",
	      "location": "[variables('location')]",
	      "tags": {
	        "displayName": "Storage Account - Premium"
	      },
	      "properties": {
	        "accountType": "[parameters('prmStorageType')]"
	      }
	    },

8. Desplácese hacia abajo hasta el siguiente recurso, como se muestra a continuación. Este recurso representa la NIC usada para el acceso a la base de datos en cada máquina virtual de la base de datos. Observe el uso de la función **copiar** en este recurso. La plantilla le permite implementar todas las máquinas virtuales que desee, según el parámetro **dbCount**. Por lo tanto, debe crear la misma cantidad de NIC para el acceso de la base de datos, uno para cada máquina virtual.

	    {
	      "apiVersion": "2015-06-15",
	      "type": "Microsoft.Network/networkInterfaces",
	      "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
	      "location": "[variables('location')]",
	      "tags": {
	        "displayName": "NetworkInterfaces - DB DA"
	      },
	      "copy": {
	        "name": "dbniccount",
	        "count": "[parameters('dbCount')]"
	      },
	      "properties": {
	        "ipConfigurations": [
	          {
	            "name": "ipconfig1",
	            "properties": {
	              "privateIPAllocationMethod": "Static",
	              "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
	              "subnet": {
	                "id": "[variables('backEndSubnetRef')]"
	              }
	            }
	          }
	        ]
	      }
	    },

9. Desplácese hacia abajo hasta el siguiente recurso, como se muestra a continuación. Este recurso representa la NIC usada para la administración en cada máquina virtual de la base de datos. Una vez más, necesita una de estas NIC para cada máquina virtual de base de datos. Observe el elemento **networkSecurityGroup** que vincula un grupo de seguridad de red que permite el acceso a RDP o SSH solo a esta NIC.

	    {
	      "apiVersion": "2015-06-15",
	      "type": "Microsoft.Network/networkInterfaces",
	      "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
	      "location": "[variables('location')]",
	      "tags": {
	        "displayName": "NetworkInterfaces - DB RA"
	      },
	      "copy": {
	        "name": "dbniccount",
	        "count": "[parameters('dbCount')]"
	      },
	      "properties": {
	        "ipConfigurations": [
	          {
	            "name": "ipconfig1",
	            "properties": {
	              "networkSecurityGroup": {
	                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
	              },
	              "privateIPAllocationMethod": "Static",
	              "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
	              "subnet": {
	                "id": "[variables('backEndSubnetRef')]"
	              }
	            }
	          }
	        ]
	      }
	    },

10. Desplácese hacia abajo hasta el siguiente recurso, como se muestra a continuación. Este recurso representa un conjunto de disponibilidad que compartir con todas las máquinas virtuales de base de datos. De este modo, se garantiza que siempre habrá una máquina virtual en el conjunto que se ejecuta durante el mantenimiento.

	    {
	      "apiVersion": "2015-06-15",
	      "type": "Microsoft.Compute/availabilitySets",
	      "name": "[variables('dbVMSetting').avsetName]",
	      "location": "[variables('location')]",
	      "tags": {
	        "displayName": "AvailabilitySet - DB"
	      }
	    },

11. Desplácese hacia abajo hasta el siguiente recurso. Este recurso representa las máquinas virtuales de base de datos, como se muestran en las primeras líneas siguientes. Observe el uso de la función **copiar** de nuevo asegurándose de que se crean varias máquinas virtuales según el parámetro **dbCount**. Observe también la colección **dependsOn**. Muestra dos NIC que tienen que crearse antes de implementar la máquina virtual, junto con el conjunto de disponibilidad y la cuenta de almacenamiento.

		  "apiVersion": "2015-06-15",
		  "type": "Microsoft.Compute/virtualMachines",
		  "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
		  "location": "[variables('location')]",
		  "dependsOn": [
		    "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
		    "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
		    "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
		    "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
		  ],
		  "tags": {
		    "displayName": "VMs - DB"
		  },
		  "copy": {
		    "name": "dbvmcount",
		    "count": "[parameters('dbCount')]"
		  },

12. Desplácese hacia abajo en el recurso de máquina virtual para el elemento **networkProfile** como se muestra a continuación. Observe que existen dos NIC que hacen referencia a cada máquina virtual. Al crear varias NIC para una máquina virtual, debe establecer la propiedad **principal** de esas NIC en *true* y el resto en *false*.

        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
              "properties": { "primary": true }
            },
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
              "properties": { "primary": false }
            }
          ]
        }
      }

## Implementar la plantilla ARM por medio de un solo clic para implementar

> [AZURE.IMPORTANT] Asegúrese de que sigue los [pasos previos](#Pre-requisites) antes de seguir las instrucciones que aparecen a continuación.

La plantilla de ejemplo disponible en el repositorio público usa un archivo de parámetros que contiene los valores predeterminados utilizados para generar el escenario descrito anteriormente. Para implementar esta plantilla mediante el método de hacer clic para implementar, siga [este vínculo](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC), a la derecha de **Grupo de recursos back-end (consulte la documentación)**, haga clic en **Implementar en Azure**, reemplace los valores de parámetro predeterminados si es necesario y siga las instrucciones del portal.

La siguiente ilustración muestra el contenido del nuevo grupo de recursos después de la implementación.

![Grupo de recursos back-end](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## Implementación de la plantilla mediante PowerShell

Para implementar la plantilla que descargó con PowerShell, siga estos pasos.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

3. Ejecute el cmdlet **`New-AzureRmResourceGroup`** para crear un grupo de recursos con esta plantilla.

		New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
		    -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
		    -TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'	

	Resultado esperado:

		ResourceGroupName : IaaSStory-Backend
		Location          : westus
		ProvisioningState : Succeeded
		Tags              : 
		Permissions       : 
		                    Actions  NotActions
		                    =======  ==========
		                    *                  
		                    
		Resources         : 
		                    Name                 Type                                 Location
		                    ===================  ===================================  ========
		                    ASDB                 Microsoft.Compute/availabilitySets   westus  
		                    DB1                  Microsoft.Compute/virtualMachines    westus  
		                    DB2                  Microsoft.Compute/virtualMachines    westus  
		                    NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
		                    NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
		                    NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
		                    NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
		                    wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  
		                    
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## Implementación la plantilla ARM mediante la CLI de Azure

Para implementar la plantilla ARM mediante la CLI de Azure, siga estos pasos.

1. Si nunca ha usado la CLI de Azure, consulte [Instalación y configuración de la CLI de Azure](xplat-cli.md) y siga las instrucciones hasta el punto donde deba seleccionar su cuenta y suscripción de Azure.
2. Ejecute el comando **`azure config mode`** para cambiar al modo de Administrador de recursos, como se muestra a continuación.

		azure config mode arm

	Este es el resultado esperado del comando anterior:

		info:    New mode is arm

3. Abra el [archivo de parámetros](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json), seleccione su contenido y guárdelo en un archivo en el equipo. Para este ejemplo, guardamos el archivo de parámetros en *parameters.json*.

4. Ejecute el cmdlet **`azure group deployment create`** para implementar la nueva red virtual con la plantilla y los archivos de parámetros que descargó y modificó anteriormente. En la lista que se muestra en la salida se explican los parámetros utilizados.

		azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json

	Resultado esperado:

		info:    Executing command group create
		+ Getting resource group IaaSStory-Backend
		+ Creating resource group IaaSStory-Backend
		info:    Created resource group IaaSStory-Backend
		+ Initializing template configurations and parameters
		+ Creating a deployment
		info:    Created template deployment "azuredeploy"
		data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
		data:    Name:                IaaSStory-Backend
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK

<!---HONumber=AcomDC_0211_2016-->