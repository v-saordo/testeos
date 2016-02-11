<properties
	pageTitle="Detección, evaluación de errores y diagnóstico"
	description="Analice los bloqueos y detecte y diagnostique problemas de rendimiento en sus aplicaciones."
	authors="alancameronwills"
	services="application-insights"
    documentationCenter=""
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="11/06/2015"
	ms.author="awills"/>

# Detección, evaluación de errores y diagnóstico con Application Insights

*Application Insights se encuentra en su versión de vista previa.*


Application Insights ayuda a averiguar cómo está funcionando su aplicación y cómo se está usando cuando está activa. Y si hay un problema, le permite tener información sobre él y ayuda a evaluar el impacto y a determinar la causa.

A continuación se muestra una cuenta de un equipo que desarrolla aplicaciones web:

* *"Hace un par de días implementamos una revisión 'secundaria'. No ejecutamos un prueba superada amplia, pero por desgracia algunos cambios inesperados se combinaron en la carga, lo que provocó incompatibilidad entre el front-end y el back-end. Inmediatamente, surgieron excepciones de servidor, nuestra alerta se disparó y nos hicimos conscientes de la situación. Con unos cuantos clics en el portal de Application Insights, teníamos suficiente información de las pilas de llamada de excepción para acotar el problema. Inmediatamente revertimos y limitamos el daño. Application Insights ha hecho que esta parte del ciclo de desarrollo sea muy fácil y simplifica la acción".*

Veamos cómo un equipo de desarrollo web típico usa Application Insights para supervisar el rendimiento. Seguiremos al equipo de Fabrikam Bank que desarrolla el sistema de banca en línea (OBS).

![Sitio web de un banco de ejemplo](./media/app-insights-detect-triage-diagnose/03-bank.png)

El equipo trabaja según este ciclo:

![Ciclo de DevOps](./media/app-insights-detect-triage-diagnose/00-devcycle.png)

Los requisitos son la fuente del trabajo pendiente de desarrollo (lista de tareas). Trabajan en sprints cortos, que a menudo entregan software que funciona (normalmente en forma de mejoras y extensiones de la aplicación existente). La aplicación activa se actualiza frecuentemente con nuevas características. Mientras está activa, el equipo supervisa su rendimiento y uso con la ayuda de Application Insights. Este análisis retroalimenta su trabajo pendiente de desarrollo.

El equipo usa Application Insights para supervisar estrechamente la aplicación web activa para: * Rendimiento. Desean comprender cómo varían los tiempos de respuesta con el número de solicitudes; cuánto se está usando la CPU, la red, el disco y otros recursos; y dónde están los cuellos de botella. * Errores. Si hay excepciones o errores de solicitud, o si un contador de rendimiento se sale de su intervalo habitual, el equipo necesita tener conocimiento de ello rápidamente para que pueda llevar a cabo la acción correspondiente. * Uso. Siempre que se publique una nueva característica, el equipo desea saber en qué medida se usa y si los usuarios tienen dificultades con ella.



Centrémonos en la parte de comentarios del ciclo:

![Detect-Triage-Diagnose](./media/app-insights-detect-triage-diagnose/01-pipe1.png)



## Detección de disponibilidad insuficiente


Marcela Markova es especialista en pruebas del equipo OBS y es responsable de supervisar el rendimiento en línea. Para tal fin, prepara varias [pruebas web][availability]\:

* Primero, una prueba con una sola dirección URL para la página de aterrizaje principal de la aplicación. http://fabrikambank.com/onlinebanking/. Establece los criterios de código HTTP 200 y el texto 'Welcome!'. Si se produce un error en esta prueba, es que sucede algo grave con la red o los servidores o quizás sea un problema de implementación. (O bien, alguien ha cambiado el mensaje Welcome! en la página sin que ella lo sepa).


* Una prueba de varios paso más profunda, que inicia una sesión y obtiene una lista de cuentas actuales, y donde se comprueban algunos detalles importantes en cada página. Esta prueba comprueba que funciona el vínculo a la base de datos de cuentas. Marcela usa un identificador de cliente ficticio: se mantienen algunos de ellos con fines de prueba.


Con estas pruebas preparadas, está segura de que el equipo se enterará rápidamente de cualquier interrupción.


Los errores se muestran como puntos rojos en el gráfico de pruebas web:

![Presentación de pruebas web que se han ejecutado durante el período anterior](./media/app-insights-detect-triage-diagnose/04-webtests.png)


Pero lo más importante, se enviará por correo electrónico una alerta sobre cualquier error al equipo de desarrollo. De esa manera, estarán informados antes casi que todos los clientes.


## Supervisión de las métricas de rendimiento.


En la misma página de información general de Application Insights, hay un gráfico que muestra diversas [métricas clave][perf].

![Varias métricas](./media/app-insights-detect-triage-diagnose/05-perfMetrics.png)

El tiempo de carga de la página del explorador se obtiene de la telemetría enviada directamente desde las páginas web. El tiempo de respuesta del servidor, el número de solicitudes del servidor y el número de solicitudes con error se miden en el servidor web y se envían a Application Insights desde allí.


El número de solicitudes con error indica los casos donde los usuarios han visto un error, normalmente a continuación de una excepción en el código. Quizás verán un mensaje que dice "Lo sentimos, no pudimos actualizar sus datos ahora" o, en el peor de los casos, un volcado de la pila en la pantalla del usuario, cortesía del servidor web.


A Marcela le gusta mirar estos gráficos de vez en cuando. La ausencia de solicitudes con error es alentadora, aunque cuando cambia el intervalo del gráfico para cubrir la semana pasada, aparecen errores ocasionales. Esto es un nivel aceptable en un servidor ocupado. Pero si se produce un salto repentino en los errores o en algunas de las demás métricas como el tiempo de respuesta del servidor, Marcela desea saberlo inmediatamente. Esto podría indicar un problema imprevisto causado por una versión de código o un error en una dependencia, como una base de datos, o quizás una reacción sin gracia a una carga elevada de solicitudes.

#### Alertas

Así que establece dos [alertas][metrics]\: una para tiempos de respuesta superiores a un umbral típico y otro para una tasa de solicitudes con error mayor que el fondo actual.


Junto con la alerta de disponibilidad, estas le proporcionan la seguridad de que tendrá información al respecto tan pronto ocurra algo inusual.


También es posible establecer alertas para una amplia variedad de otras métricas. Por ejemplo, puede recibir mensajes de correo electrónico si el recuento de excepciones se vuelve alto, o si la memoria disponible baja, o si se produce un pico en las solicitudes de cliente.



![Agregar hoja de alertas](./media/app-insights-detect-triage-diagnose/07-alerts.png)




## Detección de excepciones


Con un poco de configuración, se notifican [excepciones](app-insights-asp-net-exceptions.md)automáticamente a Application Insights. También se pueden capturar de manera explícita mediante la inserción de llamadas a [TrackException()](app-insights-api-custom-events-metrics.md#track-exception) en el código:

    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }


El equipo de Fabrikam Bank ha evolucionado la práctica de enviar siempre telemetría en una excepción, a menos que haya una recuperación obvia.

De hecho, su estrategia es incluso más amplia que eso: envían telemetría en todos los casos donde el cliente se siente frustrado por lo quería hacer, ya corresponda a una excepción en el código o no. Por ejemplo, si un sistema externo de transferencia interbancaria devuelve un mensaje «no se puede realizar esta transacción» por algún motivo operativo (no es error del cliente), entonces realizan el seguimiento de ese evento.

    var successCode = AttemptTransfer(transferAmount, ...);
    if (successCode < 0)
    {
       var properties = new Dictionary <string, string>
            {{ "Code", returnCode, ... }};
       var measurements = new Dictionary <string, double>
         {{"Value", transferAmount}};
       telemetry.TrackEvent("transfer failed", properties, measurements);
    }

TrackException se usa para informar de excepciones porque envía una copia de la pila; TrackEvent se emplea para informar de otros eventos. Puede vincular cualquier propiedad que podría resultar útil en el diagnóstico.

Las excepciones y los eventos se muestran en la hoja [Búsqueda de diagnóstico][diagnostic]. Puede profundizar en ellos para ver propiedades adicionales y el seguimiento de la pila.

![En la búsqueda de diagnóstico, utilice filtros para mostrar determinados tipos de datos.](./media/app-insights-detect-triage-diagnose/appinsights-333facets.png)

## Seguimiento de la actividad de usuario

Cuando el tiempo de respuesta es bueno de manera coherente y hay algunas excepciones, el equipo de desarrollo puede pensar en cómo mejorar la experiencia del usuario y en cómo animar a más usuarios a lograr los objetivos deseados.


Por ejemplo, un viaje de usuario típico a través del sitio web tiene un claro 'embudo': muchos clientes examinan las tasas de diferentes tipos de préstamo; algunos de ellos rellenan el formulario de presupuesto; y de aquellos que reciben un presupuesto, algunos siguen adelante y obtienen el préstamo.

![](./media/app-insights-detect-triage-diagnose/12-funnel.png)

Teniendo en cuenta dónde abandonan los mayores números de clientes, la empresa puede calcula cómo conseguir que más usuarios pasen a la parte inferior del embudo. En algunos casos se puede producir en la experiencia de usuario (UX) experiencia: por ejemplo, es difícil encontrar el botón "siguiente" o las instrucciones no son evidentes. Lo más probable es que haya motivos empresariales más importantes para los abandonos: quizás las tasas de préstamo son demasiado altas.

Independientemente de los motivos, los datos ayudan al equipo a averiguar qué hacen los usuarios. Se pueden insertar más llamadas de seguimiento para averiguar más detalles. TrackEvent() se puede usar para contar las acciones del usuario, desde los detalles mínimos como los clics de botón individuales hasta logros importantes como la liquidación de un préstamo.

El equipo se está acostumbrando a tener información sobre la actividad del usuario. En la actualidad, cada vez que diseñan una nueva característica, calculan cómo obtendrán comentarios de su uso. Diseñan llamadas de seguimiento en la característica desde el principio. Usan los comentarios para mejorar la característica de cada ciclo de desarrollo.


## Supervisión proactiva  


Marcela no se sienta de brazos cruzados a esperar las alertas. Poco después de cada reimplementación, echa un vistazo a los [tiempos de respuesta][perf], tanto la cifra general como la tabla de solicitudes más lentas, así como los recuentos de excepciones.



![Gráfico de tiempo de respuesta y cuadrícula de tiempos de respuesta del servidor.](./media/app-insights-detect-triage-diagnose/09-dependencies.png)

Para evaluar el efecto sobre el rendimiento de cada implementación, normalmente compara cada semana con la última. Si hay un empeoramiento repentino, lo notifica a los desarrolladores pertinentes.


## Evaluación de errores


Evaluar la gravedad y la extensión de un problema es el primer paso tras la detección. ¿Debemos llamar al equipo a medianoche? ¿O puede dejarse hasta el siguiente hueco conveniente en el trabajo pendiente? Hay algunas cuestiones importantes en la evaluación de errores.


¿Cuánto está sucediendo? Los gráficos de la hoja de información general proporcionan cierta perspectiva sobre un problema. Por ejemplo, la aplicación de Fabrikam generó cuatro alertas de prueba web en una noche. Al examinar el gráfico por la mañana, el equipo pudo ver que había algunos puntos rojos, aunque todavía la mayoría de las pruebas eran verdes. Cuando se profundizó en el gráfico de disponibilidad, se tuvo la certeza de que todos estos problemas intermitentes procedían de una ubicación de prueba. Se trataba obviamente de un problema de red que afectaba a una sola ruta, y es probable que se aclarara por sí solo.


Por el contrario, un aumento considerable y estable en el gráfico de recuento de excepciones o tiempos de respuesta es claramente algo de lo que tener miedo.


Una táctica de evaluación de errores útil es probarlo usted mismo. Si se tropieza con el mismo problema, sabrá que es real.


¿Qué fracción de usuarios se vieron afectados? Para obtener una respuesta aproximada, divida la tasa de error entre el recuento de sesiones.


![Gráficos de las solicitudes y sesiones con error](./media/app-insights-detect-triage-diagnose/10-failureRate.png)

En el caso de respuesta lenta, compare la tabla de solicitudes de respuesta más lenta con la frecuencia de uso de cada página.


¿Qué importancia tiene el escenario bloqueado? ¿Se trata de un problema funcional que bloquea un caso de usuario determinado?, ¿tiene mucha importancia? Si los clientes no pueden pagar sus facturas, esto es grave; si no pueden cambiar sus preferencias de color de pantalla, quizás pueda esperar. Los detalles del evento o excepción, o la identidad de la página lenta, le indican dónde tienen problemas los clientes.


## Diagnóstico


El diagnóstico no es exactamente lo mismo que la depuración. Antes de iniciar el seguimiento a través del código, debe tener una idea aproximada de por qué, dónde y cuándo se está produciendo el problema.


**¿Cuándo sucede?** La vista histórica proporcionada por los gráficos de eventos y métricas facilita la creación de una correlación entre los efectos y las posibles causas. Si hay picos intermitentes en las tasas de excepción o de tiempo de respuesta, examine el número de solicitudes: si alcanza el máximo al mismo tiempo, parece un problema de recursos. ¿Es necesario asignar más CPU o memoria? ¿O se trata de una dependencia que no puede administrar la carga?


**¿Somos nosotros?** Si tiene un descenso repentino en el rendimiento de un tipo concreto de solicitud, por ejemplo cuando el cliente desea un extracto de cuenta, hay una posibilidad de que sea un subsistema externo en lugar de la aplicación web. En el Explorador de métricas, seleccione la tasa de errores de dependencia y las tasas de duración de dependencia y compare sus historiales de las pasadas horas o días con el problema que ha detectado. Si hay cambios que se correlacionan, es posible que un subsistema externo sea el culpable.


![Gráficos de errores de dependencia y duración de llamadas a dependencias](./media/app-insights-detect-triage-diagnose/11-dependencies.png)

Algunos problemas de dependencia lenta son problemas de ubicación geográfica. Fabrikam Bank usa máquinas virtuales de Azure, y descubrieron que habían ubicado sin darse cuenta el servidor web y el servidor de cuentas en distintos países. Migrando uno de ellos, se obtuvo una mejora considerable.


**¿Qué hicimos?** Si el problema no parece estar en una dependencia, y no ha estado siempre ahí, es probable que se deba a un cambio reciente. La perspectiva histórica proporcionada por los gráficos de métricas y eventos facilita la correlación de cualquier cambio repentino con las implementaciones. De esta forma se limita la búsqueda del problema.


**¿Qué está ocurriendo?** Algunos problemas se producen solo en raras ocasiones y pueden ser difíciles de rastrear mediante pruebas sin conexión. Todo lo que podemos hacer es intentar capturar el error cuando se produzca en vivo. Puede inspeccionar los volcados de pila en los informes de excepciones. Además, puede escribir llamadas de seguimiento con su marco de registro favorito o con TrackTrace() o TrackEvent().


Fabrikam tenía un problema intermitente con las transferencias entre cuentas, pero solo con determinados tipos de cuenta. Para entender mejor lo que estaba ocurriendo, insertaron llamadas a TrackTrace() en los puntos clave del código y asociaron el tipo de cuenta como una propiedad para cada llamada. De esta forma fue fácil filtrar solo esos seguimientos en la búsqueda de diagnóstico. También asociaron valores de parámetros como propiedades y medidas a las llamadas de seguimiento.


## Lidiar con él


Una vez que ha diagnosticado el problema, puede elaborar un plan para corregirlo. Es posible que necesite revertir un cambio reciente, o quizás solo pueda seguir adelante y corregirlo. Una vez que se realiza la corrección, Application Insights le indicará si se ha realizado correctamente.


El equipo de desarrollo de Fabrikam Bank adopta un enfoque más estructurado para medir el rendimiento de lo que solían hacer antes del uso de Application Insights.

* Establecen objetivos de rendimiento en términos de medidas específicas en la página de información general de Application Insights.

* Diseñan las medidas de rendimiento en la aplicación desde el principio, por ejemplo, las métricas que miden el progreso del usuario a través de 'embudos'.




## Uso

Application Insights puede usarse también para saber qué hacen los usuarios con una aplicación. Una vez que funciona sin problemas, al equipo le gustaría saber qué características son las más populares, lo que les gusta a los usuarios o con lo que tienen dificultad y la frecuencia con que vuelven. Eso ayudará a establecer prioridades en su próximo trabajo. Y pueden planear la medición del éxito de cada característica como parte del ciclo de desarrollo. [Más información][usage].

## Sus aplicaciones

Así es como un equipo usa Application Insights no solo para solucionar problemas individuales, sino también para mejorar su ciclo de vida de desarrollo. Espero que les haya dado algunas ideas sobre cómo Application Insights puede ayudarle a mejorar el rendimiento de sus propias aplicaciones.

## Vídeo

[AZURE.VIDEO performance-monitoring-application-insights]

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[perf]: app-insights-web-monitor-performance.md
[usage]: app-insights-web-track-usage.md
 

<!---HONumber=Nov15_HO3-->