<properties 
	pageTitle="Personalización del aspecto del portal para desarrolladores en Administración de API de Azure" 
	description="Personalización del aspecto del portal para desarrolladores en Administración de API de Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/03/2015" 
	ms.author="sdanie"/>

# Personalización del aspecto del portal para desarrolladores en Administración de API de Azure

Los colores, las fuentes, los tamaños, los espacios y otros elementos del aspecto del portal para desarrolladores se definen mediante reglas de estilo. Existen conjuntos de estas reglas para cada elemento estructural de una página: el encabezado, el menú, el cuerpo del contenido, el título de página, etc. En estos procedimientos aprenderá a modificar las reglas de estilo.

Para editar las reglas estilo, haga clic en **Apariencia** en el menú **Portal para desarrolladores** del portal del publicador. A continuación, haga clic en **Iniciar personalización** para empezar a usar el editor de estilo.

El explorador cambiará entonces para mostrar una página oculta dentro del portal de desarrolladores que contiene muestras de contenido, con ejemplos de todas las reglas de estilo que se utilizan en cada una de las partes del sitio. Para abrir el editor de estilo, mueva el cursor sobre la fina línea vertical de color gris que aparece en el extremo izquierdo de la página. La barra de herramientas del editor debería aparecer de la siguiente forma:

![Customization toolbar][api-management-customization-toolbar]

Existen dos modos principales de edición de reglas de estilo: **Editar todas las reglas**, que muestra una lista de todas las reglas de estilo que se usan en cada una de las partes del sitio y **Seleccionar elemento**, que permite seleccionar un elemento de la página en la que se encuentra y solo muestra estilos para ese elemento.

En esta sección, deseamos cambiar el estilo de un solo elemento en concreto: por ejemplo, los encabezados de la página. Haga clic en la opción **Seleccionar elemento** de la barra de herramientas del editor de estilo y, a continuación, haga clic en **Seleccionar un elemento para personalizarlo**. Los elementos aparecerán resaltados cuando pase el mouse sobre ellos a fin de indicar los estilos del elemento que vería si hiciera clic.

Mueva el mouse sobre el texto que representa el título de la página en el encabezado y haga clic en él. Aparecerá un conjunto de reglas de estilos con diferentes nombres y categorías dentro del propio editor de estilo.

Cada una de las reglas representa un propiedad de estilo del elemento seleccionado. Por ejemplo, en el caso del texto del encabezado seleccionado anteriormente, el tamaño del texto está en @font-size-h1 mientras que el nombre de la fuente con alternativas está en @headings-font-family.

> Si está familiarizado con [bootstrap](http://getbootstrap.com/), estas reglas son en realidad [variables LESS](http://getbootstrap.com/css/) del tema de bootstrap usado por el portal de desarrolladores.

Ahora vamos a cambiar el color del texto del encabezado. Seleccione la entrada en el campo **@headings-color** y escriba #000000. Este es el código hexadecimal del color negro. Al realizar esta acción, verá que aparece un indicador de color cuadrado al final del cuadro de texto. Si hace clic en este indicador, un selector de colores le permitirá elegir un color.

![Color picker][api-management-customization-toolbar-color-picker]

Cuando haya terminado de hacer cambios en los estilos del elemento seleccionado, haga clic en **Obtener vista previa de cambios** para ver los resultados en pantalla. En este momento, solo serán visibles para los administradores. Si desea que todos los usuarios puedan ver estos cambios, haga clic en el botón **Publicar** en el editor de estilo y confirme los cambios.

![Publish form][api-management-customization-toolbar-publish-form]

> Para cambiar las reglas de estilo aplicables a cualquier otro elemento de la página, use el mismo proceso que ha seguido para el encabezado: haga clic en **Seleccionar un elemento** en el editor de estilo, seleccione el elemento en que esté interesado y empiece a modificar los valores de las reglas de estilo que aparecen en pantalla.


[Next steps]: #next-steps


[api-management-customization-toolbar]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar-publish-form.png

<!---HONumber=AcomDC_1210_2015-->