<properties 
	pageTitle="Administración de cuentas de usuario en Administración de API de Azure" 
	description="Más información acerca de cómo crear usuarios o invitarlos en Administración de API de Azure" 
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

# Administración de cuentas de usuario en Administración de API de Azure

En Administración de API, los desarrolladores son los usuarios de las API que se exponen mediante Administración de API. En esta guía se muestra cómo crear desarrolladores e invitarlos a usar las API y los productos que usted pone a su disposición con la instancia de Administración de API.

## <a name="create-developer"> </a>Creación de un desarrollador

Para crear un desarrollador, haga clic en **Administrar** en el Portal de Azure clásico para su servicio Administración de API. De este modo, se abre el portal del publicador de Administración de API. Si todavía no ha creado una instancia del servicio Administración de API, consulte [Creación de una instancia del servicio de Administración de API][] en el tutorial [Introducción a la Administración de API de Azure][].

![Portal del publicador][api-management-management-console]

Haga clic en **Usuarios** en el menú **Administración de API** de la izquierda y haga clic en **Agregar usuario**.

![Create developer][api-management-create-developer]

Escriba el **Correo electrónico**, la **Contraseña** y el **nombre** del nuevo desarrollador y haga clic en **Guardar**.

![Create developer][api-management-add-new-user]

De forma predeterminada, las cuentas de desarrollador recién creadas se encuentran en estado **Activo** y se asocian al grupo **Desarrolladores**.

![New developer][api-management-new-developer]

Las cuentas de desarrollador que se encuentran en estado **activo** se pueden usar para obtener acceso a todas las API para las que tienen suscripciones. Para asociar el desarrollador recién creado a otros grupos, consulte [Asociación de grupos a desarrolladores][].

## <a name="invite-developer"> </a>Invitación a un desarrollador

Para invitar a un desarrollador, haga clic en **Usuarios** en el menú **Administración de API** de la izquierda y haga clic en **Invitar a usuario**.

![Invite developer][api-management-invite-developer]

Especifique el nombre y la dirección de correo electrónico del desarrollador y haga clic en **Invitar**.

![Invite developer][api-management-invite-developer-window]

Se mostrará un mensaje de confirmación, pero el desarrollador recién invitado no aparecerá en la lista hasta que acepte la invitación.

![Invite confirmation][api-management-invite-developer-confirmation]

Cuando se invita a un desarrollador, se le envía un correo electrónico. Este correo electrónico se genera con una plantilla y se puede personalizar. Para obtener más información, consulte [Configuración de plantillas de correo electrónico][].

Una vez aceptada la invitación, la cuenta se activa.

## <a name="block-developer"> </a> Desactivación o reactivación de una cuenta de desarrollador

De forma predeterminada, las cuentas de desarrollador recién creadas o a las que se ha invitado se encuentran en estado **Activo**. Para desactivar una cuenta de desarrollador, haga clic en **Bloquear**. Para reactivar una cuenta de desarrollador bloqueada, haga clic en **Activar**. Una cuenta de desarrollador bloqueada no puede obtener acceso al portal para desarrolladores ni llamar a ninguna API.

![Block developer][api-management-new-developer]

## <a name="next-steps"> </a>Pasos siguientes

Una vez creada una cuenta de desarrollador, se puede asociar a roles y suscribirse a productos y API. Para obtener más información, consulte [Creación y uso de grupos][].


[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-]: ./media/api-management-howto-create-or-invite-developers/api-management-.png



[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[Creación y uso de grupos]: api-management-howto-create-groups.md
[Asociación de grupos a desarrolladores]: api-management-howto-create-groups.md#associate-group-developer

[Introducción a la Administración de API de Azure]: api-management-get-started.md
[Creación de una instancia del servicio de Administración de API]: api-management-get-started.md#create-service-instance
[Configuración de plantillas de correo electrónico]: api-management-howto-configure-notifications.md#email-templates

<!---HONumber=AcomDC_1210_2015-->