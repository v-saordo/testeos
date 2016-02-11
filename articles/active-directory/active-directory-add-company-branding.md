<properties
	pageTitle="Incorporación de la información de marca de empresa a sus páginas de inicio de sesión y panel de acceso"
	description="En este tema se explica cómo puede una organización aplicar un aspecto uniforme a los sitios web y los servicios que administran de manera que los usuarios finales no se confundan cuando necesiten utilizar dichos sitios."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/26/2016" 
	ms.author="MarkVi"/>

# Incorporación de la información de marca de empresa a sus páginas de inicio de sesión y panel de acceso

> [AZURE.NOTE]
>
- La información de marca de empresa es una característica que solo está disponible si ha actualizado a la edición Premium o Básico de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).
- Las ediciones Premium y Básico de Azure Active Directory están disponibles para los clientes de China que utilizan la instancia de Azure Active Directory en todo el mundo. Las ediciones Premium y Básico de Azure Active Directory no se admiten actualmente en el servicio de Microsoft Azure operado por 21Vianet en China. Para obtener más información, póngase en contacto con nosotros en el [foro de Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

Muchas empresas desean aplicar un aspecto uniforme a los sitios web y los servicios que administran de manera que los usuarios finales no se confundan cuando necesiten utilizar dichos sitios. Azure Active Directory ofrece esta funcionalidad, ya que permite personalizar la apariencia de las siguientes páginas web orientadas al usuario final para que incluyan el logotipo y las combinaciones de color de su empresa:

- **Página de inicio de sesión**: esta es la página a la que se redirigen los usuarios cuando inician sesión en Office 365 u otras aplicaciones basadas en Web y modernas que usan Azure AD como proveedor de identidades. La mayoría de los usuarios interactúan con esta página, ya sea para pasar por la detección del dominio Kerberos de inicio, que permite al sistema redirigir a los usuarios federados a su STS local (como AD FS), o para escribir sus credenciales.

- **Página de panel de acceso**: el panel de acceso es un portal basado en Web que permite a un usuario final con una cuenta profesional o educativa en un directorio de Azure AD ver e iniciar aplicaciones basadas en la nube a las que el administrador de Azure AD ha concedido acceso. El panel de acceso es accesible para todos los usuarios de su organización en myapps.microsoft.com.

## Personalización de la página de inicio de sesión

La página de inicio de sesión suele ser la página web usada con más frecuencia por los usuarios finales que necesitan acceso basado en un explorador a las aplicaciones y los servicios en la nube a los que su organización se ha suscrito, por lo que asegurarse de que tiene un buen aspecto resulta primordial. Si desea utilizar la experiencia de página de inicio de sesión sin marca, no tiene que hacer nada.

### ¿Cuánto tiempo se tardan en ver los cambios en la información de la marca de las páginas de inicio de sesión?

Puede transcurrir hasta una hora para que los usuarios vean los nuevos cambios efectuados en la información de la marca de la página de inicio de sesión.

### ¿Cuándo verán los usuarios una página de inicio de sesión con la información de marca?

Los usuarios verán una página de inicio de sesión con la información de marca cuando visiten un servicio con una dirección URL específica de inquilino, como https://outlook.com/**contoso**.com o https://mail.**contoso**.com (si se ha creado un CNAME).

Si visitan un servicio con direcciones URL no específicas de inquilinos (como https://mail.office365.com), verán una página de inicio de sesión sin información de marca. La página de inicio de sesión se actualizará para mostrar su información de marca una vez que los usuarios hayan introducido su identificador de usuario o hayan seleccionado un icono de usuario.

> [AZURE.NOTE]
>
- El nombre de dominio debe aparecer como "Active" en la sección **Active Directory** > **Directorio** > **Dominios** del Portal de administración de Azure donde se ha configurado la marca.
- La información de marca de la página de inicio de sesión no se traslada a la página de inicio de sesión de cliente de Microsoft. Esto significa que los usuarios que inicien sesión con una cuenta Microsoft personal (anteriormente Windows Live ID) podrán ver una lista de iconos de usuario con información de marca presentada por Azure AD, pero la información de marca de su organización no se aplicará a la página de inicio de sesión de la cuenta Microsoft.

### ¿Qué verá mi usuario final después de personalizar la página de inicio de sesión?

Si desea mostrar en su página la marca y los colores de su empresa, además de otros elementos personalizables, vea las imágenes siguientes para entender la diferencia entre las dos experiencias.

Si un usuario intenta iniciar sesión desde un equipo de escritorio, este es un ejemplo de lo que verá dicho usuario en la página de inicio de sesión de Office 365 en *antes* de la personalización:

![][1]

Y aquí está lo que vería el mismo usuario *después* de la personalización:

![][2]

Si un usuario intenta iniciar sesión desde un dispositivo móvil, este es un ejemplo de lo que verá dicho usuario en la página de inicio de sesión de Office 365 en *antes* de la personalización:

![][3]

Y aquí está lo que vería el mismo usuario *después* de la personalización:

![][4]

### ¿Qué elementos de la página puedo personalizar?

Puede personalizar los siguientes elementos de la página de inicio de sesión:

![][5]

 Elemento de la página | Ubicación en la página
	------------- | -------------
Logotipo del banner | Se muestra en la parte superior derecha de la página. Reemplaza al logotipo que se mostraría normalmente en el sitio de destino en el que los usuarios están iniciando sesión (por ejemplo, Office 365 o Azure).
Ilustración grande / Color de fondo | Se muestra a la izquierda de la página. Reemplaza a la imagen que se mostraría normalmente en el sitio de destino en el que los usuarios están iniciando sesión. El color de fondo puede mostrarse en lugar de la ilustración grande en conexiones con un ancho de banda bajo o en pantallas muy estrechas.
Texto de la página de inicio de sesión | Se muestra encima del pie de página cuando necesita ofrecer información útil a los usuarios antes de que inicien sesión con su cuenta profesional o educativa. Por ejemplo, es posible que desee incluir el número de teléfono de soporte técnico o una declaración legal.

> [AZURE.NOTE]
Todos los elementos son opcionales. Por ejemplo, si especifica un logotipo del banner pero no una ilustración grande, la página de inicio de sesión mostrará su logotipo y la ilustración del sitio de destino (es decir, la imagen de la autopista de California de Office 365).

También puede localizar todos los elementos de esta página. Una vez que haya configurado un conjunto de elementos de personalización "predeterminado", puede configurar versiones adicionales para las diferentes configuraciones regionales. También puede mezclar y hacer coincidir varios elementos. Por ejemplo, puede:

- Crear una ilustración grande "predeterminada" que funcione para todas las culturas y, a continuación, crear versiones específicas para inglés y francés. Los usuarios con exploradores establecidos en uno de estos dos idiomas verán la imagen específica, mientras que los demás usuarios verán la predeterminada.
- Configure logotipos diferentes para su organización (por ejemplo, para las versiones en japonés o hebreo).

### ¿Cómo aparecerá la ilustración una vez que se haya cambiado el tamaño del explorador?

Cuando se cambie el tamaño de la ventana de un explorador, la ilustración grande, como la mostrada anteriormente, casi siempre se recortará para dar cabida a diferentes proporciones de aspecto en la pantalla. Teniendo esto en cuenta, debe intentar mantener siempre los elementos visuales más importantes de la ilustración en la esquina superior izquierda (en la superior derecha para los idiomas que se leen de derecha a izquierda). Esto es importante porque el cambio de tamaño normalmente se produce desde la esquina inferior derecha hacia la parte superior izquierda o desde la parte inferior hacia la parte superior.

La imagen siguiente muestra cómo se recorta la ilustración cuando se cambia el tamaño del explorador hacia la izquierda:

![][6]

Y he aquí cómo aparecerá después de que el tamaño del explorador se cambie hacia la parte superior:

![][7]

## Personalización de la página del panel de acceso

La página del panel de acceso es esencialmente una página del portal para todos los usuarios finales que necesitan un acceso rápido, a través de iconos de aplicaciones en los que se puede hacer clic, a las distintas aplicaciones en la nube a las que se les ha concedido acceso. Si desea utilizar la experiencia de la página de panel de acceso sin información de marca, no tiene que hacer nada.

### ¿Qué verán mi usuarios finales una vez que haya personalizado la página del panel de acceso?

![][8]

## Configuración de su directorio con la información de marca de empresa

Puede configurar un conjunto predeterminado de elementos personalizables por directorio en el Portal de administración. Después de guardar los valores predeterminados, un administrador también tiene la opción de agregar versiones localizadas de cada elemento para diferentes idiomas o configuraciones regionales. Todos los elementos personalizables son opcionales.

Por ejemplo, si configura un logotipo de Banner predeterminado pero no una ilustración grande, la página de inicio de sesión mostrará su logotipo en la esquina superior derecha, sin embargo, se mostrará la ilustración predeterminada del sitio. Si configura de forma predeterminada un logotipo del banner y el texto de la página de inicio de sesión en inglés, y configura un texto de la página de inicio de sesión específico para alemán, los usuarios que tengan el alemán como idioma preferido verán el logotipo del banner predeterminado, pero el texto aparecerá en alemán. Aunque técnicamente podría configurar un conjunto diferente para cada idioma admitido por Azure AD, se recomienda que mantenga reducidas las variantes por razones de mantenimiento y rendimiento.

Para agregar la información de marca de empresa a su directorio:

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com) como el administrador del directorio que desea personalizar.
2. Seleccione el directorio que desea personalizar.
3. Seleccione la pestaña **Configurar** y a continuación, seleccione **Personalizar la información de marca**.
4. Modifique los elementos que desea personalizar. Tenga en cuenta que todos los campos son opcionales.
5. Haga clic en **Guardar**.

Puede transcurrir hasta una hora para que los usuarios vean los nuevos cambios efectuados en la información de la marca de la página de inicio de sesión.

Para agregar información de marca de empresa específica de un idioma:

1. En el [Portal de administración de Azure](https://manage.windowsazure.com), en la pestaña **Configurar**, seleccione **Personalizar la información de marca**.
2. Seleccione **Agregar personalización de marca para un idioma específico**, seleccione el idioma que para el que desea personalizar el logotipo y, a continuación, haga clic en **Siguiente**.
3. Edite únicamente aquellos elementos para los que desee configurar elementos específicos del idioma de reemplazo. Tenga en cuenta que todos los campos son opcionales. Si un campo se deja en blanco, en su lugar se mostrará el valor predeterminado personalizado (o el valor predeterminado de Microsoft si no se ha configurado un valor predeterminado personalizado).
4. Haga clic en **Guardar**.

Para quitar la información de marca de empresa del directorio

1. En el [Portal de administración de Azure](https://manage.windowsazure.com), en la pestaña **Configurar**, seleccione **Personalizar la información de marca**.
2. En la página Personalizar la información de marca, seleccione **Editar configuración de marca existente** y, a continuación, vaya a la página siguiente.
3. En función de los elementos que desee quitar, realice una o varias de las acciones siguientes:
	1. Para el logotipo del banner, haga clic en la casilla para **Quitar logotipo cargado**.
    2. Para el logotipo de icono, haga clic en la casilla para **Quitar logotipo cargado**.
    3. Para la etiqueta del nombre de usuario de la página de inicio de sesión, borre todo el texto.
    4. Para el texto de la página de inicio de sesión, borre todo el texto.
    5. Para la ilustración de la página de inicio de sesión, haga clic en la casilla para **Quitar ilustración**.
    6. Para el color de fondo de la página de inicio de sesión, borre todo el texto.
4. Haga clic en **Guardar** para quitar los elementos.
5. Si es necesario, haga clic en **Personalizar la información de marca** de nuevo y repita estos pasos para todos los elementos específicos de la marca del idioma que tengan que quitarse. Se habrán quitado todos los valores de personalización de marca cuando haga clic en **Personalizar la información de marca** y vea el formulario **Personalizar información de la marca predeterminada** sin configuraciones.

## Pruebas y ejemplos

Se recomienda que experimente con un inquilino de prueba antes de realizar cambios en el entorno de producción. La manera más sencilla de comprobar si se ha aplicado la información de marca es abrir una sesión de explorador InPrivate o de incógnito y, a continuación, visitar https://outlook.com/contoso.com, sustituyendo contoso.com por el dominio que haya personalizado. Tenga en cuenta que esto también funciona con los dominios que tengan un aspecto similar a contoso.onmicrosoft.com.

Para ayudarle a crear conjuntos eficaces de personalización, hemos personalizado las siguientes dos páginas de inicio de sesión ficticias:

- [http://AKA.ms/aaddemo001](http://aka.ms/aaddemo001)
- [http://AKA.ms/aaddemo002](http://aka.ms/aaddemo002)

Para probar la configuración específica del idioma, deberá modificar las preferencias de idioma predeterminado en el explorador web a un idioma que se haya definido en la personalización. En Internet Explorer, esto se configura en el menú **Opciones de Internet**.

## Elementos personalizables

Algunos elementos personalizables de Azure AD disponen de múltiples casos de uso. Los logotipos de empresa pueden configurarse una vez por cada directorio y se utilizan tanto en la página de inicio de sesión como en la del panel de acceso, mientras que algunos elementos personalizables son específicos solo de la página de inicio de sesión. En la tabla siguiente se proporcionan detalles para los diferentes elementos personalizables.

Nombre | Descripción | Restricciones | Recomendaciones
	------------- | ------------- | ------------- | -------------
Logotipo del banner | El logotipo del banner se muestra en la página de inicio de sesión y en el panel de acceso. | <p>JPG o PNG</p><p>60 x 280 píxeles</p><p>10 KB</p> | <p>Utilice el logotipo completo de su organización (incluido el pictograma y el logotipo)</p><p>Procure que tenga menos de 30 píxeles de altura para evitar la introducción de barras de desplazamiento en dispositivos móviles</p><p>Manténgalo por debajo de 4 KB</p><p>Utilice un PNG transparente (no presuponga que la página de inicio de sesión siempre tendrá un fondo blanco)</p>
Logotipo del icono | (no se utiliza actualmente en la página de inicio de sesión). En el futuro, este texto puede utilizarse para reemplazar el pictograma genérico de "cuenta profesional o educativa" en diferentes lugares de la experiencia. | <p>JPG o PNG</p><p>120 x 120 píxeles</p><p>10 KB</p> | <p>Conserve un diseño sencillo (sin texto pequeño), ya que el tamaño de esta imagen puede cambiar hasta en un 50%.
</p> |
Etiqueta de nombre de usuario de la página de inicio de sesión | (no se utiliza actualmente en la página de inicio de sesión). En el futuro, este texto puede utilizarse para reemplazar la cadena genérica de "cuenta profesional o educativa" en diferentes lugares de la experiencia. Puede establecerlo en algo como "Cuenta Contoso" o "Id. de Contoso". | <p>Texto Unicode, de hasta 50 caracteres</p><p>Solo texto sin formato (sin vínculos o etiquetas HTML)</p> | <p>Elija un nombre breve y sencillo</p><p>Pregunte a los usuarios cómo suelen hacer referencia a la cuenta profesional o educativa que les proporciona.</p>
Texto de la página de inicio de sesión | Este texto "reutilizable" aparece debajo del formulario de la página de inicio de sesión y puede utilizarse para comunicar instrucciones adicionales o dónde acudir para obtener ayuda y soporte técnico. | <p>Texto Unicode, hasta 256 caracteres</p><p>Solo texto sin formato (sin vínculos o etiquetas HTML)</p> | No sobrepase los 250 caracteres (aproximadamente 3 líneas de texto)
Ilustración de la página de inicio de sesión | La ilustración es una imagen grande que se muestra en la página de inicio de sesión a la izquierda del formulario de la página de inicio de sesión. | <p>JPG o PNG</p><p>1420 x 1200</p><p>500 KB</p> | <p>1420 x 1200 píxeles</p><p>Importante: manténgala lo más pequeña posible, lo ideal es que no sobrepase los 200 KB. Si esta imagen es demasiado grande, afectará al rendimiento de la página de inicio de sesión si la imagen no se almacena en caché</p><p>Esta imagen casi siempre se recortará para adaptarse a las diferentes proporciones de la pantalla. Mantenga los elementos visuales principales en la esquina superior izquierda (en la esquina superior derecha para los idiomas que se leen de derecha a izquierda), ya que el cambio de tamaño se producirá desde la esquina inferior derecha, hacia la parte superior izquierda, a medida que se reduzca la ventana del explorador.</p>
Color de fondo de la página de inicio de sesión | El color de fondo de la página de inicio de sesión se usa en el área a la izquierda del formulario de la página de inicio de sesión. Es visible incluso si no hay ninguna ilustración de página de inicio de sesión presente. | Debe ser un color RGB en formato hexadecimal (ejemplo: #FFFFFF) | <p>El color de fondo puede mostrarse en lugar de la ilustración grande en conexiones de bajo ancho de banda</p><p>Es recomendable elegir el color principal del logotipo del banner</p>


## Pasos siguientes

- [Introducción a Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-add-company-branding/SignInPage_beforecustomization.png
[2]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization.png
[3]: ./media/active-directory-add-company-branding/SignInPage_mobile_beforecustomization.png
[4]: ./media/active-directory-add-company-branding/SignInPage_mobile_aftercustomization.png
[5]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_elements.png
[6]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedleft.png
[7]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedtop.png
[8]: ./media/active-directory-add-company-branding/APBranding.png

<!---HONumber=AcomDC_0128_2016-->