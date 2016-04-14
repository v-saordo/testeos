<properties
	pageTitle="Límites y cuotas del servicio Lote | Microsoft Azure"
	description="Obtenga información sobre las cuotas, los límites y las restricciones en el uso del servicio Lote de Azure"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="danlep"/>

# Cuotas y límites del servicio de Lote de Azure

En este artículo se describen los límites máximos y predeterminados de determinados recursos que puede usar con el servicio Lote de Azure. La mayoría de estos límites son cuotas que aplica Azure a sus suscripción o sus cuentas de Lote.

Si planea ejecutar cargas de trabajo Lote de producción, deberá aumentar una o varias de las cuotas por encima del valor predeterminado. Si desea aumentar una cuota, abra una solicitud de soporte técnico al cliente en línea sin ningún costo.

>[AZURE.NOTE] Una cuota es un límite de crédito, no una garantía de capacidad. Si tiene necesidades de capacidad a gran escala, póngase en contacto con el soporte técnico de Azure.

## Cuotas de suscripción
Recurso|Límite predeterminado|Límite máximo
---|---|---
Cuentas de Lote por región y suscripción|1|50

## Cuotas de la cuenta de Lote
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## Otros límites
Recurso|Límite máximo
---|---
Tareas por nodo de proceso|4 × número de núcleos de nodo

## Visualización de las cuotas de Lote

Vea las cuotas de la cuenta de Lote en el [Portal de Azure](https://portal.azure.com).

1. En el Portal, haga clic en **Cuentas de Lote** y luego en el nombre de la cuenta de Lote.

2. En la hoja de la cuenta, haga clic en **Configuración** > **Propiedades**.

	![Cuotas de la cuenta de Lote][account_quotas]

3. En la hoja **Propiedades**, revise las cuotas que se aplican actualmente a la cuenta de Lote.

## Aumento de la cuota

Use los pasos siguientes para solicitar un aumento de la cuota en el Portal de Azure (también puede solicitar un aumento en el [Portal de Azure clásico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)).

1. En el panel de portal, haga clic en **Ayuda y soporte técnico**.

2. Haga clic en **Nueva solicitud de soporte técnico > Aspectos básicos**.

3. En la hoja **Aspectos básicos**, haga lo siguiente:

	a. En **Tipo de problema**, seleccione **Cuota**.

	b. Seleccione su suscripción.

	c. En **Tipo de cuota**, seleccione **Lote**.

	d. En **Plan de soporte técnico**, seleccione **Plan de soporte técnico de Azure - Desarrollador**.

	Haga clic en **Siguiente**.

4. En la hoja **Problema**, haga lo siguiente:

	a. Seleccione una de las opciones en **Gravedad** según su impacto en el negocio.

	b. En **Detalles**, muestre la cuota o las cuotas que quiere cambiar de una determinada cuenta y los nuevos límites que prefiere.

	Haga clic en **Siguiente**.

5. En la hoja **Información de contacto**, escriba los detalles de contacto y haga clic en **Siguiente**.

6. Haga clic en **Crear** para enviar la nueva solicitud de soporte técnico.

El servicio de soporte técnico de Azure se pondrá en contacto con usted. Completar la solicitud puede tardar hasta 2 días laborables.

## Temas relacionados

* [Creación y administración de una cuenta de Lote de Azure](batch-account-create-portal.md)

* [Información general de las características de Lote de Azure](batch-api-basics.md)

* [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md)

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

<!---HONumber=AcomDC_0218_2016-->