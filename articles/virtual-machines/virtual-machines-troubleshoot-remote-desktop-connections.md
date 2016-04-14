<properties
	pageTitle="Solución de problemas con la conexión del Escritorio remoto a una máquina virtual de Azure | Microsoft Azure"
	description="Solución de problemas con la conexión del Escritorio remoto de una máquina virtual de Windows. Obtenga pasos rápidos de mitigación, ayuda por cada mensaje de error y soluciones detalladas de problemas de red."
	keywords="Error de escritorio remoto, error de conexión del escritorio remoto, no se puede conectar a la máquina virtual, solución de problemas con el escritorio remoto"
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
	ms.date="01/25/2016"
	ms.author="dkshir"/>

# Solución de problemas de conexiones del Escritorio remoto a una máquina virtual de Azure con Windows

La conexión de Escritorio remoto (RDP) a la máquina virtual de Azure basada en Windows puede presentar un error por varios motivos. El problema puede originar en el servicio de Escritorio remoto de la máquina virtual, la conexión de red o el cliente de Escritorio remoto en el equipo host. Este artículo le ayudará a determinar las causas y corregirlas.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Este artículo solo se aplica a máquinas virtuales de Azure que ejecutan Windows. En cuanto a las máquinas virtuales que ejecutan Linux, consulte [Solucionar problemas con la conexión SSH a una Máquina virtual de Azure](virtual-machines-troubleshoot-ssh-connections.md).

Si necesita más ayuda en cualquier momento con este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure o de desbordamiento de pila](https://azure.microsoft.com/support/forums/). Como alternativa, también puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte**.


<a id="quickfixrdp"></a>
## Corregir los errores comunes de Escritorio remoto

En esta sección se indican los pasos de corrección rápida para los problemas de conexión comunes de Escritorio remoto.

### Máquinas virtuales creadas con el modelo de implementación clásico

Estos pasos pueden resolver la mayoría los errores de conexión de Escritorio remoto en las máquinas virtuales creadas con el modelo de implementación clásico. Después de cada paso, pruebe a conectarse a la máquina virtual.

- Restablezca el servicio de escritorio remoto desde el [portal de Azure](https://portal.azure.com) para solucionar problemas de inicio con el servidor RDP.<br> Haga clic en **Examinar** > **Máquinas virtuales (clásico)** > su máquina virtual Windows > **Restablecer acceso remoto...**.

- Reinicie la máquina virtual para tratar otros problemas de inicio.<br> Haga clic en **Examinar** > **Máquinas virtuales** (clásico) > su máquina virtual Windows > **Reiniciar**.

- Cambie el tamaño de la máquina virtual para corregir cualquier problema de host.<br> Haga clic en **Examinar** > **Máquinas virtuales (clásico)** > su máquina virtual Windows > **Configuración** > **Tamaño**. Para información detallada, vea [Cambiar el tamaño de la máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx).

- Revise la captura de pantalla o el registro de la consola de la VM para corregir problemas de arranque.<br> Haga clic en **Examinar** > **Máquinas virtuales (clásico)** > su máquina virtual Windows > **Configuración** > **Diagnóstico de arranque**.

- Compruebe el estado de los recursos de la VM para ver si hay algún problema en la plataforma.<br> Haga clic en **Examinar** > **Máquinas virtuales (clásico)** > su máquina virtual Windows > **Configuración** > **Comprobar estado**.

### Máquinas virtuales creadas mediante el modelo de implementación del Administrador de recursos

Estos pasos pueden resolver la mayoría los errores de conexión de Escritorio remoto en las máquinas virtuales de Azure creadas mediante el modelo de implementación del Administrador de recursos. Después de cada paso, pruebe a conectarse a la máquina virtual.

- _Restablezca el acceso remoto_ con PowerShell<br> a. Si todavía no lo tiene, [instale Azure PowerShell y conéctese a su suscripción de Azure](../powershell-install-configure.md) mediante el método de Azure AD. Tenga en cuenta que no es necesario cambiar al modo de Administrador de recursos en las nuevas versiones de Azure PowerShell 1.0. x.

	b. Restablezca la conexión de RDP usando cualquiera de los siguientes comandos de Azure PowerShell. Reemplace `myRG`, `myVM`, `myVMAccessExtension` y la ubicación con valores correspondientes a la instalación.

	```
	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccessExtension" -ExtensionType "VMAccessAgent" -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" -Location Westus
	```
	O<br>

  ```
  Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -Location Westus
  ```

- Reinicie la máquina virtual para tratar otros problemas de inicio.<br> Haga clic en **Examinar** > **Máquinas virtuales** > su máquina virtual Windows > **Reiniciar**.

- Cambie el tamaño de la máquina virtual para corregir cualquier problema de host.<br> Haga clic en **Examinar** > **Máquinas virtuales** > su máquina virtual Windows > **Configuración** > **Tamaño**.

- Revise la captura de pantalla o el registro de la consola de la VM para corregir problemas de arranque.<br> Haga clic en **Examinar** > **Máquinas virtuales** > su máquina virtual Windows > **Configuración** > **Diagnóstico de arranque**.


Si los pasos anteriores no resuelven los errores de conexión de Escritorio remoto, vaya a la sección siguiente.

## Solucionar problemas específicos con la conexión del Escritorio remoto

A continuación se incluyen los errores más comunes que pueden surgir al intentar usar Escritorio remoto en una máquina virtual de Azure:

1. [Error de conexión de Escritorio remoto: la sesión remota se desconectó porque no hay servidores de licencias de Escritorio remoto disponibles para proporcionar una licencia](#rdplicense).

2. [Error de conexión de Escritorio remoto: el Escritorio remoto no encuentra el "nombre" del equipo](#rdpname).

3. [Error de conexión de Escritorio remoto: se ha producido un error de autenticación. No se puede conectar con la autoridad de seguridad local](#rdpauth).

4. [Error de Seguridad de Windows: Las credenciales no funcionaron](#wincred).

5. [Error de conexión de Escritorio remoto: este equipo no se puede conectar al equipo remoto](#rdpconnect).

<a id="rdplicense"></a>
### Error de conexión de Escritorio remoto: la sesión remota se desconectó porque no hay servidores de licencias de Escritorio remoto disponibles para proporcionar una licencia.

Causa: el período de gracia de licencias de 120 días para el rol de servidor de Escritorio remoto expiró y necesita instalar licencias.

Como solución alternativa, guarde una copia local del archivo RDP desde el portal y ejecute este comando en un símbolo del sistema de Windows PowerShell para conectarse. De este modo, se deshabilitará la obtención de licencias solo de esa conexión.

		mstsc <File name>.RDP /admin

Si no necesita más de dos conexiones simultáneas de Escritorio remoto a la máquina virtual, puede usar el administrador del servidor para quitar el rol de servidor de Escritorio remoto.

Vea también entrada de blog [Se produce el error en la máquina virtual de Azure «No hay servidores de licencias de Escritorio remoto disponibles»](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx).

<a id="rdpname"></a>
### Error de conexión de Escritorio remoto: el Escritorio remoto no encuentra el "nombre" del equipo.

Causa: el cliente de Escritorio remoto del equipo no pudo resolver el nombre del equipo en la configuración del archivo RDP.

Posibles soluciones:

- Si se encuentra en la intranet de una organización, asegúrese de que el equipo tenga acceso al servidor proxy y puede enviarle tráfico HTTPS.
- Si usa un archivo RDP almacenado localmente, intente usar uno generado por el portal. Esto garantizará que tiene el nombre DNS correcto de la máquina virtual o el servicio en la nube y el puerto de extremo de la máquina virtual. A continuación le facilitamos un archivo RDP de ejemplo generado por el portal:

		full address:s:tailspin-azdatatier.cloudapp.net:55919
		prompt for credentials:i:1

La parte de la dirección de este archivo RDP tiene el nombre de dominio completo del servicio en la nube que contiene la VM (en este ejemplo, tailspin-azdatatier.cloudapp.net) y el puerto TCP externo del extremo para el tráfico de Escritorio remoto (55919).

<a id="rdpauth"></a>
### Error de conexión de Escritorio remoto: se ha producido un error de autenticación. No se puede conectar con la autoridad de seguridad Local.

Causa: el VM de destino no encuentra la autoridad de seguridad en la parte del nombre de usuario de las credenciales.

Si el nombre de usuario tiene la forma *autoridadDeSeguridad\nombreDeUsuario* (ejemplo: CORP\\User1), la parte *autoridadDeSeguridad* es el nombre del equipo de la máquina virtual (para la autoridad de seguridad local) o un nombre de dominio de Active Directory.

Posibles soluciones:

- Si su cuenta de usuario está en la propia VM, compruebe si el nombre de la máquina virtual está escrito correctamente.
- Si la cuenta está en un dominio de Active Directory, compruebe la ortografía del nombre de dominio.
- Si es una cuenta de dominio de Active Directory y el nombre de dominio está escrito correctamente, compruebe que hay algún controlador de dominio disponible en dicho dominio. Esto puede ser un problema común en una red virtual de Azure que contiene controladores de dominio en los que no se ha iniciado un equipo de controlador de dominio. Como solución alternativa, puede usar una cuenta de administrador local, en lugar de una cuenta de dominio.

<a id="wincred"></a>
### Error de Seguridad de Windows: Las credenciales no funcionaron.

Causa: la VM de destino no pudo validar el nombre de la cuenta y la contraseña.

Un equipo con Windows puede validar las credenciales de una cuenta local o de una cuenta de dominio.

- Para las cuentas locales, use la sintaxis *nombreDeEquipo\nombreDeUsuario* (ejemplo: SQL1\\Admin4798).
- Para las cuentas de dominio, use la sintaxis *nombreDeDominio\nombreDeUsuario* (ejemplo: CONTOSO\\johndoe).

Si promovió su máquina virtual a un controlador de dominio de un nuevo bosque de Active Directory, la cuenta de administrador local con la que inició sesión, también se convierte en una cuenta equivalente con la misma contraseña en el nuevo bosque y dominio. A continuación, se elimina la cuenta local. Por ejemplo, si inició sesión con la cuenta local DC1\\DCAdmin y promovió la máquina virtual como controlador de dominio en un bosque nuevo para el dominio corp.contoso.com, la cuenta local DC1\\DCAdmin se elimina y se crea una nueva cuenta de dominio CORP\\DCAdmin con la misma contraseña.

Asegúrese de que el nombre de la cuenta es un nombre que la máquina virtual pueda comprobar como una cuenta válida y que la contraseña sea correcta.

Si necesita cambiar la contraseña de la cuenta de administrador local, consulte [Cómo restablecer una contraseña o el servicio de Escritorio remoto para máquinas virtuales de Windows](virtual-machines-windows-reset-password.md).

<a id="rdpconnect"></a>
### Error de conexión de Escritorio remoto: este equipo no se puede conectar al equipo remoto.

Causa: la cuenta usada para conectarse no tiene derechos de inicio de sesión de Escritorio remoto.

Todos los equipos de Windows tiene un grupo local Usuarios de Escritorio remoto, que contiene las cuentas y grupos que pueden iniciar sesión en él de forma remota. Los miembros del grupo Administradores local también tienen acceso, aunque esas cuentas no se enumeran en el grupo local Usuarios de Escritorio remoto. Para los equipos unidos a un dominio, el grupo de administradores locales también contiene los administradores de dominio para el dominio.

Asegúrese de que la cuenta que usa para conectarse tiene derechos de inicio de sesión de Escritorio remoto. Como solución alternativa, use una cuenta de dominio o de administrador local para conectarse a través de Escritorio remoto y, seguidamente, use el complemento Administración de equipos (**Herramientas del sistema > Usuarios y grupos locales > Grupos > Usuarios de Escritorio remoto**) para agregar la cuenta deseada al grupo local Usuarios de Escritorio remoto.

## Solución de problemas de errores genéricos de Escritorio remoto

Si ninguno de estos errores se produjo y todavía no se pudo conectar a la VM a través de Escritorio remoto, consulte [Solución de problemas detallada de conexiones de Escritorio remoto a máquinas virtuales de Azure basadas en Windows](virtual-machines-rdp-detailed-troubleshoot.md).


## Recursos adicionales

[Paquete de diagnóstico de Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Cómo restablecer una contraseña o el servicio de Escritorio remoto para máquinas virtuales de Windows](virtual-machines-windows-reset-password.md)

[Instalación y configuración de Azure PowerShell](../powershell-install-configure.md)

[Solución de problemas de conexiones de Secure Shell (SSH) en una máquina virtual de Azure basada en Linux](virtual-machines-troubleshoot-ssh-connections.md)

[Solucionar problemas de acceso a una aplicación que se ejecuta en una máquina virtual de Azure](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=AcomDC_0204_2016-->