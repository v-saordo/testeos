<properties
	pageTitle="Personalización del portal para desarrolladores en Administración de API de Azure | Microsoft Azure"
	description="Obtenga información sobre cómo personalizar el portal para desarrolladores en Administración de API de Azure"
	services="api-management"
	documentationCenter=""
	authors="steved0x"
	manager="erikre"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/04/2016"
	ms.author="sdanie"/>

# Personalización del portal para desarrolladores en Administración de API de Azure

En esta guía se muestra cómo modificar la apariencia del portal para desarrolladores en Administración de API a fin de unificarlo con su imagen de marca.

## <a name="change-page-headers"> </a>Cambio del texto o el logotipo en los encabezados de página

Uno de los aspectos más importantes de la personalización del portal es la sustitución del texto que aparece en la parte superior de todas las páginas por el logotipo o el nombre de su empresa.

El contenido del portal para desarrolladores se modifica mediante el portal para editores, al que se accede desde el Portal de Azure clásico. Para llegar al portal para editores de la API, haga clic en **Administrar** en el Portal de Azure clásico para el servicio Administración de API.

![Portal del publicador][api-management-management-console]

El portal para desarrolladores se basa en el sistema de administración de contenido (CMS). El encabezado que aparece en todas las páginas es un tipo especial de contenido conocido como widget. Para editar el contenido de ese widget, haga clic en **Widgets** en el menú **Portal para desarrolladores** que aparece a la izquierda y seleccione el widget **Encabezado** de esa lista.

![Widgets header][api-management-widgets-header]

El contenido del encabezado se puede editar desde el campo **Cuerpo**. Cambie el texto a "Portal para desarrolladores Fabrikam" y haga clic en **Guardar** en la parte inferior de la página.

Ahora debe aparecer el nuevo encabezado en todas las páginas del portal para desarrolladores.

> Para abrir el portal para desarrolladores desde el portal para editores, haga clic en **Portal para desarrolladores** en la barra superior.

## <a name="change-headers-styling"> </a>Cambio del estilo de los encabezados

Los colores, las fuentes, los tamaños, los espacios y otros elementos relacionados con el estilo de cualquier página del portal vienen definidos por las reglas de estilo. Para editar los estilos, haga clic en **Apariencia** en el menú **Portal para desarrolladores** del portal para editores y luego haga clic en **Comenzar la personalización** para habilitar el editor de estilo.

El explorador pasa a una página oculta dentro del portal para desarrolladores que contiene muestras de contenido, con ejemplos de todas las reglas de estilo que se usan en cualquier lugar del sitio. Para abrir el editor de estilo, mueva el cursor sobre la fina línea vertical de color gris que aparece en el extremo izquierdo de la página. En ese momento debería aparecer la barra de herramientas del editor.

![Customization toolbar][api-management-customization-toolbar]

Existen dos modos principales de edición de reglas de estilo: **Editar todas las reglas**, que muestra una lista de todas las reglas de estilo que se utilizan en cada una de las partes del sitio, y **Seleccionar elemento**, que le permite seleccionar un elemento de la página en la que se encuentra y muestra estilos únicamente para ese elemento.

En esta sección queremos cambiar solo el estilo de los encabezados. Haga clic en la opción **Seleccionar elemento** de la barra de herramientas del editor de estilo y luego haga clic en **Seleccionar un elemento para personalizarlo**. Los elementos se irán resaltando cuando pase el mouse sobre ellos a fin de indicar qué estilos del elemento empezaría a editar si hiciera clic. Mueva el mouse sobre el texto que representa el nombre de la empresa en el encabezado ("Portal para desarrolladores Fabrikam" si siguió las instrucciones de la sección anterior) y haga clic en él. Aparecerá un conjunto de reglas de estilo con diferentes nombres y categorías dentro del propio editor de estilo.

Cada una de las reglas representa un propiedad de estilo del elemento seleccionado. Por ejemplo, en el caso del texto del encabezado seleccionado anteriormente, el tamaño del texto está en @font-size-h1 mientras que el nombre de la fuente con alternativas está en @headings-font-family.

> Si está familiarizado con [bootstrap][], estas reglas son en realidad [variables LESS][] del tema de bootstrap usado por el portal de desarrolladores.

Ahora cambiaremos el color del texto del encabezado. Seleccione la entrada en el campo **@headings-color** y escriba **#000000**. Este es el código hexadecimal del color negro. Al hacerlo, verá que aparece un indicador cuadrado de color al final del cuadro de texto. Si hace clic en este indicador, un selector de colores le permitirá elegir un color.

![Color picker][api-management-customization-toolbar-color-picker]

Cuando termine de hacer cambios en los estilos del elemento seleccionado, haga clic en **Obtener vista previa de cambios** para ver los resultados en pantalla. En este momento, solo serán visibles para los administradores. Si desea que todos los usuarios puedan ver estos cambios, haga clic en el botón **Publicar** en el editor de estilo y confirme los cambios.

![Publish menu][api-management-customization-toolbar-publish-form]

> Para cambiar las reglas de estilo que se aplican a cualquier otro elemento en la página, siga el mismo procedimiento que para el encabezado. Haga clic en **Seleccionar un elemento** desde el editor de estilo, seleccione el elemento que interese y empiece a modificar los valores de las reglas de estilo que aparecen en pantalla.

## <a name="edit-page-contents"> </a>Edición de los contenidos de una página

El portal para desarrolladores consta de páginas generadas automáticamente como API, Productos, Aplicaciones, Emisiones y contenido escrito a mano. Como se basa en un sistema de administración de contenido, puede crear contenido de este tipo a medida que sea necesario.

Para ver una lista de todas las páginas de contenido existentes, haga clic en **Contenido** en el menú **Portal para desarrolladores** del portal para editores.

![Manage content][api-management-customization-manage-content]

Haga clic en la página de **bienvenida** para editar lo que se muestra en la página principal del portal para desarrolladores. Haga los cambios que quiera, obtenga una vista previa de ellos si lo considera necesario y haga clic en **Publicar ahora** para que sean visibles para todos los usuarios.

> La página principal usa un diseño especial que le permite mostrar un banner en la parte superior. Este banner no se puede editar en la sección **Contenido**. Para editarlo, haga clic en **Widgets** en el menú **Portal para desarrolladores**, seleccione **Página principal** en la lista desplegable **Capa actual** y, por último, abra el elemento **Banner** en la sección **Destacados**. Los contenidos de este widget se pueden editar del mismo modo que cualquier otra página.

## <a name="next-steps"> </a>Pasos siguientes

-	Consulte el resto de temas del tutorial [Introducción a la configuración de API avanzada][].

[Change the text/logo in the page headers]: #change-page-headers
[Change the styling of the headers]: #change-headers-styling
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-portal/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-customize-portal/api-management-widgets-header.png
[api-management-customization-toolbar]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
[api-management-customization-manage-content]: ./media/api-management-customize-portal/api-management-customization-manage-content.png


[Introducción a la configuración de API avanzada]: api-management-get-started-advanced.md
[bootstrap]: http://getbootstrap.com/
[variables LESS]: http://getbootstrap.com/css/

<!---HONumber=AcomDC_0309_2016-->