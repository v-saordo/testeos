<properties
	pageTitle="Agregar la API de Google Drive a PowerApps Enterprise | Microsoft Azure"
	description="Crear o configurar una nueva API de Google Drive en el entorno del Servicio de aplicaciones de la organización"
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

# Crear una nueva API de Google Drive en el entorno del Servicio de aplicaciones de la organización

## Crear la API en el portal de Azure

1. En el [portal de Azure](https://portal.azure.com/), inicie sesión con su cuenta de trabajo. Por ejemplo, inicie sesión con *suNombreDeUsuario*@* SuEmpresa*.com. Al hacerlo, automáticamente inicia sesión en la suscripción de su empresa.
 
2. Seleccione **Examinar** en la barra de tareas: ![][15]

3. En la lista, puede desplazarse para encontrar PowerApps o escribir en *powerapps*: ![][16]

4. En **PowerApps**, seleccione **Administrar API**: ![Examine las APIs registradas][1]

5. En **Administrar API**, seleccione **Agregar** para agregar la nueva API: ![Add API][2]

6. Escriba un **nombre** descriptivo para la API.
	
7. En **Origen**, seleccione **API disponibles** para seleccionar las API preconfiguradas y seleccione **Google Drive**: ![seleccionar la api de google drive][3]

8. Seleccione **Configuración: Configure los ajustes necesarios**: ![establecer la configuración de la API de google drive][4]

9. Escriba la *clave de la aplicación* y el *secreto de la aplicación* de la aplicación Google Drive. Si no dispone de estos, consulte la sección "Registrar una aplicación Google Drive para su uso con PowerApps" de este tema para crear los valores de clave y secreto que necesita.

	> [AZURE.IMPORTANT]Guarde la **URL de redireccionamiento**. Es posible que necesite este valor más adelante en este tema.

10. Seleccione **Aceptar** para completar los pasos.

Cuando termine, se agregará una nueva API de Google Drive al entorno del Servicio de aplicaciones.


## Opcional: registrar una aplicación Google Drive para su uso con PowerApps

Si no tiene una aplicación Google Drive existente con los valores de clave y secreto, use los pasos siguientes para crear la aplicación y obtenga los valores que necesita.

1. Inicie sesión en la [Consola de desarrolladores de Google][5]\: ![Consola de desarrolladores de Google][6]

2. Seleccione **Crear un proyecto vacío**.

3. Escriba un nombre para la aplicación, acepte los términos y condiciones y seleccione **Crear**: ![crear nuevo proyecto de google drive][7]

4. Tras haber creado el nuevo proyecto correctamente, seleccione **Usar API de Google**: ![Usar api de google][8]

5. En la página de información general, seleccione **API de Drive**: ![Información general de la API de Google Drive][9]

6. Seleccione **Habilitar API**: ![Habilitar la API de Google Drive][10]

7. Al habilitar la API de Drive, seleccione **Credenciales** y, a continuación, **Id. de cliente de OAuth 2.0**: ![Agregar credenciales][12]

8. Seleccione **Configurar pantalla de consentimiento**.

9. En el **pantalla de consentimiento de OAuth**, introduzca un **nombre de producto** y seleccione **Guardar**: ![Configurar pantalla de consentimiento][13]

10. En la página de creación del Id. de cliente:

	a) En **Tipo de aplicación**, seleccione **Aplicación web**. b) Escriba un nombre para el cliente. c) Establezca la dirección URL de redireccionamiento en la dirección URL de redireccionamiento que recibió cuando se agregó la nueva API de Google Drive al Portal de Azure (en este tema). d) Seleccione **Crear**.

	![Crear Id. de cliente][14]

11. Se mostrará el identificador y el secreto de cliente de la aplicación registrada.

Se creará una nueva aplicación Google Drive. Puede usar esta aplicación en la configuración de la API de Google Drive en el Portal de Azure.

## Resumen y pasos siguientes
En este tema, ha agregado la API de Google Drive a su empresa PowersApps. A continuación, proporcione a los usuarios acceso a la API para que se pueda agregar a sus aplicaciones:

[Agregar una conexión y conceder acceso a los usuarios](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-googledrive/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-googledrive/add-api.PNG
[3]: ./media/powerapps-create-api-googledrive/select-googledrive-api.PNG
[4]: ./media/powerapps-create-api-googledrive/configure-googledrive-api.PNG
[5]: https://console.developers.google.com/
[6]: ./media/powerapps-create-api-googledrive/google-developers-console.PNG
[7]: ./media/powerapps-create-api-googledrive/googledrive-create-project.PNG
[8]: ./media/powerapps-create-api-googledrive/use-google-apis.PNG
[9]: ./media/powerapps-create-api-googledrive/googledrive-api-overview.PNG
[10]: ./media/powerapps-create-api-googledrive/enable-googledrive-api.PNG
[11]: ./media/powerapps-create-api-googledrive/googledrive-api-credentials.PNG
[12]: ./media/powerapps-create-api-googledrive/googledrive-api-credentials-add.PNG
[13]: ./media/powerapps-create-api-googledrive/configure-consent-screen.PNG
[14]: ./media/powerapps-create-api-googledrive/create-client-id.PNG
[15]: ./media/powerapps-create-api-googledrive/browseall.png
[16]: ./media/powerapps-create-api-googledrive/allresources.png

<!---HONumber=AcomDC_1203_2015-->