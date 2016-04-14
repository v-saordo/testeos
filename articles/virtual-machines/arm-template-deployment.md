<properties
	pageTitle="Implementación de recursos de Azure con una plantilla | Microsoft Azure"
	description="Aprenda a usar algunos de los clientes disponibles en la biblioteca de administración de recursos de Azure para implementar una máquina virtual, una red virtual y una cuenta de almacenamiento."
	services="virtual-machines,virtual-networks,storage"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="davidmu"/>

# Implementación de recursos de Azure mediante bibliotecas de .NET y una plantilla

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.


Mediante el uso de plantillas y grupos de recursos, tendrá la posibilidad de administrar en conjunto todos los recursos que admiten su aplicación. Este tutorial muestra cómo usar algunos de los clientes disponibles en la biblioteca de administración de recursos de Azure y cómo crear una plantilla para implementar una máquina virtual, una red virtual y una cuenta de almacenamiento.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Para completar este tutorial, también necesita:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Cuenta de Almacenamiento de Azure](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)

>[AZURE.NOTE] La cuenta de almacenamiento que crea en este punto se usa para almacenar la plantilla. Se crea otra cuenta de almacenamiento al implementar la plantilla que se usa para almacenar el disco para la máquina virtual. Cree un contenedor en esta cuenta de almacenamiento llamado templates.

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Tardará unos 30 minutos en realizar estos pasos.

## Paso 1: Adición de una aplicación a Azure AD y establecer permisos

Para usar Azure AD para autenticar las solicitudes con el Administrador de recursos de Azure, es necesario agregar una aplicación en el directorio predeterminado. Siga este procedimiento para agregar una aplicación:

1. Abra un símbolo del sistema de Azure PowerShell y luego ejecute este comando y escriba las credenciales para la suscripción cuando se le solicite:

			Login-AzureRmAccount

2. Reemplace {password} en el siguiente comando por la contraseña que desea utilizar y, a continuación, ejecútelo para crear la aplicación:

			New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

	>[AZURE.NOTE] Anote el identificador de la aplicación que se devuelve después de crear la aplicación, ya que la necesitará para el siguiente paso. Asimismo, también puede encontrar el identificador de la aplicación en el campo del identificador de cliente de la aplicación, en la sección de Active Directory del Portal de Azure.

3. Reemplace {application-id} por el identificador que acaba de anotar y, a continuación, cree la entidad de servicio correspondiente a la aplicación:

			New-AzureRmADServicePrincipal -ApplicationId {application-id}

4. Establezca el permiso para usar la aplicación:

			New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## Paso 2: Crear el proyecto de Visual Studio, el archivo de plantilla y el archivo de parámetros

### Cree el archivo de plantilla

Las plantillas del Administrador de recursos de Azure le permiten implementar y administrar los distintos recursos de Azure en conjunto mediante una descripción de JSON de los recursos y los parámetros de configuración e implementación asociados. La plantilla que cree en este tutorial será similar a cualquier plantilla que pueda encontrarse en la galería de plantillas. Para obtener más información, vea [Implementación de una VM con Windows sencilla en la región del oeste de EE. UU.](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/)

En Visual Studio, haga lo siguiente:

1. Haga clic en **Archivo** > **Nuevo** > **Proyecto**.

2. En **Plantillas** > **Visual C#**, seleccione **Aplicación de consola**, escriba el nombre y la ubicación del proyecto y, a continuación, haga clic en **Aceptar**.

3. Haga clic con el botón derecho en el nombre del proyecto en el Explorador de soluciones y, a continuación, haga clic en **Agregar** > **Nuevo elemento**.

4. En la ventana Agregar nuevo elemento, seleccione **Archivo de texto**, escriba *VirtualMachineTemplate.json* como el nombre y, a continuación, haga clic en **Agregar**.

5. Abra el archivo VirtualMachineTemplate.json y, a continuación, agregue los corchetes de apertura y de cierre y el elemento de esquema y el elemento contentVersion necesarios:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
	}
	```

6. Los [parámetros](../resource-group-authoring-templates.md#parameters) no siempre son necesarios, pero facilitan la administración de la plantilla. Describen el tipo del valor, el valor predeterminado, en caso de ser necesario, y, posiblemente, los valores permitidos del parámetro. En este tutorial, los parámetros que se usan para crear una máquina virtual, una cuenta de almacenamiento y una red virtual se agregan a la plantilla.

    Agregue el elemento parameters y sus elementos secundarios después del elemento contentVersion:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUserName": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": [ "2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter" ]
			}
		},
 	}
	```

7. Pueden usarse [variables](../resource-group-authoring-templates.md#variables) en una plantilla para especificar los valores que pueden cambiar con frecuencia o los valores que se deben crear a partir de una combinación de valores de parámetro.

    Agregue el elemento variables después de la sección parameters:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUsername": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": ["2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter"]
			}
		},
		"variables": {
			"location": "West US",
			"imagePublisher": "MicrosoftWindowsServer",
			"imageOffer": "WindowsServer",
			"OSDiskName": "osdiskforwindowssimple",
			"nicName": "myVMnic1",
			"addressPrefix": "10.0.0.0/16",
			"subnetName": "Subnet",
			"subnetPrefix": "10.0.0.0/24",
			"storageAccountType": "Standard_LRS",
			"publicIPAddressName": "myPublicIP",
			"publicIPAddressType": "Dynamic",
			"vmStorageAccountContainerName": "vhds",
			"vmName": "MyWindowsVM",
			"vmSize": "Standard_A2",
			"virtualNetworkName": "MyVNET",
			"vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
			"subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]"
		},
	}
	```

8. Los [recursos](../resource-group-authoring-templates.md#resources), como la máquina virtual, la red virtual y la cuenta de almacenamiento se definen a continuación en la plantilla.

    Agregue la sección resources después de la sección variables:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUsername": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": [ "2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter"]
			}
		},
		"variables": {
			"location": "West US",
			"imagePublisher": "MicrosoftWindowsServer",
			"imageOffer": "WindowsServer",
			"OSDiskName": "osdiskforwindowssimple",
			"nicName": "myVMnic1",
			"addressPrefix": "10.0.0.0/16",
			"subnetName": "Subnet",
			"subnetPrefix": "10.0.0.0/24",
			"storageAccountType": "Standard_LRS",
			"publicIPAddressName": "myPublicIP",
			"publicIPAddressType": "Dynamic",
			"vmStorageAccountContainerName": "vhds",
			"vmName": "MyWindowsVM",
			"vmSize": "Standard_A2",
			"virtualNetworkName": "MyVNET",
			"vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
			"subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]"
		},
		"resources": [
			{
  			"apiVersion": "2015-05-01-preview",
				"type": "Microsoft.Storage/storageAccounts",
				"name": "[parameters('newStorageAccountName')]",
				"location": "[variables('location')]",
				"properties": {
					"accountType": "[variables('storageAccountType')]"
				}
			},
			{
				"apiVersion": "2015-05-01-preview",
				"type": "Microsoft.Network/publicIPAddresses",
				"name": "[variables('publicIPAddressName')]",
				"location": "[variables('location')]",
				"properties": {
					"publicIPAllocationMethod": "[variables('publicIPAddressType')]",
					"dnsSettings": {
						"domainNameLabel": "[parameters('dnsNameForPublicIP')]"
					}
				}
			},
			{
				"apiVersion": "2015-05-01-preview",
				"type": "Microsoft.Network/virtualNetworks",
				"name": "[variables('virtualNetworkName')]",
				"location": "[variables('location')]",
				"properties": {
					"addressSpace": {
						"addressPrefixes": [ "[variables('addressPrefix')]" ]
					},
					"subnets": [ {
						"name": "[variables('subnetName')]",
						"properties": {
							"addressPrefix": "[variables('subnetPrefix')]"
						}
					} ]
				}
			},
			{
				"apiVersion": "2015-05-01-preview",
				"type": "Microsoft.Network/networkInterfaces",
				"name": "[variables('nicName')]",
				"location": "[variables('location')]",
				"dependsOn": [
					"[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
					"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
				],
				"properties": {
					"ipConfigurations": [ {
						"name": "ipconfig1",
						"properties": {
							"privateIPAllocationMethod": "Dynamic",
							"publicIPAddress": {
								"id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))]"
							},
							"subnet": {
								"id": "[variables('subnetRef')]"
							}
						}
					} ]
				}
			},
			{
				"apiVersion": "2015-06-15",
				"type": "Microsoft.Compute/virtualMachines",
				"name": "[variables('vmName')]",
				"location": "[variables('location')]",
				"dependsOn": [
					"[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
					"[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
				],
				"properties": {
					"hardwareProfile": {
						"vmSize": "[variables('vmSize')]"
					},
					"osProfile": {
						"computerName": "[variables('vmName')]",
						"adminUsername": "[parameters('adminUsername')]",
						"adminPassword": "[parameters('adminPassword')]",
					},
					"storageProfile": {
						"imageReference": {
							"publisher": "[variables('imagePublisher')]",
							"offer": "[variables('imageOffer')]",
							"sku": "[parameters('windowsOSVersion')]",
							"version" : "latest"
						},
						"osDisk": {
							"name": "osdisk",
							"vhd": {
								"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
							},
							"caching": "ReadWrite",
							"createOption": "FromImage"
						}
					},
					"networkProfile": {
						"networkInterfaces" : [ {
							"id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
						} ]
					}
				}
			} ]
		}
		```

9. Guarde el archivo de plantilla que creó.

### Cree el archivo de parámetros

Para especificar los valores de los parámetros del recurso que se definieron en la plantilla, debe crear un archivo de parámetros que contenga los valores y enviarlo al Administrador de recursos junto con la plantilla. En Visual Studio, haga lo siguiente:

1. Haga clic con el botón derecho en el nombre del proyecto en el Explorador de soluciones y, a continuación, haga clic en **Agregar** y en **Nuevo elemento**.

2. En la ventana Agregar nuevo elemento, seleccione **Archivo de texto**, escriba *Parameters.json* como el nombre y, a continuación, haga clic en **Agregar**.

3. Abra el archivo parameters.json y, a continuación, agregue el siguiente contenido JSON:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "value": "mytestsa1" },
			"adminUserName": { "value": "mytestacct1" },
			"adminPassword": { "value": "mytestpass1" },
			"dnsNameForPublicIP": { "value": "mytestdns1" }
		}
	}
	```

    >[AZURE.NOTE] En este tutorial se crea una máquina virtual donde se ejecuta una versión del sistema operativo Windows Server. Para obtener más información sobre la selección de otras imágenes, consulte [Seleccione y navegue por imágenes de máquina virtual de Azure con PowerShell y la CLI de Azure](resource-groups-vm-searching.md).


4. Guarde el archivo de parámetros que creó.

### Carga de los archivos

Administrador de recursos de Azure obtiene acceso al archivo de plantilla y al archivo de parámetros desde una cuenta de almacenamiento de Azure. Para poner los archivos en el primer almacenamiento que creó, haga lo siguiente:

1. Abra el Explorador de servidores y vaya al contenedor existente en su cuenta de almacenamiento en el que desea colocar el archivo. Para efectos de este tutorial, el contenedor donde se ubica la plantilla se denomina plantillas.

2. En la esquina superior derecha del panel del contenedor de plantillas, haga clic en el icono Cargar blob, vaya al archivo VirtualMachineTemplate.json que creó y, a continuación, haga clic en **Abrir**.

3. Haga clic nuevamente en el icono Cargar blob, vaya al archivo Parameters.json que creó y, a continuación, haga clic en **Abrir**.

## Paso 3: Instalar las bibliotecas

Los paquetes de NuGet son la manera más fácil de instalar las bibliotecas que necesita para finalizar este tutorial. Debe instalar la biblioteca de administración de recursos de Azure y la biblioteca de autenticación de Azure Active Directory. Para obtener estas bibliotecas en Visual Studio, haga lo siguiente:

1. Haga clic con el botón derecho en el Explorador de soluciones y, a continuación, haga clic en **Administrar paquetes de NuGet**.

2. Escriba *Active Directory* en el cuadro de búsqueda, haga clic en **Instalar** para el paquete de la biblioteca de autenticación de Active Directory y, a continuación, siga las instrucciones para instalar el paquete.

3. En la parte superior de la página, seleccione **Incluir versión preliminar**. Escriba *Microsoft.Azure.Management.Resources* en el cuadro de búsqueda, haga clic en **Instalar** para las bibliotecas de administración de recursos de Microsoft Azure y después siga las instrucciones para instalar el paquete.

Ahora está preparado para comenzar a usar las bibliotecas para crear la aplicación.

##Paso 4: Crear las credenciales que se utilizan para autenticar las solicitudes

Ahora que la aplicación de Azure Active Directory se creó y se instaló la biblioteca de autenticación, dé a la información de la aplicación formato de credenciales que se utilizan para autenticar las solicitudes en el Administrador de recursos de Azure. Haga lo siguiente:

1. Abra el archivo Program.cs para el proyecto que ha creado y, a continuación, agregue las siguientes instrucciones using a la parte superior del archivo:

	```
	using Microsoft.Azure;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.Rest;
	```

2.	Agregue el método siguiente a la clase Program para obtener el token necesario para crear las credenciales:

	```
	private static string GetAuthorizationHeader()
	{
		ClientCredential cc = new ClientCredential("{application-id}", "{password}");
		var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
		var result = context.AcquireTokenAsync("https://management.azure.com/", cc);
		if (result == null)
		{
			throw new InvalidOperationException("Failed to obtain the JWT token");
		}

		string token = result.AccessToken;

		return token;
	}
	```

	Reemplace {application-id} por el identificador de aplicación que anotó anteriormente, {password} por la contraseña que eligió para la aplicación AD y {tenant-id} por el identificador del inquilino de su suscripción. Puede encontrar el Id. del inquilino ejecutando Get-AzureSubscription.

3. Agregue el código siguiente al método Main en el archivo Program.cs para crear las credenciales:

	```
	var token = GetAuthorizationHeader();
	var credential = new TokenCredentials(token);
	```

4. Guarde el archivo Program.cs.


## Paso 5: Agregar el código para implementar la plantilla

Los recursos siempre se implementan a partir de una plantilla a un grupo de recursos. Utilice las clases [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) y [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) para crear el grupo de recursos en el que se implementan los recursos.

1. Agregue el método siguiente a la clase Program para crear el grupo de recursos:

	```
	public static void CreateResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the resource group...");
		var resourceManagementClient = new ResourceManagementClient(credential);
		resourceManagementClient.SubscriptionId = subscriptionId;
		var resourceGroup = new ResourceGroup {
			Location = location
		};
		var rgResult = resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, resourceGroup);
		Console.WriteLine(rgResult.StatusCode.Properties.ProvisioningState);
	}
	```

2. Agregue el código siguiente al método Main para llamar al método que acaba de agregar:

	```
	CreateResourceGroup(
		credential,
		"{group-name}",
		"{subscription-id}",
		"{location}");
		Console.ReadLine();
	```

	Reemplace {group-name} por el nombre que desea utilizar para el grupo de recursos. Reemplace {subscription-id} por el identificador de suscripción, que puede obtenerlo ejecutando Get-AzureSubscription. Reemplace la ubicación por la región que desee para crear los recursos, como "Oeste de EE. UU.".

3. Agregue el método siguiente a la clase Program para implementar los recursos en el grupo de recursos con la plantilla que definió:

	```
	public static void CreateTemplateDeployment(
		TokenCredentials credential,
		string groupName,
		string storageName,
		string deploymentName,
		string subscriptionId)
	{
		Console.WriteLine("Creating the template deployment...");
		var deployment = new Deployment();
		deployment.Properties = new DeploymentProperties
		{
			Mode = DeploymentMode.Incremental,
			TemplateLink = new TemplateLink
			{
				Uri = "https://{storage-name}.blob.core.windows.net/templates/VirtualMachineTemplate.json"
			},
			ParametersLink = new ParametersLink
			{
				Uri = "https://{storage-name}.blob.core.windows.net/templates/Parameters.json"
			}
		};
		var resourceManagementClient = new ResourceManagementClient(credential);
		resourceManagementClient.SubscriptionId = subscriptionId;
		var dpResult = resourceManagementClient.Deployments.CreateOrUpdate(
			groupName,
			deploymentName,
			deployment);
		Console.WriteLine(dpResult.Properties.ProvisioningState);
	}
	```

	Reemplace {storage-name} por el nombre de la cuenta donde colocó anteriormente los archivos.

4. Agregue el código siguiente al método Main para llamar al método que acaba de agregar:

	```
	CreateTemplateDeployment(
		credential,
		"{group-name}",
		"{storage-name}",
		"{deployment-name}",
		"{subscription-id}");
	Console.ReadLine();
	```

    Reemplace {group-name} por el nombre del grupo de recursos que creó. Reemplace {storage-name} por el nombre de la cuenta de almacenamiento donde colocó los archivos de plantilla. Reemplace {deployment-name} por el nombre que desea utilizar para identificar el conjunto de recursos de implementación. Reemplace {subscription-id} por el identificador de suscripción, que puede obtenerlo ejecutando Get-AzureSubscription.

##Paso 6: Agregar el código para eliminar los recursos

Dado que se le cobrará por los recursos utilizados en Azure, siempre es conveniente eliminar los recursos que ya no sean necesarios. No es necesario eliminar por separado cada recurso de un grupo de recursos. Puede eliminar el grupo de recursos y se eliminarán automáticamente todos sus recursos.

1.	Agregue el método siguiente a la clase Program para eliminar el grupo de recursos:

	```
	public static void DeleteResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId)
	{
		Console.WriteLine("Deleting resource group...");
		var resourceGroupClient = new ResourceManagementClient(credential);
		resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
	}
	```

2.	Agregue el código siguiente al método Main para llamar al método que acaba de agregar:

	```
	DeleteResourceGroup(
		credential,
		"{group-name}",
		"{subscription-id}");
	Console.ReadLine();
	```
    Reemplace {group-name} por el nombre del grupo de recursos que creó. Reemplace {subscription-id} por el identificador de suscripción, que puede obtenerlo ejecutando Get-AzureSubscription.

##Paso 7: Ejecutar la aplicación de consola

1.	Para ejecutar la aplicación de consola, haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

2.	Presione **Intro** después de que aparezca el estado Aceptado.

	Esta aplicación de consola tardará unos cinco minutos en ejecutarse completamente de principio a fin. Antes de presionar Entrar para comenzar la eliminación de recursos, puede dedicar unos minutos a comprobar la creación de los recursos en el Portal de Azure.

3. Vaya a los registros de auditoría en el Portal de Azure para ver el estado de los recursos:

	![Crear una aplicación de AD](./media/arm-template-deployment/crpportal.png)

<!---HONumber=AcomDC_0107_2016-->