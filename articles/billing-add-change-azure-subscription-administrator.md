<properties
	pageTitle="Incorporación o cambio de roles de administrador de Azure | Microsoft Azure"
	description="Se describe cómo agregar o cambiar el coadministrador, el administrador de servicios y el administrador de cuenta de Azure."
	services=""
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor="meerak"
	tags="billing"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="genli"/>

# Incorporación o cambio de roles de administrador de Azure

Hay tres tipos de roles de administrador en Microsoft Azure:

| Rol administrativo | Límite | Descripción
| ------------- | ------------- |---------------|
|Administrador de cuenta (AA) | 1 por cuenta de Azure |Es la persona que se registró en una suscripción de Azure o que compró una, y está autorizada para tener acceso al [Centro de cuentas](https://account.windowsazure.com/Home/Index) y realizar diversas tareas de administración. Entre estas se incluyen poder crear suscripciones, cancelar suscripciones, cambiar la facturación de una suscripción y cambiar el administrador de servicios.
| Administrador de servicios (SA) | 1 por cada suscripción de Azure |Esta persona está autorizada a administrar servicios en el [Portal de Azure](https://manage.windowsazure.com/). De forma predeterminada, en una nueva suscripción, el administrador de cuenta es también el administrador de servicios.|
|Coadministrador (CA)|200 por suscripción|Esta persona tiene los mismos privilegios de acceso que el administrador de servicios, pero no puede cambiar la asociación de suscripciones a directorios de Azure.|

> [AZURE.NOTE] Control de acceso basado en roles de Azure Active Directory (RBAC) permite que se agreguen usuarios a varios roles. Para más información, consulte [Control de acceso basado en roles de Azure Active Directory](./active-directory/role-based-access-control-configure.md).

## Incorporación de un coadministrador a una suscripción

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/).

2. En el panel de navegación, seleccione **Configuración**> **Administradores**> **Agregar**. </br>

	![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)

3. Escriba la dirección de correo electrónico de la persona que quiere agregar como coadministrador y luego seleccione la suscripción a la que quiere que el coadministrador tenga acceso.</br>

	![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

Se puede agregar la siguiente dirección de correo electrónico como coadministrador:

* **Cuenta Microsoft** (anteriormente Windows Live ID) </br> Puede usar una cuenta Microsoft para iniciar sesión en todos los productos y servicios en la nube de Microsoft orientados al consumidor, como Outlook (Hotmail), Skype (MSN), OneDrive, Windows Phone y Xbox LIVE.
* **Cuenta de organización**</br> Una cuenta de organización es una cuenta que se crea en Azure Active Directory. La dirección de la cuenta de organización se parece a esta: user@&lt;your dominio&gt;.onmicrosoft.com.

## Limitaciones y restricciones

 * Cada suscripción está asociada a un directorio de Azure AD (el directorio predeterminado). Para encontrar el directorio predeterminado al que está asociada la suscripción, vaya al [Portal de Azure clásico](https://manage.windowsazure.com/) y seleccione **Configuración** > **Suscripciones**. Compruebe el identificador de la suscripción para encontrar el directorio predeterminado.

 * Si ha iniciado sesión con una cuenta Microsoft, solo puede agregar otras cuentas Microsoft o usuarios del directorio predeterminado como coadministrador.

 * Si ha iniciado sesión con una cuenta de organización, puede agregar otras cuentas de organización de su organización como coadministrador. Por ejemplo, abby@contoso.com puede agregar bob@contoso.com como administrador de servicios o coadministrador, pero no puede agregar john@notcontoso.com a menos que john@noncontoso.com sea el usuario en el directorio predeterminado. Los usuarios que han iniciado sesión con cuentas de organización pueden continuar agregando usuarios de cuentas Microsoft como coadministrador o administrador de servicios.

 * Ahora que es posible iniciar sesión en Azure con una cuenta profesional, estos son los cambios en los requisitos de la cuenta de administrador de servicios y coadministrador:

	| Método de inicio de sesión| ¿Desea agregar cuentas Microsoft o usuarios del directorio predeterminado como coadministrador o administrador de servicios? |¿Desea agregar una cuenta de organización en la misma organización que el coadministrador o administrador de servicios? |¿Desea agregar una cuenta de organización en una organización diferente a la del coadministrador o administrador de servicios?
	| ------------- | ------------- |---------------|---------------|
	|Cuenta Microsoft |Sí|No|No|
	|Cuenta de organización|Sí|Sí|No|

## Cambio del administrador de servicios de una suscripción
Solo el administrador de cuenta puede cambiar el administrador de servicios de una suscripción.

1. Inicie sesión en el [Portal de administración de cuentas](https://account.windowsazure.com/subscriptions) con el administrador de cuenta.

2. Seleccione la suscripción que desea cancelar.

3. En el lado derecho, haga clic en **Editar suscripción**. </br>

	![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)

4. En el cuadro **ADMINISTRADOR DE SERVICIOS**, escriba la dirección de correo electrónico del nuevo administrador de servicios. </br>

	![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## Cambio del administrador de cuenta

Para transferir la propiedad de la cuenta de Azure a otra cuenta, consulte [Transferencia de suscripciones de Azure](billing-subscription-transfer.md).

## Pasos siguientes

* Para más información sobre cómo se controla el acceso a los recursos en Microsoft Azure, consulte [Descripción de acceso a los recursos de Azure](./active-directory/active-directory-understanding-resource-access.md).

* Para más información sobre cómo se relaciona Azure Active Directory con la suscripción de Azure, consulte [Asociación de las suscripciones de Azure con Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated directory.md)

* Para más información sobre cómo se relaciona Azure Active Directory con la suscripción de Azure, consulte [Asignación de roles de administrador en Azure Active Directory (Azure AD)](./active-directory/active-directory-assign-admin-roles.md).

<!---HONumber=AcomDC_0218_2016-->