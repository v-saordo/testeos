<properties
	pageTitle="Agregar la API de Salesforce a PowerApps Enterprise | Microsoft Azure"
	description="Crear o configurar una nueva API de Salesforce en el entorno del Servicio de aplicaciones de la organización"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
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

# Crear una nueva API de Salesforce en el entorno del Servicio de aplicaciones de la organización

## Crear la API en el portal de Azure

1. En el [portal de Azure](https://portal.azure.com/), inicie sesión con su cuenta de trabajo. Por ejemplo, inicie sesión con *suNombreDeUsuario*@* SuEmpresa*.com. Al hacerlo, automáticamente inicia sesión en la suscripción de su empresa.
 
2. Seleccione **Examinar** en la barra de tareas: ![][14]

3. En la lista, puede desplazarse para encontrar PowerApps o escribir en *powerapps*: ![][15]

4. En **PowerApps**, seleccione **Administrar API**: ![Examine las APIs registradas][1]

5. En **Administrar API**, seleccione **Agregar** para agregar la nueva API: ![Add API][2]

6. Escriba un **nombre** descriptivo para la API.
	
7. En **Origen**, seleccione **API disponibles** para seleccionar las API preconfiguradas y seleccione **Salesforce**: ![seleccionar la api de Salesforce][3]

8. Seleccione **Configuración: Configure los ajustes necesarios**: ![establecer la configuración de la API de dropbox][7]

9. Escriba la *clave de la aplicación* y el *secreto de la aplicación* de la aplicación Salesforce. Si no dispone de estos, consulte la sección "Registrar una aplicación Salesforce para su uso con PowerApps" de este tema para crear los valores de clave y secreto que necesita.

	> [AZURE.IMPORTANT]Guarde la **URL de redireccionamiento**. Es posible que necesite este valor más adelante en este tema.

10. Seleccione **Aceptar** para completar los pasos.

Cuando termine, se agregará una nueva API de Salesforce al entorno del Servicio de aplicaciones.


## Opcional: registrar una aplicación Salesforce para su uso con PowerApps

Si no tiene una aplicación Salesforce existente con los valores de clave y secreto, use los pasos siguientes para crear la aplicación y obtenga los valores que necesita.

1. Abra la [página principal para desarrolladores de Salesforce][5] e inicie sesión con su cuenta de Salesforce. 

2. En la página principal, seleccione su perfil y, a continuación, **Configuración**: ![Página principal de Salesforce][6]

3. Seleccione **Crear** y, a continuación, **Aplicaciones**. En la página **Aplicaciones**, seleccione **Nuevo** en **Aplicaciones conectadas**: ![Aplicación de creación de Salesforce][7]

4. En **Nueva aplicación conectada**:

	a) Escriba el valor del **Nombre de la aplicación conectada**. b) Escriba el valor del **Nombre de la API**. c) Escriba el valor del **Correo electrónico de contacto**. d) En _API (habilitar la configuración de OAuth)_, seleccione **Habilitar la configuración de OAuth** y establezca la **Dirección URL de devolución de llamada** en la dirección URL de redireccionamiento recibida al agregar la nueva API de Salesforce en el Portal de Azure (en este tema).

5. En _Ámbitos de OAuth seleccionados_, agregue los siguientes ámbitos a los **Ámbitos de OAuth seleccionados**:

	- Acceder y administrar los datos de Chatter (chatter\_api)
	- Acceder y administrar los datos (api)
	- Permitir el acceso a su identificador único (openid)
	- Realizar solicitudes en su nombre en cualquier momento (refresh\_token, offline\_access)

6. **Guarde** los cambios: ![Aplicación nueva de Salesforce][8]

Se creará una nueva aplicación Salesforce. Puede usar esta aplicación en la configuración de la API de Salesforce en el Portal de Azure.

## Resumen y pasos siguientes
En este tema, ha agregado la API de Salesforce a su empresa PowersApps. A continuación, proporcione a los usuarios acceso a la API para que se pueda agregar a sus aplicaciones:

[Agregar una conexión y conceder acceso a los usuarios](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-salesforce/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-salesforce/add-api.PNG
[3]: ./media/powerapps-create-api-salesforce/select-salesforce-api.PNG
[4]: ./media/powerapps-create-api-salesforce/configure-salesforce-api.PNG
[5]: https://developer.salesforce.com
[6]: ./media/powerapps-create-api-salesforce/salesforce-developer-homepage.PNG
[7]: ./media/powerapps-create-api-salesforce/salesforce-create-app.PNG
[8]: ./media/powerapps-create-api-salesforce/salesforce-new-app.PNG
[14]: ./media/powerapps-create-api-salesforce/browseall.png
[15]: ./media/powerapps-create-api-salesforce/allresources.png

<!---HONumber=AcomDC_1203_2015-->