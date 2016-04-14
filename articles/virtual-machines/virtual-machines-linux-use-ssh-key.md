<properties 
	pageTitle="Uso de SSH en Linux y Mac | Microsoft Azure" 
	description="Generar y usar claves SSH en Linux y Mac para los modelos de implementación clásica en Azure y el Administrador de recursos." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/15/2015" 
	ms.author="rasquill"/>

#Uso de SSH con Linux y Mac en Azure

> [AZURE.SELECTOR]
- [Windows](../articles/virtual-machines/virtual-machines-windows-use-ssh-key.md)
- [Linux/Mac](../articles/virtual-machines/virtual-machines-linux-use-ssh-key.md)

Este tema describe cómo usar **ssh keygen** y **openssl** en Linux y Mac para crear y usar archivos con formato **ssh-rsa** y **.pem** para proteger la comunicación con las máquinas virtuales de Azure basadas en Linux. Se recomienda crear máquinas virtuales de Azure basadas en Linux mediante el modelo de implementación del Administrador de recursos para las nuevas implementaciones, es necesaria una cadena o un archivo de clave pública (dependiendo del cliente de la implementación) del tipo *ssh rsa*. El [Portal de Azure](https://portal.azure.com) actualmente solo acepta las cadenas de formato **ssh rsa**, ya sea para las implementaciones clásicas o del Administrador de recursos.

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]Para crear estos tipos de archivos para su uso en un equipo de Windows con el fin de comunicarse de manera segura con las máquinas virtuales de Linux en Azure, consulte [Uso de las claves SSH en Windows](virtual-machines-windows-use-ssh-key.md).

## ¿Qué archivos necesita?

Una configuración básica ssh para Azure incluye un par de claves una pública y una privada **ssh rsa** de 2048 bits (de forma predeterminada, **ssh keygen** almacena estos archivos como **~/.ssh/id\_rsa** y **~/.ssh/id-rsa.pub** a menos que cambie los valores predeterminados) así como un archivo `.pem` generado a partir del archivo de clave privada **id\_rsa** para su uso con el modelo de implementación clásica del portal clásico.

Estos son los escenarios de implementación y los tipos de archivos que se usan en cada uno:

1. Las claves **ssh rsa** son necesarias para cualquier implementación mediante el [Portal de Azure](https://portal.azure.com), independientemente del modelo de implementación.
2. Los archivos .pem son necesarios para crear máquinas virtuales mediante el [portal clásico](https://manage.windowsazure.com). Los archivos .pem también se admiten en las implementaciones clásicas que usan el [CLI de Azure](xplat-cli-install.md). 

## Creación de claves para su uso con SSH

Azure exige los archivos de clave con formato **ssh rsa** de 2048 bits o los archivos .pem equivalentes, dependiendo del escenario. Si ya tiene este tipo de archivos, pase el archivo de clave pública al crear la máquina virtual de Azure.

Si necesita crear los archivos:

1. Asegúrese de que la implementación de **ssh keygen** y **openssl** está actualizada. Esto varía según la plataforma. 

	- Para Mac, no olvide visitar el [sitio web de Actualizaciones de seguridad de Apple](https://support.apple.com/HT201222) y, si es necesario, elegir las actualizaciones correspondientes.
	- Para las distribuciones de Linux basadas en Debian como Ubuntu, Debian, Mint y demás:

			sudo apt-get update ssh-keygen
			sudo apt-get update openssl

	- Para las distribuciones de Linux basadas en RPM como CentOS y Oracle Linux:

			sudo yum update ssh-keygen
			sudo yum update openssl

	- Para SLES y OpenSUSE

			sudo zypper update ssh-keygen
			sudo zypper update openssl

2. Use **ssh keygen** para crear archivos RSA de 2048 bits de clave pública y privada, y a menos que tenga una ubicación específica o nombres específicos para los archivos, acepte la ubicación predeterminada y el nombre de `~/.ssh/id_rsa`. El comando básico es:

		ssh-keygen -t rsa -b 2048 

	Normalmente, la implementación de **ssh keygen** agrega un comentario, con frecuencia se trata del nombre de usuario y nombre de host del equipo. Puede especificar un comentario específico mediante la opción `-C`.

3. Cree un archivo .pem desde su archivo `~/.ssh/id_rsa` para poder trabajar con el portal clásico. Use **openssl** como sigue:

		openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

	Si desea crear un archivo .pem a partir de un archivo de clave privada diferente, modifique el argumento `-key`.

> [AZURE.NOTE] Si planea administrar los servicios implementados con el modelo de implementación clásica, también puede crear un archivo de formato **.cer** para cargar en el portal, aunque esto no implica **ssh** o conectarse a máquinas virtuales de Linux, que es el tema de este artículo. Para crear esos archivos en Linux o Mac, escriba: <br /> openssl.exe x509 -outform der -in myCert.pem -out myCert.cer

Para convertir el archivo .pem en un archivo certificado X 509 con codificación DER.

## Uso de claves SSH que ya tiene

Puede usar claves ssh-rsa (`.pub`) para cualquier trabajo nuevo, especialmente con el modelo de implementación del Administrador de recursos y el portal de vista previa; es posible que tenga que crear un archivo `.pem` a partir de las claves si necesita usar el portal clásico.

## Creación de una máquina virtual con el archivo de clave público

Una vez que haya creado los archivos que necesita, hay muchas formas de crear una máquina virtual a la que puede conectarse de forma segura mediante un intercambio de claves pública y privada. En casi todas las situaciones, especialmente con las implementaciones del Administrador de recursos, pase el archivo. pub cuando se le pida una ruta de acceso del archivo de clave ssh o el contenido de un archivo como una cadena.

### Ejemplo: Creación de una máquina virtual con el archivo id\_rsa.pub

El uso más común es cuando tiene que crear de forma imperativa una máquina virtual, o cargar una plantilla para crear una máquina virtual. En el ejemplo de código siguiente se muestra la creación de una nueva y segura máquina virtual de Linux en Azure, pasando el nombre de archivo público (en este caso, el archivo predeterminado `~/.ssh/id_rsa.pub`) al comando `azure vm create`. (Los demás argumentos se crearon con anterioridad.)

	azure vm create \
	--nic-name testnic \
	--public-ip-name testpip \
	--vnet-name testvnet \
	--vnet-subnet-name testsubnet \
	--storage-account-name computeteststore 
	--image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
	--username ops \
	-ssh-publickey-file ~/.ssh/id_rsa.pub \
	testrg testvm westeurope linux

En el ejemplo siguiente se muestra el uso del formato **ssh-rsa** con una plantilla de Administrador de recursos y la CLI de Azure para crear una máquina virtual de Ubuntu que está protegida por un nombre de usuario y el contenido de `~/.ssh/id_rsa.pub` como una cadena. (En este caso, se acorta la cadena de clave pública para que sea más legible.)

	azure group deployment create \
	--resource-group test-sshtemplate \
	--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
	--name mysshdeployment
	info:    Executing command group deployment create
	info:    Supply values for the following parameters
	testnewStorageAccountName: testsshvmtemplate3
	adminUserName: ops
	sshKeyData: ssh-rsa AAAAB3NzaC1yc2EAAAADAQA+/L+rHIjz+nXTzxApgnP+iKDZco9 user@macbookpro
	dnsNameForPublicIP: testsshvmtemplate
	location: West Europe
	vmName: sshvm
	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "mysshdeployment"
	+ Waiting for deployment to complete
	data:    DeploymentName     : mysshdeployment
	data:    ResourceGroupName  : test-sshtemplate
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : 2015-10-08T00:12:12.2529678Z
	data:    Mode               : Incremental
	data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	data:    ContentVersion     : 1.0.0.0
	data:    Name                   Type    Value

	data:    newStorageAccountName  String  testtestsshvmtemplate3
	data:    adminUserName          String  ops
	data:    sshKeyData             String  ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDAkek3P6V3EhmD+xP+iKDZco9 user@macbookpro
	data:    dnsNameForPublicIP     String  testsshvmtemplate
	data:    location               String  West Europe
	data:    vmSize                 String  Standard_A2
	data:    vmName                 String  sshvm
	data:    ubuntuOSVersion        String  14.04.2-LTS
	info:    group deployment create command OK


### Ejemplo: Creación de una máquina virtual con un archivo .pem

A continuación, puede usar el archivo .pem con el portal clásico o con el modo de implementación clásica y `azure vm create`, como en el ejemplo siguiente:

	azure vm create \
	-l "West US" -n testpemasm \
	-P -t myCert.pem -e 22 \
	testpemasm \
	b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-es-ES-30GB \
	ops
	info:    Executing command vm create
	warn:    --vm-size has not been specified. Defaulting to "Small".
	+ Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-es-ES-30GB
	+ Looking up cloud service
	info:    cloud service testpemasm not found.
	+ Creating cloud service
	+ Retrieving storage accounts
	+ Configuring certificate
	+ Creating VM
	info:    vm create command OK


## Conexión a la máquina virtual

El comando **ssh** toma un nombre de usuario para iniciar sesión con la dirección de red del equipo y el puerto en el que se va a conectar a la dirección, así como muchas otras variaciones especiales. (Para más información sobre **ssh**, puede comenzar con este [artículo sobre Shell seguro](https://en.wikipedia.org/wiki/Secure_Shell))

Un uso típico con la implementación del Administrador de recursos podría ser similar al siguiente, si simplemente se especificó un subdominio y una ubicación de implementación:

	ssh user@subdomain.westus.cloudapp.azure.com -p 22

o bien, si se conecta a un servicio de nube de implementación clásica, la dirección que usaría sería algo así:

	ssh user@subdomain.cloudapp.net -p 22

Dado que el formato de dirección puede cambiar, siempre puede usar la dirección IP o quizás tenga un nombre de dominio personalizado asignado (tendría que descubrir la dirección de la máquina virtual de Azure).

### Detección de la dirección SSH de la máquina virtual de Azure con implementaciones clásicas

Puede detectar la dirección que se usa con una máquina virtual y el modelo de implementación clásica, mediante el comando `azure vm show` con el nombre de la máquina virtual:

	azure vm show testpemasm
	info:    Executing command vm show
	+ Getting virtual machines
	data:    DNSName "testpemasm.cloudapp.net"
	data:    Location "West US"
	data:    VMName "testpemasm"
	data:    IPAddress "100.116.160.154"
	data:    InstanceStatus "ReadyRole"
	data:    InstanceSize "Small"
	data:    Image "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-es-ES-30GB"
	data:    OSDisk hostCaching "ReadWrite"
	data:    OSDisk name "testpemasm-testpemasm-0-201510102050230517"
	data:    OSDisk mediaLink "https://portalvhds4blttsxgjj1rf.blob.core.windows.net/vhd-store/testpemasm-2747c9c432b043ff.vhd"
	data:    OSDisk sourceImageName "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-es-ES-30GB"
	data:    OSDisk operatingSystem "Linux"
	data:    OSDisk iOType "Standard"
	data:    ReservedIPName ""
	data:    VirtualIPAddresses 0 address "40.83.178.221"
	data:    VirtualIPAddresses 0 name "testpemasmContractContract"
	data:    VirtualIPAddresses 0 isDnsProgrammed true
	data:    Network Endpoints 0 localPort 22
	data:    Network Endpoints 0 name "ssh"
	data:    Network Endpoints 0 port 22
	data:    Network Endpoints 0 protocol "tcp"
	data:    Network Endpoints 0 virtualIPAddress "40.83.178.221"
	data:    Network Endpoints 0 enableDirectServerReturn false
	info:    vm show command OK

### Detección de la dirección SSH de la máquina virtual de Azure con implementaciones del Administrador de recursos

	azure vm show testrg testvm
	info:    Executing command vm show
	+ Looking up the VM "testvm"
	+ Looking up the NIC "testnic"
	+ Looking up the public ip "testpip"

Examine la sección de perfil de red:

	data:    Network Profile:
	data:      Network Interfaces:
	data:        Network Interface #1:
	data:          Id                        :/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic
	data:          Primary                   :true
	data:          MAC Address               :00-0D-3A-21-8E-AE
	data:          Provisioning State        :Succeeded
	data:          Name                      :testnic
	data:          Location                  :westeurope
	data:            Private IP alloc-method :Static
	data:            Private IP address      :192.168.1.101
	data:            Public IP address       :40.115.48.189
	data:            FQDN                    :testsubdomain.westeurope.cloudapp.azure.com
	data:
	data:    Diagnostics Instance View:
	info:    vm show command OK

Si no usó el puerto SSH predeterminado 22 al crear la máquina virtual, puede detectar qué puertos tienen reglas de entrada con el comando `azure network nsg show`, como en el ejemplo siguiente:

	azure network nsg show testrg testnsg
	info:    Executing command network nsg show
	+ Looking up the network security group "testnsg"
	data:    Id                              : /subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testnsg
	data:    Name                            : testnsg
	data:    Type                            : Microsoft.Network/networkSecurityGroups
	data:    Location                        : westeurope
	data:    Provisioning state              : Succeeded
	data:    Security group rules:
	data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
	data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
	data:    testnsgrule                    *                  *            *               22                Tcp       Inbound    Allow   1000
	data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000
	data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001
	data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500
	data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000
	data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001
	data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500
	info:    network nsg show command OK

### Ejemplo: salida de la sesión de SSH mediante claves de .pem e implementación clásica

Si crea una máquina virtual mediante un archivo .pem creado a partir de su archivo `~/.ssh/id_rsa`, puede conectarse directamente con ssh en esa máquina virtual. Tenga en cuenta que al hacerlo, el protocolo de enlace de certificado usará su clave privada en `~/.ssh/id_rsa`. (El proceso de creación de la máquina virtual procesa la clave pública desde el archivo .pem y coloca el formulario ssh-rsa de la clave pública en `~/.ssh/authorized_users`). La conexión podría tener un aspecto similar al ejemplo siguiente:

	ssh ops@testpemasm.cloudapp.net -p 22
	The authenticity of host 'testpemasm.cloudapp.net (40.83.178.221)' can't be established.
	RSA key fingerprint is dc:bb:e4:cc:59:db:b9:49:dc:71:a3:c8:37:36:fd:62.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added 'testpemasm.cloudapp.net,40.83.178.221' (RSA) to the list of known hosts.
	Welcome to Ubuntu 14.04.3 LTS (GNU/Linux 3.19.0-28-generic x86_64)

	* Documentation:  https://help.ubuntu.com/

	System information as of Sat Oct 10 20:53:08 UTC 2015

	System load: 0.52              Memory usage: 5%   Processes:       80
	Usage of /:  45.3% of 1.94GB   Swap usage:   0%   Users logged in: 0

	Graph this data and manage this system at:
		https://landscape.canonical.com/

	Get cloud support with Ubuntu Advantage Cloud Guest:
		http://www.ubuntu.com/business/services/cloud

	0 packages can be updated.
	0 updates are security updates.

	The programs included with the Ubuntu system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.

	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	applicable law.

## Si tiene problemas de conexión

Puede leer las sugerencias en [Solución de problemas de las conexiones SSH](virtual-machines-troubleshoot-ssh-connections.md) que quizás le ayuden a resolver la situación.

## Pasos siguientes
 
Ahora que ha conectado a la máquina virtual, asegúrese de actualizar su distribución elegida antes de continuar con su uso.

<!---HONumber=AcomDC_0204_2016-->