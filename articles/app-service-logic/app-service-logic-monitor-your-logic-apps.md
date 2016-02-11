<properties 
	pageTitle="Supervisar sus aplicaciones lógicas en el Servicio de aplicaciones de Azure | Microsoft Azure" 
	description="Visualización de lo que han hecho las aplicaciones lógicas" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/07/2015"
	ms.author="stepsic"/>

# Supervisión de las aplicaciones lógicas

Después de crear una aplicación lógica (pasos en [Crear una aplicación lógica](app-service-logic-create-a-logic-app.md)), puede ver el historial completo de su ejecución en el Portal de Azure. Para ver el historial, seleccione **Examinar**, **Web + móvil** y **Aplicaciones lógicas**. Aparecerá una lista con todas las aplicaciones lógicas incluidas en la suscripción. Las aplicaciones lógicas se pueden **habilitar** o **deshabilitar**. Las aplicaciones lógicas **habilitadas** indican que los desencadenadores ejecutan su aplicación lógica en respuesta a los efectos desencadenantes. Las aplicaciones lógicas **deshabilitadas** no se ejecutan en respuesta a eventos.

![Información general](./media/app-service-logic-monitor-your-logic-apps/overview.png)

En la hoja de la aplicación lógica, hay dos secciones importantes:

- **Resumen**: indica el estado más reciente y permite editar la aplicación lógica.
- **Todas las ejecuciones**: muestra una lista de las ejecuciones que ha tenido esta aplicación lógica.

## Ver las ejecuciones de la aplicación

![Todas las ejecuciones](./media/app-service-logic-monitor-your-logic-apps/allruns.png)

Esta lista de ejecuciones muestra los campos **Hora de inicio**, **Identificador de ejecuciones** (puede usar este valor para hacer llamadas a la API de REST) y **Duración** de las ejecución concreta. Seleccione cualquier fila para ver detalles sobre esa ejecución.

La hoja de detalles muestra un gráfico con la hora de la ejecución y la secuencia de todas las acciones de la ejecución. A continuación aparece la lista completa de todas las acciones ejecutadas:

![Ejecuciones y acciones](./media/app-service-logic-monitor-your-logic-apps/runandaction.png)

Por último, sobre una acción concreta, puede obtener todos los datos que se pasaron a la acción y que se recibieron de esta en las secciones **Entradas** y **Salidas**. Seleccione los vínculos para ver el contenido completo (también puede copiar los vínculos para descargar el contenido).

Otro fragmento importante de la información es el **Id. de seguimiento**. Este identificador se pasa en los encabezados de todas las llamadas de acción. Si ya se ha registrado en su propio servicio, se recomienda registrar el identificador de seguimiento y, a continuación, puede hacer una referencia cruzada a sus propios registros con este identificador.

## Ver el historial de desencadenamiento 

Los desencadenadores de sondeo comprueban la API cada ciertos intervalos, pero no inician necesariamente una ejecución, según la respuesta (por ejemplo, `200` implica realizar una ejecución y `202` no realizarla). El historial del desencadenador permite ver todas las llamadas que se producen, pero que no ejecutan aplicación lógica (las respuestas `202`):

![Historial de desencadenamiento](./media/app-service-logic-monitor-your-logic-apps/triggerhistory.png)

Para cada desencadenador, puede ver si se **activa**, si no se activa, o si ha tenido algún tipo de error (ha dado **Error**). Para inspeccionar por qué el desencadenador dio error, seleccione el vínculo **Salidas**. Si se ha activado, seleccione el vínculo **Ejecutar** para ver qué sucedió después de que se activara.

Tenga en cuenta que para los desencadenadores de *inserción*, *no* ve las veces que comenzaron aquí las ejecuciones. En su lugar, ve las llamadas del *registro de devolución de llamada*, que son cuando se registra la aplicación lógica para la devolución de llamada. Si el desencadenador de inserción no funciona, puede haber un problema con el registro (que puede ver en Salidas); si no es así, puede que deba investigar esa API en concreto.

## Habilitar el control de versiones

Existe una capacidad adicional que actualmente no es posible en la interfaz de usuario (lo será pronto), pero que está disponible con la [API de REST](http://go.microsoft.com/fwlink/p/?LinkID=525617&clcid=0x409). Al actualizar la definición de una aplicación lógica, se almacena la versión anterior de la definición. Esto se hace así porque si ya tiene una ejecución en curso, dicha ejecución hace referencia a la versión de la aplicación lógica que existía cuando se inició la ejecución. Las definiciones de las ejecuciones no se pueden cambiar mientras están en curso. El historial de versiones de la API de REST proporciona acceso a esta información.
 

<!---HONumber=AcomDC_1210_2015-->