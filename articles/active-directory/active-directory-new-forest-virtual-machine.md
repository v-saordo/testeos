<properties 
	pageTitle="Instalación de un bosque de Active Directory en una red virtual de Azure | Microsoft Azure" 
	description="Un tutorial que explica cómo crear un nuevo bosque de Active Directory en una máquina virtual (VM) en una red virtual de Azure." 
	services="active-directory, virtual-network"
    keywords="máquina virtual de Active Directory, instalación de un bosque de Active Directory, vídeos de Azure Active Directory"
	documentationCenter="" 
	authors="markusvi" 
	manager="stevenpo" 
	tags=""/>

<tags 
	ms.service="active-directory" 
	ms.devlang="na" 
	ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
	ms.date="01/25/2016" 
	ms.author="markusvi"/>


# Instalación de un bosque nuevo de Active Directory en una red virtual de Azure

En este tema se muestra la creación de un nuevo entorno de Windows Server Active Directory en una red virtual de Azure en una máquina virtual (VM) de una [red virtual de Azure](../virtual-network/virtual-networks-overview.md). En este caso, la red virtual de Azure no está conectada a una red de un entorno local.

Es posible que también le interesen los siguientes temas relacionados:

- Para ver un vídeo que muestre estos pasos, consulte [Instalación de un bosque nuevo de Active Directory en una red virtual de Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network).
- De manera opcional, puede [configurar una VPN de sitio a sitio](../vpn-gateway/vpn-gateway-site-to-site-create.md) y, a continuación, instalar un bosque nuevo o extender un bosque de entorno local hasta una red virtual de Azure. Para seguir esos pasos, consulte [Instalación de un controlador de dominio réplica de Active Directory en una red virtual de Azure](../virtual-networks-install-replica-active-directory-domain-controller.md).
-  Para obtener una orientación en cuanto a conceptos sobre la instalación de los Servicios de dominio de Active Directory (AD DS) en una red virtual de Azure, consulte [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## Diagrama del escenario

En este escenario, los usuarios externos necesitan tener acceso a las aplicaciones que se ejecutan en servidores unidos a un dominio. Las máquinas virtuales que ejecutan los servidores de aplicaciones y las máquinas virtuales que ejecutan los controladores de dominio se instalan en su propio servicio en la nube en una red virtual de Azure. También se incluyen dentro de un conjunto de disponibilidad para mayor tolerancia a errores.

![Bosque de Active Directory en una máquina virtual de una Red virtual de Azure][1] 7
## ¿En qué se diferencia del entorno local?

No existe mucha diferencia entre instalar un controlador de dominio en Azure y una instalación en el entorno local. Las principales diferencias se mencionan en la siguiente tabla.

Para configurar... | Local | Red virtual	
------------- | -------------  | ------------
**Dirección IP para el controlador de dominio** | Asigne la dirección IP estática en las propiedades del adaptador de red. | Ejecute el cmdlet Set-AzureStaticVNetIP para asignar una dirección IP estática.
**Resolución de clientes DNS** | Establezca la dirección de servidor DNS preferida y alternativa en las propiedades del adaptador de red de los miembros del dominio. | Establezca la dirección del servidor DNS en las propiedades de la red virtual.
**Almacenamiento de base de datos de Active Directory** | Opcionalmente, cambie la ubicación de almacenamiento predeterminada de C:\\. | Debe cambiar la ubicación de almacenamiento predeterminada de C:\\.



## Creación de una red virtual de Azure

1. Inicie sesión en el portal clásico de Azure.
2. Cree una red virtual. Haga clic en **Redes** > **Crear una red virtual**. Use los valores de la tabla siguiente para completar el asistente. 

	En esta página del asistente… | Especifique estos valores
	------------- | -------------
	**Detalles de red virtual** | <p>Nombre: escriba un nombre para la red virtual</p><p>Región: elija la región más próxima</p>
	**DNS y VPN** | <p>Dejar servidor DNS en blanco</p><p>No seleccionar una opción VPN</p>
	**Espacios de direcciones de la red virtual** | <p>Nombre de subred: escriba un nombre para la subred</p><p>IP de inicio: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p>



## Crear máquinas virtuales para ejecutar el controlador de dominio y los roles de servidor DNS
 
Repita los pasos siguientes para crear máquinas virtuales para hospedar el rol de controlador de dominio según sea necesario. Debe implementar al menos dos controladores de dominio virtuales para proporcionar redundancia y tolerancia a errores. Si la red virtual de Azure incluye al menos dos controladores de dominio que están configurados de manera similar (es decir, son ambos catálogos globales, servidor DNS de ejecución y no contienen ningún rol FSMO, etc.), a continuación, coloque las máquinas virtuales que ejecutan los controladores de dominio en un conjunto de disponibilidad para aumentar la tolerancia a errores.

Para crear las máquinas virtuales con Windows PowerShell en lugar de la interfaz de usuario, consulte [Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows](../virtual-machines-ps-create-preconfigure-windows-vms.md).

1. En el portal clásico, haga clic en **Nuevo** > **Calcular** > **Máquina virtual** > **De la galería**. Utilice los valores siguientes para completar el asistente. Acepte el valor predeterminado para una configuración a menos que se sugiera o requiera otro valor.

    En esta página del asistente… | Especifique estos valores
	------------- | -------------
	**Elija una imagen** | Windows Server 2012 R2 Datacenter
	**Configuración de la máquina virtual** | <p>Nombre de la máquina virtual: escriba un nombre de etiqueta única (como AzureDC1).</p><p>Nuevo nombre de usuario: escriba el nombre de un usuario. Este usuario será un miembro del grupo local Administradores en la máquina virtual. Necesitará este nombre para iniciar sesión en la máquina virtual por primera vez. La cuenta incorporada con el nombre de Administrador no funcionará.</p><p>Nueva contraseña/confirmación: escriba una contraseña</p>
	**Configuración de la máquina virtual** | <p>Servicio en la nube: elija <b>Crear un nuevo servicio en la nube</b> para la primera máquina virtual y seleccione el mismo nombre de servicio en la nube cuando cree más máquinas virtuales que hospedarán el rol DC.</p><p>Nombre DNS de servicio en la nube: especifique un nombre único global</p><p>Región/Grupo de afinidiad/Red virtual: especifique el nombre de red virtual (como WestUSVNet).</p><p>Cuenta de almacenamiento: elija <b>Usar una cuenta de almacenamiento generada automáticamente</b> para la primera máquina virtual y, a continuación, seleccione el mismo nombre de cuenta de almacenamiento cuando cree más máquinas virtuales que hospeden el rol DC.</p><p>Conjunto de disponibilidad: elija <b>Crear conjunto de disponibilidad</b>.</p><p>Nombre de conjunto de disponibilidad: escriba un nombre para el conjunto de disponibilidad cuando cree la primera máquina virtual y, a continuación, seleccione el mismo nombre cuando cree más máquinas virtuales.</p>
	**Configuración de la máquina virtual** | <p>Seleccione <b>Instalar el agente de máquina virtual</b> y cualquier otra extensión que necesite.</p>
2. Conecte un disco a cada máquina virtual que ejecutará el rol de servidor de controlador de dominio. Se necesita el disco adicional para almacenar la base de datos, los registros y SYSVOL de AD. Especifique un tamaño para el disco (por ejemplo, 10 GB) y deje la **Preferencia de caché de host establecida** en **Ninguno**. Para ver los pasos, consulte [Acoplamiento de un disco de datos a una máquina virtual de Windows](../storage-windows-attach-disk.md).
3. Una vez que inicie sesión por primera vez en la máquina virtual, abra **Administrador del servidor** > **Servicios de archivos y almacenamiento** para crear un volumen en este disco con NTFS.
4. Reserve una dirección IP estática para las máquinas virtuales que ejecutarán el rol de controlador de dominio. Para reservar una dirección IP estática, descargue el instalador de plataforma web de Microsoft, [instale Azure PowerShell](../powershell-install-configure.md) y ejecute el cmdlet Set-AzureStaticVNetIP. Por ejemplo:

    'Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM

Para obtener más información acerca de cómo establecer una dirección IP estática, consulte [Configuración de una dirección IP interna estática para una máquina virtual](../virtual-network/virtual-networks-reserved-private-ip.md).

## Instalación de Windows Server Active Directory

Utilice la misma rutina para [instalar AD DS](https://technet.microsoft.com/library/jj574166.aspx) que usa localmente (es decir, puede usar la interfaz de usuario, un archivo de respuesta o Windows PowerShell). Necesita proporcionar credenciales de Administrador para instalar un bosque nuevo. Para especificar la ubicación de la base de datos de Active Directory, los registros y SYSVOL, cambie la ubicación de almacenamiento predeterminada desde la unidad del sistema operativo hasta el disco de datos adicional que acopló a la VM.

Después de finalizada la instalación del controlador de dominio, vuelva a conectarse a la VM e inicie sesión en el controlador de dominio. Recuerde especificar las credenciales del dominio.

## Restablezca el servidor DNS para la red virtual de Azure

1. Restablezca la configuración del reenviador DNS en el nuevo servidor DC/DNS. 
  1. En el administrador del servidor, haga clic en **Herramientas** > **DNS**. 
  2. En **Administrador de DNS**, haga clic con el botón secundario en el nombre del servidor DNS y haga clic en **Propiedades**. 
  3. En la pestaña **Reenviadores**, haga clic en la dirección IP del reenviador y en **Editar**. Seleccione la dirección IP y haga clic en **Eliminar**. 
  4. Haga clic en **Aceptar** para cerrar el editor y en **Aceptar** para cerrar las propiedades del servidor DNS. 
2. Actualice la configuración del servidor DNS para la red virtual. 
  1. Haga clic en **Redes virtuales** > haga doble clic en la red virtual que creó > **Configurar** > **Servidores DNS**, escriba el nombre y la DIP de una de las máquinas virtuales que ejecuta el rol de servidor DC/DNS y haga clic en **Guardar**. 
  2. Seleccione la VM y haga clic en **Restablecer** para activar la VM y definir la configuración del solucionador de DNS con la dirección IP del servidor DNS nuevo. 


## Cree máquinas virtuales para miembros de dominio

1. Repita los pasos siguientes para crear VM que se ejecuten como servidores de aplicaciones. Acepte el valor predeterminado para una configuración a menos que se sugiera o requiera otro valor.

	En esta página del asistente… | Especifique estos valores
	------------- | -------------
	**Elija una imagen** | Windows Server 2012 R2 Datacenter
	**Configuración de la máquina virtual** | <p>Nombre de la máquina virtual: escriba un nombre de etiqueta única (como AppServer1).</p><p>Nuevo nombre de usuario: escriba el nombre de un usuario. Este usuario será un miembro del grupo local Administradores en la máquina virtual. Necesitará este nombre para iniciar sesión en la máquina virtual por primera vez. La cuenta incorporada con el nombre de Administrador no funcionará.</p><p>Nueva contraseña/confirmación: escriba una contraseña</p>
	**Configuración de la máquina virtual** | <p>Servicio en la nube: elija **Crear un nuevo servicio en la nube** para la primera máquina virtual y seleccione el mismo nombre de servicio en la nube cuando cree más máquinas virtuales que hospedarán la aplicación.</p><p>Nombre DNS de servicio en la nube: especifique un nombre único global</p><p>Región/Grupo de afinidiad/Red virtual: especifique el nombre de red virtual (como WestUSVNet).</p><p>Cuenta de almacenamiento: elija **Usar una cuenta de almacenamiento generada automáticamente** para la primera máquina virtual y, a continuación, seleccione el mismo nombre de cuenta de almacenamiento cuando cree más máquinas virtuales que hospeden la aplicación.</p><p>Conjunto de disponibilidad: elija **Crear conjunto de disponibilidad**.</p><p>Nombre de conjunto de disponibilidad: escriba un nombre para el conjunto de disponibilidad cuando cree la primera máquina virtual y, a continuación, seleccione el mismo nombre cuando cree más máquinas virtuales.</p>
	**Configuración de la máquina virtual** | <p>Seleccione<b> Instalar el agente de máquina virtual</b> y cualquier otra extensión que necesite.</p>
2. Una vez aprovisionada cada máquina virtual, inicie sesión y únalas al dominio. En **Administrador de servidores**, haga clic en **Servidor local** > **GRUPO DE TRABAJO** > **Cambiar…** y, a continuación, seleccione **Dominio** y escriba el nombre del dominio local. Proporcione las credenciales de un usuario de dominio y, a continuación, reinicie la máquina virtual para completar la unión al dominio.

Para crear las máquinas virtuales con Windows PowerShell en lugar de la interfaz de usuario, consulte [Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows](../virtual-machines-ps-create-preconfigure-windows-vms.md).

Para obtener más información acerca del uso de Windows PowerShell, consulte [Empezar a usar Cmdlets de Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx) y [Referencia de cmdlets de Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx).


## Otras referencias

-  [Instalación de un bosque nuevo de Active Directory en una red virtual de Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
-  [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Microsoft Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
-  [Configuración de una red virtual solo en la nube](../virtual-network/virtual-networks-create-vnet.md)
-  [Configuración de una VPN de sitio a sitio](../vpn-gateway/vpn-gateway-site-to-site-create.md)
-  [Instalación de un controlador de dominio de Active Directory de réplica en una red virtual de Azure](../virtual-networks-install-replica-active-directory-domain-controller.md)
-  [Microsoft Azure IaaS para profesionales de TI: (01) Principios básicos sobre máquinas virtuales](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IaaS para profesionales de TI: (05) Creación de redes virtuales y conectividad entre instalaciones](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [Información general sobre redes virtuales](../virtual-network/virtual-networks-overview.md)
-  [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md)
-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Referencia de cmdlets de Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx)
-  [Establecimiento de la dirección IP estática de Máquina virtual de Azure](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)
-  [Asignación de direcciones IP estáticas a una VM de Azure](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)
-  [Instalación de un nuevo bosque de Active Directory](https://technet.microsoft.com/library/jj574166.aspx)
-  [Introducción a la virtualización de los Servicios de dominio de Active Directory (AD DS) (nivel 100)](https://technet.microsoft.com/library/hh831734.aspx)

<!--Image references-->
[1]: ./media/active-directory-new-forest-virtual-machine/AD_Forest.png

 

<!---HONumber=AcomDC_0128_2016-->