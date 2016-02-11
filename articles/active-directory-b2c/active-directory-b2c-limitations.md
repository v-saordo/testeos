<properties
	pageTitle="Vista previa de Azure Active Directory B2C: limitaciones y restricciones | Microsoft Azure"
	description="Lista de limitaciones y restricciones con Azure Active Directory B2C"
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

# Vista previa de Azure Active Directory B2C: limitaciones y restricciones

Hay varias características y funcionalidades de Azure Active Directory (AD) B2C que no están admitidas aún durante la vista previa. Se quitarán muchas de estas limitaciones antes de que Azure AD B2C llegue a la disponibilidad, pero se recomienda que se tengan en cuenta si está compilando aplicaciones para consumidor con Azure AD B2C durante la vista previa.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Problemas durante la creación de inquilinos de Azure AD B2C

Si se encuentra con problemas durante la [creación de un inquilino de Azure AD B2C](active-directory-b2c-get-started), consulte este [artículo](active-directory-b2c-support-create-directory.md) como guía.

## Problemas de personalización de marca en el mensaje de confirmación

El correo electrónico de comprobación predeterminado contiene información de personalización de marca de "Microsoft". Lo quitaremos en el futuro. Por ahora, puede quitarlo mediante la [característica de personalización de marca corporativa](./active-directory/active-directory-add-company-branding.md).

## Compatibilidad para las aplicaciones de producción

Las aplicaciones que se integran con Azure AD B2C no se deberían publicar como aplicaciones de nivel de producción. Azure AD B2C se encuentra en vista previa pública en este momento: se pueden introducir cambios en cualquier momento y no hay ningún SLA garantizado por el servicio. No se ofrecerá soporte técnico para los incidentes que puedan producirse. Si está dispuesto a aceptar el riesgo de tomar una dependencia en un servicio que aún está en desarrollo, se le aconseja que se ponga en contacto con nosotros a través de Twitter en @AzureAD para tratar el ámbito de su aplicación o servicio.

## Restricciones en las aplicaciones

Los siguientes tipos de aplicaciones no se admiten actualmente en la vista previa de Azure AD B2C. Para obtener una descripción de los tipos de aplicaciones admitidos, consulte este [artículo](active-directory-b2c-apps.md).

### Single Page Application (Javascript)

Muchas aplicaciones modernas tienen una Single Page Application (SPA) escrita en front-end, principalmente en Javascript, que usa a menudo marcos SPA como AngularJS, Ember.js, Durandal, etc. Este flujo no está disponible todavía en la vista previa de Azure AD B2C.

### Aplicaciones daemon/del lado servidor

Las aplicaciones que contienen procesos de larga duración o que funcionan sin la presencia de un usuario también necesitan un modo de acceder a los recursos protegidos, como las API web. Estas aplicaciones pueden autenticar y obtener tokens con la identidad de la aplicación (en lugar de una identidad delegada del consumidor) mediante el [flujo de credenciales de cliente de OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-client-credentials-grant-flow). Este flujo aún no está disponible en la vista previa de Azure AD B2C, lo cual quiere decir que las aplicaciones solo pueden obtener tokens después de que se produzca un flujo de inicio de sesión de consumidor interactivo.

### API web independiente

En la vista previa de Azure AD B2C, tiene la capacidad de [crear una API web que se protege mediante tokens de OAuth 2.0](active-directory-b2c-apps.md#web-apis). Sin embargo, esa API web sólo podrá recibir tokens de un cliente que comparta el mismo id. de aplicación. No se admite la creación de una API web a la que se obtiene acceso desde varios clientes diferentes.

### Cadenas de la API web (en nombre de)

Muchas arquitecturas incluyen una API web que necesita llamar a otra API web de nivel inferior, ambas protegidas mediante Azure AD B2C. Este escenario es común en los clientes nativos que tienen una API web back-end, que, a su vez, llama a un servicio de Microsoft Online, como la API de Azure AD Graph.

Este escenario de API web encadenadas puede admitirse mediante la concesión de credenciales de portador Jwt de OAuth 2.0, también conocido como flujo "en nombre de". Sin embargo, el flujo "en nombre de" no está implementado actualmente en la vista previa de Azure AD B2C.

## Restricción en bibliotecas y SDK

No todos los lenguajes y plataformas tienen bibliotecas que admitan la vista previa de Azure AD B2C. El conjunto de bibliotecas de autenticación se limita actualmente a. NET, iOS, Android y NodeJS. Los tutoriales de inicio rápido correspondientes para cada uno de ellos están disponibles en nuestra sección [Introducción](active-directory-b2c-overview.md#getting-started).

Si desea integrar una aplicación en la vista previa de Azure AD B2C con otro lenguaje o plataforma, consulte la [referencia de protocolo de OAuth 2.0 y OpenID Connect](active-directory-b2c-reference-protocols.md), que le enseñará cómo construir los mensajes HTTP necesarios para comunicarse con el servicio de Azure AD B2C.

## Restricción en los protocolos

La vista previa de Azure AD B2C admite OpenID Connect y OAuth 2.0. Sin embargo, no se han implementado todas las características y capacidades de cada protocolo. Para comprender mejor el alcance de la funcionalidad del protocolo compatible con la vista previa de Azure AD B2C, lea nuestra [referencia de protocolo de OpenID Connect y OAuth 2.0](active-directory-b2c-reference-protocols.md). Ahora existe compatibilidad con los protocolos SAML y WS-Fed.

## Restricción de los tokens

Muchos de los tokens emitidos por la vista previa de Azure AD B2C se implementan como tokens web JSON o JWT. Sin embargo, no toda la información incluida en JWT (conocida como "notificaciones") es como debería ser o falta. Entre los ejemplos se incluyen las notificaciones "sub" y "preferred\_username". Debería esperar que las cosas cambien bastante durante la vista previa. Para comprender mejor los tokens emitidos actualmente por el servicio de Azure AD B2C, lea nuestra [referencia de token](active-directory-b2c-tokens.md).

## Problemas con Administración de usuarios en el Portal de Azure clásico

Se puede acceder a las características B2C desde el Portal de Azure. Sin embargo, puede usar el Portal de Azure clásico para obtener acceso a otras características de inquilino, como la administración de usuarios. Actualmente hay un par de problemas conocidos con la administración de usuarios (la pestaña **Usuarios**) en el Portal de Azure clásico.

- Para un usuario de cuenta local (es decir, un consumidor que se registra con una dirección de correo electrónico y una contraseña o un nombre de usuario y una contraseña), el campo **Nombre de usuario** no se corresponde con el identificador de inicio de sesión (dirección de correo electrónico o nombre de usuario) usado durante el registro. El motivo es que el campo que se muestra en el Portal de Azure clásico es en realidad el nombre principal del usuario (UPN), que no se usa en escenarios B2C. Para ver el identificador de inicio de sesión de la cuenta local, busque el objeto de usuario en el [Explorador de gráficos](https://graphexplorer.cloudapp.net/). Encontrará el mismo problema con un usuario de cuenta social (es decir, un consumidor que se registre a Facebook, Google +, etc.), pero en ese caso, no hay ningún identificador de inicio de sesión del que hablar.

    ![Cuenta local: UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- Para un usuario de cuenta local, no podrá editar ninguno de los campos y guardará los cambios en la pestaña **Perfiles**. Lo corregiremos pronto.

## Problemas con el restablecimiento de contraseña iniciado por el administrador en el Portal de Azure clásico

Si restablece la contraseña para un consumidor basado en una cuenta local en el Portal de Azure clásico (el comando **Restablecer contraseña** de la pestaña **Usuarios**), ese consumidor no podrá cambiar su contraseña en el siguiente inicio de sesión y será bloqueado en sus aplicaciones. Estamos trabajando para corregir este problema. Para solucionar este problema, use la [API de Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md) para restablecer la contraseña del consumidor.

## Restricción en la eliminación de inquilinos de Azure AD B2C

No podrá eliminar un inquilino de Azure AD B2C en el Portal de Azure clásico.

## Problemas con la comprobación de un dominio en el Portal de Azure clásico

Actualmente no se puede comprobar un dominio correctamente en el [Portal de Azure clásico](https://manage.windowsazure.com/). Estamos trabajando en una solución.

## Mensajes de advertencia en el Portal de Azure

Cuando acceda a la hoja de configuración B2C en el Portal de Azure, verá un mensaje de advertencia en Notificaciones (en la esquina superior derecha) que dice: "No tiene ninguna suscripción en el directorio <B2CTenantName>. Tiene otros directorios a los que puede cambiar", donde <B2CTenantName> es el nombre del inquilino de B2C. Puede omitir este mensaje de forma segura y seguir accediendo a sus características B2C. Estamos trabajando con el equipo del Portal de Azure en una solución para este problema.

<!---HONumber=AcomDC_0107_2016-->