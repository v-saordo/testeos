<properties
   pageTitle="Implementar una matriz virtual de StorSimple 3: Configurar el dispositivo virtual como servidor de archivos"
   description="En este tercer tutorial sobre la implementación de matrices virtuales StorSimple se enseña a configurar un dispositivo virtual como servidor de archivos."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/01/2016"
   ms.author="alkohli"/>

# Implementar una matriz virtual de StorSimple: Configurar un servidor de archivos

![](./media/storsimple-ova-deploy3-fs-setup/fileserver4.png)

## Introducción 

Este artículo se aplica a la matiz virtual de Microsoft Azure StorSimple (también conocida como dispositivo virtual local de StorSimple o dispositivo virtual de StorSimple) que se ejecuta en la versión de disponibilidad general de marzo de 2016. En este artículo se describe cómo realizar la instalación inicial, registrar el servidor de archivos de StorSimple, completar la instalación del dispositivo y crear y conectarse a recursos compartidos de SMB. Este es el último artículo de la serie de tutoriales de implementación necesarios para implementar completamente la matriz virtual como servidor de archivos o servidor iSCSI.

El proceso de instalación y configuración puede tardar unos 10 minutos en completarse.


## Requisitos previos de instalación

Antes de instalar y configurar el dispositivo virtual StorSimple, asegúrese de que:

-   Ha aprovisionado un dispositivo virtual y se ha conectado a él según se detalla en la sección [Provision a StorSimple Virtual Array in Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) (Aprovisionar una matriz virtual de StorSimple en Hyper-V) o [Provision a StorSimple Virtual Array in VMware](storsimple-ova-deploy2-provision-vmware.md) (Aprovisionar una matriz virtual de StorSimple en VMware).

-   Tiene la clave de registro del servicio de StorSimple Manager que creó para administrar dispositivos virtuales StorSimple. Para más información, consulte [Paso 2: Obtener la clave de registro del servicio](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) para la matriz virtual de StorSimple.

-   Si este es el segundo dispositivo virtual o subsiguiente que registra en un servicio de StorSimple Manager existente, debe tener la clave de cifrado de datos del servicio. Esta clave se generó cuando el primer dispositivo se registró correctamente en este servicio. Si perdió esta clave, consulte la sección [Obtener la clave de cifrado de datos del servicio](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) para la matriz virtual de StorSimple.

## Configuración paso a paso

Use las siguientes instrucciones paso a paso para instalar y configurar el dispositivo virtual StorSimple.

## Paso 1: Completar la configuración de la interfaz de usuario web local y registrar el dispositivo 


#### Para completar la instalación y registrar el dispositivo

1.  Abra una ventana del explorador y conéctese a la interfaz de usuario de web local. Escriba:	

    `https://<ip-address of network interface>`

	Use la dirección URL de conexión que anotó en el paso anterior. Verá un error que indica que hay un problema con el certificado de seguridad del sitio web. Haga clic en **Continue to this webpage** (Continuar a esta página web).

	![](./media/storsimple-ova-deploy3-fs-setup/image2.png)

1.  Inicie sesión en la interfaz de usuario web del dispositivo virtual como **StorSimpleAdmin**. Escriba la contraseña del administrador de dispositivos que cambió en el paso 3: Iniciar el dispositivo virtual en [Provision a StorSimple Virtual Array in Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) (Aprovisionar una matriz virtual de StorSimple en Hyper-V) o en [Provision a StorSimple Virtual Array in VMware](storsimple-ova-deploy2-provision-vmware.md) (Aprovisionar una matriz virtual de StorSimple en VMware).

	![](./media/storsimple-ova-deploy3-fs-setup/image3.png)

1.  Lo llevará a la página de **inicio**. En esta página se describen los distintos parámetros necesarios para configurar y registrar el dispositivo virtual en el servicio StorSimple Manager. Tenga en cuenta que las opciones **Network settings** (Configuración de red), **Web proxy settings** (Configuración de proxy web) y **Time settings** (Configuración de hora) son opcionales. Los únicos parámetros obligatorios son **Device settings** (Configuración de dispositivo) y **Cloud settings** (Configuración de la nube).

	![](./media/storsimple-ova-deploy3-fs-setup/image4.png)

1.  En la página **Network settings** (Configuración de red) en **Network interfaces** (Interfaces de red), DATA 0 se configurará automáticamente para usted. Cada interfaz de red se establece de forma predeterminada para obtener la dirección IP automáticamente (DHCP). Por lo tanto, una dirección IP, una subred y una puerta de enlace se asignarán automáticamente (tanto para IPv4 como para IPv6).

	![](./media/storsimple-ova-deploy3-fs-setup/image5.png)

	Si agrega más de una interfaz de red durante el aprovisionamiento del dispositivo, se pueden configurar aquí. Tenga en cuenta que puede configurar la interfaz de red como IPv4 únicamente o como IPv4 e IPv6. No se admiten las configuraciones de solo IPv6.

1.  Los servidores DNS son necesarios porque se utilizan cuando el dispositivo intenta comunicarse con sus proveedores de servicios de almacenamiento en la nube o para resolver el dispositivo por nombre cuando se configura como servidor de archivos. En la página **Network settings** (Configuración de red) en **DNS servers** (Servidores DNS):

    1.  Se configurarán automáticamente un servidor DNS principal y secundario. Si decide configurar direcciones IP estáticas, puede especificar servidores DNS. Para lograr la alta disponibilidad, se recomienda que configure un servidor DNS principal y uno secundario.

    2.  Haga clic en **Apply**. Esto aplicará y validará la configuración de red.

2.  En la página **Device settings** (Configuración de dispositivo):

    1.  Asigne un **nombre** exclusivo al dispositivo. Este nombre puede tener de 1 a 15 caracteres y puede contener letras, números y guiones.

    2.  Haga clic en el icono de **servidor de archivos** ![](./media/storsimple-ova-deploy3-fs-setup/image6.png) para el **tipo** del dispositivo que está creando. Un servidor de archivos le permitirá crear carpetas compartidas.

    3.  Como el dispositivo es un servidor de archivos, debe unir el dispositivo a un dominio. Escriba un valor en **nombre de dominio**.

	1.  Haga clic en **Apply**.

2.  Aparece un cuadro de diálogo. Escriba las credenciales del dominio en el formato especificado. Haga clic en el icono de marca de verificación. Se comprobarán las credenciales del dominio. Verá un mensaje de error si las credenciales son incorrectas.

	![](./media/storsimple-ova-deploy3-fs-setup/image7.png)

1.  Haga clic en **Apply**. Esto aplicará y validará la configuración de dispositivo.

	![](./media/storsimple-ova-deploy3-fs-setup/image8.png)

	> [AZURE.NOTE]
	> 
	> Asegúrese de que su matriz virtual está en su propia unidad organizativa (UO) de Active Directory y de que no se le aplica ningún objeto de directiva de grupo (GPO).

1.  Configure el servidor proxy web (de manera opcional). Aunque la configuración del proxy web es opcional, tenga en cuenta que, si usa un proxy web, solo puede configurarlo aquí.

	![](./media/storsimple-ova-deploy3-fs-setup/image9.png)

	En la página **Proxy web**:

	1.  Proporcione la **URL de proxy web** en este formato: *http://&lt;host-IP dirección o nombre de dominio completo&gt;: Número de puerto*. Tenga en cuenta que no se admiten direcciones URL HTTPS.

	2.  Especifique **Autenticación** como **Básica**, **NTLM** o **Ninguna**.

	3.  Si utiliza autenticación, también debe proporcionar un **Nombre de usuario** y una **Contraseña**.

	4.  Haga clic en **Apply**. Esto validará y aplicará los parámetros de proxy web configurados.

1.  (Opcionalmente) configure las opciones de hora para el dispositivo, como la zona horaria y los servidores NTP principal y secundario. Se requieren servidores NTP, ya que el dispositivo debe sincronizar la hora para que pueda autenticarse con los proveedores de servicios en la nube.

	![](./media/storsimple-ova-deploy3-fs-setup/image10.png)

	En la página **Configuración de hora**:

	1.  De la lista desplegable, seleccione la **Zona horaria** según la ubicación geográfica en la que se va a implementar el dispositivo. La zona horaria predeterminada para el dispositivo es la hora del Pacífico (PST). El dispositivo usará esta zona horaria para todas las operaciones programadas.

	2.  Especifique un **Servidor NTP principal** para el dispositivo o acepte el valor predeterminado de time.windows.com. Asegúrese de que su red permite que el tráfico NTP pase del centro de datos a Internet.

	3.  Opcionalmente, especifique un **Servidor NTP secundario** para el dispositivo.

	4.  Haga clic en **Apply**. Esto validará y aplicará los parámetros de hora configurados.

1.  Configure las opciones de nube para el dispositivo. En este paso, completa la configuración del dispositivo local y, a continuación, registra el dispositivo en el servicio StorSimple Manager.

    1.  Introduzca la **Clave de registro del servicio** que obtuvo en [Paso 2: Obtener la clave de registro del servicio](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) para la matriz virtual de StorSimple.

    2.  Si no es el primer dispositivo que va a registrar en este servicio, debe proporcionar la **Clave de cifrado de datos del servicio**. Esta clave se necesita junto con la clave de registro del servicio para registrar dispositivos adicionales en el servicio StorSimple Manager. Para obtener más información, consulte para obtener la [clave de cifrado de datos del servicio](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) en la interfaz de usuario web local.

    3.  Haga clic en **Registrar**. Se reiniciará el dispositivo. Debe esperar de 2 a 3 minutos antes de que el dispositivo se registre correctamente. Una vez que se haya reiniciado el dispositivo, irá a la página de inicio de sesión.

		![](./media/storsimple-ova-deploy3-fs-setup/image13.png)
	

1.  Vuelva al Portal de Azure clásico. En la página **Dispositivos**, compruebe que el dispositivo se conectó correctamente al servicio consultando el estado. El estado del dispositivo debe ser **Activo**.

![](./media/storsimple-ova-deploy3-fs-setup/image12.png)

## Paso 2: Completar la instalación de dispositivos necesarios

Para completar a la configuración del dispositivo StorSimple, debe hacer lo siguiente:

-   Seleccione una cuenta de almacenamiento para asociarla a este dispositivo.

-   Elija la configuración de cifrado para los datos que se envían a la nube.

Siga estos pasos en el [Portal de Azure clásico](https://manage.windowsazure.com/) para completar la configuración mínima del dispositivo.

#### Para completar la configuración mínima del dispositivo

1.  Desde la página **Dispositivos**, seleccione el dispositivo que acaba de crear. Este dispositivo se debe mostrar como **Activo**. Haga clic en la flecha situada junto al nombre del dispositivo y, a continuación, haga clic en **Inicio rápido**.

2.  Haga clic en **Completar configuración del dispositivo** para iniciar el Asistente para configurar dispositivos.

3.  En la página **Configuración básica** del Asistente para configurar dispositivos, haga lo siguiente:

	1.  Especifique una cuenta de almacenamiento para usarla con el dispositivo. Puede seleccionar una cuenta de almacenamiento existente en esta suscripción de la lista desplegable o especificar **Agregar más** para elegir una cuenta de una suscripción diferente.

	2.  Defina la configuración de cifrado para todos los datos en reposo (cifrado de AES) que se enviarán a la nube. Para cifrar los datos, seleccione el cuadro combinado para **habilitar la clave de cifrado de almacenamiento en la nube**. Escriba un cifrado de almacenamiento en la nube que contenga 32 caracteres. Vuelva a escribir la clave para confirmarla. Se usará una clave AES de 256 bits con la clave definida por el usuario para el cifrado.

	3.  Haga clic en el icono de marca de verificación ![](./media/storsimple-ova-deploy3-fs-setup/image15.png).

		![](./media/storsimple-ova-deploy3-fs-setup/image16.png)

Ahora se actualizará la configuración. Después de actualizar la configuración correctamente, el botón para completar la configuración del dispositivo aparecerá atenuado. Volverá a la página **Inicio rápido** del dispositivo.

 ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)


> [AZURE.NOTE]                                                              
>
> Puede modificar la configuración restante del dispositivo en cualquier momento accediendo a la página **Configurar**.

## Paso 3: Agregar un recurso compartido

Siga estos pasos en el [Portal de Azure clásico](https://manage.windowsazure.com/) para crear un recurso compartido.

#### Para crear un recurso compartido

1.  En la página **Inicio rápido** del dispositivo, haga clic en **Agregar un recurso compartido**. Se iniciará el Asistente para agregar un recurso compartido.

	![](./media/storsimple-ova-deploy3-fs-setup/image17.png)

1.  En la página **Configuración básica** haga lo siguiente:

    1.  Especifique un nombre exclusivo para el recurso compartido. El nombre debe ser una cadena que contenga entre 3 y 127 caracteres.

    2.  (Opcional) Proporcione una descripción para el recurso compartido. La descripción ayudará a identificar a los propietarios del recurso compartido.

    3.  Seleccione un tipo de uso para el recurso compartido. El tipo de uso puede ser **En capas** o **Anclado localmente**, donde el tipo en capas es el valor predeterminado. Para las cargas de trabajo que requieren garantías locales, latencias bajas y un rendimiento más alto, seleccione un recurso compartido **Anclado localmente**. Para todos los demás datos, seleccione un recurso compartido **En capas**.

	Un recurso compartido anclado localmente se aprovisiona de forma intensa y garantiza que los datos principales del recurso compartido continúen siendo locales en el dispositivo y que no se traspasan a la nube. Por otro lado, un recurso compartido en capas se aprovisiona de manera fina y se puede crear muy rápidamente. Cuando se crea un recurso compartido en capas, el 10 % del espacio se aprovisiona en la capa local y el 90 % del espacio se aprovisiona en la nube. Por ejemplo, si se aprovisiona un volumen de 1 TB, 100 GB residirían en el espacio local y 900 GB se utilizarían en la nube cuando se apilen los datos. A su vez, esto hace que, si agota todo el espacio local en el dispositivo, no se puede aprovisionar un recurso compartido en capas.

1.  Especifique la capacidad aprovisionada para el recurso compartido. Tenga en cuenta que la capacidad especificada debe ser menor que la capacidad disponible. Si usa un recurso compartido en capas, el tamaño del recurso compartido debe estar entre 500 GB y 20 TB. Para un recurso compartido anclado localmente, especifique un tamaño de recurso compartido de 50 GB a 2 TB. Use la capacidad disponible como guía para aprovisionar un recurso compartido. Si la capacidad local disponible es de 0 GB, no podrá aprovisionar recursos compartidos locales o en capas.

	![](./media/storsimple-ova-deploy3-fs-setup/image18.png)

1.  Haga clic en el icono de flecha ![](./media/storsimple-ova-deploy3-fs-setup/image19.png) para ir a la página siguiente.

1.  En la página **Configuración adicional**, asigne los permisos al usuario o al grupo que tendrá acceso a este recurso compartido. Especifique el nombre del usuario o del grupo de usuarios en formato *<john@contoso.com>*. Se recomienda que utilice un grupo de usuarios (en lugar de un único usuario) para otorgar los privilegios de administrador para tener acceso a estos recursos compartidos. Después de haber asignado los permisos aquí, puede utilizar el Explorador de Windows para modificar estos permisos.

	![](./media/storsimple-ova-deploy3-fs-setup/image20.png)

1.  Haga clic en el icono de marca de verificación ![](./media/storsimple-ova-deploy3-fs-setup/image21.png). Se creará un recurso compartido con la configuración especificada. De forma predeterminada, se habilitarán la supervisión y la copia de seguridad para el recurso compartido.

## Paso 4: Conectarse al recurso compartido

Ahora, necesitará conectarse a los recursos compartidos que creó en el paso anterior. Siga estos pasos en el host de Windows Server.

#### Para conectarse al recurso compartido

1.  Presione ![](./media/storsimple-ova-deploy3-fs-setup/image22.png) + R. En la ventana Ejecutar, especifique el *\<file server name>* como la ruta de acceso, reemplace *nombre del servidor de archivos* con el nombre del dispositivo que asignó a su servidor de archivos. Haga clic en **OK**.

	![](./media/storsimple-ova-deploy3-fs-setup/image23.png)

2.  Se abrirá el Explorador. Ahora podrá ver los recursos compartidos que creó como carpetas. Seleccione y haga doble clic en un recurso compartido (carpeta) para ver el contenido.

	![](./media/storsimple-ova-deploy3-fs-setup/image24.png)

3.  Ahora puede agregar archivos a estos recursos compartidos y realizar una copia de seguridad.

![icono de vídeo](./media/storsimple-ova-deploy3-fs-setup/video_icon.png) **Vídeo disponible**

Mire el vídeo para ver cómo puede configurar y registrar una matriz virtual de StorSimple como servidor de archivos.

> [AZURE.VIDEO configure-a-storsimple-virtual-array]

## Pasos siguientes

Obtenga más información acerca de usar la interfaz de usuario web local para [administrar la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0302_2016-->