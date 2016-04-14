<properties
   pageTitle="Uso del conector de Salesforce en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
   description="Creación y configuración del conector de Salesforce o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/11/2016"
   ms.author="sameerch"/>


# Introducción al conector de Salesforce y su incorporación a las aplicaciones lógicas
>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas. Para la versión de esquema 2015-08-01-preview, haga clic en [API de Salesforce](../connectors/create-api-salesforce.md).

Conéctese con Salesforce para crear y modificar entidades como cuentas, clientes potenciales y otros elementos. A continuación se muestran los escenarios de integración típicos que implican a Salesforce:

- Sincronización de cuentas entre sistemas ERP y de Salesforce como SAP y QuickBooks
- Solicitud de flujo de caja de sitemas de Salesforce a ERP

Como parte de la configuración del paquete del conector, el usuario puede especificar entidades que el conector puede administrar y las acciones y los parámetros de entrada y salida se completarán de forma dinámica. A continuación se muestran las distintas acciones disponibles en el conector de Salesforce:

- Crear entidad: use esta acción para crear una nueva entidad de Salesforce como Cuenta, Caso o un Objeto personalizado
- Actualizar entidad: use esta acción para actualizar una entidad de Salesforce existente.
- Entidad Upsert: use esta acción para actualizar una entidad de Salesforce existente o crear una si no existe.
- Eliminar entidad: use esta acción para eliminar una entidad de Salesforce existente.
- Ejecutar consulta: use esta acción para ejecutar una consulta SELECT escrita en el lenguaje de consulta de objetos de Salesforce (SOQL).

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo. Puede agregar el conector de Salesforce a sus datos de flujo de trabajo empresarial y datos de proceso como parte de este flujo de trabajo en una aplicación lógica.


## Creación de una aplicación de API del conector de Salesforce
1.	Abra Azure Marketplace mediante la opción +NUEVO en la parte inferior derecha del Portal de Azure.
2.	Vaya a "Web y móvil > Aplicaciones de API" y busque "Salesforce".
3.	Para configurar el conector de Salesforce, especifique los detalles del plan de hospedaje y el grupo de recursos y seleccione el nombre de la aplicación de API: ![][15]
4. Configure las entidades de Salesforce que le interesa leer o escribir en ’Configuración del paquete’.

Con esto, ahora puede crear una aplicación de API del conector de Salesforce.


## Crear una aplicación lógica
Vamos a crear una aplicación lógica simple que cree una cuenta en Salesforces y actualice la información de la dirección de facturación de la misma cuenta.

1.	Inicie sesión en el Portal de Azure y haga clic en "Nuevo -> Web+móvil -> Aplicación lógica": ![][1]

2.	En la página "Crear aplicación lógica", especifique la información necesaria, como el nombre, el plan de servicio de la aplicación y la ubicación: ![][2]

3.	Haga clic en "Desencadenadores y acciones". Se abre el editor de aplicación lógica: ![][3]

4.	Seleccione "Ejecutar esta lógica manualmente", con ello esta aplicación lógica solo se puede invocar manualmente:![][4]

5.	Expanda ’Aplicaciones de API’ en este grupo de recursos en la Galería para ver todas las Aplicaciones de API disponibles. Seleccione "Salesforce" en la galería y el "Conector de Salesforce" se agregará al flujo:![][5]

8.	Para autorizar a la aplicación lógica para que tenga acceso a su cuenta de Salesforce, haga clic en "Autorizar" para especificar las credenciales de inicio de sesión de Salesforce: ![][6]

9.	Se le redirige a la página de inicio de sesión de Salesforce y podrá autenticarse con las credenciales de cuenta de Salesforce.![][7]![][8]

10.	Una vez completada la autorización, se muestran todas las acciones:![][9]

11.	Seleccione la acción "Crear cuenta" y se mostrarán los parámetros de entrada: ![][10]

12.	Proporcione el nombre de cuenta y haga clic en ✓: ![][11]

13.	Seleccione "Conector de Salesforce" en la sección "Usados recientemente" en la galería y se agregará una nueva acción de Salesforce.

14.	Seleccione "Actualizar cuenta" de la lista de acciones y se mostrarán los parámetros de entrada de la acción "Actualizar cuenta":![][12]

15.	Haga clic en "+" junto a "Id. de registro" para elegir el valor del identificador de la salida de la acción "Crear cuenta": ![][13]

16.	Proporcione valores para la calle de facturación, la ciudad de facturación, el estado de facturación y el código postal de facturación y haga clic en ✓: ![][14]

17. Haga clic en Aceptar en la pantalla del editor de Aplicación lógica y, a continuación, haga clic en ’Crear’. Se tardará aproximadamente 30 segundos en completar la creación.

18. Busque la aplicación lógica creada recientemente y haga clic en ’Ejecutar’ para iniciar una ejecución.

19. Puede comprobar que se crea una cuenta nueva con el nombre "Contoso" en la cuenta de Salesforce.

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-salesforce/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-salesforce/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-salesforce/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-salesforce/4_Manual_Logic_App.png
[5]: ./media/app-service-logic-connector-salesforce/5_Select_Salesforce_Gallery.png
[6]: ./media/app-service-logic-connector-salesforce/6_Salesforce_Authorize.png
[7]: ./media/app-service-logic-connector-salesforce/7_Salesforce_Login.png
[8]: ./media/app-service-logic-connector-salesforce/8_Salesforce_User_Consent.png
[9]: ./media/app-service-logic-connector-salesforce/9_Salesforce_Actions.png
[10]: ./media/app-service-logic-connector-salesforce/10_Salesforce_Create_Account.png
[11]: ./media/app-service-logic-connector-salesforce/11_Create_Account_OK.png
[12]: ./media/app-service-logic-connector-salesforce/12_Salesforce_Update_Account.png
[13]: ./media/app-service-logic-connector-salesforce/13_Record_ID_from_Create.png
[14]: ./media/app-service-logic-connector-salesforce/14_Update_Account_Address.png
[15]: ./media/app-service-logic-connector-salesforce/15_Create_new_salesforce_connector.png

<!---HONumber=AcomDC_0224_2016-->