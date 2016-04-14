<properties
   pageTitle="Administración de identidades para aplicaciones multiinquilino | Microsoft Azure"
   description="Procedimientos recomendados para autenticación, autorización y administración de identidades en aplicaciones multiinquilino."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Administración de identidades para aplicaciones multiinquilino en Microsoft Azure

Esta serie describe los procedimientos recomendados para la arquitectura multiinquilino durante el uso de Azure AD para la administración de la autenticación y de las identidades.

Cuando está creando una aplicación multiinquilino, uno de los primeros desafíos es administrar las identidades de usuario, ya que ahora todos los usuarios pertenecen a un inquilino. Por ejemplo:

- Los usuarios inician sesión con las credenciales de la organización.
- Los usuarios deben tener acceso a datos de su organización, pero no a los datos que pertenecen a otros inquilinos.
- Una organización puede suscribirse a la aplicación y, a continuación, asignar roles de aplicación a sus miembros.

Azure Active Directory (Azure AD) tiene algunas características excelentes que admiten todos estos escenarios.

Para acompañar esta serie de artículos, también hemos creado una completa [implementación de extremo a extremo][tailspin] de una aplicación multiinquilino. Los artículos reflejan lo que hemos aprendido en el proceso de creación de la aplicación. Para empezar a trabajar con la aplicación, consulte [Running the Surveys application (Ejecución de la aplicación Surveys)](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md).


## Tabla de contenido

- [Introducción](guidance-multitenant-identity-intro.md)
- [Acerca de la aplicación Tailspin Surveys](guidance-multitenant-identity-tailspin.md)
- [Autenticación con Azure AD](guidance-multitenant-identity-authenticate.md)
- [Trabajo con identidades basadas en notificaciones](guidance-multitenant-identity-claims.md)
- [Suscripción e incorporación de inquilinos](guidance-multitenant-identity-signup.md)
- [Definición y administración de los roles de aplicación](guidance-multitenant-identity-app-roles.md)
- [Autorización](guidance-multitenant-identity-authorize.md)
- [Protección de una API web de back-end](guidance-multitenant-identity-web-api.md)
- [Almacenamiento en caché de tokens de acceso de OAuth2](guidance-multitenant-identity-token-cache.md)
- [Federación con el servicio AD FS de un cliente](guidance-multitenant-identity-adfs.md)
- [Uso de aserción de cliente para obtener tokens de acceso de Azure AD](guidance-multitenant-identity-client-assertion.md)
- [Uso de Almacén de claves para proteger los secretos de la aplicación](guidance-multitenant-identity-keyvault.md)

[tailspin]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->