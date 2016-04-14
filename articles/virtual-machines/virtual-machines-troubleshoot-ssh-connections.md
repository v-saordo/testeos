<properties
	pageTitle="Solución de problemas de conexiones SSH a una máquina virtual de Azure | Microsoft Azure"
	description="Solucionar problemas y corregir errores de SSH como, por ejemplo, errores en la conexión SSH o conexiones SSH rechazadas para una máquina virtual de Azure con Linux."
	keywords="conexión ssh rechazada, error de ssh azure ssh, error de conexión ssh"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/22/2016"
	ms.author="dkshir"/>

# Solución de problemas de conexiones de Secure Shell (SSH) en una máquina virtual de Azure basada en Linux

Pueden existir diversas causas para los errores SSH mientras se trata de conectar a una máquina virtual de Azure basada en Linux. Este artículo le ayudará a encontrar tales errores y corregirlos.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

En este artículo solo se aplica a máquinas virtuales de Azure con Linux. Para máquinas virtuales de Azure que ejecuten Windows, consulte [Solución de problemas con la conexión del Escritorio remoto a una máquina virtual de Azure ](virtual-machines-troubleshoot-remote-desktop-connections.md).

Si necesita más ayuda en cualquier momento con este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure o de desbordamiento de pila](http://azure.microsoft.com/support/forums/). Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya al sitio del [soporte técnico de Azure](http://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte**. Para obtener información sobre el uso del soporte técnico de Azure, lea las [Preguntas más frecuentes del soporte técnico de Microsoft Azure](http://azure.microsoft.com/support/faq/).


## Solución de errores comunes de SSH

En esta sección se indican los pasos de corrección rápida para los problemas de conexión comunes de SSH.

### Máquinas virtuales creadas con el modelo de implementación clásico

Siga estos pasos para resolver los errores de conexión de SSH más comunes:

1. _Restablezca el acceso remoto_ desde el [Portal de Azure](https://portal.azure.com).<br> Haga clic en **Examinar** > **Máquinas virtuales (clásico**) > su máquina virtual Linux > **Restablecer acceso remoto...**.

2. Reinicie la máquina virtual.<br> En el [Portal de Azure](https://portal.azure.com), haga clic en **Examinar** > **Máquinas virtuales (clásico)** > su máquina virtual Linux > **Reiniciar**.<br> En el [Portal de Azure clásico](https://manage.windowsazure.com), abra **Máquinas virtuales** > **Instancias** y haga clic en **Reiniciar**.

3. [Cambiar el tamaño de la máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx).

4. Siga las instrucciones de [Restablecimiento de una contraseña o de SSH para máquinas virtuales de Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) en la máquina virtual para:

	- Restablecer la contraseña o la clave de SSH.
	- Crear una nueva cuenta de usuario de _sudo_.
	- Restablecer la configuración de SSH.

5. Compruebe el estado de los recursos de la VM para ver si hay algún problema en la plataforma.<br> Haga clic en **Examinar** > **Máquinas virtuales (clásico)** > su máquina virtual Linux > **Configuración** > **Comprobar estado**.


### Máquinas virtuales creadas mediante el modelo de implementación del Administrador de recursos

Para resolver los problemas comunes de SSH para las máquinas virtuales creadas con el modelo de implementación del Administrador de recursos, pruebe los pasos siguientes.

1. _Restablezca la conexión SSH_ a la máquina virtual de Linux en la línea de comandos, ya sea mediante la CLI de Azure o con Azure PowerShell. Asegúrese de que está instalado el [agente Linux de Microsoft Azure](virtual-machines-linux-agent-user-guide.md) versión 2.0.5 o posterior.

**Uso de la CLI de Azure**:

a. Si todavía no la tiene, [instale la CLI de Azure y conéctese a su suscripción de Azure](../xplat-cli-install.md) mediante el comando `azure login`.

b. Asegúrese de que está en el modo de Administrador de recursos: Versiones más recientes de la CLI de Azure predeterminada en el modo de Administrador de recursos.

	```
	azure config mode arm
	```

c. Restablezca la conexión SSH con cualquiera de los métodos siguientes.

* Use el comando `vm reset-access` como en el siguiente ejemplo.

	```
	azure vm reset-access -g YourResourceGroupName -n YourVirtualMachineName -r
	```

Esto instalará la extensión `VMAccessForLinux` en la máquina virtual.

* Como alternativa, cree un archivo llamado PrivateConfig.json con el siguiente contenido:

	```
	{  
		"reset_ssh":"True"
	}
	```

Después, ejecute manualmente la extensión `VMAccessForLinux` para restablecer la conexión SSH.

	```
	azure vm extension set "YourResourceGroupName" "YourVirtualMachineName" VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
	```

**Uso de Azure PowerShell**:

a. Si todavía no lo tiene, [instale Azure PowerShell y conéctese a su suscripción de Azure](../powershell-install-configure.md) mediante el método de Azure AD. En las versiones de Azure PowerShell anteriores a 1.0. x, es necesario cambiar explícitamente al modo Administrador de recursos con _Switch-AzureMode_.

b. Ejecute la extensión `VMAccessForLinux` para restablecer la conexión SSH, tal y como se muestra en el siguiente ejemplo. En versiones anteriores, el comando es _Set-AzureVMExtension_.

	```
	Set-AzureRmVMExtension -ResourceGroupName "yourRG" -VMName "yourVM" -Location "West US" -Name "VMAccessForLinux" -Publisher "Microsoft.OSTCExtensions" -ExtensionType "VMAccessForLinux" -TypeHandlerVersion "1.2" -SettingString "{}" -ProtectedSettingString '{"reset_ssh":true}'
	```

2. Reinicie la VM de Linux desde el [Portal de Azure](https://portal.azure.com).<br> Haga clic en **Examinar** > **Máquinas virtuales** > su máquina virtual Linux > **Reiniciar**.

3. _Restablezca la contraseña o la clave SSH_ de la máquina virtual de Linux en la línea de comandos, mediante la CLI de Azure o con Azure PowerShell. También puede crear un nuevo nombre de usuario y una contraseña con autoridad _sudo_, tal y como se muestra en el siguiente ejemplo.

**Uso de la CLI de Azure**:

Instale y configure la CLI de Azure tal como se mencionó anteriormente. Cambie al modo de Administrador de recursos si es necesario y después ejecute la extensión mediante el uso de cualquiera de los métodos siguientes.

* Ejecute el comando `vm reset-access` para establecer cualquiera de las credenciales de SSH.

	```
	azure vm reset-access TestRgV2 TestVmV2 -u NewUser -p NewPassword
	```

Para obtener más información, escriba `azure vm reset-access -h` en la línea de comandos.

* Como alternativa, cree un archivo llamado PrivateConf.json con el siguiente contenido.

	```
	{
		"username":"NewUsername", "password":"NewPassword", "expiration":"2016-01-01", "ssh_key":"", "reset_ssh":false, "remove_user":""
	}
	```

Después, ejecute la extensión Linux mediante el uso del archivo anterior.

	```
	$azure vm extension set "testRG" "testVM" VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
	```

Tenga en cuenta que puede seguir pasos similares a los que aparecen en [Restablecer una contraseña o SSH para máquinas virtuales de Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) si desea probar otras posibilidades. Recuerde que debe modificar las instrucciones de CLI de Azure en el modo de Administrador de recursos.


**Uso de Azure PowerShell**:

Instale y configure Azure PowerShell tal como se mencionó anteriormente. Cambie al modo Administrador de recursos y después ejecute la extensión de la siguiente manera.

	```
	$RGName = 'testRG'
	$VmName = 'testVM'
	$Location = 'West US'

	$ExtensionName = 'VMAccessForLinux'
	$Publisher = 'Microsoft.OSTCExtensions'
	$Version = '1.2'

	$PublicConf = '{}'
	$PrivateConf = '{"username":"NewUsername", "password":"NewPassword", "ssh_key":"", "reset_ssh":false, "remove_user":""}'

	Set-AzureRmVMExtension -ResourceGroupName $RGName -VMName $VmName -Location $Location -Name $ExtensionName -Publisher $Publisher -ExtensionType $ExtensionName -TypeHandlerVersion $Version -SettingString $PublicConf -ProtectedSettingString $PrivateConf
	```

Asegúrese de reemplazar los valores de $RGName, $VmName, $Location y las credenciales de SSH por valores específicos de la instalación.



## Soluciones detalladas de problemas con SSH

Si el cliente SSH sigue sin poder ponerse en contacto con el servicio SSH en la máquina virtual, puede ser debido a muchos motivos. Presentamos a continuación los componentes implicados.

![Diagrama que muestra los componentes del servicio SSH](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot1.png)

Las siguientes secciones le ayudarán a aislar la causa del error y a averiguar soluciones.

### Pasos previos

En primer lugar, compruebe el estado de la máquina virtual en el portal.

En el [Portal de Azure clásico](https://manage.windowsazure.com), para las máquinas virtuales en el modelo de implementación clásica:

1. Haga clic en **Máquinas virtuales** > *Nombre de la máquina virtual*.
2. Haga clic en el **Panel** de la máquina virtual para comprobar su estado.
3. Haga clic en **Monitor** para ver la actividad reciente de los recursos de proceso, almacenamiento y de red.
4. Haga clic en **Extremos** para asegurarse de que hay un extremo para el tráfico de SSH.

En el [Portal de Azure](https://portal.azure.com):

1. En el caso de una máquina virtual creada en el modelo de implementación clásica, haga clic en **Examinar** > **Máquinas virtuales (clásico)** > *Nombre de máquina virtual*. En el caso de una máquina virtual creada con el Administrador de recursos, haga clic en **Examinar** > **Máquinas virtuales** > *Nombre de máquina virtual*. El panel de estado de la máquina virtual debe mostrar **En ejecución**. Desplácese hacia abajo para ver la actividad reciente de los recursos de proceso, almacenamiento y de red.
2. Haga clic en **Configuración** para examinar los extremos, direcciones IP y otros valores de configuración. Para identificar los puntos de conexión en las máquinas virtuales creadas con el Administrador de recursos, compruebe si está definido un [grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md), las reglas que se les aplican y si se les hace referencia en la subred.

Para comprobar la conectividad de red, compruebe los puntos de conexión configurados y vea si puede llegar a la máquina virtual a través de otro protocolo, como HTTP u otro servicio.

Después de realizar estos pasos, vuelva a intentar la conexión a SSH.


### Averiguar el origen del problema

El cliente de SSH de su equipo podría generar un error al alcanzar el servicio de SSH en la máquina virtual de Azure debido a estos posibles orígenes de problemas o errores de configuración:

- Equipo cliente de SSH
- Dispositivo perimetral de la organización
- Punto de conexión de servicio en la nube y lista de control de acceso (ACL)
- Grupos de seguridad de red
- Máquina virtual de Azure basada en Linux

#### Causa 1: Equipo cliente de SSH

Para descartar el equipo como causa del error, compruebe que puede establecer conexiones a SSH con otro equipo local basado en Linux.

![Diagrama que resalta el componente del equipo cliente de SSH](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot2.png)

Si se genera un error, compruebe si el equipo tiene lo siguiente:

- Una configuración del firewall local que está bloqueando el tráfico SSH entrante o saliente (TCP 22)
- Software de proxy de cliente instalado localmente que impide las conexiones a SSH
- Software de supervisión de red instalado localmente que impide las conexiones a SSH
- Otro tipo de software de seguridad que o bien, supervisar el tráfico o bien, permite o impide ciertos tipos de tráfico.

En todos estos casos, deshabilite temporalmente el software e intente una conexión a SSH con un equipo local para averiguar la causa. A continuación, trabaje con el administrador de red para corregir la configuración del software para permitir conexiones a SSH.

Si utiliza la autenticación de certificados, compruebe que dispone de estos permisos a la carpeta .ssh en el directorio principal:

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/*.pub
- Chmod 600 ~/.ssh/id\_rsa (o cualquier otro archivo en el que tenga almacenadas las claves privadas)
- Chmod 644 ~/.ssh/known\_hosts (contiene los hosts a los que se ha conectado a través de SSH)

#### Causa 2: Dispositivo perimetral de la organización

Para descartar el dispositivo perimetral de la organización como causa de los errores, compruebe que un equipo conectado directamente a Internet puede establecer conexiones a SSH con la máquina virtual de Azure. Si tiene acceso a la máquina virtual a través de una conexión de ExpressRoute o de una VPN de sitio a sitio, vaya a [Causa 4: grupos de seguridad de red](#nsg).

![Diagrama que resalta el dispositivo perimetral de la organización](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot3.png)

Si no tiene un equipo conectado directamente a Internet, puede crear fácilmente una nueva máquina virtual de Azure en su propio grupo de recursos o servicio en la nube y usarla. Para obtener más información, consulte [Creación de una máquina virtual que ejecuta Linux en Azure](virtual-machines-linux-tutorial.md). Cuando acabe de realizar las pruebas, elimine el grupo de recursos o la máquina virtual y el servicio en la nube.

Si puede crear una conexión a SSH con un equipo conectado directamente a Internet, compruebe si el dispositivo perimetral de la organización tiene lo siguiente:

- Un firewall interno que bloquea el tráfico SSH a Internet
- Un servidor proxy que impide las conexiones a SSH
- Software de detección de intrusiones o supervisión de red en ejecución en los dispositivos de la red perimetral que impide las conexiones a SSH

Trabaje con el administrador de red para corregir la configuración de los dispositivos perimetrales de la organización para permitir el tráfico SSH a Internet.

#### Causa 3: punto de conexión de servicio en la nube y ACL

> [AZURE.NOTE] Esta causa se aplica solo a las máquinas virtuales creadas con el modelo de implementación clásica. En el caso de las máquinas virtuales creadas con el Administrador de recursos, vaya a [Causa 4: Grupos de seguridad de red](#nsg).

Para eliminar el punto de conexión de servicio en la nube y ACL como el origen del error, en el caso de las máquinas virtuales creadas con el [modelo de implementación clásica](../resource-manager-deployment-model.md), compruebe si otra máquina virtual de Azure de la misma red virtual puede realizar conexiones de SSH para la máquina virtual.

![Diagrama que resalta el punto de conexión del servicio en la nube y ACL](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot4.png)

Si no tiene otra máquina virtual en la misma red virtual, puede crear una fácilmente. Para obtener más información, consulte [Creación de una máquina virtual que ejecuta Linux en Azure](virtual-machines-linux-tutorial.md). Cuando acabe de realizar las pruebas, elimine la máquina virtual que creó.

Si puede crear una conexión a SSH con una máquina virtual en la misma red virtual, compruebe:

- La configuración del punto de conexión para el tráfico de SSH en la máquina virtual de destino. El puerto TCP privado del punto de conexión debe coincidir con el puerto TCP en el que escucha el servicio SSH en la máquina virtual (el predeterminado es 22). En el caso de las máquinas virtuales creadas en el modelo de implementación del Administrador de recursos mediante plantillas, compruebe el número de puerto TCP de SSH en el Portal de Azure con **Examinar** > **Máquinas virtuales (v2)** > *Nombre de máquina virtual* > **Configuración** > **Puntos de conexión**.
- La ACL del punto de conexión para el tráfico de SSH en la máquina virtual de destino. Las ACL permiten especificar el tráfico entrante de Internet que se permite o se deniega en función de la dirección IP de origen. Las ACL mal configuradas pueden impedir el tráfico entrante de SSH al punto de conexión. Compruebe sus ACL para asegurarse de que está permitido el tráfico entrante desde las direcciones IP públicas del proxy o de otro servidor perimetral. Para obtener más información, consulte [Acerca de las listas de control de acceso (ACL) de red](../virtual-network/virtual-networks-acl.md).

Para descartar el punto de conexión como causa del problema, quite el actual, cree uno nuevo y especifique el nombre **SSH** (puerto TCP 22 para el número de puerto público y privado). Para obtener más información, vea [ Configuración de extremos en una máquina virtual en Azure](virtual-machines-set-up-endpoints.md).

<a id="nsg"></a>
#### Causa 4: Grupos de seguridad de red

Los grupos de seguridad de red permiten un control pormenorizado del tráfico entrante y saliente permitido. Puede crear reglas que abarquen subredes y servicios en la nube en una red virtual de Azure. Compruebe las reglas de los grupos de seguridad de red para asegurarse de que se permite el tráfico de SSH tanto a Internet como desde Internet. Para obtener más información, consulte [Acerca de los grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md).

#### Causa 5: Máquina virtual de Azure basada en Linux

La última causa de los posibles problemas puede residir en la propia máquina virtual de Azure.

![Diagrama que resalta la máquina virtual de Azure basada en Linux](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot5.png)

Si aún no lo hizo, siga las instrucciones [para restablecer una contraseña o SSH para máquinas virtuales de Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) en la máquina virtual.

Pruebe de nuevo la conexión desde su equipo. Si sigue sin funcionar, pueden darse algunos de estos problemas:

- El servicio de SSH no se está ejecutando en la máquina virtual de destino.
- El servicio SSH no está escuchando en el puerto TCP 22. Para probar esto, instale un cliente telnet en el equipo local y ejecute "telnet *cloudServiceName*.cloudapp.net 22". Esto averiguará si la máquina virtual permite la comunicación entrante y saliente al punto de conexión de SSH.
- El firewall local en la máquina virtual de destino tiene reglas que impiden el tráfico entrante o saliente de SSH.
- El software de detección de intrusiones o supervisión de red que se ejecuta en la máquina virtual de Azure impide las conexiones a SSH.


## Recursos adicionales

En el caso de las máquinas virtuales del modelo de implementación clásica, [Restablecimiento de una contraseña o de SSH para máquinas virtuales de Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md).

[Solucionar problemas de conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows ](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Solucionar problemas de acceso a una aplicación que se ejecuta en una máquina virtual de Azure](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=AcomDC_0128_2016-->