<properties 
   pageTitle="Control del enrutamiento y uso de aplicaciones virtuales en el Administrador de recursos mediante una plantilla | Microsoft Azure"
   description="Vea cómo controlar el enrutamiento y uso de aplicaciones virtuales en el Administrador de recursos mediante una plantilla"
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
   ms.date="02/23/2016"
   ms.author="telmos" />

#Creación de enrutamientos definidos por el usuario (UDR) en el administrador de recursos mediante una plantilla

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artículo trata sobre el modelo de implementación del Administrador de recursos. También puede [crear rutas definidas por el usuario en el modelo de implementación clásico](virtual-networks-udr-how-to.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

## Recursos de las rutas definidas por el usuario en un archivo de plantilla

Puede ver y descargar la [plantilla de ejemplo](https://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR).

La sección siguiente muestra la definición de las rutas definidas por el usuario de front-end en el archivo **azuredeploy-vnet-nsg-udr.json**, según el escenario anterior.

	"apiVersion": "2015-06-15",
	"type": "Microsoft.Network/routeTables",
	"name": "[parameters('frontEndRouteTableName')]",
	"location": "[resourceGroup().location]",
	"tags": {
	  "displayName": "UDR - FrontEnd"
	},
	"properties": {
	  "routes": [
	    {
	      "name": "RouteToBackEnd",
	      "properties": {
	        "addressPrefix": "[parameters('backEndSubnetPrefix')]",
	        "nextHopType": "VirtualAppliance",
	        "nextHopIpAddress": "[parameters('vmaIpAddress')]"
	      }
	    }
	  ]


Para asociar las rutas definidas por el usuario a la subred front-end, deberá cambiar la definición de la subred en la plantilla y usar el identificador de referencia para las rutas definidas por el usuario.

	"subnets": [
		"name": "[parameters('frontEndSubnetName')]",
		"properties": {
		  "addressPrefix": "[parameters('frontEndSubnetPrefix')]",
		  "networkSecurityGroup": {
		    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('frontEndNSGName'))]"
		  },
		  "routeTable": {
		      "id": "[resourceId('Microsoft.Network/routeTables', parameters('frontEndRouteTableName'))]"
		  }
		},
		...]

Haremos lo mismo para el grupo de seguridad de red de back-end y la subred back-end en la plantilla.

También debe asegurarse de que la máquina virtual **FW1** tiene la propiedad de reenvío de IP habilitada en la NIC, que se usará para recibir y reenviar paquetes. La sección siguiente muestra la definición de la NIC para FW1 en el archivo azuredeploy-nsg-udr.json, según el escenario anterior.

	"apiVersion": "2015-06-15",
	"type": "Microsoft.Network/networkInterfaces",
	"location": "[variables('location')]",
	"tags": {
	  "displayName": "NetworkInterfaces - DMZ"
	},
	"name": "[concat(variables('fwVMSettings').nicName, copyindex(1))]",
	"dependsOn": [
	  "[concat('Microsoft.Network/publicIPAddresses/', variables('fwVMSettings').pipName, copyindex(1))]",
	  "[concat('Microsoft.Resources/deployments/', 'vnetTemplate')]"
	],
	"properties": {
	  "ipConfigurations": [
	    {
	      "name": "ipconfig1",
	      "properties": {
	        "enableIPForwarding": true,
	        "privateIPAllocationMethod": "Static",
	        "privateIPAddress": "[concat('192.168.0.',copyindex(4))]",
	        "publicIPAddress": {
	          "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('fwVMSettings').pipName, copyindex(1)))]"
	        },
	        "subnet": {
	          "id": "[variables('dmzSubnetRef')]"
	        }
	      }
	    }
	  ]
	},
	"copy": {
	  "name": "fwniccount",
	  "count": "[parameters('fwCount')]"
	}

## Implementar la plantilla ARM por medio de un solo clic para implementar

La plantilla de ejemplo disponible en el repositorio público usa un archivo de parámetros que contiene los valores predeterminados utilizados para generar el escenario descrito anteriormente. Para implementar esta plantilla mediante el método de hacer clic para implementar, siga [esta plantilla](https://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR), haga clic en **Implementar en Azure**, reemplace los valores de parámetro predeterminados si es necesario y siga las instrucciones del portal.

## Implementar la plantilla ARM mediante PowerShell

Para implementar la plantilla ARM que descargó con PowerShell, siga estos pasos.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Si es la primera vez que usa Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccionar su suscripción.

2. Ejecute el cmdlet `New-AzureRmResourceGroup` para crear un grupo de recursos.

		New-AzureRmResourceGroup -Name TestRG -Location westus

3. Ejecute el cmdlet `New-AzureRmResourceGroupDeployment` para implementar la plantilla.

		New-AzureRmResourceGroupDeployment -Name DeployUDR -ResourceGroupName TestRG `
		    -TemplateUri https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.json `
		    -TemplateParameterUri https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.parameters.json	    	

	Resultado esperado:

		ResourceGroupName : TestRG
		Location          : westus
		ProvisioningState : Succeeded
		Tags              : 
		Permissions       : 
		                    Actions  NotActions
		                    =======  ==========
		                    *                  
		                    
		Resources         : 
		                    Name                Type                                     Location
		                    ==================  =======================================  ========
		                    ASFW                Microsoft.Compute/availabilitySets       westus  
		                    ASSQL               Microsoft.Compute/availabilitySets       westus  
		                    ASWEB               Microsoft.Compute/availabilitySets       westus  
		                    FW1                 Microsoft.Compute/virtualMachines        westus  
		                    SQL1                Microsoft.Compute/virtualMachines        westus  
		                    SQL2                Microsoft.Compute/virtualMachines        westus  
		                    WEB1                Microsoft.Compute/virtualMachines        westus  
		                    WEB2                Microsoft.Compute/virtualMachines        westus  
		                    NICFW1              Microsoft.Network/networkInterfaces      westus  
		                    NICSQL1             Microsoft.Network/networkInterfaces      westus  
		                    NICSQL2             Microsoft.Network/networkInterfaces      westus  
		                    NICWEB1             Microsoft.Network/networkInterfaces      westus  
		                    NICWEB2             Microsoft.Network/networkInterfaces      westus  
		                    NSG-BackEnd         Microsoft.Network/networkSecurityGroups  westus  
		                    NSG-FrontEnd        Microsoft.Network/networkSecurityGroups  westus  
		                    PIPFW1              Microsoft.Network/publicIPAddresses      westus  
		                    PIPSQL1             Microsoft.Network/publicIPAddresses      westus  
		                    PIPSQL2             Microsoft.Network/publicIPAddresses      westus  
		                    PIPWEB1             Microsoft.Network/publicIPAddresses      westus  
		                    PIPWEB2             Microsoft.Network/publicIPAddresses      westus  
		                    UDR-BackEnd         Microsoft.Network/routeTables            westus  
		                    UDR-FrontEnd        Microsoft.Network/routeTables            westus  
		                    TestVNet            Microsoft.Network/virtualNetworks        westus  
		                    testvnetstorageprm  Microsoft.Storage/storageAccounts        westus  
		                    testvnetstoragestd  Microsoft.Storage/storageAccounts        westus  
		                    
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

## Implementar la plantilla ARM mediante la CLI de Azure

Para implementar la plantilla ARM mediante la CLI de Azure, siga estos pasos.

1. Si nunca ha usado la CLI de Azure, consulte [Instalación y configuración de la CLI de Azure](../xplat-cli-install.md) y siga las instrucciones hasta el punto donde deba seleccionar su cuenta y suscripción de Azure.
2. Ejecute el comando `azure config mode` para cambiar al modo de Administrador de recursos, como se muestra a continuación.

		azure config mode arm

	Este es el resultado esperado del comando anterior:

		info:    New mode is arm

3. Desde el explorador, vaya a ****https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.parameters.json**, copie el contenido del archivo json y péguelo en un archivo nuevo en el equipo. En este escenario, deberá copiar los valores siguientes en un archivo denominado **c:\\udr\\azuredeploy.parameters.json**.

		{
		  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "fwCount": {
		      "value": 1
		    },
		    "webCount": {
		      "value": 2
		    },
		    "sqlCount": {
		      "value": 2
		    }
		  }
		}

4. Ejecute el cmdlet **azure group create** para implementar la nueva red virtual mediante la plantilla y los archivos de parámetros que descargó y modificó anteriormente. En la lista que se muestra en la salida se explican los parámetros utilizados.

		azure group create -n TestRG -l westus --template-uri 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.json' -e 'c:\udr\azuredeploy.parameters.json'

	Resultado esperado:

		info:    Executing command group create
		info:    Getting resource group TestRG
		info:    Updating resource group TestRG
		info:    Updated resource group TestRG
		info:    Initializing template configurations and parameters
		info:    Creating a deployment
		info:    Created template deployment "azuredeploy"
		data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
		data:    Name:                TestRG
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:    
		info:    group create command OK

5. Ejecute el comando **azure group show** para ver los recursos creados en el nuevo grupo de recursos.

		azure group show TestRG

	Resultado esperado
		
		info:    Executing command group show
		info:    Listing resource groups
		info:    Listing resources for the group
		data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
		data:    Name:                TestRG
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:    Resources:
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/ASFW
		data:      Name    : ASFW
		data:      Type    : availabilitySets
		data:      Location: westus
		data:      Tags    : displayName=AvailabilitySet - DMZ
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/ASSQL
		data:      Name    : ASSQL
		data:      Type    : availabilitySets
		data:      Location: westus
		data:      Tags    : displayName=AvailabilitySet - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/ASWEB
		data:      Name    : ASWEB
		data:      Type    : availabilitySets
		data:      Location: westus
		data:      Tags    : displayName=AvailabilitySet - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/FW1
		data:      Name    : FW1
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - DMZ
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/SQL1
		data:      Name    : SQL1
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/SQL2
		data:      Name    : SQL2
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WEB1
		data:      Name    : WEB1
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WEB2
		data:      Name    : WEB2
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICFW1
		data:      Name    : NICFW1
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - DMZ
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL1
		data:      Name    : NICSQL1
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL2
		data:      Name    : NICSQL2
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1
		data:      Name    : NICWEB1
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2
		data:      Name    : NICWEB2
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd
		data:      Name    : NSG-BackEnd
		data:      Type    : networkSecurityGroups
		data:      Location: westus
		data:      Tags    : displayName=NSG - Front End
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
		data:      Name    : NSG-FrontEnd
		data:      Type    : networkSecurityGroups
		data:      Location: westus
		data:      Tags    : displayName=NSG - Remote Access
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPFW1
		data:      Name    : PIPFW1
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - DMZ
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPSQL1
		data:      Name    : PIPSQL1
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPSQL2
		data:      Name    : PIPSQL2
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPWEB1
		data:      Name    : PIPWEB1
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPWEB2
		data:      Name    : PIPWEB2
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd
		data:      Name    : UDR-BackEnd
		data:      Type    : routeTables
		data:      Location: westus
		data:      Tags    : displayName=Route Table - Back End
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd
		data:      Name    : UDR-FrontEnd
		data:      Type    : routeTables
		data:      Location: westus
		data:      Tags    : displayName=UDR - FrontEnd
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		data:      Name    : TestVNet
		data:      Type    : virtualNetworks
		data:      Location: westus
		data:      Tags    : displayName=VNet
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/testvnetstorageprm
		data:      Name    : testvnetstorageprm
		data:      Type    : storageAccounts
		data:      Location: westus
		data:      Tags    : displayName=Storage Account - Premium
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/testvnetstoragestd
		data:      Name    : testvnetstoragestd
		data:      Type    : storageAccounts
		data:      Location: westus
		data:      Tags    : displayName=Storage Account - Simple
		data:    
		data:    Permissions:
		data:      Actions: *
		data:      NotActions: 
		data:    
		info:    group show command OK

>[AZURE.TIP] Si no ve todos los recursos, ejecute el comando `azure group deployment show` para comprobar que el estado de aprovisionamiento de la implementación es *Correcto*.

<!---HONumber=AcomDC_0224_2016-->