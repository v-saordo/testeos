<properties
   pageTitle="Introducción al Centro de seguridad de Azure | Microsoft Azure"
   description="Este documento le ayuda a empezar a trabajar rápidamente con el Centro de seguridad de Azure, guiándole a través de la supervisión de la seguridad y los componentes de administración de directivas y ofreciendo vínculos a los pasos siguientes."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/02/2016"
   ms.author="terrylan"/>

# Introducción al Centro de seguridad de Azure

Este documento le ayuda a empezar a trabajar rápidamente con el Centro de seguridad de Azure, guiándole a través de la supervisión de la seguridad y los componentes de administración de directivas y ofreciendo vínculos a los pasos siguientes.

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure. En este documento se presenta el servicio mediante una implementación de ejemplo. No se trata de una guía paso a paso.

## ¿Qué es el Centro de seguridad de Azure?
 El Centro de seguridad ayuda a evitar, detectar y responder a amenazas con más visibilidad y control de la seguridad en los recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

## Requisitos previos

Para empezar a trabajar con el Centro de seguridad, debe disponer de una suscripción a Microsoft Azure. El Centro de seguridad se habilita con su suscripción. Si no tiene una suscripción, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

 Se accede al Centro de seguridad desde el [Portal de Azure](https://azure.microsoft.com/features/azure-portal/). Consulte la [documentación del portal](https://azure.microsoft.com/documentation/services/azure-portal/) para más información.


## Acceso al Centro de seguridad

En el portal, siga estos pasos para acceder al Centro de seguridad:

1. Seleccione **Examinar** y después desplácese hasta la opción **Centro de seguridad**. ![Acceso al Centro de seguridad de Azure en el portal][1]

2. Seleccione **Centro de seguridad**. De este modo se abrirá la hoja **Centro de seguridad**.
3. Para facilitar el acceso a la hoja **Centro de seguridad** en el futuro, seleccione la opción **Fijar hoja al panel** (parte superior derecha). ![Opción Anclar hoja al panel][2]

## Uso del Centro de seguridad

Puede configurar directivas de seguridad para los grupos de recursos y las suscripciones de Azure. Configuremos una **directiva** de seguridad para su suscripción:

1. Haga clic en el icono **Directiva de seguridad** en la hoja **Centro de seguridad**. ![Centro de seguridad][3]

2. En la hoja **Directiva de seguridad: definir directiva por suscripción o grupo de recursos**, seleccione una suscripción. ![La hoja Directiva de seguridad en el Centro de seguridad de Azure][4]

3. En la hoja **Directiva de seguridad**, active **Recopilación de datos** para recopilar registros de forma automática. La activación de la **Recopilación de datos** también proporcionará la extensión de supervisión en todas las máquinas virtuales actuales y nuevas en la suscripción.
4. Seleccione **Elegir una cuenta de almacenamiento por región**. Para cada región en la que disponga de máquinas virtuales en funcionamiento, elija la cuenta de almacenamiento en la que se almacenan los datos recopilados de esas máquinas virtuales. Si no elige una cuenta de almacenamiento para cada región, se creará automáticamente. Los datos recopilados se aíslan lógicamente de los datos de otros clientes por seguridad.

     > [AZURE.NOTE] Se recomienda que antes active la recopilación de datos y elija una cuenta de almacenamiento en el nivel de suscripción. Las directivas de seguridad se pueden establecer en el nivel de suscripción y el nivel de grupo de recursos de Azure, pero la configuración de la recopilación de datos y la cuenta de almacenamiento tiene lugar solo en el nivel de suscripción.

5. Active las **Recomendaciones** que le gustaría ver como parte de la directiva de seguridad. Ejemplos:

 - Si se activa **Actualizaciones del sistema**, se examinarán todas las máquinas virtuales compatibles para comprobar si faltan actualizaciones del sistema operativo.
 - Si se activa **Reglas de línea de base**, se examinarán las máquinas virtuales compatibles para identificar las configuraciones del sistema operativo que podrían hacer que la máquina virtual resultara más vulnerable a ataques.

Atender las **recomendaciones**:

1. Vuelva a la hoja **Centro de seguridad** y haga clic en el icono **Recomendaciones**. El Centro de seguridad analiza periódicamente el estado de seguridad de los recursos de Azure. Una vez identificadas las posibles vulnerabilidades de seguridad, muestra una recomendación.
2.	Haga clic en cada recomendación para ver más información o actuar para resolver el problema. ![Recomendaciones en el Centro de seguridad de Azure][5]

Consulte el estado de y la seguridad de los recursos a través de **Estado de los recursos**:

1.	Vuelva a la hoja **Centro de seguridad**.
2.	El icono **Estado de los recursos** contiene indicadores del estado de seguridad de las **Máquinas virtuales**, **Redes**, **SQL** y **Aplicaciones**.
3.	Seleccione **Máquinas virtuales** para consultar más información.
4.	La hoja **Máquinas virtuales** muestra un resumen del estado de los programas antimalware, las actualizaciones del sistema, los reinicios y las reglas de línea de base de sus máquinas virtuales.
5.	Seleccione un elemento en **PASOS DE PREVENCIÓN** para ver más información o tomar medidas para configurar los controles necesarios.
6.	Explore en profundidad para ver información adicional para máquinas virtuales específicas. ![El icono Estado de los recursos en el Centro de seguridad de Azure][6]

Atender las **Alertas de seguridad**:

1.	Vuelva a la hoja **Centro de seguridad** y haga clic en el icono **Alertas de seguridad**. En la hoja **Alertas de seguridad**, se muestra una lista de alertas. Las alertas se generan mediante el análisis que el Centro de seguridad hace de los registros de seguridad y la actividad de la red. También se incluyen las alertas de soluciones de socios integradas. ![Alertas de seguridad en el Centro de seguridad de Azure][7]

2.	Seleccione una alerta para ver información adicional. ![Detalles de alertas de seguridad en el Centro de seguridad de Azure][8]

## Pasos siguientes
En este documento, se han presentado los componentes de supervisión de seguridad y de administración de directivas en el Centro de seguridad. Para obtener más información, consulte:

- [Establecimiento de directivas de seguridad en el Centro de seguridad de Azure](security-center-policies.md): obtenga información acerca de cómo configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
- [Administración de recomendaciones de seguridad en el Centro de seguridad de Azure](security-center-recommendations.md): obtenga información sobre cómo las recomendaciones lo ayudan a proteger sus recursos de Azure.
- [Supervisión del estado de seguridad en el Centro de seguridad de Azure](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar alertas de seguridad y responder a estas.
- [Preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md): encuentre las preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información de seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-get-started/security-tile.png
[2]: ./media/security-center-get-started/pin-blade.png
[3]: ./media/security-center-get-started/security-center.png
[4]: ./media/security-center-get-started/security-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png

<!---HONumber=AcomDC_0309_2016-->