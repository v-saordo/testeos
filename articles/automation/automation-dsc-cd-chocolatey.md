<properties
   pageTitle="Implementación continua de DSC de Automatización de Azure con Chocolatey | Microsoft Azure"
   description="Implementación continua de DevOps mediante DSC de Automatización de Azure y el administrador de paquetes Chocolatey. Ejemplo con plantilla de ARM JSON completa y código fuente de PowerShell."
   services="automation"
   documentationCenter=""
   authors="sebastus"
   manager="stevenka"
   editor=""/>

<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="11/11/2015"
   ms.author="golive"/>

# Ejemplo de uso: implementación continua en máquinas virtuales DSC de Automatización y Chocolatey

En el mundo del desarrollo de software con DevOps, existen muchas herramientas que ayudan en diversos puntos de la canalización de integración continua. Configuración de estado deseado (DSC) de Automatización de Azure es una opción nueva, y bienvenida, que puede ser de utilidad para los equipos de DevOps. En este artículo, se muestra cómo configurar la implementación continua (CD) para un equipo de Windows. Puede ampliar fácilmente la técnica para incluir tantos equipos de Windows como sean necesarios en el rol (por ejemplo, un sitio web) y, a partir de ahí, también a los roles adicionales.

![Implementación continua para máquinas virtuales IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## A nivel general

Aunque todo esto pueda parecer bastante complicado, afortunadamente puede dividirse en dos procesos principales:

  - Escribir código y probarlo, y después crear y publicar paquetes de instalación para las versiones principales y secundarias del sistema; 
  - Crear y administrar las máquinas virtuales que instalarán y ejecutarán el código en los paquetes.  

Una vez que estos dos procesos principales se hayan puesto en práctica, queda poco para actualizar automáticamente el paquete que se ejecuta en cualquier máquina virtual concreta a medida que se creen e implementen las nuevas versiones.

## Información general sobre los componentes

Los administradores de paquetes como [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) son muy conocidos en el mundo de Linux, pero no tanto en el de Windows. [Chocolatey](https://chocolatey.org/) es uno de ellos, y Scott Hanselman proporciona una estupenda introducción al tema en su [blog](http://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx). En pocas palabras, Chocolatey permite instalar paquetes desde un repositorio central de paquetes en un sistema de Windows mediante la línea de comandos. Puede crear y administrar su propio repositorio y Chocolatey puede instalar paquetes desde cualquier número de repositorios que designe.

Configuración de estado deseado (DSC) ([introducción](https://technet.microsoft.com/library/dn249912.aspx)) es una herramienta de PowerShell que permite declarar la configuración que desee para un equipo. Por ejemplo, puede indicar "Quiero instalar Chocolatey, quiero instalar IIS, necesito abrir el puerto 80, quiero instalar la versión 1.0.0 de mi sitio web". El Administrador de configuración local (LCM) de DSC implementa esa configuración. Un servidor de extracción de DSC contiene un repositorio de configuraciones para los equipos. El LCM de cada equipo comprueba periódicamente para ver si su configuración coincide con la almacenada. Después, notifica el estado o intenta equiparar la configuración del equipo a la almacenada. Puede editar la configuración almacenada en el servidor de extracción para hacer que un equipo o un conjunto de equipos se adapten a la configuración modificada.

Automatización de Azure es un servicio administrado de Microsoft Azure que le permite automatizar diversas tareas con runbooks, nodos, credenciales, recursos y otros activos como programaciones y variables globales. DSC de Automatización de Azure amplía esta capacidad de automatización para incluir las herramientas de DSC de PowerShell. Aquí hay una [introducción](automation-dsc-overview.md) muy buena.

Un recurso de DSC es un módulo de código con capacidades específicas, como la administración de redes, Active Directory o SQL Server. El recurso de DSC de Chocolatey sabe cómo acceder a un servidor NuGet (entre otros), descargar paquetes, instalar paquetes, etc. Existen muchos otros recursos de DSC en la [Galería de PowerShell](http://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title). Estos módulos se instalan en el servidor de extracción de DSC de Automatización de Azure (manualmente) para que se puedan usar con sus configuraciones.

Las plantillas de ARM proporcionan una forma declarativa de generar la infraestructura: como las redes, las subredes, la seguridad de red y el enrutamiento, los equilibradores de carga, las NIC, las máquinas virtuales, etc. En este [artículo](../resource-manager-deployment-model.md), se compara el modelo de implementación de ARM (declarativo) con el de Administración de servicios de Azure (ASM o clásico), que es imperativo. Este otro [artículo](../virtual-machines\virtual-machines-azurerm-versus-azuresm.md) trata acerca de los proveedores de recursos principales, procesos, redes y almacenamiento.

Una característica clave de una plantilla de ARM es su capacidad para instalar una extensión de máquina virtual en la máquina virtual cuando se aprovisiona. Una extensión de máquina virtual posee capacidades específicas, como ejecutar un script personalizado, instalar software antivirus o ejecutar un script de configuración de DSC. Existen muchos otros tipos de extensiones de máquina virtual.

## Vista rápida al diagrama

Empezando desde arriba, se escribe el código, se compila y prueba, y después se crea un paquete de instalación. Chocolatey admite diversos tipos de paquetes de instalación, como MSI, MSU o ZIP. Además, cuenta con toda la funcionalidad de PowerShell para realizar la instalación en sí si las capacidades nativas de Chocolatey no son suficientes. Se coloca el paquete en un lugar accesible: un repositorio de paquetes. En este ejemplo de uso se emplea una carpeta pública en una cuenta de almacenamiento de blobs de Azure, pero vale cualquier lugar. Chocolatey funciona de forma nativa con los servidores NuGet y otros para administrar los metadatos de los paquetes. En [este artículo](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) se describen las opciones. Este ejemplo de uso utiliza NuGet. Nuspec son los metadatos de los paquetes. Los nuspecs se "compilan" en forma de NuPkg y se almacenan en un servidor NuGet. Cuando la configuración solicita un paquete por su nombre y hace referencia a un servidor NuGet, el recurso de DSC de Chocolatey (ahora en la máquina virtual) toma el paquete y lo instala automáticamente. También se puede solicitar una versión específica de un paquete.

En la parte inferior izquierda de la imagen, se ve una plantilla del Administrador de recursos de Azure (ARM). En este ejemplo de uso, la extensión de máquina virtual registra la máquina virtual con el servidor de extracción de DSC de Automatización de Azure (es decir, un servidor de extracción) como nodo. La configuración se almacena en el servidor de extracción. En realidad, se almacena dos veces: una vez como texto sin formato y otra como archivo MOF (para aquellos a quienes les interese). En el portal, el MOF es una "configuración de nodo" (en lugar de simplemente una "configuración"). Es el artefacto que se asocia con un nodo para que el nodo conozca su configuración. Los siguientes detalles muestran cómo asignar la configuración de nodo al nodo.

Se supone que ya está haciendo lo de arriba, al menos en su mayor parte. Crear el nuspec, compilarlo y almacenarlo en un servidor NuGet no es muy complicado y, además, ya está administrando máquinas virtuales. Para dar el siguiente paso en la implementación continua, se debe configurar el servidor de extracción (una vez), registrar los nodos con él (una vez) y crear y almacenar la configuración allí (inicialmente). Luego, como los paquetes se actualizan e implementan en el repositorio, actualice la configuración y la configuración de nodo en el servidor de extracción (repítalo si es necesario).

No comenzar con una plantilla de ARM también es perfectamente válido. Hay cmdlets de PowerShell diseñados para ayudarlo a registrar sus máquinas virtuales con el servidor de extracción y todo lo demás. Para obtener más detalles, vea este artículo: [Incorporación de máquinas para administrarlas con DSC de Automatización de Azure](automation-dsc-onboarding.md).


## Paso 1: Configurar la cuenta de automatización y el servidor de extracción

En una línea de comandos de PowerShell (Add-AzureAccount) autenticada: (puede tardar unos minutos mientras se configura el servidor de extracción)

    New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
    New-AzureAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT 

Puede situar su cuenta de automatización en cualquiera de las siguientes regiones (o ubicaciones): Este de Japón, Este de EE. UU. 2, Europa occidental, Sudeste de Asia, Centro-Sur de EE. UU.

## Paso 2: Ajustes de la extensión de máquina virtual en la plantilla de ARM

Se proporcionan detalles sobre el registro de máquina virtual (con la extensión de máquina virtual de DSC de PowerShell) en esta [plantilla de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver). En este paso, se registra la nueva máquina virtual con el servidor de extracción en la lista de nodos de DSC. Parte de este registro es especificar la configuración del nodo que se aplica al nodo. Esta configuración de nodo no tiene que existir aún en el servidor de extracción, por tanto, es correcto que sea el paso 4 en donde se realice esto por primera vez. Sin embargo, aquí en el paso 2 debe haber decidido el nombre del nodo y el nombre de la configuración. En este ejemplo de uso, el nodo es "isvbox" y la configuración es "ISVBoxConfig". Por tanto, el nombre de configuración del nodo (que se especificará en DeploymentTemplate.json) es "ISVBoxConfig.isvbox".

## Paso 3: Agregar recursos de DSC necesarios al servidor de extracción

La Galería de PowerShell está equipada para instalar recursos de DSC en su cuenta de Automatización de Azure. Desplácese hasta el recurso que desee y haga clic en el botón "Deploy to Azure Automation".

![Ejemplo de la Galería de PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

También podemos aplicar el enfoque manual. La estructura de carpetas de un módulo de integración de PowerShell para un equipo de Windows es algo diferente de la estructura de carpetas que espera la Automatización de Azure. Por tanto, es necesario hacer algunos ajustes. Pero no es difícil y solo es necesario hacerlos una vez para cada recurso (a menos que desee actualizarlo en el futuro.) Para obtener más información sobre la creación de módulos de integración de PowerShell, vea este artículo: [Creación de módulos de integración para la automatización de Azure](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/).

-   Instale el módulo que necesita en su estación de trabajo, como sigue:
    -   Instale [Windows Management Framework, v5](http://aka.ms/wmf5latest). 
    -   `Install-Module  –ModuleName MODULENAME` <—toma el módulo de la Galería de PowerShell. 
-   Copie la carpeta del módulo de `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` a una carpeta temporal. 
-   Elimine los ejemplos y la documentación de la carpeta principal. 
-   Comprima la carpeta principal y asigne al archivo comprimido el mismo nombre que la carpeta. 
-   Coloque el archivo zip en una ubicación http accesible, como almacenamiento de blobs en una cuenta de almacenamiento de Azure.
-   Ejecute este código de PowerShell:

        New-AzureAutomationModule ``
            -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT ``
            -Name MODULE-NAME –ContentLink "https://STORAGE-URI/public/MODULE-NAME.zip"
        

El ejemplo incluido realiza estos pasos para cChoco y xNetworking. Consulte las [Notas](#notes) del control especial para cChoco.

## Paso 4: Agregar la configuración de nodo al servidor de extracción

No hay nada especial que mencionar acerca de la primera vez que se importa la configuración al servidor de extracción y se compila. Todas las importaciones y compilaciones posteriores de la misma configuración tendrán exactamente el mismo aspecto. Cada vez que actualice el paquete y deba enviarlo a producción, realice este paso después de asegurarse de que el archivo de configuración sea correcto, incluida la nueva versión del paquete. Este es el archivo de configuración y el código de PowerShell:

ISVBoxConfig.ps1:

    Configuration ISVBoxConfig 
    { 
        Import-DscResource -ModuleName cChoco 
        Import-DscResource -ModuleName xNetworking
    
        Node "isvbox" {   
    
            cChocoInstaller installChoco 
            { 
                InstallDir = "C:\choco" 
            }
    
            WindowsFeature installIIS 
            { 
                Ensure="Present" 
                Name="Web-Server" 
            }
    
            xFirewall WebFirewallRule 
            { 
                Direction = "Inbound" 
                Name = "Web-Server-TCP-In" 
                DisplayName = "Web Server (TCP-In)" 
                Description = "IIS allow incoming web site traffic." 
                DisplayGroup = "IIS Incoming Traffic" 
                State = "Enabled" 
                Access = "Allow" 
                Protocol = "TCP" 
                LocalPort = "80" 
                Ensure = "Present" 
            }
    
            cChocoPackageInstaller trivialWeb 
            {            
                Name = "trivialweb" 
                Version = "1.0.0" 
                Source = “MY-NUGET-V2-SERVER-ADDRESS” 
                DependsOn = "[cChocoInstaller]installChoco", 
                "[WindowsFeature]installIIS" 
            } 
        }    
    }

New-ConfigurationScript.ps1:

    Import-AzureRmAutomationDscConfiguration ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 ` 
        -Published –Force
    
    $jobData = Start-AzureRmAutomationDscCompilationJob ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -ConfigurationName ISVBoxConfig 
    
    $compilationJobId = $jobData.Id
    
    Get-AzureRmAutomationDscCompilationJob ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -Id $compilationJobId

Estos pasos dan como resultado que se coloque una nueva configuración de nodo denominada "ISVBoxConfig.isvbox" en el servidor de extracción. El nombre de configuración del nodo se crea como "configurationName.nodeName".

## Paso 5: Crear y mantener los metadatos del paquete

Cada paquete que incluya en el repositorio de paquetes necesita un nuspec que lo describa. Ese nuspec se debe compilar y almacenar en el servidor NuGet. Este proceso se describe [aquí](http://docs.nuget.org/create/creating-and-publishing-a-package). Puede usar MyGet.org como servidor NuGet. Se vende este servicio, pero también se ofrece una SKU de inicio gratuita. En NuGet.org encontrará instrucciones para instalar su propio servidor NuGet para los paquetes privados.

## Paso 6: Unión de todos los elementos

Cada vez que una versión pasa un control de calidad y se aprueba para implementación, se crea el paquete, se actualizan nuspec y nupkg, y se implementan en el servidor NuGet. Además, se debe actualizar la configuración (el paso 4 anterior) para que coincida con el nuevo número de versión. Se debe enviar al servidor de extracción y se debe compilar. Desde ese momento, corresponderá a las máquinas virtuales que dependen de esa configuración extraer la actualización e instalarla. Cada una de estas actualizaciones son sencillas: solo una línea o dos de PowerShell. En el caso de Visual Studio Team Services, algunas de ellas se encapsulan en tareas de compilación que se pueden encadenar de manera conjunta en una compilación. En [este artículo](https://www.visualstudio.com/es-ES/get-started/build/build-your-app-vs) se ofrecen más detalles. En este [repositorio de GitHub](https://github.com/Microsoft/vso-agent-tasks) se detallan las diversas tareas de compilación disponibles.

## Notas

Este ejemplo de uso comienza con una máquina virtual desde una imagen genérica de Windows 2012 R2 procedente de la Galería de Azure. Puede iniciarlo desde cualquier imagen almacenada y después ajustarla con la configuración de DSC. Sin embargo, retocar la configuración que se incluye en una imagen es mucho más difícil que actualizar dinámicamente la configuración con DSC.

No es necesario usar una plantilla de ARM ni la extensión de máquina virtual para emplear esta técnica con sus máquinas virtuales. Además, sus máquinas virtuales tampoco tienen que estar en Azure para administrarlas mediante CD. Todo lo que se necesita es que Chocolatey esté instalado y LCM esté configurado en la máquina virtual para que conozca la ubicación del servidor de extracción.

Por supuesto, cuando se actualiza un paquete en una máquina virtual que se encuentra en producción, debe sacar esa máquina virtual de producción mientras se instala la actualización. Existen métodos muy diversos de hacerlo. Por ejemplo, con una máquina virtual tras un equilibrador de carga de Azure, puede agregar un sondeo personalizado. Al actualizar la máquina virtual, haga que el extremo de sondeo devuelva 400. El ajuste necesario para ocasionar este cambio puede estar dentro de la configuración, al igual que el ajuste para devolver de nuevo 200 una vez completada la actualización.

La versión del recurso de DSC de cChoco en la Galería de PowerShell no está lo actualizada con su origen. cChoco.zip del proyecto de origen de GitHub está actualizado. Utilice la técnica manual anterior del paso 3 para instalarlo.

El código fuente completo de este ejemplo de uso se encuentra en [este proyecto de Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) en GitHub.

##Artículos relacionados##

- [Información general de DSC de Automatización de Azure](automation-dsc-overview.md)
- [Cmdlets de DSC de Automatización de Azure](https://msdn.microsoft.com/library/mt244122.aspx)
- [Incorporación de máquinas para administrarlas con DSC de Automatización de Azure](automation-dsc-onboarding.md)

<!---HONumber=AcomDC_0128_2016-->