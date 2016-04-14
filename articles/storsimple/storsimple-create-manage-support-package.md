<properties 
   pageTitle="Crear un paquete de soporte de StorSimple | Microsoft Azure"
   description="Aprenda a crear, descifrar y editar un paquete de soporte para el dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="alkohli" />


# Crear y administrar paquetes de soporte técnico de StorSimple

## Información general

Este tutorial describe las diversas tareas asociadas con la creación y administración de un paquete de soporte. Un paquete de soporte contiene todos los registros relevantes en un formato comprimido y cifrado, y se utiliza para ayudar al equipo de soporte técnico de Microsoft a solucionar los problemas del dispositivo StorSimple.

Este tutorial incluye instrucciones paso a paso para crear y administrar el paquete de soporte mediante:

- La sección **Paquete de soporte** de la página de**Mantenimiento** del servicio de Administrador de StorSimple
- Windows PowerShell para StorSimple

Después de leer este tutorial, podrá:

- Crear un paquete de soporte
- Descifrar y editar un paquete de soporte


## Crear un paquete de soporte en el Portal de Azure clásico

Para solucionar los problemas que pudiera experimentar con el servicio de Administrador de StorSimple, puede crear y cargar un paquete de soporte en el sitio de soporte técnico de Microsoft a través de la página **Mantenimiento** del servicio en el Portal de Azure clásico. Deberá proporcionar una clave de paso de soporte para permitir la carga. La clave de paso de soporte se la proporcionará el ingeniero de soporte en un correo electrónico. Se crea un paquete de soporte técnico comprimido y sin cifrar (archivo .cab). Una vez que proporciona la clave de paso, el ingeniero de soporte puede recuperar este paquete en el sitio de soporte técnico.

Lleve a cabo los siguientes pasos en el Portal clásico para crear un paquete de soporte técnico:

#### Para crear un paquete de soporte en el Portal de Azure clásico

1. Navegue hasta **Dispositivos > Mantenimiento**.

2. En la sección **Paquete de soporte**, haga clic en **Crear y cargar paquete de soporte**.

3. En el cuadro de diálogo **Crear y cargar paquete de soporte**, haga lo siguiente:

	![Crear paquete de soporte](./media/storsimple-create-manage-support-package/IC740923.png)
											
	- Proporcione la **Clave de paso de soporte técnico**. Esta clave se la debe enviar el ingeniero de soporte técnico de Microsoft en un correo electrónico.
 	
	- Active el recuadro combinado para dar su consentimiento para **cargar automáticamente el paquete de soporte en el sitio de soporte técnico de Microsoft**.
 	
	- Haga clic en el icono de marca de verificación ![Icono de marca de verificación](./media/storsimple-create-manage-support-package/IC740895.png).


## Crear un paquete de soporte en Windows PowerShell para StorSimple

Si necesita editar los archivos de registro antes de crear un paquete, debe crear el paquete a través de Windows PowerShell para StorSimple.

Lleve a cabo los siguientes pasos para crear un paquete de soporte en Windows PowerShell para StorSimple


#### Para crear un paquete de soporte en Windows PowerShell para StorSimple

1. Escriba el siguiente comando para iniciar una sesión de Windows PowerShell como administrador en el equipo remoto que utiliza para conectarse con su dispositivo StorSimple:

	`Start PowerShell`

2. En la sesión de Windows PowerShell, conéctese al espacio de ejecución de la consola SSAdmin del dispositivo:


	- En el símbolo del sistema, escriba: 
			
		`$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
		
		
	1. En el cuadro de diálogo que se abre, escriba la contraseña de administrador del dispositivo. La contraseña predeterminada es:
	 
		`Password1`

		![Sesión de PowerShell al espacio de ejecución de SSAdminConsole](./media/storsimple-create-manage-support-package/IC740962.png)

	2. Haga clic en **Aceptar**.
	1. En el símbolo del sistema, escriba: 
		
		`Enter-PSSession $MS`


3. En la sesión que se abre, escriba el comando adecuado.


	- Para los recursos compartidos de red que están protegidos por contraseña, escriba:

		`Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`

		Se le solicitará una contraseña, una ruta de acceso a la carpeta compartida de red y una frase de contraseña de cifrado (ya que el paquete de soporte está cifrado). Cuando las proporcione, se creará un paquete de soporte en la carpeta especificada.
											

	- Para las carpetas compartidas de red abiertas (que no están protegidas por contraseña), no es necesario el parámetro `-Credential`. Escriba lo siguiente:

		`Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`

		El paquete de soporte se creará para ambos controladores en la carpeta compartida de red especificada. Es un archivo comprimido y cifrado que puede enviarse al soporte técnico de Microsoft para solucionar problemas. Para obtener más información, consulte [Ponerse en contacto con el soporte técnico de Microsoft](storsimple-contact-microsoft-support.md).


### Más información sobre el cmdlet Export-HcsSupportPackage
Los distintos parámetros que pueden utilizarse con el cmdlet Export-HcsSupportPackage se incluyen a continuación.

| S. N.º | Parámetro | Obligatorio/opcional | Descripción |
|--------|----------------------|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1 | Ruta de acceso | Obligatorio | Se utiliza para proporcionar la ubicación de la carpeta compartida de red en la que se colocará el paquete de soporte. |
| 2 | EncryptionPassphrase | Obligatorio | Se utiliza para proporcionar una frase de contraseña que ayuda a cifrar el paquete de soporte. |
| 3 | Credential: | Opcional | Se utiliza para suministrar credenciales de acceso a la carpeta compartida de red. |
| 4 | Force: | Opcional | Se utiliza para omitir el paso de confirmación de la frase de contraseña de cifrado. |
| 5 | PackageTag | Opcional | Se utiliza para especificar un directorio en Path en que se colocará el paquete de soporte. El valor predeterminado es [nombre de dispositivo]-[fecha y hora actuales:aaaa-MM-dd-HH-mm-ss]. |
| 6 | Scope | Opcional | Especifique como **Clúster** (predeterminado) para crear un paquete de soporte para ambos controladores. Si desea crear un paquete únicamente para el controlador actual, especifique **Controlador**. |


## Editar un paquete de soporte

Después de haber generado un paquete de soporte, puede que necesite editarlo para quitar información específica del cliente como nombres de volumen, direcciones IP de dispositivos y nombres de copias de seguridad de los archivos de registro.

> [AZURE.IMPORTANT] Solo pueden editarse los paquetes de soporte generados a través de Windows PowerShell para StorSimple. No es posible editar los paquetes creados en el Portal de Azure clásico con el servicio de Administrador de StorSimple.

Para editar un paquete de soporte antes de cargarlo en el sitio de soporte técnico de Microsoft, debe descifrar el paquete de soporte, editar los archivos y volver a cifrarlo. Lleve a cabo los siguientes pasos para editar un paquete de soporte:

#### Para editar un paquete de soporte en Windows PowerShell para StorSimple

1. Genere un paquete de soporte como se describe en [Creación de un paquete de soporte en Windows PowerShell para StorSimple](#create-a-support-package-in-windows-powershell-for-storsimple).

2. [Descargue el script](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente en su cliente.

3. Importe el módulo de Windows PowerShell. Deberá especificar la ruta de acceso a la carpeta local en la que descargó el script. Para importar el módulo, escriba:
 
	`Import-module <Path to the folder that contains the Windows PowerShell script>`

4. Abra la carpeta del paquete de soporte. Tenga en cuenta que todos los archivos son *.aes*, es decir, archivos comprimidos y cifrados. Abra los archivos. Para abrir archivos, escriba:

	`Open-HcsSupportPackage <Path to the folder that contains support package files>`

	Esto descomprimirá y descifrará los archivos. Notará que ahora se muestran las extensiones de archivo reales para todos los archivos.
	
	![Editar paquete de soporte 3](./media/storsimple-create-manage-support-package/IC750706.png)


5. Cuando se le solicite la frase de contraseña de cifrado, escriba la frase de contraseña utilizada al crear el paquete de soporte.

    	cmdlet Open-HcsSupportPackage at command pipeline position 1
    
    	Supply values for the following parameters:EncryptionPassphrase: ****
	
6. Navegue hasta la carpeta que contiene los archivos de registro. Dado que los archivos de registro ahora están descomprimidos y descifrados, tendrán sus extensiones de archivo originales. Modifique estos archivos para quitar cualquier información específica del cliente como nombres de volumen y direcciones IP de dispositivo, y guarde los archivos.

7. Cierre los archivos. Al cerrar los archivos, se comprimirán con Gzip y se cifrarán con AES-256. Esto es por seguridad y para acelerar la transferencia del paquete de soporte por la red. Para cerrar los archivos, escriba:

	`Close-HcsSupportPackage <Path to the folder that contains support package files>`

	![Editar paquete de soporte 2](./media/storsimple-create-manage-support-package/IC750707.png)

8. Cuando se le solicite, proporcione una frase de contraseña de cifrado para el paquete de soporte modificado.

	    cmdlet Close-HcsSupportPackage at command pipeline position 1
    	Supply values for the following parameters:EncryptionPassphrase: ****

9. Anote la frase de contraseña nueva para poder compartirla con el soporte técnico de Microsoft cuando se le solicite.


### Ejemplo: Edición de archivos de un paquete de soporte en un recurso compartido protegido por contraseña

A continuación se muestra un ejemplo que explica cómo descifrar, editar y volver a cifrar un paquete de soporte.

![Editar paquete de soporte 1](./media/storsimple-create-manage-support-package/IC750708.png)

    	PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1
    
    	PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw
    
    	cmdlet Open-HcsSupportPackage at command pipeline position 1
    
    	Supply values for the following parameters:
    
    	EncryptionPassphrase: ****
    
    	PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw
    
    	cmdlet Close-HcsSupportPackage at command pipeline position 1
    
    	Supply values for the following parameters:
    
    	EncryptionPassphrase: ****
    
    	PS C:\WINDOWS\system32>

## Pasos siguientes

- Aprenda cómo [usar paquetes de soporte y registros de dispositivos para solucionar los problemas de implementación de su dispositivo](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).

- Obtenga información sobre cómo [usar el servicio StorSimple Manager para administrar el dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0224_2016-->