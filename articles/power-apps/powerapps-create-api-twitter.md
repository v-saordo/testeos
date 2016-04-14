<properties
	pageTitle="Agregar la API de Twitter a PowerApps Enterprise | Microsoft Azure"
	description="Crear o configurar una nueva API de Twitter en el entorno de servicio de aplicaciones de la organización"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>

# Crear una nueva API de Twitter en el entorno de servicio de aplicaciones de la organización

## Crear la API en el portal de Azure

1. En el [portal de Azure](https://portal.azure.com/), inicie sesión con su cuenta de trabajo. Por ejemplo, inicie sesión con *suNombreDeUsuario*@* SuEmpresa*.com. Al hacerlo, automáticamente inicia sesión en la suscripción de su empresa. 

2. Seleccione **Examinar** en la barra de tareas: ![][14]

3. En la lista, puede desplazarse para encontrar PowerApps o escribir en *powerapps*: ![][15]

4. En **PowerApps**, seleccione **Administrar APIs**: ![Examine las APIs registradas][1]

5. En **Administrar API**, seleccione **Agregar** para agregar la nueva API: ![Add API][2]

6. Escriba un **nombre** descriptivo para la API.
	
7. En **Origen**, seleccione **API disponibles** para seleccionar las API preconfiguradas y seleccione **Twitter**: ![seleccionar api de Twitter][3]

8. Seleccione **Configuración: Configure los ajustes necesarios**: ![establecer la configuración de la API de Twitter][4]

9. Escriba la *clave del consumidor* y el *secreto del consumidor* de la aplicación de Twitter. Si no dispone de estos, consulte la sección "Registrar una aplicación Twitter para su uso con PowerApps" de este tema para crear los valores de clave y secreto que necesita.

	> [AZURE.IMPORTANT]Guarde la **URL de redireccionamiento**. Es posible que necesite este valor más adelante en este tema.

10. Seleccione **Aceptar** para completar los pasos.

Cuando termine, se agregará una nueva API de Twitter al entorno del Servicio de aplicaciones.


## Opcional: registrar una aplicación Twitter para su uso con PowerApps

Si no tiene una aplicación Twitter existente con los valores de clave y secreto, use los pasos siguientes para crear la aplicación y obtenga los valores que necesita.

1. Vaya a [https://apps.twitter.com/](https://apps.twitter.com) e inicie sesión con su cuenta de Twitter.

2. Seleccione **Crear nueva aplicación**: ![Página de aplicaciones de Twitter][6]

3. En **Crear una aplicación**:
   
	a) Escriba un valor para **Nombre**. b) Escriba un valor para **Descripción**. c) Escriba un valor para **Sitio web**. d) Defina la **Dirección url de devolución de llamada** en la dirección URL de redireccionamiento que recibió al agregar la nueva API de Twitter en el Portal de Azure (en este tema). e) Acepte el contrato del desarrollador y seleccione **Crear su aplicación de Twitter**.

	![Crear aplicación de Twitter][7]

4. Cuando se cree correctamente la aplicación, se le redirigirá a la página de la aplicación.

Se creará una nueva aplicación de Twitter. Puede usar esta aplicación en la configuración de la API de Twitter en el Portal de Azure.

## Resumen y pasos siguientes
En este tema, ha agregado la API de Twitter a su empresa PowersApps. A continuación, proporcione a los usuarios acceso a la API para que se pueda agregar a sus aplicaciones:

[Agregar una conexión y conceder acceso a los usuarios](powerapps-manage-api-connection-user-access.md)


<!--References-->

[1]: ./media/powerapps-create-api-twitter/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-twitter/add-api.PNG
[3]: ./media/powerapps-create-api-twitter/select-twitter-api.PNG
[4]: ./media/powerapps-create-api-twitter/configure-twitter-api.PNG
[6]: ./media/powerapps-create-api-twitter/twitter-apps-page.PNG
[7]: ./media/powerapps-create-api-twitter/twitter-app-create.PNG
[14]: ./media/powerapps-create-api-sqlserver/browseall.png
[15]: ./media/powerapps-create-api-sqlserver/allresources.png

<!---HONumber=AcomDC_1203_2015-->