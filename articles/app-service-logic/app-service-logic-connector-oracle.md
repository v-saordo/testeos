<properties
   pageTitle="Uso del conector de base de datos de Oracle en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
   description="Creación y configuración del conector de base de datos de Oracle o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="11/30/2015"
   ms.author="sameerch"/>


# Introducción al conector de base de datos de Oracle y su incorporación a su aplicación lógica
Conéctese a un servidor de base de datos de Oracle local para crear y modificar la información o los datos. Los conectores pueden utilizarse en aplicaciones lógicas para recuperar, procesar o insertar datos como parte de un “flujo de trabajo”. Al utilizar el conector de Oracle en su flujo de trabajo, puede conseguir distintos escenarios. Por ejemplo, puede:

- Exponer una parte de los datos que residen en la base de datos Oracle a través de una aplicación móvil o web.
- Insertar los datos en la tabla de base de datos de Oracle para almacenarlos. Por ejemplo, puede especificar los registros de empleados, actualizar los pedidos de ventas y así sucesivamente.
- Obtener datos de Oracle para usarlos en un proceso empresarial. Por ejemplo, puede obtener los registros de clientes y colocarlos en SalesForce.


## Acciones y desencadenadores
Los *desencadenadores* son eventos que se producen. Por ejemplo, cuando se actualiza un pedido o cuando se agrega un cliente nuevo. Un *acción* es el resultado del desencadenador. Por ejemplo, cuando se actualiza un pedido,se envía una alerta al vendedor. O bien, cuando se agrega un nuevo cliente, a este se le envía un correo electrónico de bienvenida.

El conector de base de datos Oracle puede usarse como un desencadenador o una acción en una aplicación lógica y es compatible con los datos en formato JSON y XML. Para cada tabla incluida en la configuración del paquete (puede encontrar información adicional al respecto más adelante en este tema), hay un conjunto de acciones JSON y un conjunto de acciones XML. Si se utiliza la acción/desencadenador XML, puede utilizar la [aplicación de API de transformación](app-service-logic-transform-xml-documents.md) para convertir datos en otro formato de datos XML.

El conector de base de datos de Oracle tiene los siguientes desencadenadores y acciones disponibles:

Desencadenadores | Acciones
--- | ---
Datos de sondeo | <ul><li>Insertar en tabla</li><li>Actualizar tabla</li><li>Seleccionar en tabla</li><li>Eliminar de tabla</li><li>Llamar a procedimiento almacenado</li>


## Creación de un conector de base de datos de Oracle

Un conector puede crearse dentro de una aplicación lógica o directamente desde Azure Marketplace. Para crear un conector desde Marketplace:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. Seleccione **Aplicaciones de API** y busque "Conector de base de datos de Oracle".
3. Escriba el nombre, el plan del Servicio de aplicaciones y otras propiedades.
4. Escriba la siguiente configuración del paquete:

	Nombre | Obligatorio | Descripción
--- | --- | ---
Origen de datos | Sí | Un nombre de origen de datos (servicio de red) que se especifica en el archivo tnsnames.ora en el equipo donde está instalado el cliente de Oracle. Para obtener información acerca de los nombres del origen de datos y de tnsnames.ora, consulte [Configuración del cliente de Oracle](http://msdn.microsoft.com/library/dd787872.aspx).
User Name | Sí | Escriba un nombre de usuario válido para conectarse al servidor de Oracle.
Password | Sí | Escriba la contraseña del nombre de usuario.
Cadena de conexión del bus de servicio | Sí | Si se conecta en un entorno local, escriba la cadena de conexión de Retransmisión de bus de servicio.<br/><br/>[Uso del Administrador de conexiones híbridas](app-service-logic-hybrid-connection-manager.md)<br/>[Precios de Bus de servicio](https://azure.microsoft.com/pricing/details/service-bus/)
Tablas | No | Especifique las tablas de la base de datos que pueden modificarse mediante el conector. Por ejemplo, escriba *OrdersTable,EmployeeTable*.
Procedimientos almacenados | No | Especifique los procedimientos almacenados en la base de datos que se pueden llamar mediante el conector. Por ejemplo, escriba *IsEmployeeEligible,CalculateOrderDiscount*.
Funciones | No | Especifique las funciones de la base de datos que se pueden llamar mediante el conector. Por ejemplo, escriba *IsEmployeeEligible,CalculateOrderDiscount*.
Entidades de paquete | No | Especifique los paquetes de la base de datos que se pueden llamar mediante el conector. Por ejemplo, escriba *PackageOrderProcessing.CompleteOrder,PackageOrderProcessing.GenerateBill*.
Instrucción de datos disponibles | No | Especifique la instrucción para determinar si los datos están disponibles para el sondeo. Por ejemplo, escriba *SELECT * from table\_name*.
Tipo de sondeo | No | Especifique el tipo de sondeo. Los valores permitidos son "Select", "Procedure", "Function" y "Package".
Instrucción de sondeo | No | Especifique la instrucción para sondear la base de datos del servidor de Oracle. Por ejemplo, escriba *SELECT * from table\_name*.
Instrucción de sondeo de envío | No | Especifique la instrucción para ejecutarla después del sondeo. Por ejemplo, escriba *DELETE * from table\_name*.

5. Cuando haya terminado, la configuración del paquete tiene un aspecto similar al siguiente: <br/> ![][1]


## Uso del conector como desencadenador
Veamos una aplicación lógica sencilla que sondea datos en una tabla de Oracle, agrega los datos en otra tabla y actualiza los datos.

### Incorporación del desencadenador
1. Al crear o editar una aplicación lógica, seleccione el conector de Oracle que ha creado como desencadenador. Se mostrarán los desencadenadores disponibles: **Datos de sondeo (JSON)** y **Datos de sondeo (XML)**: <br/> ![][5]

2. Seleccione el desencadenador **Datos de sondeo (JSON)**, especifique la frecuencia y haga clic en el signo ✓: <br/> ![][6]

3. El desencadenador aparece ahora como configurado en la aplicación lógica. Se muestran las salidas del desencadenador y se pueden utilizar como entradas en un paso posterior: <br/> ![][7]

## Uso del conector como acción
Vamos a usar una aplicación lógica sencilla que sondea datos en una tabla de Oracle, agrega los datos en otra tabla y actualiza los datos.

Para usar el conector de Oracle como una acción, escriba el nombre de las tablas o procedimientos almacenados que especificó al crear el conector de Oracle:

1. Seleccione el mismo conector de Oracle de la Galería como una acción. Seleccione una de las acciones de inserción, como *insertar en TempEmployeeDetails (JSON)*: <br/> ![][8]

2. Escriba los valores de entrada del registro que se va a insertar y haga clic en el signo ✓: <br/> ![][9]

3. En la Galería, seleccione el mismo conector de Oracle que ha creado. Como una acción, seleccione la acción de actualización en la misma tabla, como *actualizar TempEmployeeDetails*: <br/> ![][11]

4. Escriba los valores de entrada para la acción de actualización y haga clic en el signo ✓: <br/> ![][12]

Puede probar la aplicación lógica mediante la adición de un nuevo registro en la tabla que se sondea.

## Configuración híbrida

> [AZURE.NOTE] Este paso solo es necesario si está utilizando Oracle local detrás del firewall.

El Servicio de aplicaciones utiliza el Administrador de configuración híbrida para conectarse de forma segura al sistema local. Si el conector usa Oracle en un entorno local, se necesita el Administrador de conexiones híbridas.

Consulte [Uso del Administrador de conexiones híbridas](app-service-logic-hybrid-connection-manager.md).

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-oracle/Create.png
[5]: ./media/app-service-logic-connector-oracle/LogicApp1.png
[6]: ./media/app-service-logic-connector-oracle/LogicApp2.png
[7]: ./media/app-service-logic-connector-oracle/LogicApp3.png
[8]: ./media/app-service-logic-connector-oracle/LogicApp4.png
[9]: ./media/app-service-logic-connector-oracle/LogicApp5.png
[10]: ./media/app-service-logic-connector-oracle/LogicApp6.png
[11]: ./media/app-service-logic-connector-oracle/LogicApp7.png
[12]: ./media/app-service-logic-connector-oracle/LogicApp8.png

<!---HONumber=AcomDC_0128_2016-->