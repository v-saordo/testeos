<properties
	pageTitle="Crear una definición de la API de Swagger 2.0 a partir de una API en PowerApps Enterprise | Microsoft Azure"
	description="Obtenga información acerca de cómo registrar una API a partir de la definición de la API de Swagger 2.0 creada a partir de una API existente"
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
   ms.date="12/17/2015"
   ms.author="guayan"/>

# Registrar una API de la definición de la API de Swagger 2.0  
Muchas organizaciones ya tienen algunas API existentes que los usuarios pueden usar y consumir dentro de sus aplicaciones. Para usar estas API en sus aplicaciones, debe "registrar" las API en el Portal de Azure. Están disponibles las siguientes opciones:

- Registro de una [API administrada por Microsoft o una API administrada por TI](powerapps-register-from-available-apis.md) integrada.
- Registro de una aplicación web, una aplicación de API y una aplicación móvil hospedada en el [entorno del Servicio de aplicaciones](powerapps-register-api-hosted-in-app-service.md).
- Registro de una de sus propias API de Swagger mediante una definición de API de Swagger 2.0 (en este tema).

En este artículo se muestra cómo **registrar una de sus propias API mediante la definición de API de Swagger 2.0** que ha creado a partir de una API existente.

#### Requisitos previos para empezar

- Suscríbase a [PowerApps Enterprise](powerapps-get-started-azure-portal.md)
- Cree un [entorno del Servicio de aplicaciones](powerapps-get-started-azure-portal.md)

## Registrar una API existente mediante la definición de la API de Swagger 2.0

Es muy fácil registrar estas API existentes. Los pasos incluyen:

1. Cree la definición de la API de [Swagger 2.0](http://swagger.io) para la API existente. PowerApps usa Swagger 2.0 como formato de definición de API. Puede usar las herramientas a las que se hace referencia en el [sitio web de Swagger 2.0](http://swagger.io) para ayudarle a crear fácilmente una definición de la API de Swagger 2.0. Puntos a tener en cuenta:  

	- La propiedad ``host`` debe apuntar al punto de conexión real de la API existente. No use ningún esquema ni rutas de acceso secundarias. Por ejemplo, escriba ``api.contoso.com``. <br/><br/>
	- La propiedad ``basePath`` debe enumerar las rutas de acceso secundarias del punto de conexión de la API existente, si hay alguno. Comience con una barra diagonal ``/``. Por ejemplo, escriba ``/purchaseorderapi``.

2. Asegúrese de que el entorno del Servicio de aplicaciones pueda acceder a la API existente de manera segura: <br/><br/> a) Si no le preocupa que se pueda acceder a su API a través de Internet, puede configurar la autenticación de acceso básico HTTP entre el entorno del Servicio de aplicaciones y la API existente. [Actualice una API existente](powerapps-configure-apis.md) para ver cómo. <br/><br/> b) Si desea mantener la API dentro de la red de su organización, puede configurar una red virtual en el entorno del Servicio de aplicaciones para tener acceso seguro a la red de su organización. Más información sobre [entornos del servicio de aplicaciones](../app-service-app-service-environment-intro.md).

3. En el [Portal de Azure](https://portal.azure.com/), seleccione **PowerApps** y, a continuación, **Administrar API**: ![][11]
4. En Administrar API, seleccione **Agregar**: ![][12]
5. En **Agregar API**, especifique las propiedades de la API:  

	- En **Nombre**, escriba un nombre para la API. Tenga en cuenta que el nombre que escriba se incluirá en la dirección URL de tiempo de ejecución de la API. Elija un nombre descriptivo y único dentro de su organización.
	- En **Origen**, seleccione **Importar desde Swagger 2.0**.

6. En **Definición de la API (Swagger 2.0)**, cargue el archivo de definición de la API de Swagger 2.0: ![][13]
7. Seleccione **AGREGAR** para completar estos pasos.

> [AZURE.TIP]Cuando registre una API, la registrará en su entorno del Servicio de aplicaciones. Una vez se encuentre en el entorno del Servicio de aplicaciones, otras aplicaciones pueden usarla dentro del mismo entorno del Servicio de aplicaciones.

## Resumen y pasos siguientes

En este tema, ha visto cómo registrar una API de la definición de la API de Swagger 2.0. Estos son algunos temas y recursos relacionados que le permitirán obtener más información sobre PowerApps:

- [Configuración de las propiedades de API](powerapps-configure-apis.md)
- [Dar a los usuarios acceso a las API](powerapps-manage-api-connection-user-access.md)
- [Empezar a crear sus aplicaciones en PowerApps](https://powerapps.microsoft.com/tutorials/)

<!--References-->
[11]: ./media/powerapps-register-existing-api-from-api-definition/registered-apis-part.png
[12]: ./media/powerapps-register-existing-api-from-api-definition/add-api-button.png
[13]: ./media/powerapps-register-existing-api-from-api-definition/add-api-blade.png

<!---HONumber=AcomDC_1223_2015-->