<properties 
	pageTitle="Entorno de prueba de nube híbrida simulado | Microsoft Azure" 
	description="Cree un entorno de nube híbrida simulado para profesionales de TI o pruebas de desarrollo con dos redes virtuales de Azure y una conexión de red virtual a red virtual." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2016" 
	ms.author="josephd"/>

# Configuración de un entorno simulado de nube híbrida para hacer pruebas (modelo de implementación clásica)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](../virtual-machines/virtual-machines-setup-simulated-hybrid-cloud-environment-testing.md).

Este artículo le guiará en el proceso de creación de un entorno de nube híbrida simulado con Microsoft Azure para las pruebas con dos redes virtuales de Azure independientes. Use esta configuración como alternativa a la [configuración de un entorno de nube híbrida para hacer pruebas](virtual-networks-setup-hybrid-cloud-environment-testing.md) cuando no tenga conexión directa a Internet ni una dirección IP pública disponible. Aquí está la configuración resultante.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)

Esto simula un entorno de producción de nube híbrida. Consta de:

- Una red local simulada y simplificada hospedada en una red virtual de Azure (la red virtual TestLab).
- Una red virtual simulada entre locales hospedada en Azure (TestVNET).
- Una conexión de red virtual a red virtual entre las dos redes virtuales.
- Un controlador de dominio secundario en la red virtual TestVNET.

Esto proporciona una base y un punto de partida común desde el que puede:

- Desarrollar y probar las aplicaciones en un entorno de nube híbrida simulado.
- Cree configuraciones de prueba de los equipos, algunos dentro de la red virtual de TestLab y otros en la red virtual de TestVNET, para simular cargas de trabajo de TI basadas en la nube híbrida.

Existen cuatro fases principales para configurar este entorno de prueba de nube híbrida:

1.	Configuración de la red virtual de TestLab.
2.	Creación de la red virtual entre locales.
3.	Creación de la conexión VPN de red virtual a red virtual.
4.	Configuración de DC2. 

Si todavía no dispone de una suscripción a Azure, puede registrarse para obtener una evaluación gratuita en [Probar Azure](https://azure.microsoft.com/pricing/free-trial/). Si tiene una suscripción a MSDN, consulte [Beneficio de Azure para los suscriptores de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE] Las máquinas virtuales y las puertas de enlace de redes virtuales en Azure incurren en un coste económico constante cuando se están ejecutando. Este costo se factura en su prueba gratuita, la suscripción de MSDN o la suscripción de pago. Para reducir el coste de la ejecución de este entorno de prueba cuando no lo esté usando, consulte [Minimización de los costes de este entorno](#costs) en este artículo para obtener más información.


## Fase 1: configuración de la red virtual TestLab

Use las instrucciones que encontrará en el [Entorno de prueba de la configuración base](../virtual-machines/virtual-machines-base-configuration-test-environment.md) para configurar los equipos DC1, APP1 y CLIENT1 en una red virtual de Azure denominada TestLab.

Desde el Portal de administración de Azure en el equipo local, conéctese a DC1 con las credenciales de CORP\\User1. Para configurar el dominio CORP para que los usuarios y equipos usen su controlador de dominio local para la autenticación, ejecute estos comandos desde un símbolo del sistema de Windows PowerShell con nivel de administrador.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
	New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET"

Esta es su configuración actual.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_1.png)
 
## Fase 2: creación de la red virtual TestVNET

En primer lugar, cree una nueva red virtual denominada TestVNET.

1.	En la barra de tareas del Portal de administración de Azure, haga clic en **Nuevo > Servicios de red > Red virtual > Creación personalizada**.
2.	En la página Detalles de redes virtuales, escriba **TestVNET** en **Nombre**.
3.	En **Ubicación**, seleccione la ubicación adecuada.
4.	Haga clic en la flecha Siguiente.
5.	En la página Servidores DNS y conectividad VPN, en **Servidores DNS**, introduzca **DC1** en **Seleccione o introduzca un nombre** y, a continuación, haga clic en la flecha Siguiente.
6.	En la página Espacios de direcciones de redes virtuales:
	- En **Espacio de direcciones**, en **IP de inicio**, seleccione o escriba **192.168.0.0**.
	- En **Subredes**, haga clic en **Subnet-1** y reemplace el nombre por **TestSubnet**. 
	- En la columna **CIDR (recuento de direcciones)** de TestSubnet, haga clic en **/24 (256)**.
7.	Haga clic en el icono Completar. Espere hasta que se cree la red virtual antes de continuar.

A continuación, siga las instrucciones en [Cómo instalar y configurar Azure PowerShell para instalar Azure PowerShell en el equipo local](../install-configure-powershell.md).

A continuación, cree un nuevo servicio en la nube para la red virtual TestVNET. Debe elegir un nombre único. Por ejemplo, podría asignarle el nombre **TestVNET-***UniqueSequence*, en el que *UniqueSequence* es una abreviatura de su organización. Por ejemplo, si el nombre de su organización es Tailspin Toys, podría llamar al servicio en la nube **TestVNET-Tailspin**.

Puede comprobar que el nombre sea único con este comando de Azure PowerShell en el equipo local.

	Test-AzureName -Service <Proposed cloud service name>

Si este comando devuelve "False", el nombre propuesto es único. Cree el servicio en la nube con este comando.

	New-AzureService -Service <Unique cloud service name> -Location "<Same location name as your virtual network>"

Esta es su configuración actual.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_2.png)
 
##Fase 3: creación de la conexión red virtual a red virtual

En primer lugar, cree redes locales que representan el espacio de direcciones de cada red virtual.

1.	En el Portal de administración de Azure en el equipo local, haga clic en **Nuevo > Servicios de red > Red virtual > Agregar red local**.
2.	En la página Especificación de detalles de la red local, escriba **TestLabLNet** en **Nombre**, escriba **131.107.0.1** en **Dirección IP del dispositivo VPN** y, a continuación, haga clic en la flecha derecha.
3.	En la página Especificación del espacio de direcciones de la página, en **Dirección IP** inicial, escriba **10.0.0.0**.
4.	En **CIDR (recuento de direcciones)**, seleccione **/24 (256)** y haga clic en la marca de verificación.
5.	Haga clic en **Nuevo > Servicios de red > Red virtual > Agregar red local**.
6.	En la página Especificación de detalles de la red local, escriba **TestVNETLNet** en **Nombre**, escriba **131.107.0.2** en **Dirección IP del dispositivo VPN** y, a continuación, haga clic en la flecha derecha.
7.	En la página Especificación del espacio de direcciones de la página, en **Dirección IP** inicial, escriba **192.168.0.0**.
8.	En **CIDR (recuento de direcciones)**, seleccione **/24 (256)** y haga clic en la marca de verificación.

Tenga en cuenta que las direcciones IP del dispositivo VPN de 131.107.0.1 y 131.107.0.2 son valores de marcador de posición temporales hasta que configure las puertas de enlace de las dos redes virtuales.

A continuación, configure cada red virtual para usar una conexión VPN de sitio a sitio y la red local correspondiente a la otra red virtual.

1.	Desde el Portal de administración de Azure en el equipo local, haga clic en **Redes** en el panel izquierdo y, a continuación, compruebe que la columna **Estado** de **TestLab** está establecida como **Creada**.
2.	Haga clic en **TestLab** y, a continuación, haga clic en **Configurar**. En la página TestLab, en la sección **Conectividad sitio a sitio**, haga clic en **Conectar a la red local**. 
3.	En **Red local**, seleccione **TestVNETLNet**.
4.	Haga clic en **Guardar** en la barra de tareas. En algunos casos, podría tener que hacer clic en **Agregar subred de puerta de enlace** para crear una subred para que la use la puerta de enlace de VPN de Azure.
5.	Haga clic en **Redes** en el panel izquierdo y compruebe que la columna **Estado** de TestVNET está establecida en **Creada**.
6.	Haga clic en **TestVNET** y, después, haga clic en **Configurar**. En la página TestVNET, en la sección **Conectividad sitio a sitio**, haga clic en **Conectar a la red local**. 
7.	En **Red local**, seleccione **TestLabLNet**.
8.	Haga clic en **Guardar** en la barra de tareas. En algunos casos, podría tener que hacer clic en **Agregar subred de puerta de enlace** para crear una subred para que la use la puerta de enlace de VPN de Azure.

A continuación, cree puertas de enlace de red virtual para las dos redes virtuales.

1.	Desde el Portal de administración de Azure, en la página **Redes**, haga clic en **TestLab**. En la página Panel, verá un estado de **No se ha creado la puerta de enlace**.
2.	En la barra de tareas, haga clic en **Crear puerta de enlace** y, a continuación, en **Enrutamiento dinámico**. Haga clic en **Sí** cuando se le solicite. Espere hasta que la puerta de enlace se haya completado y su estado cambie a **Conectando**. Esto puede tardar unos minutos.
3.	En la página Panel, tenga en cuenta la **Dirección IP de la puerta de enlace**. Se trata de la dirección IP pública de la puerta de enlace VPN de la red virtual TestLab. Anote esta dirección IP, ya que la necesitará para configurar la conexión de red virtual a red virtual.
4.	En la barra de tareas, haga clic en **Administrar clave** y, a continuación, haga clic en el icono de copia al lado de la clave para copiarla al portapapeles. Pegue esta clave en un documento y guárdelo. Necesitará este valor de clave para configurar la conexión de red virtual a red virtual.
5.	En la página Redes, haga clic en **TestVNET**. En la página Panel, verá un estado de **No se ha creado la puerta de enlace**.
6.	En la barra de tareas, haga clic en **Crear puerta de enlace** y, a continuación, en **Enrutamiento dinámico**. Haga clic en **Sí** cuando se le solicite. Espere hasta que la puerta de enlace se haya completado y su estado cambie a **Conectando**. Esto puede tardar unos minutos.
7.	En la página Panel, tenga en cuenta la **Dirección IP de la puerta de enlace**. Se trata de la dirección IP pública de la puerta de enlace VPN de la red virtual TestVNET. Anote esta dirección IP, ya que la necesitará para configurar la conexión de red virtual a red virtual.

A continuación, configure las redes locales TestLabLNet y TestVNETLNet con las direcciones IP públicas obtenidas en la creación de las puertas de enlace de red virtuales.

1.	Desde el Portal de administración de Azure, en la página Redes, haga clic en **Redes locales**. 
2.	Haga clic en **TestLabLNet** y, a continuación, haga clic en **Editar** en la barra de tareas.
3.	En la página Especificación de los detalles de la red local, escriba la dirección IP de la puerta de enlace de red virtual de la red virtual de TestLab (del paso 3 del procedimiento anterior) en **Dirección IP del dispositivo VPN (opcional)** y, a continuación, haga clic en la flecha derecha.
4.	En la página Especificación del espacio de direcciones, haga clic en la marca de verificación.
5.	En la página Redes locales, haga clic en **TestVNETLNet** y, a continuación, haga clic en **Editar** en la barra de tareas.
6.	En la página Especificación de los detalles de la red local, escriba la dirección IP de la puerta de enlace de red virtual de la red virtual de TestVNET (del paso 7 del procedimiento anterior) en **Dirección IP del dispositivo VPN (opcional)** y, a continuación, haga clic en la flecha derecha.
7.	En la página Especificación del espacio de direcciones, haga clic en la marca de verificación.

A continuación, configure la clave previamente compartida para que ambas puertas de enlace usen el mismo valor, que es el valor de clave que se determina mediante el Portal de administración de Azure de la red virtual de TestLab. Ejecute estos comandos desde un símbolo del sistema de Azure PowerShell en el equipo local e introduzca el valor de la clave previamente compartida de TestLab.

	$preSharedKey="<The preshared key for the TestLab virtual network>"
	Set-AzureVNetGatewayKey -VNetName TestVNET -LocalNetworkSiteName TestLabLNet -SharedKey $preSharedKey

A continuación, en la página Redes del Portal de administración de Azure en el equipo local, haga clic en la red virtual **TestLab**, haga clic en **Panel** y, a continuación, haga clic en **Conectar** en la barra de tareas. Espere hasta que la red virtual TestLab muestre un estado conectado.

Esta es su configuración actual.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_3.png)
 
## Fase 4: configuración de DC2

En primer lugar, cree una máquina virtual de Azure de DC2. Ejecute estos comandos en el símbolo del sistema de Azure PowerShell en el equipo local.

	$ServiceName="<Your cloud service name from Phase 2>"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC2 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.0.4
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel ADFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

A continuación, inicie sesión en la nueva máquina virtual de DC2.

1.	En el panel izquierdo del Portal de administración de Azure, haga clic en **Máquinas virtuales** y, a continuación, haga clic en **En ejecución** en la columna **Estado** de DC2.
2.	En la barra de tareas, haga clic en **Conectar**. 
3.	Cuando se le pida que abra DC2.rdp, haga clic en **Abrir**.
4.	Cuando aparezca un cuadro de mensaje de conexión a Escritorio remoto, haga clic en **Conectar**.
5.	Cuando se le pidan credenciales, utilice estas:
- Nombre: **DC2\**[Nombre de la cuenta del administrador local]
- Contraseña: [Contraseña de la cuenta de administrador local]
6.	Cuando aparezca un cuadro de mensaje de conexión a Escritorio remoto referido a certificados, haga clic en **Sí**.

A continuación, configure una regla del Firewall de Windows para permitir el tráfico para probar la conectividad básica. Desde un símbolo del sistema de Windows PowerShell con nivel de administrador en DC2, ejecute estos comandos.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

El comando de ping debería devolver cuatro respuestas correctas desde la dirección IP 10.0.0.4. Esto es una prueba de tráfico a través de la conexión de red virtual a red virtual.

A continuación, agregue el disco de datos adicional como un nuevo volumen con la letra de unidad F:.

1.	En el panel izquierdo del Administrador de servidores, haga clic en **Servicios de archivos y almacenamiento** y, a continuación, haga clic en **Discos**.
2.	En el panel de contenido, en el grupo **Discos**, haga clic en **disco 2** (con la **partición** establecida en **Desconocida**).
3.	Haga clic en **Tareas** y, a continuación, haga clic en **Nuevo volumen**.
4.	En la página Antes de empezar del Asistente para volumen nuevo, haga clic en **Siguiente**.
5.	En la página Selección del servidor y del disco, haga clic en **Disco 2** y, a continuación, haga clic en **Siguiente**. Cuando se le solicite, haga clic en **Aceptar**.
6.	En la página Especificación del tamaño de la página de volumen, haga clic en **Siguiente**.
7.	En la página Asignación a una letra de unidad o carpeta, haga clic en **Siguiente**.
8.	En la página Selección de la configuración del sistema de archivos, haga clic en **Siguiente**.
9.	En la página Confirmación de las selecciones, haga clic en **Crear**.
10.	Cuando haya terminado, haga clic en **Cerrar**.

A continuación, configure DC2 como un controlador de dominio de réplica para el dominio corp.contoso.com. Ejecute estos comandos desde el símbolo del sistema de Windows PowerShell en DC2.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Tenga en cuenta que se le pedirá que proporcione la contraseña de CORP\\User1, una contraseña de Modo de restauración de servicios de directorio (DSRM) y que reinicie DC2.

Ahora que la red virtual TestVNET tiene su propio servidor DNS (DC2), debe configurar la red virtual de TestVNET para utilizar este servidor DNS.

1.	En el panel izquierdo del Portal de administración de Azure, haga clic en **Redes** y, a continuación, haga clic en **TestVNET**.
2.	Haga clic en **Configurar**.
3.	En **Servidores DNS**, quite la entrada 10.0.0.4.
4.	En **Servidores DNS**, agregue una entrada con **DC2** como el nombre y **192.168.0.4** como la dirección IP. 
5.	En la barra de comandos de la parte inferior, haga clic en **Guardar** y, a continuación, haga clic en **Sí** cuando se le solicite. Espere hasta que finalice la actualización de la red TestVNet.

Esta es su configuración actual.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)
 
Su entorno de nube híbrida simulado ya está listo para las pruebas.

## Pasos siguientes

- Configure una [granja de intranet de SharePoint](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md), una [aplicación de línea de negocio basada en web](virtual-networks-setup-lobapp-hybrid-cloud-testing.md) o un [servidor de sincronización de directorios (DirSync) de Office 365](virtual-networks-setup-dirsync-hybrid-cloud-testing.md) en la red virtual TestVNET.


## <a id="costs"></a>Minimizar los costos de este entorno

Para minimizar el coste de la ejecución de las máquinas virtuales en este entorno, realice las pruebas y las demostraciones necesarias tan pronto como sea posible y, a continuación, elimínelas o apague las máquinas virtuales cuando no las esté utilizando. Por ejemplo, podría utilizar la automatización de Azure y un Runbook para apagar automáticamente las máquinas virtuales en las redes virtuales TestLab y Test\_VNET al final de cada día laborable. Para obtener información, consulte [Introducción a la automatización de Azure](../automation-create-runbook-from-samples.md). Cuando vuelva a iniciar las máquinas virtuales en la subred de la red corporativa, inicie en primer lugar DC1.

Una puerta de enlace VPN de Azure se implementa como un conjunto de dos máquinas virtuales de Azure que incurren en un coste económico continuo. Para obtener más información, consulte [Precios: red virtual](https://azure.microsoft.com/pricing/details/virtual-network/). Para minimizar el coste de las dos puertas de enlace VPN (una para TestLab y otra para TestVNET), cree el entorno de prueba y realice las pruebas y demostraciones necesarias tan rápido como sea posible o elimine las puertas de enlace con estos pasos.
 
1.	En el Portal de administración de Azure en el equipo local, haga clic en **Redes** en el panel izquierdo, después en **TestLab** y, a continuación, haga clic en **Panel**.
2.	En la barra de tareas, haga clic en **Eliminar puerta de enlace**. Haga clic en **Sí** cuando se le solicite. Espere hasta que se elimine la puerta de enlace y su estado cambie a **No se ha creado la puerta de enlace**.
3.	Haga clic en **Redes** en el panel izquierdo, después en **TestVNET** y, a continuación, haga clic en **Panel**.
4.	En la barra de tareas, haga clic en **Eliminar puerta de enlace**. Haga clic en **Sí** cuando se le solicite. Espere hasta que se elimine la puerta de enlace y su estado cambie a **No se ha creado la puerta de enlace**.

Si elimina las puertas de enlace y desea restaurar este entorno de prueba, primero debe crear nuevas puertas de enlace.

1.	En el Portal de administración de Azure en el equipo local, haga clic en **Redes** en el panel izquierdo y, a continuación, haga clic en **TestLab**. En la página Panel, verá un estado de **No se ha creado la puerta de enlace**.
2.	En la barra de tareas, haga clic en **Crear puerta de enlace** y, a continuación, en **Enrutamiento dinámico**. Haga clic en **Sí** cuando se le solicite. Espere hasta que la puerta de enlace se haya completado y su estado cambie a **Conectando**. Esto puede tardar unos minutos.
3.	En la página Panel, tenga en cuenta la **Dirección IP de la puerta de enlace**. Se trata de la nueva dirección IP pública de la puerta de enlace VPN de la red virtual TestLab. Necesitará esta dirección IP para volver a configurar la red local TestLabLNet.
4.	En la barra de tareas, haga clic en **Administrar clave** y, a continuación, en el icono de copia al lado de la clave para copiarla al Portapapeles. Pegue el valor de esta clave en un documento y guárdelo. Necesitará este valor de clave para volver a configurar la puerta de enlace VPN de la red virtual TestVNET.
5.	En el Portal de administración de Azure en el equipo local, haga clic en **Redes** en el panel izquierdo y, a continuación, en **TestVNET**. En la página Panel, verá un estado de **No se ha creado la puerta de enlace**.
6.	En la barra de tareas, haga clic en **Crear puerta de enlace** y, a continuación, en **Enrutamiento dinámico**. Haga clic en **Sí** cuando se le solicite. Espere hasta que la puerta de enlace se haya completado y su estado cambie a Conectando. Esto puede tardar unos minutos.
7.	En la página Panel, tenga en cuenta la **Dirección IP de la puerta de enlace**. Se trata de la nueva dirección IP pública de la puerta de enlace VPN de la red virtual TestVNET. Necesitará esta dirección IP para volver a configurar la red local TestVNETLNet.

A continuación, configure las redes locales TestLabLNet y TestVNETLNet con las nuevas direcciones IP públicas obtenidas en la creación de las puertas de enlace de red virtuales.

1.	Desde el Portal de administración de Azure, en la página Redes, haga clic en **Redes locales**. 
2.	Haga clic en **TestLabLNet** y, a continuación, haga clic en **Editar** en la barra de tareas.
3.	En la página Especificación de los detalles de la red local, escriba la dirección IP de la puerta de enlace de red virtual de la red virtual de TestLab (del paso 3 del procedimiento anterior) en **Dirección IP del dispositivo VPN (opcional)** y, a continuación, haga clic en la flecha derecha.
4.	En la página Especificación del espacio de direcciones, haga clic en la marca de verificación.
5.	En la página Redes locales, haga clic en **TestVNETLNet** y, a continuación, haga clic en **Editar** en la barra de tareas.
6.	En la página Especificación de los detalles de la red local, escriba la dirección IP de la puerta de enlace de red virtual de la red virtual de TestVNET (del paso 7 del procedimiento anterior) en **Dirección IP del dispositivo VPN (opcional)** y, a continuación, haga clic en la flecha derecha.
7.	En la página Especificación del espacio de direcciones, haga clic en la marca de verificación.

A continuación, configure la clave previamente compartida para que ambas puertas de enlace usen el mismo valor, que es el valor de clave que se determina mediante el Portal de administración de Azure de la red virtual de TestLab. Ejecute estos comandos desde un símbolo del sistema de Azure PowerShell en el equipo local e introduzca el valor de la clave previamente compartida de TestLab.

	$preSharedKey="<The preshared key for the TestLab virtual network>"
	Set-AzureVNetGatewayKey -VNetName TestVNET -LocalNetworkSiteName TestLabLNet -SharedKey $preSharedKey

A continuación, en la página Red del Portal de administración de Azure, haga clic en la red virtual **TestLab** y, a continuación, haga clic en **Conectar** en la barra de tareas. Espere hasta que la red virtual TestLab muestre un estado conectado a la red local TestVNET.

<!---HONumber=AcomDC_0211_2016-->