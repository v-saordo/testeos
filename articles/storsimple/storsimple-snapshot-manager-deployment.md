<properties 
   pageTitle="Implementación de Administrador de instantáneas StorSimple | Microsoft Azure"
   description="Obtenga información acerca de cómo descargar e instalar el Administrador de instantáneas StorSimple, un complemento de MMC para administrar las características de copia de seguridad y protección de datos de StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/07/2016"
   ms.author="v-sharos" />

# Implementar el complemento MMC del Administrador de instantáneas StorSimple

## Información general

Administrador de instantáneas StorSimple es un complemento de Microsoft Management Console (MMC) que simplifica la protección de datos y las administración de copias de seguridad en un entorno de Microsoft Azure StorSimple. Con Administrador de instantáneas StorSimple, puede administrar los datos de Microsoft Azure StorSimple de forma local y en la nube como un sistema de almacenamiento integrado y, por consiguiente, simplificar los procesos de copia de seguridad y restauración, así como reducir los costos.

En este tutorial se describen los requisitos de configuración, así como los procedimientos para instalar, quitar y actualizar Administrador de instantáneas StorSimple.

>[AZURE.NOTE]
>
>- No puede utilizar StorSimple Snapshot Manager para administrar las matrices virtuales de Microsoft Azure StorSimple (también conocidas como dispositivos virtuales locales de StorSimple).
>
>- Si tiene previsto instalar la actualización 2 de StorSimple en el dispositivo de StorSimple, asegúrese de descargar la última versión de StorSimple Snapshot Manager e instalarlo **antes de instalar la actualización 2 de StorSimple**. La última versión de StorSimple Snapshot Manager es compatible con versiones anteriores y funciona con todas las versiones publicadas de Microsoft Azure StorSimple. Si usa la versión anterior de StorSimple Snapshot Manager, debe actualizarlo (no es necesario desinstalar la versión anterior antes de instalar la nueva versión).

## Instalación de Administrador de instantáneas StorSimple

Administrador de instantáneas StorSimple puede instalarse en equipos que ejecutan el sistema operativo Windows Server 2008 R2 SP1, Windows Server 2012 o Windows Server 2012 R2 En servidores que ejecutan Windows 2008 R2, también debe instalar Windows Server 2008 SP1 y Windows Management Framework 3.0.

Antes de instalar o actualizar el complemento Administrador de instantáneas StorSimple de Microsoft Management Console (MMC), asegúrese de que el servidor host y el dispositivo Microsoft Azure StorSimple están configurados correctamente.

## Configuración de los requisitos previos

Los pasos siguientes proporcionan una descripción general de las tareas de configuración que debe completar antes de instalar Administrador de instantáneas de StorSimple. Para completar la configuración de Microsoft Azure StorSimple y la información de configuración, incluidos los requisitos del sistema y obtener instrucciones detalladas, consulte [Implementar el dispositivo StorSimple local](storsimple-deployment-walkthrough.md).

>[AZURE.IMPORTANT] Antes de comenzar, revise la [lista de comprobación de la configuración de implementación](storsimple-deployment-walkthrough.md#deployment-configuration-checklist)
> y los [requisitos previos de implementación](storsimple-deployment-walkthrough.md#deployment-prerequisites) en [Implementar el dispositivo StorSimple local](storsimple-deployment-walkthrough.md).
<br>
 
### Antes de instalar Administrador de instantáneas StorSimple

1. Desempaquete, monte y conecte el dispositivo de Microsoft Azure StorSimple, como se describe en[instalar el dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md)o[instalar el dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

2. Asegúrese de que el equipo host se ejecuta en uno de los siguientes sistemas operativos:

    - Windows Server 2008 R2 (en servidores que ejecutan Windows 2008 R2, también debe instalar Windows Server 2008 SP1 y Windows Management Framework 3.0)
    - Windows Server 2012
    - Windows Server 2012 R2
 
    Para un dispositivo StorSimple virtual, el host debe ser una máquina virtual de Microsoft Azure.

3. Asegúrese de que se cumplen todos los requisitos de configuración de Microsoft Azure StorSimple. Para obtener más información, vaya a [Requisitos previos de implementación](storsimple-deployment-walkthrough.md#deployment-prerequisites).

4. Conecte el dispositivo al host y realice la configuración inicial. Para obtener más información, vaya a[Pasos de implementación](storsimple-deployment-walkthrough.md#deployment-steps).

5. Cree volúmenes en el dispositivo, asígnelos al host y compruebe que el host puede montarlos y usarlos. Administrador de instantáneas StorSimple admite los siguientes tipos de volúmenes:

    - Volúmenes básicos
    - Volúmenes simples
    - Volúmenes dinámicos
    - Volúmenes dinámicos con reflejos (RAID 1)
    - Volúmenes compartidos de clúster
 
    Para obtener información acerca de cómo crear volúmenes en el dispositivo StorSimple o un dispositivo de StorSimple virtual, consulte [Paso 6: Crear un volumen](storsimple-deployment-walkthrough.md#step-6-create-a-volume) en [Implementar el dispositivo StorSimple local](storsimple-deployment-walkthrough.md).

## Instalación de un nuevo Administrador de instantáneas StorSimple

Antes de instalar Administrador de instantáneas StorSimple, asegúrese de que los volúmenes creados en el dispositivo StorSimple o un dispositivo StorSimple virtual se montan, inicializan y formatean según se describe en [Configuración de los requisitos previos](#configure-prerequisites).

>[AZURE.IMPORTANT]
>
>- Para un dispositivo StorSimple virtual, el host debe ser una máquina virtual de Microsoft Azure. 
>
>- El host debe estar ejecutando Windows 2008 R2, Windows Server 2012 o Windows Server 2012 R2. Si el servidor ejecuta Windows Server 2008 R2, también debe instalar Windows Server 2008 SP1 y Windows Management Framework 3.0.
>
>- Debe configurar una conexión iSCSI desde el host al dispositivo StorSimple antes de poder conectar el dispositivo a Administrador de instantáneas StorSimple.

Siga estos pasos para completar una instalación nueva de Administrador de instantáneas StorSimple. Si está instalando una actualización, vaya a [Actualización o reinstalación de Administrador de instantáneas StorSimple](#upgrade-or-reinstall-storsimple-snapshot-manager).

- Paso 1: Instalación de Administrador de instantáneas StorSimple 
- Paso 2: Conexión de Administrador de instantáneas StorSimple a un dispositivo 
- Paso 3: Comprobación de la conexión con el dispositivo 

###Paso 1: Instalación de Administrador de instantáneas StorSimple

Use los pasos siguientes para instalar Administrador de instantáneas StorSimple.

#### Para instalar Administrador de instantáneas StorSimple

1. Descargue el software Administrador de instantáneas StorSimple (vaya a [Administrador de instantáneas StorSimple](https://www.microsoft.com/download/details.aspx?id=44220) en el Centro de descarga de Microsoft) y guárdelo localmente en el host.

2. En el Explorador de archivos, haga clic en la carpeta comprimida y, a continuación, en **Extraer todo**.

3. En la ventana **Extraer carpetas comprimidas (en zip)**, en el cuadro **Seleccionar un destino y extraer archivos**, escriba o busque la ruta de acceso donde desea extraer el archivo.

       >[AZURE.IMPORTANT]Debe instalar Administrador de instantáneas StorSimple en la unidad C:.
 
4. Seleccione la casilla **Mostrar los archivos extraídos al completar** y después haga clic en **Extraer**.

    ![Extracción del cuadro de diálogo de archivos](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png)

4. Cuando finaliza la extracción, se abre la carpeta de destino. Haga doble clic en el icono de instalación de la aplicación que aparece en la carpeta de destino.

5. Cuando aparece el mensaje **La instalación se realizó correctamente**, haga clic en **Cerrar**. Debería ver el icono de Administrador de instantáneas StorSimple en su escritorio.

    ![icono del escritorio](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png)

### Paso 2: Conexión de Administrador de instantáneas StorSimple a un dispositivo

Siga estos pasos para conectar Administrador de instantáneas StorSimple a un dispositivo StorSimple.

#### Para conectar Administrador de instantáneas StorSimple a un dispositivo

1. Haga clic en el icono Administrador de instantáneas StorSimple en su escritorio. Aparecerá el cuadro de diálogo Administrador de instantáneas StorSimple. La ventana contiene un panel **Ámbito**, un panel **Resultados** y un panel **Acciones**. 

    ![Interfaz de usuario de Administrador de instantáneas StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)

    - El panel **Ámbito** (panel izquierdo) contiene una lista de nodos organizados en una estructura de árbol. Puede expandir algunos nodos para seleccionar una vista o datos específicos relacionados con ese nodo. Haga clic en el icono de flecha para expandir o contraer un nodo. Haga clic en un elemento en el panel **Ámbito** para ver una lista de acciones disponibles para ese elemento. 

    - El panel **Resultados** (panel central) contiene información de estado detallada sobre el nodo o la vista, o bien sobre los datos que ha seleccionado en el panel **Ámbito**.

    - El panel **Acciones** muestra las operaciones que puede realizar en el nodo o la vista, o bien en los datos que ha seleccionado en el panel **Ámbito**.

    Para obtener una descripción completa de la interfaz de usuario de Administrador de instantáneas StorSimple, consulte [Interfaz de usuario de Administrador de instantáneas StorSimple](storsimple-use-snapshot-manager.md).

2. En el panel **Ámbito**, haga clic con el botón derecho en el nodo **Dispositivos** y, a continuación, haga clic en **Configurar un dispositivo**. Aparece el cuadro de diálogo **Configurar un dispositivo**.

    ![Configuración de un dispositivo](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png)

3. En el cuadro de lista **Dispositivo**, seleccione la dirección IP del dispositivo de Microsoft Azure StorSimple o el dispositivo virtual. En la casilla **Contraseña**, escriba la contraseña de Administrador de instantáneas StorSimple que creó para el dispositivo en el Portal de Azure clásico. Haga clic en **Aceptar**.

4. Administrador de instantáneas StorSimple busca el dispositivo que ha identificado. Si el dispositivo está disponible, Administrador de instantáneas StorSimple agrega una conexión. También puede [comprobar la conexión al dispositivo](#to-verify-the-connection) para confirmar que la conexión se agregó correctamente.

    Si el dispositivo no está disponible por alguna razón, Administrador de instantáneas StorSimple devuelve un mensaje de error. Haga clic en **Aceptar** para cerrar el mensaje de error y, a continuación, haga clic en **Cancelar** para cerrar el cuadro de diálogo **Configurar un dispositivo**.

5. Cuando se conecta a un dispositivo, Administrador de instantáneas StorSimple importa cada grupo de volúmenes configurado para ese dispositivo, siempre que los grupos de volumen tengan asociadas copias de seguridad. Los grupos de volúmenes que no tienen copias de seguridad asociadas no se importan. Además, las directivas de copia de seguridad que se crearon para un grupo de volúmenes no se importan. Para ver los grupos importados, haga clic en la parte superior del nodo **Grupos de volúmenes** en el panel **Ámbito** y haga clic en**Alternar grupos importados**.

### Paso 3: Comprobación de la conexión con el dispositivo

Use los pasos siguientes para comprobar que Administrador de instantáneas StorSimple está conectado al dispositivo StorSimple.

#### Para comprobar la conexión

1. En el panel **Ámbito**, haga clic en el nodo **Dispositivos**.

    ![Estado del dispositivo de Administrador de instantáneas StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png)

2. Compruebe el panel **Resultados**:

   - Si aparece un indicador verde en el icono de dispositivo y **Disponible** en la columna **Estado**, el dispositivo está conectado. 

   - Si aparece un indicador rojo en el icono de dispositivo y No disponible en la columna **Estado**, el dispositivo no está conectado.

   - Si aparece **Actualizar** en la columna **Estado** y, a continuación, Administrador de instantáneas StorSimple está recuperando grupos de volúmenes y copias de seguridad asociadas para un dispositivo conectado.

## Actualización o reinstalación de Administrador de instantáneas StorSimple

Debe desinstalar Administrador de instantáneas StorSimple completamente antes de actualizar o reinstalar el software.

Antes de reinstalar Administrador de instantáneas StorSimple, realice una copia de seguridad de la base de datos de Administrador de instantáneas StorSimple existente en el equipo host. Esto guarda la información de directivas y la configuración de copia de seguridad para que pueda restaurar fácilmente estos datos de la copia de seguridad.

Si va a actualizar o reinstalar Administrador de instantáneas StorSimple, siga estos pasos:

- Paso 1: Desinstalación de Administrador de instantáneas StorSimple 
- Paso 2: Copia de seguridad de la base de datos de Administrador de instantáneas StorSimple 
- Paso 3: Instalación de Administrador de instantáneas StorSimple y restauración de la base de datos 

### Paso 1: Desinstalación de Administrador de instantáneas StorSimple

Use los pasos siguientes para desinstalar Administrador de instantáneas StorSimple.

#### Para desinstalar Administrador de instantáneas StorSimple

1. En el equipo host, abra el **Panel de control**, haga clic en**Programas** y, a continuación, en**Programas y características**.

2. En el panel izquierdo, haga clic en **Desinstalar o cambiar un programa**.

3. Haga clic en **Administrador de instantáneas StorSimple** y, a continuación, haga clic en **Desinstalar**.

4. Esto inicia el programa de instalación de Administrador de instantáneas StorSimple. Haga clic en**Modificar instalación** y, a continuación, haga clic en **Desinstalar**.

    >[AZURE.NOTE]Si hay algún proceso de MMC en ejecución en segundo plano, como Administrador de instantáneas StorSimple o Administración de discos, la desinstalación fallará y recibirá un mensaje para cerrar todas las instancias de MMC antes de intentar desinstalar el programa. Seleccione **Cerrar las aplicaciones automáticamente e intentar reiniciarlas después de que la instalación se complete** y, a continuación, haga clic en **Aceptar**.
 
5. Cuando finalice el proceso de desinstalación, aparece un mensaje **La instalación se realizó correctamente**. Haga clic en **Cerrar**.

### Paso 2: Copia de seguridad de la base de datos de Administrador de instantáneas StorSimple

Use los pasos siguientes para crear y guardar una copia de la base de datos de Administrador de instantáneas StorSimple.

#### Para realizar una copia de seguridad de la base de datos

1. Detenga el servicio de administración de Microsoft StorSimple:

   1. Inicie el Administrador del servidor.

   2. En el panel Administrador del servidor, en el menú **Herramientas**, seleccione **Servicios**.

   3. En la página **Servicios**, seleccione **Servicio de administración de Microsoft StorSimple**.

   4. En el panel derecho, en **Servicio de administración de Microsoft StorSimple**, haga clic en **Detener el servicio**.

        ![Stop the StorSimple Manager service](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)

2. Vaya a C:\ProgramData\Microsoft\StorSimple\BACatalog.

    >[AZURE.NOTE]ProgramData es una carpeta oculta.

3. Busque el archivo XML de catálogo, copie el archivo y almacene la copia en una ubicación segura o en la nube.

    ![Archivo de catálogo de copia de seguridad de StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)

4. Reinicie el servicio de administración de Microsoft StorSimple:

    1. En el panel Administrador del servidor, en el menú **Herramientas**, seleccione **Servicios**.

    2. En la página **Servicios**, seleccione **Servicio de administración de Microsoft StorSimple**.

    3. En el panel derecho, en **Servicio de administración de Microsoft StorSimple**, haga clic en **Reiniciar el servicio**.

### Paso 3: Instalación de Administrador de instantáneas StorSimple y restauración de la base de datos

Para reinstalar Administrador de instantáneas StorSimple, siga los pasos descritos en [Instalación de un nuevo Administrador de instantáneas StorSimple](#install-a-new-storsimple-snapshot-manager). A continuación, use el siguiente procedimiento para restaurar la base de datos de Administrador de instantáneas StorSimple.

#### Para restaurar la base de datos

1. Detenga el servicio de administración de Microsoft StorSimple:

    1. Inicie el Administrador del servidor.

    2. En el panel Administrador del servidor, en el menú **Herramientas**, seleccione **Servicios**.

    3. En la página **Servicios**, seleccione **Servicio de administración de Microsoft StorSimple**.

    4. En el panel derecho, en **Servicio de administración de Microsoft StorSimple**, haga clic en **Detener el servicio**.

2. Vaya a C:\ProgramData\Microsoft\StorSimple\BACatalog.

     >[AZURE.NOTE]ProgramData es una carpeta oculta.

3. Elimine el archivo XML de catálogo y reemplácelo por la versión que guardó anteriormente.

4. Reinicie el servicio de administración de Microsoft StorSimple:

    1. En el panel Administrador del servidor, en el menú **Herramientas**, seleccione **Servicios**.

    2. En la página **Servicios**, seleccione **Servicio de administración de Microsoft StorSimple**.

    3. En el panel derecho, en **Servicio de administración de Microsoft StorSimple**, haga clic en **Reiniciar el servicio**.

## Pasos siguientes

- Para obtener más información sobre Snapshot Manager de StorSimple, vaya a [¿Qué es Snapshot Manager de StorSimple?](storsimple-what-is-snapshot-manager.md)

- Para obtener más información acerca de la interfaz de usuario de Snapshot Manager de StorSimple, vaya a [Interfaz de usuario de Snapshot Manager de StorSimple](storsimple-use-snapshot-manager.md).

- Para obtener más información acerca de Snapshot Manager de StorSimple, vaya a [Use Snapshot Manager de StorSimple para administrar la solución de StorSimple](storsimple-snapshot-manager-admin.md).

<!---HONumber=AcomDC_0114_2016-->