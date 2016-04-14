<properties
	pageTitle="Configuración de Oracle GoldenGate en máquinas virtuales | Microsoft Azure"
	description="Realice un tutorial para configurar e implementar Oracle GoldenGate en máquinas virtuales de Azure Windows Server para obtener alta disponibilidad y recuperación ante desastres."
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


#Configuración de Oracle GoldenGate para Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


En este tutorial se muestra cómo configurar Oracle GoldenGate para el entorno de máquinas virtuales de Azure para obtener una alta disponibilidad y recuperación ante desastres. El tutorial se centra en la [replicación bidireccional](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_about_gg.htm) para bases de datos distintas de RAC Oracle y requiere que ambos sitios estén activos.

Oracle GoldenGate admite la distribución e integración de datos. Le permite configurar una distribución de datos y una solución de sincronización de datos a través de la configuración de replicación de Oracle a Oracle y proporciona una solución flexible de alta disponibilidad. Oracle GoldenGate complementa la protección de datos de Oracle con sus capacidades de replicación para permitir las distribución de información por toda la empresa y migraciones y actualizaciones sin interrupciones. Para obtener información detallada, consulte [Uso de Oracle GoldenGate con la protección de datos de Oracle](http://docs.oracle.com/cd/E11882_01/server.112/e17157/unplanned.htm).

Oracle GoldenGate contiene los siguientes componentes principales: Extract, bombeo de datos, réplica o archivos de extracción, puntos de comprobación, administrador y el recopilador. Para disponer de replicación bidireccional entre dos sitios, deberá crear e iniciar todos los componentes en ambos sitios. Para obtener información detallada sobre la arquitectura de Oracle GoldenGate, consulte la [Guía de Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html).

En este tutorial se supone que ya tiene conocimientos teóricos y prácticos sobre conceptos de recuperación ante desastres y alta disponibilidad de la base de datos de Oracle, así como de [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html). Para obtener más información, consulte el [sitio web de Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html).

Además, en el tutorial se supone que ya se han implementado los siguientes requisitos previos:

- Ya ha revisado la sección Consideraciones de recuperación ante desastres y de alta disponibilidad en el tema [Imágenes de máquina virtual de Oracle: consideraciones variadas](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md). Tenga en cuenta que actualmente Azure es compatible con las instancias de base de datos de Oracle independientes, pero no con los clústeres de aplicación reales de Oracle (RAC de Oracle).

- Que haya descargado el software de Oracle GoldenGate del sitio web de [Descargas de Oracle](http://www.oracle.com/us/downloads/index.html). Que haya seleccionado el Product Pack Oracle Fusion Middleware: integración de datos. A continuación, ha seleccionado Oracle GoldenGate en Oracle v11.2.1 Media Pack para Microsoft Windows x 64 (64 bits) para una base de datos Oracle 11g. A continuación, descargue Oracle GoldenGate V11.2.1.0.3 para Oracle 11g de 64 bits en Windows 2008 (64 bits).

- Que haya creado dos máquinas virtuales en Azure con la plataforma que proporciona la imagen de Oracle Enterprise Edition en Windows Server. Para obtener información, consulte [Creación de una máquina virtual de base de datos de Oracle 12c en Azure](#z3dc8d3c097cf414e9048f7a89c026f80) y [Máquinas virtuales de Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Asegúrese de que las máquinas virtuales estén en el [mismo servicio en la nube](virtual-machines-load-balance.md) y en la misma [red virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para asegurarse de que pueden tener acceso entre sí a través de la dirección IP privada persistente.

- Que haya establecido los nombres de las máquinas virtuales como "MachineGG1" en el sitio A y "MachineGG2" en el sitio B en el Portal de Azure clásico.

- Que haya creado las bases de datos de prueba "TestGG1" en el sitio A y "TestGG2" en el sitio B.

- Inicie la sesión en el servidor de Windows como miembro del grupo de Administradores o como miembro del grupo **ORA\_DBA**.

En este tutorial, aprenderá lo siguiente:

1. Configurar la base de datos en los sitios A y B  

	1. Realizar la carga inicial de datos

2. Preparar los sitios A y B para la replicación de base de datos

3. Crear todos los objetos necesarios para admitir la replicación de DDL

4. Configurar el administrador de GoldenGate en los sitios A y B

5. Crear procesos de grupo de extracción y bombeo de datos en los sitios A y B

	1. Crear procesos de extracción y bombeo de datos en el sitio A

	2. Crear una tabla de punto de comprobación de GoldenGate en el sitio B

	3. Agregar REPLICAT en el sitio B

	4. Crear procesos de extracción y bombeo de datos en el sitio B

	5. Crear una tabla de punto de comprobación de GoldenGate en el sitio A

	6. Agregar REPLICAT en el sitio A

	7. Agregar trandata en los sitios A y B

	8. Iniciar procesos de extracción y bombeo de datos en el sitio A

	9. Iniciar procesos de extracción y bombeo de datos en el sitio B

	10. Iniciar el proceso REPLICAT en el sitio A

	11. Iniciar el proceso REPLICAT en el sitio B

6. Comprobar el proceso de replicación bidireccional

>[AZURE.IMPORTANT] Este tutorial se ha configurado y probado con la siguiente configuración de software:
>
>| | **Base de datos del sitio A** | **Base de datos del sitio B** |
>|------------------------|----------------------------------|----------------------------------|
>| **Versión de Oracle** | Oracle11g versión 2 – (11.2.0.1) | Oracle11g versión 2 – (11.2.0.1) |
>| **Nombre de máquina** | MachineGG1 | MachineGG2 |
>| **Sistema operativo** | Windows 2008 R2 | Windows 2008 R2 |
>| **SID de Oracle** | TESTGG1 | TESTGG2 |
>| **Esquema de replicación** | SCOTT | SCOTT |

En versiones posteriores de la Base de datos de Oracle y de Oracle GoldenGate, es posible que haya algunos cambios adicionales que deba implementar. Para consultar la información específica de versión más actualizada, consulte la documentación [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) y [Base de datos de Oracle](http://www.oracle.com/us/corporate/features/database-12c/index.html) en el sitio web de Oracle. Por ejemplo, para una base de datos de origen de la versión 11.2.0.4 y versiones posteriores, la captura de DDL es realizada asincrónicamente por el servidor logmining y no requiere la instalación de ningún desencadenador especial, tablas ni demás objetos de base de datos. Las actualizaciones de Oracle GoldenGate pueden realizarse sin detener las aplicaciones de usuario. Es necesario el uso de un desencadenador DDL y objetos auxiliares cuando Extract está en modo integrado con una base de datos de origen de Oracle 11g de una versión anterior a la 11.2.0.4. Para obtener instrucciones detalladas, consulte [Instalar y configurar Oracle GoldenGate para la Base de datos de Oracle](http://docs.oracle.com/goldengate/1212/gg-winux/GIORA.pdf).

##1\. Configurar la base de datos en los sitios A y B
En esta sección se explica cómo realizar los requisitos previos de la base de datos en los sitios A y B. Debe realizar todos los pasos de esta sección en ambos sitios: sitios A y B.

En primer lugar, establezca un escritorio remoto para los sitios A y B a través del Portal de Azure clásico. Abra un símbolo del sistema de Windows y cree un directorio de inicio para los archivos de instalación de Oracle GoldenGate:

	mkdir C:\OracleGG

A continuación, descomprima e instale el software de Oracle GoldenGate en esta carpeta. Después de este paso, puede iniciar el intérprete de comandos de software GoldenGate (GGSCI) ejecutando el comando siguiente:

	C:\OracleGG\.\ggsci

Puede usar [GGSCI](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_gettingstarted.htm) para ejecutar varios comandos que configuren, controlen y supervisen Oracle GoldenGate.

A continuación, ejecute el siguiente comando para crear todas las subcarpetas del paquete de instalación:

	GGSCI (Hostname) 1> CREATE SUBDIRS

Ejecute el comando siguiente para salir de la ventana del símbolo del sistema de GGSCI:

	GGSCI (Hostname) 1> EXIT

A continuación, deberá crear un usuario de base de datos, que será utilizado por los procesos de administrador, extracción y replicación de Oracle GoldenGate. Tenga en cuenta que puede crear usuarios individuales para cada proceso o configurar solo un usuario común. En este tutorial, creamos un usuario, que se denomina ggate. A continuación, se conceden a ese usuario los privilegios necesarios. Tenga en cuenta que debe realizar las siguientes operaciones en los sitios A y B.

Abra la ventana del comando SQL*Plus en los sitios A y B con privilegios de administrador de base de datos mediante **SYSDBA**, como:

	Enter username: / as sysdba

Y ejecute:

	SQL> create tablespace ggs_data   datafile 'c:\OracleDatabase\oradata<DBNAME><DBNAME>ggs_data01.dbf' size 200m;
	SQL> create user ggate identified by ggate default tablespace ggs_data  temporary tablespace temp;
	      grant connect, resource to ggate;
	      grant select any dictionary, select any table to ggate;
	      grant create table to ggate;
	      grant flashback any table to ggate;
	      grant execute on dbms_flashback to ggate;
	      grant execute on utl_file to ggate;
	      grant create any table to ggate;
	      grant insert any table to ggate;
	      grant update any table to ggate;
	      grant delete any table to ggate;
	      grant drop any table to ggate;

A continuación, busque el archivo INIT < DatabaseSID >.ORA de la carpeta %ORACLE\_HOME%\\database en los sitios A y B y anexe los parámetros de la base de datos siguientes a INITTEST.ora:

	UNDO\_MANAGEMENT=AUTO
	UNDO\_RETENTION=86400

Para obtener una lista completa de todos los comandos de Oracle GoldenGate GGSCI, consulte [Referencia para Oracle GoldenGate para Windows](http://docs.oracle.com/goldengate/1212/gg-winux/GWURF/ggsci_commands.htm).

### Realizar la carga inicial de datos

Puede realizar la carga de datos inicial en la base de datos siguiendo varios métodos. Por ejemplo, puede utilizar la [Carga directa de Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_initsync.htm) o utilidades de exportación e importación regulares para exportar los datos de la tabla del sitio A al B.

Para demostrar el proceso de replicación de Oracle GoldenGate, en este tutorial se muestra cómo crear una tabla en los sitios A y B mediante los comandos siguientes.

En primer lugar, abra la ventana de comandos SQL*Plus y ejecute el siguiente comando para crear una tabla de inventario en las bases de datos de los sitios A y B:

	create table scott.inventory
	(prod_id number,
	prod_category varchar2(20),
	qty_in_stock number,
	last_dml timestamp default systimestamp);

A continuación, agregue una restricción a la tabla recién creada en las bases de datos de los sitios A y B:

	alter table scott.inventory add constraint pk_inventory primary key (prod_id) ;

A continuación, conceda todos los privilegios de la nueva tabla de inventario a la ggate de usuario en los sitios A y B:

	grant all on scott.inventory to ggate;

A continuación, cree y habilite un desencadenador de base de datos, INVENTORY\_CDR\_TRG, en la tabla recién creada para asegurarse de que todas las transacciones a la nueva tabla se registran si el usuario no es ggate. Realice esta operación en los sitios A y B.

	CREATE OR REPLACE TRIGGER INVENTORY_CDR_TRG
	BEFORE UPDATE
	ON SCOTT.INVENTORY
	REFERENCING NEW AS New OLD AS Old
	FOR EACH ROW
	BEGIN
	IF SYS_CONTEXT ('USERENV', 'SESSION_USER') != 'GGATE'
	THEN
	:NEW.LAST_DML := SYSTIMESTAMP;
	END IF;
	END;
	/


##2\. Preparar los sitios A y B para la replicación de base de datos
En esta sección se explica cómo preparar los sitios A y B para la replicación de base de datos Debe realizar todos los pasos de esta sección en ambos sitios: A y B.

En primer lugar, establezca un escritorio remoto para los sitios A y B a través del Portal de Azure clásico. Cambie la base de datos al modo archivelog mediante la ventana de comandos SQL*Plus:

	sql>shutdown immediate
	sql>startup mount
	sql>alter database archivelog;
	sql>alter database open;


A continuación, habilite el [registro complementario](http://docs.oracle.com/cd/E11882_01/server.112/e22490/logminer.htm) mínimo del modo indicado a continuación:

	SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;

A continuación, prepare la base de datos para admitir la replicación de DDL (lenguaje de definición de datos):

	SQL> alter system set recyclebin=off scope=spfile;

Después, apague y reinicie la base de datos:

	sql>shutdown immediate
	sql>startup


##3\. Crear todos los objetos necesarios para admitir la replicación de DDL
En esta sección se enumeran las secuencias de comandos que debe usar para crear todos los objetos necesarios para admitir la replicación de DDL. Debe ejecutar las secuencias de comandos especificadas en esta sección en los sitios A y B.

Abra un símbolo del sistema de Windows y navegue hasta la carpeta Oracle GoldenGate, por ejemplo, C:\\OracleGG. Inicie el símbolo del sistema de SQL*Plus con privilegios de administrador de base de datos, por ejemplo, usando **SYSDBA** en los sitios A y B.

A continuación, ejecute los siguientes scripts:

	SQL> @marker_setup.sql  
	Enter GoldenGate schema name: ggate
	SQL> @ddl_setup.sql  
	Enter GoldenGate schema name: ggate
	SQL> @role_setup.sql
	Enter GoldenGate schema name: ggate
	SQL> grant ggs_ggsuser_role to ggate;
	 Grant succeeded.
	 SQL> @ddl_enable
	 Trigger altered.
	 SQL> @ddl_pin ggate


La herramienta Oracle GoldenGate requiere un inicio de sesión de nivel de tabla para la compatibilidad con DDL (lenguaje de definición de datos). Es por eso que debe habilitar el registro complementario en el nivel de tabla mediante el comando ADD TRANDATA. Abra una ventana de intérprete de comando de Oracle GoldenGate, inicie de sesión en la base de datos y, a continuación, ejecute el comando ADD TRANDATA:

	GGSCI 5> DBLOGIN USERID ggate, PASSWORD ggate

	GGSCI(Hostname) 6> add trandata scott.inventory

##4\. Configurar el administrador de GoldenGate en los sitios A y B
El administrador de Oracle GoldenGate realiza una serie de funciones como iniciar los demás procesos de GoldenGate, informes y administración de archivos de registro de rastro.

Tendrá que configurar el proceso del Administrador de Oracle GoldenGate en los sitios A y B. Para ello, realice los pasos siguientes en los sitios A y B.

Abra la ventana de comandos de Windows e inicie el intérprete de comandos de Oracle GoldenGate:

	cd C:\OracleGG\
	c:\OracleGG>ggsci
	Oracle GoldenGate Command Interpreter for Oracle
	Version 11.2.1.0.3 14400833 OGGCORE_11.2.1.0.3_PLATFORMS_120823.1258
	Windows x64 (optimized), Oracle 11g on Aug 23 2012 16:50:36
	Copyright (C) 1995, 2012, Oracle and/or its affiliates. All rights reserved.


Registra la sesión GGSCI en una base de datos para que pueda ejecutar comandos que afecten a la base de datos:

	GGSCI (HostName) 1> DBLOGIN USERID ggate, PASSWORD ggate
	Successfully logged into database.

Abra el estado y tiempo de retardo (si procede) para todos los procesos de administrador, Extract y Replicat de un sistema:

	GGSCI (HostName) 2> info all
	Program     Status      Group       Lag           Time Since Chkpt
	MANAGER     STOPPED

Abra el archivo de parámetros con el comando EDIT PARAMS y, a continuación, agregue la información siguiente:

	GGSCI (HostName) 3> edit params mgr
	PORT 7809
	USERID ggate, PASSWORD ggate
	PURGEOLDEXTRACTS  C:\OracleGG\dirdat\ex, USECHECKPOINTS

Abra el estado y tiempo de retardo (si procede) para todos los procesos de administrador, Extract y Replicat de un sistema:

	GGSCI (HostName) 46> info all
	Program     Status      Group       Lag           Time Since Chkpt
	MANAGER     STOPPED

Registra la sesión GGSCI en una base de datos para que pueda ejecutar comandos que afecten a la base de datos:

	GGSCI (HostName) 47> dblogin USERID ggate, PASSWORD ggate

	Successfully logged into database.

Inicie el proceso de administrador:

	GGSCI (HostName) 48> start manager
	Manager started.

##5\. Crear procesos de grupo de extracción y bombeo de datos en los sitios A y B

###Crear procesos de extracción y bombeo de datos en el sitio A

Deberá crear los procesos de extracción y bombeo de datos en los sitios A y B. Establezca un escritorio remoto en los sitios A y B a través del Portal de Azure clásico. Abra una ventana de intérprete de comandos GGSCI. Ejecute los comandos siguientes en el sitio A:

	GGSCI (MachineGG1) 14> add extract ext1 tranlog begin now
	EXTRACT added.
	GGSCI (MachineGG1) 4> add exttrail C:\OracleGG\dirdat\lt, extract ext1
	EXTTRAIL added.
	GGSCI (MachineGG1) 16> add extract dpump1 exttrailsource C:\OracleGG\dirdat\aa
	EXTRACT added.
	GGSCI (MachineGG1) 17> add rmttrail C:\OracleGG\dirdat\ab extract dpump1
	RMTTRAIL added.

Abra el archivo de parámetros con el comando EDIT PARAMS y, a continuación, agregue la información siguiente: GGSCI (MachineGG1) 18 > edite los parámetros ext1 EXTRACT ext1 USERID ggate, PASSWORD ggate EXTTRAIL C:\\OracleGG\\dirdat\\aa TRANLOGOPTIONS EXCLUDEUSER ggate TABLE scott.inventory, GETBEFORECOLS ( ON UPDATE KEYINCLUDING (prod\_category,qty\_in\_stock, last\_dml), ON DELETE KEYINCLUDING (prod\_category,qty\_in\_stock, last\_dml));;

Abra el archivo de parámetros con el comando EDIT PARAMS y, a continuación, agregue la información siguiente:

	GGSCI (MachineGG1) 15> edit params dpump1
	EXTRACT dpump1
	 USERID ggate, PASSWORD ggate
	 RMTHOST ActiveGG2orcldb, MGRPORT 7809, TCPBUFSIZE 100000
	 RMTTRAIL C:\OracleGG\dirdat\ab
	 PASSTHRU
	 TABLE scott.inventory;

###Crear una tabla de punto de comprobación de GoldenGate en el sitio B

A continuación, deberá agregar una tabla de punto de comprobación en el sitio B. Para ello, debe abrir una ventana de intérprete de comando de GoldenGate y ejecutar:

	C:\OracleGG\ggsci
	GGSCI (MachineGG2) 1> DBLOGIN USERID ggate, PASSWORD ggate
	Successfully logged into database.

Y, a continuación, agregue la tabla de punto de comprobación para la base de datos, donde ggate es el propietario:

	GGSCI (MachineGG2) 2> ADD CHECKPOINTTABLE ggate.checkpointtable
	Successfully created checkpoint table ggate.checkpointtable.

Agregue el nombre de la tabla de punto de comprobación al archivo GLOBALS del servidor de destino, que es el sitio B en este paso. Edite el archivo GLOBALS en el sitio B:

	GGSCI (MachineGG2) 1> EDIT PARAMS ./GLOBALS

A continuación, anexe el parámetro CHECKPOINTTABLE al archivo GLOBALS existente:

	GGSCHEMA ggate
	CHECKPOINTTABLE ggate.checkpointtable

Como paso final, guarde y cierre el archivo de parámetros GLOBALS.


###Agregar REPLICAT en el sitio B
En esta sección se describe cómo agregar un proceso REPLICAT "REP2" en el sitio B.

Use el comando ADD REPLICAT para crear un grupo de Replicat en el sitio B:

	GGSCI (MachineGG2) 37> add replicat rep2 exttrail C:\OracleGG\dirdatab, checkpointtable ggate.checkpointtable

Abra el archivo de parámetros con el comando EDIT PARAMS y, a continuación, agregue la información siguiente:

	GGSCI (MachineGG2) 10> edit params rep2
	REPLICAT rep2
	ASSUMETARGETDEFS
	USERID ggate, PASSWORD ggate
	DISCARDFILE C:\OracleGG\dirdat\discard.txt, append,megabytes 10
	MAP scott.inventory, TARGET scott.inventory;

###Crear procesos de extracción y bombeo de datos en el sitio B

En esta sección se describe cómo crear un nuevo proceso de extracción "EXT2" y un nuevo proceso de bombeo de datos "DPUMP2" en el sitio B:

	GGSCI (MachineGG2) 3> add extract ext2 tranlog begin now
	 EXTRACT added.
	GGSCI (MachineGG2) 4> add exttrail C:\OracleGG\dirdat\ac extract ext2
	 EXTTRAIL added.
	GGSCI (MachineGG2) 5> add extract dpump2 exttrailsource C:\OracleGG\dirdat\ac
	 EXTRACT added.
	GGSCI (MachineGG2) 6> add rmttrail C:\OracleGG\dirdat\ad extract dpump2
	 RMTTRAIL added.

Abra el archivo de parámetros con el comando EDIT PARAMS y, a continuación, agregue la información siguiente:

	GGSCI (MachineGG2) 31> edit params ext2
	EXTRACT ext2
	USERID ggate, PASSWORD ggate
	EXTTRAIL C:\OracleGG\dirdat\ac
	TRANLOGOPTIONS EXCLUDEUSER ggate
	TABLE scott.inventory,
	GETBEFORECOLS (
	ON UPDATE KEYINCLUDING (prod_category,qty_in_stock, last_dml),
	ON DELETE KEYINCLUDING (prod_category,qty_in_stock, last_dml));

Abra el archivo de parámetros con el comando EDIT PARAMS y, a continuación, agregue la información siguiente:

	GGSCI (MachineGG2) 32> edit params dpump2
	EXTRACT dpump2
	USERID ggate, PASSWORD ggate
	RMTHOST MachineGG1, MGRPORT 7809, TCPBUFSIZE 100000
	RMTTRAIL C:\OracleGG\dirdat\ad
	PASSTHRU
	TABLE scott.inventory;

###Crear una tabla de punto de comprobación de GoldenGate en el sitio A

Abra una ventana de intérprete de comandos de Oracle GoldenGate y cree una tabla de punto de comprobación:

	GGSCI (MachineGG1) 1> DBLOGIN USERID ggate, PASSWORD ggate
	Successfully logged into database.

	GGSCI (MachineGG1) 2> ADD CHECKPOINTTABLE ggate.checkpointtable

	Successfully created checkpoint table ggate.checkpointtable.

También deberá agregar el nombre de la tabla de punto de comprobación al archivo GLOBALS en el sitio A.

Abra una ventana de intérprete de comandos de Oracle GoldenGate y edite el archivo GLOBALS en el sitio A:

	GGSCI (MachineGG1) 1> EDIT PARAMS ./GLOBALS
	Add the CHECKPOINTTABLE parameter to the existing GLOBALS file as follows:
	GGSCHEMA ggate
	CHECKPOINTTABLE ggate.checkpointtable

Guarde y cierre el archivo de parámetros GLOBALS.

###Agregar REPLICAT en el sitio A

En esta sección se describe cómo agregar un proceso REPLICAT "REP1" en el sitio A.

El siguiente comando crea un rep1 de grupo Replicat con el nombre de una pista y la checkpointtable asociada.

	GGSCI (MachineGG1) 21> add replicat rep1 exttrail C:\OracleGG\dirdat\ad,checkpointtable ggate.checkpointtable
	 REPLICAT added.

Abra el archivo de parámetros con el comando EDIT PARAMS y, a continuación, agregue la información siguiente:

	GGSCI (MachineGG1) 10> edit params rep1
	REPLICAT rep1
	ASSUMETARGETDEFS
	USERID ggate, PASSWORD ggate
	DISCARDFILE C:\OracleGG\dirdat\discard.txt, append, megabytes 10
	MAP scott.inventory, TARGET scott.inventory;

### Agregar trandata en los sitios A y B

Habilite el registro complementario en el nivel de tabla mediante el comando ADD TRANDATA. Abra una ventana de intérprete de comando de Oracle GoldenGate, inicie de sesión en la base de datos y, a continuación, ejecute el comando ADD TRANDATA.

Establezca un escritorio remoto en MachineGG1, abra un intérprete de comandos de Oracle GoldenGate y ejecute:

	GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	GGSCI (MachineGG1) 13> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Establezca un escritorio remoto en MachineGG2, abra un intérprete de comandos de Oracle GoldenGate y ejecute:

	GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	Logging of supplemental redo data enabled for table SCOTT.INVENTORY.

Muestre información sobre el estado del registro complementario a nivel de tabla:

	GGSCI (MachineGG2) 15> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###Agregar trandata en los sitios A y B

Habilite el registro complementario en el nivel de tabla mediante el comando ADD TRANDATA. Abra una ventana de intérprete de comando de Oracle GoldenGate, inicie de sesión en la base de datos y, a continuación, ejecute el comando ADD TRANDATA.

Establezca un escritorio remoto en MachineGG1, abra un intérprete de comandos de Oracle GoldenGate y ejecute:

	GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	GGSCI (MachineGG1) 13> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Establezca un escritorio remoto en MachineGG2, abra un intérprete de comandos de Oracle GoldenGate y ejecute:

	GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	Logging of supplemental redo data enabled for table SCOTT.INVENTORY.
	Display information about the state of table-level supplemental logging:
	GGSCI (MachineGG2) 15> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###Iniciar procesos de extracción y bombeo de datos en el sitio A

Inicie el proceso de extracción ext1 en el sitio A:

	GGSCI (MachineGG1) 31> start extract ext1
	Sending START request to MANAGER …
	EXTRACT EXT1 starting

Inicie el proceso de bombeo de datos dpump1 en el sitio A:

	GGSCI (MachineGG1) 23> start extract dpump1
	Sending START request to MANAGER …
	EXTRACT DPUMP1 starting
Visualice información acerca del grupo de extracción ext1: GGSCI (MachineGG1) 32> info extract ext1 EXTRACT EXT1 Last Started 2013-11-25 08:03 Status RUNNING Checkpoint Lag 00:00:00 (updated 00:00:02 ago) Log Read Checkpoint Oracle Redo Logs 2013-11-25 08:03:18 Seqno 6, RBA 3230720 SCN 0.1074371 (1074371)

Abra el estado y tiempo de retardo (si procede) para todos los procesos de administrador, Extract y Replicat de un sistema:

	GGSCI (MachineGG1) 16> info all
	Program     Status      Group       Lag at Chkpt  Time Since Chkpt

	MANAGER     RUNNING
	EXTRACT     RUNNING     DPUMP1      00:00:00      00:46:33
	EXTRACT     RUNNING     EXT1        00:00:00      00:00:04

###Iniciar procesos de extracción y bombeo de datos en el sitio B

Inicie el proceso de extracción ext2 en el sitio B:

	GGSCI (MachineGG2) 22> start extract ext2
	Sending START request to MANAGER …
	EXTRACT EXT2 starting

Inicie el proceso de bombeo de datos dpump2 en el sitio B:

	GGSCI (MachineGG2) 23> start extract dpump2
	Sending START request to MANAGER …
	EXTRACT DPUMP2 starting

Abra el estado y tiempo de retardo (si procede) para todos los procesos de administrador, Extract y Replicat de un sistema:

	GGSCI (ActiveGG2orcldb) 6> info all
	Program     Status      Group       Lag at Chkpt  Time Since Chkpt

	MANAGER     RUNNING
	EXTRACT     RUNNING     DPUMP2      00:00:00      136:13:33
	EXTRACT     RUNNING     EXT2        00:00:00      00:00:04

### Iniciar el proceso REPLICAT en el sitio A

En esta sección se describe cómo iniciar el proceso REPLICAT "REP1" en el sitio A.

Inicie el proceso Replicat en el sitio A:

	GGSCI (MachineGG1) 38> start replicat rep1
	Sending START request to MANAGER …
	REPLICAT REP1 starting

Muestre el estado de un grupo Replicat:

	GGSCI (MachineGG1) 39> status replicat rep1
	 REPLICAT REP1: RUNNING

###Iniciar el proceso REPLICAT en el sitio B

En esta sección se describe cómo iniciar el proceso REPLICAT "REP2" en el sitio B.

Inicie el proceso Replicat en el sitio B:

	GGSCI (MachineGG2) 26> start replicat rep2
	Sending START request to MANAGER …
	REPLICAT REP2 starting

Muestre el estado de un grupo Replicat:

	GGSCI (MachineGG2) 27> status replicat rep2
	REPLICAT REP2: RUNNING

##6\. Comprobar el proceso de replicación bidireccional

Para comprobar la configuración de Oracle GoldenGate, inserte una fila en la base de datos del sitio A. Establezca un escritorio remoto en el sitio A. Abra la ventana de comandos SQL*Plus y ejecute: SQL> seleccione nombre de v$database;

	NAME
	———
	TESTGG

	SQL> insert into inventory values  (100,’TV’,100,sysdate);

	1 row created.

	SQL> commit;

	Commit complete.

A continuación, compruebe si esa fila se replica en el sitio B. Para ello, establezca un escritorio remoto en el sitio B. Abra la ventana SQL Plus y ejecute:

	SQL> select name from v$database;

	NAME
	———
	TESTGG

	SQL> select * from inventory;

	PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
	———- ——————– ———— ———
	100 TV 100 22-MAR-13

Inserte un nuevo registro en el sitio B:

	SQL> insert into inventory  values  (101,’DVD’,10,sysdate);
	1 row created.

	SQL> commit;

	Commit complete.

Establezca un escritorio remoto en el sitio A y compruebe si la replicación ha tenido lugar:

	SQL> select * from inventory;

	PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
	———- ——————– ———— ———
	100 TV 100 22-MAR-13
	101 DVD 10 22-MAR-13

##Recursos adicionales
[Imágenes de máquina virtual de Oracle para Azure](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

<!---HONumber=AcomDC_0128_2016-->