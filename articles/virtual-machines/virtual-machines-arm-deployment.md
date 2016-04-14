<properties
	pageTitle="Implementación de recursos mediante bibliotecas de .NET | Microsoft Azure"
	description="Obtenga información sobre cómo utilizar las bibliotecas .NET de proceso, almacenamiento y red para crear y eliminar recursos de Microsoft Azure."
	services="virtual-machines,virtual-network,storage"
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
	ms.date="01/20/2016"
	ms.author="davidmu"/>

# Implementación de recursos de Azure mediante bibliotecas .NET de proceso, red y almacenamiento

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

Este tutorial muestra cómo utilizar las bibliotecas .NET de proceso, red y almacenamiento para crear y eliminar recursos de Microsoft Azure. También muestra cómo autenticar las solicitudes al Administrador de recursos de Azure con Azure Active Directory.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Para completar este tutorial, también necesita:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Cuenta de Almacenamiento de Azure](../storage/storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Tardará unos 30 minutos en realizar estos pasos.

## Paso 1: Adición de una aplicación a Azure AD y establecer permisos

Para usar Azure AD para autenticar las solicitudes con el Administrador de recursos de Azure, es necesario agregar una aplicación en el directorio predeterminado. Para obtener más información, consulte [Autenticación de una entidad de servicio con el Administrador de recursos de Azure](../resource-group-authenticate-service-principal.md)

1. Abra un símbolo del sistema de Azure PowerShell, ejecute este comando y, luego, escriba las credenciales de la suscripción cuando se le solicite:

			Login-AzureRmAccount

2. Reemplace {password} en el siguiente comando por la contraseña que desea utilizar y, a continuación, ejecútelo para crear la aplicación:

			New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

	>[AZURE.NOTE] Anote el identificador de la aplicación que se devuelve después de crear la aplicación, ya que la necesitará para el siguiente paso. Asimismo, también puede encontrar el identificador de la aplicación en el campo del identificador de cliente de la aplicación, en la sección de Active Directory del Portal de Azure.

3. Reemplace {application-id} por el identificador que acaba de anotar y, a continuación, cree la entidad de servicio correspondiente a la aplicación:

        New-AzureRmADServicePrincipal -ApplicationId {application-id}

4. Establezca el permiso para usar la aplicación:

	    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## Paso 2: Creación de un proyecto de Visual Studio e instalar las bibliotecas

Los Paquetes de NuGet son la manera más fácil de instalar las bibliotecas que necesita para finalizar este tutorial. Debe instalar la biblioteca de administración de recursos de Azure, la biblioteca de autenticación de Azure Active Directory y la biblioteca de proveedor de recursos de equipo. Para obtener estas bibliotecas en Visual Studio, haga lo siguiente:

1. Haga clic en **Archivo** > **Nuevo** > **Proyecto**.

2. En **Plantillas** > **Visual C#**, seleccione **Aplicación de consola**, escriba el nombre y la ubicación del proyecto y, a continuación, haga clic en **Aceptar**.

3. Haga clic con el botón derecho en el Explorador de soluciones y, a continuación, haga clic en **Administrar paquetes de NuGet**.

4. Escriba *Active Directory* en el cuadro de búsqueda, haga clic en **Instalar** para el paquete de la biblioteca de autenticación de Active Directory y, a continuación, siga las instrucciones para instalar el paquete.

5. En la parte superior de la página, seleccione **Incluir versión preliminar**. Escriba *Microsoft.Azure.Management.Compute* en el cuadro de búsqueda, haga clic en **Instalar** para las bibliotecas .NET de proceso y, luego, siga las instrucciones para instalar el paquete.

6. Escriba *Microsoft.Azure.Management.Network* en el cuadro de búsqueda, haga clic en **Instalar** para las bibliotecas .NET de red y, luego, siga las instrucciones para instalar el paquete.

7. Escriba *Microsoft.Azure.Management.Storage* en el cuadro de búsqueda, haga clic en **Instalar** para las bibliotecas .NET de almacenamiento y, luego, siga las instrucciones para instalar el paquete.

8. Escriba *Microsoft.Azure.Management.Resources* en el cuadro de búsqueda y haga clic en **Instalar** para las bibliotecas de administración de recursos.

Ahora está preparado para comenzar a usar las bibliotecas para crear la aplicación.

## Paso 3: Creación de las credenciales que se utilizan para autenticar las solicitudes

Ahora que la aplicación Azure Active Directory se crea y se instala la biblioteca de autenticación, de a la información de la aplicación formato de credenciales que se utilizan para autenticar las solicitudes al Administrador de recursos de Azure.

1. Abra el archivo Program.cs para el proyecto que ha creado y, a continuación, agregue las siguientes instrucciones using a la parte superior del archivo:

	```
	using Microsoft.Azure;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.Azure.Management.Storage;
	using Microsoft.Azure.Management.Storage.Models;
	using Microsoft.Azure.Management.Network;
	using Microsoft.Azure.Management.Network.Models;
	using Microsoft.Azure.Management.Compute;
	using Microsoft.Azure.Management.Compute.Models;
	using Microsoft.Rest;
	```

2. Agregue este método a la clase Program para obtener el token necesario para crear las credenciales:

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

3. Agregue este código al método Main en el archivo Program.cs para crear las credenciales:

	```
	var token = GetAuthorizationHeader();
	var credential = new TokenCredentials(token);
	```

5. Guarde el archivo Program.cs.

## Paso 4: Adición del código para registrar los proveedores y crear los recursos

### Registre los proveedores y cree un grupo de recursos

Todos los recursos deben encontrarse en un grupo de recursos. Para poder agregar recursos a un grupo, la suscripción se debe registrar con los proveedores de recursos.

1. Agregue variables al método Main de la clase Program para especificar los nombres que desea utilizar para los recursos, la ubicación de los recursos, como "Oeste de EE. UU.", la información de la cuenta de administrador y el identificador de la suscripción:

	```
	var groupName = "{resource-group-name}";
	var ipName = "{public-ip-name}";
	var avSetName = "{availability-set-name}";
	var nicName = "{network-interface-name}";
	var storageName = "{storage-account-name}";
	var vmName = "{virtual-machine-name};  
	var vnetName = "{vnet-name}";
	var subnetName = "{subnet-name}";
	var adminName = "{administrator-account-name}";
	var adminPassword = "{administrator-account-password};
	var location = "{location}";
	var subscriptionId = "{subsciption-id}";
	```

   Reemplace todos los marcadores de posición entre corchetes por los nombres que desea utilizar. Para encontrar el identificador de la suscripción, ejecute Get-AzureSubscription.

2. Agregue este método a la clase Program para crear el grupo de recursos:

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
		Console.WriteLine(rgResult.Properties.ProvisioningState);

		var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
		Console.WriteLine(rpResult.RegistrationState);
		rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
		Console.WriteLine(rpResult.RegistrationState);
		rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
		Console.WriteLine(rpResult.RegistrationState);
	}
	```

3. Agregue este código al método Main para llamar al método que acaba de agregar:

	```
	CreateResourceGroup(
		credential,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Crear una cuenta de almacenamiento

Se necesita una cuenta de almacenamiento para almacenar el archivo de disco duro virtual que se crea para la máquina virtual.

1. Agregue este método a la clase Program para crear la cuenta de almacenamiento:

	```
	public static void CreateStorageAccount(
		TokenCredentials credential,         
		string storageName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the storage account...");
		var storageManagementClient = new StorageManagementClient(credential);
		storageManagementClient.SubscriptionId = subscriptionId;
		var saResult = storageManagementClient.StorageAccounts.Create(
			groupName,
			storageName,
			new StorageAccountCreateParameters()
			{
				AccountType = AccountType.StandardLRS,
				Location = location
			}
		);
		Console.WriteLine(saResult.ProvisioningState);
	}
	```

2. Agregue este código al método Main de la clase Program para llamar al método que acaba de agregar:

	```
	CreateStorageAccount(
		credential,
		storageName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Crear la dirección IP pública

Se necesita una dirección IP pública para la comunicación con la máquina virtual.

1. Agregue este método a la clase Program para crear la dirección IP pública:

	```
	public static void CreatePublicIPAddress(
		TokenCredentials credential,
		string ipName,  
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the public ip...");
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;
		var ipResult = networkManagementClient.PublicIPAddresses.CreateOrUpdate(
			groupName,
			ipName,
			new PublicIPAddress
			{
				Location = location,
				PublicIPAllocationMethod = "Dynamic"
			}
		);
		Console.WriteLine(ipResult.ProvisioningState);
	}
	```

2. Agregue este código al método Main de la clase Program para llamar al método que acaba de agregar:

	```
	CreatePublicIPAddress(
		credential,
		ipName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Crear la red virtual

Una máquina virtual creada con el modelo de implementación del Administrador de recursos debe estar en una red virtual.

1. Agregue este método a la clase Program para crear una subred y una red virtual:

	```
	public static void CreateNetwork(
		TokenCredentials credential,
		string vnetName,
		string subnetName,
		string nicName,
		string ipName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the virtual network...");
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;

		var subnet = new Subnet
		{
			Name = subnetName,
			AddressPrefix = "10.0.0.0/24"
		};

		var address = new AddressSpace {
			AddressPrefixes = new List<string> { "10.0.0.0/16" }
		};

		var vnResult = networkManagementClient.VirtualNetworks.CreateOrUpdate(
			groupName,
			vnetName,
			new VirtualNetwork
			{
				Location = location,
				AddressSpace = address,
				Subnets = new List<Subnet> { subnet }
			}
		);
		Console.WriteLine(vnResult.ProvisioningState);

		var subnetResponse = networkManagementClient.Subnets.Get(
			groupName,
			vnetName,
			subnetName
		);

		var pubipResponse = networkManagementClient.PublicIPAddresses.Get(groupName, ipName);

		Console.WriteLine("Updating the network with the nic...");
		var nicResult = networkManagementClient.NetworkInterfaces.CreateOrUpdate(
			groupName,
			nicName,
			new NetworkInterface
			{
				Location = location,
				IpConfigurations = new List<NetworkInterfaceIPConfiguration>
				{
					new NetworkInterfaceIPConfiguration
					{
						Name = "nicConfig1",
						PublicIPAddress = pubipResponse,
						Subnet = subnetResponse
					}
				}
			}
		);
		Console.WriteLine(vnResult.ProvisioningState);
	}
	```

2. Agregue este código al método Main de la clase Program para llamar al método que acaba de agregar:

	```
	CreateNetwork(
		credential,
		vnetName,
		subnetName,
		nicName,
		ipName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Crear un conjunto de disponibilidad

Los conjuntos de disponibilidad facilitan la administración del mantenimiento de las máquinas virtuales que utiliza su aplicación.

1. Agregue este método a la clase Program para crear el conjunto de disponibilidad:

	```
	public static void CreateAvailabilitySet(
		TokenCredentials credential,
		string avsetName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the availability set...");
		var computeManagementClient = new ComputeManagementClient(credential);
		computeManagementClient.SubscriptionId = subscriptionId;
		var avResult = computeManagementClient.AvailabilitySets.CreateOrUpdate(
			groupName,
			avsetName,
			new AvailabilitySet()
			{
				Location = location
			}
		);
	}
	```

2. Agregue este código al método Main de la clase Program para llamar al método que acaba de agregar:

	```
	CreateAvailabilitySet(
		credential,
		avsetName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### de una máquina virtual

Ahora que ha creado todos los recursos auxiliares, puede crear una máquina virtual.

1. Agregue este método a la clase Program para crear la máquina virtual:

	```
	public static void CreateVirtualMachine(
		TokenCredentials credential,
		string vmName,
		string groupName,
		string nicName,
		string avsetName,
		string storageName,
		string adminName,
		string adminPassword,
		string subscriptionId,
		string location)
	{
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;
		var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

		var computeManagementClient = new ComputeManagementClient(credential);
		computeManagementClient.SubscriptionId = subscriptionId;
		var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

		Console.WriteLine("Creating the virtual machine...");
		var vm = computeManagementClient.VirtualMachines.CreateOrUpdate(
			groupName,
			vmName,
			new VirtualMachine
			{
				Location = location,
				AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
				{
					Id = avSet.Id
				},
				HardwareProfile = new HardwareProfile
				{
					VmSize = "Standard_A0"
				},
				OsProfile = new OSProfile
				{
					AdminUsername = adminName,
					AdminPassword = adminPassword,
					ComputerName = vmName,
					WindowsConfiguration = new WindowsConfiguration
					{
						ProvisionVMAgent = true
					}
				},
				NetworkProfile = new NetworkProfile
				{
					NetworkInterfaces = new List<NetworkInterfaceReference>
					{
						new NetworkInterfaceReference { Id = nic.Id }
					}
				},
				StorageProfile = new StorageProfile
				{
					ImageReference = new ImageReference
					{
						Publisher = "MicrosoftWindowsServer",
						Offer = "WindowsServer",
						Sku = "2012-R2-Datacenter",
						Version = "latest"
					},
					OsDisk = new OSDisk
					{
						Name = "mytestod1",
						CreateOption = "FromImage",
						Vhd = new VirtualHardDisk
						{
							Uri = "http://" + storageName + ".blob.core.windows.net/vhds/mytestod1.vhd"
						}
					}
				}
			}
		);
		Console.WriteLine(vm.ProvisioningState);
	}
	```

	>[AZURE.NOTE] En este tutorial se crea una máquina virtual donde se ejecuta una versión del sistema operativo Windows Server. Para obtener más información sobre la selección de otras imágenes, consulte [Seleccione y navegue por imágenes de máquina virtual de Azure con PowerShell y la CLI de Azure](resource-groups-vm-searching.md).

2. Agregue el código siguiente al método Main para llamar al método que acaba de agregar:

	```
	CreateVirtualMachine(
		credential,
		vmName,
		groupName,
		nicName,
		avsetName,
		storageName,
		adminName,
		adminPassword,
		subscriptionId,
		location);
	Console.ReadLine();
	```

##Paso 5: Adición del código para eliminar los recursos

Dado que se le cobrará por los recursos utilizados en Azure, siempre es conveniente eliminar los recursos que ya no sean necesarios. Si desea eliminar las máquinas virtuales y todos los recursos auxiliares, lo único que tiene que hacer es eliminar el grupo de recursos.

1. Agregue el método siguiente a la clase Program para crear el grupo de recursos:

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

2. Agregue el código siguiente al método Main para llamar al método que acaba de agregar:

	```
	DeleteResourceGroup(
		credential,
		groupName,
		subscriptionId);
	Console.ReadLine();
	```

## Paso 6: Ejecución de la aplicación de la consola.

1. Para ejecutar la aplicación de consola, haga clic en **Iniciar** en Visual Studio y, a continuación, inicie sesión en Azure AD con el mismo nombre de usuario y contraseña que utiliza con su suscripción.

2. Presione **Entrar** después de que se devuelva cada código de estado para crear cada recurso. Después de crear la máquina virtual, realice el siguiente paso antes de presionar Entrar para eliminar todos los recursos.

	Esta aplicación de consola tardará unos cinco minutos en ejecutarse completamente de principio a fin. Antes de presionar Entrar para comenzar la eliminación de recursos, puede dedicar unos minutos a comprobar la creación de los recursos en el Portal de Azure.

3. Vaya a los registros de auditoría en el Portal de Azure para ver el estado de los recursos:

	![Crear una aplicación de AD](./media/virtual-machines-arm-deployment/crpportal.png)

<!---HONumber=AcomDC_0128_2016-->