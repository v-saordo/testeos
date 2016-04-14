<properties
	pageTitle="Vista previa de Azure Active Directory B2C: Preguntas más frecuentes | Microsoft Azure"
	description="Preguntas más frecuentes acerca de Azure Active Directory B2C"
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
	ms.date="01/28/2016"
	ms.author="swkrish"/>

# Vista previa de Azure Active Directory B2C: Preguntas más frecuentes

Esta página responde a las preguntas más frecuentes acerca de la versión preliminar de Azure Active Directory (Azure AD) B2C. Siga comprobando si hay actualizaciones.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

### ¿Puedo usar las características de Azure AD B2C en mi inquilino de Azure de AD existente, basado en empleados?

Actualmente, las características de Azure AD B2C no pueden activarse en su inquilino existente de Azure AD. Se recomienda la creación de un inquilino independiente para usar características de Azure AD B2C a fin de administrar los consumidores.

### ¿Puedo usar Azure AD B2C para proporcionar un inicio de sesión social (Facebook y Google+) en Office 365?

Azure AD B2C no se puede usar con Microsoft Office 365. En general, no se puede usar para proporcionar autenticación en ninguna aplicación SaaS (Office 365, Salesforce, Workday, etc.). Proporciona administración de identidades y accesos únicamente para aplicaciones móviles y web orientadas al consumidor, y no es aplicable en situaciones relacionadas con empleados o socios.

### ¿Qué son las cuentas locales en Azure AD B2C? ¿En qué se distinguen de las cuentas de trabajo o educativas en Azure AD?

En un inquilino de Azure AD, todos los usuarios del inquilino (excepto los usuarios con cuentas Microsoft existentes) inician sesión con una dirección de correo electrónico de formato `<xyz>@<tenant domain>`, donde `<tenant domain>` es uno de los dominios comprobados del inquilino o el dominio `<...>.onmicrosoft.com` inicial. Este tipo de cuenta es una cuenta profesional o educativa.

En un inquilino de Azure AD B2C, la mayoría de las aplicaciones solicita al usuario que inicie sesión con cualquier dirección de correo electrónico arbitraria (por ejemplo, joe@comcast.net, bob@gmail.com, sarah@contoso.com o jim@live.com)). Este tipo de cuenta es una cuenta local. Actualmente, también se admiten nombres de usuario arbitrarios (cadenas simples) como cuentas locales (por ejemplo, joe, bob, sarah o jim). Puede elegir uno de estos dos tipos de cuentas locales en el servicio de Azure AD B2C.

### ¿Qué proveedores de identidades sociales se admiten ahora? ¿Cuáles se prevén que se van a admitir en el futuro?

Actualmente, se admiten Facebook, Google+, LinkedIn y Amazon. Agregaremos compatibilidad con otros proveedores de identidades sociales conocidos en función de la demanda del cliente.

### ¿Puedo configurar ámbitos para recopilar más información acerca de los consumidores de distintos proveedores de identidades sociales?

No, pero esta característica está en nuestro mapa de ruta. Los ámbitos predeterminados usados para el conjunto de proveedores de identidades sociales admitidos son:

- Facebook: correo electrónico
- Google+: correo electrónico
- Amazon: perfil
- LinkedIn: r\_emailaddress r\_basicprofile

### ¿Tiene mi aplicación que ejecutarse en Azure para que funcione con Azure AD B2C?

No, puede hospedar la aplicación en cualquier lugar (en la nube o de forma local). Todo lo que necesita para interactuar con Azure AD B2C es la capacidad de enviar y recibir solicitudes HTTP en puntos de conexión de acceso público.

### Tengo varios inquilinos de Azure AD B2C ¿Cómo puedo administrarlos en el Portal de Azure?

Cada inquilino de Azure AD B2C tiene su propia hoja de características B2C en el Portal de Azure. Consulte [Vista previa de Azure Active Directory B2C: Registro de la aplicación](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) para aprender a navegar a la hoja de características B2C de un inquilino determinado en el Portal de Azure. El cambio entre directorios de Azure AD B2C en el Portal de Azure no mantendrá abierta la hoja de características B2C en la mayoría de los exploradores.

### ¿Cómo puedo personalizar los mensajes de correo electrónico de comprobación (el contenido y el campo "De:") enviados por Azure AD B2C?

Utilice la [característica de personalización de marca de empresa](../active-directory/active-directory-add-company-branding.md) para personalizar el contenido de los mensajes de correo electrónico de comprobación. El campo "De:" puede cambiarse a través del soporte técnico.

### ¿Cómo puedo migrar mis nombres de usuario, contraseñas y perfiles existentes desde la base de datos a Azure AD B2C?

Puede usar la API Graph de Azure AD para escribir la herramienta de migración. Consulte el [ejemplo de API Graph](active-directory-b2c-devquickstarts-graph-dotnet.md) para obtener más información. Ofreceremos varias opciones de migración y herramientas listas para usar en el futuro.

### ¿Qué directiva de contraseñas se utiliza para las cuentas locales en Azure AD B2C?

La directiva de contraseñas de Azure AD B2C para cuentas locales se basa en la directiva para Azure AD. Azure AD B2C utiliza la seguridad de contraseña "segura" y la opción de que ninguna contraseña caduca. Lea la [Directiva de contraseñas en Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) para obtener más detalles.

### ¿Puedo usar Azure AD Connect para migrar identidades de consumidores almacenadas en mi entorno Active Directory local a Azure AD B2C?

No, Azure AD Connect no está diseñado para funcionar con Azure AD B2C. Ofreceremos varias opciones de migración y herramientas listas para usar en el futuro.

### ¿Funciona Azure AD B2C con sistemas CRM, como Microsoft Dynamics?

Actualmente, no. La integración de estos sistemas está en nuestra hoja de ruta.

### ¿Funciona Azure AD B2C con SharePoint local 2016 o una versión anterior?

Actualmente, no. Azure AD B2C no tiene compatibilidad con tokens SAML 1.1 que los portales y las aplicaciones de comercio electrónico incorporan según las necesidades de la instancia de SharePoint local. Tenga en cuenta que Azure AD B2C no está pensado para situaciones de uso compartido de socios externos de SharePoint; consulte en su lugar [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx).

### ¿Debo utilizar Azure AD B2C o B2B para administrar identidades externas?

Lea este artículo sobre [identidades externas](../active-directory/active-directory-b2b-compare-external-identities.md) para obtener más información acerca de cómo aplicar las características apropiadas a los escenarios de identidades externas.

### ¿Qué características de auditoría e informes proporciona Azure AD B2C? ¿Son las mismas que en Azure AD Premium?

No, Azure AD B2C no admite el mismo conjunto de informes que Azure AD Premium. Azure AD B2C lanzará API básicas de auditoría e informes pronto.

### ¿Puedo localizar la interfaz de usuario de páginas servidas por Azure AD B2C? ¿Qué idiomas se admiten?

Actualmente, Azure AD B2C está optimizado solo para inglés. Tenemos previsto implementar características de localización tan pronto como sea posible.

### ¿Puedo usar mis propias direcciones URL en las páginas de registro y de inicio que proporciona Azure AD B2C? Por ejemplo, ¿puedo cambiar las direcciones URL de login.microsoftonline.com a login.contoso.com?

Actualmente, no. Esta característica está en nuestro mapa de ruta. Tenga en cuenta también que verificar su dominio en la pestaña **Dominios** de su inquilino en el Portal de Azure clásico no servirá para tal fin.

### ¿Puedo obtener Azure AD B2C como parte de Enterprise Mobility Suite?

No, Azure AD B2C es un servicio de Azure de pago por uso y no forma parte de Enterprise Mobility Suite.

### ¿Cómo puedo informar sobre problemas con Azure AD B2C?

Vea [Presentación de solicitudes de soporte técnico para Azure Active Directory B2C](active-directory-b2c-support.md).

### ¿Cuándo estará disponible con carácter general Azure AD B2C?

No podemos proporcionar información sobre la fecha en la que estará disponible con carácter general de momento.

## Más información

Es posible que desee ver las [limitaciones y restricciones de la versión preliminar](active-directory-b2c-limitations.md) actual.

<!---HONumber=AcomDC_0224_2016-->