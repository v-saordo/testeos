<properties
	pageTitle="Vista previa de Azure Active Directory B2C: marco de directiva extensible | Microsoft Azure"
	description="Tema sobre el marco de directiva extensible de Azure Active Directory B2C y sobre cómo crear distintos tipos de directiva"
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

# Vista previa de Azure Active Directory B2C: marco de directiva extensible

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Conceptos básicos

El marco de directiva extensible de Azure Active Directory (AD) B2C es la fuerza esencial del servicio. Las directivas describen totalmente las experiencias de identidad de consumidor como registro, inicio de sesión y edición de perfil. Por ejemplo, una directiva de registro le permite controlar comportamientos configurando los siguientes valores:

- Tipos de cuenta (cuentas sociales como Facebook y Google y/o cuentas locales como dirección de correo electrónico/nombre de usuario y contraseña) que los consumidores usan para registrarse para la aplicación.
- Atributos (por ejemplo, nombre, código postal, número de calzado, etc.) que se recopilarán del consumidor durante el registro. 
- Uso de la autenticación multifactor.
- La apariencia de todas las páginas de registro.
- Información (que se manifiesta como notificaciones en un token) que la aplicación recibe cuando se completa la ejecución de la directiva.

Puede crear varias directivas de diferentes tipos en su inquilino y usarlas en sus aplicaciones según sea necesario. Las directivas se pueden volver a usar en todas las aplicaciones. Esto permite a los desarrolladores definir y modificar experiencias de identidad de consumidor con cambios mínimos o ningún cambio en su código. Seguiremos agregando más tipos de directivas enriquecidos a nuestro servicio.

Las directivas están disponibles para usarlas mediante una interfaz de usuario sencilla. Su aplicación desencadena una directiva mediante una solicitud de autenticación HTTP estándar (pasando un parámetro de directiva en la solicitud) y recibe un token personalizado como respuesta. Por ejemplo, la única diferencia entre las solicitudes que invocan una directiva de registro y una directiva de inicio de sesión es el nombre de la directiva que se usa en el parámetro de cadena de consulta "p":

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

Si está interesado en profundizar en el marco de directivas, lea esta [entrada de blog](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx).

## Cómo crear una directiva de registro

Para habilitar el registro en su aplicación, deberá crear una directiva de registro. Esta directiva describe las experiencias que tendrán los consumidores durante el registro y el contenido de los tokens que recibirá la aplicación en inicios de sesión correctos.

1. [Siga estos pasos para desplazarse hasta la hoja de características B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **Directivas de registro**.
3. Haga clic en **+ Agregar** en la parte superior de la hoja.
4. El **Nombre** determina el nombre de la directiva de registro usado por su aplicación. Por ejemplo, escriba "SiUp".
5. Haga clic en **Proveedores de identidades** y seleccione "Dirección de correo electrónico". También puede seleccionar proveedores de identidades sociales, si ya se han configurado. Haga clic en **OK**.

> [AZURE.NOTE]
> Para cuentas locales, las directivas de registro de Azure AD B2C usan contraseñas "Seguras" (se establecen en "No caduca nunca"). Consulte [Directiva de contraseñas de Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) para obtener información sobre otros valores de configuración (que actualmente no se usan en Azure AD B2C).

6. Haga clic en **Atributos de registro**. Aquí elige atributos que quiere recopilar del consumidor durante el registro. Por ejemplo, seleccione "Ciudad o región", "Nombre para mostrar" y "Código postal". Haga clic en **OK**.
7. Haga clic en **Notificaciones de aplicación**. Aquí puede elegir las notificaciones que quiere que se devuelvan en los tokens a su aplicación después de una experiencia de registro correcta. Por ejemplo, seleccione "Nombre para mostrar", "Proveedor de identidades", "Código Postal", "El usuario es nuevo" e "Id. de objeto del usuario".
8. Haga clic en **Crear**. Tenga en cuenta que la directiva que se acaba de crear aparece como "**B2C\_1\_SiUp**" (el fragmento **B2C\_1\_** está previamente pendiente automáticamente) en la hoja **Directivas de registro**.
9. Para abrir la directiva, haga clic en "**B2C\_1\_SiUp**".
10. Seleccione "Aplicación Contoso B2C" en el menú desplegable **Aplicaciones** y `https://localhost:44321/` en el menú desplegable **Dirección URL de respuesta/URI de redireccionamiento**. Haga clic en el botón **Ejecutar ahora**. Se abrirá una nueva pestaña del explorador y podrá recorrer la experiencia del usuario de registrarse para su aplicación.

    > [AZURE.NOTE]
    Se tarda hasta un minuto en que la creación de directivas y las actualizaciones surtan efecto.

## Cómo crear una directiva de inicio de sesión

Para habilitar el inicio de sesión en la aplicación, deberá crear una directiva de inicio de sesión. Esta directiva describe las experiencias que tendrán los consumidores durante el inicio de sesión y el contenido de los tokens que recibirá la aplicación en inicios de sesión correctos.

1. [Siga estos pasos para desplazarse hasta la hoja de características B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **Directivas de inicio de sesión**.
3. Haga clic en **+Agregar** en la parte superior de la hoja.
4. El **Nombre** determina el nombre de la directiva de inicio de sesión usada por su aplicación. Por ejemplo, escriba "SiIn".
5. Haga clic en **Proveedores de identidades** y seleccione "Dirección de correo electrónico". También puede seleccionar proveedores de identidades sociales, si ya se han configurado. Haga clic en **OK**.
6. Haga clic en **Notificaciones de aplicación**. Aquí puede elegir las notificaciones que quiere que se devuelvan en los tokens a su aplicación después de una experiencia de inicio de sesión correcta. Por ejemplo, seleccione "Nombre para mostrar", "Proveedor de identidades", "Código Postal" e "Id. de objeto del usuario". Haga clic en **OK**.
7. Haga clic en **Crear**. Tenga en cuenta que la directiva que se acaba de crear aparece como "**B2C\_1\_SiIn**" (el fragmento **B2C\_1\_** se agrega automáticamente) en la hoja **Directivas de inicio de sesión**.
8. Para abrir la directiva, haga clic en "**B2C\_1\_SiIn**".
9. Seleccione "Aplicación Contoso B2C" en el menú desplegable **Aplicaciones** y `https://localhost:44321/` en el menú desplegable **Dirección URL de respuesta/URI de redireccionamiento**. Haga clic en el botón **Ejecutar ahora**. Se abrirá una nueva pestaña del explorador y podrá recorrer la experiencia del usuario de iniciar sesión en su aplicación.

    > [AZURE.NOTE]
    Se tarda hasta un minuto en que la creación de directivas y las actualizaciones surtan efecto.

## Cómo crear una directiva de edición de perfil

Para habilitar la edición de perfiles en su aplicación, deberá crear una directiva de edición de perfiles. Esta directiva describe las experiencias que tendrán los consumidores durante la edición de perfiles y el contenido de los tokens que recibirá la aplicación al finalizar correctamente.

1. [Siga estos pasos para desplazarse hasta la hoja de características B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **Directivas de edición de perfiles**.
3. Haga clic en **+ Agregar** en la parte superior de la hoja.
4. El **Nombre** determina el nombre de la directiva de edición de perfiles usada por su aplicación. Por ejemplo, escriba "SiPe".
5. Haga clic en **Proveedores de identidades** y seleccione "Dirección de correo electrónico". También puede seleccionar proveedores de identidades sociales, si ya se han configurado. Haga clic en **OK**.
6. Haga clic en **Atributos de perfil**. Aquí se eligen los atributos que el consumidor puede ver y editar. Por ejemplo, seleccione "Ciudad o región", "Nombre para mostrar" y "Código postal". Haga clic en **OK**.
7. Haga clic en **Notificaciones de aplicación**. Aquí puede elegir las notificaciones que quiere que se devuelvan en los tokens a su aplicación después de una experiencia de edición de perfiles correcta. Por ejemplo, seleccione "Nombre para mostrar" y "Código postal".
8. Haga clic en **Crear**. Tenga en cuenta que la directiva que se acaba de crear aparece como "**B2C\_1\_SiPe**" (el fragmento **B2C\_1\_** está previamente pendiente automáticamente) en la hoja **Directivas de edición de perfiles**.
9. Para abrir la directiva, haga clic en "**B2C\_1\_SiPe**".
10. Seleccione "Aplicación Contoso B2C" en el menú desplegable **Aplicaciones** y `https://localhost:44321/` en el menú desplegable **Dirección URL de respuesta/URI de redireccionamiento**. Haga clic en el botón **Ejecutar ahora**. Se abrirá una nueva pestaña del explorador y podrá recorrer la experiencia del consumidor de edición de perfiles en su aplicación.

    > [AZURE.NOTE]
    Se tarda hasta un minuto en que la creación de directivas y las actualizaciones surtan efecto.

<!------HONumber=AcomDC_0107_2016-->
