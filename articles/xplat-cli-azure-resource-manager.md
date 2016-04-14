
<properties
	pageTitle="CLI de Azure con el Administrador de recursos | Microsoft Azure"
	description="Use CLI de Azure para Mac, Linux y Microsoft Azure para implementar varios recursos como un grupo de recursos."
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="azure-resource-manager"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/19/2016"
	ms.author="danlep"/>

# Uso de la CLI de Azure para Mac, Linux y Windows con el Administrador de recursos de Azure

> [AZURE.SELECTOR]
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)



Este artículo describe cómo crear y administrar los recursos de Azure con la Interfaz de la línea de comandos de Azure (CLI) para Mac, Linux y Windows con el modo Administrador de recursos de Azure.

>[AZURE.NOTE] Para crear y administrar recursos de Azure en la línea de comandos, necesitará una cuenta de Azure ([prueba gratuita aquí](https://azure.microsoft.com/pricing/free-trial/)). También necesitará [instalar la CLI de Azure](xplat-cli-install.md) e [iniciar sesión para usar recursos de Azure asociados a su cuenta](xplat-cli-connect.md). Si ha realizado estas acciones, ya está preparado para comenzar.

## Recursos de Azure

Use el Administrador de recursos de Azure para crear y administrar un grupo de _recursos_ (entidades administradas por el usuario, como una máquina virtual, un servidor de base de datos, una base de datos o un sitio web) como una sola unidad lógica o _grupo de recursos_.

Una ventaja del modo Administrador de recursos de Azure es que puede crear los recursos de Azure de forma _declarativa_, describiendo la estructura y las relaciones de un grupo de recursos en *plantillas* de JSON que se pueden implementar. La plantilla identifica los parámetros que se pueden completar ya sea en línea cuando se ejecuta un comando o cuando están almacenados en un archivo JSON azuredeploy-parameters.json independiente. Esto le permite crear fácilmente recursos nuevos con la misma plantilla brindando simplemente parámetros distintos. Por ejemplo, una plantilla que crea un sitio web tendrá parámetros para el nombre del sitio, la región en que estará ubicado el sitio web y otros parámetros comunes.

Cuando se usa una plantilla para modificar o crear un grupo, se crea una _implementación_, que luego se aplica al grupo. Para obtener más información sobre el Administrador de recursos de Azure, consulte [Información general del Administrador de recursos de Azure](resource-group-overview.md).

Después de crear una implementación, puede administrar los recursos individuales de forma imperativa en la línea de comandos, exactamente de la misma forma que en el modelo clásico de implementación (Administración de servicios). Por ejemplo, use los comandos de la CLI del Administrador de recursos de Azure para iniciar, detener o eliminar recursos como [máquinas virtuales del Administrador de recursos de Azure](virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md).

## Autenticación

Trabajar con el Administrador de recursos de Azure a través de la CLI de Azure requiere que se autentique en Microsoft Azure con una cuenta profesional o educativa (una cuenta de organización) o una cuenta de Microsoft (a partir de la versión 0.9.10 de CLI). En este modo no sirve autenticarse con un certificado instalado a través de un archivo .publishsettings.

Para obtener más información acerca de cómo autenticarse en Microsoft Azure, consulte [Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)](xplat-cli-connect.md).

>[AZURE.NOTE] Cuando use una cuenta profesional o educativa, administrada por Azure Active Directory, también puede utilizar el Control de acceso basado en roles de Azure (RBAC) para administrar el acceso y el uso de recursos de Azure. Para detalles, vea [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).

## Configuración del modo Administrador de recursos de Azure

Dado que el modo Administrador de recursos de Azure no está habilitado de forma predeterminada, use el siguiente comando para habilitar los comandos del Administrador de recursos de la CLI de Azure.

	azure config mode arm

>[AZURE.NOTE] Los modos Administrador de recursos de Azure y Administración de servicios de Azure se excluyen mutuamente. Es decir, los recursos creados en un modo no se pueden administrar desde el otro.

## Búsqueda de ubicaciones

La mayoría de los comandos del Administrador de recursos de Azure necesitan una ubicación válida en los que crear o buscar un recurso. Puede encontrar todas las ubicaciones disponibles para los diferentes recursos de Azure mediante el siguiente comando.

	azure location list

Esto enumera los recursos de Azure y las regiones de Azure en las que están disponibles, como "Oeste de EE. UU.", "Este de EE. UU." y así sucesivamente.

## Crear un grupo de recursos

Un grupo de recursos es una agrupación lógica de recursos de red, de almacenamiento y de otros recursos. Casi todos los comandos en el modo Administrador de recursos de Azure necesitan un grupo de recursos. Puede crear un grupo de recursos denominado _testRG_; por ejemplo, mediante el siguiente comando.

	azure group create -n "testRG" -l "West US"

Este grupo de recursos "testRG" se implementará más adelante, cuando use una plantilla para iniciar una máquina virtual Ubuntu. Una vez que haya creado un grupo de recursos, podrá agregar recursos como máquinas virtuales y redes o almacenamiento.


## Uso de plantillas de grupo de recursos

### Localización y configuración de una plantilla de grupo de recursos

Cuando trabaje con plantillas, puede [crear una plantilla propia](resource-group-authoring-templates.md), o usar una de la [galería de plantillas](https://azure.microsoft.com/documentation/templates/), que están también disponibles en [GitHub](https://github.com/Azure/azure-quickstart-templates).

La creación de una nueva plantilla está fuera del ámbito de este artículo, así que para empezar vamos a usar la plantilla _101-simple-vm-from-image_ disponible en la [Galería de plantillas](https://azure.microsoft.com/documentation/templates/101-vm-simple-linux/). De forma predeterminada, esto crea una sola máquina virtual 14.04.2-LTS Ubuntu en una nueva red virtual con una sola subred en la región Oeste de EE. UU. Solo tiene que especificar los siguientes parámetros para utilizar esta plantilla:

* Un nombre de usuario de administración para la máquina virtual = `adminUsername`
* Una contraseña = `adminPassword`
* Un nombre de dominio para la máquina virtual = `dnsLabelPrefix`

>[AZURE.TIP] Estos pasos muestran solo una de las formas de usar una plantilla de máquina virtual con la CLI de Azure. Para ver otros ejemplos, consulte [Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y CLI de Azure](virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md).

1. Siga el enlace “Aprenda más con GitHub” para descargar los archivos azuredeploy.json y azuredeploy.parameters.json de GitHub en una carpeta de trabajo en el equipo local. (Asegúrese de seleccionar el formato _raw_ de cada archivo en GitHub).

2. Abra el archivo azuredeploy.parameters.json en un editor de texto y especifique valores de parámetro adecuados para su entorno (deje el valor **ubuntuOSVersion** sin modificar).

	```
			{
			  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
			  "contentVersion": "1.0.0.0",
			  "parameters": {
			    "adminUsername": {
			      "value": "azureUser"
			    },
			    "adminPassword": {
			      "value": "GEN-PASSWORD"
			    },
			    "dnsLabelPrefix": {
			      "value": "GEN-UNIQUE"
			    },
			    "ubuntuOSVersion": {
			      "value": "14.04.2-LTS"
			    }
			  }
			}

	```
3.  Una vez modificados los parámetros de la implementación, se implementará la máquina virtual Ubuntu en el grupo de recursos que se creó anteriormente. Elija un nombre para la implementación y use el comando siguiente para iniciarla.

	```
	azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json testRG testRGdeploy
	```

	En este ejemplo, se crea una implementación llamada _testRGDeploy_, que se implementa en el grupo de recursos _testRG_. La opción `-e` especifica el archivo azuredeploy.parameters.json que modificó en el paso anterior. La opción `-f` especifica el archivo de plantilla azuredeploy.json.

	Este comando se ejecutará correctamente después de que se haya cargado la implementación, pero antes de que se aplique a los recursos del grupo.

4. Utilice el comando siguiente para comprobar el estado de la implementación.

	```
	azure group deployment show "testRG" "testRGDeploy"
	```

	El valor **ProvisioningState** muestra el estado de la implementación.

	Si la implementación se realizó correctamente, verá un resultado similar al siguiente ejemplo.

		azure-cli@0.8.0:/# azure group deployment show testRG testDeploy
		info:    Executing command group deployment show
		+ Getting deployments
		+ Getting deployments
		data:    DeploymentName     : testDeploy
		data:    ResourceGroupName  : testRG
		data:    ProvisioningState  : Running
		data:    Timestamp          : 2015-10-26T16:15:29.5562024Z
		data:    Mode               : Incremental
		data:    Name                   Type          Value
		data:    ---------------------  ------------  ---------------------
		data:    newStorageAccountName  String        MyStorageAccount
		data:    adminUsername          String        MyUserName
		data:    adminPassword          SecureString  undefined
		data:    dnsNameForPublicIP     String        MyDomainName
		data:    ubuntuOSVersion        String        14.04.2-LTS
		info:    group deployment show command OK

	>[AZURE.NOTE] Si se da cuenta de que la configuración no es correcta y necesita detener una implementación de ejecución prolongada, use el comando siguiente.
	>
	> `azure group deployment stop "testRG" "testDeploy"`
	>
	> Si no proporciona un nombre de implementación, se crea automáticamente uno basado en el nombre del archivo de plantilla. Se devolverá como parte de la salida del comando `azure group create`.

	Ahora puede usar SSH para la máquina virtual, con el nombre de dominio especificado. Cuando se conecte a la máquina virtual, tiene que utilizar un nombre de dominio completo con formato `<domainName>.<region>.cloudapp.azure.com`, como `MyDomainName.westus.cloudapp.azure.com`.

5. Para ver el grupo, use el siguiente comando.

		azure group show "testRG"

	Este comando devuelve información acerca de los recursos del grupo. Si tiene varios grupos, puede utilizar el comando `azure group list` para recuperar una lista de nombres de grupos y, a continuación, usar `azure group show` para ver detalles de un grupo específico.

También puede usar una plantilla directamente desde [GitHub](https://github.com/Azure/azure-quickstart-templates), en lugar de descargar una en el equipo. Para ello, pase la dirección URL al archivo azuredeploy.json para la plantilla en su comando mediante la opción **--template-url**. Para obtener la dirección URL, abra azuredeploy.json en GitHub en modo _sin procesar_ y copie la dirección URL que aparece en la barra de direcciones del explorador. A continuación, puede usar esta dirección URL directamente para crear una implementación mediante un comando similar al siguiente ejemplo.

	azure group deployment create "testDeploy" testResourceGroup --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-linux/azuredeploy.json
Se le pedirá que escriba los parámetros de plantilla necesarios.

> [AZURE.NOTE] Es importante abrir la plantilla de JSON en modo _sin procesar_. La dirección URL que aparece en la barra de direcciones del explorador es diferente de la que aparece en el modo normal. Para abrir el archivo en modo _sin procesar_ mientras lo ve en GitHub, haga clic en **Sin procesar** en la esquina superior derecha.

## Trabajo con recursos

A pesar de que las plantillas le permiten declarar cambios de configuración en todo el grupo, a veces es necesario trabajar solo con un recurso específico. Puede hacerlo si utiliza los comandos `azure resource`.

> [AZURE.NOTE] Cuando utilice comandos `azure resource` distintos del comando `list`, debe especificar la versión de API del recurso con que está trabajando mediante el parámetro `-o`. Si no está seguro sobre la versión de API que debe utilizar, consulte el archivo de plantilla y busque el campo **apiVersion** correspondiente al recurso.

1. Utilice el comando siguiente para ver todos los recursos de un grupo.

		azure resource list "testRG"

2. Para ver un recurso individual dentro del grupo, use un comando como el siguiente.

		azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15"

	Observe el parámetro **Microsoft.Compute/virtualMachines**. Indica el tipo del recurso sobre el que solicita información. Si observa el archivo de plantilla que se descargó anteriormente, observará que este mismo valor se usa para definir el tipo de recurso de máquina virtual descrito en la plantilla.

	Este comando devuelve información relacionada con la máquina virtual.

3. Cuando se consultan los detalles de un recurso, generalmente resulta útil usar el parámetro `--json`. Esto hace que el resultado sea más legible, ya que algunos valores son estructuras anidadas o colecciones. El siguiente ejemplo demuestra los resultados obtenidos con el comando **show** como un documento JSON.

		azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15" --json

	>[AZURE.NOTE] Puede guardar los datos JSON en archivo si utiliza el carácter &gt; para canalizar la salida al archivo. Por ejemplo:
	>
	> `azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

4. Para eliminar un recurso existente, use un comando como el siguiente.

		azure resource delete "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15"

## Registro

Para ver información registrada sobre operaciones realizadas en un grupo, utilice el comando `azure group log show`. De manera predeterminada, indicará la última operación realizada en el grupo. Para ver todas las operaciones, utilice el parámetro opcional `--all`. Para ver la última implementación, utilice `--last-deployment`. Para ver una implementación específica, utilice `--deployment` y especifique el nombre de la implementación. El ejemplo siguiente devuelve un registro de todas las operaciones realizadas en el grupo *MyGroup*.

	azure group log show MyGroup --all

## Pasos siguientes

* Para obtener información sobre el trabajo con el Administrador de recursos de Azure con Azure PowerShell, consulte [Uso de Azure PowerShell con el Administrador de recursos de Azure](powershell-azure-resource-manager.md)
* Para obtener información sobre cómo trabajar con el Administrador de recursos de Azure desde el Portal de Azure, consulte [Uso de grupos de recursos para administrar los recursos de Azure][psrm].

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760

<!---HONumber=AcomDC_0211_2016-->