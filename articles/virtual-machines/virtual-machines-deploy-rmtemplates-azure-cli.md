<properties
	pageTitle="Implementación y administración de máquinas virtuales con plantillas | Microsoft Azure"
	description="Implemente y administre las configuraciones más comunes para máquinas virtuales de Azure mediante plantillas del Administrador de recursos y la CLI de Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/01/2015"
	ms.author="rasquill"/>

# Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y CLI de Azure

> [AZURE.SELECTOR]
- [PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)
- [CLI](virtual-machines-deploy-rmtemplates-azure-cli.md)

En este artículo se muestra cómo usar las plantillas del Administrador de recursos de Azure y la línea de comandos (CLI) de Azure para realizar tareas comunes de implementación y administración de máquinas virtuales de Azure. Para obtener más plantillas que puede usar, consulte [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/) y [Marcos de aplicaciones mediante el uso de plantillas](virtual-machines-app-frameworks.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica. No puede usar plantillas en el modelo de implementación clásico.

- [Creación rápida de una máquina virtual en Azure](#quick-create-a-vm-in-azure)
- [Implementación de una máquina virtual en Azure desde una plantilla](#deploy-a-vm-in-azure-from-a-template)
- [Creación de una máquina virtual desde una imagen personalizada](#create-a-custom-vm-image)
- [Implementación de una máquina virtual que usa una red virtual y un equilibrador de carga](#deploy-a-multi-vm-application-that-uses-a-virtual-network-and-an-external-load-balancer)
- [Eliminación de un grupo de recursos](#remove-a-resource-group)
- [Visualización del registro para una implementación del grupo de recursos](#show-the-log-for-a-resource-group-deployment)
- [Visualización de información acerca de una máquina virtual](#display-information-about-a-virtual-machine)
- [Conexión a una máquina virtual Linux](#log-on-to-a-linux-based-virtual-machine)
- [Detención de una máquina virtual](#stop-a-virtual-machine)
- [Inicio de una máquina virtual](#start-a-virtual-machine)
- [Acoplamiento de un disco de datos](#attach-a-data-disk)

## Preparación

Para poder usar la CLI de Azure con grupos de recursos de Azure, necesitará la versión correcta de la CLI de Azure y una cuenta de Azure. Si no tiene la CLI de Azure, debe [instalarla](xplat-cli-install.md).

### Actualización de la CLI de Azure a la versión 0.9.0 o posterior

Escriba `azure --version` para ver si ya está instalada la versión 0.9.0 o posterior.

	azure --version
    0.9.0 (node: 0.10.25)

Si la versión no es 0.9.0 o posterior, deberá actualizarla mediante uno de los instaladores nativos, o bien a través de **npm** escribiendo `npm update -g azure-cli`.

También puede ejecutar la CLI de Azure como un contenedor de Docker con la siguiente [imagen de Docker](https://registry.hub.docker.com/u/microsoft/azure-cli/). Desde un host de Docker, ejecute el siguiente comando:

	docker run -it microsoft/azure-cli

### Definición de su cuenta y suscripción de Azure

Si aún no tiene una suscripción de Azure pero tiene una suscripción a MSDN, puede activar sus [beneficios de suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). O puede suscribirse a una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

Ahora [inicie sesión de manera interactiva en su cuenta de Azure](../xplat-cli-connect.md#use-the-log-in-method). Para ello, escriba `azure login` y siga las indicaciones para obtener una experiencia de inicio de sesión interactiva en su cuenta de Azure.

> [AZURE.NOTE] Si tiene un identificador profesional o educativo y sabe que no tiene habilitada la autenticación en dos fases, **también** puede usar `azure login -u` junto con el identificador profesional o educativo para iniciar una sesión *sin* una sesión interactiva. Si no tiene un identificador profesional o educativo, puede [crear uno desde su cuenta personal de Microsoft](resource-group-create-work-id-from-personal.md) para iniciar sesión de la misma manera.

La cuenta puede tener más de una suscripción. Puede enumerar las suscripciones escribiendo `azure account list`, que podría ser algo similar a lo siguiente:

    azure account list
    info:    Executing command account list
    data:    Name                              Id                                    Tenant Id                            Current
    data:    --------------------------------  ------------------------------------  ------------------------------------  -------
    data:    Contoso Admin                     xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  true
    data:    Fabrikam dev                      xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
    data:    Fabrikam test                     xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
    data:    Contoso production                xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  

Puede establecer la suscripción de Azure actual escribiendo lo siguiente. Use el nombre de la suscripción o el identificador que tiene los recursos que desea administrar.

	azure account set <subscription name or ID> true



### Cambio al modo de grupo de recursos de CLI de Azure

De manera predeterminada, la CLI de Azure se inicia en el modo de administración de servicios (modo **asm**). Escriba lo siguiente para cambiar al modo de grupo de recursos.

	azure config mode arm

## Descripción de las plantillas de recursos y grupos de recursos de Azure

La mayoría de las aplicaciones se desarrollan a partir de una combinación de tipos de recursos diferentes (por ejemplo, una o varias máquinas virtuales y cuentas de almacenamiento, una Base de datos SQL, una red virtual o una red de entrega de contenido). La API de administración de servicios de Azure predeterminada y el Portal de Azure clásico representan estos elementos mediante un enfoque de servicio por servicio. que requiere implementar y administrar servicios individuales (o buscar otras herramientas que lo hagan) y no como una unidad lógica de implementación.

Sin embargo, las *plantillas del Administrador de recursos de Azure* permiten implementar y administrar estos recursos diferentes como una unidad lógica de implementación de manera declarativa. En lugar de indicar imperativamente a Azure que debe implementar un comando tras otro, describa la implementación completa en un archivo JSON (todos los recursos y configuración asociada y parámetros de implementación) e indíquele a Azure que implemente esos recursos como un único grupo.

Después puede administrar el ciclo de vida general de los recursos del grupo mediante el uso de comandos de administración de recursos de CLI de Azure para:

- Detener, iniciar o eliminar todos los recursos dentro del grupo a la vez.
- Aplicar reglas de control de acceso basado en roles (RBAC) para bloquear los permisos de seguridad en ellos.
- Auditar operaciones.
- Etiquetar recursos con metadatos adicionales para un mejor seguimiento.

Puede aprender mucho más sobre los grupos de recursos de Azure y su utilidad en [Información general del Administrador de recursos de Azure](../resource-group-overview.md). Si está interesado en la creación de plantillas, consulte [Creación de plantillas del Administrador de recursos de Azure](../resource-group-authoring-templates.md).

## <a id="quick-create-a-vm-in-azure"></a>Tarea: Creación rápida de una máquina virtual en Azure

A veces sabe qué imagen necesita y necesita una máquina virtual desde esa imagen inmediatamente y no le preocupa demasiado la infraestructura: quizá tenga que probar algo en una máquina virtual limpia. Es decir, cuando desea utilizar el comando `azure vm quick-create` y pasar los argumentos necesarios para crear una máquina virtual y su infraestructura.

En primer lugar, cree el grupo de recursos.

    azure group create coreos-quick westus
    info:    Executing command group create
    + Getting resource group coreos-quick
    + Creating resource group coreos-quick
    info:    Created resource group coreos-quick
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick
    data:    Name:                coreos-quick
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK


En segundo lugar, necesitará una imagen. Para buscar una imagen con la CLI de Azure, consulte [Navegación por las imágenes de máquina virtual de Azure y su selección con PowerShell y la CLI de Azure](resource-groups-vm-searching.md). Para este artículo, le presentamos una breve lista de imágenes populares. Vamos a usar la imagen Stable de CoreOS para esta creación rápida.

> [AZURE.NOTE] Para ComputeImageVersion, puede proporcionar simplemente 'latest' como parámetro en el lenguaje de la plantilla y la CLI de Azure. Esto le permitirá usar siempre la versión más reciente y con revisiones de la imagen sin tener que modificar los scripts o las plantillas. Esto se muestra a continuación.

| PublisherName | Oferta | SKU | Versión |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| OpenLogic | CentOS | 7 | 7\.0.201503 |
| OpenLogic | CentOS | 7\.1 | 7\.1.201504 |
| CoreOS | CoreOS | Versión beta | 647\.0.0 |
| CoreOS | CoreOS | Stable | 633\.1.0 |
| MicrosoftDynamicsNAV | DynamicsNAV | 2015 | 8\.0.40459 |
| MicrosoftSharePoint | MicrosoftSharePointServer | 2013 | 1\.0.0 |
| msopentech | Oracle-Database-12c-Weblogic-Server-12c | Estándar | 1\.0.0 |
| msopentech | Oracle-Database-12c-Weblogic-Server-12c | Enterprise | 1\.0.0 |
| MicrosoftSQLServer | WS2012R2 SQL2014 | Enterprise-Optimized-for-DW | 12\.0.2430 |
| MicrosoftSQLServer | WS2012R2 SQL2014 | Enterprise-Optimized-for-OLTP | 12\.0.2430 |
| Canonical | UbuntuServer | 12\.04.5-LTS | 12\.04.201504230 |
| Canonical | UbuntuServer | 14\.04.2-LTS | 14\.04.201503090 |
| Microsoft Windows Server | Windows Server | Centro de datos de 2012 | 3\.0.201503 |
| Microsoft Windows Server | Windows Server | Centro de datos de 2012-R2 | 4\.0.201503 |
| Microsoft Windows Server | Windows Server | Windows-Server-Technical-Preview | 5\.0.201504 |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials | 1\.0.141204 |
| MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 | 4\.3.4665 |

Basta con crear la máquina virtual mediante el comando `azure vm quick-create` y estar preparado para las indicaciones. Debe tener un aspecto similar al siguiente:

    azure vm quick-create
    info:    Executing command vm quick-create
    Resource group name: coreos-quick
    Virtual machine name: coreos
    Location name: westus
    Operating system Type [Windows, Linux]: linux
    ImageURN (format: "publisherName:offer:skus:version"): coreos:coreos:stable:latest
    User name: ops
    Password: *********
    Confirm password: *********
    + Looking up the VM "coreos"
    info:    Using the VM Size "Standard_A1"
    info:    The [OS, Data] Disk or image configuration requires storage account
    + Retrieving storage accounts
    info:    Could not find any storage accounts in the region "westus", trying to create new one
    + Creating storage account "cli9fd3fce49e9a9b3d14302" in "westus"
    + Looking up the storage account cli9fd3fce49e9a9b3d14302
    + Looking up the NIC "coreo-westu-1430261891570-nic"
    info:    An nic with given name "coreo-westu-1430261891570-nic" not found, creating a new one
    + Looking up the virtual network "coreo-westu-1430261891570-vnet"
    info:    Preparing to create new virtual network and subnet
    / Creating a new virtual network "coreo-westu-1430261891570-vnet" [address prefix: "10.0.0.0/16"] with subnet "coreo-westu-1430261891570-sne+" [address prefix: "10.0.1.0/24"]
    + Looking up the virtual network "coreo-westu-1430261891570-vnet"
    + Looking up the subnet "coreo-westu-1430261891570-snet" under the virtual network "coreo-westu-1430261891570-vnet"
    info:    Found public ip parameters, trying to setup PublicIP profile
    + Looking up the public ip "coreo-westu-1430261891570-pip"
    info:    PublicIP with given name "coreo-westu-1430261891570-pip" not found, creating a new one
    + Creating public ip "coreo-westu-1430261891570-pip"
    + Looking up the public ip "coreo-westu-1430261891570-pip"
    + Creating NIC "coreo-westu-1430261891570-nic"
    + Looking up the NIC "coreo-westu-1430261891570-nic"
    + Creating VM "coreos"
    + Looking up the VM "coreos"
    + Looking up the NIC "coreo-westu-1430261891570-nic"
    + Looking up the public ip "coreo-westu-1430261891570-pip"
    data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick/providers/Microsoft.Compute/virtualMachines/coreos
    data:    ProvisioningState               :Succeeded
    data:    Name                            :coreos
    data:    Location                        :westus
    data:    FQDN                            :coreo-westu-1430261891570-pip.westus.cloudapp.azure.com
    data:    Type                            :Microsoft.Compute/virtualMachines
    data:
    data:    Hardware Profile:
    data:      Size                          :Standard_A1
    data:
    data:    Storage Profile:
    data:      Image reference:
    data:        Publisher                   :coreos
    data:        Offer                       :coreos
    data:        Sku                         :stable
    data:        Version                     :633.1.0
    data:
    data:      OS Disk:
    data:        OSType                      :Linux
    data:        Name                        :cli9fd3fce49e9a9b3d-os-1430261892283
    data:        Caching                     :ReadWrite
    data:        CreateOption                :FromImage
    data:        Vhd:
    data:          Uri                       :https://cli9fd3fce49e9a9b3d14302.blob.core.windows.net/vhds/cli9fd3fce49e9a9b3d-os-1430261892283.vhd
    data:
    data:    OS Profile:
    data:      Computer Name                 :coreos
    data:      User Name                     :ops
    data:      Linux Configuration:
    data:        Disable Password Auth       :false
    data:
    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick/providers/Microsoft.Network/networkInterfaces/coreo-westu-1430261891570-nic
    data:          Primary                   :true
    data:          MAC Address               :00-0D-3A-30-72-E3
    data:          Provisioning State        :Succeeded
    data:          Name                      :coreo-westu-1430261891570-nic
    data:          Location                  :westus
    data:            Private IP alloc-method :Dynamic
    data:            Private IP address      :10.0.1.4
    data:            Public IP address       :104.40.24.124
    data:            FQDN                    :coreo-westu-1430261891570-pip.westus.cloudapp.azure.com
    info:    vm quick-create command OK

Y ya está lista la nueva máquina virtual.

## <a id="deploy-a-vm-in-azure-from-a-template"></a>Tarea: Implementación de una máquina virtual en Azure desde una plantilla

Siga las instrucciones de estas secciones para implementar una nueva máquina virtual de Azure mediante una plantilla con la CLI de Azure. Esta plantilla crea una única máquina virtual en una nueva red virtual con una única subred y, a diferencia de `azure vm quick-create`, le permite describir precisamente lo que desea y repitirlo sin errores. Esto es lo que crea esta plantilla:

![](./media/virtual-machines-deploy-rmtemplates-azure-cli/new-vm.png)

### Paso 1: Examen en el archivo JSON de los parámetros de plantilla

Este es el contenido del archivo JSON de la plantilla. (La plantilla también se encuentra en [GitHub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json)).

Las plantillas son flexibles, de tal forma que el diseñador pueda haber optado por ofrecer muchos parámetros, o haya decidido ofrecer unas pocas para crear una plantilla más específica. Para recopilar la información que se necesita para pasar la plantilla como parámetros, abra el archivo de plantilla (este tema tiene una plantilla insertada, a continuación) y examine los valores de **parameters**.

En este caso, la plantilla siguiente solicitará:

- Un nombre de cuenta de almacenamiento único
- Un nombre de usuario de administración para la máquina virtual
- Una contraseña
- Un nombre de dominio para que lo usen los usuarios externos
- Un número de versión de Ubuntu Server, pero solo uno, de una lista

Una vez haya decidido estos valores, estará preparado para crear un grupo e implementar esta plantilla en la suscripción de Azure.

    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "newStorageAccountName": {
        "type": "string",
        "metadata": {
            "description": "Unique DNS name for the storage account where the virtual machine's disks will be placed."
        }
        },
        "adminUsername": {
        "type": "string",
        "metadata": {
            "description": "User name for the virtual machine."
        }
        },
        "adminPassword": {
        "type": "securestring",
        "metadata": {
            "description": "Password for the virtual machine."
        }
        },
        "dnsNameForPublicIP": {
        "type": "string",
        "metadata": {
            "description": "Unique DNS name for the public IP used to access the virtual machine."
        }
        },
        "ubuntuOSVersion": {
        "type": "string",
        "defaultValue": "14.04.2-LTS",
        "allowedValues": [
            "12.04.5-LTS",
            "14.04.2-LTS",
            "15.04"
        ],
        "metadata": {
            "description": "The Ubuntu version for the VM. This will pick a fully patched image of this given Ubuntu version. Allowed values: 12.04.5-LTS, 14.04.2-LTS, 15.04."
        }
        }
    },
    "variables": {
        "location": "West US",
        "imagePublisher": "Canonical",
        "imageOffer": "UbuntuServer",
        "OSDiskName": "osdiskforlinuxsimple",
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "storageAccountType": "Standard_LRS",
        "publicIPAddressName": "myPublicIP",
        "publicIPAddressType": "Dynamic",
        "vmStorageAccountContainerName": "vhds",
        "vmName": "MyUbuntuVM",
        "vmSize": "Standard_D1",
        "virtualNetworkName": "MyVNET",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
    },
    "resources": [
        {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[parameters('newStorageAccountName')]",
        "apiVersion": "2015-05-01-preview",
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
            "addressPrefixes": [
                "[variables('addressPrefix')]"
            ]
            },
            "subnets": [
            {
                "name": "[variables('subnetName')]",
                "properties": {
                "addressPrefix": "[variables('subnetPrefix')]"
                }
            }
            ]
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
            "ipConfigurations": [
            {
                "name": "ipconfig1",
                "properties": {
                "privateIPAllocationMethod": "Dynamic",
                "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                },
                "subnet": {
                    "id": "[variables('subnetRef')]"
                }
                }
            }
            ]
        }
        },
        {
        "apiVersion": "2015-05-01-preview",
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
            "computername": "[variables('vmName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
            "imageReference": {
                "publisher": "[variables('imagePublisher')]",
                "offer": "[variables('imageOffer')]",
                "sku": "[parameters('ubuntuOSVersion')]",
                "version": "latest"
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
            "networkInterfaces": [
                {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                }
            ]
            }
        }
        }
    ]
    }


### Paso 2: Creación de la máquina virtual con la plantilla

Cuando estén preparados los valores de los parámetros, debe crear un grupo de recursos para la implementación de la plantilla y después implementar la plantilla.

Para crear el grupo de recursos, escriba `azure group create <group name> <location>` con el nombre del grupo que desea y la ubicación del centro de datos donde quiere realizar la implementación. Esto sucede rápidamente:

    azure group create myResourceGroup westus
    info:    Executing command group create
    + Getting resource group myResourceGroup
    + Creating resource group myResourceGroup
    info:    Created resource group myResourceGroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup
    data:    Name:                myResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK


Ahora para crear la implementación, llame a `azure group deployment create` y pase:

- El archivo de plantilla (en caso de que haga guardado la plantilla JSON anterior en un archivo local)
- Un URI de plantilla (si desea apuntar al archivo en Github o a alguna otra dirección web)
- El grupo de recursos en el que desea realizar la implementación
- Un nombre de implementación opcional

Se le pedirá que proporcione los valores de parámetros en la sección "parameters" del archivo JSON. Cuando haya especificado todos los valores de los parámetros, se iniciará la implementación.

Este es un ejemplo:

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json myResourceGroup firstDeployment
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    newStorageAccountName: storageaccount
    adminUsername: ops
    adminPassword: password
    dnsNameForPublicIP: newdomainname

Recibirá el siguiente tipo de información:

    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "firstDeployment"
    + Registering providers
    info:    Registering provider microsoft.storage
    info:    Registering provider microsoft.network
    info:    Registering provider microsoft.compute
    + Waiting for deployment to complete
    data:    DeploymentName     : firstDeployment
    data:    ResourceGroupName  : myResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-04-28T07:53:55.1828878Z
    data:    Mode               : Incremental
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    Name                   Type          Value
    data:    ---------------------  ------------  -------------
    data:    newStorageAccountName  String        storageaccount
    data:    adminUsername          String        ops
    data:    adminPassword          SecureString  undefined
    data:    dnsNameForPublicIP     String        newdomainname
    data:    ubuntuOSVersion        String        14.10
    info:    group deployment create command OK



## <a id="create-a-custom-vm-image"></a>Tarea: Creación de una imagen de máquina virtual personalizada

Ha visto el uso básico de las plantillas anteriores, de modo que ahora podemos usar instrucciones similares para crear una máquina virtual personalizada desde un archivo .vhd concreto en Azure con una plantilla mediante la CLI de Azure. La diferencia aquí radica en que esta plantilla crea una única máquina virtual desde un disco duro virtual (VHD) especificado.

### Paso 1: Examen en el archivo JSON de la plantilla

Este es el contenido del archivo JSON para la plantilla que usa esta sección como ejemplo.

De nuevo, deberá buscar los valores que desee especificar para los parámetros que no tienen valores predeterminados. Al ejecutar el comando `azure group deployment create`, la CLI de Azure le solicitará que escriba esos valores.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
            "userImageStorageAccountName": {
                "type": "string",
                "defaultValue" : "userImageStorageAccountName"
            },
            "userImageStorageContainerName" : {
                "type" : "string",
                "defaultValue" : "userImageStorageContainerName"
            },
            "userImageVhdName" : {
                "type" : "string",
                "defaultValue" : "userImageVhdName"
            },
            "dnsNameForPublicIP" : {
                "type" : "string",
                "defaultValue": "uniqueDnsNameForPublicIP"
            },
            "adminUserName": {
                "type": "string"
            },
            "adminPassword": {
                "type": "securestring"
            },
            "osType" : {
                "type" : "string",
                "allowedValues" : [
                    "windows",
                    "linux"
                ]
            },
            "subscriptionId":{
                "type" : "string"
            },
            "location": {
                "type": "String",
                "defaultValue" : "West US"
            },
            "vmSize": {
                "type": "string",
                "defaultValue": "Standard_A2"
            },
            "publicIPAddressName": {
                "type": "string",
                "defaultValue" : "myPublicIP"
            },
            "vmName": {
                "type": "string",
                "defaultValue" : "myVM"
            },
            "virtualNetworkName":{
                "type" : "string",
                "defaultValue" : "myVNET"
            },
            "nicName":{
                "type" : "string",
                "defaultValue":"myNIC"
            }
        },
        "variables": {
            "addressPrefix":"10.0.0.0/16",
            "subnet1Name": "Subnet-1",
            "subnet2Name": "Subnet-2",
            "subnet1Prefix" : "10.0.0.0/24",
            "subnet2Prefix" : "10.0.1.0/24",
            "publicIPAddressType" : "Dynamic",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
            "userImageName" : "[concat('http://',parameters('userImageStorageAccountName'),'.blob.core.windows.net/',parameters('userImageStorageContainerName'),'/',parameters('userImageVhdName'))]",
            "osDiskVhdName" : "[concat('http://',parameters('userImageStorageAccountName'),'.blob.core.windows.net/',parameters('userImageStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
            }
        },
        {
          "apiVersion": "2014-12-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[variables('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[variables('subnet1Name')]",
                "properties" : {
                    "addressPrefix": "[variables('subnet1Prefix')]"
                }
              },
              {
                "name": "[variables('subnet2Name')]",
                "properties" : {
                    "addressPrefix": "[variables('subnet2Prefix')]"
                }
              }
            ]
          }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                {
                    "name": "ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod": "Dynamic",
                        "publicIPAddress": {
                            "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                        },
                        "subnet": {
                            "id": "[variables('subnet1Ref')]"
                        }
                    }
                }
                ]
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[parameters('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "osDisk" : {
                        "name" : "[concat(parameters('vmName'),'-osDisk')]",
                        "osType" : "[parameters('osType')]",
                        "caching" : "ReadWrite",
                        "image" : {
                            "uri" : "[variables('userImageName')]"
                        },
                        "vhd" : {
                            "uri" : "[variables('osDiskVhdName')]"
                        }
                    }
                },
                "networkProfile": {
                    "networkInterfaces" : [
                    {
                        "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                    }
                    ]
                }
            }
        }
        ]
    }

### Paso 2: Obtención del disco duro virtual

Obviamente, necesitará un archivo .vhd para esto. Puede usar otro que ya creado en Azure, o puede cargar uno.

Para una máquina virtual Windows, consulte [Creación y carga de un disco duro virtual de Windows Server en Azure](virtual-machines-create-upload-vhd-windows-server.md).

Para una máquina virtual Linux, consulte [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux](virtual-machines-linux-create-upload-vhd.md).

### Paso 3: Creación de la máquina virtual con la plantilla

Ahora ya puede crear una máquina virtual nueva basada en .vhd. Cree un grupo en el cual realizar la implementación a través de `azure group create <location>`:

    azure group create myResourceGroupUser eastus
    info:    Executing command group create
    + Getting resource group myResourceGroupUser
    + Creating resource group myResourceGroupUser
    info:    Created resource group myResourceGroupUser
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupUser
    data:    Name:                myResourceGroupUser
    data:    Location:            eastus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK

Después, cree la implementación mediante la opción `--template-uri` para llamar directamente a la plantilla (o puede usar la opción `--template-file` para usar un archivo guardado localmente). Tenga en cuenta que como la plantilla tiene valores predeterminados especificados, solo se le pedirán un par de cosas. Si implementa la plantilla en distintos lugares, puede encontrar que tengan lugar algunos conflictos de nomenclatura con los valores predeterminados (especialmente el nombre DNS creado).

    azure group deployment create \
    > --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json \
    > myResourceGroup \
    > customVhdDeployment
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    adminUserName: ops
    adminPassword: password
    osType: linux
    subscriptionId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

La salida es similar a la siguiente:

    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "customVhdDeployment"
    + Registering providers
    info:    Registering provider microsoft.network
    info:    Registering provider microsoft.compute
    + Waiting for deployment to complete
    error:   Deployment provisioning state was not successful
    data:    DeploymentName     : customVhdDeployment
    data:    ResourceGroupName  : myResourceGroupUser
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-04-28T14:55:48.0963829Z
    data:    Mode               : Incremental
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    Name                           Type          Value
    data:    -----------------------------  ------------  ------------------------------------
    data:    userImageStorageAccountName    String        userImageStorageAccountName
    data:    userImageStorageContainerName  String        userImageStorageContainerName
    data:    userImageVhdName               String        userImageVhdName
    data:    dnsNameForPublicIP             String        uniqueDnsNameForPublicIP
    data:    adminUserName                  String        ops
    data:    adminPassword                  SecureString  undefined
    data:    osType                         String        linux
    data:    subscriptionId                 String        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    data:    location                       String        West US
    data:    vmSize                         String        Standard_A2
    data:    publicIPAddressName            String        myPublicIP
    data:    vmName                         String        myVM
    data:    virtualNetworkName             String        myVNET
    data:    nicName                        String        myNIC
    info:    group deployment create command OK


## <a id="deploy-a-multi-vm-application-that-uses-a-virtual-network-and-an-external-load-balancer"></a>Tarea: Implementación de una aplicación de varias máquinas virtuales que usa una red virtual y un equilibrador de carga externo

Esta plantilla permite crear dos máquinas virtuales en un equilibrador de carga y configurar una regla de equilibrio de carga en el puerto 80. Esta plantilla también implementa una cuenta de almacenamiento, la red virtual, la dirección IP pública, un conjunto de disponibilidad y las interfaces de red.

![](./media/virtual-machines-deploy-rmtemplates-azure-cli/multivmextlb.png)

Siga estos pasos para implementar una aplicación de varias máquinas virtuales que usa una red virtual y un equilibrador de carga mediante una plantilla del Administrador de recursos en el repositorio de plantillas de GitHub mediante comandos de Azure PowerShell.

### Paso 1: Examen en el archivo JSON de la plantilla

Este es el contenido del archivo JSON de la plantilla. Si desea la última versión, se encuentra [en el repositorio de plantillas de Github](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json). Este tema se usa el modificador `--template-uri` para llamar a la plantilla, pero también puede utilizar el modificador `--template-file` para pasar a una versión local.


    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "location": {
                "type": "string",
                "metadata": {
                  "description": "Location of resources"
                }
            },
            "storageAccountName": {
                "type": "string",
                "metadata": {
                  "description": "Name of storage account"
                }
            },
            "adminUsername": {
                "type": "string",
                "metadata": {
                  "description": "Admin user name"
                }
            },
            "adminPassword": {
                "type": "securestring",
                "metadata": {
                  "description": "Admin password"
                }
            },
            "dnsNameforLBIP": {
                "type": "string",
                "metadata": {
                  "description": "DNS for load balancer IP"
                }
            },
            "backendPort": {
                "type": "int",
                "defaultValue": 3389,
                "metadata": {
                  "description": "Back-end port"
                }
            },
            "vmNamePrefix": {
                "type": "string",
                "defaultValue": "myVM",
                "metadata": {
                  "description": "Prefix to use for VM names"
                }
            },
            "vmSourceImageName": {
                "type": "string",
                "defaultValue": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201412.01-en.us-127GB.vhd"
            },
            "lbName": {
                "type": "string",
                "defaultValue": "myLB",
                "metadata": {
                  "description": "Load balancer name"
                }
            },
            "nicNamePrefix": {
                "type": "string",
                "defaultValue": "nic",
                "metadata": {
                  "description": "Network interface name prefix"
                }
            },
            "publicIPAddressName": {
                "type": "string",
                "defaultValue": "myPublicIP",
                "metadata": {
                  "description": "Public IP name"
                }
            },
            "vnetName": {
                "type": "string",
                "defaultValue": "myVNET",
                "metadata": {
                  "description": "Virtual network name"
                }
            },
            "vmSize": {
                "type": "string",
                "defaultValue": "Standard_A1",
                "metadata": {
                  "description": "Size of the VM"
                }
            }
        },
        "variables": {
            "storageAccountType": "Standard_LRS",
            "vmStorageAccountContainerName": "vhds",
            "availabilitySetName": "myAvSet",
            "addressPrefix": "10.0.0.0/16",
            "subnetName": "Subnet-1",
            "subnetPrefix": "10.0.0.0/24",
            "publicIPAddressType": "Dynamic",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
            "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
            "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
            "numberOfInstances": 2,
            "nicId1": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 0))]",
            "nicId2": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 1))]",
            "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LBFE')]",
            "backEndIPConfigID1": "[concat(variables('nicId1'),'/ipConfigurations/ipconfig1')]",
            "backEndIPConfigID2": "[concat(variables('nicId2'),'/ipConfigurations/ipconfig1')]",
            "sourceImageName": "[concat('/', subscription().subscriptionId,'/services/images/',parameters('vmSourceImageName'))]",
            "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/LBBE')]",
            "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
        },
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "name": "[parameters('storageAccountName')]",
                "apiVersion": "2015-05-01-preview",
                "location": "[parameters('location')]",
                "properties": {
                    "accountType": "[variables('storageAccountType')]"
                }
            },
            {
                "type": "Microsoft.Compute/availabilitySets",
                "name": "[variables('availabilitySetName')]",
                "apiVersion": "2015-05-01-preview",
                "location": "[parameters('location')]",
                "properties": { }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[parameters('publicIPAddressName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                    "dnsSettings": {
                        "domainNameLabel": "[parameters('dnsNameforLBIP')]"
                    }
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[parameters('vnetName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "[variables('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnetName')]",
                            "properties": {
                                "addressPrefix": "[variables('subnetPrefix')]"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
                "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
                "location": "[parameters('location')]",
                "copy": {
                    "name": "nicLoop",
                    "count": "[variables('numberOfInstances')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
                ],
                "properties": {
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "subnet": {
                                    "id": "[variables('subnetRef')]"
                                }
                            },
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('Microsoft.Network/loadBalancers/',parameters('lbName'),'/backendAddressPools/LBBE')]"
                                }
                            ],
                            "loadBalancerInboundNatRules": [
                                {
                                    "id": "[concat('Microsoft.Network/loadBalancers/',parameters('lbName'),'/inboundNatRule/RDP-VM', copyindex())]"
                                }
                            ]


                        }
                    ]

                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "name": "[parameters('lbName')]",
                "type": "Microsoft.Network/loadBalancers",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "nicLoop",
                    "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LBFE",
                            "properties": {
                                "publicIPAddress": {
                                    "id": "[variables('publicIPAddressID')]"
                                }
                            }
                        }
                    ],
                    "backendAddressPools": [
                        {
                            "name": "LBBE"

                        }
                    ],
                    "inboundNatRules": [
                        {
                            "name": "RDP-VM1",
                            "properties": {
                                "frontendIPConfiguration":
                                    {
                                        "id": "[variables('frontEndIPConfigID')]"
                                    },
                                "protocol": "tcp",
                                "frontendPort": 50001,
                                "backendPort": 3389,
                                "enableFloatingIP": false
                            }
                        },
                        {
                            "name": "RDP-VM2",
                            "properties": {
                                "frontendIPConfiguration":
                                    {
                                        "id": "[variables('frontEndIPConfigID')]"
                                    },
                                "protocol": "tcp",
                                "frontendPort": 50002,
                                "backendPort": 3389,
                                "enableFloatingIP": false
                            }
                        }
                    ],
                    "loadBalancingRules": [
                        {
                            "name": "LBRule",
                            "properties": {
                                "frontendIPConfiguration": {
                                    "id": "[variables('frontEndIPConfigID')]"
                                },
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID')]"
                                },
                                "protocol": "tcp",
                                "frontendPort": 80,
                                "backendPort": 80,
                                "enableFloatingIP": false,
                                "idleTimeoutInMinutes": 5,
                                "probe": {
                                    "id": "[variables('lbProbeID')]"
                                }
                            }
                        }
                    ],
                    "probes": [
                        {
                            "name": "tcpProbe",
                            "properties": {
                                "protocol": "tcp",
                                "port": 80,
                                "intervalInSeconds": "5",
                                "numberOfProbes": "2"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Compute/virtualMachines",
                "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
                "copy": {
                    "name": "virtualMachineLoop",
                    "count": "[variables('numberOfInstances')]"
                },
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                    "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
                    "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
                ],
                "properties": {
                    "availabilitySet": {
                        "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
                    },
                    "hardwareProfile": {
                        "vmSize": "[parameters('vmSize')]"
                    },
                    "osProfile": {
                        "computername": "[concat(parameters('vmNamePrefix'), copyIndex())]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "adminPassword": "[parameters('adminPassword')]"
                    },
                    "storageProfile": {
                        "sourceImage": {
                            "id": "[variables('sourceImageName')]"
                        },
                        "destinationVhdsContainer": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/')]"
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
                            }
                        ]
                    }
                }
            }
        ]
    }

### Paso 2: Creación de la implementación con la plantilla

Cree un grupo de recursos para la plantilla mediante `azure group create <location>`. Después, cree una implementación en ese grupo de recursos; para ello, use `azure group deployment create`, pase el grupo de recursos y el nombre de la implementación y, luego, responda a las solicitudes de los parámetros de la plantilla que no tengan valores predeterminados.


    azure group create lbgroup westus
    info:    Executing command group create
    + Getting resource group lbgroup
    + Creating resource group lbgroup
    info:    Created resource group lbgroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/lbgroup
    data:    Name:                lbgroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK


Ahora utilice el comando `azure group deployment create` y la opción `--template-uri` para implementar la plantilla. Prepare los valores de parámetros cuando se le pida, como se muestra a continuación.

    azure group deployment create \
    > --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json \
    > lbgroup \
    > newdeployment
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    location: westus
    newStorageAccountName: storagename
    adminUsername: ops
    adminPassword: password
    dnsNameforLBIP: lbdomainname
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "newdeployment"
    + Registering providers
    info:    Registering provider microsoft.storage
    info:    Registering provider microsoft.compute
    info:    Registering provider microsoft.network
    + Waiting for deployment to complete
    data:    DeploymentName     : newdeployment
    data:    ResourceGroupName  : lbgroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-04-28T20:58:40.1678876Z
    data:    Mode               : Incremental
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    Name                   Type          Value
    data:    ---------------------  ------------  ----------------------
    data:    location               String        westus
    data:    newStorageAccountName  String        storagename
    data:    adminUsername          String        ops
    data:    adminPassword          SecureString  undefined
    data:    dnsNameforLBIP         String        lbdomainname
    data:    backendPort            Int           3389
    data:    vmNamePrefix           String        myVM
    data:    imagePublisher         String        MicrosoftWindowsServer
    data:    imageOffer             String        WindowsServer
    data:    imageSKU               String        2012-R2-Datacenter
    data:    lbName                 String        myLB
    data:    nicNamePrefix          String        nic
    data:    publicIPAddressName    String        myPublicIP
    data:    vnetName               String        myVNET
    data:    vmSize                 String        Standard_A1
    info:    group deployment create command OK

Tenga en cuenta que esta plantilla implementa una imagen de Windows Server; sin embargo, también se podría reemplazar fácilmente por cualquier imagen de Linux. ¿Desea crear un clúster de Docker con varios administradores de swarm? [Puede hacerlo](https://azure.microsoft.com/documentation/templates/docker-swarm-cluster/).

## <a id="remove-a-resource-group"></a>Tarea: Eliminación de un grupo de recursos

Recuerde que puede volver a implementar un grupo de recursos, pero si ya terminó con uno, puede eliminarlo mediante `azure group delete <group name>`.

    azure group delete myResourceGroup
    info:    Executing command group delete
    Delete resource group myResourceGroup? [y/n] y
    + Deleting resource group myResourceGroup
    info:    group delete command OK

## <a id="show-the-log-for-a-resource-group-deployment"></a>Tarea: Visualización del registro para una implementación de grupo de recursos

Es común al crear o usar plantillas. La llamada para mostrar los registros de implementación de un grupo es `azure group log show <groupname>`, que muestra gran cantidad de información útil para entender por qué ocurrió algo o por qué no fue así. (Para obtener más información sobre cómo solucionar problemas de las implementaciones, así como otra información acerca de problemas, consulte [Solución de problemas de implementaciones de grupo de recursos en Azure](resource-group-deploy-debug.md)).

Por ejemplo, para solucionar errores específicos, puede usar herramientas como **jq** para realizar consultas algo más precisas, como qué errores individuales se deben corregir. El siguiente ejemplo usa **jq** para analizar un registro de implementación para **lbgroup**, en busca de errores.

    azure group log show lbgroup -l --json | jq '.[] | select(.status.value == "Failed") | .properties'

Puede detectar rápidamente qué salió mal, corregirlo y volver a intentarlo. En el caso siguiente, la plantilla había creado dos máquinas virtuales al mismo tiempo, lo que ha creado un bloqueo en el archivo .vhd. (Después de modificar la plantilla, la implementación se realizó correctamente).

    {
      "statusCode": "Conflict",
      "statusMessage": "{"status":"Failed","error":{"code":"ResourceDeploymentFailure","message":"The resource operation completed with terminal provisioning state 'Failed'.","details":[{"code":"AcquireDiskLeaseFailed","message":"Failed to acquire lease while creating disk 'osdisk' using blob with URI http://storage.blob.core.windows.net/vhds/osdisk.vhd."}]}}"
    }


## <a id="display-information-about-a-virtual-machine"></a>Tarea: Visualización de información sobre una máquina virtual

Con el comando `azure vm show <groupname> <vmname> command` puede ver información sobre máquinas virtuales específicas en el grupo de recursos. Si tiene más de una máquina virtual en el grupo, es posible que primero deba enumerarlas en un grupo con `azure vm list <groupname>`.

    azure vm list zoo
    info:    Executing command vm list
    + Getting virtual machines
    data:    Name   ProvisioningState  Location  Size
    data:    -----  -----------------  --------  -----------
    data:    myVM0  Succeeded          westus    Standard_A1
    data:    myVM1  Failed             westus    Standard_A1

Y, después, buscar myVM1:

    azure vm show zoo myVM1
    info:    Executing command vm show
    + Looking up the VM "myVM1"
    + Looking up the NIC "nic1"
    data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Compute/virtualMachines/myVM1
    data:    ProvisioningState               :Failed
    data:    Name                            :myVM1
    data:    Location                        :westus
    data:    Type                            :Microsoft.Compute/virtualMachines
    data:
    data:    Hardware Profile:
    data:      Size                          :Standard_A1
    data:
    data:    Storage Profile:
    data:      Image reference:
    data:        Publisher                   :MicrosoftWindowsServer
    data:        Offer                       :WindowsServer
    data:        Sku                         :2012-R2-Datacenter
    data:        Version                     :latest
    data:
    data:      OS Disk:
    data:        OSType                      :Windows
    data:        Name                        :osdisk
    data:        Caching                     :ReadWrite
    data:        CreateOption                :FromImage
    data:        Vhd:
    data:          Uri                       :http://zoostorageralph.blob.core.windows.net/vhds/osdisk.vhd
    data:
    data:    OS Profile:
    data:      Computer Name                 :myVM1
    data:      User Name                     :ops
    data:      Windows Configuration:
    data:        Provision VM Agent          :true
    data:        Enable automatic updates    :true
    data:
    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Network/networkInterfaces/nic1
    data:          Primary                   :false
    data:          Provisioning State        :Succeeded
    data:          Name                      :nic1
    data:          Location                  :westus
    data:            Private IP alloc-method :Dynamic
    data:            Private IP address      :10.0.0.5
    data:
    data:    AvailabilitySet:
    data:      Id                            :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Compute/availabilitySets/MYAVSET
    info:    vm show command OK


> [AZURE.NOTE] Si desea almacenar y manipular mediante programación la salida de los comandos de consola, puede usar una herramienta de análisis de JSON como **[jq](https://github.com/stedolan/jq)** o **[jsawk](https://github.com/micha/jsawk)** o bibliotecas de idioma adecuadas para la tarea.

## <a id="log-on-to-a-linux-based-virtual-machine"></a>Tarea: Inicio de sesión en una máquina virtual Linux

Normalmente los equipos Linux están conectados a través de SSH. Para obtener más información, consulte [Uso de SSH con Linux en Azure](virtual-machines-linux-use-ssh-key.md).

## <a id="stop-a-virtual-machine"></a>Tarea: Detención de una máquina virtual

Ejecute este comando:

    azure vm stop <group name> <virtual machine name>

>[AZURE.IMPORTANT] Use este parámetro para mantener la IP virtual (VIP) de la vnet en caso de que sea la última máquina virtual de esa vnet. <br><br> Si usa el parámetro `StayProvisioned`, se le facturará por la máquina virtual.

## <a id="start-a-virtual-machine"></a>Tarea: Inicio de una máquina virtual

Ejecute este comando:

    azure vm start <group name> <virtual machine name>

## <a id="attach-a-data-disk"></a>Tarea: Acoplamiento de un disco de datos

También tendrá que decidir si desea adjuntar un disco nuevo o uno que contenga los datos. Para un disco nuevo, el comando crea el archivo .vhd y lo adjunta en el mismo comando.

Para adjuntar un disco nuevo, ejecute este comando:

     azure vm disk attach-new <resource-group> <vm-name> <size-in-gb>

Para adjuntar un disco de datos existente, ejecute este comando:

    azure vm disk attach <resource-group> <vm-name> [vhd-url]

A continuación, deberá montar el disco, como haría normalmente en Linux (o en Windows).


## Pasos siguientes

Para consultar más ejemplos de uso de la CLI de Azure con el modo **arm**, consulte [Uso de la CLI de Azure para Mac, Linux y Windows con el Administrador de recursos de Azure](xplat-cli-azure-resource-manager.md). Para obtener más información acerca de los recursos de Azure y sus conceptos, consulte [Información general del Administrador de recursos de Azure](../resource-group-overview.md).

Para obtener más plantillas que puede usar, consulte [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/) y [Marcos de aplicaciones mediante el uso de plantillas](virtual-machines-app-frameworks.md).

<!---HONumber=AcomDC_0128_2016-->