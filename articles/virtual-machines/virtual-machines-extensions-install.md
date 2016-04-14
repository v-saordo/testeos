<properties
 pageTitle="Administración de extensiones de máquina virtual | Microsoft Azure"
 description="Describe cómo agregar, buscar, actualizar y quitar extensiones para máquinas virtuales de Azure, en el modelo de implementación clásico."
 services="virtual-machines"
 documentationCenter=""
 authors="squillace"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
 ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="12/08/2015"
 ms.author="rasquill"/>
#Administración de extensiones de máquina virtual

Describe cómo encontrar, agregar, modificar o quitar extensiones de VM con máquinas virtuales de Windows o Linux en Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos


##Uso de extensiones de máquina virtual

Las extensiones de VM de Azure implementan comportamientos o características que bien ayudan a otros programas a funcionar en máquinas virtuales de Azure (por ejemplo, la extensión **WebDeployForVSDevTest** permite Visual Studio en soluciones Web Deploy en la VM de Azure), o bien ofrecen la posibilidad de interactuar con la VM para que admita algún otro comportamiento (por ejemplo, puede usar las extensiones de acceso de VM desde Powershell, la CLI de Azure y clientes REST para restablecer o modificar valores de acceso remoto en la VM de Azure).

>[AZURE.IMPORTANT]Para una lista completa de extensiones por las características que admiten, vea [Características y extensiones de máquina virtual de Azure](virtual-machines-extensions-features.md). Dado que cada extensión de máquina virtual admite una característica específica, lo que se puede y no se puede hacer exactamente con una extensión depende de la extensión. Por lo tanto, antes de modificar la máquina virtual, asegúrese de leer la documentación de la extensión de máquina virtual que quiera usar. Algunas extensiones de máquina virtual no admiten que se quiten; otras tienen propiedades que se pueden establecer y cambian radicalmente el comportamiento de la máquina virtual.

Las tareas más comunes son:

1.  Buscar extensiones disponibles

2.  Actualizar extensiones cargadas

3.  Agregar extensiones

4.  Quitar extensiones

##Búsqueda de extensiones disponibles

Son extensiones de máquina virtual de Azure (para una lista completa de extensiones por las características que admiten, vea [Características y extensiones de máquina virtual de Azure](virtual-machines-extensions-features.md)). Puede buscar la extensión e información extendida con:

-   PowerShell
-   Interfaz entre plataformas de Azure (CLI de Azure)
-   API de REST de administración del servicio

Use los cmdlet de [Azure PowerShell](https://msdn.microsoft.com/library/azure/dn495240.aspx) o las [API de REST de administración de servicios](https://msdn.microsoft.com/library/ee460799.aspx) para encontrar información sobre extensiones disponibles.

###Azure PowerShell

Algunas extensiones tienen cmdlets de Powershell específicos para ellas, lo que puede facilitar su configuración en PowerShell; pero los siguientes cmdlets funcionan para todas las extensiones de máquina virtual.

Puede usar los cmdlets siguientes para obtener información sobre las extensiones disponibles:

-   Para instancias de roles web o roles de trabajo, puede usar el cmdlet [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx).
-   Para instancias de máquinas virtuales, puede usar el cmdlet [Get-AzureVMAvailableExtension](https://msdn.microsoft.com/library/azure/dn722480.aspx).

     En el ejemplo de código siguiente se indica cómo se muestra la información de la extensión **IaaSDiagnostics** con PowerShell.

        PS C:\PowerShell> Get-AzureVMAvailableExtension -ExtensionName IaaSDiagnostics
        VERBOSE: 5:09:01 PM - Begin Operation: Get-AzureVMAvailableExtension
        VERBOSE: 5:09:06 PM - Completed Operation: Get-AzureVMAvailableExtension

        Publisher                   : Microsoft.Azure.Diagnostics
        ExtensionName               : IaaSDiagnostics
        Version                     : 1.2
        Label                       : Microsoft Monitoring Agent Diagnostics
        Description                 : Microsoft Monitoring Agent Extension
        PublicConfigurationSchema   :
        PrivateConfigurationSchema  :
        IsInternalExtension         : False
        SampleConfig                :
        ReplicationCompleted        : True
        Eula                        :
        PrivacyUri                  :
        HomepageUri                 :
        IsJsonExtension             : True
        DisallowMajorVersionUpgrade : False
        SupportedOS                 :
        PublishedDate               :
        CompanyName                 :


###Interfaz de la línea de comandos de Azure (CLI de Azure)

Algunas extensiones tienen comandos de CLI de Azure específicos para ellas (la extensión de máquina virtual de Docker es un ejemplo), lo que puede facilitar su configuración; pero los siguientes comandos funcionan para todas las extensiones de máquina virtual.

Puede usar el comando **azure vm extension list** para obtener información sobre las extensiones disponibles y usar la opción **–-json** para mostrar toda la información disponible sobre una o varias extensiones. Si no incluye un nombre de extensión, el comando devuelve una descripción json de todas las extensiones disponibles.

En el ejemplo de código siguiente se indica cómo se muestra la información de la extensión **IaaSDiagnostics** con el comando **azure vm extension list** de CLI de Azure y se usa la opción **–-json** para devolver información completa.


    $ azure vm extension list -n IaaSDiagnostics --json
    [
      {
        "publisher": "Microsoft.Azure.Diagnostics",
        "name": "IaaSDiagnostics",
        "version": "1.2",
        "label": "Microsoft Monitoring Agent Diagnostics",
        "description": "Microsoft Monitoring Agent Extension",
        "replicationCompleted": true,
        "isJsonExtension": true
      }
    ]



###API de REST de administración del servicio

Puede usar las API de REST siguientes para obtener información sobre las extensiones disponibles:

-   Para instancias de roles web o roles de trabajo, puede usar la operación [Enumerar extensiones disponibles](https://msdn.microsoft.com/library/dn169559.aspx). Para mostrar las versiones de las extensiones disponibles, puede usar [Enumerar versiones de extensión](https://msdn.microsoft.com/library/dn495437.aspx).

-   Para instancias de máquinas virtuales, puede usar la operación [Enumerar extensiones de recursos](https://msdn.microsoft.com/library/dn495441.aspx). Para mostrar las versiones de las extensiones disponibles, puede usar [Enumerar versiones de extensiones de recursos](https://msdn.microsoft.com/library/dn495440.aspx).

##Adición, actualización o deshabilitación de extensiones

Las extensiones pueden agregarse cuando se crea una instancia o se pueden agregar a una instancia en ejecución. Las extensiones se pueden actualizar, deshabilitar o quitar. Puede realizar estas acciones mediante cmdlets de Azure PowerShell o mediante operaciones de la API de REST de administración de servicios. Se requieren parámetros para instalar y configurar algunas extensiones. Se admiten parámetros públicos y privados con las extensiones.


###Azure PowerShell

El uso de cmdlets de Azure PowerShell es la manera más fácil de agregar y actualizar extensiones. Al usar los cmdlets de extensión, la mayor parte de la configuración de la extensión se realiza automáticamente. En ocasiones, puede que necesite agregar una extensión mediante programación. En ese caso, debe proporcionar la configuración de la extensión.

Puede utilizar los cmdlets siguientes para saber si una extensión requiere una configuración de parámetros públicos o privados:

-   Para instancias de roles web o roles de trabajo, puede usar el cmdlet **Get-AzureServiceAvailableExtension**.

-   Para instancias de máquinas virtuales, puede usar el cmdlet **Get-AzureVMAvailableExtension**.

###API de REST de administración del servicio

Cuando recupera una lista de extensiones disponibles mediante las API de REST, recibirá información sobre cómo se tiene que configurar la extensión. Puede que la información que se devuelva muestre información sobre parámetros representada por un esquema público y un esquema privado. Los valores de parámetros públicos se devuelven en las consultas sobre las instancias. No se devuelven los valores de parámetros privados.

Puede usar las API de REST siguientes para saber si una extensión requiere una configuración de parámetros públicos o privados:

-   Para las instancias de roles web o roles de trabajo, los elementos **PublicConfigurationSchema** y **PrivateConfigurationSchema** contienen la información de la respuesta en la operación [Enumerar extensiones disponibles](https://msdn.microsoft.com/library/dn169559.aspx).

-   Para las instancias de máquinas virtuales, los elementos **PublicConfigurationSchema** y **PrivateConfigurationSchema** contienen la información de la respuesta en la operación [Enumerar extensiones de recursos](https://msdn.microsoft.com/library/dn495441.aspx).

>[AZURE.NOTE]Las extensiones también pueden usar configuraciones que se definen con JSON. Cuando se utilizan estos tipos de extensiones, solo se usa el elemento **SampleConfig**.

<!---HONumber=AcomDC_1217_2015-->