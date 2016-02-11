<properties
	pageTitle="Vista previa de Azure Active Directory B2C: herramienta auxiliar de personalización de la interfaz de usuario de página | Microsoft Azure"
	description="Herramienta auxiliar que se usa para mostrar la característica de personalización de la interfaz de usuario (IU) de página en Azure Active Directory B2C"
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
	ms.date="12/22/2015"
	ms.author="swkrish"/>

# Vista previa de Azure Active Directory B2C: una herramienta auxiliar que se usa para mostrar la característica de personalización de la interfaz de usuario (IU) de página

Este artículo es un complemento del [artículo principal sobre personalización de la interfaz de usuario](active-directory-b2c-reference-ui-customization.md) en Azure Active Directory B2C. Los pasos siguientes describen cómo ejecutar la característica de personalización de la interfaz de usuario de página mediante contenido HTML y CSS de ejemplo que le hemos proporcionado.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Obtención de un inquilino de Azure AD B2C

Antes de personalizar nada, deberá [obtener un inquilino de Azure AD B2C](active-directory-b2c-get-started.md) si todavía no tiene uno.

## Creación de una directiva de registro

El contenido de ejemplo que hemos proporcionado personaliza dos páginas de una [directiva de registro](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy): la [Página de selección de IDP](active-directory-b2c-reference-ui-customization.md#identity-provider-selection-page) y la [Página de suscripción de la cuenta local](active-directory-b2c-reference-ui-customization.md#local-account-sign-up-page). Al [crear la directiva de registro](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy), agregue Cuenta local (dirección de correo electrónico), Facebook y Google+ como **proveedores de identidades**. Estos son los únicos IDP que aceptará nuestro contenido HTML de ejemplo.

## Registro de una aplicación

Deberá [registrar la aplicación](active-directory-b2c-app-registration.md) en el inquilino B2C que puede usarse para ejecutar la directiva. Después de registrar la aplicación, tiene algunas opciones que puede usar para ejecutar la directiva de registro:

- Compilar una de las aplicaciones de inicio rápido de Azure AD B2C que aparecen [aquí](active-directory-b2c-overview.md#getting-started).
- Usar la aplicación de [área de juegos de Azure AD B2C](https://aadb2cplayground.azurewebsites.net) previamente compilada. Si decide usar el área de juegos, debe registrar una aplicación en el inquilino B2C mediante el **URI de redirección** `https://aadb2cplayground.azurewebsites.net/`
- Use el botón **Ejecutar ahora** en la directiva del [Portal de Azure](https://portal.azure.com/).

## Personalización de la directiva

Para personalizar la apariencia y comportamiento de las directivas, debe crear primero los archivos HTML y CSS usando las convenciones específicas de Azure AD B2C. Luego, puede cargar su contenido estático en una ubicación disponible públicamente para que Azure AD B2C pueda tener acceso a él. Esta ubicación podría ser su propio servidor web dedicado, el almacenamiento de blobs de Azure, la red CDN de Azure o cualquier otro proveedor de hospedaje de recursos estáticos. Los únicos requisitos son que el contenido esté disponible a través de HTTPS y se pueda tener acceso a él mediante CORS. Una vez que se ha expuesto el contenido estático en la web, puede editar la directiva para que señale a esta ubicación y presentar ese contenido a los usuarios finales. El [artículo principal sobre personalización de la interfaz de usuario](active-directory-b2c-reference-ui-customization.md) describe de forma detallada cómo funciona la característica de personalización de Azure AD B2C.

Para los fines de este tutorial, ya hemos creado algún contenido de ejemplo y lo hemos hospedado en el almacenamiento de blobs de Azure. El contenido de ejemplo es una personalización muy básica del tema de nuestra empresa artificial, "Contoso B2C". Para probarlo en su propia directiva, siga estos pasos:

1. Inicie sesión en el inquilino en el [Portal de Azure](https://portal.azure.com/) y vaya hasta la hoja de características B2C.
2. Haga clic en **Directivas de registro** y, luego, en la directiva de registro (por ejemplo, "b2c\_1\_sign\_up").
3. Haga clic en **Personalización de la interfaz de usuario de página** y, luego, en **Página de selección de proveedor de identidades**.
4. Alterne el conmutador **Usar plantilla personalizada** a **Sí**. En el campo **URI de página personalizada**, escriba `https://contosob2c.blob.core.windows.net/static/Index.html`. Haga clic en **OK**.
5. Haga clic en **Página de suscripción de cuenta local**. Alterne el conmutador **Usar plantilla personalizada** a **Sí**. En el campo **URI de página personalizada**, escriba `https://contosob2c.blob.core.windows.net/static/EmailVerification.html`. Haga clic en **Aceptar** dos veces para cerrar las hojas de personalización de la interfaz de usuario.
6. Haga clic en **Guardar**.

Ahora puede probar la directiva personalizada. Puede usar su propia aplicación o el área de juegos de AAD B2C; también puede hacer simplemente clic en el comando **Ejecutar ahora** en la hoja de directivas. Seleccione la aplicación en la lista desplegable y el URI de redirección correspondiente. Haga clic en el botón **Ejecutar ahora**. Se abrirá una nueva pestaña del explorador y podrá pasar por la experiencia del usuario de registrarse para obtener su aplicación con el nuevo contenido implementado.

## Carga del contenido de ejemplo en el almacenamiento de blobs de Azure

Si desea usar el almacenamiento de blobs de Azure para hospedar el contenido de su página, puede crear su propia cuenta de almacenamiento y usar nuestra herramienta auxiliar de B2C para cargar los archivos.

#### Crear una cuenta de almacenamiento

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **+ Nuevo** -> **Datos + almacenamiento** -> **Cuenta de almacenamiento**. Necesitará una suscripción de Azure para crear una cuenta de Almacenamiento de blobs de Azure. Puede registrarse para una evaluación gratuita [aquí](https://azure.microsoft.com/pricing/free-trial/).
3. Proporcione un **Nombre** para la cuenta de almacenamiento (por ejemplo, "contoso") y elija las selecciones adecuadas para **Plan de tarifa**, **Grupo de recursos** y **Suscripción**. Asegúrese de tener la opción **Anclar a Panel de inicio** activada. Haga clic en **Crear**.
4. Regrese al panel de inicio y haga clic en la cuenta de almacenamiento que acaba de crear.
5. En la sección **Resumen**, haga clic en **Contenedores** y luego en **+Agregar**.
6. Asigne un **Nombre** al contenedor (por ejemplo, "b2c") y seleccione **Blob** como el **Tipo de acceso**. Haga clic en **OK**.
7. El contenedor que creó aparecerá en la lista de la hoja **Blobs**. Anote la URL del contenedor, por ejemplo, debería ser similar a `https://contoso.blob.core.windows.net/b2c`. Cierre la hoja **Blobs**.
8. En la hoja de cuenta de almacenamiento, haga clic en **Claves** y anote los valores de los campos **Nombre de cuenta de almacenamiento** y **Clave de acceso principal**.

> [AZURE.NOTE]
	La **Clave de acceso principal** es una credencial de seguridad importante.

#### Descargue la herramienta auxiliar y los archivos de ejemplo.

Puede descargar la [herramienta auxiliar de almacenamiento de blobs de Azure y los archivos de ejemplo como .zip aquí](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) o clonarlos desde GitHub:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Este repositorio contiene un `sample_templates\contoso` directorio, que contiene HTML, CSS e imágenes de ejemplo. Para que estas plantillas hagan referencia a su propia cuenta de almacenamiento de blobs de Azure, debe editar los archivos HTML. Abra `Index.htnml` y `EmailValidation.html` y reemplace todas las instancias de `https://localhost` por la dirección URL del contenedor que copió en los pasos anteriores. Es necesario usar la ruta de acceso absoluta de los archivos HTML porque en este caso, el código HTML será atendido por Azure AD, bajo el dominio `https://login.microsoftonline.com`.

#### Carga de los archivos de ejemplo

En el mismo repositorio, descomprima `B2CAzureStorageClient.zip` y ejecute el archivo `B2CAzureStorageClient.exe` que hay dentro. Este programa simplemente carga todos los archivos del directorio especificado en la cuenta de almacenamiento y permite que CORS acceda a esos archivos. Si siguió los pasos anteriores, los archivos HTML y CSS apuntarán ahora a la cuenta de almacenamiento. Tenga en cuenta que el nombre de la cuenta de almacenamiento es la parte que precede a `blob.core.windows.net`, por ejemplo, `contoso`. Para comprobar que el contenido se ha cargado correctamente, intente acceder a `https://{storage-account-name}.blob.core.windows.net/{container-name}/Index.html` en un explorador. Use también [http://test-cors.org/](http://test-cors.org/) para asegurarse de que el contenido está ahora habilitado con CORS (busque XHR status: 200 en el resultado).

#### Personalización de la directiva, de nuevo

Ahora que ha cargado el contenido de ejemplo en su propia cuenta de almacenamiento, debe modificar la directiva de registro para que haga referencia a él. Repita los pasos de la sección anterior ["Personalización de la directiva"](#customize-your-policy), esta vez usando las direcciones URL de su propia cuenta de almacenamiento. Por ejemplo, la ubicación de su archivo `Index.html` sería `<url-of-your-container>/Index.html`.
        
Ahora puede usar el botón **Ejecutar ahora** o su propia aplicación para ejecutar de nuevo la directiva. El resultado debería ser casi exactamente el mismo, ya que usó el mismo HTML y CSS de ejemplo en ambos casos. Sin embargo, las directivas ahora hacen referencia a su propia instancia de almacenamiento de blobs de Azure, y es libre de editar y volver a cargar los archivos a su gusto. Para obtener más información sobre la personalización del contenido HTML y CSS, consulte el [artículo principal sobre personalización de la interfaz de usuario](active-directory-b2c-reference-ui-customization.md).

<!---HONumber=AcomDC_0128_2016-->