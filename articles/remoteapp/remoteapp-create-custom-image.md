<properties
	pageTitle="Creación de una imagen de plantilla personalizada para Azure RemoteApp | Microsoft Azure"
	description="Aprenda a crear una imagen de plantilla personalizada para Azure RemoteApp. Puede usar esta plantilla con una colección híbrida o en la nube."
	services="remoteapp"
	documentationCenter=""
	authors="lizap"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="remoteapp"
	ms.workload="compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/02/2016" 
	ms.author="elizapo"/>

# Creación de una imagen de plantilla personalizada para Azure RemoteApp
RemoteApp de Azure usa una imagen de plantilla de Windows Server 2012 R2 para hospedar todos los programas que desea compartir con sus usuarios. Para crear una imagen de plantilla de RemoteApp, puede comenzar a usar una imagen existente o crear una nueva.


> [AZURE.TIP] ¿Sabía que ahora puede crear una imagen a partir de una máquina virtual de Azure? Verídico, y esto reduce la cantidad de tiempo que se tarda en importar la imagen. Consulte los pasos [aquí](remoteapp-image-on-azurevm.md).

Los requisitos para la imagen que se pueden cargar para usarse con RemoteApp de Azure son:


- El tamaño de la imagen debe ser un múltiplo de MB. Si se intenta cargar una imagen que no es un múltiplo exacto, la carga no se realizará.
- El tamaño de imagen debe ser de 127 GB o menos.
- Debe estar en un archivo VHD (los archivos VHDX [unidades de disco duro virtuales de Hyper-V] no se admiten actualmente).
- El disco duro virtual no debe ser una máquina virtual de generación 2.
- El archivo VHD puede ser de tamaño fijo o expandirse dinámicamente. Se recomienda un archivo VHD que se expanda dinámicamente porque tarda menos tiempo en cargarse en Azure que un archivo VHD de tamaño fijo.
- El disco se debe inicializar usando el estilo de particiones Registro de arranque maestro (MBR, Master Boot Record). El estilo de particiones de tabla de particiones GUID (GPT) no se admite.
- El archivo VHD debe contener una sola instalación de Windows Server 2012 R2. Puede contener varios volúmenes, pero uno de ellos con una instalación de Windows.
- El rol Host de sesión de Escritorio remoto (RDSH, Remote Desktop Session Host) y la característica Experiencia de escritorio deben estar instalados.
- El rol Agente de conexión a Escritorio remoto *no* debe instalarse.
- El Sistema de cifrado de archivos (EFS, Encrypting File System) debe estar instalado.
- La imagen debe estar preparada con sysprep con los parámetros **/oobe /generalize /shutdown** (No USE el parámetro **/mode:vm**).
- No se admite la carga de su VHD desde una cadena de instantáneas.


**Antes de empezar**

Necesita llevar a cabo los pasos siguientes antes de crear el servicio:

- [Suscríbase](https://azure.microsoft.com/services/remoteapp/) a RemoteApp.
- Cree una cuenta de usuario en Active Directory para usar la cuenta de servicio RemoteApp. Restrinja los permisos para esta cuenta de forma que solamente pueda unir máquinas al dominio. Consulte [Configuración de Azure Active Directory para RemoteApp](remoteapp-ad.md) para obtener más información.
- Recopile información sobre la red local: dirección IP de información y detalles de dispositivos VPN.
- Instale el módulo de [Azure PowerShell](../powershell-install-configure.md).
- Recopile información sobre los usuarios a los que quiera conceder acceso. Esta información puede ser información de cuentas de Microsoft o información de cuentas de trabajo de Active Directory de usuarios.



## Creación de una imagen de plantilla ##

Estos son los pasos de alto nivel para crear una nueva imagen de plantilla desde el principio:

1.	Busque un DVD o una imagen ISO de actualización de Windows Server 2012 R2.
2.	Cree un archivo VHD.
4.	Instale Windows Server 2012 R2.
5.	Instale el rol Host de sesión de Escritorio remoto (RDSH, Remote Desktop Session Host) y la característica Experiencia de escritorio.
6.	Instale las características adicionales que necesitan sus aplicaciones.
7.	Instale y configure sus aplicaciones. Para que le resulte más fácil compartir aplicaciones, agregue las aplicaciones o los programas que quiera compartir al menú **Inicio** de la imagen, específicamente en **%systemdrive%\\ProgramData\\Microsoft\\Windows\\Menú Inicio\\Programas.
8.	Realice cualquier configuración adicional de Windows que requieran sus aplicaciones.
9.	Deshabilite el Sistema de cifrado de archivos (EFS).
10.	**REQERIDO:** vaya a Windows Update e instale todas las actualizaciones importantes.
9.	Aplique la herramienta SYSPREP a la imagen.

A continuación se indican los pasos detallados para crear una nueva imagen:

1.	Busque un DVD o una imagen ISO de actualización de Windows Server 2012 R2.
2.	Cree un archivo VHD usando Administración de discos.
	1.	Inicie Administración de discos (diskmgmt.msc).
	2.	Cree un archivo VHD que se expanda dinámicamente con un tamaño mínimo de 40 GB. (Calcule la cantidad de espacio necesario para Windows, sus aplicaciones y personalizaciones. Windows Server con el rol RDSH y la característica Experiencia de escritorio instalados requerirán 10 GB de espacio aproximadamente).
		1.	Haga clic en **Acción > Crear VHD**.
		2.	Especifique la ubicación, el tamaño y el formato VHD. Seleccione **Expansión dinámica** y haga clic en **Aceptar**.

			Esto se ejecutará durante varios segundos. Cuando la creación del archivo VHD se complete, debe ver un nuevo disco sin ninguna letra de unidad y en el estado "No inicializado" en la consola Administración de discos.

		- Haga clic con el botón secundario en el disco (no en el espacio sin asignar) y haga clic en **Inicializar disco**. Seleccione **MBR** (registro de arranque maestro) como estilo de partición y haga clic en **Aceptar**.
		- Cree un nuevo volumen: haga clic con el botón secundario en el espacio sin asignar y, luego, haga clic en **Nuevo volumen simple**. Puede aceptar los valores predeterminados del asistente pero asegúrese de asignar una letra de unidad para evitar problemas potenciales cuando cargue la imagen de plantilla.
		- Haga clic con el botón secundario en el disco y después en **Ocultar VHD**.





1. Instale Windows Server 2012 R2:
	1. Cree una nueva máquina virtual. Use el Asistente para nueva máquina virtual de Administrador de Hyper-V o Cliente Hyper-V.
		1. En la página Especificar generación, elija **Generación 1**.
		2. En la página Conectar disco duro virtual, seleccione **Usar un disco duro virtual existente** y busque el VHD que creó en el paso anterior.
		2. En la página Opciones de instalación, seleccione **Instalar un sistema operativo desde un CD/DVD-ROM de arranque** y después seleccione la ubicación del disco de instalación de Windows Server 2012 R2.
		3. Elija otras opciones del asistente necesarias para instalar Windows y sus aplicaciones. Finalice el asistente.
	2.  Después de finalizar el asistente, edite la configuración de la máquina virtual y realice cualquier cambio necesario para instalar Windows y sus programas —por ejemplo, el número de procesadores virtuales— y, por último, haga clic en **Aceptar**.
	4.  Conéctese a la máquina virtual e instale Windows Server 2012 R2.
1. Instale el rol Host de sesión de Escritorio remoto (RDSH, Remote Desktop Session Host) y la característica Experiencia de escritorio:
	1. Inicie Administrador de servidores.
	2. Haga clic en **Agregar roles y características** en la pantalla de bienvenida o desde el menú **Administrar**.
	3. Haga clic en **Siguiente** en la página Antes de empezar.
	4. Seleccione **Instalación basada en características o en roles** y haga clic en **Siguiente**.
	5. Seleccione la máquina local en la lista y haga clic en **Siguiente**.
	6. Seleccione **Servicios de Escritorio remoto** y haga clic en **Siguiente**.
	7. Expanda **Infraestructura e interfaces de usuario** y seleccione **Experiencia de escritorio**.
	8. Haga clic en **Agregar características** y después en **Siguiente**.
	9. En la página Servicios de Escritorio remoto, haga clic en **Siguiente**.
	10. Haga clic en **Host de sesión de Escritorio remoto**.
	11. Haga clic en **Agregar características** y después en **Siguiente**.
	12. En la página Confirmar selecciones de instalación, seleccione **Reiniciar automáticamente el servidor de destino en caso necesario** y haga clic en **Sí** en la advertencia de reinicio.
	13. Haga clic en **Instalar**. El equipo se reiniciará.
1.	Instale las características adicionales que necesiten sus aplicaciones, como por ejemplo .NET Framework 3.5. Para instalar las características, ejecute el Asistente para agregar roles y características.
7.	Instale y configure los programas y aplicaciones que desee publicar a través de RemoteApp.

>[AZURE.IMPORTANT]
>
>Instale el rol RDSH antes de instalar las aplicaciones para garantizar que se detecta cualquier problema con la compatibilidad de aplicaciones antes de que la imagen se cargue en RemoteApp.
>
>Asegúrese de que aparece un acceso directo a la aplicación (archivo **.lnk**) en el menú **Inicio** para todos los usuarios (%systemdrive%\\ProgramData\\Microsoft\\Windows\\Menú Inicio\\Programas). Asegúrese también de que el icono que ve en el menú **Inicio** es lo que quiere que vean los usuarios. Si no es así, cámbielo. (No *tiene* que agregar la aplicación al menú Inicio, pero resulta mucho más fácil publicar la aplicación en RemoteApp. De lo contrario, debe proporcionar la ruta de instalación de la aplicación cuando publique la aplicación.)


8.	Realice cualquier configuración adicional de Windows que requieran sus aplicaciones.
9.	Deshabilite el Sistema de cifrado de archivos (EFS). Ejecute el comando siguiente en una ventana de comandos con privilegios elevados:

		Fsutil behavior set disableencryption 1

	Alternativamente, puede establecer o agregar el siguiente valor DWORD en el Registro:

		HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1
9.	Si crea la imagen dentro de una máquina virtual de Azure, cambie el nombre del archivo **\\%windir%\\Panther\\Unattend.xml**, ya que bloqueará el script de carga usado posteriormente desde el trabajo. Cambie el nombre de este archivo por Unattend.old; de este modo, seguirá teniendo el archivo en caso de que necesite revertir la implementación.
10.	Vaya a Windows Update e instale todas las actualizaciones importantes. Puede que tenga que ejecutar varias veces Windows Update para obtener todas las actualizaciones. (A veces se instala una actualización, y esa misma actualización requiere una actualización).
10.	Aplique la herramienta SYSPREP a la imagen. En un símbolo del sistema con privilegios elevados, ejecute el siguiente comando:

	**C:\\Windows\\System32\\sysprep\\sysprep.exe /generalize /oobe /shutdown**

	**Nota:** no use el modificador **/mode:vm** del comando SYSPREP aunque se trate de una máquina virtual.


## Pasos siguientes ##
Ahora que ya tiene su imagen de plantilla personalizada, es necesario que la cargue en su colección de RemoteApp. Para obtener información sobre cómo crear la colección, lea los artículos siguientes:


- [Creación de una colección híbrida de RemoteApp](remoteapp-create-hybrid-deployment.md)
- [Creación de una colección en la nube de RemoteApp](remoteapp-create-cloud-deployment.md)
 

<!---HONumber=AcomDC_0211_2016-->