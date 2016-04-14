<properties
 pageTitle="Características y extensiones de las máquinas virtuales | Microsoft Azure"
 description="Obtenga información acerca de qué extensiones están disponibles para máquinas virtuales de Azure, agrupadas por lo que proporcionan o mejoran."
 services="virtual-machines"
 documentationCenter=""
 authors="squillace"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager"/>

<tags
 ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="12/08/2015"
 ms.author="rasquill"/>
#Acerca de las características y extensiones de las máquinas virtuales

Microsoft Azure proporciona extensiones de máquina virtual creadas por Microsoft y proveedores de terceros de confianza para habilitar seguridad, tiempo de ejecución, administración y otras características que puede aprovechar con el fin de aumentar su productividad con Máquinas virtuales de Azure. En este tema, se describen varias características que las extensiones de máquina virtual de Azure brindan a las máquinas virtuales de Windows y Linux para su uso y apunta a documentación correspondiente a cada una de ellas.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



Para detalles sobre los agentes de máquina virtual y cómo funcionan para admitir las extensiones de máquina virtual, vea [Información general sobre el agente de máquina virtual y las extensiones de máquina virtual](virtual-machines-extensions-install.md).

##Extensiones de máquina virtual de Azure

Las extensiones de máquina virtual implementan la mayor parte de la funcionalidad crítica que desea usar con las máquinas virtuales, incluida funcionalidad básica, como el restablecimiento de contraseñas, la configuración del protocolo de escritorio remoto y muchas otras más. Como siempre se agregan extensiones nuevas, la cantidad de posibles características que admiten las máquinas virtuales en Azure sigue creciendo. De manera predeterminada, se instalan varias extensiones básicas de máquina virtual cuando crea la máquina virtual desde la Galería de imágenes, incluida **IaaSDiagnostics** (actualmente, solo para máquinas virtuales de Windows), **VMAccess** y **BGInfo** (también disponible actualmente solo para Windows). Sin embargo, no todas las extensiones se implementan en Windows y Linux en un momento específico, debido al flujo constante de actualizaciones de características y extensiones nuevas.

##Administración de conectividad y administración básica

Las extensiones siguientes son críticas para habilitar, volver a habilitar o deshabilitar la conectividad básica con las máquinas virtuales una vez creadas y en ejecución.

|Nombre de la extensión de máquina virtual|Descripción de la característica|Más información
|---|---|---|
|VMAccessAgent (Windows)|Crear, actualizar y restablecer la información del usuario y las configuraciones de conexión RDP y SSH.|[Windows](virtual-machines-extensions-customscript.md)
|VMAccessForLinux (Linux)|Crear, actualizar y restablecer la información del usuario y las configuraciones de conexión RDP y SSH.|[Linux](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)

##Administración de la implementación y la configuración

Las extensiones siguientes admiten distintos tipos de escenarios y características para la administración de la implementación y la configuración. Consulte también la sección sobre operaciones y administración de máquinas virtuales que aparece más adelante.

|Nombre de la extensión de máquina virtual|Descripción de la característica|Más información|
|---|---|---|
|**MSEnterpriseApplication**|Implementa características compatibles con el centro del sistema de Windows.|[System Center 2012 R2 Virtual Machine Roles (Roles de máquina virtual de System Center 2012 R2)](http://social.technet.microsoft.com/wiki/contents/articles/18274.system-center-2012-r2-virtual-machine-role-authoring-guide-resource-extension-package.aspx)|
|**Octopus Deploy** (basada en la extensión DSC)|Admite la implementación automatizada de las aplicaciones web de ASP.NET y Servicios de Windows en entornos de desarrollo, prueba y producción.|[Getting Started with Octopus Deploy (Introducción a Octopus Deploy)](http://docs.octopusdeploy.com/display/OD/Getting%20started)|
|**Administrador de versiones de Visual Studio** (basada en la extensión DSC)|Admite la implementación continua con Visual Studio.|[Implementaciones automatizadas con administración de versiones](https://msdn.microsoft.com/Library/vs/alm/Release/overview)|
|**CentosChefClient**|||
|**ChefClient**|Crea un cliente Chef en Windows. (También puede utilizar la extensión DSC que aparece a continuación).|[Chef y Microsoft Azure](https://www.getchef.com/solutions/azure/)|
|**LinuxChefClient**|||
|**DockerExtension**|Instala el demonio de Docker para admitir los comandos remotos de Docker.|[Uso de la extensión de máquina virtual Docker](virtual-machines-docker-vm-extension.md)Para obtener información más extensa, consulte la [Docker VM Extension User Guide](https://github.com/Azure/azure-docker-extension/blob/master/README.md) (Guía del usuario de la extensión de máquina virtual Docker)|
|**DSC**|Extensión DSC (configuración de estado deseado) de PowerShell|[Azure PowerShell DSC (Desired State Configuration) extension (Extensión DSC de Azure PowerShell)](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx)|
|**PuppetEnterpriseAgent**|Implementa las características de Puppet Enterprise |[Puppet on Azure (Puppet en Azure)](http://puppetlabs.com/solutions/microsoft)|
|**CustomScriptExtension** (Windows)**CustomScriptForLinux** (Linux)|Invoca scripts personalizados en la máquina virtual en cualquier momento, ya sea en el inicio o durante su vigencia.|[Extensión Custom Script](virtual-machines-extensions-customscript.md) | [Linux](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript)|
|**AzureCATExtensionHandler**|Consume los datos de diagnóstico que recopila **IaaSDiagnostics** y algunas otras fuentes de datos, como las [métricas del análisis de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/hh343270.aspx) y los transforma en un conjunto de datos agregados adecuado para que los utilice el proceso de control del host de SAP.|[Azure Enhanced Monitoring for SAP (Supervisión mejorada de Azure para SAP)](https://azure.microsoft.com/blog/2014/06/04/azure-enhanced-monitoring-for-sap/)|

##Seguridad y protección

Las extensiones que aparecen en esta sección proporcionan características de seguridad críticas para las máquinas virtuales de Azure.

|Nombre de la extensión de máquina virtual|Descripción de la característica|Más información|
|---|---|---|
|**CloudLinkSecureVMWindowsAgent**|Brinda a los clientes de Microsoft Azure la funcionalidad para cifrar los datos de máquina virtual en una infraestructura compartida multiempresa y el control total de las claves de cifrado de los datos cifrados en la infraestructura de almacenamiento de Azure.|[Securing Microsoft Azure Virtual Machines leveraging BitLocker and Native OS encryption (Protección de las máquinas virtuales de Microsoft Azure con cifrado de BitLocker y SO nativo)](http://www.cloudlinktech.com/azure)|
|**McAfeeEndpointSecurity**|Protege la máquina virtual contra software malintencionado.|[McAfee](https://www.mcafeeasap.com/MarketingContent/default.aspx)|
|**TrendMicroDSA**|Habilita la compatibilidad para la plataforma Deep Security de TrendMicro para proporcionar detección y prevención de intrusiones, firewall, antimalware, reputación web, inspección de registros y supervisión de la integridad.|[Instalación y configuración de Trend Micro Deep Security como servicio en una máquina virtual de Azure](virtual-machines-install-trend.md)|
|**PortalProtectExtension**|Protege contra las amenazas al entorno de Microsoft SharePoint.|[Securing Your SharePoint Deployment on Azure (Protección de la implementación de SharePoint en Azure)](http://blog.trendmicro.com/securing-sharepoint-deployment-azure/)|
|**IaaSAntimalware**|Microsoft Antimalware para Servicios en la nube y Máquinas virtuales de Azure es una funcionalidad de protección en tiempo real que ayuda a identificar y eliminar virus, spyware y otro software malintencionado, con alertas que se pueden configurar para cuando software malintencionado o no deseado conocido intente se intente instalar o ejecutar en el sistema.|[Descargue información sobre antimalware](http://go.microsoft.com/fwlink/?linkid=398023&clcid=0x409)|
|**SymantecEndpointProtection**|Symantec Endpoint Protection 12.1.4 habilita la seguridad y el rendimiento entre sistemas físicos y virtuales.|[Instalación y configuración de Endpoint Protection en una máquina virtual de Azure](virtual-machines-install-symantec.md)

##Administración y operaciones de máquinas virtuales

Admite comportamiento y características de administración de operaciones comunes. Consulte también la sección sobre Administración de la implementación y la configuración que se encuentra más arriba.

|**Nombre de la extensión de máquina virtual**|Descripción de la característica|Más información|
|---|---|---|
|**AzureVmLogCollector**|Puede usar la extensión **AzureVMLogCollector** a petición para realizar una recopilación única de registros provenientes de una o más máquinas virtuales del Servicio en la nube (desde roles web y roles de trabajo) y transferir los archivos recopilados a una cuenta de almacenamiento de Azure, sin iniciar sesión de manera remota en ninguna de las máquinas virtuales. |[AzureLogCollector Extension](virtual-machines-extensions-log-collector.md)|
|**IaaSDiagnostics**|Habilita, deshabilita y configura Diagnósticos de Azure y también la usa **AzureCATExtensionHandler** para admitir la supervisión de SAP.|[Microsoft Azure Virtual Machine Monitoring with Azure Diagnostics Extension (Supervisión de máquinas virtuales de Microsoft Azure con la extensión de Diagnósticos de Azure)](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/)|
|**OSPatchingForLinux**|Permite que los administradores de máquinas virtuales de Azure automaticen las actualizaciones del SO de las máquinas virtuales con las configuraciones personalizadas. Puede utilizar la extensión OSPatching para configurar las actualizaciones de SO de las máquinas virtuales, las que incluyen especificar la frecuencia y el momento de instalar las revisiones del SO, especificar las revisiones que se instalarán y configurar el comportamiento de reinicio después de las actualizaciones.|[Entrada de blog sobre las extensiones de revisiones de SO](https://azure.microsoft.com/blog/2014/10/23/automate-linux-vm-os-updates-using-ospatching-extension/). Vea también el archivo Léame y el código fuente en GitHub en [Extensión de revisiones de SO](https://github.com/Azure/azure-linux-extensions).|

##Desarrollo y depuración

Estas extensiones de máquina virtual para que la información sea completa, dado que brindan la compatibilidad necesaria para las características relacionadas con Visual Studio y no están creadas para usarlas directamente.

|Nombre de la extensión de máquina virtual|Descripción de la característica|Más información|
|---|---|---|
|**VS14CTPDebugger**|Admite la depuración remota desde Visual Studio con el SDK 2.4 de Azure|[Depuración remota en Visual Studio](https://msdn.microsoft.com/library/y7f5zaaa.aspx)|
|**VS2013Debugger**|Admite la depuración remota desde Visual Studio con el SDK 2.4 de Azure||
|**VS2012Debugger**|Admite la depuración remota desde Visual Studio con el SDK 2.4 de Azure||
|**RemoteDebugVS14CTP**|Admite la depuración remota desde Visual Studio con el SDK 2.3 de Azure||
|**RemoteDebugVS2013**|Admite la depuración remota desde Visual Studio con el SDK 2.3 de Azure||
|**RemoteDebugVS2012**|Admite la depuración remota desde Visual Studio con el SDK 2.3 de Azure||
|**WebDeployForVSDevTest**|Instala y configura IIS y Web Deploy en Windows Server. No se admite su eliminación o deshabilitación.|

##Características varias

Estas extensiones brindan la compatibilidad para otras características de máquinas virtuales que puede resultar útiles.

|Nombre de la extensión de máquina virtual|Descripción de la característica|Más información|
|---|---|---|
|**BGInfo**|Presenta una imagen consolidada de la información útil del servidor en el escritorio cuando se utiliza el protocolo de Escritorio remoto.|[Extensión de BGInfo](https://msdn.microsoft.com/library/dn606289.aspx)|
|**HpcVmDrivers**|Instala, configura y mantiene los controladores de dispositivos de red para el acceso directo a memoria remota (RDMA) en una máquina virtual de tamaño A8 o A9 que ejecute Windows Server 2012 R2 o Windows Server 2012. Permite que las máquinas virtuales A8 o A9 agrupadas utilicen la red RDMA cuando ejecuten aplicaciones MPI en paralelo.|[Sobre las instancias de proceso intensivo A8, A9, A10 y A11](virtual-machines-a8-a9-a10-a11-specs.md)

<!---HONumber=AcomDC_0128_2016-->