<properties
  pageTitle="Control de versiones del SDK de cliente y servidor en Aplicaciones móviles y Servicios móviles | Servicio de aplicaciones de Azure"
  description="Lista de SDK de cliente y compatibilidad con versiones de SDK de servidor para Servicios móviles y Aplicaciones móviles de Azure"
  services="app-service\mobile"
  documentationCenter=""
  authors="lindydonna" 
  manager="dwrede"
  editor=""/>

<tags
  ms.service="app-service-mobile"
  ms.workload="mobile"
  ms.tgt_pltfrm="mobile-multiple"
  ms.devlang="dotnet"
  ms.topic="article"
  ms.date="12/15/2015"
  ms.author="donnam"/>

# Control de versiones de cliente y servidor en Aplicaciones móviles y Servicios móviles

La versión más reciente de Servicios móviles de Azure es la característica **Aplicaciones móviles** del Servicio de aplicaciones de Azure.

<!-- Azure App Service offers a number of platform benefits over Mobile Services, including continuous integration and deployment, staging lots, and VNET support.
 -->

Los SDK de cliente y servidor de Aplicaciones móviles originalmente se basaban en los de Servicios móviles, pero *no* son compatibles entre sí. Es decir, debe usar el SDK de cliente de *Aplicaciones móviles* con un SDK de servidor de *Aplicaciones móviles* y, de forma similar, para *Servicios móviles*. Este contrato se aplica a través de un valor de encabezado especial usado por los SDK de cliente y servidor, `ZUMO-API-VERSION`.

Nota: cada vez que este documento hace referencia a un back-end de *Servicios móviles*, no es necesario que esté hospedado en Servicios móviles. Ahora es posible migrar un servicio móvil para que se ejecute en Servicio de aplicaciones sin realizar ningún cambio en el código, pero el servicio seguiría usando versiones de SDK de *Servicios móviles*.

Para más información sobre la migración al Servicio de aplicaciones sin realizar ningún cambio en el código, consulte el artículo [Migrate your existing Azure mobile service to App Service].

## Especificación del encabezado

La clave `ZUMO-API-VERSION` se puede especificar en el encabezado HTTP o en la cadena de consulta. El valor es una cadena de versión con el formato **x.y.z**.

Por ejemplo:

GET https://service.azurewebsites.net/tables/TodoItem

HEADERS: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## Anulación de la comprobación de la versión

Puede anular la comprobación de la versión estableciendo el valor **true** para la configuración de la aplicación **MS\_SkipVersionCheck**. Especifique esto en el archivo web.config o en la sección Configuración de la aplicación del Portal de Azure.

> [AZURE.NOTE]Hay una serie de cambios de comportamiento entre Servicios móviles y Aplicaciones móviles, especialmente en las áreas de sincronización sin conexión, autenticación y notificaciones push. Solo debe anular la comprobación de versión después de realizar pruebas exhaustivas para asegurarse de que estos cambios de comportamiento no rompen la funcionalidad de la aplicación.

## Resumen de compatibilidad para todas las versiones

En la tabla siguiente se muestra la compatibilidad entre todos los tipos de cliente y servidor. Un back-end se clasifica como **Servicios** móviles o **Aplicaciones** móviles según el SDK de servidor que usa.

| | Node.js o .NET de **Servicios móviles** | Node.js o .NET de **Aplicaciones móviles** |
| ----------                | -----------------------             |   ----------------              |
| [Clientes de Servicios móviles] | Aceptar | Error* |
| [Clientes de Aplicaciones móviles] | Error* | Aceptar |

*Esto se puede controlar especificando **MS\_SkipVersionCheck**.


<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is http://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>Cliente y servidor de Servicios móviles

Los SDK de cliente de la tabla siguiente son compatibles con **Servicios móviles**.

Nota: los SDK de cliente de Servicios móviles *no* envían un valor de encabezado para `ZUMO-API-VERSION`. Si el servicio recibe este valor de encabezado o de cadena de consulta, se devolverá un error, a menos que lo haya anulado explícitamente como se ha descrito anteriormente.

### <a name="MobileServicesClients"></a> SDK de cliente de *Servicios* móviles

| Plataforma de cliente | Versión | Valor de encabezado de versión |
| -------------------               | ------------------------                                                  | -------------------  |
| Cliente administrado (Windows, Xamarin) | [1\.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) | N/D |
| iOS | [2\.2.2](http://aka.ms/gc6fex) | N/D |
| Android | [2\.0.3](https://go.microsoft.com/fwLink/?LinkID=280126) | N/D |
| HTML | [1\.2.7](http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) | N/D |

### SDK de servidor de *Servicios* móviles

| Plataforma de servidor | Versión | Encabezado de versión aceptado |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET | [WindowsAzure.MobileServices.Backend.* Versión 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) | **Sin encabezado de versión** |
| Node.js | (próximamente) | **Sin encabezado de versión** |

<!-- TODO: add Node npm version -->

### Comportamiento de back-ends de Servicios móviles

| ZUMO-API-VERSION | Valor de MS\_SkipVersionCheck | Response |
| ---------------- | ---------------------------- | -------- |
| Sin especificar | Cualquiera | 200 - CORRECTO |
| Cualquier valor | True | 200 - CORRECTO |
| Cualquier valor | False/Sin especificar | 400 - Solicitud incorrecta | 

## <a name="2.0.0"></a>Cliente y servidor de Aplicaciones móviles de Azure

### <a name="MobileAppsClients"></a> SDK de cliente de *Aplicaciones* móviles

La comprobación de versión se introdujo a partir de las siguientes versiones del SDK de cliente de **Aplicaciones móviles de Azure**:

| Plataforma de cliente | Versión | Valor de encabezado de versión |
| -------------------               | ------------------------  | -----------------    |
| Cliente administrado (Windows, Xamarin) | [2\.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) | 2\.0.0 |
| iOS | [3\.0.0](http://go.microsoft.com/fwlink/?LinkID=529823) | 2\.0.0 |
| Android | [3\.0.0](http://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) | 3\.0.0 |

<!-- TODO: add HTML version when released -->

### SDK de servidor de *Aplicaciones* móviles

La comprobación de versión se incluye en las siguientes versiones del SDK de servidor:

| Plataforma de servidor | SDK | Encabezado de versión aceptado |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET | [Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) | 2\.0.0 |
| Node.js | [azure-mobile-apps Versión 1.0-beta1 (o posterior)](https://www.npmjs.com/package/azure-mobile-apps) | 2\.0.0 |

### Comportamiento de back-ends de Aplicaciones móviles

| ZUMO-API-VERSION | Valor de MS\_SkipVersionCheck | Response |
| ---------------- | ---------------------------- | -------- |
| x.y.z o Null | True | 200 - CORRECTO |
| Null | False/Sin especificar | 400 - Solicitud incorrecta |
| 1\.x.y | False/Sin especificar | 400 - Solicitud incorrecta |
| 2\.0.0-2.x.y | False/Sin especificar | 200 - CORRECTO |
| 3\.0.0-3.x.y | False/Sin especificar | 400 - Solicitud incorrecta |


## Pasos siguientes

- [Migración de un servicio móvil a Servicio de aplicaciones de Azure]


[Clientes de Servicios móviles]: #MobileServicesClients
[Clientes de Aplicaciones móviles]: #MobileAppsClients


[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Migración de un servicio móvil a Servicio de aplicaciones de Azure]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Azure mobile service to App Service]: app-service-mobile-migrating-from-mobile-services.md

<!---HONumber=AcomDC_1217_2015-->
