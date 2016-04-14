<properties
	pageTitle="Solicitud de aumento de los límites de la cuenta de DocumentDB | Microsoft Azure"
	description="Obtenga información acerca de cómo solicitar un ajuste en los límites de DocumentDB, como el número de colecciones permitidas, los procedimientos almacenados y las cláusulas de consulta."
	services="documentdb"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"
	documentationCenter=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/28/2016"
	ms.author="anhoh"/>

# Solicitud de aumento de los límites de la cuenta de DocumentDB

[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) tiene un conjunto de límites predeterminados y aplicaciones de cuota. Es posible ajustar algunas cuotas estableciendo contacto con el soporte técnico de Azure. En este artículo se muestra cómo solicitar un aumento del límite de la cuenta.

Después de leer este artículo, podrá responder a las preguntas siguientes:

-	¿Qué cuotas de la cuenta de DocumentDB pueden ajustarse estableciendo contacto con el soporte técnico de Azure?
-	¿Cómo puedo solicitar un ajuste en la cuota de la cuenta de DocumentDB?

##<a id="AdjustableQuotas"></a> Cuotas ajustables de la cuenta de DocumentDB

En la tabla siguiente se describen las cuotas de DocumentDB que se pueden ajustar estableciendo contacto con el soporte técnico de Azure:

|Entidad |Cuota (oferta estándar)|
|-------|--------|
|Cuentas de la base de datos |5
|Número de procedimientos almacenados, desencadenadores y UDF por colección |25 cada una
|Número máximo de colecciones por cuenta de base de datos |100
|Almacenamiento máximo de documentos por base de datos (100 colecciones) |1 TB
|Número máximo de UDF por consulta |2
|Número máximo de JOIN por consulta |5
|Número máximo de cláusulas AND por consulta |20
|Número máximo de cláusulas OR por consulta |20 |
|Número máximo de valores por expresión IN |200
|Número máximo de puntos de un argumento de polígono de una consulta ST\_WITHIN |16
|Número máximo de creaciones de colección por minuto |5
|Número máximo de operaciones de escala por minuto |5

##<a id="RequestQuotaIncrease"></a> Solicitud de un ajuste de cuota
En los pasos siguientes se muestra cómo solicitar un ajuste de cuota.

1. En el [Portal de Azure](https://portal.azure.com), haga clic en **Examinar** y, a continuación, en **Ayuda+Soporte técnico**.

	![Captura de pantalla del inicio de ayuda y soporte técnico](media/documentdb-increase-limits/helpsupport.png)

2. En la hoja **Ayuda+Soporte técnico**, haga clic en **Obtener soporte técnico**.

	![Captura de pantalla de la creación de una incidencia de soporte técnico](media/documentdb-increase-limits/getsupport.png)

3. En la hoja **Nueva solicitud de soporte**, haga clic en **Fundamentos**. A continuación, establezca el **Tipo de problema** en **Cuota**, **Suscripción** en la suscripción que hospeda su cuenta de DocumentDB, **Servicio** en **DocumentDB** y **Plan de soporte** en **COMPATIBILIDAD con cuotas - incluido**. Finalmente, haga clic en **Siguiente**.

	![Captura de pantalla del tipo de solicitud de incidencia de soporte técnico](media/documentdb-increase-limits/supportrequest1.png)

4. En la hoja **Problema**, elija un nivel de gravedad. Establezca **Tipo de problema** en **DocumentDB** e incluya información sobre el aumento de la cuota en **Detalles**. Haga clic en **Siguiente**.

	![Captura de pantalla del selector de suscripción de incidencia de soporte técnico](media/documentdb-increase-limits/supportrequest2.png)

5. Por último, rellene la información de contacto en la hoja **Información de contacto**.

	![Captura de pantalla de selector de recursos de incidencia de soporte técnico](media/documentdb-increase-limits/supportrequest3.png)

Una vez creada la incidencia de soporte técnico, debería recibir el número de solicitud de soporte técnico por correo electrónico. También puede ver la solicitud de soporte técnico haciendo clic en **Administrar solicitudes de soporte técnico** en la hoja **Ayuda+Soporte técnico**.

![Captura de pantalla de la hoja de solicitudes de soporte técnico](media/documentdb-increase-limits/supportrequest4.png)


##<a name="NextSteps"></a> Pasos siguientes
- Para obtener más información acerca de DocumentDB, haga clic [aquí](http://azure.com/docdb).

<!---HONumber=AcomDC_0128_2016-->