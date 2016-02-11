<properties 
	pageTitle="Creación y uso de grupos para administrar cuentas de desarrollador en Administración de API de Azure" 
	description="Obtenga información acerca de cómo administrar cuentas de desarrollador mediante los grupos en Administración de API de Azure." 
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
	ms.date="12/07/2015" 
	ms.author="sdanie"/>

# Creación y uso de grupos para administrar cuentas de desarrollador en Administración de API de Azure

En Administración de API, los grupos se usan para administrar la visibilidad de productos para los desarrolladores. Los productos son visibles en primer lugar para los grupos y luego los desarrolladores de dichos grupos pueden ver y suscribirse a los productos asociados a los grupos.

Administración de API tiene los siguientes grupos invariables del sistema.

-	**Administradores**: los administradores de la suscripción de Azure son miembros de este grupo. Los administradores controlan las instancias del servicio Administración de API y crean las API, las operaciones y los productos que usan los desarrolladores.
-	**Desarrolladores**: los usuarios del portal para desarrolladores autenticados se incluyen en este grupo. Los desarrolladores son los clientes que compilan aplicaciones con sus API. Los desarrolladores, después de que se les concede acceso al portal para desarrolladores, crean aplicaciones que llaman a las operaciones de una API.
-	**Invitados**: a este grupo pertenecen los usuarios del portal para desarrolladores no autenticados como, por ejemplo, clientes potenciales que visitan el portal para desarrolladores de una instancia de Administración de API. Se les concede determinado acceso de solo lectura, como por ejemplo la posibilidad de ver API pero no llamarlas.

Además de estos grupos del sistema, los administradores pueden crear grupos personalizados o [aprovechar los grupos externos en inquilinos de Azure Active Directory asociados][]. Los grupos personalizados y externos pueden usarse junto con grupos del sistema en la concesión a los desarrolladores de visibilidad y acceso a productos de la API. Por ejemplo, podría crear un grupo personalizado para los desarrolladores afiliados a una organización asociada específica y permitirles el acceso a las API a partir de un producto que contenga solo las API relevantes. Un usuario puede ser miembro de más de un grupo.

En esta guía se muestra cómo los administradores de una instancia de Administración de API pueden agregar nuevos grupos y asociarlos a productos y desarrolladores.

>[AZURE.NOTE]Además de crear y administrar grupos en el portal del editor, puede crear y administrar sus grupos mediante la entidad de [grupo](https://msdn.microsoft.com/library/azure/dn776329.aspx) de la API de REST de administración.

## <a name="create-group"> </a>Creación de un grupo

Para crear un grupo nuevo, haga clic en **Administrar** en el Portal de Azure clásico para su servicio Administración de API. De este modo, se abre el portal del publicador de Administración de API.

![Portal del publicador][api-management-management-console]

>Si todavía no ha creado una instancia del servicio Administración de API, consulte [Creación de una instancia del servicio de Administración de API][] en el tutorial [Introducción a la Administración de API de Azure][].

Haga clic en **Grupos** en el menú **Administración de API** de la izquierda y haga clic en **Agregar grupo**.

![Add new group][api-management-add-group]

Especifique un nombre único para el grupo y una descripción opcional y haga clic en **Guardar**.

![Add new group][api-management-add-group-window]

El nuevo grupo se muestra en la pestaña Grupos. Para editar el **Nombre** o la **Descripción** del grupo, haga clic en el nombre del grupo en la lista. Para eliminar el grupo, haga clic en **Eliminar**.

![Group added][api-management-new-group]

Ahora que se ha creado el grupo, se puede asociar a productos y desarrolladores.

## <a name="associate-group-product"> </a>Asociación de un grupo a un producto

Para asociar un grupo a un producto, haga clic en **Productos** en el menú **Administración de API** de la izquierda y haga clic en el nombre del producto que desee.

![Set visibility][api-management-add-group-to-product]

Seleccione la pestaña **Visibilidad** para agregar y quitar grupos, así como para ver los grupos actuales para el producto. Para agregar o quitar grupos, active o desactive las casillas correspondientes a los grupos que desee y haga clic en **Guardar**.

![Set visibility][api-management-add-group-to-product-visibility]

>[AZURE.NOTE]Para agregar grupos de Azure Active Directory, consulte [Autorización de las cuentas de desarrollador con Azure Active Directory en Administración de API de Azure](api-management-howto-aad.md).
>
>Para configurar grupos en la pestaña **Visibilidad** para un producto, haga clic en **Administrar grupos**.

Una vez asociado un producto a un grupo, los desarrolladores del grupo pueden ver el producto y suscribirse a él.

## <a name="associate-group-developer"> </a>Asociación de grupos a desarrolladores

Para asociar grupos a desarrolladores, haga clic en **Usuarios** en el menú **Administración de API** de la izquierda y active la casilla situada junto a los desarrolladores que desee asociar a un grupo.

![Add developer to group][api-management-add-group-to-developer]

Una vez seleccionados los desarrolladores que desee, haga clic en el grupo que desee en la lista desplegable **Agregar a grupo**. Los desarrolladores se pueden quitar de los grupos con la lista desplegable **Quitar de grupo**.

![Desarrolladores][api-management-add-group-to-developer-saved]

Una vez agregada, la asociación entre el desarrollador y el grupo se puede ver en la pestaña **Usuarios**.


## <a name="next-steps"> </a>Pasos siguientes

-	Una vez agregado a un grupo, un desarrollador puede ver los productos asociados al grupo y suscribirse a ellos. Para obtener más información, consulte [Creación y publicación de un producto en Administración de API de Azure][].
-	Además de crear y administrar grupos en el portal del editor, puede crear y administrar sus grupos mediante la entidad de [grupo](https://msdn.microsoft.com/library/azure/dn776329.aspx) de la API de REST de administración.


[api-management-management-console]: ./media/api-management-howto-create-groups/api-management-management-console.png
[api-management-add-group]: ./media/api-management-howto-create-groups/api-management-add-group.png
[api-management-add-group-window]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
[api-management-new-group]: ./media/api-management-howto-create-groups/api-management-new-group.png
[api-management-add-group-to-product]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
[api-management-add-group-to-product-visibility]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
[api-management-add-group-to-developer]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
[api-management-add-group-to-developer-saved]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png

[api-management-]: ./media/api-management-howto-create-groups/api-management-.png

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[Creación y publicación de un producto en Administración de API de Azure]: api-management-howto-add-products.md

[Introducción a la Administración de API de Azure]: api-management-get-started.md
[Creación de una instancia del servicio de Administración de API]: api-management-get-started.md#create-service-instance
[aprovechar los grupos externos en inquilinos de Azure Active Directory asociados]: api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group

<!---HONumber=AcomDC_1210_2015-->