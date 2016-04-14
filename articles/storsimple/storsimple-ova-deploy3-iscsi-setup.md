<properties 
   pageTitle="Configuración del servidor iSCSI de matrices virtuales de StorSimple | Microsoft Azure"
   description="Describe cómo realizar la configuración inicial, registrar el servidor iSCSI de StorSimple y completar la configuración del dispositivo."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="03/01/2016"
   ms.author="alkohli" />


# Implementar una matriz virtual de StorSimple: Configurar el dispositivo virtual como servidor iSCSI

![flujo del proceso de configuración de iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/iscsi4.png)

## Información general

Este tutorial de implementación se aplica a la matriz virtual de Microsoft Azure StorSimple (también conocida como dispositivo virtual local de StorSimple o dispositivo virtual de StorSimple) que ejecuta la versión de disponibilidad general de marzo de 2016. En este tutorial se describe cómo realizar la configuración inicial, registrar el servidor iSCSI de StorSimple, completar la configuración del dispositivo y, a continuación, crear, montar, inicializar y formatear volúmenes en el servidor iSCSI del dispositivo virtual StorSimple. La información de instalación de StorSimple de este artículo solo se aplica a las matrices virtual de StorSimple.

Los procedimientos descritos aquí demoran aproximadamente de 30 minutos a 1 hora en completarse. La información publicada en este artículo solo se aplica a las matrices virtual de StorSimple.

## Requisitos previos de instalación

Antes de instalar y configurar el dispositivo virtual StorSimple, asegúrese de que:

- Ha aprovisionado un dispositivo virtual y se ha conectado a él según se describe en la sección [Implementar una matriz virtual de StorSimple: Aprovisionar una matriz virtual en Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) o [Implementar una matriz virtual de StorSimple: Aprovisionar una matriz virtual en VMware](storsimple-ova-deploy2-provision-vmware.md).

- Tiene la clave de registro del servicio de StorSimple Manager que creó para administrar dispositivos virtuales StorSimple. Para más información, consulte **Paso 2: Obtener la clave de registro del servicio** en [Implementar una matriz virtual de StorSimple: Preparar el portal](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).

- Si este es el segundo dispositivo virtual o subsiguiente que registra en un servicio de StorSimple Manager existente, debe tener la clave de cifrado de datos del servicio. Esta clave se generó cuando el primer dispositivo se registró correctamente en este servicio. Si perdió esta clave, consulte la sección **Obtener la clave de cifrado de datos del servicio** en [Utilizar la interfaz de usuario web para administrar la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## Configuración paso a paso 

Use las siguientes instrucciones paso a paso para instalar y configurar el dispositivo virtual StorSimple:

-  [Paso 1: Completar la configuración de la interfaz de usuario web local y registrar el dispositivo](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
-  [Paso 2: Completar la instalación de dispositivos necesarios](#step-2-complete-the-required-device-setup)
-  [Paso 3: Agregar un volumen](#step-3-add-a-volume)
-  [Paso 4: Montar, inicializar y formatear un volumen](#step-4-mount-initialize-and-format-a-volume)  

## Paso 1: Completar la configuración de la interfaz de usuario web local y registrar el dispositivo 

#### Para completar la instalación y registrar el dispositivo

1. Abra una ventana del explorador y, para conectarse a la interfaz de usuario de web, escriba:

    `https://<ip-address of network interface>`

    Use la dirección URL de conexión que anotó en el paso anterior. Verá un error que le indica que hay un problema con el certificado de seguridad del sitio web. Haga clic en **Continue to this web page** (Continuar a esta página web).

    ![error de certificado de seguridad](./media/storsimple-ova-deploy3-iscsi-setup/image3.png)

2. Inicie sesión en la interfaz de usuario web del dispositivo virtual como **StorSimpleAdmin**. Escriba la contraseña del administrador de dispositivos que cambió en Paso 3: Iniciar el dispositivo virtual en [Implementar una matriz virtual de StorSimple: Aprovisionar un dispositivo virtual en Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) o en [Implementar una matriz virtual de StorSimple: Aprovisionar un dispositivo virtual en VMware](storsimple-ova-deploy2-provision-vmware.md).

    ![Página de inicio de sesión](./media/storsimple-ova-deploy3-iscsi-setup/image4.png)

3. Lo llevará a la página de **inicio**. En esta página se describen los distintos parámetros necesarios para configurar y registrar el dispositivo virtual en el servicio StorSimple Manager. Tenga en cuenta que las opciones **Network settings** (Configuración de red), **Web proxy settings** (Configuración de proxy web) y **Time settings** (Configuración de hora) son opcionales. Los únicos parámetros obligatorios son **Device settings** (Configuración de dispositivo) y **Cloud settings** (Configuración de la nube).

    ![Página de inicio](./media/storsimple-ova-deploy3-iscsi-setup/image5.png)

4. En la página **Network settings** (Configuración de red) en **Network interfaces** (Interfaces de red), DATA 0 se configurará automáticamente para usted. Cada interfaz de red se establece de forma predeterminada para obtener la dirección IP automáticamente (DHCP). Por lo tanto, una dirección IP, una subred y una puerta de enlace se asignarán automáticamente (tanto para IPv4 como para IPv6).

    Mientras planea implementar el dispositivo como servidor iSCSI (para aprovisionar el almacenamiento en bloque), se recomienda que deshabilite la opción **Get IP address automatically** (Obtener una dirección IP automáticamente) y configure direcciones IP estáticas.

    ![Página de configuración de red](./media/storsimple-ova-deploy3-iscsi-setup/image6.png)

    Si agrega más de una interfaz de red durante el aprovisionamiento del dispositivo, se pueden configurar aquí. Tenga en cuenta que puede configurar la interfaz de red como IPv4 únicamente o como IPv4 e IPv6. No se admiten las configuraciones de solo IPv6.

5. Los servidores DNS son necesarios porque se utilizan cuando el dispositivo intenta comunicarse con sus proveedores de servicios de almacenamiento en la nube o para resolver el dispositivo por nombre si se configura como servidor de archivos. En la página **Network settings** (Configuración de red) en **DNS servers** (Servidores DNS):

    1. Se configurarán automáticamente un servidor DNS principal y secundario. Si decide configurar direcciones IP estáticas, puede especificar servidores DNS. Para lograr la alta disponibilidad, se recomienda que configure un servidor DNS principal y uno secundario.

    2. Haga clic en **Apply**. Esto aplicará y validará la configuración de red.

6. En la página **Device settings** (Configuración de dispositivo):

    1. Asigne un **nombre** exclusivo al dispositivo. Este nombre puede tener de 1 a 15 caracteres y puede contener letras, números y guiones.

    2. Haga clic en el icono de **servidor iSCSI** ![icono de servidor iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image7.png) para el **tipo** del dispositivo que está creando. Un servidor iSCSI le permitirá aprovisionar el almacenamiento en bloque.

    3. Especifique si desea que este dispositivo esté unido al dominio. Si el dispositivo es un servidor iSCSI, la unión al dominio es opcional. Si decide no unir el servidor iSCSI a un dominio, haga clic en **Apply** (Aplicar), espere a que la configuración se aplique y, a continuación, vaya al paso siguiente.

        Si desea unir el dispositivo a un dominio. Escriba el **nombre de dominio** (se muestra a continuación).

    4. Haga clic en **Apply**.

    5. Aparece un cuadro de diálogo. Escriba las credenciales del dominio en el formato especificado. Haga clic en el icono de marca de verificación ![icono de marca de verificación](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Se comprobarán las credenciales del dominio. Verá un mensaje de error si las credenciales son incorrectas.

        ![credentials](./media/storsimple-ova-deploy3-iscsi-setup/image8.png)
        
           > [AZURE.NOTE]
	   > 
	   > Si une el servidor iSCSI a un dominio, asegúrese de que su matriz virtual esté en su propia unidad organizativa (UO) de Microsoft Azure Active Directory y de que no se le aplica ningún objeto de directiva de grupo (GPO).
	   

    6. Haga clic en **Apply**. Esto aplicará y validará la configuración de dispositivo.
 
7. Configure el servidor proxy web (de manera opcional). Aunque la configuración del proxy web es opcional, tenga en cuenta que, si usa un proxy web, solo puede configurarlo aquí.

    ![configurar el proxy web](./media/storsimple-ova-deploy3-iscsi-setup/image9.png)

    En la página **Web proxy** (Proxy web):

    1. Proporcione la **dirección URL de proxy web** en este formato: *http://host-IP dirección* o *nombre de dominio completo: Número de puerto*. Tenga en cuenta que no se admiten direcciones URL HTTPS.

    2. Especifique **Authentication** (Autenticación) como **Basic** (Básica), **NTLM** o **None** (Ninguna).

    3. Si utiliza autenticación, también debe proporcionar un **nombre de usuario** y una **contraseña**.

    4. Haga clic en **Apply**. Esto validará y aplicará los parámetros de proxy web configurados.
 
8. (Opcionalmente) configure las opciones de hora para el dispositivo, como la zona horaria y los servidores NTP principal y secundario. Se requieren servidores NTP, ya que el dispositivo debe sincronizar la hora para que pueda autenticarse con los proveedores de servicios en la nube.

    ![Configuración de hora](./media/storsimple-ova-deploy3-iscsi-setup/image10.png)

    En la página **Time settings** (Configuración de hora):

    1. De la lista desplegable, seleccione la **zona horaria** según la ubicación geográfica en la que se va a implementar el dispositivo. La zona horaria predeterminada para el dispositivo es la hora del Pacífico (PST). El dispositivo usará esta zona horaria para todas las operaciones programadas.

    2. Especifique un **servidor NTP principal** para el dispositivo o acepte el valor predeterminado de time.windows.com. Asegúrese de que su red permite que el tráfico NTP pase del centro de datos a Internet.

    3. Opcionalmente, especifique un **servidor NTP secundario** para el dispositivo.

    4. Haga clic en **Apply**. Esto validará y aplicará los parámetros de hora configurados.

9. Configure las opciones de nube para el dispositivo. En este paso, completa la configuración del dispositivo local y, a continuación, registra el dispositivo en el servicio StorSimple Manager.

    1. Introduzca la **clave de registro del servicio** que obtuvo en **Paso 2: Obtener la clave de registro del servicio** en [Implementar una matriz virtual de StorSimple: Preparar el portal](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).

    2. Si no es el primer dispositivo que va a registrar en este servicio, debe proporcionar la **clave de cifrado de datos del servicio**. Esta clave se necesita junto con la clave de registro del servicio para registrar dispositivos adicionales en el servicio StorSimple Manager. Para más información, consulte cómo obtener la [clave de cifrado de datos del servicio](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) en la interfaz de usuario web local.

    3. Haga clic en **Registrar**. Se reiniciará el dispositivo. Debe esperar de 2 a 3 minutos antes de que el dispositivo se registre correctamente. Una vez que se haya reiniciado el dispositivo, irá a la página de inicio de sesión.

       ![Registrar dispositivo](./media/storsimple-ova-deploy3-iscsi-setup/image11.png)

10. Vuelva al Portal de Azure clásico. En la página **Dispositivos**, compruebe que el dispositivo se conectó correctamente al servicio consultando el estado. El estado del dispositivo debe ser **Activo**.

    ![Página de dispositivos](./media/storsimple-ova-deploy3-iscsi-setup/image12.png)

## Paso 2: Completar la instalación de dispositivos necesarios

Para completar a la configuración del dispositivo StorSimple, debe hacer lo siguiente:

- Seleccione una cuenta de almacenamiento para asociarla a este dispositivo.

- Elija la configuración de cifrado para los datos que se envían a la nube.

Siga estos pasos en el Portal de Azure clásico para completar la configuración mínima del dispositivo.

#### Para completar la configuración mínima del dispositivo

1. En la página **Devices** (Dispositivos), seleccione el dispositivo que acaba de crear. Este dispositivo se debe mostrar como **Active** (Activo). Haga clic en la flecha situada junto al nombre del dispositivo y, a continuación, haga clic en **Quick Start** (Inicio rápido).

    ![Página de dispositivos](./media/storsimple-ova-deploy3-iscsi-setup/image13.png)

2. Haga clic en **complete device setup** (Completar configuración del dispositivo) para iniciar el Asistente para configurar dispositivos.

    ![Asistente para configurar dispositivos](./media/storsimple-ova-deploy3-iscsi-setup/image14.png)

3. En la página **Basic Settings** (Configuración básica) del Asistente para configurar dispositivos, haga lo siguiente:

   1. Especifique una cuenta de almacenamiento para usarla con el dispositivo. En esta suscripción, puede seleccionar una cuenta de almacenamiento existente de la lista desplegable o puede especificar **Add more** (Agregar más) para elegir una cuenta de una suscripción diferente.

   2. Defina la configuración de cifrado para todos los datos en reposo que se enviarán a la nube. (StorSimple usa el cifrado AES-256). Para cifrar los datos, active la casilla **Enable cloud storage encryption** (Habilitar cifrado de almacenamiento en la nube). Escriba un cifrado de almacenamiento en la nube que contenga 32 caracteres. Vuelva a escribir la clave para confirmarla.

   3. Haga clic en el icono de marca de verificación ![icono de marca de verificación](./media/storsimple-ova-deploy3-iscsi-setup/image15.png).

    ![Configuración básica](./media/storsimple-ova-deploy3-iscsi-setup/image16.png)

    Ahora se actualizará la configuración. Después de actualizar la configuración correctamente, el botón para completar la configuración del dispositivo no estará disponible. Volverá a la página **Inicio rápido** del dispositivo.

>[AZURE.NOTE]Puede modificar la configuración restante del dispositivo en cualquier momento accediendo a la página **Configurar**.

## Paso 3: Agregar un volumen

Siga estos pasos en el Portal de Azure clásico para crear un volumen.

#### Para crear un volumen

1. En la página **Inicio rápido** del dispositivo, haga clic en **Agregar un volumen**. Se iniciará el Asistente para agregar volúmenes.

2. En el Asistente para agregar volúmenes, en **Configuración básica**, haga lo siguiente:

    1. Proporcione un nombre exclusivo para el volumen. El nombre debe ser una cadena que contenga entre 3 y 127 caracteres.

    2. Proporcione una descripción para el volumen. La descripción ayudará a identificar a los propietarios del volumen.

    3. Seleccione un tipo de uso para el volumen. El tipo de uso puede ser **Tiered volume** (Volumen en capas) o **Locally pinned volume** (Volumen anclado localmente). (**Tiered volume** es el valor predeterminado). Para las cargas de trabajo que requieren garantías locales, latencias bajas y un rendimiento más alto, seleccione **Locally pinned** **volume** (Volumen anclado localmente). Para todos los demás datos, seleccione **Tiered** **volume** (Volumen en capas).

        Un volumen anclado localmente se aprovisiona de forma intensa y garantiza que los datos principales del volumen se mantengan en el dispositivo y que no se traspasan a la nube. Si crea un volumen anclado localmente, el dispositivo comprobará el espacio disponible en las capas locales para aprovisionar un volumen del tamaño solicitado. Crear un volumen anclado localmente puede implicar traspasar los datos existentes del dispositivo a la nube, por lo que la creación del volumen puede llevar mucho tiempo. El tiempo total depende del tamaño del volumen aprovisionado, el ancho de banda de red disponible y los datos en el dispositivo.

        Por otro lado, un volumen en capas se aprovisiona de manera fina y se puede crear muy rápidamente. Cuando se crea un volumen en capas, el 10 % del espacio se aprovisiona en la capa local y el 90 % del espacio se aprovisiona en la nube. Por ejemplo, si se aprovisiona un volumen de 1 TB, 100 GB residirían en el espacio local y 900 GB se utilizarían en la nube cuando se apilen los datos. A su vez, esto hace que, si agota todo el espacio local en el dispositivo, no se puede aprovisionar un recurso compartido en capas (porque el 10 % no estará disponible).

    4. Especifique la capacidad aprovisionada para el volumen. Tenga en cuenta que la capacidad especificada debe ser menor que la capacidad disponible. Si va a crear un volumen en capas, el tamaño debe estar entre 500 GB y 20 TB. Para un volumen anclado localmente, especifique un tamaño de volumen de 50 GB a 2 TB. Use la capacidad disponible como guía para aprovisionar un volumen. Si la capacidad local disponible es de 0 GB, no podrá aprovisionar un volumen anclado localmente o en capas.

        ![Configuración básica](./media/storsimple-ova-deploy3-iscsi-setup/image17.png)

    5. Haga clic en el icono de flecha ![icono de flecha](./media/storsimple-ova-deploy3-iscsi-setup/image18.png) para ir a la página siguiente.

3. En la página **Configuración adicional**, agregue un nuevo registro de control de acceso (ACR):

    1. Proporcione un **Nombre** para el ACR.

    2. En **Nombre del iniciador iSCSI**, proporcione el nombre completo del iSCSI (IQN) del host de Windows. Si no tiene el IQN, vaya a [Apéndice A: Obtener el IQN de un host de Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).

    3. Le recomendamos que, para habilitar una copia de seguridad predeterminada, active la casilla **Habilitar una copia de seguridad predeterminada para este volumen**. La copia de seguridad predeterminada creará una directiva que se ejecuta a las 22:30 cada día (hora del dispositivo) y crea una instantánea en la nube de este volumen.

        ![configuración adicional](./media/storsimple-ova-deploy3-iscsi-setup/image19.png)

    4. Haga clic en el icono de marca de verificación ![icono de marca de verificación](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Esto inicia el trabajo de creación del volumen. Verá un mensaje de progreso similar al siguiente.

        ![mensaje de progreso](./media/storsimple-ova-deploy3-iscsi-setup/image20.png)

        Se creará un volumen con la configuración especificada. De forma predeterminada, se habilitarán la supervisión y la copia de seguridad para el volumen.

    5. Para confirmar que el volumen se creó correctamente, vaya a la página **Volumes** (Volúmenes). Debería ver el volumen mostrado.

        ![](./media/storsimple-ova-deploy3-iscsi-setup/image21.png)

## Paso 4: Montar, inicializar y formatear un volumen

Realice los pasos siguientes para montar, inicializar y formatear los volúmenes StorSimple en un host de Windows Server.

#### Para montar, inicializar y formatear un volumen

1. Inicie el iniciador iSCSI de Microsoft.

2. En la ventana **Propiedades del iniciador iSCSI**, en la pestaña **Detección**, haga clic en **Detectar portal**.

    ![detectar portal](./media/storsimple-ova-deploy3-iscsi-setup/image22.png)

3. En el cuadro de diálogo **Detectar portal de destino**, proporcione la dirección IP de la interfaz de red habilitada para iSCSI y, a continuación, haga clic en **Aceptar**.

    ![Dirección IP](./media/storsimple-ova-deploy3-iscsi-setup/image23.png)

4. En la ventana **Propiedades del iniciador iSCSI**, en la pestaña **Destinos**, busque los **Destinos detectados**. (Cada volumen será un destino detectado). El estado del dispositivo debe aparecer como **Inactivo**.

    ![destinos detectados](./media/storsimple-ova-deploy3-iscsi-setup/image24.png)

5. Seleccione un dispositivo de destino y, a continuación, haga clic en **Connect** (Conectar). Después de conectar el dispositivo, el estado debería cambiar a **Conectado**. (Para más información sobre el uso del iniciador iSCSI de Microsoft, consulte [Instalar y configurar el iniciador iSCSI de Microsoft][1]).

    ![seleccionar dispositivo de destino](./media/storsimple-ova-deploy3-iscsi-setup/image25.png)

6. En el host de Windows, presione la tecla del logotipo de Windows + X y, a continuación, haga clic en **Ejecutar**.

7. En el cuadro de diálogo **Ejecutar**, escriba **Diskmgmt.msc**. Haga clic en **Aceptar**, y aparecerá el cuadro de diálogo **Administración de discos**. El panel derecho mostrará los volúmenes del host.

8. En la ventana **Administración de discos**, aparecerán los volúmenes montados, tal como se muestra en la siguiente ilustración. Haga clic con el botón derecho en el volumen detectado (haga clic en el nombre del disco) y, a continuación, haga clic en **Conectado**.

    ![administración de discos](./media/storsimple-ova-deploy3-iscsi-setup/image26.png)

9. Haga clic con el botón derecho y seleccione **Initialize Disk** (Inicializar disco).

    ![inicializar disco 1](./media/storsimple-ova-deploy3-iscsi-setup/image27.png)

10. En el cuadro de diálogo, seleccione los discos que desea inicializar y, a continuación, haga clic en **OK** (Aceptar).

    ![inicializar disco 2](./media/storsimple-ova-deploy3-iscsi-setup/image28.png)

11. Se inicia el Asistente para nuevo volumen simple. Seleccione un tamaño de disco y, a continuación, haga clic en **Next** (Siguiente).

    ![asistente para nuevo volumen 1](./media/storsimple-ova-deploy3-iscsi-setup/image29.png)

12. Asigne una letra de unidad al volumen y, a continuación, haga clic en **Next** (Siguiente).

    ![asistente para nuevo volumen 2](./media/storsimple-ova-deploy3-iscsi-setup/image30.png)

13. Especifique los parámetros para formatear el volumen. **En Windows Server, solo se admite NTFS.** Establezca el AUS en 64K. Proporcione una etiqueta para el volumen. Es una práctica recomendada que este nombre sea idéntico al nombre de volumen proporcionado en el dispositivo virtual StorSimple. Haga clic en **Siguiente**.

    ![asistente para nuevo volumen 3](./media/storsimple-ova-deploy3-iscsi-setup/image31.png)

14. Compruebe los valores para el volumen y, a continuación, haga clic en **Finish** (Finalizar).

    ![asistente para nuevo volumen 4](./media/storsimple-ova-deploy3-iscsi-setup/image32.png)

    Los volúmenes aparecerán como **Online** (En línea) en la página **Disk Management** (Administración de discos).

    ![volúmenes en línea](./media/storsimple-ova-deploy3-iscsi-setup/image33.png)

## Pasos siguientes

Aprenda a usar la interfaz de usuario web local para [administrar la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md).

## Apéndice A: Obtener el IQN de un host de Windows Server

Siga estos pasos para obtener el nombre completo del iSCSI (IQN) de un host de Windows que ejecute Windows Server 2012.

#### Para obtener el IQN de un host de Windows

1. Inicie el iniciador iSCSI de Microsoft en el host de Windows.

2. En la ventana **Propiedades del iniciador iSCSI**, en la pestaña **Configuración**, seleccione y copie la cadena desde el campo **Nombre de iniciador**.

    ![Propiedades del iniciador iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image34.png)

2. Guarde esta cadena.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx

<!---HONumber=AcomDC_0302_2016-->