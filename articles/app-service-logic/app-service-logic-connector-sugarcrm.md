<properties
   pageTitle="Uso del conector de SugarCRM en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
   description="Creación y configuración del conector de SugarCRM o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
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
   ms.date="02/10/2016"
   ms.author="sameerch"/>


# Introducción al conector de SugarCRM y su incorporación a las aplicaciones lógicas
>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas.

El conector de SugarCRM le permite crear y modificar diferentes entidades como cuentas, clientes potenciales, contactos, etc. A continuación se muestran los escenarios de integración típicos relacionados con SugarCRM:

- Sincronización de cuentas entre sistemas ERP y SugarCRM como SAP
- Sincronización de cuentas, contactos y clientes potenciales entre Marketo y SugarCRM
- Solicitud de flujo de caja de SugarCRM a sistemas ERP

Como parte de la configuración del paquete del conector, puede especificar entidades que el conector puede administrar y las acciones y los parámetros de entrada y salida se completarán de forma dinámica.

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo. Puede agregar el conector de SugarCRM a sus datos de flujo de trabajo empresarial y datos de proceso como parte de este flujo de trabajo en una aplicación lógica.



## Acciones de conector de SugarCRM
A continuación se muestran las distintas acciones disponibles en el conector de SugarCRM:

- Crear módulo: use esta acción para crear un nuevo registro para el módulo SugarCRM, como cuentas, clientes potenciales y contactos.

- Actualizar módulo: use esta acción para actualizar un registro existente para el módulo SugarCRM.

- Eliminar módulo: use esta acción para eliminar un registro existente para el módulo SugarCRM.

- Lista de módulo: use esta acción para mostrar una lista de los registros filtrados. Si no se especifica ninguna consulta se devuelven todos los registros.

- Obtener módulo: use esta acción para recuperar un único registro del módulo especificado.

- Obtener recuento de registros: use esta acción para obtener el número de registros en el módulo que coinciden con la consulta. Si no se especifica ninguna consulta, se devuelve el número total de registros del módulo.

- Comprobar módulo duplicado: use esta acción para buscar registros duplicados dentro de un módulo.

*Nota*: para obtener más detalles sobre los argumentos admitidos en las consultas, consulte la documentación de [API de REST de SugarCRM](https://msdn.microsoft.com/library/dn705870) .

## Creación de una aplicación de API del conector de SugarCRM
1.	Vaya a portal.azure.com. Abra Azure Marketplace mediante la opción +NUEVO en la esquina superior izquierda del Portal de Azure.
2.	Diríjase a "Marketplace > Todo" y busque "SugarCRM".
3.	Para configurar el conector de SugarCRM, proporcione los detalles del plan del Servicio de aplicaciones, el grupo de recursos y seleccione el nombre de la aplicación de API.
4. Configure los ajustes del paquete del conector de SugarCRM. A continuación se proporciona la configuración del paquete que es necesario proporcionar para crear el conector:

	Nombre | Obligatorio | Descripción
--- | --- | ---
Dirección URL del sitio | Sí | Escriba la dirección URL de la instancia de SugarCRM. Por ejemplo, escriba: https://abcde1234.sugarcrm.com.
Id. de cliente | Sí | Escriba la clave de consumidor de clave de OAUTH 2.0 en SugarCRM. 
Secreto del cliente | Sí | Escriba el secreto de consumidor de OAUTH.
Nombre de usuario | Sí | Escriba el nombre de usuario de SugarCRM.
Password | Sí | Escriba la contraseña del usuario de SugarCRM.
Nombres de módulo | Sí | Especifique los módulos de SugarCRM (como cuentas, contactos o productos) en los que desea realizar la operación<br><br>Ejemplo: cuentas, clientes potenciales, contactos.  
  
![][9]



## Crear una aplicación lógica
Vamos a crear una aplicación lógica simple que cree una cuenta en SugarCRM y actualice la información de la dirección de facturación de la misma cuenta.

1.	Inicie sesión en el Portal de Azure y haga clic en "Nuevo -> Web+móvil -> Aplicación lógica": ![][1]

2.	En la página "Crear aplicación lógica", especifique la información necesaria, como el nombre, el plan de servicio de la aplicación y la ubicación: ![][2]

3.	Haga clic en "Desencadenadores y acciones" y aparecerá la pantalla del editor de aplicación lógica. Seleccione "Ejecutar esta lógica manualmente", lo que significa que esta aplicación lógica solo se puede invocar manualmente.

4.	Expanda ’Aplicaciones de API’ en este grupo de recursos en la Galería para ver todas las Aplicaciones de API disponibles. Seleccione "SugarCRM" en la galería y el "Conector de SugarCRM" se agregará al flujo: ![][3]

5.	Seleccione la acción "Crear cuenta" y se mostrarán los parámetros de entrada: ![][4]

6.	Proporcione un nombre como ’Cuenta Microsoft’ y haga clic en ✓: ![][5]

7.	Seleccione ’Conector de SugarCRM’ en la sección ’Usados recientemente’ en la galería y se agregará una nueva acción de SugarCRM.

8.	Seleccione "Actualizar cuenta" (estará en Acciones avanzadas "...") de la lista de acciones y se mostrarán los parámetros de entrada de la acción "Actualizar cuenta": ![][6]

9.	Haga clic en "..." junto a "Id. de registro" para elegir el valor del identificador de la salida de la acción "Crear cuenta": ![][7]

10.	Proporcione los valores de la información de dirección de facturación y haga clic en ✓: ![][8]

11. Haga clic en Aceptar en la pantalla del editor de Aplicación lógica y, a continuación, haga clic en ’Crear’. Se tardará aproximadamente 30 segundos en completar la creación.

12. Busque la aplicación lógica creada recientemente y haga clic en "Ejecutar ahora" para iniciar una ejecución.

13. Puede comprobar que se crea una nueva cuenta llamada ’Cuenta Microsoft’ en su cuenta de SugarCRM y la misma cuenta también se actualiza con la información de dirección de facturación.

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-sugarcrm/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-sugarcrm/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-sugarcrm/3_Select_SugarCRM_Gallery.png
[4]: ./media/app-service-logic-connector-sugarcrm/4_SugarCRM_Create_Account.png
[5]: ./media/app-service-logic-connector-sugarcrm/5_Create_Account_OK.png
[6]: ./media/app-service-logic-connector-sugarcrm/6_SugarCRM_Update_Account.png
[7]: ./media/app-service-logic-connector-sugarcrm/7_Record_ID_from_Create.png
[8]: ./media/app-service-logic-connector-sugarcrm/8_Update_Account_Address.png
[9]: ./media/app-service-logic-connector-sugarcrm/9_Create_new_SugarCRM_connector.png

<!---HONumber=AcomDC_0224_2016-->