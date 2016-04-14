<properties
	pageTitle="Agregar la API de Microsoft Translator a PowerApps Enterprise | Microsoft Azure"
	description="Crear o configurar una nueva API de Microsoft Translator en el entorno del Servicio de aplicaciones de la organización"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
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

# Crear una nueva API de Microsoft Translator en el entorno del Servicio de aplicaciones de la organización

## Crear la API en el portal de Azure

1. En el [portal de Azure](https://portal.azure.com/), inicie sesión con su cuenta de trabajo. Por ejemplo, inicie sesión con *suNombreDeUsuario*@* SuEmpresa*.com. Al hacerlo, automáticamente inicia sesión en la suscripción de su empresa.
 
2. Seleccione **Examinar** en la barra de tareas: ![][7]

3. En la lista, puede desplazarse para encontrar PowerApps o escribir en *powerapps*: ![][8]

4. En **PowerApps**, seleccione **Administrar API**: ![Examine las APIs registradas][1]

5. En **Administrar API**, seleccione **Agregar** para agregar la nueva API: ![Add API][2]

6. Escriba un **nombre** descriptivo para la API.
	
7. En **Origen**, seleccione **API disponibles** para seleccionar las API preconfiguradas y seleccione **Microsoft Translator**: ![seleccionar la api de Microsoft Translator][3]

8. Seleccione **Configuración: Configure los ajustes necesarios**: ![establecer la configuración de la API de Microsoft Translator][4]

9. Escriba el *Id. de cliente* y el *Secreto de cliente* de la aplicación Microsoft Translator. Si no dispone de estos, consulte la sección "Registrar una aplicación Microsoft Translator para su uso con PowerApps" en este tema para crear el identificador y los valores secretos que necesita.

9. Seleccione **Aceptar** para completar los pasos.

Cuando termine, se agregará una nueva API de Microsoft Translator al entorno del Servicio de aplicaciones.


## Opcional: registrar una aplicación Microsoft Translator para su uso con PowerApps

Si no tiene una aplicación Microsoft Translator existente con los valores de Id. y secreto, use los pasos siguientes para crear la aplicación y obtenga los valores que necesita.


1. Vaya a la [página del programador de Azure Data Market][5] e inicie sesión con su Cuenta de Microsoft. 

2. Seleccione **Registrar**.

3. En **Registrar su aplicación**:

	(a) Escriba un valor para el **Id. de cliente**. b) Escriba el **nombre** de su aplicación. c) Escriba un valor ficticio para la **dirección url de redireccionamiento**. Por ejemplo, escriba **https://contosoredirecturl*. d) Escriba una **descripción**. e) Seleccione **Crear**.

	![Registrar su aplicación][6]

Se creará una nueva aplicación Microsoft Translator. Puede usar esta aplicación en la configuración de la API de Microsoft Translator en el Portal de Azure.


## Resumen y pasos siguientes
En este tema, ha agregado la API de Microsoft Translator a su empresa PowersApps. A continuación, proporcione a los usuarios acceso a la API para que se pueda agregar a sus aplicaciones:

[Agregar una conexión y conceder acceso a los usuarios](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-microsofttranslator/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-microsofttranslator/add-api.PNG
[3]: ./media/powerapps-create-api-microsofttranslator/select-microsofttranslator-api.PNG
[4]: ./media/powerapps-create-api-microsofttranslator/configure-microsofttranslator-api.PNG
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/powerapps-create-api-microsofttranslator/register-your-application.PNG
[7]: ./media/powerapps-create-api-microsofttranslator/browseall.png
[8]: ./media/powerapps-create-api-microsofttranslator/allresources.png

<!---HONumber=AcomDC_1203_2015-->