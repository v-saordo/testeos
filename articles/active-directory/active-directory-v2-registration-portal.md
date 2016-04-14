<properties
	pageTitle="Temas de Ayuda del portal de registro de aplicaciones | Microsoft Azure"
	description="Descripción de las distintas características del portal de registro de aplicaciones de Microsoft."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# Referencia del registro de aplicaciones
Este documento proporciona el contexto y las descripciones de las distintas características que se encuentran en el portal de registro de aplicaciones de Microsoft [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com).

## Mis aplicaciones
Esta lista contiene todas las aplicaciones que se registran para su uso con el punto de conexión v2.0 de Azure AD. Estas aplicaciones tienen la capacidad de permitir el inicio de sesión de los usuarios tanto con cuentas personales de Microsoft como con cuentas profesionales/educativas de Azure Active Directory. Para obtener más información sobre el punto de conexión v2.0 de Azure AD, consulte nuestra [información general sobre v2.0](active-directory-appmodel-v2-overview.md). Estas aplicaciones también se pueden usar para la integración con el punto de conexión de autenticación de la cuenta Microsoft, `https://login.live.com`.

## Aplicaciones de SDK de Live
Esta lista contiene todas las aplicaciones registradas para su uso únicamente con la cuenta Microsoft. No están habilitadas en absoluto para su uso con Azure Active Directory. Aquí es donde encontrará todas las aplicaciones que se registraron previamente en el portal de desarrolladores de MSA en `https://account.live.com/developers/applications`. Todas las funciones que realizaba anteriormente en `https://account.live.com/developers/applications` ahora se pueden realizar en este nuevo portal, `https://apps.dev.microsoft.com`. Si tiene alguna pregunta acerca de las aplicaciones de la cuenta Microsoft, póngase en contacto con nosotros.

## Secretos de aplicación
Los secretos de aplicación son credenciales que permiten que la aplicación realice una [autenticación de cliente](http://tools.ietf.org/html/rfc6749#section-2.3) confiable con Azure AD. En OAuth y OpenID Connect, los secretos de aplicación se conocen comúnmente como `client_secret`. En el protocolo v2.0, cualquier aplicación que reciba un token de seguridad en una ubicación direccionable web (mediante un esquema `https`) debe usar un secreto de aplicación para identificarse en Azure AD en el momento del canje de ese token de seguridad. Además, se prohibirá a cualquier cliente nativo que reciba tokens en un dispositivo el uso de un secreto de aplicación para realizar la autenticación de cliente, a fin de evitar el almacenamiento de secretos en entornos no seguros.

Cada aplicación puede contener dos secretos de aplicación válidos en un momento determinado. Al mantener dos secretos, tiene la posibilidad de realizar una sustitución de claves periódica en todo el entorno de la aplicación. Una vez que haya migrado la totalidad de la aplicación a un nuevo secreto, podrá eliminar el antiguo secreto y aprovisionar uno nuevo.

En este momento, solo se permiten dos tipos de secretos de aplicación en el portal de registro de aplicaciones. Al elegir **Generar nueva contraseña** se generará y almacenará un secreto compartido en el almacén de datos correspondiente, que podrá utilizar en su aplicación. Al elegir **Generar nuevo par de claves** se creará un nuevo par de claves pública y privada que puede descargar y usar para la autenticación de cliente en Azure AD.

## Perfil
La sección de perfil del portal de registro de aplicaciones se puede utilizar para personalizar la página de inicio de sesión de la aplicación. En este momento puede modificar el logotipo de la aplicación de la página de inicio, la dirección URL de los términos de servicio y la declaración de privacidad. El logotipo debe ser una imagen transparente de 48 x 48 o 50 x 50 píxeles en un archivo de formato GIF, PNG o JPEG que tenga un tamaño máximo de 15 KB. ¡Pruebe a cambiar los valores y ver la página de inicio de sesión que resulta!

## Compatibilidad con SDK de Live
Cuando habilita la "Compatibilidad con SDK de Live", los secretos de la aplicación que cree se aprovisionarán en los almacenes de datos tanto de Azure AD como de la cuenta Microsoft. Esto permitirá a la aplicación integrarse directamente con el servicio de la cuenta Microsoft (login.live.com). Si desea compilar una aplicación usando directamente la cuenta Microsoft (en lugar de usar el punto de conexión v2.0 de Azure AD), debe asegurarse de que esté habilitada la compatibilidad con SDK de Live.

Si deshabilita la compatibilidad con SDK de Live, se asegurará de que el secreto de la aplicación solo se escriba en el almacén de datos de Azure AD. El almacén de datos de Azure AD incorpora las normas empresariales que le que permiten satisfacer ciertas regulaciones, como el cumplimiento de FISMA. Si habilita la compatibilidad con el SDK de Live, es posible que la aplicación no pueda lograr el cumplimiento con algunas de estos regulaciones.

Si solo piensa usar el punto de conexión v2.0 de Azure AD, puede deshabilitar sin riesgos la compatibilidad del SDK de Live.

<!---HONumber=AcomDC_0224_2016-->