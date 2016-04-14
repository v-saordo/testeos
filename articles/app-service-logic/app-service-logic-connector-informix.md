<properties
   pageTitle="Uso del conector de Informix en el Servicio de aplicaciones de Microsoft Azure | Microsoft Azure"
   description="Cómo usar el conector de Informix con desencadenadores y acciones de aplicación lógica"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="gplarsen"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/10/2016"
   ms.author="plarsen"/>

# Conector de Informix
>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas.

Microsoft Connector para Informix es una aplicación de API para conectar aplicaciones a los recursos almacenados en una base de datos Informix de IBM a través del Servicio de aplicaciones de Azure. El conector incluye un cliente Microsoft para conectarse a equipos servidores remotos de Informix a través de una conexión de red TCP/IP, incluidas las conexiones híbridas de Azure con servidores locales de Informix mediante la Retransmisión de bus de servicio de Azure. El conector admite las siguientes operaciones de base de datos:

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


## Creación del conector de Informix
Puede definir un conector dentro de una aplicación lógica o desde Azure Marketplace, como en el ejemplo siguiente:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. En la hoja **Todo**, escriba **informix** en el cuadro **Buscar en todo** y después haga clic en la tecla ENTRAR.
3. En el panel de resultados para Buscar en todo, seleccione **Conector de Informix**.
4. En la hoja de descripción del conector de Informix, seleccione **Crear**.
5. En la hoja del paquete del conector de Informix, escriba el nombre (por ejemplo, "InformixConnectorNewOrders"), el plan del Servicio de aplicaciones y otras propiedades.
6. Seleccione **Configuración del paquete** e indique la siguiente configuración para el paquete.

	Nombre | Obligatorio | Descripción
--- | --- | ---
ConnectionString | Sí | Cadena de conexión de cliente de Informix (por ejemplo, "Network Address=nombreDeServidor;Network Port=9089;User ID=nombreDeUsuario;Password=contraseña;Initial Catalog=nwind;Default Schema=informix").
Tablas | Sí | Lista delimitada por comas de nombres de tabla, vista y alias necesarios para las operaciones de OData y para generar documentación de Swagger con ejemplos (por ejemplo, "NEWORDERS").
Procedimientos | Sí | Lista delimitada por comas de nombres de función y procedimiento (por ejemplo, "SPORDERID").
OnPremise | No | Implementación local mediante la Retransmisión de bus de servicio de Azure
ServiceBusConnectionString | No | Cadena de conexión de la Retransmisión de bus de servicio de Azure
PollToCheckData | No | Instrucción SELECT COUNT para usar con un desencadenador de aplicación lógica (por ejemplo, "SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL").
PollToReadData | No | Instrucción SELECT para usar con un desencadenador de aplicación lógica (por ejemplo, "SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE").
PollToAlterData | No | Instrucción UPDATE o DELETE para usar con un desencadenador de aplicación lógica (por ejemplo, "UPDATE NEWORDERS SET SHIPDATE = CURRENT DATE WHERE CURRENT OF &lt;CURSOR&gt;").

7. Seleccione **Aceptar** y después **Crear**.
8. Cuando termine, la configuración del paquete tendrá un aspecto similar al siguiente: ![][1]


## Aplicación lógica con la acción del conector de Informix para agregar datos ##
Puede definir una acción de aplicación lógica para agregar datos a una tabla de Informix mediante una operación de OData para la inserción de API o la publicación en una entidad. Por ejemplo, puede insertar un registro de pedido de cliente nuevo; para ello, se procesa una instrucción SQL INSERT en una tabla definida con una columna de identidad, lo que devuelve el valor de identidad o las filas afectadas a la aplicación lógica (SELECT ORDID FROM FINAL TABLE (INSERT INTO NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

> [AZURE.TIP] Con "*Publicar en EntitySet*" en la conexión de Informix, se devuelve el valor de la columna de identidad y con "*Inserción de API*", se devuelven las filas afectadas.

1. En el panel de inicio de Azure, seleccione **+** (signo más), **Web y móvil** y después **Aplicación lógica**.
2. Escriba el nombre (por ejemplo, "NewOrdersInformix"), el plan del Servicio de aplicaciones y otras propiedades, y después seleccione **Crear**.
3. En el panel de inicio de Azure, seleccione la aplicación lógica que acaba de crear, **Configuración** y después **Desencadenadores y acciones**.
4. En la hoja Desencadenadores y acciones, seleccione **Crear desde cero** en Plantillas de aplicación lógica.
5. En el panel Aplicaciones de API, seleccione **Periodicidad**, establezca un intervalo y una frecuencia y después seleccione la **marca de verificación**.
6. En el panel Aplicaciones de API, seleccione **Conector de Informix** y expanda la lista de operaciones para seleccionar **Insertar en NEWORDER**.
7. Expanda la lista de parámetros para especificar los valores siguientes:  

	Nombre | Valor
--- | --- 
CUSTID | 10042
SHIPID | 10000
SHIPNAME | Lazy K Kountry Store 
SHIPADDR | 12 Orchestra Terrace
SHIPCITY | Walla Walla 
SHIPREG | WA
SHIPZIP | 99362 

8. Seleccione la **marca de verificación** para guardar la configuración de la acción y después haga clic en **Guardar**.
9. La configuración debe tener el aspecto siguiente: ![][3]  
10. En la lista **Todas las ejecuciones** de **Operaciones**, seleccione el primer elemento (ejecución más reciente). 
11. En la hoja **Ejecución de aplicación lógica**, seleccione el elemento **informixconnectorneworders** de **Acción**.
12. En la hoja **Acción de aplicación lógica**, seleccione el **vínculo Entradas**. El conector de Informix usa las entradas para procesar una instrucción INSERT con parámetros.
13. En la hoja **Acción de aplicación lógica**, seleccione el **vínculo Salidas**. Las entradas deben tener el aspecto siguiente: ![][4]

#### Lo que necesita saber

- El conector trunca los nombres de tabla de Informix al formar los nombres de acción de aplicación lógica. Por ejemplo, la operación **Insertar en NEWORDERS** se trunca como **Insertar NEWORDER**.
- Después de guardar **Desencadenadores y acciones** para la aplicación lógica, esta procesa la operación. Puede haber un retraso de varios segundos (por ejemplo, entre 3 y 5 segundos) antes de que la aplicación lógica procese la operación. Opcionalmente, puede hacer clic en **Ejecutar ahora** para procesar la operación.
- El conector de Informix define los miembros de EntitySet con atributos, lo que incluye si el miembro corresponde a una columna de Informix con un valor predeterminado o columnas generadas (por ejemplo, identidad). En la aplicación lógica, se muestra un asterisco rojo junto al nombre de identidad del miembro de EntitySet para indicar las columnas de Informix que requieren valores. No debe escribir un valor para el miembro ORDID, que corresponde a la columna de identidad de Informix. Puede escribir valores para otros miembros opcionales (ITEMS, ORDDATE, REQDATE, SHIPID, FREIGHT, SHIPCTRY), que corresponden a columnas de Informix con valores predeterminados. 
- El conector de Informix devuelve a la aplicación lógica la respuesta en la acción Publicar en EntitySet que incluye los valores de las columnas de identidad, que se deriva de SQLDARD (datos de respuesta del área de datos de SQL) de DRDA en la instrucción SQL INSERT preparada. El servidor de Informix no devuelve los valores insertados para aquellas columnas con valores predeterminados.  


## Aplicación lógica con la acción del conector de Informix para agregar datos en masa ##
Puede definir una acción de aplicación lógica para agregar datos a una tabla de Informix mediante una operación de inserción de API en masa. Por ejemplo, puede insertar dos registros de pedido de cliente nuevos; para ello, se procesa una instrucción SQL INSERT con una matriz de valores de fila en una tabla definida con una columna de identidad, lo que devuelve las filas afectadas a la aplicación lógica (SELECT ORDID FROM FINAL TABLE (INSERT INTO NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

1. En el panel de inicio de Azure, seleccione **+** (signo más), **Web y móvil** y después **Aplicación lógica**.
2. Escriba el nombre (por ejemplo, "NewOrdersBulkInformix"), el plan del Servicio de aplicaciones y otras propiedades, y después seleccione **Crear**.
3. En el panel de inicio de Azure, seleccione la aplicación lógica que acaba de crear, **Configuración** y después **Desencadenadores y acciones**.
4. En la hoja Desencadenadores y acciones, seleccione **Crear desde cero** en Plantillas de aplicación lógica.
5. En el panel Aplicaciones de API, seleccione **Periodicidad**, establezca un intervalo y una frecuencia y después seleccione la **marca de verificación**.
6. En el panel Aplicaciones de API, seleccione **Conector de Informix** y expanda la lista de operaciones para seleccionar **Insertar en masa en NEW**.
7. Escriba el valor de **rows** como matriz. Por ejemplo, copie y pegue lo siguiente:  

	```
    [{"custid":10081,"shipid":10000,"shipname":"Trail's Head Gourmet Provisioners","shipaddr":"722 DaVinci Blvd.","shipcity":"Kirkland","shipreg":"WA","shipzip":"98034"},{"custid":10088,"shipid":10000,"shipname":"White Clover Markets","shipaddr":"305 14th Ave. S. Suite 3B","shipcity":"Seattle","shipreg":"WA","shipzip":"98128","shipctry":"USA"}]
	```
        
8. Seleccione la **marca de verificación** para guardar la configuración de la acción y después haga clic en **Guardar**. La configuración debe tener el aspecto siguiente: ![][6]

9. En la lista **Todas las ejecuciones** de **Operaciones**, haga clic en el primer elemento (ejecución más reciente).
10. En la hoja **Ejecución de aplicación lógica**, haga clic en el elemento de **Acción**.
11. En la hoja **Acción de aplicación lógica**, haga clic en el **vínculo Entradas**. Las salidas deben tener el aspecto siguiente: [][7]
12. En la hoja **Acción de aplicación lógica**, haga clic en el **vínculo Salidas**. Las salidas deben tener el aspecto siguiente: ![][8]

#### Lo que necesita saber

- El conector trunca los nombres de tabla de Informix al formar los nombres de acción de aplicación lógica. Por ejemplo, la operación **Insertar en masa en NEWORDERS** se trunca como **Insertar en masa en NEW**.
- Es posible que la base de datos de Informix distinga mayúsculas de minúsculas en los nombres de tablas y columnas. Por ejemplo, puede que deba especificar los nombres de las columnas de la matriz en la operación Insertar en masa en minúsculas ("custid") en lugar de mayúsculas ("CUSTID").
- Si se omiten las columnas de identidad (por ejemplo, ORDID), las columnas que aceptan valores null (por ejemplo, SHIPDATE) y las columnas con valores predeterminados (como ORDDATE, REQDATE, SHIPID, FREIGHT y SHIPCTRY), la base de datos de Informix genera valores.
- Si se especifica "today" y "tomorrow", el conector de Informix genera las funciones "CURRENT DATE" y "CURRENT DATE + 1 DAY" (por ejemplo, REQDATE). 


## Aplicación lógica con el desencadenador del conector de Informix para leer, modificar o eliminar datos ##
Puede definir un desencadenador de aplicación lógica para sondear y leer datos de una tabla de Informix mediante una operación compuesta de datos de sondeo de API. Por ejemplo, puede leer uno o más registros de pedido de cliente nuevos y devolver los registros a la aplicación lógica. La configuración de aplicación o de paquete de conexión de Informix debe tener el aspecto siguiente:

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | <no value specified>


Además, puede definir un desencadenador de aplicación lógica para sondear, leer y modificar datos de una tabla de Informix mediante una operación compuesta de datos de sondeo de API. Por ejemplo, puede leer uno o más registros de pedido de cliente nuevos, actualizar los valores de fila y devolver los registros seleccionados (antes de la actualización) a la aplicación lógica. La configuración de aplicación o de paquete de conexión de Informix debe tener el aspecto siguiente:

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | UPDATE NEWORDERS SET SHIPDATE = CURRENT DATE WHERE CURRENT OF &lt;CURSOR&gt;


Además, puede definir un desencadenador de aplicación lógica para sondear, leer y quitar datos de una tabla de Informix mediante una operación compuesta de datos de sondeo de API. Por ejemplo, puede leer uno o más registros de pedido de cliente nuevos, eliminar las filas y devolver los registros seleccionados (antes de la eliminación) a la aplicación lógica. La configuración de aplicación o de paquete de conexión de Informix debe tener el aspecto siguiente:

	App Setting | Value
--- | --- | ---
PollToCheckData | SELECT COUNT(*) FROM NEWORDERS WHERE SHIPDATE IS NULL
PollToReadData | SELECT * FROM NEWORDERS WHERE SHIPDATE IS NULL FOR UPDATE
PollToAlterData | DELETE NEWORDERS WHERE CURRENT OF &lt;CURSOR&gt;

En este ejemplo, la aplicación lógica sondeará, leerá, actualizará y después volverá a leer los datos en la tabla de Informix.

1. En el panel de inicio de Azure, seleccione **+** (signo más), **Web y móvil** y después **Aplicación lógica**.
2. Escriba el nombre (por ejemplo, "ShipOrdersInformix"), el plan del Servicio de aplicaciones y otras propiedades, y después seleccione **Crear**.
3. En el panel de inicio de Azure, seleccione la aplicación lógica que acaba de crear, **Configuración** y después **Desencadenadores y acciones**.
4. En la hoja Desencadenadores y acciones, seleccione **Crear desde cero** en Plantillas de aplicación lógica.
5. En el panel Aplicaciones de API, seleccione **Conector de Informix**, establezca un intervalo y una frecuencia y después seleccione la **marca de verificación**.
6. En el panel Aplicaciones de API, seleccione **Conector de Informix** y expanda la lista de operaciones para elegir **Seleccionar desde NEWORDERS**.
7. Seleccione la **marca de verificación** para guardar la configuración de la acción y después haga clic en **Guardar**. La configuración debe tener el aspecto siguiente: ![][10]
8. Haga clic para cerrar la hoja **Desencadenadores y acciones**, y después repita la acción para cerrar la hoja **Configuración**.
9. En la lista **Todas las ejecuciones** de **Operaciones**, haga clic en el primer elemento (ejecución más reciente).
10. En la hoja **Ejecución de aplicación lógica**, haga clic en el elemento de **Acción**.
11. En la hoja **Acción de aplicación lógica**, haga clic en el **vínculo Salidas**. Las salidas deben tener el aspecto siguiente: ![][11]


## Aplicación lógica con la acción del conector de Informix para quitar datos ##
Puede definir una acción de aplicación lógica para quitar datos de una tabla de Informix mediante una operación de OData para la eliminación de API o la publicación en una entidad. Por ejemplo, puede insertar un registro de pedido de cliente nuevo; para ello, se procesa una instrucción SQL INSERT en una tabla definida con una columna de identidad, lo que devuelve el valor de identidad o las filas afectadas a la aplicación lógica (SELECT ORDID FROM FINAL TABLE (INSERT INTO NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) VALUES (?,?,?,?,?,?))).

## Creación de una aplicación lógica con el conector de Informix para quitar datos ##
Puede crear una nueva aplicación lógica desde Azure Marketplace y después usar el conector de Informix como acción para quitar pedidos de cliente. Por ejemplo, puede usar la operación de eliminación condicional del conector de Informix para procesar una instrucción SQL DELETE (DELETE FROM NEWORDERS WHERE ORDID >= 10000).

1. En el menú del concentrador en el **panel de inicio** de Azure, haga clic en **+** (signo más), en **Web y móvil** y después en **Aplicación lógica**. 
2. En la hoja **Crear aplicación lógica**, escriba un valor para **Nombre**, como por ejemplo **RemoveOrdersInformix**.
3. Seleccione o defina los valores para las demás configuraciones (por ejemplo, el plan de servicio, el grupo de recursos).
4. La configuración debe tener el aspecto siguiente. Haga clic en **Crear**: ![][12]
5. En la hoja **Configuración**, haga clic en **Desencadenadores y acciones**.
6. En la hoja **Desencadenadores y acciones**, en la lista **Plantillas de aplicación lógica**, haga clic en **Crear desde cero**.
7. En la hoja **Desencadenadores y acciones**, en el panel **Aplicaciones de API**, dentro del grupo de recursos, haga clic en **Periodicidad**.
8. En la superficie de diseño de aplicación lógica, haga clic en el elemento **Periodicidad** y establezca valores en **Frecuencia** e **Intervalo**, por ejemplo **Días** y **1**. Después, haga clic en la **marca de verificación** para guardar la configuración del elemento de periodicidad.
9. En la hoja **Desencadenadores y acciones**, en el panel **Aplicaciones de API**, dentro del grupo de recursos, haga clic en **Conector de Informix**.
10. En la superficie de diseño de aplicación lógica, haga clic en el elemento de acción **Conector de Informix**, haga clic en el botón de puntos suspensivos (**…**) para expandir la lista de operaciones y después haga clic en **Eliminación condicional de N**.
11. En el elemento de acción del conector de Informix, escriba **ordid ge 10000** para una **expresión que identifica un subconjunto de entradas**.
12. Haga clic en la **marca de verificación** para guardar la configuración de la acción y después haga clic en **Guardar**. La configuración debe tener el aspecto siguiente: ![][13]
13. Haga clic para cerrar la hoja **Desencadenadores y acciones**, y después repita la acción para cerrar la hoja **Configuración**.
14. En la lista **Todas las ejecuciones** de **Operaciones**, haga clic en el primer elemento (ejecución más reciente).
15. En la hoja **Ejecución de aplicación lógica**, haga clic en el elemento de **Acción**.
16. En la hoja **Acción de aplicación lógica**, haga clic en el **vínculo Salidas**. Las salidas deben tener el aspecto siguiente: ![][14]

**Nota:** El diseñador de aplicaciones lógicas trunca los nombres de tabla. Por ejemplo, la operación **Eliminación condicional de NEWORDERS** se trunca como **Eliminación condicional de N**.


> [AZURE.TIP] Use las siguientes instrucciones SQL para crear la tabla y los procedimientos almacenados de ejemplo.

Puede crear la tabla NEWORDERS de ejemplo mediante las siguientes instrucciones DDL de SQL para Informix:
 
    create table neworders (  
 		ordid serial(10000) unique ,  
 		custid int not null ,  
 		empid int not null default 10000 ,  
 		orddate date not null default today ,  
 		reqdate date default today ,  
 		shipdate date ,  
 		shipid int not null default 10000 ,  
 		freight decimal (9,2) not null default 0.00 ,  
 		shipname char (40) not null ,  
 		shipaddr char (60) not null ,  
 		shipcity char (20) not null ,  
 		shipreg char (15) not null ,  
 		shipzip char (10) not null ,  
 		shipctry char (15) not null default ''USA'' 
 		)


Puede crear el procedimiento almacenado SPORDERID de ejemplo mediante la siguiente instrucción DDL para Informix:
 
    create procedure sporderid ( ord_id int)  
 		returning int, int, int, date, date, date, int, decimal (9,2), char (40), char (60), char (20), char (15), char (10), char (15)  
 		define xordid, xcustid, xempid, xshipid int;  
 		define xorddate, xreqdate, xshipdate date;  
 		define xfreight decimal (9,2);  
 		define xshipname char (40);  
 		define xshipaddr char (60);  
 		define xshipcity char (20);  
 		define xshipreg, xshipctry char (15);  
 		define xshipzip char (10);  
 		select ordid, custid, empid, orddate, reqdate, shipdate, shipid, freight, shipname, shipaddr, shipcity, shipreg, shipzip, shipctry  
 			into xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry  
 			from neworders where ordid = ord_id;  
 		return xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry;  
    end procedure; 


## Configuración híbrida (opcional)

> [AZURE.NOTE] Este paso solo es necesario si usa un conector de DB2 local tras el firewall.

El Servicio de aplicaciones utiliza el Administrador de configuración híbrida para conectarse de forma segura al sistema local. Si el conector usa un servidor IBM DB2 local para Windows, se necesita el Administrador de conexiones híbridas.

Consulte [Uso del Administrador de conexiones híbridas](app-service-logic-hybrid-connection-manager.md).


## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

Cree las aplicaciones de API mediante las API de REST. Consulte [Referencia sobre conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-informix/ApiApp_InformixConnector_Create.png
[2]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Create.png
[3]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_TriggersActions.png
[4]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Outputs.png
[5]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Create.png
[6]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_TriggersActions.png
[7]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Inputs.png
[8]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Outputs.png
[9]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Create.png
[10]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_TriggersActions.png
[11]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Outputs.png
[12]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Create.png
[13]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_TriggersActions.png
[14]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Outputs.png

<!---HONumber=AcomDC_0224_2016-->