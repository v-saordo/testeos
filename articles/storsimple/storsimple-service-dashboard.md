<properties 
   pageTitle="Panel del servicio del Administrador de StorSimple | Microsoft Azure"
   description="Describe el panel del servicio del Administrador de StorSimple y explica cómo se usa para supervisar el estado de la solución de StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/01/2016"
   ms.author="v-sharos" />

# Uso del panel del servicio StorSimple Manager

## Información general

La página del panel del servicio StorSimple Manager proporciona una vista resumida de todos los dispositivos que están conectados al servicio StorSimple Manager, y resalta aquellas que requieren la atención del administrador del sistema. Este tutorial presenta la página del panel, explica el contenido y la función del panel, y describe las tareas que puede realizar desde esta página.

![Panel del servicio](./media/storsimple-service-dashboard/HCS_ServiceDashboard.png)

El panel del servicio StorSimple Manager muestra la siguiente información:

- En el área **gráfica**, puede ver el gráfico de las métricas pertinentes para sus dispositivos. Puede ver el almacenamiento principal usado en todos los dispositivos, así como el almacenamiento en la nube consumido por los dispositivos durante un período de tiempo. Use los controles de la esquina superior derecha del gráfico para especificar una escala de tiempo de 1 semana, 1 mes, 3 meses o 1 año.

- La **información general del uso** muestra el almacenamiento principal aprovisionado y usado por todos los dispositivos en relación con el almacenamiento total disponible en todos los dispositivos. **Aprovisionado** se refiere a la cantidad de almacenamiento que está preparada y asignada para su uso, mientras que **Usado** se refiere al uso de los volúmenes, tal como lo ven los iniciadores que están conectados a los dispositivos.

- El área **alertas** proporciona una instantánea de todas las alertas activas en todos los dispositivos, agrupados por gravedad. Al hacer clic en el nivel de gravedad se abre la página **alertas**, dedicada a mostrar dichas alertas. En la página **alertas**, puede hacer clic en una alerta individual para ver detalles adicionales acerca de ella, incluidas las acciones recomendadas. También puede desactivar la alerta si el problema se ha resuelto.

- El área **trabajos** proporciona una instantánea de los trabajos recientes en todos los dispositivos que están conectados a su servicio. Hay vínculos que puede usar para consultar los trabajos que están actualmente en curso, los que produjeron errores en las últimas 24 horas o los que están programados para ejecutarse en las próximas 24 horas.

- El área de **vista rápida** proporciona información útil, como el estado del servicio, el número de dispositivos conectados al servicio, la ubicación del servicio y los detalles de la suscripción asociada con el servicio. También hay un vínculo al registro de operaciones. Haga clic en el vínculo para ver una lista de todas las operaciones completadas del servicio StorSimple Manager.

Puede usar la página del panel del servicio StorSimple Manager para iniciar las siguientes tareas:

- Ver o volver a generar la clave de registro del servicio.
- Cambiar la clave de cifrado de datos del servicio.
- Ver los registros de operaciones.

## Ver o volver a generar la clave de registro de servicio

La clave de registro del servicio se usa para registrar un dispositivo de Microsoft Azure StorSimple con el servicio StorSimple Manager, para que el dispositivo aparezca en el Portal de Azure clásico para otras tareas administrativas. La clave se crea en el primer dispositivo y se comparte con el resto de los dispositivos.

Al hacer clic en **Clave de registro** (en la parte inferior de la página), se abre el cuadro de diálogo **Clave de registro del servicio**, donde puede copiar la clave de registro del servicio actual en el Portapapeles o volver a generar la clave de registro del servicio.

Volver a generar la clave no afecta a los dispositivos registrados anteriormente: solo afecta a los dispositivos que se registran en el servicio después de volver a generar la clave.

Para obtener más información sobre cómo ver y generar la clave de registro del servicio, vaya a [Obtener la clave de registro del servicio](storsimple-manage-service.md#get-the-service-registration-key)

## Cambiar la clave de cifrado de datos de servicio

Las claves de cifrado de datos del servicio se usan para cifrar datos confidenciales de los clientes, como las credenciales de la cuenta de almacenamiento, que se envían desde el servicio StorSimple Manager al dispositivo de StorSimple. Necesitará cambiar estas claves periódicamente si su organización de TI tiene una directiva de rotación de claves en los dispositivos de almacenamiento. El proceso de cambio de clave puede ser ligeramente diferente dependiendo de si el servicio StorSimple Manager administra uno o varios dispositivos.

El cambio de la clave de cifrado de datos del servicio se realiza en 3 pasos:

1. En el Portal de Azure clásico, autorice que un dispositivo cambie la clave de cifrado de datos del servicio.
2. En Windows PowerShell para StorSimple, inicie el cambio de claves de cifrado de datos del servicio.
3. Si tiene más de un dispositivo de StorSimple, actualice la clave de cifrado de datos del servicio en los demás dispositivos.

Los pasos siguientes describen el proceso de sustitución de la clave de cifrado de datos del servicio.

[AZURE.INCLUDE [storsimple-change-data-encryption-key](../../includes/storsimple-change-data-encryption-key.md)]


## Consulta de los registros de operaciones

Para ver los registros de operaciones, haga clic en el vínculo de registros de operaciones disponible en la sección **vista rápida** del panel. Esto le llevará a la página de servicios de administración, donde puede filtrar y ver los registros específicos de su servicio StorSimple Manager.

## Pasos siguientes

- Aprenda cómo [solucionar problemas de un dispositivo de StorSimple](storsimple-troubleshoot-operational-device.md).

- Obtenga más información sobre cómo [usar el servicio StorSimple Manager para administrar su dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0218_2016-->