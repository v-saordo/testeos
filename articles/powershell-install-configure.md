<properties
	pageTitle="Cómo instalar y configurar Azure PowerShell"
	description="Obtenga información acerca de cómo instalar y configurar Azure PowerShell."
	editor="tysonn"
	manager="stevenka"
	documentationCenter=""
	services=""
	authors="coreyp-at-msft"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="coreyp"/>

# Cómo instalar y configurar Azure PowerShell#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="CLI de Azure">CLI de Azure</a></div>

##¿Qué es Azure PowerShell?#
Azure PowerShell es un módulo que ofrece cmdlet para administrar Azure con Windows PowerShell. Puede utilizar los cmdlets para crear, probar, implementar y administrar soluciones y servicios ofrecidos a través de la plataforma de Azure. En la mayoría de los casos, se pueden usar los cmdlet para las mismas tareas que el Portal de administración de Azure, como la creación y configuración de servicios en la nube, máquinas virtuales, redes virtuales y aplicaciones web.

<a id="Install"></a>
## Paso 1: Instalar

A continuación se indican los dos métodos con los que puede instalar Azure PowerShell. Puede instalarlo desde WebPI o de la Galería de PowerShell:

> [AZURE.NOTE] Puede que tenga que reiniciar tras la instalación para poder ver todos los comandos de la característica Entorno de scripting integrado (ISE) de Windows PowerShell.

###Instalar Azure PowerShell desde WebPI

La instalación de Azure PowerShell 1.0 y versiones posteriores desde WebPI es igual que para 0.9.x. Descargue [Azure Powershell](http://aka.ms/webpi-azps) e inicie la instalación. Si tiene Azure PowerShell 0.9.x instalado, se le pedirá que desinstale 0.9.x. Si ha instalado módulos de Azure PowerShell desde la Galería de PowerShell, el instalador requiere que los módulos se quiten antes de la instalación para garantizar un entorno coherente de Azure PowerShell.

> [AZURE.NOTE] Si ha instalado los módulos de Azure de la Galería de PowerShell, el instalador los quitará automáticamente. Esto tiene por objeto evitar confusiones sobre los módulos que ha instalado y dónde se encuentran. Los módulos de la Galería de PowerShell normalmente se instalarán en **%ProgramFiles%\\WindowsPowerShell\\Modules**. En cambio, el instalador de WebPI instalará los módulos de Azure en **%ProgramFiles%\\Microsoft SDKs\\Azure\\PowerShell**. **PowerShellGet** desinstalará módulos y dejará los archivos .dll bloqueados y sus carpetas si hay cargada una dependencia del módulo cuando se desinstala. Si se produce un error durante la instalación, elimine las carpetas de Azure* de su carpeta **%ProgramFiles%\\WindowsPowerShell\\Modules**.

Si ha instalado Azure PowerShell a través de la Galería de PowerShell y en su lugar desea usar la instalación de WebPI, dicha instalación quitará automáticamente los cmdlets instalados desde la galería.

> [AZURE.NOTE] Existe un problema conocido con **$env: PSModulePath** de PowerShell que se produce cuando la instalación se realiza desde WebPI. Si el equipo requiere un reinicio debido a actualizaciones del sistema u otras instalaciones, puede hacer que **$env: PSModulePath** no incluya la ruta de acceso donde está instalado Azure PowerShell. Para corregir esto, reinicie el equipo.

###Instalación de Azure PowerShell desde la galería

Instale Azure PowerShell 1.0 o versión posterior desde la galería mediante los siguientes comandos:

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM
    Install-AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

    # Import AzureRM modules for the given version manifest in the AzureRM module
    Import-AzureRM

    # Import Azure Service Management module
    Import-Module Azure

####Más información sobre estos comandos

- **Install-Module AzureRM** instala un módulo de arranque para los módulos de AzureRM. Este módulo contiene cmdlets para actualizar, desinstalar e importar los módulos de AzureRM de una manera segura y coherente. El módulo AzureRM contiene una lista de módulos y el intervalo de versiones (min y max) necesario para asegurarse se introducirán cambios importantes para la versión principal de AzureRM. Para más información sobre el control de versiones semánticas, consulte [semver.org](http://semver.org). Esto significa que puede crear sus cmdlets con una versión específica de AzureRM y saber que todos los módulos instalados mediante el programa previo, no introducirán ningún cambio importante.
- **Install-AzureRM** instala todos los módulos declarados en el módulo de arranque.
- **Install-Module Azure** instala el módulo de Azure. Se trata del módulo Administración de servicios de Azure PowerShell 0.9.x. No debe tener cambios importantes y poderse intercambiar por la versión anterior del módulo de Azure.
- **Import-AzureRM** importa todos los módulos de la lista de módulos y versiones del módulo de AzureRM. De este modo se garantiza que los módulos de Azure PowerShell que se cargan quedan dentro del intervalo de versiones requerido por el módulo AzureRM.
- **Import-Module Azure** importa el módulo Administración de servicios de Azure. Observe que el módulo de Azure y los módulos de AzureRM se cargan en la sesión de PowerShell y se pueden usar juntos.


## Paso 2: Iniciar
Puede ejecutar los cmdlets desde la consola estándar de Windows PowerShell o desde el Entorno de scripting integrado (ISE) de PowerShell. El método que debe seguir para abrir cualquiera de estas consolas depende de la versión de Windows que esté utilizando:

- Si el equipo dispone de Windows 8 o Windows Server 2012, o una versión posterior, puede utilizar la búsqueda integrada. En la pantalla **Inicio**, comience a escribir power. Se devolverá una lista de aplicaciones que incluyen Windows PowerShell. Haga clic en cualquier aplicación para abrir la consola. (Para anclar la aplicación a la pantalla **Inicio**, haga clic con el botón derecho en el icono).

- Si el equipo dispone de una versión anterior a Windows 8 o Windows Server 2012, use el menú **Inicio**. En el menú **Inicio**, haga clic sucesivamente en **Todos los programas**, **Accesorios**, en la carpeta **Windows PowerShell** y luego en **Windows PowerShell**.

También puede ejecutar **Windows PowerShell ISE** para usar elementos de menú y métodos abreviados de teclado para realizar muchas de las mismas tareas que realizaría en la consola de Windows PowerShell. Para usar el ISE, en la consola de Windows PowerShell, ejecute Cmd.exe o, en el cuadro **Ejecutar**, escriba **powershell\_ise.exe**.

###Comandos para ayudarle a empezar a trabajar

    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 
	
	# If the Azure PowerShell module is not listed when you run Get-Module, you may need to import it
    Import-Module Azure 
	
    # To login to Azure Resource Manager
    Login-AzureRmAccount

    # You can also use a specific Tenant if you would like a faster login experience
    # Login-AzureRmAccount -TenantId xxxx

    # To view all subscriptions for your account
    Get-AzureRmSubscription

    # To select a default subscription for your current session
    Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

    # View your current Azure PowerShell session context
    # This session state is only applicable to the current session and will not affect other sessions
    Get-AzureRmContext

    # To select the default storage context for your current session
    Set-AzureRmCurrentStorageAccount –ResourceGroupName “your resource group” –StorageAccountName “your storage account name”

    # View your current Azure PowerShell session context
    # Note: the CurrentStorageAccount is now set in your session context
    Get-AzureRmContext

    # To import the Azure.Storage data plane module (blob, queue, table)
    Import-Module Azure.Storage

    # To list all of the blobs in all of your containers in all of your accounts
    Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob


## Paso 3: Conectar
Los cmdlets necesitan la suscripción para que puedan administrar los servicios. Puede comprar una suscripción de Azure si no tiene ya una. Para instrucciones, consulte [Instrucciones para contratar Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Escriba **Login-AzureRmAccount**.

2. Escriba la dirección de correo electrónico y la contraseña asociadas a su cuenta. Azure autentica y guarda las credenciales y, a continuación, cierra la ventana.

--O--

Inicie sesión en su cuenta profesional o educativa:

    $cred = Get-Credential
    Login-AzureRmAccount -Credential $cred
> [AZURE.NOTE] Si tiene más de un inquilino asociado a la cuenta de su organización, especifique el parámetro TenantId:

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [AZURE.NOTE] Este método de inicio de sesión no interactivo solo funciona con una cuenta profesional o educativa. Una cuenta profesional o educativa es un usuario bajo la administración del trabajo o de la escuela y que está definido en la instancia de Azure Active Directory para el trabajo o la escuela. Si actualmente no tiene una cuenta profesional o educativa y usa una cuenta Microsoft para iniciar sesión en su suscripción de Azure, puede crear una fácilmente siguiente los pasos que se indican a continuación.

> 1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com) y haga clic en **Active Directory**.

> 2. Si no hay ningún directorio, seleccione **Crear su directorio** y proporcione la información que se le pida.

> 3. Seleccione su directorio y agregue un nuevo usuario. Este usuario nuevo puede iniciar sesión con una cuenta profesional o educativa. Durante la creación del usuario, se le proporcionará una dirección de correo electrónico para el usuario y una contraseña temporal. Guarde esta información, la usará en el paso 5 que viene a continuación.

> 4. En el portal de administración, seleccione **Configuración** y, a continuación, **Administradores**. Seleccione **Agregar** y agregue el usuario nuevo como coadministrador. Esto permite que la cuenta profesional o educativa administre su suscripción de Azure.

> 5. Finalmente, cierre sesión en el portal de Azure y, a continuación, vuelva a iniciarla usando la cuenta profesional o educativa. Si es la primera vez que inicia sesión con esta cuenta, se le pedirá que cambie la contraseña.

> Para obtener más información acerca del inicio de sesión en Microsoft Azure con una cuenta profesional o educativa, consulte [Inicio de sesión en Azure como una organización](sign-up-organization.md).

> Para obtener más información acerca de la autenticación y la administración de la suscripción en Azure, consulte [Administrar cuentas, suscripciones y roles administrativos](http://go.microsoft.com/fwlink/?LinkId=324796).

### Visualización de los detalles de las cuentas y las suscripciones

Puede tener varias cuentas y suscripciones disponibles para su uso por parte de Azure PowerShell. Puede agregar varias cuentas ejecutando **Add-AzureRmAccount** más de una vez.

Para mostrar las cuentas de Azure disponibles, escriba **Get-AzureAccount**.

Para mostrar las suscripciones de Azure, escriba **Get-AzureRmSubscription**.

##<a id="Help"></a>Ayuda##

Estos recursos ofrecen ayuda para cmdlets concretos:


-   Desde la consola, puede utilizar el sistema de ayuda integrado. El cmdlet **Get-Help** proporciona acceso a este sistema. 

- Para obtener ayuda de la comunidad, utilice estos foros populares:

 - [Foro de Azure en MSDN](http://go.microsoft.com/fwlink/p/?LinkId=320212)
 - [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

##Más información


Para más información sobre el uso de cmdlets, vea los siguientes recursos:

Para obtener instrucciones básicas sobre el uso de Windows PowerShell, consulte [Uso de Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=321939).

Para más información sobre los cmdlets, consulte [Referencia de cmdlets de Azure](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Para ver scripts de ejemplo e instrucciones que le ayuden a aprender a usar scripting para administrar Azure, visite el [Centro de scripts](http://go.microsoft.com/fwlink/p/?LinkId=321940).

<!---HONumber=AcomDC_0218_2016-->