<properties 
   pageTitle="Implementar una máquina virtual con una dirección IP pública estática mediante una plantilla en el Administrador de recursos | Microsoft Azure"
   description="Aprender a implementar las máquinas virtuales con una dirección IP pública estática mediante una plantilla en el Administrador de recursos"
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
   ms.date="01/08/2016"
   ms.author="telmos" />

# Implementar una máquina virtual con una dirección IP pública estática mediante una plantilla

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## Recursos de IP pública en un archivo de plantilla

Puede ver y descargar la [plantilla de ejemplo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json).

En la sección siguiente se muestra la definición del recurso de IP pública, basada en el escenario anterior.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[variables('webVMSetting').pipName]",
        "location": "[variables('location')]",
        "properties": {
          "publicIPAllocationMethod": "Static"
        },
        "tags": {
          "displayName": "PublicIPAddress - Web"
        }
      },

Observe la propiedad **publicIPAllocationMethod**, que se establece en *Static*. Esta propiedad puede ser *Dynamic* (valor predeterminado) o *Static*. Si se establece en estática, se garantiza que nunca cambiará la dirección IP para esta dirección IP pública.

En la sección siguiente se muestra la asociación de la dirección IP pública anterior con una NIC.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "[variables('webVMSetting').nicName]",
        "location": "[variables('location')]",
        "tags": {
          "displayName": "NetworkInterface - Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Network/publicIPAddresses/', variables('webVMSetting').pipName)]",
          "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
          "ipConfigurations": [
            {
              "name": "ipconfig1",
              "properties": {
                "privateIPAllocationMethod": "Static",
                "privateIPAddress": "[variables('webVMSetting').ipAddress]",
                "publicIPAddress": {
                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('webVMSetting').pipName)]"
                },
                "subnet": {
                  "id": "[variables('frontEndSubnetRef')]"
                }
              }
            }
          ]
        }
      },

Observe la propiedad **publicIPAddress** que señala al **Id.** de un recurso denominado **variables('webVMSetting').pipName**. Se trata del nombre del recurso de IP pública que se muestra arriba.

Por último, la NIC anterior se muestra en la propiedad **networkProfile** de la máquina virtual que se va a crear.

      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('webVMSetting').nicName)]"
          }
        ]
      }

## Implementar la plantilla por medio de un solo clic para implementar

La plantilla de ejemplo disponible en el repositorio público usa un archivo de parámetros que contiene los valores predeterminados utilizados para generar el escenario descrito anteriormente. Para implementar esta plantilla mediante el método de hacer clic para implementar, siga [esta plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/03-Static-public-IP), haga clic en **Implementar en Azure**, reemplace los valores de parámetro predeterminados si es necesario y siga las instrucciones del portal.

## Implementación de la plantilla mediante PowerShell

Para implementar la plantilla que descargó con PowerShell, siga estos pasos.

1. Si nunca usó Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell](powershell-install-configure.md) y siga las instrucciones en los pasos 1 a 3.

2. En una consola de PowerShell, ejecute el cmdlet **New-AzureRmResourceGroup** para crear un nuevo grupo de recursos, si fuera necesario. Si ya tiene un grupo de recursos creado, vaya al paso 3.

		New-AzureRmResourceGroup -Name StaticPublicIP -Location westus

	Resultado esperado:

		ResourceGroupName : StaticPublicIP
		Location          : westus
		ProvisioningState : Succeeded
		Tags              : 
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/StaticPublicIP

3. En una consola de PowerShell, ejecute el cmdlet **AzureRmResourceGroupDeployment nuevo** para implementar la plantilla.

		New-AzureRmResourceGroupDeployment -Name DeployVM -ResourceGroupName PIPTEST `
		    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json `
		    -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json

	Resultado esperado:

		DeploymentName    : DeployVM
		ResourceGroupName : PIPTEST
		ProvisioningState : Succeeded
		Timestamp         : 1/8/2016 7:04:44 PM
		Mode              : Incremental
		TemplateLink      : 
		                    Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/mas
		                    ter/IaaS-Story/03-Static-public-IP/azuredeploy.json
		                    ContentVersion : 1.0.0.0
		                    
		Parameters        : 
		                    Name                      Type                       Value     
		                    ========================  =========================  ==========
		                    vnetName                  String                     WTestVNet 
		                    vnetPrefix                String                     192.168.0.0/16
		                    frontEndSubnetName        String                     FrontEnd  
		                    frontEndSubnetPrefix      String                     192.168.1.0/24
		                    storageAccountNamePrefix  String                     iaasestd  
		                    stdStorageType            String                     Standard_LRS
		                    osType                    String                     Windows   
		                    adminUsername             String                     adminUser 
		                    adminPassword             SecureString                         
		                    
		Outputs           : 

## Implementación la plantilla ARM mediante la CLI de Azure

Para implementar la plantilla ARM mediante la CLI de Azure, siga estos pasos.

1. Si nunca ha usado la CLI de Azure, consulte [Instalación y configuración de la CLI de Azure](xplat-cli.md) y siga las instrucciones hasta el punto donde deba seleccionar su cuenta y suscripción de Azure.
2. Ejecute el comando **azure config mode** para cambiar al modo de Administrador de recursos, como se muestra a continuación.

		azure config mode arm

	Este es el resultado esperado del comando anterior:

		info:    New mode is arm

3. Abra el [archivo de parámetros](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json), seleccione su contenido y guárdelo en un archivo del equipo. Para este ejemplo, guardamos el archivo de parámetros en *parameters.json*.

4. Ejecute el cmdlet **azure group deployment create** para implementar la nueva red virtual mediante la plantilla y los archivos de parámetros que descargó y modificó antes. En la lista que se muestra en la salida se explican los parámetros utilizados.

		azure group create -n PIPTEST2 -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json -e parameters.json

	Resultado esperado:

		info:    Executing command group create
		+ Getting resource group PIPTEST2
		+ Creating resource group PIPTEST2
		info:    Created resource group PIPTEST2
		+ Initializing template configurations and parameters
		+ Creating a deployment
		info:    Created template deployment "azuredeploy"
		data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/PIPTEST2
		data:    Name:                PIPTEST2
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK

<!---HONumber=AcomDC_0128_2016-->