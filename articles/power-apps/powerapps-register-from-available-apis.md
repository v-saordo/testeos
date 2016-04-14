<properties
	pageTitle="Creación y configuración de API administradas por Microsoft y API administradas por TI en PowerApps Enterprise | Microsoft Azure"
	description="Obtenga información sobre las API disponibles en PowerApps y cómo registrarlas en el Portal de Azure."
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="01/21/2016"
   ms.author="guayan"/>

# Registro de una API administrada por Microsoft o una API administrada por TI
Existen API **administradas por Microsoft** y API **administradas por TI**. Cuando habilita PowerApps Enterprise, las API administradas por Microsoft quedan automáticamente a su disposición. La memoria, la conectividad, la confianza y más también se administran de forma automática por usted. El paso siguiente es escribir alguna configuración de usuario específica, como una cuenta y contraseña de Twitter.

Con las API administradas por TI, controla y supervisa todo el contenido, incluida la memoria, la conectividad, la confianza, etc. Las API administradas por TI también incluyen las API que se pueden conectar a un sistema local, como SQL Server y SharePoint Server.

Para usar las API **administradas por Microsoft** o las API **administradas por TI**, debe "registrar" las API en el Portal de Azure. Una vez que las registra, puede usarlas en las aplicaciones. Están disponibles las siguientes opciones:

- Registro de una API administrada por Microsoft pregenerada o una API administrada por TI (en este tema).
- Registro de una aplicación web, una aplicación de API y una aplicación móvil hospedada en el [entorno del Servicio de aplicaciones](powerapps-register-api-hosted-in-app-service.md).
- Registro de una de sus propias API de Swagger mediante una [definición de API de Swagger 2.0](powerapps-register-existing-api-from-api-definition.md).

Este artículo se centra en el **registro de las API administradas por Microsoft pregeneradas y las API administradas por TI**.

#### Requisitos previos para empezar

- Suscríbase a [PowerApps Enterprise](powerapps-get-started-azure-portal.md).
- Cree un [entorno del Servicio de aplicaciones](powerapps-get-started-azure-portal.md).


## Vista de las API disponibles administradas por Microsoft
Las API **administradas por Microsoft** se incluyen con PowerApps Enterprise y también se hospedan en Microsoft. En muchos escenarios, las API administradas por Microsoft son ideales para las aplicaciones. Por ejemplo, si la aplicación envía un tweet, carga un archivo a OneDrive o muestra los datos de un archivo Excel, estas API administradas por Microsoft son una opción adecuada.

Algunos de los beneficios adicionales incluyen:

- Obtiene todas las API administradas por Microsoft disponibles para registrar su propia instancia. 
- Los recursos, incluida la red, la memoria o las configuraciones de seguridad, se supervisan de forma automática. Por ejemplo, si necesita más memoria para mostrar datos de Excel en la aplicación, se agrega automáticamente más memoria. 
- Se crea automáticamente una confianza entre la aplicación y la API, como Office y Twitter. 


#### API administradas por Microsoft

API | Descripción | Vínculo a los pasos
--- | --- | ---
![][31] | **Dropbox**<br/><br/> Puede obtener elementos, actualizarlos y eliminarlos, etc. | [**Introducción**](powerapps-create-api-dropbox.md)
![][32] | **Dynamics CRM Online**<br/><br/> Puede obtener elementos, actualizarlos y eliminarlos, etc. | [**Introducción**](powerapps-create-api-crmonline.md)
![][33] | **Excel**<br/><br/> Puede obtener elementos, actualizarlos y eliminarlos, etc. | [**Introducción**](powerapps-create-api-excel.md)
![][34] | **Google Drive**<br/><br/> Puede obtener elementos, actualizarlos y eliminarlos, etc. | [**Introducción**](powerapps-create-api-googledrive.md)
![][35] | **Microsoft Translator**<br/><br/>Traduce texto, detecta idiomas, etc. | [**Introducción**](powerapps-create-api-microsofttranslator.md)
![][36] | **Office365 Outlook**<br/><br/>Administre su correo electrónico. | [**Introducción**](powerapps-create-api-office365-outlook.md)
![][37] | **Usuarios de Office365**<br/><br/>Tenga acceso a perfiles de usuario, sus administradores, sus subordinados directos, etc. | [**Introducción**](powerapps-create-api-office365-users.md)
![][38] | **OneDrive**<br/><br/> Puede obtener elementos, actualizarlos y eliminarlos, etc. | [**Introducción**](powerapps-create-api-onedrive.md)
![][39] | **Salesforce**<br/><br/> Puede obtener elementos, actualizarlos y eliminarlos, etc. | [**Introducción**](powerapps-create-api-salesforce.md)
![][40] | **SharePoint Online**<br/><br/> Puede obtener elementos, actualizarlos y eliminarlos, etc. | [**Introducción**](powerapps-create-api-sharepointonline.md)
![][43] | **Twitter**<br/><br/> Envíe y busque tweets, vea los seguidores, etc. | [**Introducción**](powerapps-create-api-twitter.md)


## Vista de las API administradas por TI
Usted controla y administra las API **administradas por TI**. No se ejecutan en el entorno administrado por Microsoft. En algunos escenarios, usar estas API en su propio entorno administrado por TI puede satisfacer las necesidades de sus aplicaciones. Por ejemplo, su aplicación usa la API de Twitter y desea usar la clave de Twitter de la organización (en lugar de la clave de Twitter de Microsoft). En esta situación, es mejor configurar la API de Twitter como una API administrada por TI. En otro ejemplo, la aplicación utiliza la API de SQL Server para conectarse a una base de datos local. En un entorno administrado por TI, puede configurar una red virtual o usar Express Route para conectarse de forma local. La decisión depende de usted.

Algunos de los beneficios adicionales incluyen:

- Usted supervisa los recursos, incluida la red, la memoria o las configuraciones de seguridad. Por ejemplo, si necesita más memoria para mostrar datos de Excel en su aplicación, usted controla cuánta memoria más debe agregar al entorno. 
- Configura la confianza y controla la seguridad entre las aplicaciones y la API. Por ejemplo, determina si la API de Office365 puede ser una API administrada por Microsoft (una confianza automática) o usa la API de Office365 dentro de su propio entorno (crea su propia confianza). 
- **Todas** las API administradas por Microsoft también pueden ser API administradas por TI. Por ejemplo, si quiere crear su propia instancia de Office365 y tener el control total de esta instancia, puede hacerlo. Luego, puede usar su API de Office365 administrada por TI y la API de Office365 administrada por Microsoft en el mismo entorno. En realidad, depende de las necesidades de la aplicación.
- Cuando se conecta a sistemas locales o con la API de Búsqueda de Bing, controla la seguridad, la autenticación, las licencias, etc.


#### API administradas por TI
> [AZURE.NOTE] Recuerde que **todas** las API administradas por Microsoft también pueden ser API administradas por TI. Las API que aparecen a continuación solo son API administradas por TI; no pueden ser administradas por Microsoft.

API | Descripción | Vínculo a los pasos
--- | --- | ---
![][30] | **Búsqueda de Bing**<br/><br/>Inserte resultados de la búsqueda, agregue funcionalidad de búsqueda, etc. | [**Introducción**](powerapps-create-api-bingsearch.md)
![][42] | **SQL Server**<br/><br/>Puede obtener elementos, actualizarlos y eliminarlos, etc. | [**Introducción**](powerapps-create-api-sqlserver.md)
![][41] | **SharePoint Server**<br/><br/>Puede obtener elementos, actualizarlos y eliminarlos, etc. | [**Introducción**](powerapps-create-api-sharepointserver.md)


#### Por qué registrar sus propias instancias

Usar las API administradas por Microsoft listas para usar resulta conveniente. Dicho esto, registrar sus propias instancias como API administradas por TI presenta muchos beneficios. A nivel general, le recomendamos crear API administradas por TI cuando desee:

- Tener una capacidad de administración completa sobre las API, incluido el acceso de usuario, la seguridad cuando se conecta a los demás sistemas, los límites de llamadas a la API, la supervisión y características avanzadas, como directivas, etc.
- Tener acceso a los datos locales, dado que el Entorno del Servicio de aplicaciones es compatible con las redes virtuales.
- Configurar las API para los usuarios profesionales, las que probablemente no puedan usar por sí mismos.

La tabla siguiente compara las funcionalidades de las API administradas por Microsoft y las administradas por TI:

| Capacidad | Administradas por Microsoft | Administradas por TI |
| ---------- | ----------------- | ------------ |
| Límites de llamadas a la API | Definida por Microsoft | Definida por usted (a través de directivas) |
| Traiga su propia clave cuando se conecta a SaaS | No compatible | Compatible |
| Acceso de usuario a la API | Habilitada para todos | Completamente administrable a nivel de usuario y grupo de AAD |
| Supervisión de la API | No compatible | Compatible |
| Directivas de la API | No compatible | Compatible |
| Acceso de usuario a la conexión | Solo vista | Completamente administrable a nivel de usuario y grupo de AAD |
| Administración de conexión | Solo vista | Completamente administrable |


## Registro de una API administrada por Microsoft o una API administrada por TI

1. En el [Portal de Azure](https://portal.azure.com/), inicie sesión con su cuenta profesional (*suNombreUsuario*@*SuEmpresa*.com). Automáticamente inicia sesión en la suscripción de su empresa.
2. Seleccione **Examinar**, **PowerApps** y, luego, **Administrar API**: 
![][17]
3. En Administrar API, seleccione **Agregar**: 
![][18]  
4. En **Agregar API**, especifique las propiedades de la API:  

	- En **Nombre**, escriba un nombre para la API. Tenga en cuenta que el nombre que escriba se incluirá en la dirección URL de tiempo de ejecución de la API. Elija un nombre descriptivo y único dentro de su organización.
	- En **Origen**, seleccione **Desde API disponibles**: 
	![][19]
5. Seleccione **API** y, luego, elija la API que desea registrar: 
![][20]
6. Seleccione la API específica y agregue cualquier propiedad configurable.
7. Seleccione **AGREGAR** para completar estos pasos.

> [AZURE.TIP] Cuando registre una API, la registrará en su entorno del Servicio de aplicaciones. Una vez se encuentre en el entorno del Servicio de aplicaciones, otras aplicaciones pueden usarla dentro del mismo entorno del Servicio de aplicaciones.


## Resumen y pasos siguientes

En este tema, vio cómo registrar su propia instancia de las API disponibles que existen listas en PowerApps. Estos son algunos temas y recursos relacionados que le permitirán obtener más información sobre PowerApps:


- [Configurar las propiedades de la API](powerapps-configure-apis.md)
- [Dar a los usuarios acceso a las API](powerapps-manage-api-connection-user-access.md)
- [Empezar a crear sus propias aplicaciones PowerApps](https://powerapps.microsoft.com/tutorials/)


<!--References-->

[17]: ./media/powerapps-register-from-available-apis/registered-apis-part.png
[18]: ./media/powerapps-register-from-available-apis/add-api-button.png
[19]: ./media/powerapps-register-from-available-apis/add-api-blade.png
[20]: ./media/powerapps-register-from-available-apis/add-api-select-from-marketplace-blade.png
[30]: ./media/powerapps-register-from-available-apis/bingsearch.png
[31]: ./media/powerapps-register-from-available-apis/dropbox.png
[32]: ./media/powerapps-register-from-available-apis/dynamicscrmonline.png
[33]: ./media/powerapps-register-from-available-apis/excel.png
[34]: ./media/powerapps-register-from-available-apis/googledrive.png
[35]: ./media/powerapps-register-from-available-apis/microsofttranslator.png
[36]: ./media/powerapps-register-from-available-apis/office365outlook.png
[37]: ./media/powerapps-register-from-available-apis/office365users.png
[38]: ./media/powerapps-register-from-available-apis/onedrive.png
[39]: ./media/powerapps-register-from-available-apis/salesforce.png
[40]: ./media/powerapps-register-from-available-apis/sharepointonline.png
[41]: ./media/powerapps-register-from-available-apis/sharepointserver.png
[42]: ./media/powerapps-register-from-available-apis/sqlserver.png
[43]: ./media/powerapps-register-from-available-apis/twitter.png

<!---HONumber=AcomDC_0224_2016-->