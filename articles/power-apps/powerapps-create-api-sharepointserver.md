<properties
	pageTitle="Agregar la API de SharePoint Server a PowerApps Enterprise | Microsoft Azure"
	description="Crear o configurar una nueva API de SharePoint Server en el entorno del Servicio de aplicaciones de la organización"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajram"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/29/2015"
   ms.author="litran"/>

# Crear una nueva API de SharePoint Server en el entorno del Servicio de aplicaciones de la organización

## Crear la API en el portal de Azure

1. En el [portal de Azure](https://portal.azure.com/), inicie sesión con su cuenta de trabajo. Por ejemplo, inicie sesión con *suNombreDeUsuario*@* SuEmpresa*.com. Al hacerlo, automáticamente inicia sesión en la suscripción de su empresa.
 
2. Seleccione **Examinar** en la barra de tareas: ![][14]

3. En la lista, puede desplazarse para encontrar PowerApps o escribir en *powerapps*: ![][15]

4. En **PowerApps**, seleccione **Administrar API**: ![Examine las APIs registradas][5]

5. En **Administrar API**, seleccione **Agregar** para agregar la nueva API: ![Add API][6]

6. Escriba un **nombre** descriptivo para la API.
7. En **Origen**, seleccione **API disponibles** para seleccionar las API preconfiguradas y seleccione **SharePoint Server**. 
8. Seleccione **Configuración: Configure los ajustes necesarios**.
9. Escriba el *Id. de cliente* y la *Clave de la aplicación* para la aplicación de Azure Active Directory (aplicación AAD) de SharePoint Server, la *Dirección URL de SharePoint* y el *Id. de recurso* de la aplicación proxy de AAD. Siga los pasos descritos en la sección siguiente para configurar la conectividad con SharePoint Server local.  

	> [AZURE.IMPORTANT]Guarde la **URL de redireccionamiento**. Es posible que necesite este valor más adelante en este tema.
	
10. Seleccione **Aceptar** para completar los pasos.

Cuando termine, se agregará una nueva API de SharePoint Server en el entorno de Servicio de aplicaciones.


## Configurar la conectividad a un SharePoint Server local

SharePoint Server usa Active Directory para la autenticación de usuarios. Las API de los entornos con servicio de aplicaciones se autentican mediante Azure Active Directory (AAD). Es necesario intercambiar el token de AAD del usuario y convertirlo en el token de AD. A continuación, este token de AD se puede usar para conectarse al servicio local.

[El proxy de aplicación de Azure (proxy de AAD)](../active-directory-application-proxy-publish.md) se usa para este requisito. Es un servicio de Azure en GA y protege el acceso remoto y SSO en aplicaciones web locales. Los pasos para habilitar el proxy de AAD están bien documentados en MSDN. A un nivel alto, los pasos incluyen:

1. [Habilitar servicios de Proxy de aplicación](../active-directory-application-proxy-enable.md) – Esto incluye:  

	- Habilitar el proxy de la aplicación en Azure AD
	- Instalar y registrar el conector del Proxy de la Aplicación de Azure

2. [Publicar aplicaciones con Proxy de aplicación](../active-directory-application-proxy-publish.md) – Esto incluye:

	- Publicar una aplicación de Proxy de aplicación mediante el asistente. Tenga en cuenta que la dirección URL externa del sitio de sharepoint de intranet una vez creada la aplicación de Proxy.
	- Asigne usuarios y un grupo a la aplicación
	- Especifique una configuración avanzada como el SPN (nombre de entidad de servicio) que usa el conector de Proxy de aplicación para capturar el token Kerberos localmente.

Una vez creada la aplicación proxy, tendrá que crear otra aplicación AAD que delegue a la aplicación de proxy. Esto es necesario para obtener el token de acceso y de actualización necesarios para el flujo de consentimiento. Puede crear una nueva aplicación de AAD siguiendo [estas instrucciones](../active-directory-integrating-applications.md).

## Resumen y pasos siguientes
En este tema, ha agregado la API de Outlook para Office 365 a su empresa PowersApps. A continuación, proporcione a los usuarios acceso a la API para que se pueda agregar a sus aplicaciones:

[Agregar una conexión y conceder acceso a los usuarios](powerapps-manage-api-connection-user-access.md)


<!--References-->
[2]: https://msdn.microsoft.com/library/azure/dn768219.aspx
[3]: https://msdn.microsoft.com/library/azure/dn768214.aspx
[4]: https://msdn.microsoft.com/library/azure/dn768220.aspx
[5]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[6]: ./media/powerapps-create-api-dropbox/add-api.PNG
[14]: ./media/powerapps-create-api-office365-outlook/browseall.png
[15]: ./media/powerapps-create-api-office365-outlook/allresources.png

<!---HONumber=AcomDC_1203_2015-->