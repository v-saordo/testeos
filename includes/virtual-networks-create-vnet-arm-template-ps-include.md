## Implementar la plantilla ARM mediante PowerShell

Para implementar la plantilla ARM que descargó con PowerShell, siga estos pasos.

1. Si es la primera vez que usa Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](powershell-install-configure.md) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccionar su suscripción.

3. Si es necesario, ejecute el cmdlet **`New-AzureRmResourceGroup`** para crear un nuevo grupo de recursos. El siguiente comando crea un grupo de recursos denominado *TestRG* en la región *Central US* de Azure. Para obtener más información sobre los grupos de recursos, visite [Información general del Administrador de recursos de Azure](resource-group-overview.md).

		New-AzureRmResourceGroup -Name TestRG -Location centralus
		
	Este es el resultado esperado del comando anterior:

		ResourceGroupName : TestRG
		Location          : centralus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		                    Actions  NotActions
		                    =======  ==========
		                    *
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

4. Ejecute el cmdlet **`New-AzureRmResourceGroupDeployment`** para implementar la nueva red virtual mediante la plantilla y los archivos de parámetros que descargó y modificó anteriormente.

		New-AzureRmResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
			-TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
			
	Este es el resultado esperado del comando anterior:
		
		DeploymentName    : TestVNetDeployment
		ResourceGroupName : TestRG
		ProvisioningState : Succeeded
		Timestamp         : 8/14/2015 9:40:00 PM
		Mode              : Incremental
		TemplateLink      :
		Parameters        :
		                    Name             Type                       Value
		                    ===============  =========================  ==========
		                    location         String                     Central US
		                    vnetName         String                     TestVNet
		                    addressPrefix    String                     192.168.0.0/16
		                    subnet1Prefix    String                     192.168.1.0/24
		                    subnet1Name      String                     FrontEnd
		                    subnet2Prefix    String                     192.168.2.0/24
		                    subnet2Name      String                     BackEnd
		
		Outputs           :

5. Ejecute el cmdlet **`Get-AzureRmVirtualNetwork`** para ver las propiedades de la nueva red virtual, como se muestra a continuación.


		Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
		
	Este es el resultado esperado del comando anterior:
		
		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState : Succeeded
		Tags              :
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": null
		                    }
		NetworkInterfaces : null
		Subnets           : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      },
		                      {
		                        "Name": "BackEnd",
		                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
		                        "AddressPrefix": "192.168.2.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      }
		                    ]

<!---HONumber=AcomDC_0211_2016-->