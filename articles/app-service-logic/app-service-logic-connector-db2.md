<properties
   pageTitle="Uso del conector de DB2 en el Servicio de aplicaciones de Microsoft Azure | Microsoft Azure"
   description="Cómo usar el conector de DB2 con desencadenadores y acciones de aplicación lógica"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="gplarsen"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="12/03/2015"
   ms.author="plarsen"/>

# Conector de DB2

Microsoft Connector para DB2 es una aplicación de API para conectar aplicaciones, a través del Servicio de aplicaciones de Azure, a los recursos almacenados en una base de datos DB2 de IBM. El conector incluye un cliente Microsoft para conectarse a equipos servidores remotos de DB2 a través de una conexión de red TCP/IP, incluidas las conexiones híbridas de Azure con servidores locales de DB2 mediante la Retransmisión de bus de servicio de Azure. El conector admite las siguientes operaciones de base de datos:

- Leer las filas mediante SELECT
- Sondear para leer filas mediante SELECT COUNT, seguido de SELECT
- Agregar una o varias filas (en masa) mediante INSERT
- Modificar una o varias filas (en masa) mediante UPDATE
- Quitar una o varias filas (en masa) mediante DELETE
- Leer para modificar filas con SELECT CURSOR seguido de UPDATE WHERE CURRENT OF CURSOR
- Leer para quitar filas con SELECT CURSOR seguido de UPDATE WHERE CURRENT OF CURSOR
- Ejecutar un procedimiento con parámetros de entrada y salida, valor devuelto y conjunto de resultados mediante CALL
- Comandos personalizados y operaciones compuestas con SELECT, INSERT, UPDATE, DELETE

## Acciones y desencadenadores
El conector admite los siguientes desencadenadores y acciones de aplicación lógica:

Desencadenadores | Acciones
--- | ---
<ul><li>Datos de sondeo</li></ul> | <ul><li>Inserción en masa</li><li>Inserción</li><li>Actualización en masa</li><li>Actualización</li><li>Llamada</li><li>Eliminación en masa</li><li>Eliminación</li><li>Selección</li><li>Actualización condicional</li><li>Publicación en EntitySet</li><li>Eliminación condicional</li><li>Selección de una única entidad</li><li>Eliminación</li><li>Upsert en EntitySet</li><li>Comandos personalizados</li><li>Operaciones compuestas</li></ul>


## Creación del conector DB2
Puede definir un conector dentro de una aplicación lógica o desde Azure Marketplace, como en el ejemplo siguiente:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. En la hoja **Todo**, escriba **db2** en el cuadro **Buscar en todo** y después haga clic en la tecla ENTRAR.
3. En el panel de resultados de Buscar en todo, seleccione **Conector de DB2**.
4. En la hoja de descripción del conector de DB2, seleccione **Crear**.
5. En la hoja del paquete del conector de DB2, escriba el nombre (por ejemplo, "Db2ConnectorNewOrders"), el plan del Servicio de aplicaciones y otras propiedades.
6. Seleccione **Configuración del paquete** e indique la siguiente configuración para el paquete:  

	Nombre | Obligatorio | Descripción
--- | --- | ---
ConnectionString | Sí | Cadena de conexión de cliente de DB2 (por ejemplo, "Network Address=nombreDeServidor;Network Port=50000;User ID=nombreDeUsuario;Password=contraseña;Initial Catalog=SAMPLE;Package Collection=NWIND;Default Schema=NWIND").
Tablas | Sí | Lista delimitada por comas de nombres de tabla, vista y alias necesarios para las operaciones de OData y para generar documentación de Swagger con ejemplos (por ejemplo, "*NEWORDERS*").
Procedimientos | Sí | Lista delimitada por comas de nombres de función y procedimiento (por ejemplo, "SPORDERID").
OnPremise | No | Implementación local mediante la Retransmisión de bus de servicio de Azure
ServiceBusConnectionString | No | Cadena de conexión de la Retransmisión de bus de servicio de Azure
PollToCheckData | No | Instrucción SELECT COUNT para usar con un desencadenador de aplicación lógica (por ejemplo, "SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL").
PollToReadData | No | Instrucción SELECT para usar con un desencadenador de aplicación lógica (por ejemplo, "SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE").
PollToAlterData | No | Instrucción UPDATE o DELETE para usar con un desencadenador de aplicación lógica (por ejemplo, "UPDATE NEWORDERS SET SHIPDATE = CURRENT DATE WHERE CURRENT OF &lt;CURSOR&gt;").

7. Seleccione **Aceptar** y después **Crear**.
8. Cuando termine, la configuración del paquete tendrá un aspecto similar al siguiente: ![][1]


## Aplicación lógica con la acción del conector de DB2 para agregar datos ##
Puede definir una acción de aplicación lógica para agregar datos a una tabla de DB2 mediante una operación de OData para la inserción o la publicación de API en una entidad. Por ejemplo, puede insertar un registro de pedido de cliente nuevo; para ello, se procesa una instrucción SQL INSERT en una tabla definida con una columna de identidad, lo que devuelve el valor de identidad o las filas afectadas a la aplicación lógica (SELECT ORDID FROM FINAL TABLE (INSERT INTO NWIND.NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

> [AZURE.TIP]Con "*Publicar en EntitySet*" en la conexión de DB2, se devuelve el valor de la columna de identidad y con "*Inserción de API*", se devuelven las filas afectadas.

1. En el panel de inicio de Azure, seleccione **+** (signo más), **Web y móvil** y después **Aplicación lógica**.
2. Escriba el nombre (por ejemplo, "NewOrdersDb2"), el plan del Servicio de aplicaciones y otras propiedades, y después seleccione **Crear**.
3. En el panel de inicio de Azure, seleccione la aplicación lógica que acaba de crear, **Configuración** y después **Desencadenadores y acciones**.
4. En la hoja Desencadenadores y acciones, seleccione **Crear desde cero** en Plantillas de aplicación lógica.
5. En el panel Aplicaciones de API, seleccione **Periodicidad**, establezca un intervalo y una frecuencia y después seleccione la **marca de verificación**.
6. En el panel Aplicaciones de API, seleccione **Conector de DB2** y expanda la lista de operaciones para seleccionar **Insertar en NEWORDER**.
7. Expanda la lista de parámetros para especificar los valores siguientes:  

	Nombre | Valor
--- | --- 
CUSTID | 10042
SHIPNAME | Lazy K Kountry Store 
SHIPADDR | 12 Orchestra Terrace
SHIPCITY | Walla Walla 
SHIPREG | WA
SHIPZIP | 99362 

8. Seleccione la **marca de verificación** para guardar la configuración de la acción y después haga clic en **Guardar**.
9. La configuración debe tener el aspecto siguiente: ![][3]

10. En la lista **Todas las ejecuciones** de **Operaciones**, seleccione el primer elemento (ejecución más reciente).
11. En la hoja **Ejecución de aplicación lógica**, seleccione el elemento de **Acción** **db2connectorneworders**.
12. En la hoja **Acción de aplicación lógica**, seleccione el **vínculo Entradas**. El conector de DB2 usa las entradas para procesar una instrucción INSERT con parámetros.
13. En la hoja **Acción de aplicación lógica**, seleccione el **vínculo Salidas**. Las entradas deben tener el aspecto siguiente: ![][4]

#### Lo que necesita saber

- El conector trunca los nombres de tabla de DB2 al formar los nombres de acción de aplicación lógica. Por ejemplo, la operación **Insertar en NEWORDERS** se trunca como **Insertar NEWORDER**.
- Después de guardar **Desencadenadores y acciones** para la aplicación lógica, esta procesa la operación. Puede haber un retraso de varios segundos (por ejemplo, entre 3 y 5 segundos) antes de que la aplicación lógica procese la operación. Opcionalmente, puede hacer clic en **Ejecutar ahora** para procesar la operación.
- El conector de DB2 define los miembros de EntitySet con atributos, incluso si el miembro corresponde a una columna de DB2 con un valor predeterminado o a columnas generadas (por ejemplo, identidad). En la aplicación lógica, se muestra un asterisco rojo junto al nombre de identidad del miembro de EntitySet para indicar las columnas de DB2 que requieren valores. No debe escribir un valor para el miembro ORDID, que corresponde a la columna de identidad de DB2. Puede escribir valores para otros miembros opcionales (ITEMS, ORDDATE, REQDATE, SHIPID, FREIGHT, SHIPCTRY), que corresponden a las columnas de DB2 con valores predeterminados. 
- El conector de DB2 devuelve a la aplicación lógica la respuesta en la acción Publicar en EntitySet que incluye los valores de las columnas de identidad, que se deriva de SQLDARD (datos de respuesta del área de datos de SQL) de DRDA en la instrucción SQL INSERT preparada. El servidor de DB2 no devuelve los valores insertados para aquellas columnas con valores predeterminados.  


## Aplicación lógica con la acción del conector de DB2 para agregar datos en masa ##
Puede definir una acción de aplicación lógica para agregar datos a una tabla de DB2 mediante una operación de inserción de API en masa. Por ejemplo, puede insertar dos registros de pedido de cliente nuevos; para ello, se procesa una instrucción SQL INSERT con una matriz de valores de fila en una tabla definida con una columna de identidad, lo que devuelve las filas afectadas a la aplicación lógica (SELECT ORDID FROM FINAL TABLE (INSERT INTO NWIND.NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

1. En el panel de inicio de Azure, seleccione **+** (signo más), **Web y móvil** y después **Aplicación lógica**.
2. Escriba el nombre (por ejemplo, "NewOrdersBulkDb2"), el plan del Servicio de aplicaciones y otras propiedades, y después seleccione **Crear**.
3. En el panel de inicio de Azure, seleccione la aplicación lógica que acaba de crear, **Configuración** y después **Desencadenadores y acciones**.
4. En la hoja Desencadenadores y acciones, seleccione **Crear desde cero** en Plantillas de aplicación lógica.
5. En el panel Aplicaciones de API, seleccione **Periodicidad**, establezca un intervalo y una frecuencia y después seleccione la **marca de verificación**.
6. En el panel Aplicaciones de API, seleccione **Conector de DB2** y expanda la lista de operaciones para seleccionar **Insertar en masa en NEW**.
7. Escriba el valor de **rows** como matriz. Por ejemplo, copie y pegue lo siguiente:

	```
    [{"CUSTID":10081,"SHIPNAME":"Trail's Head Gourmet Provisioners","SHIPADDR":"722 DaVinci Blvd.","SHIPCITY":"Kirkland","SHIPREG":"WA","SHIPZIP":"98034"},{"CUSTID":10088,"SHIPNAME":"White Clover Markets","SHIPADDR":"305 14th Ave. S. Suite 3B","SHIPCITY":"Seattle","SHIPREG":"WA","SHIPZIP":"98128","SHIPCTRY":"USA"}]
	```

8. Seleccione la **marca de verificación** para guardar la configuración de la acción y después haga clic en **Guardar**. La configuración debe tener el aspecto siguiente: ![][6]

9. En la lista **Todas las ejecuciones** de **Operaciones**, haga clic en el primer elemento (ejecución más reciente).
10. En la hoja **Ejecución de aplicación lógica**, haga clic en el elemento de **Acción**.
11. En la hoja **Acción de aplicación lógica**, haga clic en el **vínculo Entradas**. Las salidas deben tener el aspecto siguiente: [][7]
12. En la hoja **Acción de aplicación lógica**, haga clic en el **vínculo Salidas**. Las salidas deben tener el aspecto siguiente: ![][8]

#### Lo que necesita saber

- El conector trunca los nombres de tabla de DB2 al formar los nombres de acción de aplicación lógica. Por ejemplo, la operación **Insertar en masa en NEWORDERS** se trunca como **Insertar en masa en NEW**.
- Si se omiten las columnas de identidad (por ejemplo, ORDID), las columnas que aceptan valores null (por ejemplo, SHIPDATE) y las columnas con valores predeterminados (como ORDDATE, REQDATE, SHIPID, FREIGHT y SHIPCTRY), la base de datos de DB2 genera valores.
- Si se especifica "today" y "tomorrow", el conector de DB2 genera las funciones "CURRENT DATE" y "CURRENT DATE + 1 DAY" (por ejemplo, REQDATE). 


## Aplicación lógica con el desencadenador del conector de DB2 para leer, modificar o eliminar datos ##
Puede definir un desencadenador de aplicación lógica para sondear y leer datos de una tabla de DB2 mediante una operación compuesta de datos de sondeo de API. Por ejemplo, puede leer uno o más registros de pedido de cliente nuevos y devolver los registros a la aplicación lógica. La configuración de aplicación o de paquete de conexión de DB2 debe tener el aspecto siguiente:

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | <no value specified>


Además, puede definir un desencadenador de aplicación lógica para sondear, leer y modificar datos de una tabla de DB2 mediante una operación compuesta de datos de sondeo de API. Por ejemplo, puede leer uno o más registros de pedido de cliente nuevos, actualizar los valores de fila y devolver los registros seleccionados (antes de la actualización) a la aplicación lógica. La configuración de aplicación o de paquete de conexión de DB2 debe tener el aspecto siguiente:

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | UPDATE NEWORDERS SET SHIPDATE = CURRENT DATE WHERE CURRENT OF &lt;CURSOR&gt;


Además, puede definir un desencadenador de aplicación lógica para sondear, leer y quitar datos de una tabla de DB2 mediante una operación compuesta de datos de sondeo de API. Por ejemplo, puede leer uno o más registros de pedido de cliente nuevos, eliminar las filas y devolver los registros seleccionados (antes de la eliminación) a la aplicación lógica. La configuración de aplicación o de paquete de conexión de DB2 debe tener el aspecto siguiente:

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | DELETE NEWORDERS WHERE CURRENT OF &lt;CURSOR&gt;

En este ejemplo, la aplicación lógica sondeará, leerá, actualizará y después volverá a leer los datos en la tabla de DB2.

1. En el panel de inicio de Azure, seleccione **+** (signo más), **Web y móvil** y después **Aplicación lógica**.
2. Escriba el nombre (por ejemplo, "ShipOrdersDb2"), el plan del Servicio de aplicaciones y otras propiedades, y después seleccione **Crear**.
3. En el panel de inicio de Azure, seleccione la aplicación lógica que acaba de crear, **Configuración** y después **Desencadenadores y acciones**.
4. En la hoja Desencadenadores y acciones, seleccione **Crear desde cero** en Plantillas de aplicación lógica.
5. En el panel Aplicaciones de API, seleccione **Conector de DB2**, establezca un intervalo y una frecuencia y después seleccione la **marca de verificación**.
6. En el panel Aplicaciones de API, seleccione **Conector de DB2** y expanda la lista de operaciones para elegir **Seleccionar desde NEWORDERS**.
7. Seleccione la **marca de verificación** para guardar la configuración de la acción y después haga clic en **Guardar**. La configuración debe tener el aspecto siguiente: ![][10]  
8. Haga clic para cerrar la hoja **Desencadenadores y acciones**, y después repita la acción para cerrar la hoja **Configuración**.
9. En la lista **Todas las ejecuciones** de **Operaciones**, haga clic en el primer elemento (ejecución más reciente).
10. En la hoja **Ejecución de aplicación lógica**, haga clic en el elemento de **Acción**.
11. En la hoja **Acción de aplicación lógica**, haga clic en el **vínculo Salidas**. Las salidas deben tener el aspecto siguiente: ![][11]


## Aplicación lógica con la acción del conector de DB2 para quitar datos ##
Puede definir una acción de aplicación lógica para quitar datos de una tabla de DB2 mediante una operación de OData para la eliminación de API o la publicación en una entidad. Por ejemplo, puede insertar un registro de pedido de cliente nuevo; para ello, se procesa una instrucción SQL INSERT en una tabla definida con una columna de identidad, lo que devuelve el valor de identidad o las filas afectadas a la aplicación lógica (SELECT ORDID FROM FINAL TABLE (INSERT INTO NWIND.NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

## Creación de una aplicación lógica con el conector de DB2 para quitar datos ##
Puede crear una nueva aplicación lógica desde Azure Marketplace y después usar el conector de DB2 como acción para quitar pedidos de cliente. Por ejemplo, puede usar la operación de eliminación condicional del conector de DB2 para procesar una instrucción SQL DELETE (DELETE FROM NEWORDERS WHERE ORDID >= 10000).

1. En el menú del concentrador en el **panel de inicio** de Azure, haga clic en **+** (signo más), en **Web y móvil** y después en **Aplicación lógica**. 
2. En la hoja **Crear aplicación lógica**, escriba un valor para **Nombre**, como por ejemplo **RemoveOrdersDb2**.
3. Seleccione o defina los valores para las demás configuraciones (por ejemplo, el plan de servicio, el grupo de recursos).
4. La configuración debe tener el aspecto siguiente. Haga clic en **Crear**: ![][12]  
5. En la hoja **Configuración**, haga clic en **Desencadenadores y acciones**.
6. En la hoja **Desencadenadores y acciones**, en la lista **Plantillas de aplicación lógica**, haga clic en **Crear desde cero**.
7. En la hoja **Desencadenadores y acciones**, en el panel **Aplicaciones de API**, dentro del grupo de recursos, haga clic en **Periodicidad**.
8. En la superficie de diseño de aplicación lógica, haga clic en el elemento **Periodicidad** y establezca valores en **Frecuencia** e **Intervalo**, por ejemplo **Días** y **1**. Después, haga clic en la **marca de verificación** para guardar la configuración del elemento de periodicidad.
9. En la hoja **Desencadenadores y acciones**, en el panel **Aplicaciones de API**, dentro del grupo de recursos, haga clic en **Conector de DB2**.
10. En la superficie de diseño de aplicación lógica, haga clic en el elemento de acción **Conector de DB2**, haga clic en el botón de puntos suspensivos (**…**) para expandir la lista de operaciones y después haga clic en **Eliminación condicional de N**.
11. En el elemento de acción del conector de DB2, escriba **ORDID ge 10000** para una **expresión que identifica un subconjunto de entradas**.
12. Haga clic en la **marca de verificación** para guardar la configuración de la acción y después haga clic en **Guardar**. La configuración debe tener el aspecto siguiente: ![][13]  
13. Haga clic para cerrar la hoja **Desencadenadores y acciones**, y después repita la acción para cerrar la hoja **Configuración**.
14. En la lista **Todas las ejecuciones** de **Operaciones**, haga clic en el primer elemento (ejecución más reciente).
15. En la hoja **Ejecución de aplicación lógica**, haga clic en el elemento de **Acción**.
16. En la hoja **Acción de aplicación lógica**, haga clic en el **vínculo Salidas**. Las salidas deben tener el aspecto siguiente: ![][14]

**Nota:** El diseñador de aplicaciones lógicas trunca los nombres de tabla. Por ejemplo, la operación **Eliminación condicional de NEWORDERS** se trunca como **Eliminación condicional de N**.


> [AZURE.TIP]Use las siguientes instrucciones SQL para crear la tabla y los procedimientos almacenados de ejemplo.

Puede crear la tabla NEWORDERS de ejemplo mediante las siguientes instrucciones DDL de SQL para DB2:
 
 	CREATE TABLE ORDERS (  
 		ORDID INT NOT NULL GENERATED BY DEFAULT AS IDENTITY (START WITH 10000, INCREMENT BY 1) ,  
 		CUSTID INT NOT NULL ,  
 		EMPID INT NOT NULL DEFAULT 10000 ,  
 		ORDDATE DATE NOT NULL DEFAULT CURRENT DATE ,  
 		REQDATE DATE DEFAULT CURRENT DATE ,  
 		SHIPDATE DATE ,  
 		SHIPID INT NOT NULL DEFAULT 10000,  
 		FREIGHT DECIMAL (9,2) NOT NULL DEFAULT 0.00 ,  
 		SHIPNAME CHAR (40) NOT NULL ,  
 		SHIPADDR CHAR (60) NOT NULL ,  
 		SHIPCITY CHAR (20) NOT NULL ,  
 		SHIPREG CHAR (15) NOT NULL ,  
 		SHIPZIP CHAR (10) NOT NULL ,  
 		SHIPCTRY CHAR (15) NOT NULL DEFAULT 'USA' ,  
 		PRIMARY KEY(ORDID)  
 		)  
 
 	CREATE UNIQUE INDEX XORDID ON ORDERS (ORDID ASC)  



Puede crear el procedimiento almacenado SPOERID de ejemplo mediante la siguiente instrucción DDL para DB2:
 
 	CREATE OR REPLACE PROCEDURE NWIND.SPORDERID (IN ORDERID VARCHAR(128))  
 		DYNAMIC RESULT SETS 1  
 	P1: BEGIN  
 		DECLARE CURSOR1 CURSOR WITH RETURN FOR  
 			SELECT * FROM NWIND.NEWORDERS  
 				WHERE ORDID = ORDERID;  
 		OPEN CURSOR1;  
 	END P1  
 	') 


## Configuración híbrida (opcional)

> [AZURE.NOTE]Este paso solo es necesario si usa un conector de DB2 local tras el firewall.

El Servicio de aplicaciones utiliza el Administrador de configuración híbrida para conectarse de forma segura al sistema local. Si el conector usa un servidor IBM DB2 local para Windows, se necesita el Administrador de conexiones híbridas.

Consulte [Uso del Administrador de conexiones híbridas](app-service-logic-hybrid-connection-manager.md).


## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

Cree las aplicaciones de API mediante las API de REST. Consulte [Referencia de Aplicaciones de API y conectores](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-db2/ApiApp_Db2Connector_Create.png
[2]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Create.png
[3]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_TriggersActions.png
[4]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Outputs.png
[5]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Create.png
[6]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_TriggersActions.png
[7]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Inputs.png
[8]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Outputs.png
[9]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Create.png
[10]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_TriggersActions.png
[11]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Outputs.png
[12]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Create.png
[13]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_TriggersActions.png
[14]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Outputs.png

<!---HONumber=AcomDC_1210_2015-->