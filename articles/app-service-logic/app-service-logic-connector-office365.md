<properties
   pageTitle="Uso del conector de Office 365 en aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
   description="Creación y configuración del conector de Office 365 o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
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


# Introducción al conector de Office 365 y su incorporación a las aplicaciones lógicas
Conéctese a su cuenta de Office 365 para enviar y recibir correos electrónicos y administrar su calendario y contactos. Puede realizar diversas acciones como enviar, recibir y obtener mensajes de correo electrónico, crear y eliminar eventos del calendario y crear, actualizar, obtener y eliminar los contactos.

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo. Puede agregar el conector de Office 365 a sus datos de flujo de trabajo empresarial y datos de proceso como parte de este flujo de trabajo en una aplicación lógica.

**Acciones básicas**

- Correo nuevo (desencadenador)
- Enviar correo
- Responder al correo
- Enviar evento
- Agregar contacto

## Creación de la aplicación de API del conector de O365
Un conector puede crearse dentro de una aplicación lógica o directamente desde Azure Marketplace. Para crear un conector desde Marketplace:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. Busque "Conector de Office 365", selecciónelo y seleccione **Crear**.
3.	Para configurar el conector de Office 365, proporcione los detalles del plan de hospedaje, el grupo de recursos y seleccione el nombre de la aplicación de API: ![][21]


## Crear una aplicación lógica
Vamos a crear una aplicación lógica simple que se desencadene cuando se reciba un correo electrónico (en el identificador de correo electrónico de consultas sobre ventas, por ejemplo, sales@contoso.com). Crea un evento, agrega un contacto con los detalles del remitente, envía un correo electrónico a su cuenta personal y finalmente envía una respuesta con una confirmación.

1.	Inicie sesión en el Portal de Azure y haga clic en "Nuevo -> Web + móvil -> Aplicación lógica": ![][1]

2.	En la página "Crear aplicación lógica", especifique la información necesaria, como el nombre, el plan de servicio de la aplicación y la ubicación: ![][2]

3.	Haga clic en "Desencadenadores y acciones" para que se abra el editor de aplicación lógica: ![][3]

4.	Seleccione el desencadenador de Office 365 en la sección 'Aplicaciones de API de este grupo de recursos' de la galería para agregarlo al flujo: ![][4]

6.	Conectarse a Office 365 requiere autorizar la aplicación lógica para poder acceder a su cuenta. Haga clic en "Autorizar" para proporcionar las credenciales de inicio de sesión de Office 365: ![][5]

7.	Se le redirigirá a la página de inicio de sesión de Office 365 y podrá autenticarse con las credenciales de su cuenta de Office 365: ![][6] ![][7]

8.	Una vez completada la autorización, se muestran los desencadenadores de Office 365: ![][8]

9.	Seleccione el desencadenador "Nuevo correo electrónico" y se mostrarán todos los parámetros de entrada.


10.	Cambie la frecuencia del desencadenador a 'Minutos' y haga clic en ✓: ![][9]

11. El desencadenador 'Nuevo correo electrónico' de Office 365 está configurado, y puede ver también los parámetros de salida: ![][10]

12.	Seleccione "Conector de Office 365" en la sección "Usados recientemente" en la galería y se agregará una nueva acción de "Office 365".

13.	Seleccione "Enviar evento" en la lista de acciones y se mostrarán los parámetros de entrada de la acción "Enviar evento": ![][11]

14.	Especifique los detalles del evento y haga clic en ✓: ![][12]

15.	Seleccione "Conector de Office 365" en la sección "Usados recientemente" en la galería y se agregará una nueva acción de "Office 365".

16.	Seleccione "Agregar contacto" en la lista de acciones y se mostrarán los parámetros de entrada de la acción "Agregar contacto": ![][13]

17.	Haga clic en '+' junto al campo 'Dirección de correo electrónico' y seleccione el valor de campo de salida 'Desde' del desencadenador: ![][14]

18. Haga clic en ✓ y la configuración de la acción se habrá completado: ![][15]

19.	Seleccione "Conector de Office 365" en la sección "Usados recientemente" en la galería y se agregará una nueva acción de "Office 365".


20.	Seleccione "Enviar correo electrónico" en la lista de acciones y se mostrarán los parámetros de entrada de la acción "Enviar correo electrónico": ![][19]

21.	Proporcione los detalles necesarios para enviar el correo electrónico. Puede crear un mensaje escribiendo algo como aparece a continuación. Cuando la acción 'Enviar correo electrónico' esté configurada, haga clic en ✓:

		Body - @concat('You got a new sales enquiry from',triggers().output.body.From)

	![][20]
22.	Seleccione "Conector de Office 365" en la sección "Usados recientemente" en la galería y se agregará una nueva acción de "Office 365".


23.	Seleccione "Responder a" en la lista de acciones y se mostrarán los parámetros de entrada de la acción "Responder a": ![][16]

24.	Haga clic en '+' junto al campo 'Desde' y seleccione el valor del identificador de mensaje de salida del desencadenador, y, después, haga clic en ✓: ![][17]

25. Haga clic en Aceptar en la pantalla del editor de Aplicación lógica y, a continuación, haga clic en ’Crear’. Se tardará aproximadamente 30 segundos en completar la creación.

26. Envíe un correo electrónico a la cuenta que configuró con el desencadenador. Debería ver un correo electrónico en su cuenta de correo personal y eventos del calendario y contactos en la cuenta de correo de la empresa. Además, obtendrá una respuesta en la que se le indicará que se responderá a las consultas sobre ventas en breve.

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

> [AZURE.NOTE]Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-office365/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-office365/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-office365/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-office365/4_Select_Office365_Gallery.png
[5]: ./media/app-service-logic-connector-office365/5_Office365_Authorize.png
[6]: ./media/app-service-logic-connector-office365/6_Office365_Login.png
[7]: ./media/app-service-logic-connector-office365/7_Office365_User_Consent.png
[8]: ./media/app-service-logic-connector-office365/8_Office365_Trigger.png
[9]: ./media/app-service-logic-connector-office365/9_Office365_Trigger_Settings.png
[10]: ./media/app-service-logic-connector-office365/10_Office365_Trigger_Configured.png
[11]: ./media/app-service-logic-connector-office365/11_Office365_Actions_List.png
[12]: ./media/app-service-logic-connector-office365/12_Office365_Create_Event_Inputs.png
[13]: ./media/app-service-logic-connector-office365/13_Office365_Add_Contact_Inputs.png
[14]: ./media/app-service-logic-connector-office365/14_Office365_Add_Contact_Email_FromTrigger.png
[15]: ./media/app-service-logic-connector-office365/15_Office365_Add_Contacts_Configured.png
[16]: ./media/app-service-logic-connector-office365/16_Office365_Reply_To_Inputs.png
[17]: ./media/app-service-logic-connector-office365/17_Office365_Reply_To_MessageId.png
[18]: ./media/app-service-logic-connector-office365/18_Office365_Reply_To_Configured.png
[19]: ./media/app-service-logic-connector-office365/19_Office365_Send_Inputs.png
[20]: ./media/app-service-logic-connector-office365/20_Office365_Send_Configured.png
[21]: ./media/app-service-logic-connector-office365/21-create-new-o365-api-app.png

<!---HONumber=AcomDC_1203_2015-->