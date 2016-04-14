<properties
	pageTitle="Instalación de un controlador de dominio de réplica en Azure | Microsoft Azure"
	description="Tutorial que explica cómo instalar un controlador de dominio de un bosque de Active Directory local en una máquina virtual de Azure."
	services="virtual-network"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="virtual-network"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/01/2016"
	ms.author="curtand"/>


# Instalación de un controlador de dominio de Active Directory de réplica en una red virtual de Azure

En este tema se muestra cómo instalar controladores de dominio adicionales (también conocidos como controladores de dominio de réplica) para un dominio de Active Directory local en máquinas virtuales (VM) en una red virtual de Azure.

Es posible que también le interesen los siguientes temas relacionados:

-  Opcionalmente, puede instalar un nuevo bosque de Active Directory en una red virtual. Para realizar esos pasos, consulte [Instalación de un bosque de Active Directory nuevo en una red virtual de Azure](../active-directory/active-directory-new-forest-virtual-machine.md).
-  Para obtener una orientación en cuanto a conceptos sobre la instalación de los Servicios de dominio de Active Directory (AD DS) en una red virtual de Azure, consulte [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).


## Diagrama del escenario

En este escenario, los usuarios externos necesitan tener acceso a las aplicaciones que se ejecutan en servidores unidos a un dominio. Las máquinas virtuales que ejecutan los servidores de aplicaciones y los controladores de dominio de réplica se instalan en una red virtual de Azure. La red virtual puede estar conectada a la red local mediante una conexión [VPN sitio a sitio](../vpn-gateway/vpn-gateway-site-to-site-create.md), tal y como se muestra en el diagrama siguiente, o bien puede usar [ExpressRoute](../../services/expressroute/) para conseguir una conexión más rápida.

Los servidores de aplicaciones y los controladores de dominio se implementan en servicios en la nube independientes para distribuir el procesamiento informático y en [conjuntos de disponibilidad](../virtual-machines/virtual-machines-manage-availability.md) para una mejor tolerancia a errores. Los controladores de dominio se replican entre sí y con controladores de dominio locales mediante la replicación de Active Directory. No se necesitan herramientas de sincronización.

![][1]

## Crear un sitio de Active Directory para la red virtual de Azure

Es buena idea crear un sitio de Active Directory que represente la región de red correspondiente a la red virtual. Eso ayuda a optimizar la autenticación, la replicación y otras operaciones de ubicación del controlador de dominio. Los pasos siguientes explican cómo crear un sitio y, para obtener más información, consulte [Agregar un sitio nuevo](https://technet.microsoft.com/library/cc781496.aspx).

1. Abra sitios y servicios de Active Directory: **Administrador del servidor** > **Herramientas** > **Servicios y sitios de Active Directory**.
2. Cree un sitio que represente la región donde creó una red virtual de Azure: haga clic en **Sitios** > **Acción** > **Nuevo sitio** > escriba el nombre del nuevo sitio (por ejemplo, Azure, oeste de EE. UU.) > seleccione un vínculo de sitio > **Aceptar**.
3. Cree una subred y asóciela al nuevo sitio: haga doble clic en **Sitios** > haga clic con el botón derecho en **Subredes** > **Nueva subred** > escriba el intervalo de direcciones IP de la red virtual (por ejemplo, 10.1.0.0/16 en el diagrama del escenario) > seleccione el nuevo sitio de Azure > **Aceptar**.

## Creación de una red virtual de Azure

1. En el Portal de Azure clásico, haga clic en **Nuevo** > **Servicios de red** > **Red virtual** > **Creación personalizada** y use los siguientes valores para completar el asistente.

    En esta página del asistente… | Especifique estos valores
	------------- | -------------
	**Detalles de red virtual** | <p>Nombre: escriba un nombre para la red virtual, como WestUSVNet.</p><p>Región: elija la región más cercana.</p>
	**Conectividad DNS y VPN** | <p>Servidores DNS: especifique el nombre y la dirección IP de uno o más servidores DNS locales.</p><p>Conectividad: seleccione **Configurar una VPN sitio a sitio**.</p><p>Red local: especifique una nueva red local.</p><p>Si está utilizando ExpressRoute en lugar de una VPN, consulte [Configurar una conexión ExpressRoute a través de un proveedor de Exchange](../expressroute/expressroute-configuring-exps.md).</p>
	**Conectividad de sitio a sitio** | <p>Nombre: escriba un nombre para la red local.</p><p>Dirección IP del dispositivo VPN: especifique la dirección IP pública del dispositivo que se conectará a la red virtual. No se pudo ubicar el dispositivo VPN detrás de una dirección</p><p>.NAT: especifique los intervalos de direcciones de la red local (por ejemplo, 192.168.0.0/16 en el diagrama del escenario).</p>
	**Espacios de direcciones de la red virtual** | <p>Espacio de direcciones: especifique el intervalo de direcciones IP para las máquinas virtuales que desea ejecutar en la red virtual de Azure (por ejemplo, 10.1.0.0/16 en el diagrama del escenario). Este intervalo de direcciones no puede solaparse con los intervalos de direcciones de la red local.</p><p>Subredes: especifique un nombre y una dirección para una subred de los servidores de aplicaciones (como Front-end, 10.1.1.0/24) y para los controladores de dominio (por ejemplo, Back-end, 10.1.2.0/24).</p><p>Haga clic en **Agregar subred de puerta de enlace**.</p>

2. A continuación, configurará la puerta de enlace de red virtual para crear una conexión VPN de sitio a sitio segura. Consulte [Configurar una puerta de enlace de red virtual](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) para obtener instrucciones.
3. Cree la conexión VPN de sitio a sitio entre la nueva red virtual y un dispositivo VPN local. Consulte [Configurar una puerta de enlace de red virtual](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) para obtener instrucciones.



## Crear máquinas virtuales de Azure para los roles de controlador de dominio

Repita los pasos siguientes para crear máquinas virtuales para hospedar el rol de controlador de dominio según sea necesario. Debe implementar al menos dos controladores de dominio virtuales para proporcionar redundancia y tolerancia a errores. Si la red virtual de Azure incluye al menos dos controladores de dominio que están configurados de manera similar (es decir, son ambos catálogos globales, servidor DNS de ejecución y no contienen ningún rol FSMO, etc.), a continuación, coloque las máquinas virtuales que ejecutan los controladores de dominio en un conjunto de disponibilidad para aumentar la tolerancia a errores. Para crear las máquinas virtuales con Windows PowerShell en lugar de la interfaz de usuario, consulte [Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows](../virtual-machines/virtual-machines-ps-create-preconfigure-windows-vms.md).

1. En el portal de Azure clásico, haga clic en **Nuevo** > **Proceso** > **Máquina virtual** > **De la galería**. Utilice los valores siguientes para completar el asistente. Acepte el valor predeterminado para una configuración a menos que se sugiera o requiera otro valor.

    En esta página del asistente… | Especifique estos valores
	------------- | -------------
	**Elija una imagen** | Windows Server 2012 R2 Datacenter
	**Configuración de la máquina virtual** | <p>Nombre de la máquina virtual: escriba un nombre de etiqueta única (como AzureDC1).</p><p>Nuevo nombre de usuario: escriba el nombre de un usuario. Este usuario será un miembro del grupo local Administradores en la máquina virtual. Necesitará este nombre para iniciar sesión en la máquina virtual por primera vez. La cuenta incorporada con el nombre de Administrador no funcionará.</p><p>Nueva contraseña/confirmación: escriba una contraseña</p>
	**Configuración de la máquina virtual** | <p>Servicio en la nube: elija <b>Crear un nuevo servicio en la nube</b> para la primera máquina virtual y seleccione el mismo nombre de servicio en la nube cuando cree más máquinas virtuales que hospedarán el rol DC.</p><p>Nombre DNS de servicio en la nube: especifique un nombre único global</p><p>Región/Grupo de afinidiad/Red virtual: especifique el nombre de red virtual (como WestUSVNet).</p><p>Cuenta de almacenamiento: elija <b>Usar una cuenta de almacenamiento generada automáticamente</b> para la primera máquina virtual y, a continuación, seleccione el mismo nombre de cuenta de almacenamiento cuando cree más máquinas virtuales que hospeden el rol DC.</p><p>Conjunto de disponibilidad: elija <b>Crear conjunto de disponibilidad</b>.</p><p>Nombre de conjunto de disponibilidad: escriba un nombre para el conjunto de disponibilidad cuando cree la primera máquina virtual y, a continuación, seleccione el mismo nombre cuando cree más máquinas virtuales.</p>
	**Configuración de la máquina virtual** | <p>Seleccione <b>Instalar el agente de máquina virtual</b> y cualquier otra extensión que necesite.</p>
2. Conecte un disco a cada máquina virtual que ejecutará el rol de servidor de controlador de dominio. Se necesita el disco adicional para almacenar la base de datos, los registros y SYSVOL de AD. Especifique un tamaño para el disco (por ejemplo, 10 GB) y deje la **Preferencia de caché de host establecida** en **Ninguno**. Para ver los pasos, consulte [Acoplamiento de un disco de datos a una máquina virtual de Windows](../virtual-machines/storage-windows-attach-disk.md).
3. Una vez que inicie sesión por primera vez en la máquina virtual, abra **Administrador del servidor** > **Servicios de archivos y almacenamiento** para crear un volumen en este disco con NTFS.
4. Reserve una dirección IP estática para las máquinas virtuales que ejecutarán el rol de controlador de dominio. Para reservar una dirección IP estática, descargue el instalador de plataforma web de Microsoft, [instale Azure PowerShell](../powershell-install-configure.md) y ejecute el cmdlet Set-AzureStaticVNetIP. Por ejemplo:

    'Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM

Para obtener más información acerca de cómo establecer una dirección IP estática, consulte [Configuración de una dirección IP interna estática para una máquina virtual](../virtual-network/virtual-networks-reserved-private-ip.md).

## Instalar AD DS en máquinas virtuales de Azure

Inicie sesión en una máquina virtual y compruebe que tiene conectividad a través de la conexión de ExpressRoute o de VPN de sitio a sitio a los recursos de la red local. Después, instale AD DS en las máquinas virtuales de Azure. Puede usar el mismo proceso que se utiliza para instalar un controlador de dominio adicional en la red local (interfaz de usuario, Windows PowerShell o un archivo de respuesta). Cuando instale AD DS, asegúrese de que especifica el nuevo volumen para la ubicación de la base de datos, los registros y SYSVOL de AD. Si necesita hacer un repaso sobre la instalación de AD DS, consulte [Instalar servicios de dominio de Active Directory (nivel 100)](https://technet.microsoft.com/library/hh472162.aspx) o [Instalar un controlador de dominio de réplica de Windows Server 2012 en un dominio existente (nivel 200)](https://technet.microsoft.com/library/jj574134.aspx).

## Volver a configurar el servidor DNS para la red virtual

1. En el portal de Azure clásico, haga clic en el nombre de la red virtual y, a continuación, en la pestaña **Configurar** para [volver a configurar las direcciones IP del servidor DNS de la red virtual](virtual-networks-manage-dns-in-vnet.md) y así usar las direcciones IP estáticas que están asignadas a los controladores de dominio de réplica en lugar de las direcciones IP de los servidores DNS locales.

2. Para asegurarse de que todas las máquinas virtuales del controlador de dominio de réplica de la red virtual están configuradas para usar servidores DNS en la red virtual, haga clic en **Máquinas virtuales**, en la columna de estado para cada máquina virtual y, a continuación, en **Reiniciar**. Espere hasta que la máquina virtual muestre el estado **En ejecución** antes de intentar iniciar sesión en ella.

## Crear máquinas virtuales para servidores de aplicaciones

1. Repita los pasos siguientes para crear VM que se ejecuten como servidores de aplicaciones. Acepte el valor predeterminado para una configuración a menos que se sugiera o requiera otro valor.


	En esta página del asistente… | Especifique estos valores
	------------- | -------------
	**Elija una imagen** | Windows Server 2012 R2 Datacenter
	**Configuración de la máquina virtual** | <p>Nombre de la máquina virtual: escriba un nombre de etiqueta única (como AppServer1).</p><p>Nuevo nombre de usuario: escriba el nombre de un usuario. Este usuario será un miembro del grupo local Administradores en la máquina virtual. Necesitará este nombre para iniciar sesión en la máquina virtual por primera vez. La cuenta incorporada con el nombre de Administrador no funcionará.</p><p>Nueva contraseña/confirmación: escriba una contraseña</p>
	**Configuración de la máquina virtual** | <p>Servicio en la nube: elija **Crear un nuevo servicio en la nube** para la primera máquina virtual y seleccione el mismo nombre de servicio en la nube cuando cree más máquinas virtuales que hospedarán la aplicación.</p><p>Nombre DNS de servicio en la nube: especifique un nombre único global</p><p>Región/Grupo de afinidiad/Red virtual: especifique el nombre de red virtual (como WestUSVNet).</p><p>Cuenta de almacenamiento: elija **Usar una cuenta de almacenamiento generada automáticamente** para la primera máquina virtual y, a continuación, seleccione el mismo nombre de cuenta de almacenamiento cuando cree más máquinas virtuales que hospeden la aplicación.</p><p>Conjunto de disponibilidad: elija **Crear conjunto de disponibilidad**.</p><p>Nombre de conjunto de disponibilidad: escriba un nombre para el conjunto de disponibilidad cuando cree la primera máquina virtual y, a continuación, seleccione el mismo nombre cuando cree más máquinas virtuales.</p>
	**Configuración de la máquina virtual** | <p>Seleccione<b> Instalar el agente de máquina virtual</b> y cualquier otra extensión que necesite.</p>

2. Una vez aprovisionada cada máquina virtual, inicie sesión y únalas al dominio. En **Administrador de servidores**, haga clic en **Servidor local** > **GRUPO DE TRABAJO** > **Cambiar…** y, a continuación, seleccione **Dominio** y escriba el nombre del dominio local. Proporcione las credenciales de un usuario de dominio y, a continuación, reinicie la máquina virtual para completar la unión al dominio.

Para crear las máquinas virtuales con Windows PowerShell en lugar de la interfaz de usuario, consulte [Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows](../virtual-machines/virtual-machines-ps-create-preconfigure-windows-vms.md).

Para obtener más información acerca del uso de Windows PowerShell, consulte [Empezar a usar Cmdlets de Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx) y [Referencia de cmdlets de Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx).

## Recursos adicionales

-  [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Microsoft Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
-  [Cómo cargar los controladores de dominio de Hyper-V locales existentes a Azure con Azure PowerShell](http://support.microsoft.com/kb/2904015)
-  [Instalación de un bosque nuevo de Active Directory en una red virtual de Azure](../active-directory-new-forest-virtual-machine.md)
-  [Red virtual de Azure](../virtual-network/virtual-networks-overview.md)
-  [Microsoft Azure IaaS para profesionales de TI: (01) Principios básicos sobre máquinas virtuales](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IaaS para profesionales de TI: (05) Creación de redes virtuales y conectividad entre instalaciones](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Cmdlets de administración de Azure](https://msdn.microsoft.com/library/azure/jj152841)

<!--Image references-->
[1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png

<!---HONumber=AcomDC_0204_2016-->