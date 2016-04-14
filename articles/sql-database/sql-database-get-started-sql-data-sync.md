<properties
	pageTitle="Introducción a Data Sync con bases de datos SQL"
	description="Este tutorial le ayuda a comenzar con SQL Data Sync de Azure (vista previa)."
	services="sql-database"
	documentationCenter=""
	authors="spelluru"
	manager="JennieHubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/19/2016"
	ms.author="jhubbard"/>


#Introducción a SQL Data Sync de Azure (vista previa)
En este tutorial aprenderá los fundamentos del uso de SQL Data Sync de Azure mediante el Portal de Azure clásico.

En este tutorial se presupone una experiencia previa mínima en SQL Server y Base de datos SQL de Azure. En este tutorial, creará un grupo de sincronización híbrido (sesiones de SQL Server y Base de datos SQL) completamente configurado y sincronizado en función de cómo lo programe.

> [AZURE.NOTE]La documentación técnica completa para Azure SQL Data Sync, que anteriormente se encontraba en MSDN, está disponible como .pdf. Descárguela [aquí](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

## Paso 1: Conexión a la Base de datos SQL de Azure

1. Inicie sesión en el [Portal clásico](http://manage.windowsazure.com).

2. Haga clic en **BASES DE DATOS SQL** en el panel izquierdo.

3. Haga clic en **SINCRONIZAR** en la parte inferior de la página. Cuando haga clic en SINCRONIZAR, aparecerá una lista que muestra lo que puede agregar: **Nuevo grupo de sincronización** y **Nuevo agente de sincronización**.

4. Para iniciar el Asistente para el nuevo agente de SQL Data Sync, haga clic en **Nuevo agente de sincronización**.

5. Si no había ningún agente agregado, **haga clic para descargarlo aquí**.

	![Image1](./media/sql-database-get-started-sql-data-sync/SQLDatabaseScreen-Figure1.PNG)


## Paso 2: Incorporación de un agente cliente
Este paso solo es necesario si va a incluir en su grupo de sincronización una base de datos de SQL Server local. Vaya hasta el paso 4 si su grupo de sincronización solo tiene instancias de Base de datos SQL.

<a id="InstallRequiredSoftware"></a>
### Paso 2a: Instalación del software necesario
Asegúrese de tener el software siguiente instalado en el equipo donde instalará el agente cliente.

- **.NET Framework 4.0**

 Puede instalar .NET Framework 4.0 desde [aquí](http://go.microsoft.com/fwlink/?linkid=205836).

- **Microsoft SQL Server 2008 R2 SP1 System CLR Types (x86)**

 Puede instalar Microsoft SQL Server 2008 R2 SP1 System CLR Types (x86) desde [aquí](http://www.microsoft.com/download/en/details.aspx?id=26728).

- **Objetos de administración compartida de Microsoft SQL Server 2008 R2 SP1 (x86)**

 Puede instalar los objetos de administración compartida de Microsoft SQL Server 2008 R2 SP1 (x86) desde [aquí](http://www.microsoft.com/download/en/details.aspx?id=26728).



<a id="InstallClient"></a>
### Paso 2b: Instalación de un nuevo agente cliente

Siga las instrucciones de [Instalar un agente cliente de (SQL Data Sync)](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf) para instalar el agente.



<a id="RegisterSSDb"></a>
### Paso 2c: Finalización del Asistente para el nuevo agente de SQL Data Sync

1. 	Vuelva al Asistente de nuevo agente de SQL Data Sync.
2.	Ponga un nombre descriptivo al agente.
3.	En el menú desplegable, seleccione el valor de **REGIÓN** (centro de datos) donde se hospedará a este agente.
4.	En el menú desplegable, seleccione el valor de **SUSCRIPCIÓN** donde se hospedará a este agente.
5.	Haga clic en la flecha derecha.



## Paso 3: Registro de una base de datos de SQL Server con el agente cliente

Cuando del agente cliente se haya instalado, registre todas las bases de datos de SQL Server locales que pretenda incluir en un grupo de sincronización con el agente. Para registrar una base de datos con el agente, siga las instrucciones de [Registrar una base de datos de SQL Server con un agente cliente](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).



## Paso 4: Creación de un grupo de sincronización


<a id="StartNewSGWizard"></a>
### Paso 4a: Inicio del asistente Nuevo grupo de sincronización

1.	Vuelva al [Portal clásico](http://manage.windowsazure.com).
2.	Haga clic en **BASES DE DATOS SQL**.
3.	Haga clic en **AGREGAR SINCRONIZACIÓN** al final de la página y, a continuación, seleccione Nuevo grupo de sincronización en el cajón.

	![Image2](./media/sql-database-get-started-sql-data-sync/NewSyncGroup-Figure2.png)



<a id=""></a>
### Paso 4b: Introducción de la configuración básica


1.	Escriba un nombre descriptivo para el grupo de sincronización.
2.	En el menú desplegable, seleccione el valor de **REGIÓN** (centro de datos) donde se hospedará a este grupo de sincronización.
3. Haga clic en la flecha derecha.

	![Image3](./media/sql-database-get-started-sql-data-sync/NewSyncGroupName-Figure3.PNG)


<a id="DefineHubDB"></a>
### Paso 4c: Definición de la base de datos central de sincronización

1. En el menú desplegable, seleccione la instancia de Base de datos SQL para que sirva de concentrador de grupo de sincronización.
2. Escriba las credenciales para esta instancia de Base de datos SQL: **NOMBRE DE USUARIO DE LA BASE DE DATOS CENTRAL** y **CONTRASEÑA DE LA BASE DE DATOS CENTRAL**.
3. Espere a que SQL Data Sync confirme NOMBRE DE USUARIO y CONTRASEÑA. Aparecerá una marca de verificación a la derecha de PASSWORD cuando las credenciales se confirmen.
4. En el menú desplegable, seleccione la directiva **RESOLUCIÓN DE CONFLICTOS**.

 **Prevalece la base de datos central**: todos los cambios incluidos en la base de datos central se escriben en las bases de datos de referencia, sobrescribiendo los cambios en el mismo registro de la base de datos de referencia. Funcionalmente esto significa que el primer cambio escrito en el concentrador se propaga a las demás bases de datos.


 **Prevalece el cliente**: los cambios escritos en la base de datos central se sobrescriben con los cambios escritos en las bases de datos de referencia. Funcionalmente esto significa que el último cambio escrito en el concentrador es el que se conserva y propaga a las otras bases de datos.

5.	Haga clic en la flecha derecha.

	![Image4](./media/sql-database-get-started-sql-data-sync/NewSyncGroupHub-Figure4.PNG)

<a id="AddRefDB"></a>
### Paso 4d: Incorporación de una base de datos de referencia


Repita este paso en todas las bases de datos adicionales que quiera agregar al grupo de sincronización.

1. En el menú desplegable, seleccione la base de datos que desea agregar.

	Las bases de datos del menú desplegable incluyen tanto bases de datos de SQL Server que se han registrado con el agente como instancias de Base de datos SQL.
2.	Escriba las credenciales para esta base de datos: **NOMBRE DE USUARIO** y **CONTRASEÑA**.
3.	En el menú desplegable, seleccione la **DIRECCIÓN DE SINCRONIZACIÓN** para esta base de datos.

	**Bidireccional**: los cambios en la base de datos de referencia se escriben en la base de datos central y los cambios realizados en la base de datos central se escriben en la base de datos de referencia.

	**Sincronizar desde la base de datos central**: la base de datos recibe actualizaciones desde la base de datos central. Sin embargo, no envía los cambios a la base de datos central.

	**Sincronizar con la base de datos central**: la base de datos envía actualizaciones a la base de datos central. Sin embargo, los cambios del concentrador no se escriben en esta base de datos.

4.	Para terminar de crear el grupo de sincronización, haga clic en la casilla situada en la parte inferior derecha del asistente. Espere a que SQL Data Sync confirme las credenciales. Una marca de verificación verde indica que las credenciales se han confirmado.

5.	Haga clic en la marca de verificación otra vez. De este modo, volverá a la página **SINCRONIZACIÓN**, bajo Bases de datos SQL. Este grupo de sincronización ahora aparece con los demás grupos de sincronización y agentes.

	![Image5](./media/sql-database-get-started-sql-data-sync/NewSyncGroupReference-Figure5.PNG)


## Paso 5: Definición de los datos que hay que sincronizar

SQL Data Sync de Azure le permite seleccionar tablas y columnas para sincronizarlas. Si además desea filtrar una columna para que solo sincronice filas con valores concretos (como, Age>=65), use el portal SQL Data Sync en Azure y la documentación de Seleccionar las tablas, las columnas y las filas que hay que sincronizar para definir los datos que se sincronizarán.

1.	Vuelva al [Portal clásico](http://manage.windowsazure.com).
2.	Haga clic en **BASES DE DATOS SQL**.
3.	Haga clic en la pestaña **SINCRONIZACIÓN**.
4.	Haga clic en el nombre de este grupo de sincronización.
5.	Haga clic en la pestaña **REGLAS DE SINCRONIZACIÓN**.
6.	Seleccione la base de datos a la que desea proporcionar el esquema de grupo de sincronización.
7.	Haga clic en la flecha derecha.
8.	Haga clic en **ACTUALIZAR ESQUEMA**.
9.	En cada tabla de la base de datos, seleccione las columnas que desee incluir en las sincronizaciones.
	- No se pueden seleccionar columnas con tipos de datos incompatibles.
	- Si no hay columnas seleccionadas en una tabla, esta no se incluye en el grupo de sincronización.
	- Para seleccionar o anular la selección de todas las tablas, haga clic en SELECT en la parte inferior de la pantalla.
10.	Haga clic en **GUARDAR** y espere a que el grupo de sincronización termine el aprovisionamiento.
11.	Para volver a la página de aterrizaje de Data Sync, haga clic en la flecha atrás situada en la parte superior izquierda de la pantalla (encima del nombre del grupo de sincronización).

	![Image6](./media/sql-database-get-started-sql-data-sync/NewSyncGroupSyncRules-Figure6.PNG)

## Paso 6: Configuración del grupo de sincronización

Siempre podrá sincronizar un grupo de sincronización haciendo clic en SYNC en la parte inferior de la página de aterrizaje de Data Sync. Si desea programar la sincronización de un grupo de sincronización, configúrelo en el grupo de sincronización.

1.	Vuelva al [Portal clásico](http://manage.windowsazure.com).
2.	Haga clic en **BASES DE DATOS SQL**.
3.	Haga clic en la pestaña **SINCRONIZACIÓN**.
4.	Haga clic en el nombre de este grupo de sincronización.
5.	Haga clic en la pestaña **Configurar**.
6.	**SINCRONIZACIÓN AUTOMÁTICA**
	- Para que el grupo de sincronización se sincronice con una frecuencia determinada, haga clic en **ACTIVAR**. No obstante, puede seguir sincronizando a petición haciendo clic en SYNC.
	- Haga clic en **DESACTIVAR** para configurar el grupo de sincronización de forma que se sincronice solo cuando haga clic en SINCRONIZACIÓN.
7.	**FRECUENCIA DE SINCRONIZACIÓN**
	- Si SINCRONIZACIÓN AUTOMÁTICA está ACTIVADA, configure la frecuencia de sincronización. La frecuencia debe estar entre 5 minutos y 1 mes.
8.	Haga clic en **GUARDAR**.

![Image7](./media/sql-database-get-started-sql-data-sync/NewSyncGroupConfigure-Figure7.PNG)

¡Enhorabuena! Ha creado un grupo de sincronización que incluye una instancia de Base de datos SQL y una base de datos SQL Server.

## Pasos siguientes
Para obtener más información acerca de Base de datos SQL y SQL Data Sync, consulte:

* [Descarga de la documentación técnica completa de SQL Data Sync](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)
* [Información general de Base de datos SQL](sql-database-technical-overview.md)
* [Administración del ciclo de vida de las aplicaciones](https://msdn.microsoft.com/library/jj907294.aspx)
 

 

<!---HONumber=AcomDC_0121_2016-->