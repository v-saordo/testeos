<properties
   pageTitle="Uso del conector de QuickBooks en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
   description="Creación y configuración del conector de QuickBooks o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
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


# Introducción al conector de QuickBooks y su incorporación a su aplicación lógica
Use el conector de QuickBooks para crear y modificar diferentes entidades de QuickBooks. En la tabla siguiente se enumeran las entidades compatibles:

Entidades|Descripción
---|---
Cuenta|Una cuenta es un componente de un plan de cuentas y forma parte de un libro mayor. Permite registrar un importe monetario total asignado para un uso específico
Nota de crédito|Una nota de crédito es una transacción financiera que representa un reembolso o el abono de un pago o parte de un pago por bienes o servicios que se han vendido.
Cliente|Un cliente es un consumidor del servicio o producto que ofrece su negocio.
Presupuesto|El presupuesto representa una propuesta para una transacción financiera de una empresa a un cliente por bienes o servicios puestos a la venta, incluidos los precios propuestos.
Factura|Una factura representa un formulario de venta donde el cliente paga un producto o servicio más adelante. Aquí se encuentra información adicional acerca de cómo utilizar el modelo de datos de la factura.
Elemento|Un elemento es algo que su compañía adquiere, vende o revende; por ejemplo, productos, gastos de envío y manipulación, descuentos e impuestos (si corresponde). Un elemento se muestra como una línea en una factura u otro formulario de venta.
Recibo de venta|Esta entidad representa el recibo de venta entregado a un cliente.

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo. Puede agregar el conector de QuickBooks a sus datos de flujo de trabajo empresarial y datos de proceso como parte de este flujo de trabajo en una aplicación lógica.

##Acciones de QuickBooks ##
A continuación se muestran las distintas acciones disponibles en el conector de QuickBooks.

Acción|Descripción
---|---
Leer entidad|Lee el objeto entidad.
Crear o actualizar entidad|Crea o actualiza el objeto entidad. El objeto se actualiza si ya existe; de lo contrario, se crea un nuevo objeto.
Eliminar|Esta acción elimina el objeto especificado de la entidad seleccionada.
Consultar|La operación de consulta es el método para crear una consulta guiada en una entidad.

##Creación de una aplicación de API del conector de QuickBooks##
1.	Abra Azure Marketplace mediante la opción +NUEVO en la parte inferior derecha del Portal de Azure.
2.	Vaya a “Web y móvil > Aplicaciones de API” y busque “QuickBooks”.
3.	Para configurar el conector de QuickBooks, proporcione los detalles del plan de hospedaje, el grupo de recursos y seleccione el nombre de la aplicación de API.

	![][13]
4. Configure las entidades de QuickBooks que desea leer o escribir en ’Configuración del paquete’.

Con esto, ahora puede crear una aplicación de API del conector de QuickBooks.


##Crear una aplicación lógica##
Nos permite crear una aplicación lógica sencilla que crea una cuenta en QuickBooks y actualiza el ’Tipo de categoría’ de la misma cuenta.

1.	Inicie sesión en el Portal de Azure y haga clic en "Nuevo -> Web+móvil -> Aplicación lógica"

	![][1]

2.	En la página "Crear aplicación lógica", especifique la información necesaria, como el nombre, el plan de servicio de la aplicación y la ubicación.

	![][2]

3.	Haga clic en "Desencadenadores y acciones" y aparecerá la pantalla del editor de aplicación lógica.

	![][3]

4.	Seleccione "Ejecutar esta lógica manualmente", lo que significa que esta aplicación lógica solo se puede invocar manualmente.


5.	Expanda ’Aplicaciones de API’ en este grupo de recursos en la Galería para ver todas las Aplicaciones de API disponibles. Seleccione ’Conector de QuickBooks’ en la galería para agregar el ’Conector de QuickBooks’ al flujo.


6.	Se debe autenticar y autorizar las aplicaciones lógicas para realizar operaciones en su nombre si QuickBooks está en línea. Para iniciar la autorización, haga clic en Autorizar en el conector de QuickBooks.

	![][4]

7.	Al hacer clic en Autorizar, se abre el cuadro de diálogo de autenticación de QuickBooks. Proporcione los detalles de inicio de sesión de la cuenta de QuickBooks en la que desea realizar las operaciones.

	![][5]

8. Conceda acceso a su cuenta a las aplicaciones lógicas para llevar a cabo la operación en su nombre; para ello, haga clic en Autorizar en el cuadro de diálogo de consentimiento.

	![][6]

9.	Una vez completada la autorización, se muestran todas las acciones.

	![][7]

10.	Seleccione la acción ’Crear o actualizar cuenta’; se mostrarán los parámetros de entrada.

	![][8]

11.	Proporcione el ’Nombre’ y ’Tipo de cuenta’ y haga clic en ✓.

	![][9]

12.	Seleccione ’Conector de QuickBooks’ en la sección ’Usados recientemente’ en la galería y se agregará una nueva acción de QuickBooks.

13.	Seleccione ’Crear o actualizar cuenta’ en la lista de acciones; se mostrarán los parámetros de entrada de la acción.

	![][10]

14.	Haga clic en ’+’ junto a ’Id.’ para elegir el valor del identificador de la salida de la acción de crear cuenta.

	![][11]

15.	Proporcione nuevos valores para Tipo de cuenta y haga clic en ✓.

	![][12]

16. Haga clic en Aceptar en la pantalla del editor de Aplicación lógica y, a continuación, haga clic en ’Crear’. Se tardará aproximadamente 30 segundos en completar la creación.

17. Busque la aplicación lógica creada recientemente y haga clic en ’Ejecutar’ para iniciar una ejecución.

18. Puede comprobar que se crea una cuenta nueva con el nombre ’Contoso’ en la cuenta de QuickBooks.

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE]Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-quickbooks/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-quickbooks/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-quickbooks/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-quickbooks/4_QuickBooks_Authorize.png
[5]: ./media/app-service-logic-connector-quickbooks/5_QuickBooks_Login.png
[6]: ./media/app-service-logic-connector-quickbooks/6_QuickBooks_User_Consent.png
[7]: ./media/app-service-logic-connector-quickbooks/7_QuickBooks_Actions.png
[8]: ./media/app-service-logic-connector-quickbooks/8_QuickBooks_Create_Account.png
[9]: ./media/app-service-logic-connector-quickbooks/9_Create_Account_OK.png
[10]: ./media/app-service-logic-connector-quickbooks/10_QuickBooks_Update_Account.png
[11]: ./media/app-service-logic-connector-quickbooks/11_Record_ID_from_Create.png
[12]: ./media/app-service-logic-connector-quickbooks/12_Update_Account_Address.png
[13]: ./media/app-service-logic-connector-quickbooks/13_Create_new_quickbooks_connector.png

<!---HONumber=AcomDC_1203_2015-->