<properties
	pageTitle="Vista previa de Azure Active Directory B2C: Información general | Microsoft Azure"
	description="Desarrollo de aplicaciones orientadas al consumidor con Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="swkrish"/>

# Vista previa de Azure Active Directory B2C: Registro e inicio de sesión de consumidores en las aplicaciones

**Azure Active Directory B2C** es una solución completa de administración de identidades en la nube, destinada a aplicaciones móviles y web orientadas al consumidor. Se trata de un servicio global de alta disponibilidad que se puede escalar a cientos de millones de identidades de consumidor. Basado en una plataforma segura de nivel empresarial, Azure Active Directory B2C protege las aplicaciones, la empresa y los consumidores.

En el pasado, los desarrolladores de aplicaciones que deseaban registrar e iniciar la sesión de los consumidores en sus aplicaciones tenían que escribir su propio código. Y usaban bases de datos o sistemas locales para almacenar nombres de usuario y contraseñas. Azure Active Directory B2C ofrece a los desarrolladores una manera mejor de integrar la administración de identidades de consumidor en sus aplicaciones gracias a la ayuda de una plataforma segura basada en estándares y un amplio conjunto de directivas extensibles. El uso de Azure Active Directory B2C permite a los consumidores registrarse en las aplicaciones con sus cuentas de redes sociales existentes (Facebook, Google, Amazon, LinkedIn) o mediante la creación de nuevas credenciales (dirección de correo electrónico y contraseña o nombre de usuario y contraseña); a esto último lo llamamos "cuentas locales".

Azure Active Directory B2C se encuentra en versión de vista previa. Durante este tiempo, estamos ansiosos por conocer su opinión y experiencia mientras lo prueba. En función de estos comentarios, podemos hacer cambios importantes con el fin de mejorar el servicio. Durante este período, no debe lanzar ninguna aplicación de producción usando la vista previa. Envíenos sus ideas mediante la sección de [opiniones de los usuarios](https://feedback.azure.com/forums/169401-azure-active-directory/).

## Introducción

Para crear una aplicación que acepte registros e inicios de sesión de consumidores, primero deberá registrarla en un inquilino de Azure Active Directory B2C. Obtenga su propio inquilino siguiendo los pasos descritos en este [artículo](active-directory-b2c-get-started.md).

Puede escribir la aplicación para el servicio Azure Active Directory B2C de dos maneras: puede elegir enviar mensajes de protocolo directamente con [OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-authorization-code-flow) o [Open ID Connect](active-directory-b2c-reference-protocols.md#openid-connect-sign-in-flow), o bien usar nuestras bibliotecas para que el trabajo se realice automáticamente (elija a continuación su plataforma favorita y comience).

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## Novedades

Vuelva a esta sección a menudo para conocer los cambios futuros en la versión de vista previa de Azure Active Directory B2C. También enviaremos tweets acerca de las actualizaciones mediante @AzureAD.

- Obtenga información sobre nuestro [marco extensible de directivas](active-directory-b2c-reference-policies.md) y sobre los tipos de directivas que puede crear y usar en sus aplicaciones.
- [Limitaciones y restricciones de la versión preliminar](active-directory-b2c-limitations.md) actual.

## Procedimientos

Aprenda a usar determinadas características de vista previa de Azure Active Directory B2C:

- Configure las cuentas de [Facebook](active-directory-b2c-setup-fb-app.md), [Google+](active-directory-b2c-setup-goog-app.md), [Microsoft Account](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md) y [LinkedIn](active-directory-b2c-setup-li-app.md) para usarlas en las aplicaciones orientadas al consumidor.
- [Uso de atributos personalizados para recopilar información sobre los consumidores](active-directory-b2c-reference-custom-attr.md).
- [Habilitación de Multi-Factor Authentication en las aplicaciones orientadas al consumidor](active-directory-b2c-reference-mfa.md).
- [Configuración del autoservicio de restablecimiento de contraseña para los consumidores](active-directory-b2c-reference-sspr.md).
- [Personalización de la apariencia de las páginas de registro, inicio de sesión, etc., orientadas al consumidor](active-directory-b2c-reference-ui-customization.md) a las que presta servicio Azure Active Directory B2C.
- [Uso de la API Graph de Azure Active Directory para crear, leer, actualizar y eliminar consumidores mediante programación](active-directory-b2c-devquickstarts-graph-dotnet.md) en el directorio de Azure Active Directory B2C.

## Referencia

Estos vínculos serán útiles para explorar el servicio en profundidad:

- Consulte la [información de precios de Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
- Obtenga ayuda acerca del desbordamiento de pila con las etiquetas [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) o [adal](http://stackoverflow.com/questions/tagged/adal).
- Envíenos sus ideas sobre la versión preliminar con [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/); ¡queremos conocerlas! Use la frase "AzureADB2C:" en el título de la entrada para que podamos encontrarla.
- Azure Active Directory B2C es compatible con los protocolos estándar del sector, OpenID Connect y OAuth 2.0, mediante el modelo de registro de aplicaciones que llamamos "Modelo de aplicaciones v2.0".
  - [Referencia de protocolos del modelo de aplicaciones v2.0](active-directory-b2c-reference-protocols.md)
  - [Referencia de tokens del modelo de aplicaciones v2.0](active-directory-b2c-reference-tokens.md)
- [P+F sobre Azure Active Directory B2C](active-directory-b2c-faqs.md)
- [Presentación de solicitudes de soporte técnico con respecto a Azure Active Directory B2C](active-directory-b2c-support.md)

<!---HONumber=AcomDC_0128_2016-->