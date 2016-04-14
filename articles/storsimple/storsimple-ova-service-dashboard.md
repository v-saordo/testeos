<properties 
   pageTitle="Panel de servicio StorSimple Manager - Matriz virtual| Microsoft Azure"
   description="Describe el panel de servicio StorSimple Manager y explica cómo usarlo para supervisar el estado de la matriz virtual de StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="v-sharos" />

# Uso del panel de servicio StorSimple Manager para la matriz virtual de StorSimple (vista previa)

## Información general

La página del panel de servicio StorSimple Manager proporciona una vista resumida de las matrices virtuales de StorSimple (también conocidas como dispositivos virtuales o dispositivos virtuales locales de StorSimple) que están conectadas con el servicio StorSimple Manager y resalta las que necesitan la atención de un administrador del sistema. Este tutorial presenta la página del panel, explica el contenido y la función del panel, y describe las tareas que puede realizar desde esta página.

![Panel del servicio](./media/storsimple-ova-service-dashboard/dashboard1.png)

El panel del servicio StorSimple Manager muestra la siguiente información:

- En el área del **gráfico** que se encuentra en la parte superior de la página, puede ver las métricas pertinentes para los dispositivos virtuales. Puede ver el almacenamiento principal que se usa en todos los dispositivos virtuales, así como el almacenamiento en nube que consumen los dispositivos virtuales durante un período de tiempo. Use los controles que aparecen en la esquina superior derecha del gráfico para especificar el uso relativo o absoluto y para ver una escala de tiempo de 1 semana, 1 mes, 3 meses o 1 año. Use el control de actualizaciones ![control-actualizaciones](./media/storsimple-ova-service-dashboard/refresh-control.png) para actualizar el gráfico.

- La sección de **información general del uso** muestra el almacenamiento principal que aprovisionan y consumen todos los dispositivos virtuales en relación con el almacenamiento total disponible en todos los dispositivos virtuales. **Aprovisionado** se refiere a la cantidad de almacenamiento preparado y asignado para su uso, **Usado** se refiere al uso de recursos compartidos o volúmenes, tal como lo ven los iniciadores que están conectados a los dispositivos virtuales y **Capacidad máxima** muestra la capacidad máxima de todos los dispositivos virtuales.

- El área de **alertas** proporciona una instantánea de todas las alertas activas en todos los dispositivos, agrupados por gravedad. Al hacer clic en el nivel de gravedad se abre la página **alertas**, dedicada a mostrar dichas alertas. En la página **alertas**, puede hacer clic en una alerta individual para ver detalles adicionales acerca de ella, incluidas las acciones recomendadas. También puede desactivar la alerta si el problema se ha resuelto.

- El área **trabajos** proporciona una instantánea de los trabajos recientes en todos los dispositivos virtuales que están conectados a su servicio. Hay vínculos que puede usar para ver los trabajos que actualmente están en curso y los que se realizaron correctamente o presentaron errores durante las últimas 24 horas.

- El área de **vista rápida** que aparece a la derecha de la página proporciona información útil, como el estado del servicio, la cantidad total de dispositivos virtuales conectados al servicio, la ubicación del servicio y los detalles de la suscripción asociada con el servicio. También hay un vínculo al registro de operaciones. Haga clic en el vínculo para ver una lista de todas las operaciones completadas del servicio StorSimple Manager.

Puede usar la página del panel del servicio StorSimple Manager para iniciar las siguientes tareas:

- obtención de la clave de registro del servicio.
- Ver los registros de operaciones.

## Obtener la clave de registro del servicio

La clave de registro del servicio se usa para registrar un dispositivo virtual de StorSimple con el servicio StorSimple Manager, para que el dispositivo aparezca en el Portal de Azure clásico para otras acciones de administración. La clave se crea en el primer dispositivo virtual y se comparte con los dispositivos virtuales restantes.

Al hacer clic en **Clave de registro** (en la parte inferior de la página), se abre el cuadro de diálogo **Clave de registro del servicio**, donde puede copiar la clave de registro del servicio actual en el Portapapeles o volver a generar la clave de registro del servicio.

![clave de registro](./media/storsimple-ova-service-dashboard/service-dashboard3.png)

Volver a generar la clave no afecta los dispositivos virtuales registrados anteriormente: solo afecta a los dispositivos virtuales que se registran con el servicio después de volver a generar la clave.

Para obtener más información sobre cómo obtener la clave de registro, vaya a [Obtener la clave de registro del servicio](storsimple-ova-manage-service.md#get-the-service-registration-key).

## Consulta de los registros de operaciones

Para ver los registros de operaciones, haga clic en el vínculo de registros de operaciones disponible en la sección **vista rápida** del panel. Esto le llevará a la página de servicios de administración, donde puede filtrar y ver los registros específicos de su servicio StorSimple Manager.

![Registro de operaciones](./media/storsimple-ova-service-dashboard/ops-log.png)

## Pasos siguientes

Obtener información sobre cómo [usar la interfaz de usuario web local para administrar la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0224_2016-->