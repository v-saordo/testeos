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
	ms.date="01/28/2016"
	ms.author="swkrish"/>

# Versión preliminar de Azure Active Directory B2C: marco de directiva extensible

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Conceptos básicos

El marco de directiva extensible de Azure Active Directory (Azure AD) B2C es la fortaleza esencial del servicio. Las directivas describen totalmente las experiencias de identidad del consumidor como el registro, el inicio de sesión y la edición de perfil. Por ejemplo, una directiva de registro le permite controlar comportamientos configurando los siguientes valores:

- Tipos de cuenta (cuentas sociales como Facebook, o cuentas locales como la dirección de correo electrónico) que los consumidores pueden usar para registrarse en la aplicación.
- Atributos (por ejemplo, nombre, código postal, número de calzado, etc.) que se recopilarán del consumidor durante el registro.
- Uso de Multi-Factor Authentication.
- La apariencia de todas las páginas de registro.
- Información (que se manifiesta como notificaciones en un token) que la aplicación recibe cuando se completa la ejecución de la directiva.

Puede crear varias directivas de diferentes tipos en su inquilino y usarlas en sus aplicaciones según sea necesario. Las directivas se pueden volver a usar en todas las aplicaciones. Esto permite a los desarrolladores definir y modificar experiencias de identidad de consumidor con cambios mínimos o ningún cambio en su código.

Las directivas están disponibles para usarlas mediante una interfaz de usuario sencilla. Su aplicación desencadena una directiva mediante una solicitud de autenticación HTTP estándar (pasando un parámetro de directiva en la solicitud) y recibe un token personalizado como respuesta. Por ejemplo, la única diferencia entre las solicitudes que invocan una directiva de registro y las que invocan una directiva de inicio de sesión es el nombre de la directiva que se usa en el parámetro de cadena de consulta "p":

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

Para obtener más detalles sobre el marco de directivas, consulte esta [entrada de blog](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx).

## Creación de una directiva de registro

Para habilitar el registro en su aplicación, deberá crear una directiva de registro. Esta directiva describe las experiencias que tendrán los consumidores durante el registro y el contenido de los tokens que recibirá la aplicación en inicios de sesión completados correctamente.

1. [Siga estos pasos para desplazarse hasta la hoja de características B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **Directivas de registro**.
3. Haga clic en **+ Agregar** en la parte superior de la hoja.
4. El **Nombre** determina el nombre de la directiva de registro usado por su aplicación. Por ejemplo, escriba "SiUp".
5. Haga clic en **Proveedores de identidades** y seleccione "Dirección de correo electrónico". También puede seleccionar proveedores de identidades sociales, si ya se han configurado. Haga clic en **OK**.
6. Haga clic en **Atributos de registro**. Aquí elige atributos que quiere recopilar del consumidor durante el registro. Por ejemplo, seleccione "Ciudad o región", "Nombre para mostrar" y "Código postal". Haga clic en **OK**.
7. Haga clic en **Notificaciones de aplicación**. Aquí puede elegir las notificaciones que quiere que se devuelvan en los tokens enviados de vuelta a su aplicación después de una experiencia de registro correcta. Por ejemplo, seleccione "Nombre para mostrar", "Proveedor de identidades", "Código Postal", "El usuario es nuevo" e "Id. de objeto del usuario".
8. Haga clic en **Crear**. Tenga en cuenta que la directiva que se acaba de crear aparece como "**B2C\_1\_SiUp**" (el fragmento **B2C\_1\_** se agrega automáticamente) en la hoja **Directivas de registro**.
9. Para abrir la directiva, haga clic en "**B2C\_1\_SiUp**".
10. Seleccione "Aplicación Contoso B2C" en el menú desplegable **Aplicaciones** y `https://localhost:44321/` en el menú desplegable **Dirección URL de respuesta/URI de redireccionamiento**.
11. Haga clic en **Ejecutar ahora**. Se abrirá una nueva pestaña del explorador y podrá recorrer la experiencia del consumidor de registro en su aplicación.

    > [AZURE.NOTE]
    Se tarda hasta un minuto en que la creación de directivas y las actualizaciones surtan efecto.

## Uso de una directiva de inicio de sesión

Para habilitar el inicio de sesión en la aplicación, deberá crear una directiva de inicio de sesión. Esta directiva describe las experiencias que tendrán los consumidores durante el inicio de sesión y el contenido de los tokens que recibirá la aplicación en inicios de sesión correctos.

1. [Siga estos pasos para desplazarse hasta la hoja de características B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **Directivas de inicio de sesión**.
3. Haga clic en **+Agregar** en la parte superior de la hoja.
4. El **Nombre** determina el nombre de la directiva de inicio de sesión usada por su aplicación. Por ejemplo, escriba "SiIn".
5. Haga clic en **Proveedores de identidades** y seleccione "Dirección de correo electrónico". También puede seleccionar proveedores de identidades sociales, si ya se han configurado. Haga clic en **OK**.
6. Haga clic en **Notificaciones de aplicación**. Aquí puede elegir las notificaciones que quiere que se devuelvan en los tokens enviados de vuelta a su aplicación después de una experiencia de inicio de sesión correcta. Por ejemplo, seleccione "Nombre para mostrar", "Proveedor de identidades", "Código Postal" e "Id. de objeto del usuario". Haga clic en **OK**.
7. Haga clic en **Crear**. Tenga en cuenta que la directiva que se acaba de crear aparece como "**B2C\_1\_SiIn**" (el fragmento **B2C\_1\_** se agrega automáticamente) en la hoja **Directivas de inicio de sesión**.
8. Para abrir la directiva, haga clic en "**B2C\_1\_SiIn**".
9. Seleccione "Aplicación Contoso B2C" en el menú desplegable **Aplicaciones** y `https://localhost:44321/` en el menú desplegable **Dirección URL de respuesta/URI de redireccionamiento**.
10. Haga clic en **Ejecutar ahora**. Se abrirá una nueva pestaña del explorador y podrá recorrer la experiencia del consumidor de inicio de sesión en su aplicación.

    > [AZURE.NOTE]
    Se tarda hasta un minuto en que la creación de directivas y las actualizaciones surtan efecto.

## Creación de una directiva de edición de perfil

Para habilitar la edición de perfiles en su aplicación, deberá crear una directiva de edición de perfiles. Esta directiva describe las experiencias que tendrán los consumidores durante la edición de perfiles y el contenido de los tokens que recibirá la aplicación al finalizar correctamente.

1. [Siga estos pasos para desplazarse hasta la hoja de características B2C en el Portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Haga clic en **Directivas de edición de perfiles**.
3. Haga clic en **+ Agregar** en la parte superior de la hoja.
4. El **Nombre** determina el nombre de la directiva de edición de perfiles usada por su aplicación. Por ejemplo, escriba "SiPe".
5. Haga clic en **Proveedores de identidades** y seleccione "Dirección de correo electrónico". También puede seleccionar proveedores de identidades sociales, si ya se han configurado. Haga clic en **OK**.
6. Haga clic en **Atributos de perfil**. Aquí se eligen los atributos que el consumidor puede ver y editar. Por ejemplo, seleccione "Ciudad o región", "Nombre para mostrar" y "Código postal". Haga clic en **Aceptar**.
7. Haga clic en **Notificaciones de aplicación**. Aquí puede elegir las notificaciones que quiere que se devuelvan en los tokens a su aplicación después de una experiencia de edición de perfiles correcta. Por ejemplo, seleccione "Nombre para mostrar" y "Código postal".
8. Haga clic en **Crear**. Tenga en cuenta que la directiva que se acaba de crear aparece como "**B2C\_1\_SiPe**" (el fragmento **B2C\_1\_** se agrega automáticamente) en la hoja **Directivas de edición de perfiles**.
9. Para abrir la directiva, haga clic en "**B2C\_1\_SiPe**".
10. Seleccione "Aplicación Contoso B2C" en el menú desplegable **Aplicaciones** y `https://localhost:44321/` en el menú desplegable **Dirección URL de respuesta/URI de redireccionamiento**.
11. Haga clic en **Ejecutar ahora**. Se abrirá una nueva pestaña del explorador y podrá recorrer la experiencia del consumidor de edición de perfiles en su aplicación.

    > [AZURE.NOTE]
    Se tarda hasta un minuto en que la creación de directivas y las actualizaciones surtan efecto.

<!---HONumber=AcomDC_0224_2016-->