<properties 
	pageTitle="CENC con varios DRM y control de acceso: diseño e implementación de referencia en Azure y Servicios multimedia de Azure" 
	description="Más información sobre cómo obtener licencias de Microsoft® Smooth Streaming Client Porting Kit." 
	services="media-services" 
	documentationCenter="" 
	authors="willzhan"  
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2016"  
	ms.author="willzhan;kilroyh;yanmf;juliako"/>

#CENC con varios DRM y control de acceso: diseño e implementación de referencia en Azure y Servicios multimedia de Azure

##Palabras clave
 
Azure Active Directory, Servicios multimedia de Azure, Reproductor multimedia de Azure, Cifrado dinámico, Entrega de licencias,PlayReady, Widevine, FairPlay, Cifrado común (CENC), Varios DRM, Axinom, DASH, EME, MSE, JSON Web Token (JWT), Notificaciones, Exploradores modernos, Sustitución de claves, Clave simétrica, Clave asimétrica, OpenID Connect, Certificado X509.

##En este artículo

En este artículo se tratan los siguientes temas:

- [Introducción](media-services-cenc-with-multidrm-access-control.md#introduction)
	- [Información general de este artículo](media-services-cenc-with-multidrm-access-control.md#overview-of-this-article)
- [Un diseño de referencia](media-services-cenc-with-multidrm-access-control.md#a-reference-design)
- [Asignación del diseño a la tecnología para la implementación](media-services-cenc-with-multidrm-access-control.md#mapping-design-to-technology-for-implementation)
- [Implementación](media-services-cenc-with-multidrm-access-control.md#implementation)
	- [Procedimientos de implementación](media-services-cenc-with-multidrm-access-control.md#implementation-procedures)
	- [Algunos problemas de implementación](media-services-cenc-with-multidrm-access-control.md#some-gotchas-in-implementation)
- [Temas adicionales para la implementación](media-services-cenc-with-multidrm-access-control.md#additional-topics-for-implementation)
	- [HTTP o HTTPS](media-services-cenc-with-multidrm-access-control.md#http-or-https)
	- [Sustitución de claves de firma de Azure Active Directory](media-services-cenc-with-multidrm-access-control.md#azure-active-directory-signing-key-rollover)
	- [¿Dónde está el token de acceso?](media-services-cenc-with-multidrm-access-control.md#where-is-the-access-token)
	- [¿Y qué pasa con el streaming en vivo?](media-services-cenc-with-multidrm-access-control.md#what-about-live-streaming)
	- [¿Y qué sucede con los servidores de licencias que están fuera de Servicios multimedia de Azure?](media-services-cenc-with-multidrm-access-control.md#what-about-license-servers-outside-of-azure-media-services)
	- [¿Y si quiero usar un STS personalizado?](media-services-cenc-with-multidrm-access-control.md#what-if-i-want-to-use-a-custom-sts)
- [Finalización del sistema y prueba](media-services-cenc-with-multidrm-access-control.md#the-completed-system-and-test)
	- [Inicio de sesión de usuario](media-services-cenc-with-multidrm-access-control.md#user-login)
	- [Uso de extensiones multimedia cifradas para PlayReady](media-services-cenc-with-multidrm-access-control.md#using-encrypted-media-extensipons-for-playready)
	- [Uso de EME para Widevine](media-services-cenc-with-multidrm-access-control.md#using-eme-for-widevine)
	- [Usuarios no autorizados](media-services-cenc-with-multidrm-access-control.md#not-entitled-users)
	- [Ejecución del servicio de token seguro personalizado](media-services-cenc-with-multidrm-access-control.md#running-custom-secure-token-service)
- [Resumen](media-services-cenc-with-multidrm-access-control.md#summary)

##Introducción

Es bien sabido que diseñar y compilar un subsistema DRM para una solución de streaming en línea u OTT es una tarea compleja. Y una práctica común de los operadores o proveedores de vídeo en línea es subcontratar esta parte a proveedores de servicios DRM especializados. El objetivo de este documento es presentar un diseño de referencia y la implementación del subsistema DRM de extremo a extremo en una solución de streaming en línea o de OTT.

Este documento va dirigido a ingenieros que trabajan en el subsistema DRM de soluciones OTT o soluciones de varias pantallas o streaming en línea, o a cualquier lector interesado en el subsistema DRM. Se supone que los lectores están familiarizados con al menos una de las tecnologías DRM del mercado, como PlayReady, Widevine, FairPlay o Adobe Access.

En el término DRM, también incluimos CENC (Cifrado común) con varios DRM. Una de las principales tendencias en el sector de OTT y del streaming en línea es el uso de CENC con varios DRM nativos en distintas plataformas cliente, lo que supone un cambio desde la tendencia anterior de uso de un solo DRM y su SDK cliente. Cuando se utiliza CENC con varios DRM nativos, tanto PlayReady como Widevine se cifran según la especificación [Cifrado común (ISO/IEC 23001-7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/).

Las ventajas de CENC con varios DRM son las siguientes:

1. Reduce el costo del cifrado puesto que se emplea un solo procesamiento de cifrado que tiene como destino distintas plataformas con sus DRM nativos.
1. Reduce el costo de administrar los activos cifrados dado que solo se necesita una copia de ellos.
1. Elimina el costo de licencia de clientes DRM dado que el cliente DRM nativo suele ser gratuito en su plataforma nativa.

Microsoft ha sido un promotor activo de DASH y CENC junto con algunos de los principales reproductores del sector. Servicios multimedia de Microsoft Azure ha estado ofreciendo soporte técnico para DASH y CENC. Para conocer los anuncios recientes, consulte los blogs de Mingfei: [Announcing Google Widevine license delivery services in Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/) (Anuncio de los servicios de entrega de licencias de Google Widevine) y [Azure Media Services adds Google Widevine packaging for delivering multi-DRM stream](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) (Servicios multimedia de Azure agregan el paquete Google Widevine para la entrega de transmisiones de varios DRM).

### Información general de este artículo

Este artículo se ha escrito teniendo en mente los siguientes objetivos:

1. Proporcionar un diseño de referencia del subsistema DRM mediante CENC con varios DRM.
1. Proporcionar una implementación de referencia en la plataforma de Servicios multimedia de Azure o Microsoft Azure.
1. Describir algunos temas de diseño e implementación.

En este artículo, cuando hablamos de "varios DRM" hacemos referencia a:

1. Microsoft PlayReady
1. Google Widevine
1. FairPlay de Apple (aún no se admite en Servicios multimedia de Azure)

En la tabla siguiente se resume la aplicación o plataforma nativa y los exploradores que admite cada DRM.

**Plataforma de cliente**|**Compatibilidad con DRM nativo**|**Explorador/aplicación**|**Formatos de streaming**
----|------|----|----
**Televisores inteligentes, operador STB, OTT STB**|PlayReady principalmente o Widevine u otros|Linux, Opera y WebKit, otros|Varios formatos
**Dispositivos Windows 10 (Windows PC, tabletas de Windows, Windows Phone, Xbox)**|PlayReady|MS Edge/IE11/EME<br/><br/><br/>UWP|DASH (no se admite PlayReady para HLS)<br/><br/>DASH, Smooth Streaming (no se admite PlayReady para HLS) 
**Dispositivos Android (teléfono, tableta, TV)**|Widevine|Chrome/EME|DASH
**iOS (iPhone, iPad), clientes de OS X y Apple TV**|FairPlay|Safari 8+/EME|HLS
**Complemento: Adobe Primetime**|Primetime Access|Complemento del explorador|HDS, HLS

Teniendo en cuenta el estado actual de implementación para cada DRM, normalmente el servicio querrá implementar dos o tres DRM para asegurarse de que aborda todos los tipos de puntos de conexión de la mejor manera.

Hay un equilibrio entre la complejidad de la lógica del servicio y la complejidad del cliente para alcanzar un determinado nivel de experiencia del usuario en los distintos clientes.

Para realizar la selección, tenga en cuenta lo siguiente:

- PlayReady se implementa de forma nativa en cada dispositivo de Windows, en algunos dispositivos Android y está disponibles a través de SDK de software en prácticamente cualquier plataforma.
- Widevine se implementa de forma nativa en todos los dispositivos Android, en Chrome y algunos otros dispositivos.
- FairPlay solo está disponible en clientes iOS y Mac OS o a través de iTunes.

Por tanto un multi-DRM típico sería:

- Opción 1: PlayReady y Widevine
- Opción 2: PlayReady, Widevine y FairPlay


## Un diseño de referencia

En esta sección, presentamos un diseño de referencia que es independiente de las tecnologías usadas para implementarlo.

Un subsistema DRM puede contener los siguientes componentes:

1. Administración de claves
1. Empaquetado de DRM
1. Entrega de licencias DRM
1. Comprobación de derechos
1. Autenticación y autorización
1. Reproductor
1. Origen y CDN

El siguiente diagrama ilustra la interacción de alto nivel entre los componentes de un subsistema DRM.

![Subsistema DRM con CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)
 
Existen tres "capas" básicas en el diseño:

1. Capa de área de operaciones (en negro), que no se expone externamente.
1. Capa de "red perimetral" (azul), que contiene todos los puntos de conexión de cara al público.
1. Capa de Internet pública (azul claro), que contiene la CDN y los reproductores con el tráfico que atraviesa la Internet pública.
 
Debe haber una herramienta de administración del contenido para controlar la protección DRM, con independencia de que el cifrado sea estático o dinámico. Las entradas del cifrado de DRM deben incluir:

1. Contenido de vídeo MBR
1. Clave de contenido
1. Direcciones URL de adquisición de licencias.

Durante el tiempo de reproducción, el flujo de alto nivel es:

1. El usuario está autenticado.
1. Se crea el token de autorización para el usuario.
1. El contenido protegido con DRM (manifiesto) se descarga en el reproductor.
1. El reproductor envía la solicitud de adquisición de licencias a los servidores de licencias junto con el identificador de clave y el token de autorización.

Antes de pasar al tema siguiente, algunas palabras sobre el diseño de la administración de claves.

**Clave de contenido a activo**|**Escenario**
------|---------------------------
1-1|El caso más simple. Proporciona el control más específico. Sin embargo, el resultado es generalmente un costo más alto en la entrega de licencias. Se requiere al menos una solicitud de licencia para cada activo protegido.
1 a muchos|Puede utilizar la misma clave de contenido para varios activos. Por ejemplo, para todos los activos de un grupo lógico, como un género o un subconjunto del género (o género de película), podría utilizar una única clave de contenido.
Muchos a 1|Se necesitan varias claves de contenido para cada activo. <br/><br/>Por ejemplo, si tiene que aplicar protección CENC dinámica con varios DRM para MPEG-DASH y cifrado AES-128 para HLS, necesitará dos claves de contenido distintas, cada una con su propio valor de ContentKeyType. (Para la clave de contenido que se usa en la protección CENC dinámica, se debe utilizar ContentKeyType.CommonEncryption, mientras que para la clave de contenido que se usa en el cifrado AES-128 dinámico, se debe utilizar ContentKeyType.EnvelopeEncryption).<br/><br/>Otro ejemplo: en la protección CENC de contenido DASH, en teoría, se puede usar una clave de contenido para proteger la transmisión de vídeo y otra para proteger la transmisión de audio. 
Muchos a muchos|Combinación de los dos escenarios anteriores: se utiliza un conjunto de claves de contenido para cada uno de los diversos activos del mismo "grupo" de activos.


Otro factor importante a tener en cuenta es el uso de licencias persistentes y no persistentes.

¿Por qué son importantes estas consideraciones?

Si usa la nube pública para la entrega de licencias, afectan directamente a su costo. Para ilustrar esto, pensemos en los dos siguientes casos de diseño diferentes:



1. Suscripción mensual: utiliza licencias persistentes y la asignación de clave de contenido a activo de 1 a muchos. Por ejemplo, para todas las películas de niños, usamos una única clave de contenido para el cifrado. En este caso: 

	El número total de licencias solicitadas para todas las películas o dispositivos de niños = 1

1. Suscripción mensual: utiliza licencias no persistentes y asignación 1 a 1 entre activos y claves de contenido. En este caso:

	El número total de licencias solicitadas para todas las películas o dispositivos de niños = [n.º de películas vistas] x [n.º de sesiones].

Como puede ver fácilmente, los dos diseños diferentes dan como resultado patrones de solicitud de licencia muy diferentes, de ahí el costo de entrega de las licencias si el servicio de entrega de licencias se proporciona mediante una nube pública como Servicios multimedia de Azure.

## Asignación del diseño a la tecnología para la implementación

A continuación, asignamos nuestro diseño genérico a las tecnologías de la plataforma Microsoft Azure o Servicios multimedia de Azure, para lo cual especificamos la tecnología que queremos usar en cada bloque de creación.

En la tabla siguiente se muestra la asignación:

**Bloque de creación**|**Technology**
------|-------
**Reproductor**|[Reproductor multimedia de Azure](https://azure.microsoft.com/services/media-services/media-player/)
**Proveedor de identidades (IDP)**|Azure Active Directory
**Servicio de token seguro (STS)**|Azure Active Directory
**Flujo de trabajo de protección DRM**|Protección dinámica de Servicios multimedia de Azure
**Entrega de licencias DRM**|1\. Entrega de licencias de Servicios multimedia de Azure (PlayReady, Widevine, FairPlay), o <br/>2. Servidor de licencias de Axinom, <br/>3. Servidor de licencias personalizado de PlayReady
**Origen**|Punto de conexión de streaming de Servicios multimedia de Azure
**Administración de claves**|No es necesaria para la implementación de referencia.
**Administración de contenido**|Una aplicación de consola de C#.

En otras palabras, el proveedor de identidades (IDP) y el servicio de token seguro (STS) serán Azure AD. Para el reproductor, usaremos la [API del Reproductor multimedia de Azure](http://amp.azure.net/libs/amp/latest/docs/). Tanto los Servicios multimedia de Azure como el Reproductor multimedia de Azure admiten DASH y CENC con varios DRM.

En el diagrama siguiente se muestra la estructura y el flujo generales con la asignación de tecnología anterior.

![CENC en AMS](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Para configurar el cifrado CENC dinámico, la herramienta de administración de contenido usará las siguientes entradas:

1. Contenido abierto
1. Clave de contenido de la generación o administración de claves
1. Direcciones URL de adquisición de licencias
1. Una lista de información de Azure AD.

La salida de la herramienta de administración de contenido será:

1. ContentKeyAuthorizationPolicy, que contiene la especificación de cómo la entrega de licencias comprueba un token de JWT y las especificaciones de licencias de DRM.
1. AssetDeliveryPolicy, que contiene las especificaciones de formato de streaming, protección DRM y direcciones URL de adquisición de licencias.

Durante el tiempo de ejecución, el flujo es como se muestra a continuación:

1. Tras la autenticación del usuario, se genera un token de JWT.
1. Una de las notificaciones contenidas en el token de JWT es la notificación "grupos" que contiene el identificador de objeto de grupo de "EntitledUserGroup". Esta notificación se utilizará para pasar la "comprobación de derechos".
1. El reproductor descarga el manifiesto de cliente de un contenido protegido por CENC y "ve" lo siguiente:
	1. El id. de clave. 
	1. El contenido está protegido por CENC.
	1. Las direcciones URL de adquisición de licencias.

1. El reproductor realiza una solicitud de adquisición de licencias basada en el explorador o DRM admitidos. En la solicitud de adquisición de licencias, también se envían el id. de clave y el token de JWT. El servicio de entrega de licencias comprobará el token de JWT y las notificaciones contenidas antes de emitir la licencia necesaria.

##Implementación

###Procedimientos de implementación

La implementación incluye los pasos siguientes:

1. Preparar los activos de prueba: codificar o empaquetar un vídeo de prueba en MP4 fragmentado de varias velocidades de bits en Servicios Multimedia de Azure. Este activo NO está protegido con DRM. La protección DRM se realizará posteriormente mediante protección dinámica.
1. Crear el id. de clave y la clave de contenido (opcionalmente a partir de la inicialización de clave). Para nuestro propósito, el sistema de administración de claves no es necesario puesto que estamos trabajando con un solo conjunto de id. de clave y clave de contenido para un par de activos de prueba.
1. Usar la API de AMS para configurar servicios de entrega de licencias de varios DRM para el activo de prueba. Si usa servidores de licencia personalizados por su empresa o proveedores de su empresa en lugar de los servicios de licencia de Servicios multimedia de Azure, puede omitir este paso y especificar las direcciones URL de adquisición de licencias en el paso para configurar la entrega de licencias. Las API de AMS son necesarias para especificar algunas configuraciones detalladas, como restricción de directivas de autorización, plantillas de respuesta de licencia para distintos servicios de licencias DRM, etc. En este momento, el Portal de Azure no proporciona aún la interfaz de usuario necesaria para esta configuración. Puede encontrar información de nivel de API y código de ejemplo en el documento de Julia Kornich: [Uso de cifrado dinámico común de PlayReady o Widevine](media-services-protect-with-drm.md). 
1. Usar la API de AMS para configurar la directiva de entrega de activos para el activo de prueba. Puede encontrar información de nivel de API y código de ejemplo en el documento de Julia Kornich: [Uso de cifrado dinámico común de PlayReady o Widevine](media-services-protect-with-drm.md).
1. Crear y configurar un inquilino de Azure Active Directory en Azure.
1. Crear algunas cuentas y grupos de usuarios en el inquilino de Azure Active Directory: debe crear al menos el grupo "EntitledUser" y agregar un usuario a este grupo. Los usuarios de este grupo pasarán la comprobación de derechos en la adquisición de licencias y los usuarios que no están en este grupo no podrán pasar la comprobación de autenticación y no podrán adquirir ninguna licencia. Ser miembro del grupo "EntitledUser" es una notificación de "grupos" necesaria en el token de JWT emitido por Azure AD. Este requisito de notificación debe especificarse en la configuración del paso de servicios de entrega de licencias de varios DRM.
1. Crear una aplicación de ASP.NET MVC que hospedará el reproductor de vídeo. Esta aplicación ASP.NET estará protegida con autenticación de usuario con respecto al inquilino de Azure Active Directory. Se incluirán las notificaciones adecuadas en los tokens de acceso obtenidos después de la autenticación de usuario. Para este paso se recomienda la API de OpenID Connect. Deberá instalar los siguientes paquetes de NuGet:
	- Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
	- Install-Package Microsoft.Owin.Security.OpenIdConnect
	- Install-Package Microsoft.Owin.Security.Cookies
	- Install-Package Microsoft.Owin.Host.SystemWeb
	- Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
1. Crear un reproductor mediante la [API del Reproductor multimedia de Azure](http://amp.azure.net/libs/amp/latest/docs/). La [API ProtectionInfo del Reproductor multimedia de Azure](http://amp.azure.net/libs/amp/latest/docs/) le permite especificar la tecnología DRM que se usará en distintas plataformas DRM.
1. Matriz de pruebas:

**DRM**|**Browser**|**Resultado de usuario autorizado**|**Resultado de usuario no autorizado**
---|---|-----|---------
**PlayReady**|MS Edge o IE11 en Windows 10|Correcto|Fail (no superado)
**Widevine**|Chrome en Windows 10|Correcto|Fail (no superado)
**FairPlay** |TBD||

George Trifonov, del equipo de Servicios multimedia de Azure, ha escrito un blog donde se proporcionan los pasos detallados para configurar Azure Active Directory para una aplicación de reproductor ASP.NET MVC: [Integración de la aplicación OWIN basada en MVC de Servicios multimedia de Azure con Azure Active Directory y restricción de la entrega de claves de contenido basada en notificaciones de JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

George también ha escrito un blog sobre la [autenticación de tokens de JWT en Servicios multimedia de Azure y Cifrado dinámico](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/). Y este es su [ejemplo sobre la integración de Azure AD con la entrega de claves de Servicios multimedia de Azure](https://github.com/AzureMediaServicesSamples/Key-delivery-with-AAD-integration/).

Para más información sobre Azure Active Directory

- Puede encontrar información para desarrolladores en la [Guía para desarrolladores de Azure Active Directory](../active-directory/active-directory-developers-guide.md).
- Puede encontrar información para administradores en [Administración del directorio de Azure AD](../active-directory/active-directory-administer.md).

### Algunos problemas de implementación

Existen algunas dificultades en la implementación. Por fortuna, la siguiente lista puede ayudarle a solucionar problemas en caso de toparse con ellos.

1. La dirección URL del **emisor** debe terminar con **"/"**.  

	La **audiencia** debe ser el identificador de cliente de la aplicación de reproductor y también se debe agregar **"/"** al final de la dirección URL del emisor.

		<add key="ida:audience" value="[Application Client ID GUID]" />
		<add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" /> 

	En [JWT Decoder](http://jwt.calebb.net/), debería ver **aud** e **iss**, como se muestra a continuación en el token de JWT:
	
	![Problema 1](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)


2. Agregue permisos a la aplicación en AAD (en la pestaña Configurar de la aplicación). Este paso es necesario para cada aplicación (las versiones local e implementada).

	![Problema 2](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)


3. Utilice el emisor correcto en la configuración de la protección CENC dinámica:

		<add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
	
	Lo siguiente no funcionará:
	
		<add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
	
	El GUID es el identificador del inquilino de AAD. Puede encontrar el GUID en la ventana emergente de puntos de conexión del Portal de Azure.

4. Otorgue privilegios de notificaciones de pertenencia a grupo. Asegúrese de que en el archivo de manifiesto de aplicación de AAD tengamos lo siguiente:

	"groupMembershipClaims": "All", (el valor predeterminado es nulo)

5. Establezca el valor adecuado de TokenType al crear los requisitos de restricción.

		objTokenRestrictionTemplate.TokenType = TokenType.JWT;

	Desde que se agregó compatibilidad con JWT (AAD), además de con SWT (ACS), el valor predeterminado de TokenType es TokenType.JWT. Si utiliza SWT y ACS, debe establecerlo en TokenType.SWT.

## Temas adicionales para la implementación
A continuación trataremos algunos temas adicionales de nuestro diseño e implementación.

###¿HTTP o HTTPS?

La aplicación de reproductor de ASP.NET MVC que hemos creado debe ser compatible con lo siguiente:

1. Autenticación de usuarios a través de Azure AD que debe estar en HTTPS.
1. Intercambio de tokens de JWT entre el cliente y Azure AD que debe estar en HTTPS.
1. Se requiere la adquisición de licencias DRM por el cliente para estar en HTTPS si Servicios multimedia de Azure proporciona la entrega de licencias. Por supuesto, el conjunto de productos de PlayReady no impone HTTPS para la entrega de licencias. Si el servidor de licencias de PlayReady está fuera de Servicios multimedia de Azure, se podría usar HTTP o HTTPS.

Por lo tanto, la aplicación de reproductor ASP.NET usará HTTPS como procedimiento recomendado. Esto significa que el Reproductor multimedia de Azure estará en una página en HTTPS. Sin embargo, para el streaming preferimos HTTP, de ahí que debamos considerar el problema del contenido mixto.

1. El explorador no permite contenido mixto. Pero, los complementos como Silverlight y OSMF para Smooth y DASH sí lo hacen. El contenido mixto es un problema de seguridad: esto se debe a la amenaza de la posibilidad de insertar JS malintencionado que puede poner en riesgo los datos del cliente. Los exploradores lo bloquean de forma predeterminada y, hasta ahora, la única manera de solucionar esto es en el servidor (origen), para permitir todos los dominios (con independencia de si son https o http). Probablemente esto tampoco sea una buena idea.
1. Debemos evitar contenido mixto: tanto si se usa HTTP o HTTPS. Al reproducir contenido mixto, silverlightSS tech requiere que se borre una advertencia de contenido mixto. flashSS tech controla el contenido mixto sin advertencia de contenido mixto.
1. Si su punto de conexión de streaming se creó antes de agosto de 2014, no admitirá HTTPS. En este caso, cree y utilice un nuevo punto de conexión de streaming para HTTPS.

En la implementación de referencia, para el contenido protegido con DRM, tanto la aplicación como el proceso de streaming estarán en HTTTPS. Para el contenido abierto, el reproductor no necesita autenticación ni licencia, por lo que tiene la libertad de utilizar HTTP o HTTPS.

### Sustitución de claves de firma de Azure Active Directory

Se trata de un punto importante de su implementación a tener en cuenta. Si no lo considera en su implementación, el sistema completado finalmente dejará de funcionar completamente al cabo de 6 semanas a lo sumo.

Azure AD utiliza el estándar del sector para establecer la confianza entre sí mismo y las aplicaciones que usan Azure AD. En concreto, Azure AD utiliza una clave de firma que consta de un par de claves pública y privada. Cuando Azure AD crea un token de seguridad que contiene información sobre el usuario, Azure AD firma este token con su clave privada antes de enviarlo a la aplicación. Para comprobar que el token es válido y que realmente se originó en Azure AD, la aplicación debe validar la firma del token usando la clave pública expuesta por Azure AD que se encuentra en el documento de metadatos de federación del inquilino. Esta clave pública, y la clave de firma de la que se deriva, es la misma que se utiliza en todos los inquilinos de Azure AD.

Puede encontrar información detallada sobre la sustitución de claves de Azure AD en el documento: [Información importante acerca de la sustitución de la clave de firma en Azure AD](http://msdn.microsoft.com/library/azure/dn641920.aspx/).

Entre el [par de claves pública y privada](https://login.windows.net/common/discovery/keys/):

- Azure Active Directory utiliza la clave privada para generar un token de JWT.
- La clave pública se utiliza en una aplicación, como Servicios de entrega de licencias de DRM en AMS, para comprobar el token de JWT.
 
Por motivos de seguridad, Azure Active Directory realiza la rotación de este certificado periódicamente (cada 6 semanas). En caso de infracciones de seguridad, la sustitución de claves se produce en cualquier momento. Por lo tanto, los servicios de entrega de licencias de AMS deben actualizar la clave pública utilizada cuando Azure AD realiza la rotación del par de claves, de lo contrario, la autenticación del token en AMS dará error y no se emitirá ninguna licencia.

Para ello, es necesario establecer TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument al configurar los servicios de entrega de licencias de DRM.

El flujo de tokens de JWT es como se muestra a continuación:

1.	Azure AD emitirá el token de JWT con la clave privada actual de un usuario autenticado.
2.	Cuando un reproductor ve un CENC con contenido protegido por varios DRM, primero busca el token de JWT emitido por Azure AD.
3.	El reproductor envía la solicitud de adquisición de licencias con el token de JWT a los servicios de entrega de licencias de AMS.
4.	Los servicios de entrega de licencias de AMS utilizarán la clave pública actual o válida de Azure AD para comprobar el token de JWT, antes de emitir las licencias.

Los servicios de entrega de licencias de DRM siempre estarán buscando la clave pública actual o válida de Azure AD. La clave pública presentada por Azure AD será la clave utilizada para comprobar un token de JWT emitido por Azure AD.

¿Y si la sustitución de claves sucede después de que AAD genera un token de JWT pero antes de que los reproductores envíen el token de JWT a los servicios de entrega de licencias de DRM en AMS para que lo comprueben?

Dado que una clave se puede sustituir en cualquier momento, siempre hay más de una clave pública válida disponible en el documento de metadatos de federación. En la entrega de licencias de Servicios multimedia de Azure se puede utilizar cualquiera de las claves especificadas en el documento, ya que una clave se puede sustituir pronto, otra puede ser su reemplazo, etc.

### ¿Dónde está el token de acceso?

Si observa cómo una aplicación web llama a una aplicación de API en [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md#web-application-to-web-api), el flujo de autenticación tiene lugar como se indica a continuación:

1.	Un usuario inicia sesión en Azure AD en la aplicación web (consulte la sección [Explorador web a aplicación web](active-directory-authentication-scenarios.md#web-browser-to-web-application)).
2.	El punto de conexión de autorización de Azure AD redirige al agente de usuario a la aplicación cliente con un código de autorización. El agente de usuario devuelve el código de autorización al URI de redireccionamiento de la aplicación cliente.
3.	La aplicación web necesita adquirir un token de acceso para poder autenticarse ante la API web y recuperar el recurso deseado. Realiza una solicitud al extremo de token de Azure AD y proporciona las credenciales, el identificador del cliente y el URI del identificador de aplicación de la API web. Presenta el código de autorización para demostrar que el usuario ha dado su consentimiento.
4.	Azure AD autentica la aplicación y devuelve un token de acceso de JWT que se usa para llamar a la API web.
5.	A través de HTTPS, la aplicación web usa el token de acceso de JWT devuelto para agregar la cadena JWT con una designación “Bearer” en el encabezado Authorization de la solicitud a la API web. A continuación, la API web valida el token de JWT y, si la validación es correcta, devuelve el recurso deseado.

En este flujo de "identidad de aplicación", la API web confía en que la aplicación web autenticó al usuario. Por ello, este patrón se conoce como subsistema de confianza. En el [diagrama de esta página](http://msdn.microsoft.com/library/azure/dn645542.aspx/) se describe cómo funciona el flujo de concesión del código de autorización.

En la adquisición de licencias con restricción de token, estamos siguiendo el mismo patrón de subsistema de confianza. Y el servicio de entrega de licencias de Servicios multimedia de Azure es el recurso de API web, el "recurso de back-end", una aplicación web que necesita acceso. Entonces, ¿dónde está el token de acceso?

De hecho, vamos a obtener el token de acceso desde Azure AD. Después de autenticar al usuario correctamente, se devuelve el código de autorización. El código de autorización se utiliza entonces junto con el id. de cliente y la clave de aplicación para intercambiarlos por el token de acceso. Y el token de acceso sirve para acceder a una aplicación "puntero" que señala o representa el servicio de entrega de licencias de Servicios multimedia de Azure.

Es necesario registrar y configurar la aplicación "puntero" en Azure AD mediante los pasos siguientes:

1.	En el inquilino de Azure AD,

	- agregue una aplicación (recurso) con la dirección URL de inicio de sesión 

	https://[resource_name].azurewebsites.net/ y

	- la URL de identificador de aplicación 
	
	https://[aad_tenant_name].onmicrosoft.com/[resource_name]; 
2.	Agregue una nueva clave para la aplicación de recursos.
3.	Actualice el archivo de manifiesto de aplicación para que la propiedad groupMembershipClaims tenga el siguiente valor: "groupMembershipClaims": "All".  
4.	En la aplicación de Azure AD que apunta a la aplicación web de reproductor, en la sección "Permisos para otras aplicaciones", agregue la aplicación de recursos que se agregó en el paso 1 anteriormente. En "Permisos delegados", active la casilla "Acceso [nombre\_de\_recurso]". Con ello se proporciona permiso a la aplicación web para crear tokens de acceso para el acceso a la aplicación de recursos. Esto debe hacerlo para la versión local e implementada de la aplicación web si va a desarrollar con la aplicación web de Visual Studio y Azure.
	
Por lo tanto, el token de JWT emitido por Azure AD es realmente el token de acceso a este recurso "puntero".

### ¿Y qué pasa con el streaming en vivo?

Antes, nuestra discusión se ha centrado en los activos a petición. Pero, ¿y el streaming en vivo?

La buena noticia es que puede usar exactamente el mismo diseño y la misma implementación para proteger el streaming en vivo en Servicios multimedia de Azure, solo hay que tratar el activo asociado con un programa como un "activo VOD".

En concreto, es bien sabido que para hacer streaming en vivo en Servicios multimedia de Azure, debe crear un canal y, luego, un programa bajo él. Para crear el programa, debe crear un activo que contendrá el archivo en vivo del programa. Para proporcionar a CENC protección de varios DRM del contenido en directo, todo lo que debe hacer es aplicar la misma configuración o procesamiento al activo como si fuera un "activo VOD", antes de iniciar el programa.

### ¿Y qué sucede con los servidores de licencias que están fuera de Servicios multimedia de Azure?

Con frecuencia, puede ocurrir que los clientes inviertan en granjas de servidores de licencias en su propio centro de datos u hospedados por proveedores de servicios DRM. Por fortuna, la protección del contenido de Servicios multimedia de Azure le permite trabajar en modo híbrido: el contenido está hospedado y protegido de forma dinámica en Servicios multimedia de Azure, mientras que las licencias DRM las entregan servidores externos a Servicios multimedia de Azure. En este caso, hay considerar los siguientes cambios:

1. El servicio de token seguro debe emitir tokens que sean aceptables y que se puedan comprobar en la granja de servidores de licencias. Por ejemplo, los servidores de licencias de Widevine proporcionados por Axinom requieren un token de JWT específico que contenga el "mensaje de derechos". Por lo tanto, debe tener un STS para emitir dicho token. Los autores han realizado este tipo de implementación. Puede encontrar los detalles en el siguiente documento en [Centro de documentación de Azure](https://azure.microsoft.com/documentation/): [Uso de Axinom para entregar licencias de Widevine a Servicios multimedia de Azure](media-services-axinom-integration.md). 
1. Ya no necesitará configurar el servicio de entrega de licencias (ContentKeyAuthorizationPolicy) en Servicios multimedia de Azure. Lo que debe hacer es proporcionar las direcciones URL de adquisición de licencias (para PlayReady, Widevine y FairPlay) cuando configura AssetDeliveryPolicy en la configuración de CENC con varios DRM.
 
### ¿Y si quiero usar un STS personalizado?

Puede haber varias razones por las que un cliente prefiera el uso de un STS (Servicio de token seguro) personalizado para proporcionar tokens de JWT. Algunas de ellas son:

1.	El proveedor de identidades (IDP) utilizada por el cliente no admite a STS. En este caso, un STS personalizado puede ser una opción.
2.	El cliente puede necesitar un control más flexible o más estricto en la integración de STS con el sistema de facturación de los suscriptores del cliente. Por ejemplo, un operador MVPD puede ofrecer varios paquetes de suscriptor OTT, por ejemplo, premium, básico, deportes, etc. El operador puede querer hacer coincidir las notificaciones de un token con el paquete de un suscriptor de forma que solo el contenido del paquete adecuado esté disponible. En este caso, un STS personalizado proporciona la flexibilidad y el control necesarios.

Dos cambios deben realizarse cuando se utiliza un STS personalizado:

1.	Al configurar el servicio de entrega de licencias para un activo, debe especificar la clave de seguridad utilizada para la comprobación por el STS personalizado (más detalles a continuación) en lugar de la clave actual de Azure Active Directory.
2.	Cuando se genera un token de JTW, se especifica una clave de seguridad en lugar de la clave privada del certificado X509 actual en Azure Active Directory.

Hay dos tipos de claves de seguridad:

1.	Clave simétrica: se utiliza la misma clave para generar y comprobar un token de JWT.
2.	Clave asimétrica: se utiliza un par de claves pública/privada de un certificado X509 junto con la clave privada para cifrar o generar un token de JWT, y la clave pública para comprobar el token.

####Nota técnica

Si utiliza .NET Framework o C# como plataforma de desarrollo, el certificado X509 usado en la clave de seguridad asimétrica debe tener una longitud de clave de al menos 2048. Se trata de un requisito de la clase System.IdentityModel.Tokens.X509AsymmetricSecurityKey en .NET Framework. De lo contrario, se producirá la siguiente excepción:

IDX10630: El valor 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' de la firma no puede ser inferior a '2048' bits.

## Finalización del sistema y prueba

Recorreremos algunos escenarios en el sistema de un extremo a otro completado para que los lectores puedan tener una idea general del comportamiento antes de obtener una cuenta de inicio de sesión.

Puede encontrar la aplicación web de reproductor y su inicio de sesión [aquí](https://openidconnectweb.azurewebsites.net/).

Si lo que necesita es un escenario "no integrado": activos de vídeo hospedados en Servicios multimedia de Azure que están sin protección o DRM protegidos pero sin autenticación de token (emisión de una licencia a cualquiera que la solicite), puede probarlo sin inicio de sesión (cambiando a HTTP si su transmisión por secuencias de vídeo es sobre HTTP).

Si lo que necesita es un escenario integrado de extremo a extremo: los activos de vídeo están bajo la protección DRM dinámica en Servicios multimedia de Azure, con autenticación de token y token de JWT generado por Azure AD, debe iniciar sesión.

### Inicio de sesión de usuario

Para probar el sistema DRM integrado de extremo a extremo, debe tener una "cuenta" creada o agregada.

¿Qué cuenta?

Aunque Azure inicialmente permitía el acceso únicamente a usuarios de cuentas Microsoft, ahora permite el acceso a usuarios de ambos sistemas. Esto se hizo haciendo que todas las propiedades de Azure confiaran en Azure AD para la autenticación, haciendo que Azure AD autenticara usuarios de la organización y creando una relación de federación en la que Azure AD confiaba en el sistema de identidad del cliente de cuentas de Microsoft para autenticar usuarios de cliente. Como resultado, Azure AD puede autenticar cuentas Microsoft de tipo "Invitado", así como cuentas de Azure AD "nativas".

Puesto que Azure AD confía en el dominio de la cuenta Microsoft (MSA), puede agregar cuentas de cualquiera de los siguientes dominios al inquilino de Azure AD personalizado y usar la cuenta para iniciar sesión:

**Nombre de dominio**|**Dominio**
-----------|----------
**Dominio de inquilino de Azure AD personalizado**|somename.onmicrosoft.com
**Dominio corporativo**|microsoft.com
**Dominio de la cuenta Microsoft (MSA)**|outlook.com, live.com, hotmail.com

Puede ponerse en contacto con cualquiera de los autores para que le creen o le agreguen una cuenta.

A continuación, se muestran las capturas de pantalla de diferentes páginas de inicio de sesión que se usan con distintas cuentas de dominio.

**Cuenta de dominio de inquilino de Azure AD personalizado**: en este caso, verá la página de inicio de sesión personalizada del dominio de inquilino de Azure AD personalizado.

![Cuenta de dominio del inquilino de Azure AD personalizado](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Cuenta de dominio de Microsoft con tarjeta inteligente**: en este caso, verá la página de inicio de sesión personalizada por el responsable de TI corporativa de Microsoft con autenticación en dos fases.

![Cuenta de dominio del inquilino de Azure AD personalizado](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Cuenta Microsoft (MSA)**: en este caso, verá la página de inicio de sesión de Microsoft de cuentas para consumidores.

![Cuenta de dominio del inquilino de Azure AD personalizado](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### Uso de extensiones multimedia cifradas para PlayReady

En un explorador moderno con compatibilidad con las extensiones multimedia cifradas (EME)/PlayReady, como Internet Explorer 11 en Windows 8.1 o superior y el explorador de Microsoft Edge en Windows 10, PlayReady será el DRM subyacente para EME.

![Uso de EME para PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

El área oscura del reproductor se debe a que la protección de PlayReady impide la realización de una captura de pantalla del vídeo protegido.

La siguiente pantalla muestra los complementos y la compatibilidad con MSE/EME del reproductor.

![Uso de EME para PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME en Microsoft Edge e Internet Explorer 11 en Windows 10 permiten invocar [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) en dispositivos de Windows 10 que lo admitan. PlayReady SL3000 desbloquea el flujo del contenido premium mejorado (4K, HDR, etc.) y los nuevos modelos de entrega de contenido (ventana anticipada para el contenido mejorado).

Céntrese en los dispositivos de Windows: PlayReady es el único DRM en el hardware disponible en dispositivos de Windows (PlayReady SL3000). Un servicio de streaming puede usar PlayReady mediante EME o una aplicación de UWP y ofrecer una mayor calidad de vídeo con PlayReady SL3000 que otro DRM. Normalmente, el contenido de 2K se propagará a través de Chrome o Firefox y el contenido de 4K lo hará a través de Microsoft Edge/IE11 o una aplicación de UWP en el mismo dispositivo (según la configuración e implementación del servicio).


#### Uso de EME para Widevine

En un explorador moderno con compatibilidad con EME/Widevine, como Chrome 41+ en Windows 10, Windows 8.1, Mac OSX Yosemite y Chrome en Android 4.4.4, Google Widevine es el DRM que subyace a EME.

![Uso de EME para Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Tenga en cuenta que Widevine no impide la realización de capturas de pantalla de vídeo protegido.

![Uso de EME para Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### Usuarios no autorizados

Si un usuario no es miembro del grupo "Usuarios autorizados", el usuario no podrá pasar la "comprobación de derechos" y el servicio de licencias de varios DRM se negará a emitir la licencia solicitada, como se muestra a continuación. La descripción detallada es "error al adquirir licencias", que es como se ha diseñado.

![Usuarios no autorizados](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### Ejecución del servicio de token seguro personalizado

Para el escenario de ejecución del servicio de token seguro (STS) personalizado, el STS personalizado emitirá el token de JWT mediante la clave simétrica o asimétrica.

Caso de uso de clave simétrica (con Chrome):

![Ejecución del STS personalizado](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

El caso de uso de una clave asimétrica mediante un certificado X 509 (con el explorador moderno de Microsoft).

![Ejecución del STS personalizado](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

En los dos casos mencionados anteriormente, la autenticación de usuario indica lo mismo: mediante Azure AD. La única diferencia es que los tokens los emite el STS personalizado en lugar de Azure AD. Desde luego, al configurar la protección CENC dinámica, la restricción del servicio de entrega de licencias especifica el tipo de token de JWT: clave simétrica o asimétrica.

## Resumen

En este documento, hemos examinado el CENC con varios DRM nativos y el control de acceso mediante la autenticación con tokens: su diseño y su implementación mediante Azure, Servicios multimedia de Azure y el Reproductor multimedia de Azure.

- Se presenta un diseño de referencia que contiene todos los componentes necesarios de un subsistema DRM/CENC,
- y una implementación de referencia en Azure, Servicios multimedia de Azure y el Reproductor multimedia de Azure.
- También se han tratado algunos temas directamente relacionados con el diseño y la implementación.


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###Agradecimientos 

William Zhang, Mingfei Yan, Roland Le Franc, Kilroy Hughes, Julia Kornich

<!---HONumber=AcomDC_0204_2016-->