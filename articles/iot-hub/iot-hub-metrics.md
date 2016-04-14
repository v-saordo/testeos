<properties
 pageTitle="Métricas de diagnóstico del Centro de IoT"
 description="Información general de las métricas del Centro de IoT de Azure que permite a los usuarios evaluar el estado general de sus recursos"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="nberdy"/>

# Introducción a las métricas de diagnóstico

Las métricas de diagnóstico forman parte de una inserción de métricas en todo el sistema de Azure cuyo fin es ofrecer mejores datos sobre el estado de los recursos de Azure en su suscripción. Las métricas permiten que un usuario evalúe el estado general del servicio y los dispositivos conectados a él. Las estadísticas orientadas al usuario son importantes porque ayudan a estos a ver lo que está ocurriendo con su Centro de IoT y ayudan a determinar la causa raíz de los problemas sin necesidad de ponerse en contacto con el soporte técnico de Azure.

Puede habilitar las métricas de diagnóstico desde el Portal de Azure.

## Habilitación de las métricas de diagnóstico

1. Cree un Centro de IoT. Puede encontrar instrucciones sobre cómo crear un Centro de IoT en la guía [Introducción][lnk-get-started].

2. Abra la hoja de su Centro de IoT. Desde allí, haga clic en **Toda la configuración** y luego haga clic en **Diagnósticos**.

    ![][1]

3. Configure el diagnóstico estableciendo el estado en **Activado** y seleccionando una cuenta de almacenamiento para almacenar los datos de diagnóstico. Active la casilla de las métricas y luego presione **Guardar**. Recuerde que la cuenta de almacenamiento se debe crear con antelación y se le cobrará por separado para el almacenamiento.

    ![][2]

4. Una vez haya configurado los diagnósticos, vuelva a la hoja principal de Centro de IoT. La información de las métricas se rellena en la sección **Supervisión** de la hoja. Al hacer clic en el gráfico se abre el panel de métricas, donde puede ver un resumen de la información de las métricas para su Centro de IoT y modificar la selección de las métricas que se muestra en el gráfico. También puede configurar alertas basadas en valores de métrica.

    ![][3]

## Las métricas y su uso

Centro de IoT proporciona varias métricas para ofrecerle una visión general del estado de su centro y el número total de dispositivos conectados a él. Puede combinar información de varias métricas para conseguir una imagen más amplia del estado del Centro de IoT. La tabla siguiente describe las métricas de las que cada Centro de IoT realiza un seguimiento y cómo se relaciona cada métrica con el estado general del Centro de IoT.

| Métrica | Descripción de la métrica | Para qué se usa la métrica |
| ---- | ---- | ---- |
| d2c.telemetry.ingress.allProtocol | Número de mensajes enviados en todos los dispositivos | Datos de información general sobre el envío de mensajes |
| d2c.telemetry.ingress.success | Número de mensajes completados correctamente en el Centro | Información general de las recepciones de mensajes completadas correctamente en el Centro |
| c2d.commands.egress.complete.success | Número de todos los mensajes de comando completados por el dispositivo receptor en todos los dispositivos | Junto con las métricas de abandono y rechazo, ofrece una visión general de la tasa de éxito global de comandos de nube a dispositivo |
| c2d.commands.egress.abandon.success | Número de todos los mensajes abandonados correctamente por el dispositivo receptor en todos los dispositivos | Destaca los posibles problemas si se están abandonando mensajes con una frecuencia superior a la esperada |
| c2d.commands.egress.reject.success | Número de todos los mensajes rechazados correctamente por el dispositivo receptor en todos los dispositivos | Destaca los posibles problemas si se están rechazando mensajes con una frecuencia superior a la esperada |
| devices.totalDevices | Promedio, mínimo y máximo de dispositivos registrados en el Centro de IoT | Número de dispositivos registrados en el Centro |
| devices.connectedDevices.allProtocol | Promedio, mínimo y máximo de dispositivos conectados simultáneamente | Información general sobre el número de dispositivos conectados en el Centro |

## Pasos siguientes

Ahora que ha visto la información general sobre las métricas de diagnóstico, siga estos vínculos para obtener más información:

- [IoT Hub operations monitoring (Supervisión de operaciones de Centro de IoT)][lnk-operations-monitoring]
- [Escalado del Centro de IoT][lnk-scaling]
- [Alta disponibilidad y recuperación ante desastres del Centro de IoT][lnk-dr]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png
[3]: media/iot-hub-metrics/enable-metrics-3.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

<!---HONumber=AcomDC_0204_2016-->