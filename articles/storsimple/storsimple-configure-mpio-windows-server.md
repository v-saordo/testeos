<properties 
   pageTitle="Configuración de MPIO para su dispositivo StorSimple | Microsoft Azure"
   description="Describe cómo configurar E/S de múltiples rutas (MPIO) para el dispositivo StorSimple conectado a un host que ejecuta Windows Server 2012 R2."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/05/2016"
   ms.author="alkohli" />

# Configurar E/S de múltiples rutas para el dispositivo StorSimple

Microsoft incorporó a Windows Server la compatibilidad con la característica de E/S de múltiples rutas (MPIO) para que sea más fácil crear configuraciones de SAN de alta disponibilidad y tolerancia a errores. MPIO usa componentes de ruta de acceso físicas redundantes (es decir, adaptadores, cables y conmutadores) para crear rutas de acceso lógicas entre el servidor y el dispositivo de almacenamiento. Si se produce un error de componente, lo que haría que la ruta de acceso lógica no funcionara, la lógica de múltiples rutas usa una ruta alternativa para E/S a fin de que las aplicaciones puedan seguir teniendo acceso a los datos. Además, dependiendo de la configuración de MPIO, puede mejorar el rendimiento al volver a equilibrar la carga entre estas rutas de acceso. Para más información, consulte [Introducción a E/S de múltiples rutas](https://technet.microsoft.com/library/cc725907.aspx "Información general y características de MPIO").

Para conseguir una alta disponibilidad de la solución StorSimple, es necesario que MPIO esté configurado en el dispositivo StorSimple. Cuando MPIO se instala en los servidores host que ejecutan Windows Server 2012 R2, dichos servidores tendrán tolerancia a errores de vínculo, red e interfaz.

MPIO es una característica opcional en Windows Server y no se instala de forma predeterminada. Se debe instalar como una característica a través del Administrador del servidor. En este tema se describen los pasos que hay que realizar para instalar y usar la característica MPIO en un host que ejecuta Windows Server 2012 R2 y que está conectado a un dispositivo físico StorSimple.

>[AZURE.NOTE] **Los dispositivos virtuales StorSimple no admiten MPIO.**

Realice estos pasos para configurar MPIO en su dispositivo StorSimple:

- Paso 1: instalar MPIO en el host de Windows Server

- Paso 2: configurar MPIO para volúmenes de StorSimple

- Paso 3: montar los volúmenes de StorSimple en el host

- Paso 4: configurar MPIO para alta disponibilidad y equilibrio de carga

En las siguientes secciones se detallan cada uno de estos pasos.

## Paso 1: instalar MPIO en el host de Windows Server

Haga lo siguiente para instalar esta característica en el host de Windows Server.

#### Para instalar MPIO en el host

1. Abra el Administrador del servidor en el host de Windows Server. El Administrador del servidor se inicia de forma predeterminada cuando un miembro del grupo Administradores inicia sesión en un equipo que ejecuta Windows Server 2012 R2 o Windows Server 2012. Si el Administrador del servidor no está abierto aún, haga clic en **Inicio > Administrador del servidor**. ![Administrador del servidor](./media/storsimple-configure-mpio-windows-server/IC740997.png)
2. Haga clic en **Administrador del servidor > Panel > Agregar roles y características**. De esta forma, se abre el **Asistente para agregar roles y características**. ![Asistente para agregar roles y características 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. En el **Asistente para agregar roles y características**, haga lo siguiente:

	- En la página **Antes de comenzar**, haga clic en **Siguiente**.
	- En la página **Seleccionar el tipo de instalación**, acepte la configuración predeterminada de **Instalación basada en características o en roles**. Haga clic en **Siguiente**.![Asistente para agregar roles y características 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
	- En la página **Seleccionar servidor de destino**, elija **Seleccionar un servidor del grupo de servidores**. El servidor host debería detectarse automáticamente. Haga clic en **Siguiente**.
	- En la página **Seleccionar roles de servidor**, haga clic en **Siguiente**.
	- En la página **Seleccionar características**, seleccione **E/S de múltiples rutas** y, luego, haga clic en **Siguiente**.![Asistente para agregar roles y características 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
	- En la página **Confirmar selecciones de instalación**, confirme la selección y, después, seleccione **Reiniciar automáticamente el servidor de destino en caso necesario**, tal y como se muestra aquí. Haga clic en **Instalar**.![Asistente para agregar roles y características 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
	- Se le notificará cuando la instalación finalice. Haga clic en **Cerrar** para cerrar el asistente.![Asistente para agregar roles y características 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## Paso 2: configurar MPIO para volúmenes de StorSimple

MPIO tiene que configurarse de forma que identifique los volúmenes de StorSimple. Haga lo siguiente para configurar MPIO para reconocer volúmenes de StorSimple.

#### Para configurar MPIO para volúmenes de StorSimple

1. Abra la **configuración de MPIO**. Haga clic en **Administrador del servidor > Panel > Herramientas > MPIO**.

2. En el cuadro de diálogo **Propiedades de MPIO**, seleccione la pestaña **Detectar múltiples rutas**.

3. Seleccione **Agregar compatibilidad con dispositivos iSCSI** y, luego, haga clic en **Agregar**. ![Propiedades de MPIO > Detectar múltiples rutas](./media/storsimple-configure-mpio-windows-server/IC741003.png)

4. Reinicie el servidor cuando se le pida.
5. En el cuadro de diálogo **Propiedades de MPIO**, haga clic en la pestaña **Dispositivos con MPIO**. Haga clic en **Agregar**. </br>![Propiedades de MPIO > Dispositivo con MPIO](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. En el cuadro de diálogo **Agregar compatibilidad con MPIO**, en **Identificador de hardware de dispositivo**, escriba el número de serie del dispositivo. Encontrará el número de serie del dispositivo si tiene acceso al administrador de StorSimple y va a **Dispositivos > Panel**. El número de serie del dispositivo se muestra en el panel de **vista rápida** derecho del panel del dispositivo. </br>![Agregar compatibilidad con MPIO](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Reinicie el servidor cuando se le pida.

## Paso 3: montar los volúmenes de StorSimple en el host

Después de configurar MPIO en Windows Server, los volúmenes creados en el dispositivo StorSimple se pueden montar para que puedan usar MPIO para la redundancia. Haga lo siguiente para montar un volumen.

#### Para montar volúmenes en el host

1. Abra la ventana **Propiedades del iniciador iSCSI** en el host de Windows Server. Haga clic en **Administrador del servidor > Panel > Herramientas > Iniciador iSCSI**.
2. En el cuadro de diálogo **Propiedades del iniciador iSCSI**, haga clic en la pestaña Detección y, después, en **Detectar portal de destino**.
3. Haga lo siguiente en el cuadro de diálogo **Detectar portal de destino**:
	
	- Escriba la dirección IP del puerto DATA del dispositivo StorSimple (por ejemplo, escriba DATA 0).
	- Haga clic en **Aceptar** para volver al cuadro de diálogo **Propiedades del iniciador iSCSI**.

	>[AZURE.IMPORTANT] **Si está usando una red privada para las conexiones iSCSI, escriba la dirección IP del puerto DATA que esté conectado a la red privada.**

4. Repita los pasos 2 y 3 para una segunda interfaz de red (por ejemplo, DATA 1) en el dispositivo. No olvide que estas interfaces deben estar habilitadas para iSCSI. Para obtener más información al respecto, consulte [Modificación de interfaces de red](storsimple-modify-device-config.md#modify-network-interfaces).
5. Seleccione la pestaña **Destinos** del cuadro de diálogo **Propiedades del iniciador iSCSI**. Debería ver el destino de dispositivo StorSimple IQN en **Destinos detectados**. ![Propiedades del iniciador iSCSI > pestaña Destinos](./media/storsimple-configure-mpio-windows-server/IC741007.png)
6. Haga clic en **Conectar** para establecer una sesión iSCSI con el dispositivo StorSimple. Aparecerá el cuadro de diálogo **Conectarse al destino**.

7. En el cuadro de diálogo **Conectarse al destino**, active la casilla **Habilitar múltiples rutas**. Haga clic en **Avanzadas**.

8. Haga lo siguiente en el cuadro de diálogo **Configuración avanzada**:
	- 	 En la lista desplegable **Adaptador local**, seleccione **Iniciador iSCSI de Microsoft**.
	- 	 En la lista desplegable **IP de iniciador**, seleccione la dirección IP del host.
	- 	 En la lista desplegable **IP del portal de destino**, seleccione la dirección IP de la interfaz de dispositivo.
	- 	 Haga clic en **Aceptar** para volver al cuadro de diálogo **Propiedades del iniciador iSCSI**.

9. Haga clic en **Propiedades**. En el cuadro de diálogo **Propiedades**, haga clic en **Agregar sesión**.
10. En el cuadro de diálogo **Conectarse al destino**, active la casilla **Habilitar múltiples rutas**. Haga clic en **Avanzadas**.
11. En el cuadro de diálogo **Configuración avanzada**:										
	-  En la lista desplegable **Adaptador local**, seleccione Iniciador iSCSI de Microsoft.
	-  En la lista desplegable **IP de iniciador**, seleccione la dirección IP correspondiente al host. En este caso, estamos conectando dos interfaces de red en el dispositivo a una única interfaz de red en el host. Por lo tanto, esta interfaz es la misma que la proporcionada para la primera sesión.
	-  En la lista desplegable **IP del portal de destino**, seleccione la dirección IP de la segunda interfaz de datos habilitada en el dispositivo.
	-  Haga clic en **Aceptar** para volver al cuadro de diálogo Propiedades del iniciador iSCSI. Agregó una segunda sesión al destino.

12. Abra **Administración de equipos**; para ello, vaya a **Administrador del servidor > Panel > Administración de equipos**. En el panel izquierdo, haga clic en **Almacenamiento > Administración de discos**. Los volúmenes creados en el dispositivo StorSimple que son visibles para este host aparecerán en **Administración de discos** como discos nuevos.

13. Inicialice el disco y cree otro volumen. Durante el proceso de formato, seleccione un tamaño de bloque de 64 KB. ![Administración de discos](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. En **Administración de discos**, haga clic con el botón secundario en el **disco** y seleccione **Propiedades**.
15. En el cuadro de diálogo **Propiedades del dispositivo de disco de múltiples rutas** del número de modelo de StorSimple pertinente, haga clic en la pestaña **MPIO**. ![Propiedades del dispositivo de disco de múltiples rutas StorSimple 8100.](./media/storsimple-configure-mpio-windows-server/IC741009.png)

16. En la sección **Nombre DSM**, haga clic en **Detalles** y compruebe que los parámetros están establecidos en los valores predeterminados. Los parámetros predeterminados son los siguientes:

	- Período de comprob. de ruta = 30
	- Número de reintentos = 3
	- Período de eliminación PDO = 20
	- Intervalo de reintento = 1
	- Comprobación de ruta habilitada = Sin seleccionar


>[AZURE.NOTE] **No modifique los parámetros predeterminados.**

## Paso 4: configurar MPIO para alta disponibilidad y equilibrio de carga

En entornos de múltiples rutas basadas en alta disponibilidad y equilibrio de carga, hay que agregar manualmente varias sesiones para declarar las distintas rutas de acceso disponibles. Por ejemplo, si el host tiene dos interfaces conectadas a la SAN y el dispositivo también tiene otras dos interfaces conectadas a la SAN, serán necesarias cuatro sesiones configuradas con permutaciones de ruta de acceso adecuadas (solo se necesitarán dos sesiones si cada interfaz DATA e interfaz de host están en una subred IP diferente y no es enrutable).

>[AZURE.IMPORTANT] **Recomendamos no combinar interfaces de red de 1 GbE y de 10 GbE. Si usa dos interfaces de red, ambas deben ser de idéntico tipo.**

Con el siguiente procedimiento explicamos cómo agregar las sesiones cuando un dispositivo StorSimple con dos interfaces de red está conectado a un host con dos interfaces de red.

### Para configurar MPIO para alta disponibilidad y equilibrio de carga

1. Realice una detección en el destino; para ello, en el cuadro de diálogo **Propiedades del iniciador iSCSI**, en **Detección**, haga clic **Detectar portal**.
2. En el cuadro de diálogo **Conectarse al destino**, escriba la dirección IP de una de las interfaces de red del dispositivo.
3. Haga clic en **Aceptar** para volver al cuadro de diálogo **Propiedades del iniciador iSCSI**.

4. En el cuadro de diálogo **Propiedades del iniciador iSCSI**, seleccione la pestaña **Destinos**, resalte el destino detectado y, después, haga clic en **Conectar**. Aparecerá el cuadro de diálogo **Conectarse al destino**.

5. En el cuadro de diálogo **Conectarse al destino**:
	
	- Deje seleccionada la configuración de destino predeterminada en **Agregar esta conexión** a la lista de destinos favoritos. Esto hará que el dispositivo intente reiniciar la conexión automáticamente cada vez que el equipo se reinicie.
	- Active la casilla **Habilitar múltiples rutas**.
	- Haga clic en **Avanzadas**.

6. En el cuadro de diálogo **Configuración avanzada**:
	- En la lista desplegable **Adaptador local**, seleccione **Iniciador iSCSI de Microsoft**.
	- En la lista desplegable **IP de iniciador**, seleccione la dirección IP del host.
	- En la lista desplegable **IP del portal de destino**, seleccione la dirección IP de la interfaz de datos habilitada en el dispositivo.
	- Haga clic en **Aceptar** para volver al cuadro de diálogo Propiedades del iniciador iSCSI.

7. Haga clic en **Propiedades** y, en el cuadro de diálogo **Propiedades**, haga clic en **Agregar sesión**.

8. En el cuadro de diálogo **Conectarse al destino**, active la casilla **Habilitar múltiples rutas** y, luego, haga clic en **Avanzadas**.

9. En el cuadro de diálogo **Configuración avanzada**:
	1. En la lista desplegable **Adaptador local**, seleccione **Iniciador iSCSI de Microsoft**.
	2. En la lista desplegable **IP de iniciador**, seleccione la dirección IP correspondiente a la segunda interfaz en el host.
	3. En la lista desplegable **IP del portal de destino**, seleccione la dirección IP de la segunda interfaz de datos habilitada en el dispositivo.
	4. Haga clic en **Aceptar** para volver al cuadro de diálogo **Propiedades del iniciador iSCSI**. Agregó una segunda sesión al destino.

10. Repita los pasos 8, 9 y 10 para agregar más sesiones (rutas de acceso) al destino. Con dos interfaces en el host y dos en el dispositivo, puede agregar un total de cuatro sesiones.

11. Después de agregar las sesiones (rutas de acceso) que quiera, en el cuadro de diálogo **Propiedades del iniciador iSCSI**, seleccione el destino y haga clic en **Propiedades**. En la pestaña Sesiones del cuadro de diálogo **Propiedades**, fíjese en los cuatro identificadores de sesión, que se corresponden con las posibles permutaciones de ruta de acceso. Para cancelar una sesión, active la casilla situada junto a un identificador de sesión y, luego, haga clic en **Desconectar**.

12. Para ver los dispositivos presentados en las sesiones, seleccione la pestaña **Dispositivos**. Para configurar la directiva MPIO de un dispositivo seleccionado, haga clic en **MPIO**. Aparecerá el cuadro de diálogo **Detalles del dispositivo**. En la pestaña **MPIO**, puede seleccionar la configuración de **Directiva de equilibrio de carga** apropiada. También puede ver el tipo de ruta de acceso **Activo** o **En espera**.

## Pasos siguientes

Obtenga más información sobre el [uso del servicio StorSimple Manager para modificar la configuración del dispositivo StorSimple](storsimple-modify-device-config.md).
 

<!---HONumber=AcomDC_0224_2016-->