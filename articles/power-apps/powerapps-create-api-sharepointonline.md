<properties
	pageTitle="Agregar la API de SharePoint Online a PowerApps Enterprise | Microsoft Azure"
	description="Crear o configurar una nueva API de SharePoint Online en el entorno del Servicio de aplicaciones de la organización"
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

# Crear una nueva API de SharePoint Online en el entorno del Servicio de aplicaciones de la organización

## Crear la API en el portal de Azure

1. En el [portal de Azure](https://portal.azure.com/), inicie sesión con su cuenta de trabajo. Por ejemplo, inicie sesión con *suNombreDeUsuario*@* SuEmpresa*.com. Al hacerlo, automáticamente inicia sesión en la suscripción de su empresa.
 
2. Seleccione **Examinar** en la barra de tareas: ![][14]

3. En la lista, puede desplazarse para encontrar PowerApps o escribir en *powerapps*: ![][15]

4. En **PowerApps**, seleccione **Administrar API**: ![Examine las APIs registradas][1]

5. En **Administrar API**, seleccione **Agregar** para agregar la nueva API: ![Add API][2]

6. Escriba un **nombre** descriptivo para la API.
	
7. En **Origen**, seleccione **API disponibles** para seleccionar las API preconfiguradas y seleccione **SharePoint Online**: ![seleccionar la api de SharePoint Online][3]

8. Seleccione **Configuración: Configure los ajustes necesarios**: ![establecer la configuración de la API de SharePoint Online][4]

9. Escriba el *Id. de cliente* y el *Secreto de la aplicación* de la aplicación Azure Active Directory (AAD) de SharePoint Online. Si no dispone de estos, consulte la sección "Registrar una aplicación de AAD para su uso con PowerApps" en este tema para crear el identificador y los valores secretos que necesita.

	> [AZURE.IMPORTANT]Guarde la **URL de redireccionamiento**. Es posible que necesite este valor más adelante en este tema.

10. Seleccione **Aceptar** para completar los pasos.

Cuando termine, se agregará una nueva API de SharePoint Online en el entorno de Servicio de aplicaciones.


## Registrar una aplicación de AAD para su uso con la API de SharePoint Online de PowerApps

1. Abra el [Portal de Azure][5].

2. Seleccione **Examinar** y, a continuación, seleccione **Active Directory**:

	> [AZURE.NOTE]De este modo se abre Active Directory en el Portal de Azure clásico.

3. Seleccione el nombre del inquilino de su organización: ![Iniciar Azure Active Directory][6]

4. Seleccione la pestaña **Aplicaciones** y seleccione **Agregar**: ![Aplicaciones del inquilino de AAD][7].

5. En **Agregar aplicación**:

	a) Escriba un **nombre** para su aplicación. b) Establezca el tipo de aplicación como **Web**. c) Seleccione **Siguiente**.


	![Agregar aplicación de AAD: información de la aplicación][8]

6. En **Propiedades de la aplicación**:

	a) Introduzca la **DIRECCIÓN URL DE INICIO DE SESIÓN** de la aplicación. Puesto que va a autenticarse con AAD para PowerApps, establezca la dirección URL de inicio de sesión en \__https://login.windows.net_. b) Escriba un **Identificador URI DE ID DE APLICACIÓN** para la aplicación. c) Seleccione **Aceptar**.

	![Agregar aplicación de AAD: propiedades de la aplicación][9]

7. Cuando se finalice correctamente, se le redirigirá a la nueva aplicación de AAD. Seleccione **Configurar**: ![Aplicación AAD de Contoso][10]

8. Establezca la **Dirección URL de respuesta** de la sección _OAuth 2_ en la dirección URL de redireccionamiento que recibió cuando se agregó la nueva API de SharePoint Online en el Portal de Azure (en este tema). Seleccione **Agregar una aplicación**: ![Configurar aplicación AAD de Contoso][11]

9. En la ventana **Permisos para otras aplicaciones**, seleccione **Office 365 Exchange Online** y seleccione **Aceptar**: ![Delegado de la aplicación de Contoso][12]

10. De nuevo en la página Configurar, tenga en cuenta que _Office 365 Exchange Online_ se agrega a la lista _Permiso para otras aplicaciones_.

11. Seleccione **Permisos delegados** para _Office 365 Exchange Online_ y seleccione los siguientes permisos:

	- Leer y escribir elementos en todas las colecciones de sitios
	- Leer y escribir elementos en todas las listas de todas las colecciones de sitios

	![Permisos de delegado de la aplicación de Contoso][13]

Se creará una nueva aplicación de Azure Active Directory. Puede usar esta aplicación en la configuración de la API de SharePoint Online en el portal de Azure.

## Resumen y pasos siguientes
En este tema, ha agregado la API de SharePoint Online para su empresa PowersApps. A continuación, proporcione a los usuarios acceso a la API para que se pueda agregar a sus aplicaciones:

[Agregar una conexión y conceder acceso a los usuarios](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-sharepointonline/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-sharepointonline/add-api.PNG
[3]: ./media/powerapps-create-api-sharepointonline/select-sharepointonline-api.PNG
[4]: ./media/powerapps-create-api-sharepointonline/configure-sharepointonline-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-sharepointonline/launch-aad.PNG
[7]: ./media/powerapps-create-api-sharepointonline/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-sharepointonline/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-sharepointonline/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app-configure.PNG
[12]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app-delegate-sharepointonline.PNG
[13]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app-delegate-sharepointonline-permissions.PNG
[14]: ./media/powerapps-create-api-sharepointonline/browseall.png
[15]: ./media/powerapps-create-api-sharepointonline/allresources.png

<!---HONumber=AcomDC_1203_2015-->