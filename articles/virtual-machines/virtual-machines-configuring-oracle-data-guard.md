<properties
	pageTitle="Configuración de la protección de datos de Oracle en máquinas virtuales | Microsoft Azure"
	description="Realice un tutorial para configurar e implementar la protección de datos de Oracle en máquinas virtuales de Azure para obtener alta disponibilidad y recuperación ante desastres."
	services="virtual-machines"
	authors="bbenz"
	documentationCenter=""
	tags="azure-service-management"/>
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz" />

#Configuración de la protección de datos de Oracle para Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


En este tutorial se muestra cómo instalar e implementar la protección de datos de Oracle en un entorno de máquinas virtuales de Azure para obtener una alta disponibilidad y recuperación ante desastres. El tutorial se centra en la replicación unidireccional para bases de datos de Oracle distintas de RAC.

Protección de datos de Oracle admite la protección de datos y la recuperación ante desastres para bases de datos Oracle. Se trata de una solución sencilla, de alto rendimiento directa para la recuperación ante desastres, la protección de datos y la obtención de alta disponibilidad para toda la base de datos de Oracle.

En este tutorial se supone que ya tiene conocimientos teóricos y prácticos sobre conceptos de recuperación ante desastres y alta disponibilidad de la base de datos de Oracle. Para obtener información, consulte el [sitio web de Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html) y también los [Conceptos de protección de datos y la Guía de administración de Oracle](http://docs.oracle.com/cd/E11882_01/server.112/e17022/create_ps.htm).

Además, en el tutorial se supone que ya se han implementado los siguientes requisitos previos:

- Ya ha revisado la sección Consideraciones de recuperación ante desastres y de alta disponibilidad en el tema [Imágenes de máquina virtual de Oracle: consideraciones variadas](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md). Tenga en cuenta que actualmente Azure es compatible con las instancias de base de datos de Oracle independientes, pero no con los clústeres de aplicación reales de Oracle (RAC de Oracle).

- Ha creado dos máquinas virtuales (VM) en Azure con la misma plataforma que proporciona la imagen de Oracle Enterprise Edition en Windows Server. Para obtener información, consulte [Creación de una máquina virtual de base de datos de Oracle 12c en Azure](virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine.md) y [Máquinas virtuales de Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Asegúrese de que las máquinas virtuales estén en el [mismo servicio en la nube](virtual-machines-load-balance.md) y en la misma [red virtual](azure.microsoft.com/documentation/services/virtual-network/) para asegurarse de que pueden tener acceso entre sí a través de la dirección IP privada persistente. Además, se recomienda colocar las máquinas virtuales en el mismo [conjunto de disponibilidad](virtual-machines-manage-availability.md) para permitir a Azure colocarlas en dominios de error y de actualización independientes. Tenga en cuenta que la protección de datos de Oracle solo está disponible con Oracle Database Enterprise Edition. Cada máquina debe tener al menos 2 GB de memoria y 5 GB de espacio en disco. Para obtener la información más actualizada sobre los tamaños de máquina virtual proporcionados por la plataforma, consulte [Tamaños de máquina virtual de Azure](http://msdn.microsoft.com/library/dn197896.aspx). Si necesita un volumen de disco adicional para las máquinas virtuales, puede conectar discos adicionales. Para obtener información, consulte [Acoplamiento de un disco de datos a una máquina virtual](storage-windows-attach-disk.md).

- Ha establecido los nombres de máquina virtual "Machine1" para la máquina virtual principal y "Machine2" para la máquina virtual en espera en el Portal de Azure clásico.

- Ha establecido la variable de entorno **ORACLE\_HOME** para que señale a la misma ruta de instalación de raíz de Oracle en las máquinas virtuales principal y en espera, como `C:\OracleDatabase\product\11.2.0\dbhome_1\database`.

- Inicie la sesión en el servidor de Windows como miembro del grupo de **Administradores** o como miembro del grupo **ORA\_DBA**.

En este tutorial, aprenderá lo siguiente:

Implementar el entorno físico de base de datos en espera

1. Crear una base de datos principal

2. Preparar la base de datos principal para la creación de la base de datos en espera

	1. Habilitar el registro forzado

	2. Crear un archivo de contraseña

	3. Configurar un registro de repetición de puesta en espera

	4. Habilitar archivado

	5. Establecer parámetros de inicialización de la base de datos principal

Crear una base de datos física en espera

1. Preparar un archivo de parámetros de inicialización de base de datos en espera

2. Configurar el agente de escucha y tnsnames para admitir la base de datos en los equipos principales y en espera

	1. Configurar listener.ora en ambos servidores para mantener las entradas para ambas bases de datos

	2. Configurar tnsnames.ora en las máquinas virtuales principal y en espera para contener las entradas para las bases de datos principales y en espera

	3. Inicie el agente de escucha y compruebe tnsping de ambas máquinas virtuales en ambos servicios.

3. Inicie la instancia en modo de espera en estado nomount

4. Utilice RMAN para clonar la base de datos y crear una base de datos en espera

5. Iniciar la base de datos física en espera en modo de recuperación administrado

6. Compruebe la base de datos física en espera

> [AZURE.IMPORTANT] Este tutorial se ha configurado y probado con la siguiente configuración de hardware y software:
>
>| | **Base de datos principal** | **Base de datos en espera** |
>|----------------------|-------------------------------------------|-------------------------------------------|
>| **Versión de Oracle** | Versión de Oracle11g Enterprise (11.2.0.4.0) | Versión de Oracle11g Enterprise (11.2.0.4.0) |
>| **Nombre de máquina** | Machine1 | Machine2 |
>| **Sistema operativo** | Windows 2008 R2 | Windows 2008 R2 |
>| **SID de Oracle** | PRUEBA | TEST\_STBY |
>| **Memoria** | Mín: 2 GB | Mín: 2 GB |
>| **Espacio en disco** | Mín: 5 GB | Mín: 5 GB |

En versiones posteriores de la Base de datos de Oracle y de la Protección de datos de Oracle, es posible que haya algunos cambios adicionales que deba implementar. Para consultar la información específica de versión más actualizada, consulte la documentación sobre [protección de datos](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) y [base de datos de Oracle](http://www.oracle.com/us/corporate/features/database-12c/index.html) en el sitio web de Oracle.

##Implementar el entorno físico de base de datos en espera
### 1\. Crear una base de datos principal

- Cree una base de datos principal "TEST" en la máquina Virtual principal. Para obtener información, consulte Crear y configurar una base de datos de Oracle.
- Conéctese a la base de datos como usuario SYS con el rol SYSDBA en el símbolo del sistema SQL*Plus y ejecute la instrucción siguiente para ver el nombre de la base de datos:

		SQL> select name from v$database;

		The result will display like the following:

		NAME
		---------
		TEST
- A continuación, consulte los nombres de los archivos de base de datos de la vista del sistema dba\_data\_files:

		SQL> select file_name from dba_data_files;
		FILE_NAME
		-------------------------------------------------------------------------------
		C:\ <YourLocalFolder>\TEST\USERS01.DBF
		C:\ <YourLocalFolder>\TEST\UNDOTBS01.DBF
		C:\ <YourLocalFolder>\TEST\SYSAUX01.DBF
		C:<YourLocalFolder>\TEST\SYSTEM01.DBF
		C:<YourLocalFolder>\TEST\EXAMPLE01.DBF

### 2\. Preparar la base de datos principal para la creación de la base de datos en espera

Antes de crear una base de datos en espera, es recomendable asegurarse de que la base de datos principal está configurada correctamente. A continuación se facilita una lista de los pasos que debe realizar:

1. Habilitar el registro forzado
2. Crear un archivo de contraseña
3. Configurar un registro de repetición de puesta en espera
4. Habilitar archivado
5. Establecer parámetros de inicialización de la base de datos principal

#### Habilitar el registro forzado

Para implementar una base de datos en espera, es necesario habilitar el "Registro forzado" en la base de datos principal. Esta opción garantiza que incluso en caso de que se realice una operación de “no registro”, forzar el registro tendrá prioridad y se registrarán todas las operaciones en los registros de rehacer. Por lo tanto, nos aseguramos de que todo el contenido de la base de datos principal se registre y que la replicación en el servidor de espera incluya todas las operaciones de la base de datos principal. Ejecute la instrucción de alteración de la base de datos para habilitar el registro forzado:

	SQL> ALTER DATABASE FORCE LOGGING;

	Database altered.

#### Crear un archivo de contraseña

Para poder enviar y aplicar los registros archivados desde el servidor principal al servidor en espera, la contraseña sys debe ser idéntica en los servidores principal y en espera. Ese es el motivo por el que se debe crear un archivo de contraseña en la base de datos principal y copiarlo en el servidor en espera.

>[AZURE.IMPORTANT] Cuando se usa Oracle Database 12c, hay un nuevo usuario, **SYSDG**, que puede usar para administrar la protección de datos de Oracle. Para obtener más información, consulte los [cambios en la versión de Oracle Database 12ce](http://docs.oracle.com/cd/E16655_01/server.121/e10638/release_changes.htm).

Además, asegúrese de que el entorno de ORACLE\_HOME ya esté definido en Machine1. Si no es así, deberá definirlo como una variable de entorno mediante el cuadro de diálogo Variables de entorno. Para tener acceso a este cuadro de diálogo, inicie la utilidad **System** haciendo doble clic en el icono del sistema del **Panel de control**; a continuación, haga clic en la pestaña **Avanzado** y elija **Variables de entorno**. Haga clic en el botón **Nuevo** en **Variables del sistema** para establecer las variables de entorno. Después de configurar las variables de entorno, cierre el símbolo del sistema de Windows existente y abra uno nuevo.

Ejecute la instrucción siguiente para cambiar al directorio Oracle\_Home, por ejemplo, C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\database.

	cd %ORACLE_HOME%\database

A continuación, cree un archivo de contraseña mediante la utilidad de creación de archivos de contraseña, [ORAPWD](http://docs.oracle.com/cd/B28359_01/server.111/b28310/dba007.htm). En el mismo símbolo del sistema de Windows en Machine1, ejecute el siguiente comando estableciendo el valor de contraseña como la contraseña de **SYS**:

	ORAPWD FILE=PWDTEST.ora PASSWORD=password FORCE=y

Este comando crea un archivo de contraseña, denominado PWDTEST.ora, en el directorio ORACLE\_HOME\\database. Debe copiar manualmente este archivo en el directorio %ORACLE\_HOME%\\database en Máquina2.

#### Configurar un registro de repetición de puesta en espera

A continuación, deberá configurar un registro de rehacer en espera para que el elemento principal pueda recibir correctamente la puesta al día cuando se convierta en un estado de espera. Crearlos previamente aquí también permite crear registros de rehacer en espera automáticamente en el modo de espera. Es importante configurar los registros de rehacer en espera (SRL) con el mismo tamaño que los registros de rehacer en línea. El tamaño de los archivos de registro de rehacer en espera actuales debe coincidir exactamente con el tamaño de los archivos de registro de rehacer en línea de base de datos principal actuales.

Ejecute la siguiente instrucción en el símbolo del sistema SQL*PLUS en Machine1. v$logfile es una vista del sistema que contiene información acerca de los archivos de registro de rehacer.

	SQL> select * from v$logfile;
	GROUP# STATUS  TYPE    MEMBER                                                       IS_
	---------- ------- ------- ------------------------------------------------------------ ---
	3         ONLINE  C:<YourLocalFolder>\TEST\REDO03.LOG               NO
	2         ONLINE  C:<YourLocalFolder>\TEST\REDO02.LOG               NO
	1         ONLINE  C:<YourLocalFolder>\TEST\REDO01.LOG               NO
	Next, query the v$log system view, displays log file information from the control file.
	SQL> select bytes from v$log;
	BYTES
	----------
	52428800
	52428800
	52428800


Tenga en cuenta que 52428800 son 50 megabytes.

A continuación, en la ventana SQL*Plus, ejecute las instrucciones siguientes para agregar un nuevo grupo de archivos de registro de rehacer en espera a una base de datos en espera y especifique un número que identifica el grupo mediante la cláusula GROUP. Con números de grupo, puede simplificar el proceso de administración de grupos de archivos de registro de rehacer en espera:

	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 4 'C:<YourLocalFolder>\TEST\REDO04.LOG' SIZE 50M;
	Database altered.
	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 5 'C:<YourLocalFolder>\TEST\REDO05.LOG' SIZE 50M;
	Database altered.
	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 6 'C:<YourLocalFolder>\TEST\REDO06.LOG' SIZE 50M;
	Database altered.

A continuación, ejecute la siguiente vista del sistema para mostrar información acerca de los archivos de registro de rehacer. Esta operación también comprueba que se han creado los grupos de archivos de registro de rehacer en espera:

	SQL> select * from v$logfile;
	GROUP# STATUS  TYPE MEMBER IS_
	---------- ------- ------- --------------------------------------------- ---
	3         ONLINE C:<YourLocalFolder>\TEST\REDO03.LOG NO
	2         ONLINE C:<YourLocalFolder>\TEST\REDO02.LOG NO
	1         ONLINE C:<YourLocalFolder>\TEST\REDO01.LOG NO
	4         STANDBY C:<YourLocalFolder>\TEST\REDO04.LOG
	5         STANDBY C:<YourLocalFolder>\TEST\REDO05.LOG NO
	6         STANDBY C:<YourLocalFolder>\TEST\REDO06.LOG NO
	6 rows selected.

#### Habilitar archivado

A continuación, habilite el archivado ejecutando las siguientes instrucciones para poner la base de datos principal en modo ARCHIVELOG y habilitar el archivado automático. Puede habilitar el modo de registro de archivado montando la base de datos y, a continuación, ejecutando el comando archivelog.

En primer lugar, inicie sesión como sysdba. En el símbolo del sistema de Windows, ejecute:

	sqlplus /nolog

	connect / as sysdba

A continuación, cierre la base de datos en el símbolo del sistema SQL*Plus:

	SQL> shutdown immediate;
	Database closed.
	Database dismounted.
	ORACLE instance shut down.

A continuación, ejecute el comando de montaje de inicio para montar la base de datos. Esto garantiza que Oracle asocie la instancia a la base de datos especificada.

	SQL> startup mount;
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes
	Database mounted.

A continuación, ejecute:

	SQL> alter database archivelog;
	Database altered.

A continuación, ejecute la instrucción de alteración de la base de datos con la cláusula abierta para que la base de datos esté disponible para el uso normal:

	SQL> alter database open;

	Database altered.

#### Establecer parámetros de inicialización de la base de datos principal

Para configurar la protección de datos, primero deberá crear y configurar los parámetros en espera en un pfile regular (archivo de parámetros de inicialización de texto). Cuando el pfile esté listo, deberá convertirlo en un archivo de parámetro de servidor (SPFILE).

Puede controlar el entorno de protección de datos usando los parámetros del archivo INIT.ORA. Al seguir este tutorial, deberá actualizar la base de datos principal INIT.ORA para que pueda almacenar ambos roles: principal o en espera.

	SQL> create pfile from spfile;
	File created.

A continuación, deberá modificar el pfile para agregar los parámetros en espera. Para ello, abra el archivo INITTEST.ORA en la ubicación de %ORACLE\_HOME%\\database. A continuación, agregue las siguientes instrucciones al archivo INITTEST.ora. Tenga en cuenta que la convención de nomenclatura para el archivo INIT.ORA es INIT< YourDatabaseName >.ORA.

	db_name='TEST'
	db_unique_name='TEST'
	LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
	LOG_ARCHIVE_DEST_1= 'LOCATION=C:\OracleDatabase\archive   VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=TEST'
	LOG_ARCHIVE_DEST_2= 'SERVICE=TEST_STBY LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=TEST_STBY'
	LOG_ARCHIVE_DEST_STATE_1=ENABLE
	LOG_ARCHIVE_DEST_STATE_2=ENABLE
	REMOTE_LOGIN_PASSWORDFILE=EXCLUSIVE
	LOG_ARCHIVE_FORMAT=%t_%s_%r.arc
	LOG_ARCHIVE_MAX_PROCESSES=30
	# Standby role parameters --------------------------------------------------------------------
	fal_server=TEST_STBY
	fal_client=TEST
	standby_file_management=auto
	db_file_name_convert='TEST_STBY','TEST'
	log_file_name_convert='TEST_STBY','TEST'
	# ---------------------------------------------------------------------------------------------


El bloque de la instrucción anterior incluye tres elementos de configuración importantes: - **LOG\_ARCHIVE\_CONFIG...:** defina los identificadores únicos de la base de datos usando esta instrucción. - **LOG\_ARCHIVE\_DEST\_1...:** defina la ubicación de la carpeta de archivo local con esta instrucción. Es recomendable crear un nuevo directorio para las necesidades de archivado de su base de datos y especificar la ubicación del archivo local con esta instrucción de forma explícita, en lugar de usar la carpeta predeterminada de Oracle %ORACLE\_HOME%\\database\\archive. - **LOG\_ARCHIVE\_DEST\_2 .... LGWR ASYNC...:** defina un proceso de escritura de registro asincrónico (LGWR) para recopilar datos de puesta al día de transacción y transmitirlos a destinos en espera. En este caso, DB\_UNIQUE\_NAME especifica un nombre único para la base de datos del servidor en modo de espera de destino.

Una vez preparado el nuevo archivo de parámetros, deberá crear el spfile a partir de él.

Primero, cierre la base de datos:

	SQL> shutdown immediate;

	Database closed.

	Database dismounted.

	ORACLE instance shut down.

A continuación, ejecute el comando nomount de inicio como sigue:

	SQL> startup nomount pfile='c:\OracleDatabase\product\11.2.0\dbhome_1\database\initTEST.ora';
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes

Ahora, cree un spfile:

	SQL>create spfile frompfile='c:\OracleDatabase\product\11.2.0\dbhome\_1\database\initTEST.ora';

	File created.

A continuación, cierre la base de datos:

	SQL> shutdown immediate;

	ORA-01507: database not mounted

A continuación, use el comando de inicio para iniciar una instancia:

	SQL> startup;
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes
	Database mounted.
	Database opened.

##Crear una base de datos física en espera
Esta sección se centra en los pasos que se deben realizar en Machine2 para preparar la base de datos física en espera.

En primer lugar, necesitará un escritorio remoto en Machine2 a través del Portal de Azure clásico.

A continuación, en el servidor de espera (Machine2), cree todas las carpetas necesarias para la base de datos en espera, por ejemplo, C:\\<YourLocalFolder>\\TEST. Mientras sigue este tutorial, asegúrese de que la estructura de carpetas coincida con la estructura de carpetas de Machine1 para mantener todos los archivos necesarios, como archivos de control, archivos de datos, archivos de registros de rehacer, archivos bdump y cdump. Además, puede definir las variables del entorno ORACLE\_HOME y ORACLE\_BASE en Machine2. Si no es así, deberá definirlos como una variable de entorno mediante el cuadro de diálogo Variables de entorno. Para tener acceso a este cuadro de diálogo, inicie la utilidad **System** haciendo doble clic en el icono del sistema del **Panel de control**; a continuación, haga clic en la pestaña **Avanzado** y elija **Variables de entorno**. Haga clic en el botón **Nuevo** en **Variables del sistema** para establecer las variables de entorno. Después de configurar las variables de entorno, cierre el símbolo del sistema de Windows existente y abra uno nuevo para ver los cambios.

A continuación, siga estos pasos:

1. Preparar un archivo de parámetros de inicialización de base de datos en espera

2. Configurar el agente de escucha y tnsnames para admitir la base de datos en los equipos principales y en espera

	1. Configurar listener.ora en ambos servidores para mantener las entradas para ambas bases de datos

	2. Configurar tnsnames.ora en las máquinas virtuales principal y en espera para contener las entradas para las bases de datos principales y en espera

	3. Inicie el agente de escucha y compruebe tnsping de ambas máquinas virtuales en ambos servicios.

3. Inicie la instancia en modo de espera en estado nomount

4. Utilice RMAN para clonar la base de datos y crear una base de datos en espera

5. Iniciar la base de datos física en espera en modo de recuperación administrado

6. Compruebe la base de datos física en espera

### 1\. Preparar un archivo de parámetros de inicialización de base de datos en espera

En esta sección se muestra cómo preparar un archivo de parámetros de inicialización para la base de datos en espera. Para ello, copie primero el archivo INITTEST. ORA de Machine1 en Machine2 manualmente. Deberá ser capaz de ver el archivo INITTEST. ORA en la carpeta %ORACLE\_HOME%\\database en ambos equipos. A continuación, modifique el archivo INITTEST.ora en Machine2 para configurarlo para el rol en espera tal y como se especifica a continuación:

	db_name='TEST'
	db_unique_name='TEST_STBY'
	db_create_file_dest='c:\OracleDatabase\oradata\test_stby’
	db_file_name_convert=’TEST’,’TEST_STBY’
	log_file_name_convert='TEST','TEST_STBY'


	job_queue_processes=10
	LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
	LOG_ARCHIVE_DEST_1='LOCATION=c:\OracleDatabase\TEST_STBY\archives VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=’TEST'
	LOG_ARCHIVE_DEST_2='SERVICE=TEST LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE)
	LOG_ARCHIVE_DEST_STATE_1='ENABLE'
	LOG_ARCHIVE_DEST_STATE_2='ENABLE'
	LOG_ARCHIVE_FORMAT='%t_%s_%r.arc'
	LOG_ARCHIVE_MAX_PROCESSES=30


El bloque de la instrucción anterior incluye dos elementos de configuración importantes:

-	***.LOG\_ARCHIVE\_DEST\_1:** tendrá que crear manualmente la carpeta c:\\OracleDatabase\\TEST\_STBY\\archives en Machine2.
-	***.LOG\_ARCHIVE\_DEST\_2:** se trata de un paso opcional. Debe establecer esto como si fuese necesario cuando el equipo principal está en mantenimiento y el equipo en espera se convierte en una base de datos principal.

A continuación, deberá iniciar la instancia en modo de espera. En el servidor de la base de datos en espera, escriba el siguiente comando en un símbolo del sistema de Windows para crear una instancia de Oracle mediante la creación de un nuevo servicio de Windows:

	oradim -NEW -SID TEST\_STBY -STARTMODE MANUAL

Tenga en cuenta que el comando **Oradim** crea una instancia de Oracle, pero no la inicia. Puede encontrarla en el directorio C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\BIN.

##Configurar el agente de escucha y tnsnames para admitir la base de datos en los equipos principales y en espera
Antes de crear una base de datos en espera, tiene que asegurarse de que las bases de datos principal y en espera de la configuración puedan comunicarse entre sí. Para ello, deberá configurar el agente de escucha y TNSNames manualmente o mediante la utilidad de configuración de red NETCA. Esta es una tarea obligatoria cuando se utiliza la utilidad Administrador de recuperación (RMAN).

### Configurar listener.ora en ambos servidores para mantener las entradas para ambas bases de datos

Establezca un escritorio remoto a Machine1 y edite el archivo listener.ora como se especifica a continuación. Cuando edite el archivo listener.ora, asegúrese siempre de que los paréntesis de apertura y cierre se alineen en la misma columna. Puede encontrar el archivo listener.ora en la siguiente carpeta: c:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\NETWORK\\ADMIN\\.

	# listener.ora Network Configuration File: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

	# Generated by Oracle configuration tools.

	SID_LIST_LISTENER =
	  (SID_LIST =
	    (SID_DESC =
	      (SID_NAME = test)
	      (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
	      (PROGRAM = extproc)
	      (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
	    )
	  )

	LISTENER =
	  (DESCRIPTION_LIST =
	    (DESCRIPTION =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
	    )
	  )

A continuación, establezca un escritorio remoto a Machine2 y edite el archivo listener.ora del modo indicado a continuación: archivo de configuración de red de # listener.ora: C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\network\\admin\\listener.ora

	# Generated by Oracle configuration tools.

	SID_LIST_LISTENER =
	  (SID_LIST =
	    (SID_DESC =
	      (SID_NAME = test_stby)
	      (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
	      (PROGRAM = extproc)
	      (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
	    )
	  )

	LISTENER =
	  (DESCRIPTION_LIST =
	    (DESCRIPTION =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
	    )
	  )


### Configurar tnsnames.ora en las máquinas virtuales principal y en espera para contener las entradas para las bases de datos principales y en espera

Establezca un escritorio remoto a Machine1 y edite el archivo tnsnames.ora como se especifica a continuación. Puede encontrar el archivo tnsnames.ora en la siguiente carpeta: c:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\NETWORK\\ADMIN\\.

	TEST =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test)
	    )
	  )

	TEST_STBY =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test_stby)
	    )
	  )

Establezca un escritorio remoto a Machine2 y edite el archivo tnsnames.ora como se especifica a continuación:

	TEST =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test)
	    )
	  )

	TEST_STBY =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test_stby)
	    )
	  )


### Inicie el agente de escucha y compruebe tnsping de ambas máquinas virtuales en ambos servicios.

Abra un nuevo símbolo del sistema de Windows en máquinas virtuales principales y en espera y ejecute las instrucciones siguientes:

	C:\Users\DBAdmin>tnsping test

	TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:08
	Copyright (c) 1997, 2010, Oracle.  All rights reserved.
	Used parameter files:
	C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
	Used TNSNAMES adapter to resolve the alias
	Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))) (CONNECT_DATA = (SER
	VICE_NAME = test)))
	OK (0 msec)


	C:\Users\DBAdmin>tnsping test_stby

	TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:16
	Copyright (c) 1997, 2010, Oracle.  All rights reserved.
	Used parameter files:
	C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
	Used TNSNAMES adapter to resolve the alias
	Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))) (CONNECT_DATA = (SER
	VICE_NAME = test_stby)))
	OK (260 msec)


##Inicie la instancia en modo de espera en estado nomount
Deberá configurar el entorno para admitir la base de datos en espera en la máquina virtual (MACHINE2) en espera.

En primer lugar, copie el archivo de contraseña desde el equipo principal (Machine1) en el equipo en espera (Machine2) manualmente. Esto es necesario, ya que la contraseña **sys** debe ser idéntica en ambos equipos.

A continuación, abra el símbolo del sistema de Windows en Machine2 y configure las variables de entorno para que apunten a la base de datos en espera del modo indicado a continuación:

	SET ORACLE_HOME=C:\OracleDatabase\product\11.2.0\dbhome_1
	SET ORACLE_SID=TEST_STBY

A continuación, inicie la base de datos en espera que se encuentra en el estado nomount y, a continuación, genere un spfile.

Inicie la base de datos:

	SQL>shutdown immediate;

	SQL>startup nomount
	ORACLE instance started.

	Total System Global Area  747417600 bytes
	Fixed Size                  2179496 bytes
	Variable Size             473960024 bytes
	Database Buffers          264241152 bytes
	Redo Buffers                7036928 bytes


##Utilice RMAN para clonar la base de datos y crear una base de datos en espera
Puede usar la utilidad Administrador de recuperación (RMAN) para realizar cualquier copia de seguridad de la base de datos principal para crear la base de datos física en espera.

Establezca un escritorio remoto a la máquina virtual en modo de espera (MACHINE2) y ejecute la utilidad RMAN especificando una cadena de conexión completa para las instancias TARGET (base de datos principal, Machine1) y AUXILLARY (database en espera, Machine2).

>[AZURE.IMPORTANT] No use la autenticación de sistema operativo ya que no hay todavía ninguna base de datos en el equipo del servidor en espera.

	C:\> RMAN TARGET sys/password@test AUXILIARY sys/password@test_STBY

	RMAN>DUPLICATE TARGET DATABASE
	  FOR STANDBY
	  FROM ACTIVE DATABASE
	  DORECOVER
	    NOFILENAMECHECK;

##Iniciar la base de datos física en espera en modo de recuperación administrado
Este tutorial muestra cómo crear una base de datos física en espera. Para obtener información sobre la creación de una base de datos en espera lógica, consulte la documentación de Oracle.

Abra el símbolo del sistema SQL*Plus y habilite la protección de datos en la máquina virtual en espera o el servidor (MACHINE2) del modo indicado a continuación:

	SHUTDOWN IMMEDIATE;
	STARTUP MOUNT;
	ALTER DATABASE RECOVER MANAGED STANDBY DATABASE DISCONNECT FROM SESSION;

Al abrir la base de datos en espera en el modo **MOUNT**, el envío del registro del archivo continúa y el proceso de recuperación administrado continúa el registro aplicando la base de datos en espera. Esto garantiza que la base de datos en espera permanezca actualizada con la base de datos principal. Tenga en cuenta que no se puede acceder a la base de datos en espera para la elaboración de informes durante este tiempo.

Al abrir la base de datos en espera en modo de **SOLO LECTURA**, el envío del registro de archivo continúa. Pero se detiene el proceso de recuperación administrado. Esto hace que la base de datos en espera esté cada vez más desactualizada hasta que se reanuda el proceso de recuperación administrado. Puede tener acceso a la base de datos en espera para la elaboración de informes durante este período, pero los datos pueden no reflejar los cambios más recientes.

En general, se recomienda mantener la base de datos en espera en modo **MOUNT** para mantener actualizados los datos en la base de datos en espera en caso de error de la base de datos principal. Sin embargo, puede mantener la base de datos en espera en modo de **SOLO LECTURA** para la elaboración de informes en función de los requisitos de su aplicación. Los pasos siguientes muestran cómo habilitar la protección de datos en modo de solo lectura mediante SQL*Plus:

	SHUTDOWN IMMEDIATE;
	STARTUP MOUNT;
	ALTER DATABASE OPEN READ ONLY;


##Compruebe la base de datos física en espera
En esta sección se muestra cómo comprobar la configuración de alta disponibilidad como administrador.

Abra la ventana del símbolo del sistema SQL*Plus y compruebe el registro de rehacer archivado en la máquina virtual de modo de espera (Machine2):

	SQL> show parameters db_unique_name;

	NAME                                TYPE       VALUE
	------------------------------------ ----------- ------------------------------
	db_unique_name                      string     TEST_STBY

	SQL> SELECT NAME FROM V$DATABASE

	SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

	SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
	----------------  ---------------  --------------- ------------
	45                    23-FEB-14   23-FEB-14   YES
	45                    23-FEB-14   23-FEB-14   NO
	46                    23-FEB-14   23-FEB-14   NO
	46                    23-FEB-14   23-FEB-14   YES
	47                    23-FEB-14   23-FEB-14   NO
	47                    23-FEB-14   23-FEB-14   NO

Abra la ventana del símbolo del sistema SQL*Plus y cambie los archivos de registro en la máquina principal (Machine1):

	SQL> alter system switch logfile;
	System altered.

	SQL> archive log list
	Database log mode              Archive Mode
	Automatic archival             Enabled
	Archive destination            C:\OracleDatabase\archive
	Oldest online log sequence     69
	Next log sequence to archive   71
	Current log sequence           71

Compruebe el registro de rehacer archivado en la máquina virtual de modo de espera (Machine2):

	SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

	SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
	----------------  ---------------  --------------- ------------
	45                    23-FEB-14   23-FEB-14   YES
	46                    23-FEB-14   23-FEB-14   YES
	47                    23-FEB-14   23-FEB-14   YES
	48                    23-FEB-14   23-FEB-14   YES

	49                    23-FEB-14   23-FEB-14   YES
	50                    23-FEB-14   23-FEB-14   IN-MEMORY

Compruebe si hay brechas en la máquina virtual de modo de espera (Machine2):

	SQL> SELECT * FROM V$ARCHIVE_GAP;
	no rows selected.

Otro método de verificación podría ser la conmutación por error en la base de datos en espera y, a continuación, probar si es posible efectuar la conmutación por recuperación en la base de datos principal. Para activar la base de datos en espera como una base de datos principal, use las instrucciones siguientes:

	SQL> ALTER DATABASE RECOVER MANAGED STANDBY DATABASE FINISH;
	SQL> ALTER DATABASE ACTIVATE STANDBY DATABASE;

Si no ha habilitado flashback en la base de datos principal original, es recomendable colocar la base de datos principal original y volver a crearla como una base de datos en espera.

Es recomendable habilitar la base de datos flashback en las bases de datos principal y en espera. Cuando se produce una conmutación por error, la base de datos principal puede restablecerse al momento anterior a la conmutación por error y convertirse rápidamente en una base de datos en espera.

##Recursos adicionales
[Imágenes de máquina virtual de Oracle para Azure](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

<!---HONumber=AcomDC_0128_2016-->