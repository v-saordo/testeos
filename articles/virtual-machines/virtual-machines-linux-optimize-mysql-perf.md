<properties
	pageTitle="Optimización del rendimiento de MySQL en máquinas virtuales Linux | Microsoft Azure"
	description="Aprenda a optimizar MySQL ejecutado en una máquina virtual (VM) de Azure con Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="NingKuang"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="ningk"/>

#Optimización del rendimiento de MySQL en máquinas virtuales de Azure con Linux

Existen muchos factores que afectan al rendimiento de MySQL en Azure, tanto en la configuración de selección de software y hardware virtual. Este artículo se centra en la optimización del rendimiento a través del almacenamiento, el sistema y las configuraciones de base de datos.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


##Uso de RAID en una máquina virtual de Azure
El almacenamiento es el factor clave que afecta al rendimiento de la base de datos en entornos de nube. En comparación con un solo disco, RAID puede proporcionar un acceso más rápido gracias a la simultaneidad. Consulte [Niveles RAID estándar](http://en.wikipedia.org/wiki/Standard_RAID_levels) para obtener más detalles.

El rendimiento de E/S de disco, así como el tiempo de respuesta de las E/S pueden mejorarse significativamente con RAID. Nuestras pruebas de laboratorio indican que es posible duplicar el rendimiento de E/S de disco. Asimismo, es posible reducir a la mitad el tiempo de respuesta de E/S de media cuando se duplica el número de discos RAID (de 2 a 4, de 4 a 8, etc.). Consulte el [Apéndice A](#AppendixA) para obtener más información.

Además de las E/S de disco, el rendimiento de MySQL mejora al aumentar el nivel de RAID. Consulte el [Apéndice B](#AppendixB) para obtener más información.

También puede que desee considerar el tamaño del fragmento. En general, cuando el tamaño de fragmento es mayor, obtendrá una menor sobrecarga, especialmente para las operaciones de escritura de mayor envergadura. Sin embargo, si el tamaño del fragmento es demasiado grande, podría agregar una sobrecarga adicional, con lo que no se aprovecharían las ventajas de RAID. El tamaño predeterminado actual es de 512 KB. Este es el tamaño óptimo para los entornos de producción más generales. Consulte el [Apéndice C](#AppendixC) para obtener más información.

Tenga en cuenta que existen límites en cuanto al número de discos que puede agregar para los distintos tipos de máquinas virtuales. Estos límites se detallan en [Tamaños de máquinas virtuales y servicios en la nube de Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Necesitará 4 discos de datos conectados para seguir el ejemplo de RAID que se describe en este artículo, aunque puede optar por configurar RAID con menos discos.

En este artículo se da por supuesto que ya ha creado una máquina virtual Linux y que MYSQL está instalado y configurado. Para obtener más información acerca de cómo empezar, consulte Instalación de MySQL en Azure.

###Configuración de RAID en Azure
Los siguientes pasos muestran cómo crear RAID en Azure mediante el Portal de Azure clásico. También puede configurar RAID mediante scripts de Windows PowerShell. En este ejemplo, configuraremos RAID 0 con 4 discos.

####Paso 1: Agregación de un disco de datos a la máquina virtual  

En la página de máquinas virtuales del Portal de Azure clásico, haga clic en la máquina virtual a la que desea agregar un disco de datos. En este ejemplo, la máquina virtual es mysqlnode1.

![][1]

En la página de la máquina virtual, haga clic en **Panel**.

![][2]


En la barra de tareas, haga clic en **Conectar**.

![][3]

A continuación, haga clic en **Conectar disco vacío**.

![][4]

Para discos de datos, **Preferencia de caché de Host** debe establecerse en **Ninguna**.

Esta acción agregará un disco vacío a la máquina virtual. Repita este paso tres veces más para que disponer de 4 discos de datos para RAID.

Puede ver las unidades agregadas en la máquina virtual examinando el registro de mensajes del kernel. Por ejemplo, para ver esto en Ubuntu, use el comando siguiente:

	sudo grep SCSI /var/log/dmesg

####Paso 2: Creación de RAID con los discos adicionales
Siga las instrucciones de este artículo para obtener información detallada acerca de la configuración de RAID:

[Configuración del software RAID en Linux](virtual-machines-linux-configure-RAID.md)

>[AZURE.NOTE]Si usa el sistema de archivos XFS, siga los pasos siguientes después de haber creado RAID.

Para instalar XFS en Debian, Ubuntu o Linux Mint, use el comando siguiente:

	apt-get -y install xfsprogs  

Para instalar XFS en Fedora, CentOS o RHEL, use el comando siguiente:

	yum -y install xfsprogs  xfsdump


####Paso 3: Configuración de una nueva ruta de acceso de almacenamiento
Use el comando siguiente:

	root@mysqlnode1:~# mkdir -p /RAID0/mysql

####Paso 4: Copia de los datos originales en la nueva ruta de acceso de almacenamiento
Use el comando siguiente:

	root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

####Paso 5: Modificación de permisos para que MySQL pueda tener acceso (lectura y escritura) al disco de datos
Use el comando siguiente:

	root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


##Ajuste el algoritmo de programación de E/S de disco
Linux implementa cuatro tipos de algoritmos de programación de E/S:

-	Algoritmo NOOP (sin operación)
-	Algoritmo de fecha límite (fecha límite)
-	Algoritmo de cola justa (CFQ)
-	Algoritmo de período de presupuesto (antelación)  

Puede seleccionar distintos programadores de E/S en distintos escenarios para optimizar el rendimiento. En un entorno de acceso completamente aleatorio, no hay una gran diferencia entre los algoritmos CFQ y de fecha límite en cuanto a rendimiento. Por lo general se recomienda establecer el entorno de base de datos MySQL en Fecha límite para disponer de mayor estabilidad. Si hay un elevado volumen de E/S secuenciales, el algoritmo CFQ puede reducir el rendimiento de las E/S de disco.

Para SSD y otros equipos, el algoritmo NOOP o de Fecha límite puede ofrecer mayor rendimiento que el programador predeterminado.

Desde el kernel 2.5, el algoritmo de programación de E/S predeterminado es el algoritmo de Fecha límite. A partir del kernel 2.6.18, CFQ se convirtió en el algoritmo de programación de E/S predeterminado. Puede especificar este valor en el tiempo de arranque de Kernel, o bien modificar esta configuración de forma dinámica cuando el sistema se está ejecutando.

En el ejemplo siguiente se muestra cómo comprobar y establecer el programador predeterminado con el algoritmo NOOP.

Para la familia de distribución Debian:

###Paso 1. Visualizar el programador de E/S actual
Use el comando siguiente:

	root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

Se mostrará el la salida siguiente, que indica cuál es el programador actual.

	noop [deadline] cfq


###Paso 2: Cambiar el dispositivo actual (/dev/sda) del algoritmo de programación de E/S
Use los comandos siguientes:

	azureuser@mysqlnode1:~$ sudo su -
	root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
	root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
	root@mysqlnode1:~# update-grub

>[AZURE.NOTE]Establecer este valor solo para /dev/sda no es útil. Debe establecerse en todos los discos de datos en los que reside la base de datos.

Debería mostrarse el resultado siguiente, que indica que grub.cfg se ha vuelto a compilar correctamente y que se ha actualizado el programador predeterminado a NOOP.

	Generating grub configuration file ...
	Found linux image: /boot/vmlinuz-3.13.0-34-generic
	Found initrd image: /boot/initrd.img-3.13.0-34-generic
	Found linux image: /boot/vmlinuz-3.13.0-32-generic
	Found initrd image: /boot/initrd.img-3.13.0-32-generic
	Found memtest86+ image: /memtest86+.elf
	Found memtest86+ image: /memtest86+.bin
	done

Para la familia de distribución Redhat, solo necesita el siguiente comando:

	echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

##Configurar las opciones de las operaciones de archivos de sistema
Una práctica recomendada consiste en deshabilitar la característica de registro atime en el sistema de archivos. Atime representa la última hora de acceso al archivo. Cada vez que se tiene acceso a un archivo, el sistema de archivos registra la marca de tiempo en el registro. Sin embargo, esta información no suele usarse. Por lo tanto, se puede deshabilitar si no es necesaria. Esto reducirá el tiempo total de acceso al disco.

Para deshabilitar el registro de atime, deberá modificar el archivo /etc/ fstab de la configuración del sistema y agregar la opción **noatime**.

Por ejemplo, edite el archivo vim /etc/fstab agregando la opción noatime tal como se muestra a continuación.

	# CLOUD_IMG: This file was created/modified by the Cloud Image build process
	UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
	#Add the “noatime” option below to disable atime logging
	UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
	/dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

A continuación, vuelva a montar el sistema de archivos con el comando siguiente:

	mount -o remount /RAID0

Pruebe el resultado modificado. Tenga en cuenta que cuando se modifica el archivo de prueba, la hora de acceso no se modifica.

Antes del ejemplo:

![][5]

Después del ejemplo:

![][6]

##Aumentar el número máximo de identificadores del sistema para incrementar la simultaneidad
MySQL es una base de datos de alta simultaneidad. El número predeterminado de identificadores simultáneos es de 1024 para Linux. Esta cantidad no siempre es suficiente. **Siga los pasos siguientes para aumentar los identificadores simultáneos máximos del sistema para permitir una elevada concurrencia de MySQL**.

###Paso 1: Modificación del archivo limits.conf
Agregue las cuatro líneas siguientes al archivo /etc/security/limits.conf para incrementar los identificadores simultáneos máximos permitidos. Tenga en cuenta que 65536 es el número máximo que puede admitir el sistema.

	* soft nofile 65536
	* hard nofile 65536
	* soft nproc 65536
	* hard nproc 65536

###Paso 2: Actualización del sistema con los nuevos límites
Ejecute los comandos siguientes:

	ulimit -SHn 65536
	ulimit -SHu 65536

###Paso 3: Asegurarse de que los límites se actualizan durante el arranque
Escriba los siguientes comandos de inicio en el archivo /etc/rc.local para que surtan efecto durante cada tiempo de arranque.

	echo “ulimit -SHn 65536” >>/etc/rc.local
	echo “ulimit -SHu 65536” >>/etc/rc.local

##Optimización de la base de datos de MySQL
Puede usar la misma estrategia de optimización del rendimiento para configurar MySQL en Azure como en un equipo local.

Las principales reglas de optimización de E/S son las siguientes:

-	Aumentar el tamaño de la memoria caché.
-	Reducir el tiempo de respuesta de E/S.  

Para optimizar la configuración del servidor MySQL, puede actualizar el archivo my.cnf, que es el archivo de configuración predeterminado tanto para los equipos cliente como servidor.

Los elementos de configuración siguientes son los principales factores que influyen en el rendimiento de MySQL:

-	**innodb\_buffer\_pool\_size**: El grupo de búferes contiene los datos almacenados en el búfer, así como el índice. Normalmente se establece en el 70% de memoria física.
-	**innodb\_log\_file\_size**: Este es el tamaño de registro de rehacer. Los registros de rehacer se usan para garantizar que las operaciones de escritura son rápidas, confiables y recuperables después de un bloqueo. Se establece en 512 MB, lo que proporcionará una cantidad de espacio suficiente para registrar las operaciones de escritura.
-	**max\_connections**: A veces, las aplicaciones no cierran las conexiones correctamente. Un valor mayor proporciona al servidor más tiempo para reciclar las conexiones inactivas. El número máximo de conexiones es de 10000, pero el máximo recomendado es de 5000.
-	**Innodb\_file\_per\_table**: Esta configuración habilita o deshabilita la posibilidad de InnoDB de almacenar tablas en archivos independientes. Al activar la opción se asegurará de que se pueden aplicar varias operaciones avanzadas de administración eficaces. Desde el punto de vista del rendimiento, puede acelerar la transmisión del espacio de tabla y optimizar el rendimiento de la administración de residuos. Por lo tanto, el valor recomendado para esto es ON.</br> Desde MySQL 5.6, el valor predeterminado es ON. Por lo tanto, no se requiere ninguna acción. Para otras versiones, anteriores a la 5.6, la configuración predeterminada es OFF. Es necesario establecer esta opción en ON. Debe establecerla antes de cargar los datos, ya que solo afecta a las tablas recién creadas.
-	**innodb\_flush\_log\_at\_trx\_commit**: El valor predeterminado es 1, con el ámbito establecido en 0~2. El valor predeterminado es la opción más adecuada para la base de datos MySQL independiente. El valor 2 permite una mayor integridad de datos y es adecuado para Master en clúster de MySQL. El valor 0 permite la pérdida de datos, lo que puede afectar a la confiabilidad, en algunos casos con un mejor rendimiento, y es adecuado para la opción de esclavo en clúster de MySQL.
-	**Innodb\_log\_buffer\_size**: El búfer de registro permite que las transacciones se ejecuten sin tener que vaciar el registro en el disco antes de confirmar las transacciones. Sin embargo, si hay un objeto binario de gran tamaño o un campo de texto, se consumirá la memoria caché muy rápidamente y se activará la E/S de discos frecuentes. Es mejor incrementar el tamaño del búfer si la variable de estado Innodb\_log\_waits no es 0.
-	**query\_cache\_size**: La mejor opción es deshabilitarla desde el principio. Establezca query\_cache\_size en 0 (ahora es el valor predeterminado en MySQL 5.6) y use otros métodos para agilizar las consultas.  

Consulte el [Apéndice D](#AppendixD) para comparar el rendimiento después de la optimización.


##Activar el registro de consultas lentas de MySQL para analizar el cuello de botella de rendimiento
El registro de consultas lentas de MySQL puede ayudarle a identificar las consultas lentas de MySQL. Después de habilitar el registro de consultas lentas de MySQL, puede usar las herramientas de MySQL como **mysqldumpslow** para identificar el cuello de botella de rendimiento.

Tenga en cuenta que no está habilitado de forma predeterminada. Activar el registro de consultas lento puede consumir algunos recursos de CPU. Por lo tanto, se recomienda habilitar esta opción de forma temporal para solucionar los cuellos de botella de rendimiento.

###Paso 1: Modificación del archivo my.cnf agregando las líneas siguientes al final   

	long_query_time = 2
	slow_query_log = 1
	slow_query_log_file = /RAID0/mysql/mysql-slow.log

###Paso 2: Reinicio del servidor mysql
	service  mysql  restart

###Paso 3: Comprobación de si la configuración surte efecto con el comando "show"

![][7]

![][8]

En este ejemplo, puede ver que se ha activado la característica de consulta lenta. De este modo, podrá usar la herramienta **mysqldumpslow** para determinar los cuellos de botella de rendimiento y optimizar el rendimiento como, por ejemplo, la adición de índices.





##Anexo

A continuación se muestran los datos de las pruebas de rendimiento obtenidos en el entorno de laboratorio de destino. Estos datos proporcionan información general acerca de las tendencias de datos de rendimiento con distintos enfoques de optimización del rendimiento. Sin embargo, los resultados pueden variar con las distintas versiones de producto o entorno.

<a name="AppendixA"></a>Apéndice A: **Rendimiento del disco (IOPS) con los distintos niveles de RAID**


![][9]

**Comandos de prueba:**

	fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

>AZURE.NOTE: La carga de trabajo de esta prueba usa 64 subprocesos para intentar alcanzar el límite superior de RAID.

<a name="AppendixB"></a>Apéndice B: **Comparación de rendimiento de MySQL con distintos niveles de RAID** (sistema de archivos XFS)


![][10] ![][11]

**Comandos de prueba:**

	mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**Comparación de rendimiento (OLTP) de MySQL con distintos niveles de RAID** ![][12]

**Comandos de prueba:**

	time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

<a name="AppendixC"></a>Apéndice C: **Comparación de rendimiento (IOPS) de disco con distintos tamaños de fragmento** (sistema de archivos XFS)


![][13]

**Comandos de prueba:**

	fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
	fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

Tenga en cuenta que el tamaño de archivo utilizado para esta prueba es de 30 GB y 1 GB respectivamente, con el sistema de archivos XFS RAID 0 (4 discos).


<a name="AppendixD"></a>Apéndice D: **Comparación de rendimiento de MySQL antes y después de la optimización** (sistema de archivos XFS)


![][14]

**Comandos de prueba:**

	mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**Los valores de configuración predeterminada y de optimización son los siguientes:**

|Parámetros |Valor predeterminado |optimización
|-----------|-----------|-----------
|**innodb\_buffer\_pool\_size** |None |7G
|**innodb\_log\_file\_size** |5M |512M
|**max\_connections** |100 |5\.000
|**innodb\_file\_per\_table** |0 |1
|**innodb\_flush\_log\_at\_trx\_commit** |1 |2
|**innodb\_log\_buffer\_size** |8M |128M
|**query\_cache\_size** |16M |0


Consulte las instrucciones oficiales de mysql para obtener parámetros de configuración de optimización más detallados.

[http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html)

[http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar\_innodb\_flush\_method](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method)

**Entorno de prueba**

|Hardware |Detalles
|-----------|-------
|Cpu |Procesador AMD Opteron(tm) 4171 HE/4 núcleos
|Memoria |14G
|disk |10G/disco
|SO |Ubuntu 14.04.1 LTS



[1]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-01.png
[2]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-02.png
[3]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-03.png
[4]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-04.png
[5]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-05.png
[6]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-06.png
[7]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-07.png
[8]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-08.png
[9]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-09.png
[10]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-10.png
[11]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-11.png
[12]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-12.png
[13]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-13.png
[14]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-14.png

<!---HONumber=AcomDC_1223_2015-->