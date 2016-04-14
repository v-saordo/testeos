<properties
	pageTitle="Configurar alertas de facturación para las suscripciones de Microsoft Azure | Microsoft Azure"
	description="Describe cómo puede configurar alertas en su factura de Azure para que pueda evitar sorpresas de facturación."
	services=""
	documentationCenter=""
	authors="vikdesai"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/17/2015" 
	ms.author="vikdesai"/>

# Configurar alertas de facturación para las suscripciones de Microsoft Azure

¿Le preocupa cuánto gasta cada mes en su suscripción de Azure? Si es usted el Administrador de cuentas de una suscripción de Azure, puede utilizar el servicio de alertas de facturación de Azure para crear alertas de facturación personalizadas que le ayudarán a supervisar y administrar la actividad de facturación de las cuentas de Azure.

Este es un servicio de vista previa, por lo que lo primero que debe hacer es suscribirse en él: visite la <a href="https://account.windowsazure.com/PreviewFeatures">página de funciones de vista previa</a> en el portal de administración de cuentas de Azure para hacerlo.

## Establecimiento de los destinatarios de correo electrónico y del umbral de alerta

Después de recibir la confirmación de correo electrónico acerca de que el servicio de facturación está activado para su suscripción, visite <a href="https://account.windowsazure.com/Subscriptions">la página de suscripciones</a> en el portal de cuentas. Haga clic en la suscripción que desea supervisar y, a continuación, haga clic en **Alertas**.

![][Image1]

A continuación, haga clic en **Agregar alerta** para crear la primera alerta. Puede configurar un total de cinco alertas de facturación por suscripción, con un umbral diferente y hasta dos destinatarios de correo electrónico para cada alerta.

![][Image2]

Cuando agregue una alerta, asígnele un nombre único, elija un umbral de gasto y elija las direcciones de correo electrónico a las que se enviarán las alertas. Al configurar el umbral, podrá elegir un **Total de facturación** o un **Crédito monetario** desde la lista **Alerta** lista. Para el total de facturación, se enviará una alerta cuando el gasto de la suscripción supere el umbral. Para un crédito monetario, se enviará una alerta cuando los créditos monetarios caigan por debajo del límite. Los créditos monetarios suelen aplicarse a pruebas gratuitas y suscripciones asociadas a las cuentas de MSDN.

![][Image3]

Azure admite cualquier dirección de correo electrónico pero no comprueba que la dirección de correo electrónico funcione. Por lo tanto, revise si hay errores ortográficos.

## Comprobación de las alertas

Después de configurar las alertas, el centro de cuentas enumera y muestra cuántas más se pueden configurar. Para cada alerta, se mostrará la fecha y la hora de envío, si es una alerta de total de facturación o de crédito monetario, así como el límite configurado. El formato de fecha y hora es de 24 horas según el horario universal coordinado (UTC) y la fecha tiene el formato aaaa-mm-dd. Haga clic en el signo más de una alerta de la lista para modificarla o haga clic en la papelera para eliminarla.

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png

<!---HONumber=AcomDC_1125_2015-->