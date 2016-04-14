<properties
	pageTitle="Agregar la API de Dynamics CRM Online a PowerApps Enterprise | Microsoft Azure"
	description="Crear o configurar una nueva API de Dynamics CRM Online en el entorno de servicio de aplicaciones de la organización"
	services=""
    suite="powerapps"
	documentationCenter=""
	authors="schabungbam"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/25/2015"
   ms.author="sameerch"/>

# Crear una nueva API de Dynamics CRM Online en el entorno de servicio de aplicaciones de la organización

## Crear la API en el portal de Azure

1. En el [portal de Azure](https://portal.azure.com), inicie sesión con su cuenta de trabajo. Por ejemplo, inicie sesión con *suNombreDeUsuario*@* SuEmpresa*.com. Al hacerlo, automáticamente inicia sesión en la suscripción de su empresa.

2. Seleccione **Examinar** en la barra de tareas:  
![][1]

3. En la lista, puede desplazarse para encontrar PowerApps o escribir en *powerapps*:  
![][2]

4. En **PowerApps**, seleccione **Administrar API**:  
![Examine las APIs registradas][3]

5. En **Administrar API**, seleccione **Agregar** para agregar la nueva API:  
![Add API][4]

6. Escriba un **nombre** descriptivo para la API.

7. En **Origen**, seleccione **APIs disponibles** para seleccionar las API preconfiguradas y seleccione **Dynamics CRM Online**:  
![Seleccionar la API de Dynamics CRM Online][5]

8. Seleccione **Configuración: Configure los ajustes necesarios**:  
![Configurar la API de Dynamics CRM Online][6]

9. Escriba el **Id. de cliente** y la **Clave de aplicación** de la aplicación Azure Active Directory (AAD) de Dynamics CRM Online. Si no dispone de estos, consulte la sección "Registrar una aplicación de AAD para su uso con PowerApps" en este tema para crear el identificador y los valores secretos que necesita.

	> [AZURE.IMPORTANT]Guarde la **URL de redireccionamiento**. Es posible que necesite este valor más adelante en este tema.

10. Seleccione **Aceptar** para completar los pasos.

Cuando termine, se agregará una nueva API de Dynamics CRM Online en el entorno de Servicio de aplicaciones.

## Registrar una aplicación de AAD para su uso con la API de PowerApps Dynamics CRM Online

1. Abra el [Portal de Azure](https://portal.azure.com).

2. Seleccione **Examinar** y, a continuación, seleccione **Active Directory**:

	> [AZURE.NOTE]De este modo se abre Active Directory en el Portal de Azure clásico.

3. Seleccione el nombre del inquilino de su organización:  
![Iniciar Azure Active Directory][7]

4. Seleccione la pestaña **Aplicaciones** y seleccione **Agregar**:  
![Aplicaciones del inquilino de AAD][8].

5. En **Agregar aplicación**:

	a) Escriba un **nombre** para su aplicación. b) Establezca el tipo de aplicación como **Web**. c) Seleccione **Siguiente**.

	![Agregar aplicación de AAD: información de la aplicación][9]

6. En **Propiedades de la aplicación**:

	a) Introduzca la **DIRECCIÓN URL DE INICIO DE SESIÓN** de la aplicación. Puesto que va a autenticarse con AAD para PowerApps, establezca la dirección URL de inicio de sesión en \__https://login.windows.net_. b) Escriba un **Identificador URI DE ID DE APLICACIÓN** para la aplicación. c) Seleccione **Aceptar**.

	![Agregar aplicación de AAD: propiedades de la aplicación][10]

7. Cuando se finalice correctamente, se le redirigirá a la nueva aplicación de AAD. Seleccione **Configurar**:  
![Aplicación AAD de Contoso][11]

8. Establezca la **Dirección URL de respuesta** de la sección _OAuth 2_ en la dirección URL de redireccionamiento que recibió cuando se agregó la nueva API de Dynamics CRM Online en el Portal de Azure (en este tema):  
![Configurar aplicación AAD de Contoso][12]

9. Seleccione **Guardar**.

Se creará una nueva aplicación de Azure Active Directory. Puede usar esta aplicación en la configuración de la API de Dynamics CRM Online en el portal de Azure.

## Resumen y pasos siguientes
En este tema, ha agregado la API de Dynamics CRM Online para su empresa PowersApps. A continuación, proporcione a los usuarios acceso a la API para que se pueda agregar a sus aplicaciones:

[Agregar una conexión y conceder acceso a los usuarios](powerapps-manage-api-connection-user-access.md)

<!-- References -->

[1]: ./media/powerapps-create-api-crmonline/browseall.png
[2]: ./media/powerapps-create-api-crmonline/allresources.png
[3]: ./media/powerapps-create-api-crmonline/browse-to-registered-apis.PNG
[4]: ./media/powerapps-create-api-crmonline/add-api.PNG
[5]: ./media/powerapps-create-api-crmonline/select-crmonline-api.PNG
[6]: ./media/powerapps-create-api-crmonline/configure-crmonline-settings.PNG
[7]: ./media/powerapps-create-api-crmonline/launch-aad.PNG
[8]: ./media/powerapps-create-api-crmonline/aad-tenant-applications.PNG
[9]: ./media/powerapps-create-api-crmonline/aad-tenant-applications-add-appinfo.PNG
[10]: ./media/powerapps-create-api-crmonline/aad-tenant-applications-add-app-properties.PNG
[11]: ./media/powerapps-create-api-crmonline/contoso-aad-app.PNG
[12]: ./media/powerapps-create-api-crmonline/contoso-aad-app-configure.PNG

<!----HONumber=AcomDC_1203_2015-->