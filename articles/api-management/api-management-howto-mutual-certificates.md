<properties 
	pageTitle="Cómo asegurar servicios back-end con la autenticación de certificados de cliente en Administración de API de Azure" 
	description="Averigüe cómo asegurar servicios back-end con la autenticación de certificados de cliente en Administración de API de Azure" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/07/2015" 
	ms.author="sdanie"/>

# Cómo asegurar servicios back-end con la autenticación de certificados de cliente en Administración de API de Azure

Administración de API permite acceder de forma segura al servicio back-end de una API con certificados de cliente. Esta guía muestra cómo administrar certificados en el portal del publicador de API y cómo configurar una API para acceder al servicio back-end correspondiente con un certificado.

Para obtener más información sobre cómo administrar certificados con la API de REST de Administración de API, consulte [Entidad de certificado de la API REST de Administración de API de Azure][].

## <a name="prerequisites"> </a>Requisitos previos

Esta guía muestra cómo configurar la instancia de servicio de Administración de API para acceder al servicio back-end de una API con la autenticación de certificados de cliente. Antes de realizar los pasos de este tema, configure el servicio back-end para la autenticación de certificados de cliente y asegúrese de disponer de acceso al certificado y a la contraseña correspondiente para poder cargarlo en el portal del publicador de Administración de API.

## <a name="step1"> </a>Cargar un certificado de cliente

Para comenzar, haga clic en **Administrar** en el Portal de Azure clásico para el servicio Administración de API. De este modo, se abre el portal del publicador de Administración de API.

![Portal del publicador de API][api-management-management-console]

>Si todavía no ha creado una instancia del servicio Administración de API, consulte [Creación de una instancia del servicio de Administración de API][] en el tutorial [Introducción a la Administración de API de Azure][].

Haga clic en **Seguridad** en el menú **Administración de API** de la izquierda y en **Certificados de cliente**.

![Certificados de cliente][api-management-security-client-certificates]

Para cargar un certificado nuevo, haga clic en **Cargar certificado**.

![Carga del certificado][api-management-upload-certificate]

Examine el certificado y escriba la contraseña correspondiente.

>El certificado debe estar en formato **.pfx**. Se admiten los certificados autofirmados.

![Carga del certificado][api-management-upload-certificate-form]

Haga clic en **Cargar** para cargar el certificado.

>En ese momento se validará la contraseña del certificado. Si es incorrecta, aparecerá un mensaje de error.

![Certificado cargado][api-management-certificate-uploaded]

Cuando el certificado se carga, aparece en la pestaña **Certificados de cliente**. Si cuenta con varios certificados, anote el asunto o los cuatro últimos caracteres de la huella digital con los que se selecciona el certificado al configurar una API para usar certificados (conforme a la sección [Configurar una API para realizar la autenticación de puerta de enlace con un certificado de cliente][] que aparece más abajo).

## <a name="step1a"> </a>Eliminar un certificado de cliente

Para eliminar un certificado, haga clic en **Eliminar** junto a este.

![Eliminar certificado][api-management-certificate-delete]

Haga clic en **Sí, eliminar** para confirmar.

![Confirmar eliminación][api-management-confirm-delete]

Si alguna API está usando el certificado, aparecerá una pantalla de advertencia. Para eliminar el certificado, primero quítelo de todas las API que se hayan configurado para usarlo.

![Confirmar eliminación][api-management-confirm-delete-policy]

## <a name="step2"> </a>Configurar una API para realizar la autenticación de puerta de enlace con un certificado de cliente

Haga clic en **API** en el menú **Administración de API** de la izquierda, en el nombre de la API en cuestión y en la pestaña **Seguridad**.

![Seguridad de API][api-management-api-security]

Seleccione **Certificados de cliente**s en la lista desplegable **Con credenciales**.

![Certificados de cliente][api-management-mutual-certificates]

Seleccione el certificado que desea en la lista desplegable **Certificado de cliente**. Si aparecen varios certificados, revise el asunto o los últimos cuatro caracteres de la huella digital (conforme a la sección anterior) para determinar cuál es el certificado correcto.

![Seleccionar certificado][api-management-select-certificate]

Haga clic en **Guardar** para guardar el cambio de configuración de la API.

>Este cambio se hace efectivo de forma inmediata y llama a las operaciones de la API que realizarán la autenticación en el servidor back-end con el certificado.

![Guardar cambios de API][api-management-save-api]

>Cuando se especifica un certificado para la autenticación de puerta de enlace del servicio back-end de una API, el certificado se integra en la directiva de dicha API y puede verse en el editor de directivas.

![Directiva de certificados][api-management-certificate-policy]

## Pasos siguientes

Para más información sobre otras formas de proteger el servicio back-end, como la autenticación HTTP básica o de secretos compartidos, vea el siguiente vídeo.

> [AZURE.VIDEO last-mile-security]

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Introducción a la Administración de API de Azure]: api-management-get-started.md
[Get started with advanced API configuration]: api-management-get-started-advanced.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Creación de una instancia del servicio de Administración de API]: api-management-get-started.md#create-service-instance

[Entidad de certificado de la API REST de Administración de API de Azure]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configurar una API para realizar la autenticación de puerta de enlace con un certificado de cliente]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps



 

<!---HONumber=AcomDC_1210_2015-->