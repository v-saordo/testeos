<properties 
	pageTitle="Código de Búfer de anillo de XEvent para Base de datos SQL | Microsoft Azure" 
	description="Proporciona un ejemplo de código de Transact-SQL más fácil y rápido mediante el uso del destino de Búfer de anillo, en Base de datos SQL de Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor="" 
	tags=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/30/2015" 
	ms.author="genemi"/>


# Código de destino de búfer de anillo para eventos extendidos en Base de datos SQL


Desea tener un ejemplo de código completo de la manera más rápida y fácil para capturar y notificar información de un evento extendido durante una prueba. El destino más fácil para los datos de eventos extendidos es el [destino de búfer de anillo](http://msdn.microsoft.com/library/ff878182.aspx).


En este tema, se presenta un ejemplo de código de Transact-SQL que:


1. Crea una tabla con datos con los cuales demostrarse.

2. Crea una sesión para un evento extendido existente, es decir, **sqlserver.sql\_statement\_starting**.
 - El evento se limita a las instrucciones SQL que contienen una cadena Update determinada: **statement LIKE '%UPDATE tabEmployee%'**.
 - Elige enviar la salida del evento a un destino de tipo Búfer de anillo, es decir, **package0.ring\_buffer**.

3. Inicia la sesión de eventos.

4. Emite un par de instrucciones SQL UPDATE simple.

5. Emite una instrucción SQL SELECT para recuperar la salida de evento del Búfer de anillo.
 - Se unen **sys.dm\_xe\_database\_session\_targets** y otras vistas de administración dinámicas (DMV).

6. Detiene la sesión de eventos.

7. Anula el destino de Búfer de anillo para liberar sus recursos.

8. Anula la sesión de eventos y la tabla de demostración.


## Requisitos previos


- Una cuenta y una suscripción de Azure. Puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).


- Cualquier base de datos donde pueda crear una tabla.
 - De manera opcional, puede [crear una base de datos de **AdventureWorksLT** de demostración](sql-database-get-started.md) en cuestión de minutos.


- SQL Server Management Studio (ssms.exe), la versión preliminar de agosto de 2015 o una versión posterior. Puede descargar la versión más reciente de ssms.exe desde:
 - [Un vínculo en el tema.](http://msdn.microsoft.com/library/mt238290.aspx)
 - [Un vínculo directo a la descarga.](http://go.microsoft.com/fwlink/?linkid=616025)
 - Microsoft recomienda actualizar periódicamente el archivo ssms.exe, quizá mensualmente.


## Código de ejemplo


Con modificaciones muy pequeñas, el siguiente ejemplo de código de Búfer de anillo se puede ejecutar en Base de datos SQL de Azure o en Microsoft SQL Server. La diferencia es la presencia del nodo "\_database" en el nombre de algunas vistas de administración dinámicas (DMV) en la cláusula FROM del paso 5. Por ejemplo:

- sys.dm\_xe**\_database**\_session\_targets
- sys.dm\_xe\_session\_targets


&nbsp;


```
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
	(SELECT * FROM sys.objects
		WHERE type = 'U' and name = 'tabEmployee')
BEGIN
	DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
	EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
	EmployeeId           int                not null  identity(1,1),
	EmployeeKudosCount   int                not null  default 0,
	EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
	VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
	(SELECT * from sys.database_event_sessions
		WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
	DROP EVENT SESSION eventsession_gm_azuresqldb51
		ON DATABASE;
END
GO


CREATE
	EVENT SESSION eventsession_gm_azuresqldb51
	ON DATABASE
	ADD EVENT
		sqlserver.sql_statement_starting
			(
			ACTION (sqlserver.sql_text)
			WHERE statement LIKE '%UPDATE tabEmployee%'
			)
	ADD TARGET
		package0.ring_buffer
			(SET
				max_memory = 500   -- Units of KB.
			);
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
	ON DATABASE
	STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
	SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
	SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
	ON DATABASE
	STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
	ON DATABASE
	DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
	ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```


&nbsp;


## Contenido del Búfer de anillo


Se usa ssms.exe para ejecutar el ejemplo de código.


Para ver los resultados, hicimos clic en la celda bajo el encabezado de columna **target\_data\_XML**.

Luego, en el panel de resultados, hicimos clic en la celda bajo el encabezado de columna **target\_data\_XML**. Esto creó otra pestaña de archivo en ssms.exe donde se mostró el contenido de la celda de resultado, como XML.


El resultado se muestra en el bloque siguiente. Parece largo, pero son solo dos elementos **<event>**.


&nbsp;


```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```


#### Liberar los recursos contenidos en el Búfer de anillo


Cuando termine de utilizar el Búfer de anillo, puede quitarlo y liberar sus recursos. Para ello, emita un comando **ALTER** como el siguiente:


```
ALTER EVENT SESSION eventsession_gm_azuresqldb51
	ON DATABASE
	DROP TARGET package0.ring_buffer;
GO
```


La definición de la sesión de eventos está actualizada, pero no se quita. Más adelante, puede agregar otra instancia del Búfer de anillo a la sesión de eventos:


```
ALTER EVENT SESSION eventsession_gm_azuresqldb51
	ON DATABASE
	ADD TARGET
		package0.ring_buffer
			(SET
				max_memory = 500   -- Units of KB.
			);
```


## Más información


El tema principal de los eventos extendidos en Base de datos SQL de Azure es:


- [Consideraciones de eventos extendidos en Base de datos SQL](sql-database-xevent-db-diff-from-svr.md), que compara algunos aspectos de los eventos extendidos que son distintos entre Base de datos SQL de Azure y Microsoft SQL Server.


Otros temas de ejemplo de código para eventos extendidos están disponibles en los siguientes vínculos. De todas formas, debe comprobar siempre cualquier ejemplo para ver si está destinado a Microsoft SQL Server frente a Base de datos SQL de Azure. A continuación, puede decidir si es necesario algún pequeño cambio para ejecutar el ejemplo.


- Ejemplo de código para Base de datos SQL de Azure: [Código de destino del archivo de evento para eventos extendidos en Base de datos SQL](sql-database-xevent-code-event-file.md)


<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->

<!---HONumber=AcomDC_0128_2016-->