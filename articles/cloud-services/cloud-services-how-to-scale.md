<properties 
	pageTitle="Configuración de escalado automático para un servicio en la nube | Microsoft Azure" 
	description="Aprenda a usar el portal para configurar reglas de escalado automático de un servicio en la nube y recursos vinculados en Azure." 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/07/2015"
	ms.author="adegeo"/>





# Escalado automático de una aplicación

En la página Scale del Portal de Azure clásico, puede escalar su aplicación manualmente o puede establecer los parámetros para escalarla automáticamente. Puede escalar aplicaciones que ejecutan roles web, roles de trabajo o máquinas virtuales. Para escalar una aplicación que ejecuta instancias de roles web o roles de trabajo, agregue o quite instancias de roles para acomodar la carga de trabajo.

Al escalar o reducir verticalmente una aplicación que ejecuta máquinas virtuales, no se crean nuevas máquinas ni se eliminan, sino que se activan o desactivan desde un conjunto de disponibilidad de máquinas que se crearon anteriormente. Puede especificar el escalado según el porcentaje promedio de uso de CPU o según la cantidad de mensajes en una cola.

Debe considerar la siguiente información antes de configurar el escalado para su aplicación:

- Las máquinas virtuales que crea debe agregarlas a un conjunto de disponibilidad para escalar una aplicación que las utiliza. Las máquinas virtuales que agrega pueden inicialmente activarse o desactivarse, pero se activarán en una acción de escalado vertical y se desactivarán en una acción de reducción vertical. Para obtener más información sobre las máquinas virtuales y los conjuntos de disponibilidad, consulte [Administración de la disponibilidad de las máquinas virtuales](../virtual-machines-manage-availability.md).

- El uso de núcleos afecta el escalado. Las instancias de rol o las máquinas virtuales más grandes usan más núcleos. Solo puede escalar una aplicación dentro del límite de núcleos para su suscripción. Por ejemplo, si su suscripción tiene un límite de veinte núcleos y ejecuta una aplicación con dos máquinas virtuales de tamaño mediano (un total de cuatro núcleos), solo puede escalar verticalmente otras implementaciones de servicio en la nube en su suscripción con dieciséis núcleos. Todas las máquinas virtuales de un conjunto de disponibilidad que se usan al escalar una aplicación deben tener el mismo tamaño. Para obtener más información acerca del uso de núcleos y los tamaños de las máquinas, consulte [Tamaños de máquinas virtuales y servicios en la nube de Azure](http://msdn.microsoft.com/library/dn197896.aspx).

- Debe crear una cola y asociarla con un rol o conjunto de disponibilidad para poder escalar una aplicación según el umbral de un mensaje. Para obtener más información, consulte [Uso del servicio de almacenamiento de colas](../storage-dotnet-how-to-use-queues.md).

- Puede escalar los recursos que están vinculados con su servicio en la nube. Para obtener más información acerca de los recursos de vinculación, consulte [Vinculación de un recurso a un servicio en la nube](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service).

- Para permitir una alta disponibilidad de la aplicación, debe asegurarse de que se implemente con dos o más instancias de rol o máquinas virtuales. Para obtener más información, consulte [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).


## Escalado manual de una aplicación que ejecuta roles web o roles de trabajo

En la página Scale, puede aumentar o disminuir manualmente la cantidad de instancias en ejecución en un servicio en la nube.

1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Servicios en la nube** y, a continuación, haga clic en el nombre del servicio en la nube para abrir el panel.

2. Haga clic en **Escalar**. El escalado automático está deshabitado de manera predeterminada para todos los roles, lo que significa que puede cambiar manualmente la cantidad de instancias que usa su aplicación.

    ![Página de escala][manual_scale]

3. Cada rol en el servicio en la nube tiene un control deslizante para cambiar la cantidad de instancias que se van a usar. Para agregar una instancia de rol, arrastre la barra hacia la derecha. Para quitar una instancia, arrastre la barra hacia la izquierda.
    
    ![Rol de escalado][slider_role]
    
    Solo puede aumentar la cantidad de instancias que se usan si la cantidad adecuada de núcleos está disponible para admitir las instancias. Los colores del control deslizante representan los núcleos usados y disponibles en su suscripción:
    
    - Azul representa los núcleos usados por el rol seleccionado
    
    - Gris representa los núcleos usados por todos los roles y las máquinas virtuales de la suscripción
    
    - Gris claro representa los núcleos que están disponibles para usarlos en el escalado
    
    - Rosado representa un cambio hecho que no se ha guardado

4. Haga clic en **Guardar**. Las instancias de rol se agregaran o quitarán según las selecciones realizadas.

## Escalado automático de una aplicación que ejecuta roles web, roles de trabajo o máquinas virtuales

En la página Scale, puede configurar su servicio en la nube para aumentar o disminuir automáticamente la cantidad de instancias o máquinas virtuales que usa su aplicación. Puede configurar el escalado según los siguientes parámetros:

- [Uso promedio de CPU](#averagecpu): si el porcentaje promedio de uso de CPU está por encima o por debajo de los umbrales especificados, se crean o eliminan instancias de rol, o bien, las máquinas virtuales se activan o desactivan desde un conjunto de disponibilidad.
- [Mensajes en cola](#queuemessages): si la cantidad de mensajes en una cola está por encima o por debajo de un umbral específico, se crean o eliminan instancias de rol, o bien, las máquinas virtuales se activan o desactivan desde un conjunto de disponibilidad.

## Uso promedio de CPU

1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Servicios en la nube** y, a continuación, haga clic en el nombre del servicio en la nube para abrir el panel.

2. Haga clic en **Escalar**.

3. Desplácese hasta la sección correspondiente al rol o el conjunto de disponibilidad y, a continuación, haga clic en **CPU**. Esto permite el escalado automático de su aplicación, según el porcentaje promedio de recursos de CPU que utiliza.

    ![Escalado automático activado][autoscale_on]

4. Cada rol o conjunto de disponibilidad tiene un control deslizante para cambiar la cantidad de instancias que se pueden usar. Para establecer la cantidad máxima de instancias que se pueden usar, arrastre la barra de la derecha hacia la derecha. Para establecer la cantidad máxima de instancias que se pueden usar, arrastre la barra de la izquierda hacia la izquierda.
    
    **Nota**: en la página Escalar, **Instancia** representa una instancia de rol o una instancia de una máquina virtual.
    
    ![Rango de instancias][instance_range]
    
    La cantidad máxima de instancias está limitada por los núcleos que están disponibles en la suscripción. Los colores del control deslizante representan los núcleos usados y disponibles en su suscripción:
    
    - Azul representa la cantidad máxima de núcleos que puede usar el rol.
    
    - Gris oscuro representa los núcleos que usan todos los roles y las máquinas virtuales de la suscripción Cuando este valor coincide parcialmente con los núcleos usados por el rol, el color cambia a azul oscuro.
    
    - Gris claro representa los núcleos que están disponibles para usarlos en el escalado.
    
    - Rosado representa un cambio hecho que no se ha guardado.

5. Se usa un control deslizante para especificar el rango de porcentaje promedio de uso de CPU. Cuando el porcentaje promedio de uso de CPU está por encima de la configuración máxima, se crean más instancias de rol o se activan más máquinas virtuales. Cuando el porcentaje promedio de uso de CPU está por debajo de la configuración mínima, se eliminan instancias de rol o se desactivan máquinas virtuales. Para establecer el porcentaje promedio máximo de CPU, arrastre la barra de la derecha hacia la derecha. Para establecer el porcentaje promedio mínimo de CPU, arrastre la barra de la izquierda hacia la izquierda.

    ![CPU objetivo][target_cpu]

6. Puede especificar la cantidad de instancias para agregar o activar cada vez que su aplicación se escala verticalmente. Para aumentar la cantidad de instancias que se crean o activan cuando su aplicación se escala verticalmente, arrastre la barra hacia la derecha. Para disminuir la cantidad, arrastre la barra hacia la izquierda.

    ![Escalado vertical de CPU][scale_cpuup]

7. Ajuste la cantidad de minutos para esperar entre la última acción de escalado y la próxima acción de escalado vertical. La última acción de escalado puede ser escalado o reducción vertical.

    ![Tiempo de escalado][scale_uptime]

    Al calcular el porcentaje promedio de uso de CPU se incluyen todas las instancias; el promedio está basado en el uso durante la hora anterior. Según la cantidad de instancias que use su aplicación, la acción de escalado puede tardar más que el tiempo de espera especificado si el tiempo de espera se establece muy bajo. El tiempo mínimo entre las acciones de escalado es de cinco minutos. Las acciones de escalado no se pueden producir si alguna de las instancias se encuentra en estado de transición.

8. Puede también especificar la cantidad de instancias que desea eliminar o desactivar cuando la aplicación se reduzca verticalmente. Para aumentar la cantidad de instancias que se eliminan o desactivan cuando su aplicación se reduce verticalmente, arrastre la barra hacia la derecha. Para disminuir la cantidad, arrastre la barra hacia la izquierda.

    ![Reducción vertical de CPU][scale_cpudown]
    
    Si su aplicación puede tener aumentos repentinos en el uso de CPU, debe asegurarse de que tenga una cantidad de instancias mínima suficiente para administrarlas.

9. Ajuste la cantidad de minutos para esperar entre la última acción de escalado y la próxima acción de reducción vertical. La última acción de escalado puede ser escalado o reducción vertical.

    ![Tiempo de reducción][scale_downtime]

10. Haga clic en **Guardar**. La acción de escalado puede tardar hasta cinco minutos en finalizar.

## Mensajes en cola

1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Servicios en la nube** y, a continuación, haga clic en el nombre del servicio en la nube para abrir el panel.
2. Haga clic en **Escalar**.
3. Desplácese hasta la sección correspondiente al rol o el conjunto de disponibilidad y, a continuación, haga clic en **Cola**. Esto permite el escalado automático de su aplicación según una cantidad objetivo de mensajes en cola.

    ![Cola de escalado][scale_queue]

4. Cada rol o conjunto de disponibilidad en el servicio en la nube tiene un control deslizante para cambiar la cantidad de instancias que se pueden usar. Para establecer la cantidad máxima de instancias que se pueden usar, arrastre la barra de la derecha hacia la derecha. Para establecer la cantidad máxima de instancias que se pueden usar, arrastre la barra de la izquierda hacia la izquierda.

    ![Rango de cola][queue_range]
    
    **Nota**: en la página Escalar, **Instancia** representa una instancia de rol o una instancia de una máquina virtual.
    
    La cantidad máxima de instancias está limitada por los núcleos que están disponibles en la suscripción. Los colores del control deslizante representan los núcleos usados y disponibles en la suscripción: - El azul representa el número máximo de núcleos que puede usar el rol. - El gris oscuro representa los núcleos que usan todos los roles y las máquinas virtuales de la suscripción. Cuando este valor se superpone a los núcleos que usa el rol, el color cambia a azul oscuro. - El gris claro representa los núcleos que están disponibles para usarlos en el escalado. - El rosa representa un cambio que se ha hecho y que no se ha guardado.

5. Seleccione la cuenta de almacenamiento que se asocia con la cola que desea usar.

    ![Nombre de almacenamiento][storage_name]

6. Seleccione la cola.

    ![Nombre de cola][queue_name]

7. Especifique la cantidad de mensajes que espera que admita cada instancia. Las instancias escalan según la cantidad total de mensajes dividida por la cantidad objetivo de mensajes por máquina.

    ![Número de mensajes][message_number]

8. Puede especificar la cantidad de instancias para agregar o activar cada vez que su aplicación se escala verticalmente. Para aumentar la cantidad de instancias que se agregan o activan cuando su aplicación se escala verticalmente, arrastre la barra hacia la derecha. Para disminuir la cantidad, arrastre la barra hacia la izquierda.

    ![Escalado vertical de CPU][scale_cpuup]

9. Ajuste la cantidad de minutos para esperar entre la última acción de escalado y la próxima acción de escalado vertical. La última acción de escalado puede ser escalado o reducción vertical.

    ![Tiempo de escalado][scale_uptime]
    
    El tiempo mínimo entre las acciones de escalado es de cinco minutos. Las acciones de escalado no se pueden producir si alguna de las instancias se encuentra en estado de transición.

10. Puede también especificar la cantidad de instancias que desea eliminar o no utilizar cuando la aplicación se reduzca verticalmente. Se usa un control deslizante para especificar el incremento del escalado. Para aumentar la cantidad de instancias que se eliminan o no se usan cuando su aplicación se reduce verticalmente, arrastre la barra hacia la derecha. Para disminuir la cantidad, arrastre la barra hacia la izquierda.

    ![Reducción vertical de CPU][scale_cpudown]

11.	Ajuste la cantidad de minutos para esperar entre la última acción de escalado y la próxima acción de reducción vertical. La última acción de escalado puede ser escalado o reducción vertical.

    ![Tiempo de reducción][scale_downtime]

12. Haga clic en **Guardar**. La acción de escalado puede tardar hasta cinco minutos en finalizar.

## Escalado de recursos vinculados

A menudo, cuando se escala un rol, es beneficioso también escalar la base de datos que la aplicación está usando. Si vincula la base de datos con el servicio en la nube, puede cambiar la edición de Base de datos SQL y cambiar el tamaño de la base de datos en la página Scale.

1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Servicios en la nube** y, a continuación, haga clic en el nombre del servicio en la nube para abrir el panel.
2. Haga clic en **Escalar**.
3. En la sección Linked Resources, seleccione la edición que desea usar para la base de datos.

    ![Linked Resources][linked_resources]

4. Seleccione el tamaño de la base de datos.
5. Haga clic en **Guardar** para actualizar los recursos vinculados.

## Programación del escalado de su aplicación

Puede programar el escalado automático de su aplicación al configurar programas para diferentes horas. Las siguientes opciones están disponibles para el escalado automático:

- **Ninguna programación**: esta es la opción predeterminada y permite que su aplicación se escale automáticamente de la misma manera todas las veces.

- **Día y noche**: esta opción le permite especificar el escalado para horas específicas del día y noche.

**Nota**: las programaciones no están actualmente disponibles para aplicaciones que usan máquinas virtuales.

1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Servicios en la nube** y, a continuación, haga clic en el nombre del servicio en la nube para abrir el panel.
2. Haga clic en **Escalar**.
3. En la página Escalar, haga clic en **Configurar horas de programación**.

    ![Programar escalado][scale_schedule]

4. Seleccione el tipo de programación de escalado que desea configurar.

5. Especifique las horas de inicio y finalización del día y establezca la zona horaria. Para la programación del día y la noche, las horas representan el inicio y la finalización del día; el resto del tiempo representa la noche.

6. Haga clic en la marca de verificación en la parte inferior de la página para guardar las programaciones.

7. Después de guardar las programaciones, aparecerán en la lista. Puede seleccionar la programación de tiempo que desea usar y luego modificar su configuración de escalado. La configuración de escalado solo se aplicará durante la programación que seleccionó. Puede editar las programaciones al hacer clic en **Configurar horas de programación**.

[manual_scale]: ./media/cloud-services-how-to-scale/CloudServices_ManualScaleRoles.png
[slider_role]: ./media/cloud-services-how-to-scale/CloudServices_SliderRole.png
[autoscale_on]: ./media/cloud-services-how-to-scale/CloudServices_AutoscaleOn.png
[instance_range]: ./media/cloud-services-how-to-scale/CloudServices_InstanceRange.png
[target_cpu]: ./media/cloud-services-how-to-scale/CloudServices_TargetCPURange.png
[scale_cpuup]: ./media/cloud-services-how-to-scale/CloudServices_ScaleUpBy.png
[scale_uptime]: ./media/cloud-services-how-to-scale/CloudServices_ScaleUpWaitTime.png
[scale_cpudown]: ./media/cloud-services-how-to-scale/CloudServices_ScaleDownBy.png
[scale_downtime]: ./media/cloud-services-how-to-scale/CloudServices_ScaleDownWaitTime.png
[scale_queue]: ./media/cloud-services-how-to-scale/CloudServices_QueueScale.png
[queue_range]: ./media/cloud-services-how-to-scale/CloudServices_QueueRange.png
[storage_name]: ./media/cloud-services-how-to-scale/CloudServices_StorageAccountName.png
[queue_name]: ./media/cloud-services-how-to-scale/CloudServices_QueueName.png
[message_number]: ./media/cloud-services-how-to-scale/CloudServices_TargetMessageNumber.png
[linked_resources]: ./media/cloud-services-how-to-scale/CloudServices_ScaleLinkedResources.png
[scale_schedule]: ./media/cloud-services-how-to-scale/CloudServices_SetUpSchedule.png
 

<!---HONumber=AcomDC_0128_2016-->