<properties

	pageTitle="Managing security groups in Azure Active Directory | Microsoft Azure"
	description="How to create and manage security groups to manage Azure resource access using Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/10/2016"
	ms.author="curtand"/>


#Administración de grupos de seguridad en Azure Active Directory

Dentro de Azure Active Directory (Azure AD), una de las principales características es la capacidad para administrar el acceso a los recursos. Estos recursos pueden formar parte del directorio, como en el caso de los permisos para administrar objetos a través de roles en el directorio, o recursos externos al directorio, como las aplicaciones SaaS, servicios de Azure y sitios de SharePoint o publicados en modo local. El propietario de un recurso puede asignarle un grupo y, al hacerlo, conceder acceso al recurso a los miembros de ese grupo. El propietario del grupo puede administrar entonces la pertenencia dicho grupo. El propietario del recurso delega efectivamente en el propietario del grupo el permiso para asignar a usuarios a sus recursos.


##Creación y administración de un grupo de seguridad

**Para crear un grupo en el portal de Azure**

1. En el Portal de Azure, haga clic en **Active Directory** y luego haga clic en el nombre del directorio de su organización.
2. Haga clic en la pestaña **Grupos**.
3. En la página Grupos, haga clic en **Agregar grupo**.
4. En la ventana **Agregar grupo**, especifique el nombre y la descripción de un grupo.
5. Esta tarea puede realizarse mediante el portal de la cuenta de Office 365, el portal de cuentas de Windows Intune o el portal de Azure, dependiendo de los servicios a los que se haya suscrito su organización. Para obtener más información acerca del uso de portales para administrar su directorio de Azure Active Directory, consulte [Administración de su directorio de Azure AD](active-directory-administer).

## Asignación o eliminación de usuarios en un grupo de seguridad

**Para agregar un miembro a un grupo del Portal de Azure**

1. En el Portal de Azure, haga clic en **Active Directory** y luego haga clic en el nombre del directorio de su organización.
2. Haga clic en la pestaña **Grupos**.
3. En la página **Grupos**, haga clic en el nombre del grupo al que quiere agregar miembros. De forma predeterminada, aparece la pestaña **Miembros** del grupo seleccionado.
4. En la página del grupo, haga clic en**Agregar miembros**.
5. En la página **Agregar miembros**, haga clic en el nombre del usuario o el grupo que quiere agregar como miembro de este grupo y asegúrese de que este nombre se agrega al panel Seleccionado.


**Para eliminar un miembro de un grupo del Portal de Azure**

1. En el Portal de Azure, haga clic en **Active Directory** y luego haga clic en el nombre del directorio de su organización.
2. Haga clic en la pestaña **Grupos**.
3. En la página Grupos, haga clic en el nombre del grupo del que desea quitar miembros.
4. En la página del grupo, haga clic en la pestaña **Miembros**.
5. En la página de ese grupo, haga clic en el nombre del miembro que quiere quitar de este grupo y luego haga clic en **Quitar**.
6. Confirme que quiere quitar este miembro del grupo. Para ello, haga clic en **Sí** como respuesta a la pregunta de comprobación de la acción.


## Uso de una regla para administrar dinámicamente los miembros de un grupo de seguridad

**Para habilitar la pertenencia dinámica para un grupo determinado, siga estos pasos:**

1. En el Portal de Azure, en la pestaña **Grupos**, seleccione el grupo que quiere editar y luego, en la pestaña **Configurar** de este grupo, establezca el conmutador **Habilitar pertenencia dinámica** en **Sí**.
2. Ahora puede configurar una sola regla sencilla para el grupo que controlará cómo funciona la pertenencia dinámica para este grupo. Asegúrese de que el botón de radio **Agregar usuarios donde** está activado y luego seleccione una propiedad de usuario en el menú desplegable (por ejemplo, department, jobTitle, etc.).
3. A continuación, seleccione una condición (Not Equals, Equals, Not Starts With, Starts With, Not Contains, Contains, Not Match, Match), y por último, especifique un valor para la propiedad del usuario seleccionado.

Por ejemplo, si un grupo se asigna a una aplicación SaaS (para más información, vea Asignación de acceso para un grupo a una aplicación SaaS en Azure AD) y habilita las pertenencias dinámicas para este grupo estableciendo una regla mediante la cual los usuarios Agregar usuarios donde se establece para un puesto de trabajo (jobTitle) con la condición Equals(-eq)Sales Rep, todos los usuarios de su directorio de Azure AD cuyos puestos de trabajo se hayan configurado en Sales Rep tendrán acceso a la aplicación SaaS.

## Información adicional

Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)

* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

* [¿Qué es Azure Active Directory?](active-directory-whatis.md)

* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0218_2016-->