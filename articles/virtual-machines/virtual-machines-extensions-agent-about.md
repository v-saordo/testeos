<properties
 pageTitle="Agente de máquina virtual y extensiones de Azure | Microsoft Azure"
 description="Proporciona información general de los agentes y las extensiones y sobre cómo instalar el agente mediante el modelo de implementación clásico."
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
 ms.date="01/04/2016"
 ms.author="rasquill"/>

#Acerca de las extensiones y del agente de máquina virtual

El Agente de máquina virtual (Agente de VM) de Azure se utiliza para instalar, configurar, administrar y ejecutar Extensiones de máquina virtual (Extensiones de VM) de Azure. Las Extensiones de VM proporcionan características dinámicas que proporcionan Microsoft y otros terceros. El agente y las extensiones se agregan principalmente a través del Portal de administración, pero también puede usar los cmdlets de [Powershell](../powershell-install-configure.md) o la [CLI de Azure](../xplat-cli-install.md) para agregar y configurar si desea crear una máquina virtual o con máquinas virtuales existentes.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.



Las extensiones de VM pueden ayudarle a:

-   Modificar las características de seguridad e identidad, como el restablecimiento de valores de cuenta y el uso de antimalware
-   Iniciar, detener o configurar la supervisión y el diagnóstico
-   Restablecer o instalar características de conectividad, como RDP y SSH
-   Diagnosticar, supervisar y administrar las máquinas virtuales

Además, existen muchas otras características; con frecuencia, se lanzan nuevas características de Extensión de VM. En este artículo, se describen los Agentes de VM de Azure para Windows y Linux y cómo admiten la funcionalidad de Extensión de VM. Si desea obtener un listado de las Extensiones de VM por categoría de característica, consulte [Características y extensiones de VM de Azure](virtual-machines-extensions-features.md).

##Agentes de VM de Azure para Windows y Linux

El Agente de máquinas virtuales de Azure (Agente de VM) es un proceso ligero y protegido que instala, configura y eliminar extensiones de VM en instancias de Máquinas virtuales de Azure desde la Galería de imágenes y en instancias de VM personalizadas, si tienen instalado el Agente de VM. El Agente de VM actúa como el servicio de control local seguro para la VM de Azure. Las extensiones que carga el agente proporcionan características específicas para aumentar la productividad mediante el uso de la instancia.

Existen dos Agentes de VM de Azure, uno para máquinas virtuales de Windows y uno para máquinas virtuales de Linux. De manera predeterminada, el Agente de VM se instala automáticamente cuando crea una máquina virtual desde la Galería de imágenes, pero también puede instalar el agente de VM una vez creada la instancia o puede instalarlo en una imagen de VM personalizada que luego carga usted mismo.

>[AZURE.IMPORTANT]Estos agentes de VM son servicios muy ligeros que permiten la administración protegida de las instancias de máquina virtual. Es posible que existan casos en los que no desee tener el Agente de VM. Si es así, asegúrese de crear máquinas virtuales que no tengan instalado el Agente de VM. A pesar de que el Agente de VM se puede quitar físicamente, el comportamiento de cualquier extensión de VM en la instancia es indefinido. Como resultado, en este momento no es compatible la eliminación del agente de VM una vez instalado.

El Agente de VM se habilita en las siguientes situaciones:

-   Cuando crea una instancia de una máquina virtual a través del método **Creación rápida** en el Portal de administración, o bien a través del método **Creación personalizada** de dicho portal y asegurándose de que la casilla **Instalar el agente de VM** esté activada (tal como aparece en la imagen siguiente). Para obtener más información, consulte [Creación de una máquina virtual personalizada](virtual-machines-create-custom.md).

    ![Casilla del Agente de VM](media/virtual-machines-extensions-agent-about/IC719409.png)

-   Cuando crea una instancia de una máquina virtual con el cmdlet [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) o el cmdlet [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx). Puede crear una máquina virtual sin tener instalado el Agente de VM; para ello, agregue el parámetro **–DisableGuestAgent** al cmdlet [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).

-   A través de la descarga e instalación manual del Agente de VM (ya sea en su versión de Windows o de Linux) en una instancia de máquina virtual existente y luego definiendo el valor **ProvisionGuestAgent** en **true**, con PowerShell o una llamada de REST. (Si no establece este valor después de instalar manualmente el Agente de VM, la incorporación del Agente de VM no se detectará de manera adecuada). El ejemplo de código siguiente muestra cómo hacerlo con PowerShell, donde los argumentos `$svc` y `$name` ya están determinados.

        $vm = Get-AzureVM –serviceName $svc –Name $name
        $vm.VM.ProvisionGuestAgent = $TRUE
        Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc

-   Mediante la creación de una imagen de máquina virtual que tiene instalado el Agente de VM antes de cargarla a Azure. En el caso de una VM de Windows, descargue el [archivo .msi del agente de máquina virtual de Windows](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) e instale dicho agente. En el caso de una VM de Linux, lo instalará desde el repositorio de github que se encuentra en <https://github.com/Azure/WALinuxAgent>. Para obtener más información sobre cómo instalar el agente de máquina virtual en Linux, consulte la [Guía de usuario del Agente de Linux de Azure](virtual-machines-linux-agent-user-guide.md).

>[AZURE.NOTE]En PaaS, el agente de máquina virtual se denomina **GuestAgent** y siempre está disponible en VM de rol web y rol de trabajo. (Para obtener más información, consulte [Arquitectura de roles de Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx).) El agente de VM para máquinas virtuales de rol ahora puede agregar extensiones a las máquinas virtuales de servicio en la nube, de la misma forma en que lo hace para las máquinas virtuales persistentes. La diferencia principal entre Extensiones de VM en VM de rol y VM persistentes es que, en el caso de las VM de rol, las extensiones se agregan primero al servicio en la nube y luego a las implementaciones dentro de ese servicio en la nube.

>Utilice el cmdlet [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) para mostrar todas las extensiones de VM de rol disponibles.

##Buscar, agregar, actualizar y quitar extensiones de VM  

Para obtener detalles sobre estas tareas, consulte [Agregar, buscar, actualizar y quitar extensiones de VM de Azure](virtual-machines-extensions-install.md).

<!---HONumber=AcomDC_0107_2016-->