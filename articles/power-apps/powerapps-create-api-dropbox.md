<properties
	pageTitle="Agregar la API de Dropbox a PowerApps Enterprise | Microsoft Azure"
	description="Crear o configurar una nueva API de Dropbox en el entorno de servicio de aplicaciones de la organización"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="dwerde"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>

# Crear una nueva API de Dropbox en el entorno de servicio de aplicaciones de la organización

## Crear la API en el portal de Azure

1. En el [portal de Azure](https://portal.azure.com/), inicie sesión con su cuenta de trabajo. Por ejemplo, inicie sesión con *suNombreDeUsuario*@* SuEmpresa*.com. Al hacerlo, automáticamente inicia sesión en la suscripción de su empresa.
 
2. Seleccione **Examinar** en la barra de tareas:  
![][12]

3. En la lista, puede desplazarse para encontrar PowerApps o escribir en *powerapps*:  
![][13]

4. En **PowerApps**, seleccione **Administrar API**:  
![Examine las APIs registradas][4]

5. En **Administrar API**, seleccione **Agregar** para agregar la nueva API:  
![Add API][5]

6. Escriba un **nombre** descriptivo para la API.
	
7. En **Origen**, seleccione **API disponibles** para ver las API preconfiguradas y seleccione **Dropbox**:  
![seleccionar la api de dropbox][6]

8. Seleccione **Configuración: Configure los ajustes necesarios**:  
![establecer la configuración de la API de dropbox][7]

9. Escriba los valores de **clave de aplicación** y **secreto de aplicación** de la aplicación Dropbox. Si todavía no dispone de estos, consulte la sección "Registrar una aplicación Dropbox para su uso con PowerApps" de este tema para crear los valores de clave y secreto que necesita.

	> [AZURE.IMPORTANT]Guarde la **URL de redireccionamiento**. Es posible que necesite este valor más adelante en este tema.

10. Seleccione **Aceptar** para completar los pasos.


Cuando termine, se agregará una nueva API de Dropbox al entorno del Servicio de aplicaciones.


## Opcional: registrar una aplicación Dropbox para su uso con PowerApps

Si no tiene una aplicación Dropbox existente con los valores de clave y secreto, use los pasos siguientes para crear la aplicación y obtenga los valores que necesita.

1. Vaya a [Dropbox][1] e inicie sesión con su cuenta.

2. Vaya al sitio para desarrolladores de Dropbox y seleccione **Mis aplicaciones**:  
![Sitio para desarrolladores de Dropbox][8]

3. Seleccione **Crear aplicación**:  
![Aplicación de creación de Dropbox][9]

4. En **Crear una nueva aplicación en la plataforma de Dropbox**:

	a) En **Elegir API**, seleccione **API de Dropbox**. b) En **Elegir el tipo de acceso que necesita**, seleccione **Dropbox completo...**. c) Escriba un nombre para la aplicación.

	![Aplicación de creación de Dropbox, página 1][10]

5. En la página de configuración de aplicaciones:

	a) En **OAuth 2**, establezca la **Dirección URL de redireccionamiento** en la dirección URL de redireccionamiento que recibió al agregar la nueva API de Dropbox en el Portal de Azure (en este tema). Seleccione **Agregar**. b) Seleccione el vínculo **Mostrar** para revelar el **secreto de la aplicación**:

	![Aplicación de creación de Dropbox, página 2][11]

Se creará una nueva aplicación de Dropbox. Puede usar esta aplicación en la configuración de la API de Dropbox en el Portal de Azure.


## Resumen y pasos siguientes
En este tema, ha agregado la API de Dropbox para su empresa PowersApps. A continuación, proporcione a los usuarios acceso a la API para que se pueda agregar a sus aplicaciones:

[Agregar una conexión y conceder acceso a los usuarios](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: https://www.dropbox.com/login
[2]: https://www.dropbox.com/developers/apps/create
[3]: https://www.dropbox.com/developers/apps
[4]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[5]: ./media/powerapps-create-api-dropbox/add-api.PNG
[6]: ./media/powerapps-create-api-dropbox/select-dropbox-api.PNG
[7]: ./media/powerapps-create-api-dropbox/configure-dropbox-api.PNG
[8]: ./media/powerapps-create-api-dropbox/dropbox-developer-site.PNG
[9]: ./media/powerapps-create-api-dropbox/dropbox-create-app.PNG
[10]: ./media/powerapps-create-api-dropbox/dropbox-create-app-page1.PNG
[11]: ./media/powerapps-create-api-dropbox/dropbox-create-app-page2.PNG


[12]: ./media/powerapps-create-api-dropbox/browseall.png
[13]: ./media/powerapps-create-api-dropbox/allresources.png

<!----HONumber=AcomDC_1203_2015-->