<properties 
   pageTitle="Conmutación por recuperación de máquinas virtuales de VMware y servidores físicos desde Azure hasta VMware | Microsoft Azure" 
   description="Este artículo describe cómo realizar una conmutación por recuperación de una máquina virtual de VMware replicada en Azure con Azure Site Recovery." 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="storage-backup-recovery" 
   ms.date="12/14/2015"
   ms.author="ruturajd@microsoft.com"/>

# Conmutación por recuperación de máquinas virtuales de VMware y servidores físicos desde Azure hasta VMware con Azure Site Recovery (heredado)

> [AZURE.SELECTOR]
- [Enhanced](site-recovery-failback-azure-to-vmware-classic.md)
- [Legacy](site-recovery-failback-azure-to-vmware-classic-legacy.md)


## Información general

En este documento se describe cómo conmutar por recuperación máquinas virtuales de VMware y servidores físicos con Windows o Linux desde Azure a un sitio local.

Para configurar la replicación y conmutación por error en este escenario siga las instrucciones de [este artículo](site-recovery-vmware-to-azure.md). Después de una conmutación correcta de máquinas virtuales de VMware o servidores físicos en Azure con Site Recovery, las máquinas estarán disponibles en la pestaña Máquinas virtuales de Azure.

>[AZURE.NOTE]Solo puede conmutar por recuperación máquinas virtuales de VMware y servidores físicos con Windows o Linux desde Azure a máquinas virtuales de VMware en el sitio local principal. Si se realiza una conmutación por recuperación en una máquina física, la conmutación por error a Azure la convertirá en una máquina virtual de Azure y conmutación por recuperación a VMware la convertirá en una máquina virtual de VMware.

Este diagrama representa el escenario de conmutación por error y conmutación por recuperación. Las líneas azules son las conexiones utilizadas durante la conmutación por error. Las líneas rojas son las conexiones utilizadas durante la conmutación por recuperación. Las líneas con flechas van a través de Internet.

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## Paso 1: Instalación local de vContinuum

Es preciso instalar un servidor local de vContinuum y apuntarlo hacia el servidor de configuración.

1.  [Descargue vContinuum](http://go.microsoft.com/fwlink/?linkid=526305). 
2.  Una vez que lo haya descargado, descargue la versión actualizada de [vContinuum](http://go.microsoft.com/fwlink/?LinkID=533813).
3.  Ejecute el programa de instalación de la versión más reciente para instalar vContinuum. En la página **principal**, haga clic en **Siguiente**.![](./media/site-recovery-failback-azure-to-vmware/image2.png)
4.  En la primera página del asistente, especifique tanto la dirección IP como el puerto del servidor de CX. Seleccione **Use HTTPS** (Usar HTTPS).

	![](./media/site-recovery-failback-azure-to-vmware/image3.png)

5.  Busque la dirección IP del servidor de configuración en la pestaña **Panel** de la máquina virtual del servidor de configuración en Azure. ![](./media/site-recovery-failback-azure-to-vmware/image4.png)

6.  Busque el puerto público HTTPS en la pestaña **Puntos de conexión** de la máquina virtual del servidor de configuración en Azure.

	![](./media/site-recovery-failback-azure-to-vmware/image5.png)

7. En la página **CS Passphrase Details** (Detalles de frase de contraseña de CS), especifique la frase de contraseña que anotó al registrar el servidor de configuración. Si no la recuerda, puede encontrarla en la carpeta **C:\\Program Files (x86)\\InMage Systems\\private\\connection.passphrase** de la máquina virtual del servidor de configuración.

	![](./media/site-recovery-failback-azure-to-vmware/image6.png)

8.  En la página **Select Destination Location** (Seleccionar ubicación de destino), especifique la ubicación en que desea instalar el servidor de vContinuum y haga clic en **Install** (Instalar).

	![](./media/site-recovery-failback-azure-to-vmware/image7.png)

9. Una vez que se complete la instalación, puede iniciar vContinuum. ![](./media/site-recovery-failback-azure-to-vmware/image8.png)


## Paso 2: Instalación de un servidor de procesos de Azure 

Para que las máquinas virtuales de Azure puedan devolver los datos a un servidor maestro local de destino, es preciso instalar un servidor de procesos en Azure.

1.  En la página **Servidores de configuración** de Azure, seleccione la opción para agregar un nuevo servidor de procesos.

	![](./media/site-recovery-failback-azure-to-vmware/image9.png)

2.  Especifique un nombre de servidor de procesos y un nombre y una contraseña para conectarse a la máquina virtual como administrador. Seleccione el servidor de configuración en el que desea registrar el servidor de procesos. Debe ser el mismo servidor que use para proteger y conmutar por error las máquinas virtuales.
3.  Especifique la red de Azure en la que debe implementarse el servidor de procesos. Debe ser la misma red que el servidor de configuración. Especifique una subred con una dirección IP única seleccionada y comience la implementación.

	![](./media/site-recovery-failback-azure-to-vmware/image10.png)

4.  Se desencadenará un trabajo para implementar el servidor de procesos.

	![](./media/site-recovery-failback-azure-to-vmware/image11.png)

5.  Una vez que el servidor de procesos se implemente en Azure, podrá iniciar sesión en el servidor con las credenciales que especificó. Registre el servidor de procesos de la misma manera que registró el servidor de procesos local.

	![](./media/site-recovery-failback-azure-to-vmware/image12.png)

>[AZURE.NOTE]Los servidores registrados durante la conmutación por separación no se verán en las propiedades de la máquina virtual en Site Recovery. Solo se verán en la pestaña **Servidores** del servidor de configuración en que se han registrado. El servidor de procesos puede tardar aproximadamente 10-15 minutos en aparecer en la pestaña.


## Paso 3: Instalación de un servidor de destino maestro local

En función del sistema operativos de las máquinas virtuales de origen necesitará instalar un servidor de destino maestro local con Linux o con Windows.

### Implementación de un servidor de destino maestro con Windows

El programa de instalación de vContinuum incluye un destino maestro con Windows. Al instalar vContinuum, también se implementa un servidor maestro en la misma máquina, y se registra en el servidor de configuración.

1.  Para comenzar la implementación, cree una máquina vacía local en el host ESX en el que desea recuperar las máquinas virtuales de Azure.

2.  Asegúrese de que hay al menos dos discos conectados a la máquina virtual: uno se usa para el sistema operativo y el otro para la unidad de retención.

3.  Instale el sistema operativo.

4.  Instale vContinuum en el servidor. De esta forma se completa la instalación del servidor de destino maestro.

### Implementación de un servidor de destino maestro con Linux

1.  Para comenzar la implementación, cree una máquina vacía local en el host ESX en el que desea recuperar las máquinas virtuales de Azure.

2.  Asegúrese de que hay al menos dos discos conectados a la máquina virtual: uno se usa para el sistema operativo y el otro para la unidad de retención.

3.  Instale el sistema operativo Linux. El sistema de destino maestro de Linux no debe usar LVM para los espacios de almacenamiento raíz o de retención. Se configura un servidor de destino maestro de Linux para evitar la detección de discos y particiones LVM de forma predeterminada.
4.  Estas son las particiones que puede crear:

	![](./media/site-recovery-failback-azure-to-vmware/image13.png)

5.  Antes de comenzar la instalación del destino maestro realice los siguientes pasos posteriores a la instalación.


#### Pasos posteriores a la instalación del sistema operativo:

Para obtener los identificadores de SCSI de los discos duros SCSI de una máquina virtual con Linux, habilite el parámetro "disk.EnableUUID = TRUE" como se indica a continuación:

1. Apague la máquina virtual.
2. Haga clic con el botón derecho en la entrada de la máquina virtual en el panel izquierdo > **Edit Settings** (Editar configuración).
3. Haga clic en la pestaña **Opciones**. Seleccione **Advanced>General item** (Opciones avanzadas > Elemento general) > **Configuration Parameters** (Parámetros de configuración). La opción **Configuration Parameters** (Parámetros de configuración) solo está disponible cuando se apaga la máquina.

	![](./media/site-recovery-failback-azure-to-vmware/image14.png)

4. Comprueba si existe alguna fila con **disk.EnableUUID**. Si existe y está establecida en **False**, establézcala en **True** (no distingue mayúsculas de minúsculas). Si existe y está establecida en True, haga clic en **Cancel** Cancelar y pruebe el comando SCSI en el sistema operativo invitado después de que haya arrancado. Si no existe, haga clic **Add Row** (Agregar fila).
5. Agregue disk.EnableUUID en la columna **Name** (Nombre). Establezca su valor en TRUE. No agregue los valores anteriores entre comillas dobles.

	![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### Descarga e instalación de los paquetes adicionales

Nota: Antes de descargar e instalar los paquetes adicionales asegúrese de que el sistema tiene conectividad a Internet.

\# yum install -y xfsprogs perl lsscsi rsync wget kexec-tools

Este comando descarga estos 15 paquetes desde el repositorio de CentOS 6.6 y los instala:

bc-1.06.95-1.el6.x86\_64.rpm

busybox-1.15.1-20.el6.x86\_64.rpm

elfutils-libs-0.158-3.2.el6.x86\_64.rpm

kexec-tools-2.0.0-280.el6.x86\_64.rpm

lsscsi-0.23-2.el6.x86\_64.rpm

lzo-2.03-3.1.el6\_5.1.x86\_64.rpm

perl-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-Module-Pluggable-3.90-136.el6\_6.1.x86\_64.rpm

perl-Pod-Escapes-1.04-136.el6\_6.1.x86\_64.rpm
	
perl-Pod-Simple-3.13-136.el6\_6.1.x86\_64.rpm

perl-libs-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-version-0.77-136.el6\_6.1.x86\_64.rpm

rsync-3.0.6-12.el6.x86\_64.rpm

snappy-1.1.0-1.el6.x86\_64.rpm

wget-1.12-5.el6\_6.1.x86\_64.rpm

Nota: Si la máquina de origen utiliza los sistemas de archivos Reiser o XFS para el raíz o el dispositivo de arranque, los siguientes paquetes deben descargarse e instalarse en el destino maestro Linux antes de la protección.

\# cd /usr/local

\# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

\# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

\# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm

\# wget <http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

\# rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

#### Aplicación de cambios en la configuración personalizada

Antes de aplicar estos cambios asegúrese de que ha completado la sección anterior y, a continuación, siga estos pasos:


1. Copie al binario de RHEL 6-64 Unified Agent en el sistema operativo recién creado.

2. Ejecute este comando para descomprimir el binario: **tar - zxvf < nombreDeArchivo >**

3. Ejecute este comando para conceder permisos: # **chmod 755 ./ApplyCustomChanges.sh**

4. Ejecute el script: **# ./ApplyCustomChanges.sh**. Ejecute el script sólo una vez en el servidor. Reinicie el servidor después de que se ejecute el script.



### Instalación del servidor Linux


1. [Descargue](http://go.microsoft.com/fwlink/?LinkID=529757) el archivo de instalación.
2. Copie el archivo descargado a la máquina virtual del destino maestro de Linux con la utilidad cliente de SFTP que prefiera. O bien, puede iniciar sesión en la máquina virtual del servidor de destino maestra con Linux y usar wget para descargar el paquete de instalación desde el vínculo proporcionado.
3. Inicie sesión en la máquina virtual del servidor de destino maestra de Linux con el cliente ssh que prefiera.
4. Si está conectado a la red de Azure en la que implementó el servidor de destino maestro de Linux a través de una conexión VPN, utilice la dirección IP interna para el servidor que encontrará en la pestaña **Dashboard** (Panel) de la máquina virtual y el puerto 22 para conectarse al servidor de destino maestro de Linux mediante Secure seguro.
5. Si se va a conectar al servidor de destino maestro de Linux a través de una conexión a Internet pública, use la dirección IP virtual pública del servidor de destino maestro [desde la pestaña **Dashboard** (Panel) de las máquinas virtuales] y el punto de conexión público creado para ssh para iniciar sesión en el servidor de Linux.
6. Extraiga los archivos del archivo tar del instalador de servidor de destino maestro de Linux comprimido con gzip ejecutando: *“tar –xvzf Microsoft-ASR\_UA\_8.2.0.0\_RHEL6-64*”* del directorio que contenga el archivo del instalador.

	![](./media/site-recovery-failback-azure-to-vmware/image16.png)

7. Si ha extraído los archivos del instalador a un directorio diferente, cambie al directorio en el que se extrajo el contenido del archivo tar. En la ruta de acceso al directorio, ejecute "sudo ./install.sh"

	![](./media/site-recovery-failback-azure-to-vmware/image17.png)

8. Cuando se le solicite que elija un rol principal, seleccione **2 (destino maestro)**. Deje las restantes opciones de la instalación interactiva con sus valores predeterminados.
9. Espere hasta que continúe la instalación y aparezca la interfaz de Host Config. La utilidad Host Configuration del servidor de destino maestro de Linux es una utilidad de línea de comandos. No cambie el tamaño de la ventana de la utilidad del cliente ssh. Use las teclas de dirección para seleccionar la opción **Global** y presione Entrar en el teclado.

	![](./media/site-recovery-failback-azure-to-vmware/image18.png)

	![](./media/site-recovery-failback-azure-to-vmware/image19.png)


10. En el campo **IP** escriba la dirección IP interna del servidor de configuración [puede encontrarla en la pestaña **Dashboard** (Panel) de la máquina virtual del servidor de configuración] y presione Entrar. En el campo **Port** (Puerto), escriba **22** y presione Entrar.
11.  En **Use HTTPS** (Usar HTTPS) deje la opción **Yes** (Sí) y presione Entrar.
12.  Escriba la frase de contraseña que se generó en el servidor de configuración. Si usa un cliente PUTTY desde una máquina con Windows para ssh hasta la máquina virtual de Linux, puede utilizar Mayús+Insertar para pegar el contenido del Portapapeles. Copie la frase de contraseña en el Portapapeles local mediante Ctrl-C y presione Mayús+Insertar para pegarlo. Presione Entrar.
13.  Utilice la tecla de dirección derecha para salir y presione Entrar. Espere hasta que la instalación se complete.

	![](./media/site-recovery-failback-azure-to-vmware/image20.png)

Si por alguna razón no pudo registrar el servidor de destino maestro de Linux en el servidor de configuración, puede hacerlo ejecutando la utilidad de configuración de host desde /usr/local/ASR/Vx/bin/hostconfigcli (antes tendrá que establecer los permisos de acceso a este directorio mediante la ejecución de chmod como superusuario).


#### Validación del registro de destino maestro

Puede validar que el servidor de destino maestro se ha registrado correctamente en el servidor de configuración en el almacén de Azure Site Recovery > **Servidor de configuración** > **Detalles del servidor**.

>[AZURE.NOTE]Después de registrar el servidor de destino maestro, si recibe errores de configuración que indican la máquina virtual se podría haber eliminado de Azure o que los puntos de conexión no se han configurado correctamente, se debe a que aunque los puntos de conexión de Azure detectan la configuración del destino principal cuando el destino maestro se implementa en Azure, no lo hacen cuando el servidor de destino maestro servidor es local. Esto no afectará a la conmutación por recuperación y puede ignorar dichos errores.



## Paso 4: Protección de las máquinas virtuales en el sitio local

Antes de la conmutación por recuperación es preciso proteger las máquinas virtuales en el sitio local.

### Antes de empezar

Cuando una máquina virtual se conmuta por error a Azure, agrega una unidad temporal adicional para el archivo de paginación. La máquina virtual que se ha conmutado por error no suele requerir dicha unidad, ya que es posible que disponga de una unidad dedicada al archivo de paginación. Antes de comenzar la protección inversa de las máquinas virtuales, es preciso asegurarse de que la unidad está sin conexión, con el fin de que no reciba protección. Para ello, realice lo siguiente:

1.  Abra Administración de almacenamiento y seleccione Administración de almacenamiento para que enumere tanto los discos en línea como los conectados a la máquina.
2.  Seleccione el disco temporal conectado a la máquina y elija ponerlo sin conexión. 

### Protección de las máquinas virtuales

1. En el Portal de Azure, examine los estados de la máquina virtual para asegurase de que está conmutada por error.

	![](./media/site-recovery-failback-azure-to-vmware/image21.png)

2. Inicie vContinuum en el equipo

	![](./media/site-recovery-failback-azure-to-vmware/image8.png)

3. Haga clic en **New Protection** (Nueva protección) y seleccione el tipo de sistema de operación.

4.  En la nueva ventana que se abra, seleccione **OS type** (Tipo de operación) > **Get Details** (Obtener detalles) para las máquinas virtuales que desea conmutar por recuperación. En **Primary server details** (Detalles de servidor principal), identifique y seleccione las máquinas virtuales que desea proteger. Las máquinas virtuales se muestran bajo el nombre de host de vCenter en el que estaban antes de la conmutación por error.
5.  Al seleccionar la máquina virtual que va proteger (y que ya se ha conmutado por error a Azure), aparecerá una ventana emergente con dos entradas para la máquina virtual. Esto se debe a que el servidor de configuración detecta dos instancias de las máquinas virtuales registradas. Debe quitar la entrada de la VM local, con el fin de que pueda proteger la máquina virtual correcta. Para identificar la entrada de la máquina virtual de Azure correcta, puede iniciar sesión en la máquina virtual de Azure y e ir a C:\\Archivos de programa (x86)\\Microsoft Azure Site Recovery\\Application Data\\etc. En el archivo drscout.conf, identifique el id. del host. En el cuadro de diálogo vContinuum, conserve la entrada cuyo id. de host encontró en la máquina virtual. Elimine las restantes entradas. Para seleccionar la máquina virtual correcta, puede consultar su dirección IP. El rango local de direcciones IP será la máquina virtual local.

	![](./media/site-recovery-failback-azure-to-vmware/image22.png)

	![](./media/site-recovery-failback-azure-to-vmware/image23.png)

6. En el servidor de vCenter detenga la máquina virtual. También puede eliminar las máquinas virtuales locales.
7. A continuación, especifique el servidor de destino maestro local en el que desea proteger las máquinas virtuales. Para ello, conéctese al vCenter en el que desee realizar la conmutación por recuperación.

	![](./media/site-recovery-failback-azure-to-vmware/image24.png)

8. Seleccione el servidor de destino maestro según el host en el que desee recuperar la máquina virtual.

	![](./media/site-recovery-failback-azure-to-vmware/image24.png)

9. Proporcione la opción de replicación en todas las máquinas virtuales. Para ello, deberá seleccionar el almacén de datos del lado de la recuperación en el que se recuperarán las máquinas virtuales. La tabla resumen las distintas opciones que es preciso proporcionar a cada máquina virtual.

	![](./media/site-recovery-failback-azure-to-vmware/image25.png)

	**Opción** | **Valor recomendado de la opción**
	---|---
	Process server IP address (Dirección IP del servidor de procesos) | Seleccione el servidor de procesos implementado en Azure
	Retention size in MB (Tamaño de retención en MB)| 
	Retention value (Valor de retención) | 1
	Days/Hours (Días y horas) | Days (Días)
	Consistency Interval (Intervalo de consistencia) | 1
	Select target Datastore (Seleccionar almacén de datos de destino) | El almacén de datos disponible en el lado de la recuperación. Este almacén de datos debe tener espacio suficiente y estar disponible en el host ESX en el que desea restaurar la máquina virtual.


10. Configure las propiedades que va a adquirir la máquina virtual después de la conmutación por error en el sitio local. Dichas propiedades se resumen en la tabla siguiente.

	![](./media/site-recovery-failback-azure-to-vmware/image26.png)

	**Propiedad** | **Detalles**
	---|---
	Network configuration (Configuración de red)| En cada adaptador de red detectado, selecciónelo y haga clic en **Change** (Cambiar) para configurar la dirección IP de conmutación por recuperación de la máquina virtual. 
	Hardware Configuration (Configuración de hardware)| Especifique la CPU y memoria de la máquina virtual. La configuración se puede aplicar a todas las máquinas virtuales que intenta proteger. Para identificar los valores correctos de la CPU y memoria, puede consultar el tamaño del rol de las máquinas virtuales de IAAS y ver el número de núcleos y la memoria asignados.
	Nombre para mostrar | Después de realizar la conmutación por recuperación a local, puede cambiar el nombre de las máquinas virtuales por el que aparecerá en el inventario de vCenter. El nombre predeterminado es el nombre de host del equipo de la máquina virtual. Para identificar el nombre de la máquina virtual, puede hacer referencia a la lista de máquinas virtuales del grupo de protección.
	NAT Configuration (Configuración de NAT) | Se describe en detalle a continuación.

	![](./media/site-recovery-failback-azure-to-vmware/image27.png)

#### Configuración de NAT

1. Para habilitar la protección de las máquinas virtuales, es preciso establecer dos canales de comunicación. El primer canal está entre la máquina virtual y el servidor de procesos. Este canal recopila los datos de la máquina virtual y los envía al servidor de procesos que, a su vez, los envía al servidor de destino maestro. Si el servidor de procesos y la máquina virtual que se va a proteger están en la misma red virtual de Azure, no es preciso utilizar la configuración de NAT. De lo contrario, especifique la configuración de NAT. Vea la dirección IP pública del servidor de procesos en Azure. 

	![](./media/site-recovery-failback-azure-to-vmware/image28.png)

2. El segundo canal está entre el servidor de procesos y el servidor de destino maestro. La opción de usar NAT, o no, depende de si se usa una conexión basada en VPN o se comunica a través de Internet. No seleccione NAT si utiliza VPN, solo si usa una conexión a Internet.

	![](./media/site-recovery-failback-azure-to-vmware/image29.png)

	![](./media/site-recovery-failback-azure-to-vmware/image30.png)

3. Si no ha eliminado las máquinas virtuales locales como se ha especificado y si el almacén de datos al que se realiza la conmutación por recuperación aún contiene el VMDK anterior, tendrá que asegurarse de que la máquina virtual de conmutación por recuperación se crea en un lugar nuevo. Para ello, seleccione **Advanced** (Opciones avanzadas) y especifique una carpeta alternativa en la que realizar la restauración en **Folder Name Settings** (Configuración de nombre de carpeta). Deje las restantes opciones con su configuración predeterminada. Aplique la configuración de los nombres de las carpetas a todos los servidores.

	![](./media/site-recovery-failback-azure-to-vmware/image31.png)

4. Ejecute una comprobación de preparación para asegurarse de que las máquinas virtuales están listas para protegerse en local.

	![](./media/site-recovery-failback-azure-to-vmware/image32.png)


5. Espere hasta que se complete la operación. Si el resultado es satisfactorio en todas las máquinas virtuales, puede especificar un nombre para el plan de protección. A continuación, haga clic en **Protect** (Proteger) para comenzar.

	![](./media/site-recovery-failback-azure-to-vmware/image33.png)


6. El progreso se puede supervisar en vContinuum.

	![](./media/site-recovery-failback-azure-to-vmware/image34.png)

7. Cuando el paso **Activating Protection Plan** (Activación del plan de protección) finalice, puede supervisar la protección de la máquina virtual en el portal de Site Recovery.

	![](./media/site-recovery-failback-azure-to-vmware/image35.png)

8. Para ver el estado exacto, haga clic en la máquina virtual y supervise su progreso.

	![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## Preparación del plan de conmutación por recuperación

Puede preparar un plan de conmutación por recuperación con vContinuum, de modo que pueda volver a realizarse la conmutación por recuperación de la aplicación en el sitio local en cualquier momento. Dichos planes de recuperación son muy similares a los planes de recuperación de Site Recovery.

1.  Inicie vContinuum y seleccione **Manage plans** (Administrar planes) > **Recover** (Recuperar). Puede ver de la lista de todos los planes que se han utilizado para conmutar por error las máquinas virtuales. Puede usar los mismos para realizar la recuperación.

	![](./media/site-recovery-failback-azure-to-vmware/image37.png)

2. Seleccione el plan de protección y todas las máquinas virtuales que desea recuperar en él. Al seleccionar cada máquina virtual puede ver más detalles, incluido el servidor ESX de destino y el disco de la máquina virtual de origen. Haga clic en **Next** (Siguiente) para iniciar el Asistente para recuperación y seleccione las máquinas virtuales que desea recuperar.

	![](./media/site-recovery-failback-azure-to-vmware/image38.png)

3. Se pueden elegir varias opciones al realizar la recuperación, pero le recomendamos que use **Latest Tag** (Última etiqueta) y seleccione **Apply for All VMs** (Aplicar a todas las máquinas virtuales) para asegurarse de que se usarán los últimos datos de la máquina virtual.
4. Ejecute la **comprobación de idoneidad.** Así se comprobará si están configurados los parámetros correctos para habilitar la recuperación de la máquina virtual. Haga clic en **Next** (Siguiente) si todas las comprobaciones son correctas. Si no es así, compruebe el registro y resuelva los errores.

	![](./media/site-recovery-failback-azure-to-vmware/image39.png)

8.  En **VM Configuration** (Configuración de máquina virtual), asegúrese de que la configuración de recuperación está establecida correctamente. Si necesita cambiar la configuración de la máquina virtual, puede hacerlo.

	![](./media/site-recovery-failback-azure-to-vmware/image40.png)

9. Revise la lista de máquinas virtuales que se van a recuperar y especifique el orden de recuperación. Tenga en cuenta que las máquinas virtuales se enumeran con el nombre de host del equipo. Puede que sea difícil asignar el nombre de host del equipo a la máquina virtual. Para asignar los nombres, vaya al **Panel** de las máquinas virtuales en Azure y compruebe el nombre de host de la máquina virtual.

	![](./media/site-recovery-failback-azure-to-vmware/image41.png)

10. Especifique un nombre de plan y seleccione **Recover later** (Recuperar más adelante). Se recomienda realizar la recuperación más adelante, ya que la protección inicial podría no estar completa.
11. Haga clic en **Recover** (Recuperar) para guardar el plan o desencadenar la recuperación si ha seleccionado realizar la recuperación ahora, no más adelante. Puede comprobar el estado de recuperación para ver si el plan se ha guardado.

	![](./media/site-recovery-failback-azure-to-vmware/image42.png)

	![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## Recuperación de máquinas virtuales

Una vez creado el plan, puede recuperar las máquinas virtuales. Antes de hacerlo, compruebe que se ha completado la sincronización de las máquinas virtuales. Si en Replication Status (Estado de replicación) aparece el valor OK (Correcto), significa que la protección se ha completado y se ha alcanzado el umbral de RPO. Puede comprobar el estado en las propiedades de la máquina virtual.

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

Antes de iniciar la recuperación desconecte las máquinas virtuales de Azure. Esto garantiza que no hay cerebro dividido y que los usuarios sólo tendrán acceso a una copia de la aplicación.


1.  Inicie el plan guardado. En vContinuum, seleccione **Monitor** (Supervisar) para supervisar los planes. Así se enumeran todos los planes que se han ejecutado.

	![](./media/site-recovery-failback-azure-to-vmware/image45.png)

2.  Seleccione el plan en **Recovery** (Recuperación) y haga clic en **Start** (Iniciar). Puede supervisar la recuperación. Después de haber activado las máquinas virtuales puede conectarse a ellas en vCenter.

	![](./media/site-recovery-failback-azure-to-vmware/image46.png)

## Reprotección en Azure después de la conmutación por recuperación

Una vez que la conmutación por recuperación se haya completado, puede volver a proteger las máquinas virtuales. Para ello, realice lo siguiente:

1.  Compruebe que los máquinas virtuales locales funcionan y que se puede acceder a las aplicaciones.
2.  En el portal de Azure Site Recovery, seleccione las máquinas virtuales y elimínelas. Seleccione esta opción para deshabilitar la protección de las máquinas virtuales. Esto garantizará que las máquinas virtuales dejan de estar protegidas.
3.  En Azure, elimine las máquinas virtuales de Azure en que se ha realizado la conmutación por error.
4.  Elimine la máquina virtual anterior en vSpehere. Estas son las máquinas virtuales que anteriormente se conmutaron por error en Azure.
5.  En el portal de Site Recovery, proteja las máquinas virtuales que recientemente se conmutaron por error. Una vez protegidas, puede agregarlas a un plan de recuperación.
 
## Pasos siguientes

[Más información](site-recovery-vmware-to-azure-classic-legacy.md) acerca de la replicación de máquinas virtuales de VMware en Azure

 

<!---HONumber=AcomDC_0114_2016--->