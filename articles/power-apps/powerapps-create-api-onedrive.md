<properties
	pageTitle="Agregar la API de OneDrive a PowerApps Enterprise | Microsoft Azure"
	description="Crear o configurar una nueva API de OneDrive en el entorno del Servicio de aplicaciones de la organización"
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

# Crear una nueva API de OneDrive en el entorno del Servicio de aplicaciones de la organización

## Crear la API en el portal de Azure

1. En el [portal de Azure](https://portal.azure.com/), inicie sesión con su cuenta de trabajo. Por ejemplo, inicie sesión con *suNombreDeUsuario*@* SuEmpresa*.com. Al hacerlo, automáticamente inicia sesión en la suscripción de su empresa.
 
2. Seleccione **Examinar** en la barra de tareas: ![][14]

3. En la lista, puede desplazarse para encontrar PowerApps o escribir en *powerapps*: ![][15]

4. En **PowerApps**, seleccione **Administrar API**: ![Examine las APIs registradas][1]

5. En **Administrar API**, seleccione **Agregar** para agregar la nueva API: ![Add API][2]

6. Escriba un **nombre** descriptivo para la API.
	
7. En **Origen**, seleccione **API disponibles** para seleccionar las API preconfiguradas y seleccione **OneDrive**: ![seleccionar api de OneDrive][3]

8. Seleccione **Configuración: Configure los ajustes necesarios**: ![establecer la configuración de la API de OneDrive][4]

9. Escriba la *clave de la aplicación* y el *secreto de la aplicación* de la aplicación OneDrive. Si no dispone de estos, consulte la sección "Registrar una aplicación OneDrive para su uso con PowerApps" de este tema para crear los valores de clave y secreto que necesita.

	> [AZURE.IMPORTANT]Guarde la **URL de redireccionamiento**. Es posible que necesite este valor más adelante en este tema.

10. Seleccione **Aceptar** para completar los pasos.

Cuando termine, se agregará una nueva API de OneDrive al entorno del Servicio de aplicaciones.

## Opcional: registrar una aplicación OneDrive para su uso con PowerApps

Si no tiene una aplicación OneDrive existente con los valores de clave y secreto, use los pasos siguientes para crear la aplicación y obtenga los valores que necesita.

1. Vaya a la [página de creación de la aplicación][5] en _Centro para desarrolladores de la Cuenta Microsoft_ e inicie sesión con su _Cuenta Microsoft_.

2. Escriba su **nombre de la aplicación** y seleccione **Acepto**: ![Nueva aplicación OneDrive][6]

3. En la página Configuración:

	a) Seleccione **Configuración de la API**. b) Establezca la dirección URL de redireccionamiento en la dirección URL de redireccionamiento que recibió al agregar la nueva API de OneDrive en el Portal de Azure (en este tema). c) Seleccione **Guardar**.

	![Configuración de la API de la aplicación OneDrive][7]

Se creará una nueva aplicación OneDrive. Puede usar esta aplicación en la configuración de la API de OneDrive en el Portal de Azure.

## Resumen y pasos siguientes
En este tema, ha agregado la API de OneDrive a su empresa PowersApps. A continuación, proporcione a los usuarios acceso a la API para que se pueda agregar a sus aplicaciones:

[Agregar una conexión y conceder acceso a los usuarios](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-onedrive/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-onedrive/add-api.PNG
[3]: ./media/powerapps-create-api-onedrive/select-onedrive-api.PNG
[4]: ./media/powerapps-create-api-onedrive/configure-onedrive-api.PNG
[5]: https://account.live.com/developers/applications/create
[6]: ./media/powerapps-create-api-onedrive/onedrive-new-app.PNG
[7]: ./media/powerapps-create-api-onedrive/onedrive-app-api-settings.PNG
[14]: ./media/powerapps-create-api-onedrive/browseall.png
[15]: ./media/powerapps-create-api-onedrive/allresources.png

<!---HONumber=AcomDC_1203_2015-->