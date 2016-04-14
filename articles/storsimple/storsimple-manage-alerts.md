<properties 
   pageTitle="Visualización y administración de alertas de StorSimple | Microsoft Azure"
   description="Describe las condiciones de alerta y gravedad de StorSimple, cómo configurar notificaciones de alerta y cómo usar el servicio Administrador de StorSimple para administrar las alertas."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/18/2016"
   ms.author="v-sharos" />

# Usar el servicio de Administrador de StorSimple para ver y administrar alertas de StorSimple

## Información general

La pestaña **Alertas** del servicio de Administrador de StorSimple proporciona una forma de revisar y borrar alertas relacionadas con el dispositivo StorSimple en tiempo real. Desde esta pestaña, puede supervisar de forma centralizada los problemas de estado de los dispositivos StorSimple y la solución general Microsoft Azure StorSimple.

En este tutorial se describen las condiciones de alerta comunes, los niveles de gravedad de alerta y cómo configurar las notificaciones de alerta. Además, incluye tablas de referencia rápida de alertas, que le permiten localizar una alerta específica rápidamente y responder de forma adecuada.

![Página de alertas](./media/storsimple-manage-alerts/HCS_AlertsPage.png)

## Condiciones de alerta comunes

El dispositivo StorSimple genera alertas en respuesta a una serie de condiciones. A continuación se detallan los tipos más comunes de condiciones de alerta:

- **Problemas de hardware** – Estas alertas le informan sobre el estado del hardware. Le permiten saber si son necesarias actualizaciones de firmware, si una interfaz de red tiene problemas o si existe un problema con alguno de sus discos de datos.

- **Problemas de conectividad**: estas alertas se producen cuando existen dificultades para transferir datos. Los problemas de comunicación pueden ocurrir durante la transferencia de datos a y de la cuenta de almacenamiento de Azure o debido a la falta de conectividad entre los dispositivos y el servicio de Administrador de StorSimple. Los problemas de comunicación están entre los más difíciles de solucionar porque existen numerosos puntos de error. En primer lugar, siempre debe verificar que estén disponibles la conectividad de la red y el acceso a Internet antes de pasar a soluciones de problemas más avanzados. Si necesita ayuda para la solución de problemas, vaya a [Solución de problemas con el cmdlet Test-Connection](storsimple-troubleshoot-deployment.md).

- **Problemas de rendimiento**: estas alertas se producen cuando el sistema no tiene un rendimiento óptimo, por ejemplo, cuando está sobrecargado.

Además, podría ver alertas relacionadas con seguridad, actualizaciones o errores en el trabajo.

## Niveles de gravedad de alerta

Las alertas tienen distintos niveles de gravedad, según el impacto que tendrá la situación de alerta y la necesidad de dar respuesta a la misma. Los niveles de gravedad son:

- **Crítica** – Esta alerta es en respuesta a una condición que está afectando el rendimiento correcto del sistema. Se requiere una acción para asegurar que el servicio de StorSimple no se vea interrumpido.

- **Advertencia**: esta condición puede convertirse en crítica si no se resuelve. Debe investigar la situación y tomar las acciones necesarias para resolver el problema.

- **Información**: esta alerta contiene información que puede ser útil para el seguimiento y la administración del sistema.

## Configurar alertas

Puede elegir si desea recibir una notificación por correo electrónico de las condiciones de alerta para cada uno de los dispositivos StorSimple. Además, puede identificar otros destinatarios de notificaciones de alerta mediante la especificación de sus direcciones de correo electrónico en el cuadro **Otros destinatarios de correo electrónico**, separadas por punto y coma.

>[AZURE.NOTE] Puede escribir un máximo de 20 direcciones de correo electrónico por dispositivo.

Después de habilitar la notificación por correo electrónico para un dispositivo, los miembros de la lista de notificación recibirán un mensaje de correo electrónico cada vez que se produzca una alerta crítica. Los mensajes se enviarán desde **storsimple-alerts-noreply@mail.windowsazure.com* y describirán la condición de alerta. Los destinatarios pueden hacer clic en **Eliminar suscripción** para quitarse de la lista de notificación por correo electrónico.

#### Para habilitar la notificación de alertas por correo electrónico de un dispositivo

1. Vaya a **Dispositivos** > **Configurar** en el dispositivo.

2. En **Configuración de alertas**, establezca lo siguiente:

    1. En el campo **Enviar notificación por correo electrónico**, seleccione **SÍ**.

    2. En el campo **Administradores del servicio de correo electrónico**, seleccione **SÍ** si desea que el administrador de servicios y todos los coadministradores reciban las notificaciones de alerta.

    3. En el campo **Otros destinatarios de correo electrónico**, escriba las direcciones de correo electrónico de los demás destinatarios que deben recibir las notificaciones de alerta. Escriba los nombres en el formato **someone@somewhere.com*. Utilice punto y coma para separar las direcciones de correo electrónico. Puede configurar un máximo de 20 direcciones de correo electrónico por dispositivo.

        ![Configuración de notificaciones de alerta](./media/storsimple-manage-alerts/AlertNotify.png)

3. Para enviar una notificación de prueba por correo electrónico, haga clic en el icono de flecha junto a **Enviar correo electrónico de prueba**. El servicio Administrador de StorSimple mostrará mensajes de estado mientras envía la notificación de prueba.

4. Cuando aparece el siguiente mensaje, haga clic en **Aceptar**.

    ![Correo electrónico de notificación de alertas de prueba enviado](./media/storsimple-manage-alerts/HCS_AlertNotificationConfig3.png)

    >[AZURE.NOTE] Si el mensaje de notificación de prueba no puede enviarse, el servicio Administrador de StorSimple mostrará un mensaje adecuado. Haga clic en **Aceptar** y, después de algunos minutos, intente volver a enviar el mensaje de notificación de prueba.

## Ver y realizar un seguimiento de las alertas

El panel del servicio StorSimple Manager le permite ver de un vistazo el número de alertas en sus dispositivos, organizadas por nivel de gravedad.

![Panel de alertas](./media/storsimple-manage-alerts/admin_alerts_dashboard.png)

Al hacer clic en el nivel de gravedad, se abre la pestaña **Alertas**. Los resultados solo incluyen las alertas que coinciden con ese nivel de gravedad.

![Informe de alertas enfocado al tipo de alerta](./media/storsimple-manage-alerts/admin_alerts_scoped.png)

Al hacer clic en la lista, se muestran detalles adicionales de la alerta, incluyendo la última vez que se emitió la alerta, el número de repeticiones de la alerta en el dispositivo y la acción recomendada para resolver la alerta. Si se trata de una alerta de hardware, también identificará el componente de hardware.

![Ejemplo de alerta de hardware](./media/storsimple-manage-alerts/admin_alerts_hardware.png)

Si necesita enviar la información al soporte técnico de Microsoft, puede copiar los detalles de las alertas en un archivo de texto. Después de haber seguido las recomendación y resuelto la condición de alerta en el entorno local, debe borrar la alerta del dispositivo; para ello, selecciónela en la pestaña **Alertas** y haga clic en **Borrar**. Para borrar varias alertas, seleccione cada alerta, haga clic en cualquier columna excepto en la columna **Alerta** y, a continuación, haga clic en **Borrar** después de haber seleccionado todas las alertas que vaya a borrar. Tenga en cuenta que algunas alertas se eliminan automáticamente cuando se resuelve el problema o cuando el sistema actualiza la alerta con nueva información.

Al hacer clic en **Borrar**, tendrá la posibilidad de proporcionar comentarios sobre la alerta y los pasos llevados a cabo para resolver el problema. El sistema borrará algunos eventos si se desencadena otro evento con información nueva. En ese caso, verá el siguiente mensaje.

![Borrar mensaje de alerta](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## Clasificar y revisar alertas

Quizá le resulte más eficiente ejecutar informes de alertas que le permitan revisar y borrar las mismas en grupos. Además, la pestaña **Alertas** puede mostrar hasta 250 alertas. Si se ha superado ese número de alertas, no todas las alertas se mostrarán en la vista predeterminada. Puede combinar los siguientes campos para personalizar las alertas que se muestran:

- **Estado** – Puede mostrar alertas **Activas** o **Desactivadas**. Las alertas activas se siguen desencadenando en el sistema, mientras que las alertas desactivadas han sido borradas de forma manual por un administrador o se han borrado de forma programada porque el sistema actualizó la condición de alerta con nueva información.

- **Gravedad** – Puede mostrar las alertas de todos los niveles de gravedad (crítica, advertencia, información), o solo las de determinada gravedad, como las alertas críticas únicamente.

- **Origen** – Puede mostrar las alertas de todos los orígenes, o limitar las alertas procedentes del servicio, o de uno o todos los dispositivos.

- **Intervalo de tiempo** – Al especificar las fechas **Desde** y **Hasta** y las marcas de tiempo, puede ver las alertas durante el período de tiempo que le interesa.

## Referencia rápida de alertas

Las siguientes tablas enumeran algunas de las alertas de Microsoft Azure StorSimple que pueden encontrarse, así como información adicional y recomendaciones donde estén disponibles. Las alertas del dispositivo StorSimple se clasifican en alguna de las siguientes categorías:

- [Alertas de conectividad de la nube](#cloud-connectivity-alerts)

- [Alertas de clúster](#cluster-alerts)

- [Alertas de recuperación ante desastres](#disaster-recovery-alerts)

- [Alertas de hardware](#hardware-alerts)

- [Alertas de errores de trabajo](#job-failure-alerts)

- [Alertas de volumen anclado localmente](#locally-pinned-volume-alerts)

- [Alertas de red](#networking-alerts)

- [Alertas de rendimiento](#performance-alerts)

- [Alertas de seguridad](#security-alerts)

- [Alertas de paquetes de soporte](#support-package-alerts)

- [Alertas de actualización](#update-alerts)

### Alertas de conectividad de la nube

|Texto de la alerta|Evento|Más información / acciones recomendadas|
|:---|:---|:---|
|No se puede establecer la conectividad a <*nombre de credencial de nube*>.|No es posible conectarse a la cuenta de almacenamiento.|Parece que podría existir un problema de conectividad con su dispositivo. Ejecute el cmdlet `Test-HcsmConnection` desde la interfaz de Windows PowerShell para StorSimple en su dispositivo para identificar y corregir el problema. Si la configuración es correcta, puede que el problema sea con las credenciales de la cuenta de almacenamiento por la que se desencadenó la alarma. En este caso, use el cmdlet `Test-HcsStorageAccountCredential` para determinar si existen problemas que pueda resolver.<ul><li>Compruebe la configuración de red.</li><li>Compruebe las credenciales de su cuenta de almacenamiento.</li></ul>|
|No hemos recibido ningún latido de su dispositivo durante los últimos <*número*> minutos.|No es posible conectarse al dispositivo.|Parece que existe un problema de conectividad con su dispositivo. Use el cmdlet `Test-HcsmConnection` de la interfaz de Windows PowerShell para StorSimple en su dispositivo para identificar y corregir el problema, o póngase en contacto con su administrador de red.|

### Comportamiento de StorSimple cuando se produce un error de conectividad de la nube

¿Qué sucede si se produce un error de conectividad de la nube para mi dispositivo StorSimple que se ejecuta en producción?

Si se produce un error en la conectividad de la nube en el dispositivo de producción de StorSimple, según el estado del dispositivo, puede ocurrir lo siguiente:

- **Para los datos locales en el dispositivo**: durante algún tiempo, no se producirá ninguna interrupción y las lecturas se seguirán atendiendo. Sin embargo, cuando el número de operaciones de E/S pendientes aumenta y excede el límite, las lecturas pueden comenzar a fallar. 

	Según la cantidad de datos en el dispositivo, las escrituras seguirán también produciéndose en las primeras horas después de la interrupción de la conectividad de la nube. Las escrituras se ralentizarán y finalmente comenzarán a fallar si se interrumpe la conectividad de la nube durante varias horas. (Hay almacenamiento temporal en el dispositivo para los datos que se van a insertar en la nube. Este área se vacía cuando se envían los datos. Si se produce un error en la conectividad, los datos de este área de almacenamiento no se insertarán en la nube y se producirá un error de E/S).

 
- **Para los datos en la nube**: en la mayoría de los errores de conectividad de la nube, se devuelve un error. Una vez restaurada la conectividad, se reanudarán las operaciones de E/S sin que el usuario tiene que incorporar el volumen en línea. En raras ocasiones, podría ser necesaria la intervención del usuario para recuperar el volumen en línea desde el Portal de Azure clásico.
 
- **Para las instantáneas de nube en curso**: la operación se reintenta varias veces en 4 o 5 horas y, si no se restaura la conectividad, se produce un error en las instantáneas de nube.


### Alertas de clúster

|Texto de la alerta|Evento|Más información / acciones recomendadas|
|:---|:---|:---|
|Conmutación por error de dispositivo a <*nombre de dispositivo*>.|El dispositivo está en modo de mantenimiento.|Conmutación por error de dispositivo debido a entrada o salida del modo de mantenimiento. Esto es normal y no se requiere ninguna acción. Después de confirmar esta alerta, bórrela de la página de alertas.|
|Conmutación por error de dispositivo a <*nombre de dispositivo*>.|Se actualizó recientemente el firmware o software del dispositivo.|Se produjo una conmutación por error de clúster debido a una actualización. Esto es normal y no se requiere ninguna acción. Después de confirmar esta alerta, bórrela de la página de alertas.|
|El dispositivo conmutó por error a <*nombre del dispositivo*>.|Se apagó o reinició el controlador.|Conmutación por error de dispositivo debido al apagado o reinicio del controlador por parte de un administrador. No se requiere ninguna acción. Después de confirmar esta alerta, bórrela de la página de alertas.|
|El dispositivo conmutó por error a <*nombre del dispositivo*>.|Conmutación por error planeada.|Verifique que se trató de una conmutación por error planeada. Después de realizar la acción apropiada, borre esta alerta de la página de alertas.|
|El dispositivo conmutó por error a <*nombre del dispositivo*>.|Conmutación por error no planeada.|StorSimple está diseñado para recuperarse automáticamente de las conmutaciones por error no planeadas. Si ve un gran número de estas alertas, póngase en contacto con el soporte técnico de Microsoft.|
|El dispositivo conmutó por error a <*nombre del dispositivo*>.|Otras causas/causas desconocidas.|Si ve un gran número de estas alertas, póngase en contacto con el soporte técnico de Microsoft. Después de resolver el problema, borre esta alerta de la página de alertas.|
|Un servicio de dispositivo crítico indica un estado error.|Error del servicio de ruta de datos. |Para recibir asistencia, póngase en contacto con el servicio de soporte técnico de Microsoft.|
|La dirección IP virtual de la interfaz de red <*DATA #*> indica un estado de error. |Otras causas/causas desconocidas. |A veces las condiciones temporales pueden causar estas alertas. Si es el caso, esta alerta se borrará automáticamente después de un tiempo. Si el problema persiste, póngase en contacto con el Soporte técnico de Microsoft.|
|La dirección IP virtual de la interfaz de red <*DATA #*> indica un estado de error.|Nombre de la interfaz: <*DATA #*> La dirección IP <IP address> no se puede poner en línea porque se ha detectado una dirección IP duplicada en la red. |Asegúrese de que la dirección IP duplicada se quita de la red o vuelva a configurar la interfaz con una dirección IP diferente.|


### Alertas de recuperación ante desastres

|Texto de la alerta|Evento|Más información / acciones recomendadas|
|:---|:---|:---|
|Las operaciones de recuperación no lograron restaurar todos los valores de configuración para este servicio. Los datos de configuración de dispositivo están en un estado incoherente para algunos dispositivos.|Incoherencia de datos detectada después de la recuperación ante desastres.|Los datos cifrados en el servicio no están sincronizados con los del dispositivo. Autorice el dispositivo <*nombre del dispositivo*> desde StorSimple Manager para iniciar el proceso de sincronización. Use la interfaz de Windows PowerShell para StorSimple para ejecutar el cmdlet `Restore-HcsmEncryptedServiceData` en el dispositivo <*nombre del dispositivo*>, y proporcione la contraseña anterior como entrada a este cmdlet para restaurar el perfil de seguridad. Después, ejecute el cmdlet `Invoke-HcsmServiceDataEncryptionKeyChange` para actualizar la clave de cifrado de datos de servicio. Después de realizar la acción apropiada, borre esta alerta de la página de alertas.|


### Alertas de hardware

|Texto de la alerta|Evento|Más información / acciones recomendadas|
|:---|:---|:---|
|El componente de hardware <*id. de componente*> notifica el estado como <*estado*>.||A veces las condiciones temporales pueden causar estas alertas. Si es el caso, esta alerta se borrará automáticamente después de un tiempo. Si el problema persiste, póngase en contacto con el Soporte técnico de Microsoft.|
|Funcionamiento incorrecto del controlador pasivo.|El controlador pasivo (secundario) no funciona.|El dispositivo está operativo, pero uno de los controladores no funciona. Intente reiniciar ese controlador. Si el problema persiste, póngase en contacto con el soporte técnico de Microsoft.|

### Alertas de errores de trabajo

|Texto de la alerta|Evento|Más información / acciones recomendadas|
|:---|:---|:---|
|Error de copia de seguridad de <*Id. de grupo de volúmenes de origen*>|Error de trabajo de copia de seguridad.|Podrían existir problemas de conectividad que impiden que la operación de copia de seguridad se complete correctamente. Si no hay problemas de conectividad, podría haber alcanzado el número máximo de copias de seguridad. Elimine las copias de seguridad que ya no sean necesarias y vuelva a intentar la operación. Después de realizar la acción apropiada, borre esta alerta de la página de alertas.|
|Error de clonación de <*id. de elemento de copia de seguridad de origen*> a <*números de serie de volúmenes de destino*>.|Error de trabajo de clonación.|Actualice la lista de copias de seguridad para verificar que la copia de seguridad siga siendo válida. Si la copia de seguridad es válida, es posible que existan problemas de conectividad de nube que impidan que la operación de clonación se complete correctamente. Si no hay problemas de conectividad, podría haber alcanzado el límite de almacenamiento. Elimine las copias de seguridad que ya no sean necesarias y vuelva a intentar la operación. Después de realizar la acción apropiada para resolver el problema, borre esta alerta de la página de alertas.|
|Error de restauración de <*id. de elemento de copia de seguridad de origen*>.|Error de trabajo de restauración.|Actualice la lista de copias de seguridad para verificar que la copia de seguridad siga siendo válida. Si la copia de seguridad es válida, es posible que existan problemas de conectividad de nube que impidan que la operación de restauración se complete correctamente. Si no hay problemas de conectividad, podría haber alcanzado el límite de almacenamiento. Elimine las copias de seguridad que ya no sean necesarias y vuelva a intentar la operación. Después de realizar la acción apropiada para resolver el problema, borre esta alerta de la página de alertas.|

### Alertas de volumen ancladas localmente

|Texto de la alerta|Evento|Más información / acciones recomendadas|
|:---|:---|:---|
|Error de creación de un volumen local <*nombre de volumen*>.| Error del trabajo de creación de volumen. <*Mensaje de error correspondiente al código de error de incorrecto*>.|Puede que existan problemas de conectividad que impidan que la operación de creación de espacio se complete correctamente. Los volúmenes anclados localmente están aprovisionados densamente y el proceso de creación de espacio supone escribir los volúmenes en capas en la nube. Si no hay ningún problema de conectividad, puede que haya agotado el espacio local en el dispositivo. Determine si hay espacio en el dispositivo antes de volver a intentar esta operación.|
|Error de expansión del volumen local <*nombre de volumen*>.|Error del trabajo de modificación del volumen debido a <*mensaje de error correspondiente al código de error de incorrecto*>.|Puede que existan problemas de conectividad que impidan que la operación de expansión del volumen se realice correctamente. Los volúmenes anclados localmente están aprovisionados densamente y el proceso de ampliar el espacio existente supone escribir los volúmenes en capas en la nube. Si no hay ningún problema de conectividad, puede que haya agotado el espacio local en el dispositivo. Determine si hay espacio en el dispositivo antes de volver a intentar esta operación.|
|Error de conversión de volumen <*nombre de volumen*>.|El trabajo de conversión de volumen para convertir el tipo de volumen anclado localmente en un volumen en capas ha dado error.|La conversión del volumen anclado localmente en un volumen en capas no se ha podido realizar. Asegúrese de que no haya ningún problema de conectividad que impida que la operación se realice correctamente. Para solucionar los problemas de conectividad, vaya a [Solución de problemas con el cmdlet Test-HcsmConnection](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>El volumen original anclado localmente ahora se ha marcado como un volumen en capas, dado que algunos de sus datos se han escrito en la nube durante la conversión. El volumen en capas resultante todavía ocupa espacio local en el dispositivo que no se puede reclamar para futuros volúmenes locales.<br>Solucione los problemas de conectividad, borre la alerta y vuelva a convertir este volumen en anclado localmente para garantizar que todos los datos vuelven a estar disponibles localmente.|
|Error de conversión de volumen <*nombre de volumen*>.|El trabajo de conversión de volumen para convertir el tipo de volumen en capas en un volumen anclado localmente ha dado error.|La conversión del volumen en capas a un volumen anclado localmente no se ha podido realizar. Asegúrese de que no haya ningún problema de conectividad que impida que la operación se realice correctamente. Para solucionar los problemas de conectividad, vaya a [Solución de problemas con el cmdlet Test-HcsmConnection](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>El volumen original en capas marcado ahora como volumen anclado localmente como parte del proceso de conversión, sigue teniendo datos que residen en la nube, aunque el espacio densamente aprovisionado en el dispositivo para este volumen ya no se puede reclamar para futuros volúmenes locales.<br>Solucione los problemas de conectividad, borre la alerta y vuelva a convertir este volumen en el tipo de volumen en capas original para asegurarse de que se pueda reclamar el espacio local densamente aprovisionado en el dispositivo.|
|A punto de consumir el espacio local para instantáneas locales de <*nombre del grupo de volumen*>|Las instantáneas locales de la directiva de copia de seguridad podrían quedarse pronto sin espacio e invalidarse para evitar errores de escritura del host.|La existencia de instantáneas locales frecuentes junto con una alta actividad de datos en los volúmenes asociados a este grupo de directiva de copia de seguridad están haciendo que el espacio local en el dispositivo se consuma rápidamente. Elimine las instantáneas locales que no sean necesarias. Asimismo, actualice las programaciones de instantáneas locales de esta directiva de copia de seguridad para que se tomen las instantáneas locales menos frecuentes, y asegúrese de que las instantáneas de nube se toman con regularidad. Si no se realizan estas acciones, el espacio local de estas instantáneas puede agotarse pronto y el sistema las eliminará automáticamente para asegurarse de que las escrituras del host se siguen procesando correctamente.|
|Las instantáneas locales para <*nombre del grupo de volumen*> se han invalidado.|Las instantáneas locales para <*nombre del grupo de volumen*> se han invalidado y, luego, se han eliminado porque superaban el espacio local en el dispositivo.|Para asegurarse de que esto no vuelva a pasar en un futuro, revise las programaciones de instantáneas locales de esta directiva de copia de seguridad y elimine las instantáneas locales que ya no sean necesarias. La existencia de instantáneas locales frecuentes junto con una alta actividad de datos en los volúmenes asociados a este grupo de directiva de copia de seguridad pueden hacer que el espacio local en el dispositivo se consuma rápidamente.|
|Error de restauración de <*id. de elemento de copia de seguridad de origen*>.|Error del trabajo de restauración.|Si tiene volúmenes anclados localmente o una mezcla de volúmenes anclados localmente y volúmenes en capas en esta directiva de copia de seguridad, actualice la lista de copias de seguridad para comprobar que la copia de seguridad sigue siendo válida. Si la copia de seguridad es válida, es posible que existan problemas de conectividad de nube que impidan que la operación de restauración se complete correctamente. Los volúmenes anclados localmente que se estaban restaurando como parte de este grupo de instantáneas no han descargados todos sus datos en el dispositivo y, si tiene una mezcla de volúmenes anclados localmente y en capas en este grupo de instantáneas, no se sincronizarán entre sí. Para completar correctamente la operación de restauración, desconecte los volúmenes de este grupo en el host y vuelva a intentar la operación de restauración. Tenga en cuenta que las modificaciones realizadas en los datos del volumen durante el proceso de restauración se perderán.|

### Alertas de red

|Texto de la alerta|Evento|Más información / acciones recomendadas|
|:---|:---|:---|
|No se han podido iniciar los servicios de StorSimple.|Error de ruta de datos |Si el persiste el problema, póngase en contacto con el Soporte técnico de Microsoft.|
|Se ha detectado una dirección IP duplicada para 'Data0'.| |El sistema ha detectado un conflicto para la dirección IP '10.0.0.1'. El recurso de red 'Data0' en el dispositivo *<device1>* está sin conexión. Asegúrese de que ninguna otra entidad de esta red utilice esta dirección IP. Para solucionar los problemas de red, vaya a [Solución de problemas con el cmdlet Get-NetAdapter](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Para resolver este problema, póngase en contacto con el administrador de red. Si el persiste el problema, póngase en contacto con el Soporte técnico de Microsoft. |
|La dirección IPv4 (o IPv6) para 'Data0' está sin conexión.| |El recurso de red 'Data0' con la dirección IP '10.0.0.1.' y un prefijo de longitud '22' en el dispositivo *<device1>* está sin conexión. Asegúrese de que los puertos de conmutador a los que está conectada esta interfaz están operativos. Para solucionar los problemas de red, vaya a [Solución de problemas con el cmdlet Get-NetAdapter](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
 

### Alertas de rendimiento

|Texto de la alerta|Evento|Más información / acciones recomendadas|
|:---|:---|:---|
|La carga del dispositivo ha superado el <*umbral*>.|Tiempos de respuesta más lentos que lo esperado.|El dispositivo indica su utilización con una carga elevada de entrada/salida. Esto podría provocar que el dispositivo no funcione tan bien como debería. Revise las cargas de trabajo que ha conectado al dispositivo y determine si hay alguna que se pueda mover a otro dispositivo o que ya no sea necesaria.<br>Para comprender el estado actual, vaya a [Uso del servicio StorSimple Manager para supervisar el dispositivo StorSimple](storsimple-monitor-device.md).|

### Alertas de seguridad

|Texto de la alerta|Evento|Más información / acciones recomendadas|
|:---|:---|:---|
|Se ha iniciado una sesión de soporte técnico de Microsoft Support.|Un tercero ha accedido a la sesión de soporte técnico.|Confirme que este acceso está autorizado. Después de realizar la acción apropiada, borre esta alerta de la página de alertas.|
|La contraseña de <*elemento*> caducará en <*período de tiempo*>.|La caducidad está a punto de caducar.|Cambiar la contraseña antes de que caduque.|
|Falta información de la configuración de seguridad para <*id. de elemento*>.||Los volúmenes asociados con este contenedor de volúmenes no pueden utilizarse para replicar la configuración de StorSimple. Para asegurarse de que sus datos se almacenan de forma segura, recomendamos eliminar el contenedor de volúmenes y los volúmenes asociados con el contenedor de volúmenes. Después de realizar la acción apropiada, borre esta alerta de la página de alertas.|
|<*número*> intentos de inicio de sesión incorrectos para <*id. de elemento*>.|Múltiples intentos de inicio de sesión erróneos.|Su dispositivo puede estar bajo ataque o un usuario autorizado está intentando conectarse con una contraseña incorrecta.<ul><li>Póngase en contacto con los usuarios autorizados y verifique que estos intentos proceden de un origen legítimo. Si continúa viendo un gran número de intentos de inicio de sesión erróneos, considere la posibilidad de deshabilitar la administración remota y de ponerse en contacto con su administrador de red. Después de realizar las acción apropiada, borre esta alerta de la página de alertas.</li><li>Compruebe que las instancias de Snapshot Manager estén configuradas con la contraseña correcta. Después de realizar la acción apropiada, borre esta alerta de la página de alertas.</li></ul>Para más información, vaya a [Cambio de una contraseña de dispositivo caducada](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password).|
|Se ha producido uno o más errores al cambiar la clave de cifrado de datos del servicio.||Se encontraron errores al cambiar la clave de cifrado de datos del servicio. Después de solucionar las condiciones de error, ejecute el cmdlet `Invoke-HcsmServiceDataEncryptionKeyChange` desde la interfaz de Windows PowerShell para StorSimple en su dispositivo para actualizar el servicio. Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Microsoft. Cuando haya solucionado el problema, borre esta alerta de la página de alertas.|

### Alertas de paquetes de soporte

|Texto de la alerta|Evento|Más información / acciones recomendadas|
|:---|:---|:---|
|Error de creación de paquete de soporte técnico.|StorSimple no pudo generar el paquete.|Vuelva a intentarlo. Si el problema persiste, póngase en contacto con el Soporte técnico de Microsoft. Cuando haya solucionado el problema, borre esta alerta de la página de alertas.|

### Alertas de actualización

|Texto de la alerta|Evento|Más información / acciones recomendadas|
|:---|:---|:---|
|Revisión instalada.|Actualización de software o firmware completada.|La revisión se instaló correctamente en el dispositivo.|
|Actualizaciones manuales disponibles.|Notificación de actualizaciones disponibles.|Utilice la interfaz de Windows PowerShell para StorSimple de su dispositivo para instalar estas actualizaciones. <br>Para más información, vaya a [Actualización del dispositivo de la serie StorSimple 8000](storsimple-update-device.md).|
|Nuevas actualizaciones disponibles.|Notificación de actualizaciones disponibles.|Puede instalar estas actualizaciones desde la página **Mantenimiento** o mediante la interfaz de Windows PowerShell para StorSimple de su dispositivo. <br>Para más información, vaya a [Actualización del dispositivo de la serie StorSimple 8000](storsimple-update-device.md).|
|Error al instalar las actualizaciones.|Las actualizaciones no se instalaron correctamente.|El sistema no pudo instalar las actualizaciones. Puede instalar estas actualizaciones desde la página **Mantenimiento** o mediante la interfaz de Windows PowerShell para StorSimple de su dispositivo. Si el problema persiste, póngase en contacto con el Soporte técnico de Microsoft. <br>Para más información, vaya a [Actualización del dispositivo de la serie StorSimple 8000](storsimple-update-device.md).|
|No se puede comprobar automáticamente si hay nuevas actualizaciones.|Error de comprobación automática.|Puede comprobar manualmente si hay nuevas actualizaciones desde la página **Mantenimiento**.|
|Nuevo agente WUA disponible.|Notificación de actualización disponible.|Descargue la versión más reciente del Agente de Windows Update e instálela desde la interfaz de Windows PowerShell.|
|La versión del componente de firmware <*Id. de componente*> no coincide con el hardware.|Las actualizaciones de firmaware no se instalaron correctamente.|Póngase en contacto con el soporte técnico de Microsoft|

## Pasos siguientes

Aprenda más sobre [ los errores de StorSimple y la solución de problemas de un dispositivo operativo](storsimple-troubleshoot-operational-device.md).

<!---HONumber=AcomDC_0224_2016-->