<properties
	pageTitle="No puedo iniciar sesión para administrar mi suscripción de Azure | Microsoft Azure"
	description="Describe la información de solución de problemas para algunos problemas comunes de inicio de sesión de la suscripción de Azure."
	services="billing"
	documentationCenter=""
	authors="genlin"
	manager="jarrettr"
	editor="na"
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/30/2015"
	ms.author="genli"/>

# No puedo iniciar sesión para administrar mi suscripción de Azure

Este artículo le ayudará a solucionar algunas de las causas comunes de los problemas de inicio de sesión.

## ¿A qué portal está intentando obtener acceso?

Un propietario de cuenta solo puede tener acceso al [Centro de cuentas](https://account.windowsazure.com/) mientras que los administradores de servicios (SA) y los coadministradores (CA) tienen acceso al [Portal de Azure](https://manage.windowsazure.com/).

Haga clic en los vínculos siguientes para instrucciones sobre cómo actualizar los roles de administrador:

- [Para actualizar el administrador de servicio de su cuenta](./billing-add-change-azure-subscription-administrator.md#change-service-administrator-for-a-subscription)

- [Para agregar un nuevo CA en el portal de administración](./billing-add-change-azure-subscription-administrator.md#add-a-co-administrator-for-a-subscription)

## ¿Está su suscripción asociada a una cuenta de Microsoft o a una cuenta de organización?

Su cuenta de Microsoft es la dirección de correo electrónico que usa, junto con su contraseña para iniciar sesión en cualquier servicio o programa de Windows Live, como Outlook, Hotmail, OneDrive o MSN. Puede configurar una cuenta de Microsoft usando cualquier dirección de correo electrónico que tenga, incluido el correo electrónico de la compañía. Vea [www.microsoft.com/account](http://www.microsoft.com/account) para más detalles.

Si su cuenta está asociada a una cuenta de organización, seleccione la opción de inicio de sesión correcta tal y como se muestra a continuación. Para más información sobre el uso de la cuenta de la organización, vea [Registro en Azure como una organización](./active-directory/sign-up-organization.md).

![página de inicio de sesión](./media/billing-cannot-login-subscription/signin.png)

## Coadministrador: ¿Está usando el tipo de cuenta correcto para administrar otras cuentas?

- Si ha iniciado sesión con una cuenta Microsoft, solo puede agregar otras cuentas Microsoft como coadministradores. Este es un requisito de seguridad para evitar que se detecten las cuentas no organizativas si algunas cuentas (por ejemplo, janedoe@contoso.com) son válidas.
- Si ha iniciado sesión con una cuenta organizativa, puede agregar otras cuentas organizativas de su organización como coadministradores. Por ejemplo, abby@contoso.com puede agregar bob@contoso.com como administrador de servicios o coadministrador, pero no puede agregar john@notcontoso.com. Los usuarios que han iniciado sesión con cuentas organizativas también pueden agregar usuarios de cuentas de Microsoft como coadministradores o administradores de servicios.

Ahora que es posible iniciar sesión en Azure con una cuenta profesional, estos son los cambios en los requisitos de la cuenta de administrador de servicios (SA) y coadministrador (CA):

| Método de inicio de sesión| ¿Agregar una cuenta Microsoft como coadministrador o administrador de servicios? |¿Agregar una cuenta de organización de la misma organización como coadministrador o administrador de servicios? |¿Agregar una cuenta de organización de una organización diferente como coadministrador o administrador de servicios?
| ------------- | ------------- |---------------|---------------|
|Cuenta Microsoft |Sí|No|No|
|Cuenta de organización|Sí|Sí|No|

## Intente eliminar caché o cookies, usando el modo de exploración de InPrivate en Internet Explorer y también mediante un explorador diferente.

Si necesita más ayuda en cualquier momento con este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure o de desbordamiento de pila](https://azure.microsoft.com/support/forums/). Como alternativa, también puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en Obtener soporte técnico. Para obtener información sobre el uso del soporte técnico de Azure, lea las [Preguntas más frecuentes del soporte técnico de Microsoft Azure](https://azure.microsoft.com/support/faq/).

<!---HONumber=AcomDC_0128_2016-->