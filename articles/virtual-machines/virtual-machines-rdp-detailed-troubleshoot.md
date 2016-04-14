<properties
	pageTitle="Solución de problemas detallada de Escritorio remoto | Microsoft Azure"
	description="Pasos de solución de problemas detallada para las conexiones de RDP a una máquina virtual de Azure con Windows."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016"
	ms.author="dkshir"/>

# Solución de problemas detallada de conexiones de Escritorio remoto a máquinas virtuales de Azure basadas en Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Este artículo ofrece pasos detallados de solución de problemas para diagnosticar y corregir errores complejos de Escritorio remoto en máquinas virtuales de Azure basadas en Windows.

> [AZURE.IMPORTANT] Para eliminar los errores más comunes de Escritorio remoto, asegúrese de leer [el artículo acerca de la solución de problemas básicos de Escritorio remoto](virtual-machines-troubleshoot-remote-desktop-connections.md) antes de continuar.

Si obtiene un mensaje de error de Escritorio remoto que no se parece a ninguno de los mensajes de error específicos que se tratan en el artículo [Solución de problemas de conexiones del Escritorio remoto a una máquina virtual de Azure con Windows](virtual-machines-troubleshoot-remote-desktop-connections.md), puede seguir estos pasos e intentar averiguar por qué el cliente de Escritorio remoto (o [RDP](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol)) no se puede conectar al servicio RDP en la máquina virtual de Azure.

Si necesita más ayuda en cualquier momento con este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure y de desbordamiento de pila](https://azure.microsoft.com/support/forums/). Como alternativa, también puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte técnico**. Para obtener información sobre el uso del soporte técnico de Azure, lea las [Preguntas más frecuentes del soporte técnico de Microsoft Azure](https://azure.microsoft.com/support/faq/).


## Componentes de una conexión de Escritorio remoto

Estos son los componentes implicados en una conexión RDP:

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_0.png)

Antes de continuar, podría resultar útil revisar mentalmente qué ha cambiado desde la última conexión correcta de Escritorio remoto a la máquina virtual. Por ejemplo:

- Si la dirección IP pública de la máquina virtual o del servicio en la nube que contiene la máquina virtual (también denominada dirección IP virtual o [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) ha cambiado, el error de RDP podría deberse a que la memoria caché del cliente DNS sigue teniendo la *dirección IP antigua* registrada para el nombre DNS. Vacíe la memoria caché del cliente DNS e intente conectar la máquina virtual de nuevo. O bien, intente conectarse directamente con la VIP nueva.
- Si está utilizando una aplicación de terceros para administrar las conexiones a Escritorio remoto en lugar de utilizar alguno de los portales de Azure, asegúrese de que la configuración de la aplicación incluye el puerto TCP correcto para el tráfico de Escritorio remoto. Puede comprobar este puerto para una máquina virtual clásica en el [Portal de Azure](https://portal.azure.com), haciendo clic en Configuración de la máquina virtual > Puntos de conexión.


## Pasos previos

Antes de continuar con la solución de problemas detallada,

- compruebe el estado de la máquina virtual en el Portal de Azure clásico o en el Portal de Azure para detectar cualquier error obvio
- Siga los [pasos de corrección rápida para los errores comunes de RDP en la Guía de solución de problemas básicos](virtual-machines-troubleshoot-remote-desktop-connections.md#quickfixrdp)


Vuelva a conectarse a la máquina virtual a través de Escritorio remoto después de realizar estos pasos.


## Solución de problemas detallada

Puede que el cliente de Escritorio remoto no pueda ponerse en contacto con el servicio de Escritorio remoto en la máquina virtual de Azure debido a problemas en los orígenes siguientes:

- Equipo cliente de Escritorio remoto
- Dispositivo perimetral de intranet de la organización
- Extremo de servicio en la nube y lista de control de acceso (ACL)
- Grupos de seguridad de red
- Máquina virtual de Azure basada en Windows

### Causa 1: equipo cliente de Escritorio remoto

Compruebe que el equipo puede establecer conexiones de Escritorio remoto a otro equipo local, basado en Windows.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_1.png)

Si no es posible, compruebe si el equipo tiene lo siguiente:

- Una configuración del firewall local que bloquea el tráfico de Escritorio remoto
- Software de proxy de cliente instalado localmente que impide las conexiones a Escritorio remoto
- Software de supervisión de red instalado localmente que impide las conexiones a Escritorio remoto
- Otro tipo de software de seguridad, por ejemplo para supervisar el tráfico o para permitir o impedir ciertos tipos de tráfico, que imposibilita las conexiones a Escritorio remoto

En todos estos casos, deshabilite temporalmente el software e intente conectarse a un equipo local a través de Escritorio remoto. Si puede averiguar la causa real mediante este método, trabaje con el administrador de red para corregir la configuración del software para permitir conexiones a Escritorio remoto.

### Causa 2: dispositivo perimetral de intranet de la organización

Compruebe que un equipo conectado directamente a Internet puede establecer conexiones de Escritorio remoto a la máquina virtual de Azure.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_2.png)

Si no tiene un equipo conectado directamente a Internet, cree y pruebe con una nueva máquina virtual de Azure en un grupo de recursos o servicio en la nube. Para obtener más información, vea [Crear una máquina virtual que ejecuta Windows en Azure](virtual-machines-windows-tutorial.md). Puede eliminar la máquina virtual y el grupo de recursos o el servicio en la nube después de la prueba.

Si puede crear una conexión a Escritorio remoto con un equipo conectado directamente a Internet, compruebe si el dispositivo perimetral de intranet de la organización tiene lo siguiente:

- Un firewall interno que bloquea las conexiones HTTPS a Internet
- Un servidor proxy que impide las conexiones a Escritorio remoto
- Software de detección de intrusiones o supervisión de red en ejecución en los dispositivos de la red perimetral que impide las conexiones a Escritorio remoto

Trabaje con el administrador de red para corregir la configuración del dispositivo perimetral de intranet de la organización para permitir conexiones a Escritorio remoto a Internet basadas en HTTPS.

### Causa 3: extremo de servicio en la nube y ACL

Para las máquinas virtuales creadas mediante el modelo clásico de implementación, compruebe que otra máquina virtual de Azure que se encuentre en el mismo servicio en la nube o la misma red virtual pueda establecer conexiones de Escritorio remoto a su máquina virtual de Azure.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_3.png)

> [AZURE.NOTE] Para las máquinas virtuales creadas en el Administrador de recursos, vaya a [Causa 4: grupos de seguridad de red](#nsgs).

Si no tiene otra máquina virtual en el mismo servicio en la nube o la misma red virtual, puede crear una nueva siguiendo los pasos descritos en [Creación de una máquina virtual que ejecuta Windows en el Portal de Azure](virtual-machines-windows-tutorial.md). Una vez completada la prueba, elimine la máquina virtual adicional.

Si puede conectarse a una máquina virtual en el mismo servicio en la nube o la misma red virtual mediante Escritorio remoto, compruebe lo siguiente:

- La configuración del punto de conexión para el tráfico de Escritorio remoto en la máquina virtual de destino: el puerto TCP privado del punto de conexión debe coincidir con el puerto TCP en el que está escuchando el servicio de Escritorio remoto de la máquina virtual (el valor predeterminado es 3389).
- La ACL del punto de conexión del tráfico de Escritorio remoto en la máquina virtual de destino: las ACL permiten especificar el tráfico entrante de Internet que se permite o se deniega en función de la dirección IP de origen. Las ACL mal configuradas pueden impedir el tráfico entrante de Escritorio remoto al extremo. Compruebe las ACL para asegurarse de que está permitido el tráfico entrante desde las direcciones IP públicas del proxy o de otro servidor perimetral. Para obtener más información, consulte [¿Qué es una lista de control de acceso (ACL) de red?](../virtual-network/virtual-networks-acl.md).

Para comprobar si el punto de conexión es la causa del problema, quite el punto de conexión actual y cree uno nuevo. Para ello, elija un puerto aleatorio en el intervalo que va entre 49152 y 65535 para el número de puerto externo. Para obtener más información, vea [Configuración de extremos en una máquina virtual](virtual-machines-set-up-endpoints.md).

### <a id="nsgs"></a>Causa 4: Grupos de seguridad de red

Los grupos de seguridad de red permiten un control pormenorizado del tráfico entrante y saliente permitido. Puede crear reglas que abarquen subredes y servicios en la nube en una red virtual de Azure. Compruebe las reglas del grupo de seguridad de red para asegurarse de que se permite el tráfico de Escritorio remoto desde Internet.

Para obtener más información, vea [¿Qué es un grupo de seguridad de red?](../virtual-network/virtual-networks-nsg.md)

### Causa 5: máquina virtual de Azure basada en Windows

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_5.png)

Utilice el [paquete de diagnóstico de Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) para ver si el error es debido a la propia máquina virtual de Azure. Si este paquete de diagnóstico no pudo resolver el problema **Conectividad de RDP a una máquina virtual de Azure (se requiere reinicio)**, siga las instrucciones de [este artículo](virtual-machines-windows-reset-password.md) para restablecer el servicio de Escritorio remoto en la máquina virtual. De este modo:

- Se habilitará la regla predeterminada «Escritorio remoto» del firewall de Windows (puerto TCP 3389).
- Se habilitarán las conexiones a Escritorio remoto estableciendo en 0 el valor HKLM\\System\\CurrentControlSet\\Control\\Terminal Server\\fDenyTSConnections del Registro.

Pruebe de nuevo la conexión desde su equipo. Si todavía no puede conectarse mediante Escritorio remoto, compruebe los siguientes problemas posibles:

- El servicio de Escritorio remoto no se está ejecutando en la máquina virtual de destino.
- El servicio de Escritorio remoto no está escuchando en el puerto TCP 3389.
- El firewall de Windows u otro firewall local tiene una regla de salida que impide el tráfico de Escritorio remoto.
- El software de detección de intrusiones o supervisión de red que se ejecuta en la máquina virtual de Azure impide las conexiones a Escritorio remoto.

Para las máquinas virtuales creadas con el modelo de implementación clásico, puede utilizar una sesión remota de Azure PowerShell para la máquina virtual de Azure. En primer lugar, deberá instalar un certificado para el servicio en la nube de hospedaje de la máquina virtual. Vaya a [Configurar el acceso remoto seguro de PowerShell a máquinas virtuales de Azure](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) y descargue el archivo de script **InstallWinRMCertAzureVM.ps1** en el equipo local.

A continuación, instale Azure PowerShell si todavía no lo ha hecho. Consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

Después, abra un símbolo del sistema de Azure PowerShell y cambie la carpeta actual a la ubicación del archivo de script **InstallWinRMCertAzureVM.ps1**. Para ejecutar un script de Azure PowerShell, debe establecer la directiva de ejecución correcta. Ejecute el comando **Get-ExecutionPolicy** para determinar el nivel de directiva actual. Para obtener información sobre cómo establecer el nivel adecuado, vea [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

A continuación, rellene su nombre de suscripción a Azure, el nombre del servicio en la nube y el nombre de la máquina virtual (quitando los caracteres < and >) y ejecute estos comandos.

	$subscr="<Name of your Azure subscription>"
	$serviceName="<Name of the cloud service that contains the target virtual machine>"
	$vmName="<Name of the target virtual machine>"
	.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

Puede obtener el nombre de suscripción correcto en la propiedad _SubscriptionName_ de la pantalla del comando **Get-AzureSubscription**. Puede obtener el nombre del servicio en la nube de la máquina virtual en la columna _ServiceName_ de la pantalla del comando **Get-AzureVM**.

Compruebe que tiene el nuevo certificado, abra un complemento Certificados para el usuario actual y vaya a la carpeta **Entidades de certificación raíz de confianza\\Certificados**. Debería ver un certificado con el nombre DNS del servicio en la nube en la columna Emitido para (ejemplo: cloudservice4testing.cloudapp.net).

A continuación, inicie una sesión remota de Azure PowerShell mediante el uso de estos comandos.

	$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
	$creds = Get-Credential
	Enter-PSSession -ConnectionUri $uri -Credential $creds

Después de escribir las credenciales de administrador válidas, debería ver algo parecido a lo siguiente como símbolo del sistema de PowerShell de Azure:

	[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

La primera parte de este indicador es el nombre del servicio en la nube que contiene la máquina virtual de destino, que podría ser diferente de "cloudservice4testing.cloudapp.net". Ahora puede emitir comandos de Azure PowerShell para este servicio en la nube, para investigar los problemas adicionales que se mencionaron anteriormente y corregir la configuración.

### Para corregir manualmente el puerto TCP de escucha de Servicios de Escritorio remoto

Si no pudo ejecutar el [paquete de diagnóstico de Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) para solucionar el problema **Conectividad de RDP a una máquina virtual de Azure (se requiere reinicio)**, en el símbolo del sistema de la sesión remota de Azure PowerShell, ejecute este comando.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

La propiedad PortNumber muestra el número de puerto actual. Si es necesario, vuelva a poner el número de puerto del Escritorio remoto en su valor predeterminado (3389) con este comando.

	Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

Compruebe que el puerto cambió a 3389 con este comando.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Salga de la sesión remota de Azure PowerShell con este comando.

	Exit-PSSession

Compruebe que el punto de conexión de Escritorio remoto para la máquina virtual de Azure también usa el puerto TCP 3398 como puerto interno. Reinicie la máquina virtual de Azure y vuelva a intentar la conexión a Escritorio remoto.


## Recursos adicionales

[Paquete de diagnóstico de Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Cómo restablecer una contraseña o el servicio de Escritorio remoto para máquinas virtuales de Windows](virtual-machines-windows-reset-password.md)

[Instalación y configuración de Azure PowerShell](../install-configure-powershell.md)

[Solución de problemas de conexiones de Secure Shell (SSH) en una máquina virtual de Azure basada en Linux](virtual-machines-troubleshoot-ssh-connections.md)

[Solucionar problemas de acceso a una aplicación que se ejecuta en una máquina virtual de Azure](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=AcomDC_0204_2016-->