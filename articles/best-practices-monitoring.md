<properties
   pageTitle="Guía de supervisión y diagnósticos | Microsoft Azure"
   description="Prácticas recomendadas para la supervisión de aplicaciones distribuidas en la nube."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/17/2015"
   ms.author="masashin"/>

# Guía de supervisión y diagnósticos

![](media/best-practices-monitoring/pnp-logo.png)

## Información general
Las aplicaciones distribuidas y servicios que se ejecutan en la nube son, por su naturaleza, complejos fragmentos de software que se componen de muchas partes móviles. En un entorno de producción, es importante poder hacer un seguimiento de la forma en que los usuarios usan el sistema y de la utilización de recursos, y supervisar en general el estado y el rendimiento del sistema. Esta información puede usarse como un diagnóstico de ayuda para detectar y corregir problemas y para ayudar a detectar problemas potenciales e impedir que se produzcan.

## Escenarios de supervisión y diagnósticos
La supervisión permite comprender en profundidad cómo funciona un sistema y es una parte fundamental para mantener los objetivos de calidad de servicio. Algunos escenarios comunes para la recopilación de datos de supervisión son:

- Garantizar que el sistema esté en buen estado.
- Realizar un seguimiento de la disponibilidad del sistema y sus elementos componentes.
- Mantener el rendimiento para garantizar que el procesamiento del sistema no se degrada inesperadamente a medida que el volumen de trabajo aumenta.
- Garantizar que el sistema cumple que los SLA acordados con los clientes.
- Proteger la privacidad y la seguridad del sistema, los usuarios y sus datos.
- Realizar un seguimiento de las operaciones que se llevan a cabo como auditoría para fines normativos.
- Supervisar el uso diario del sistema y ayudar a identificar las tendencias que podría producir problemas si no se resuelven.
- Realizar un seguimiento de los problemas que se producen, desde el informe inicial al análisis de las causas posibles, su rectificación, las actualizaciones de software pertinentes y la implementación.
- Realizar un seguimiento de las operaciones y depurar las versiones de software.

> [AZURE.NOTE] Esta lista no pretende ser exhaustiva. Este documento se centra en estos escenarios como las situaciones más habituales para realizar una supervisión, pero también puede haber otros que sean menos comunes o específicos en su propio entorno.

En las siguientes secciones se describen estas configuraciones con más detalle. La información para cada escenario se describe en el siguiente formato:

- Una breve descripción del escenario.
- Los requisitos típicos de este escenario.
- Los datos de instrumentación sin procesar necesarios para admitir el escenario y los orígenes posibles de esta información.
- Cómo pueden analizarse y combinarse estos datos sin procesar para generar información de diagnóstico significativa.

## Supervisión del estado
Un sistema es correcto si está en funcionamiento y es capaz de procesar solicitudes. El propósito de la supervisión del estado es generar una instantánea del estado actual del sistema que le permite comprobar que todos los componentes del sistema están funcionando según lo previsto.

### Requisitos para la supervisión del estado
Un operador debe recibir una alerta rápidamente (en cuestión de segundos) si se considera que cualquier parte del sistema tiene un estado incorrecto. El operador debe ser capaz de determinar las partes del sistema que funcionan con normalidad y las partes que tienen problemas. El estado del sistema se puede resaltar mediante un sistema de semáforo; rojo si es incorrecto (el sistema se detuvo), amarillo si es parcialmente correcto (el sistema se ejecuta con funcionalidad reducida) y verde si es completamente correcto.

Un completo sistema de supervisión del estado permite que un operador explore en profundidad el sistema para ver el estado de los subsistemas y componentes. Por ejemplo, si el sistema global se representa como parcialmente correcto, el operador debe ser capaz de profundizar y determinar la funcionalidad que no está disponible actualmente.

### Requisitos de colecciones de datos, instrumentación y orígenes de datos
Los datos sin procesar necesarios para admitir la supervisión del estado pueden generarse como resultado de:

- El seguimiento de la ejecución de las solicitudes de los usuarios. Esta información puede usarse para determinar las solicitudes que se realizaron correctamente, las que produjeron un error y cuánto tiempo tarda cada solicitud.
- Supervisión de usuarios sintéticos. Este proceso simula los pasos que realiza un usuario y sigue una serie predefinida de pasos. Se deben capturar los resultados de cada paso.
- Registro de excepciones, errores y advertencias. Esta información se puede capturar como resultado de las instrucciones de seguimiento incrustadas en el código de la aplicación, así como para recuperar la información de los registros de eventos de cualquier servicio al que el sistema haga referencia.
- Supervisión del estado de los servicios de terceros que usa el sistema. Esto puede requerir que se tengan que recuperar y analizar los datos de estado que ofrecen estos servicios, y esta información puede tener distintos formatos.
- Supervisión de extremos. Este mecanismo se describe con más detalle en la sección de supervisión de la disponibilidad.
- Recopilación de la información de rendimiento del ambiente, como el uso de la CPU en segundo plano o la actividad de E/S (incluida la red).

### Análisis de los datos de estado
El objetivo principal de la supervisión del estado es indicar rápidamente si el sistema está en ejecución. El análisis en caliente de los datos inmediatos puede desencadenar una alerta si se detecta que un componente esencial con un estado incorrecto (no puede responder a una serie consecutiva de pings, por ejemplo). El operador puede entonces tomar la acción correctiva apropiada.

Un sistema más avanzado podría incluir un elemento de predicción que realice un análisis en frío de las cargas de trabajo actuales y recientes para detectar tendencias y determinar si es probable que el sistema se mantenga en buen estado o si serán necesarios recursos adicionales. Este elemento de predicción debe basarse en métricas de rendimiento esenciales, como la tasa de solicitudes dirigidas a cada servicio o subsistema, los tiempos de respuesta de dichas solicitudes y el volumen de datos que fluye dentro y fuera de cada servicio. Si el valor de cualquier métrica supera un umbral definido, el sistema puede generar una alerta para habilitar un operador o escalarse automáticamente (si está disponible) para tomar las acciones preventivas necesarias para mantener el buen estado del sistema. Estas acciones pueden implicar que se agreguen recursos, se reinicie uno o varios servicios que no funcionen correctamente o se aplique una limitación a las solicitudes de menor prioridad.

## Supervisión de la disponibilidad
Un sistema con un estado verdaderamente correcto requiere que los componentes y subsistemas que componen el sistema estén disponibles. La supervisión de la disponibilidad está estrechamente relacionada con la supervisión del estado, pero mientras que la supervisión del estado ofrece una vista inmediata del estado actual del sistema, la supervisión de la disponibilidad se ocupa de realizar un seguimiento de la disponibilidad del sistema y sus componentes para generar estadísticas sobre el tiempo de actividad del sistema.

En muchos sistemas, algunos componentes (por ejemplo, una base de datos) se configuran con redundancia integrada para permitir una rápida conmutación por error si se produce un error grave o una pérdida de conectividad. Idealmente, los usuarios no deben percibir que se ha producido un error, pero desde una perspectiva de supervisión de la disponibilidad, es necesario recopilar toda la información posible sobre estos errores para intentar determinar la causa y tomar medidas correctivas para impedir que vuelva a producirse.

Los datos necesarios para realizar el seguimiento de la disponibilidad pueden depender de una serie de factores de nivel inferior, muchos de los cuales pueden ser específicos de la aplicación, el sistema y el entorno. Un sistema de supervisión eficaz captura los datos de disponibilidad que corresponden a esos factores de bajo nivel y, después, los agrupa para ofrecer una visión general del sistema. Por ejemplo, en un sistema de comercio electrónico, la funcionalidad empresarial que permite que un cliente haga pedidos puede depender del repositorio en que se almacenan los detalles del pedido y del sistema de pago que controla las transacciones monetarias para pagar dichos pedidos. La disponibilidad de la parte de la realización del pedido del sistema es, por tanto, una función de la disponibilidad del repositorio y el subsistema de pago.

### Requisitos para la supervisión de la disponibilidad
Un operador también debe ser capaz de ver el histórico de disponibilidad de cada sistema y subsistema y usar esta información para identificar las tendencias que puedan estar provocando errores en uno o varios subsistemas (¿los servicios empiezan a funcionar de forma incorrecta en un momento determinado del día, que se corresponde con las horas de mayor procesamiento?)

Además de proporcionar una vista inmediata e histórica de la disponibilidad (o no) de cada subsistema, una solución de supervisión también debe ser capaz de alertar rápidamente a un operador si se producen errores en uno o más servicios o los usuarios no pueden conectarse a los mismos. No es solo una cuestión de supervisar cada servicio, sino también examinar las acciones que cada usuario realiza si estas acciones producen un error al intentar comunicarse con un servicio. Hasta cierto punto, un cierto margen de errores de conectividad es normal ya que pueden deberse a errores transitorios, pero puede ser útil permitir que el sistema genere una alerta del número de errores de conectividad en un subsistema concreto durante un período de tiempo específico.

### Requisitos de colecciones de datos, instrumentación y orígenes de datos
Al igual que con la supervisión del estado, los datos sin procesar necesarios para admitir la supervisión de la disponibilidad pueden generarse como resultado de la supervisión de los usuarios sintéticos y el registro de las excepciones, los errores y las advertencias que puedan producirse. Además, los datos de la disponibilidad se pueden obtener de la supervisión de los extremos. La aplicación puede exponer uno o más extremos de estado, cada uno de los cuales realiza pruebas de acceso a un área funcional dentro del sistema. El sistema de supervisión puede hacer ping a cada extremo siguiendo una programación definida y recopilar los resultados (correcto o error).

Se deben registrar todos los errores de conectividad de red y de tiempos de expiración y los reintentos de conexión. Todos los datos deben tener marca de tiempo.

<a name="analyzing-availablity-data"></a>
### Análisis de los datos de disponibilidad
Los datos de instrumentación se deben agregar y realizarse una correlación para admitir los siguientes tipos de análisis:

- La disponibilidad inmediata del sistema y los subsistemas.
- Las tasas de error de disponibilidad del sistema y los subsistemas. Lo ideal es que un operador sea capaz de realizar una correlación de los errores con actividades específicas; ¿qué ocurría cuando se produjo el error en el sistema?
- Una vista histórica de las tasas de error del sistema o los subsistemas en cualquier período de tiempo especificado y la carga del sistema (por ejemplo, el número de solicitudes de usuario) cuando se produjo un error.
- Las razones de falta de disponibilidad del sistema o los subsistemas. Por ejemplo, el servicio no está en ejecución; se produjo una pérdida de conectividad; está conectado, pero se agotó el tiempo de expiración; y está conectado, pero se devuelven errores.

Puede calcular el porcentaje de disponibilidad de un servicio en un período de tiempo con la fórmula:

```
%Availability =  ((Total Time – Total Downtime) / Total Time ) * 100
```

Esto es útil para fines de SLA (La [supervisión de SLA](#SLA-monitoring) se describe con más detalle más adelante en esta guía). La definición de _tiempo de inactividad_ depende del servicio. Por ejemplo, el servicio de compilación de Visual Studio Team Services define el tiempo de inactividad como el período (minutos acumulados totales) durante el cual el servicio de compilación está disponible. Se considera que un minuto no está disponible si todas las solicitudes HTTP que recibe el servicio de compilación para realizar operaciones iniciadas por el cliente a lo largo del minuto dan lugar a un código de error o no devuelven ninguna respuesta.

## Supervisión del rendimiento
Como el sistema se somete a condiciones de carga cada vez mayores a medida que el volumen de usuarios incrementa y el tamaño de los conjuntos de datos a los que tienen acceso crece, los errores posibles en uno o varios componentes son cada vez más probables. Con frecuencia, el error de componentes está precedido por una disminución del rendimiento. Si puede detectar esa disminución, puede tomar medidas proactivas para remediar la situación.

El rendimiento del sistema depende de varios factores. Cada factor se mide normalmente mediante indicadores clave de rendimiento (KPI), como el número de transacciones de la base de datos por segundo o el volumen de solicitudes de red que se atienden correctamente en un período de tiempo determinado. Algunos de estos KPI pueden estar disponibles como medidas de rendimiento específicas, mientras que otros pueden derivarse de una combinación de métricas.

> [AZURE.NOTE] La determinación de un buen o mal rendimiento requiere que comprenda el nivel de rendimiento al que el sistema debería ser capaz de funcionar. Esto requiere observar el sistema mientras está funcionando bajo una carga típica y capturar los datos de cada KPI durante un período de tiempo. Esto puede implicar tener que ejecutar el sistema bajo una carga simulada en un entorno de pruebas y recopilar los datos apropiados antes de implementar el sistema en un entorno de producción.

> También debe asegurarse de que la supervisión para objetivos de rendimiento no se convierta en una carga injustificada para el sistema. Es posible que pueda ajustar dinámicamente el nivel de detalle de los datos que recopila el proceso de supervisión del rendimiento.

### Requisitos de la supervisión del rendimiento
Para examinar el rendimiento del sistema, un operador normalmente necesitaría ver cierta información, entre la que se incluye:

- Las tasas de respuesta de las solicitudes de usuario.
- El número de solicitudes de usuario simultáneas.
- El volumen de tráfico de la red.
- Las tasas a las que se completan las transacciones de negocios.
- El tiempo medio de procesamiento de solicitudes.

También puede ser útil ofrecer herramientas que permitan que un operador ayude a identificar correlaciones, como por ejemplo:

- El número de usuarios simultáneos frente a los tiempos de latencia de las solicitudes (cuánto se tarda en iniciar el procesamiento de una solicitud después de que el usuario la haya enviado).
- El número de usuarios simultáneos frente al tiempo de respuesta promedio (cuánto se tarda en completar una solicitud después de que haya comenzado a procesarse).
- El volumen de solicitudes frente al número de errores de procesamiento.

Además de esta información funcional de alto nivel, un operador también debe poder obtener una vista detallada del rendimiento de cada componente del sistema. Estos datos normalmente se ofrecen mediante el uso de información de seguimiento de contadores de rendimiento de bajo nivel, como:

- el uso de memoria;
- el número de subprocesos;
- el tiempo de procesamiento de la CPU;
- la longitud de la cola de solicitudes;
- los errores y las tasas de E/S de las redes y los discos;
- el número de bytes escritos o leídos;
- los indicadores de software intermedio, como la longitud de la cola.

Todas las visualizaciones deben permitir que un operador especifique un período de tiempo; los datos que se muestran podrían ser una instantánea de la situación actual o una vista histórica del rendimiento.

Un operador debe ser capaz de generar una alerta en función de una medida de rendimiento de un valor dado durante un intervalo de tiempo especificado.

### Requisitos de colecciones de datos, instrumentación y orígenes de datos
Se pueden recopilar los datos de rendimiento a alto nivel (procesamiento, número de usuarios simultáneos, número de transacciones de negocio, tasas de error, etc.) mediante la supervisión del progreso de las solicitudes de los usuarios cuando llegan y pasan a través del sistema. Esto implica la incorporación de instrucciones de seguimiento en puntos clave del código de la aplicación junto con información de tiempo. Todos los errores, excepciones y advertencias deben capturarse con datos suficientes para que puedan ponerse en correlación con las solicitudes que los provocaron. El registro de IIS es otro origen útil.

Si es posible, también debería capturar datos de rendimiento de los sistemas externos que use la aplicación. Estos sistemas externos podrían proporcionar sus propios contadores de rendimiento u otras características para solicitar datos de rendimiento. Si esto no es posible, debe registrar la información como la horas de inicio y finalización de cada solicitud realizada en un sistema externo, junto con el estado (correcto, error o advertencia) de la operación. Por ejemplo, puede aplicar un enfoque de cronómetro a las solicitudes de tiempo; inicie un temporizador cuando se inicie la solicitud y deténgalo cuando la solicitud se complete.

Los datos de rendimiento de bajo nivel de los componentes individuales en un sistema pueden estar disponibles a través de características como los contadores de rendimiento de Windows y Diagnósticos de Microsoft Azure.

### Análisis de los datos de rendimiento
Gran parte del trabajo de análisis consiste en agregar datos de rendimiento por tipo de solicitud de usuario (por ejemplo, agregar un artículo al carro de la compra o realizar el proceso de pago en un sistema de comercio electrónico) o el subsistema o servicio a los que se envía cada solicitud.

Otro requisito común es resumir los datos de rendimiento en percentiles seleccionados. Por ejemplo, determinar los tiempos de respuesta del 99 %, 95 % y 70 % de las solicitudes. Puede haber objetivos del SLA u otros conjuntos de objetivos para cada percentil. Debe informarse de los resultados en curso tanto en tiempo real para ayudar a detectar problemas inmediatos, como de forma agregada a largo plazo para fines estadísticos.

En el caso de problemas de latencia que afecten al rendimiento, un operador debe ser capaz de identificar rápidamente la causa del cuello de botella examinando la latencia de cada paso que se ha realizado para cada solicitud. Por tanto, los datos de rendimiento deben ofrecer una forma de realizar una correlación de las medidas de rendimiento de cada paso para vincularlas a una solicitud concreta.

Según los requisitos de visualización, puede ser útil generar y almacenar un cubo de datos que contenga vistas de los datos sin procesar para permitir consultas ad hoc complejas y el análisis de la información de rendimiento.

## Supervisión de la seguridad
Todos los sistemas comerciales que incluyen datos confidenciales deben implementar una estructura de seguridad. Normalmente, la complejidad del mecanismo de seguridad depende de la confidencialidad de los datos. En un sistema que requiera que los usuarios se autentiquen, se deben registrar todos los intentos de inicio de sesión, tanto si realizan correctamente como si producen un error. Además, se deben registrar todas las operaciones que se realizan y los detalles de todos los recursos a los que obtiene acceso un usuario autenticado. Cuando el usuario termina su sesión y la cierra, esta información también debe registrarse.

La supervisión puede ser capaz de ayudar a detectar ataques en el sistema. Por ejemplo, un número elevado de intentos de inicio de sesión erróneos podría indicar un ataque de fuerza bruta, y un aumento inesperado en las solicitudes podría ser el resultado de un ataque DDoS. Debe estar preparado para supervisar todas las solicitudes de todos los recursos independientemente del origen de estas solicitudes; un sistema con una vulnerabilidad de inicio de sesión puede exponer recursos al mundo exterior de forma accidental sin necesidad de que un usuario inicie realmente sesión.

### Requisitos para la supervisión de la seguridad
Los aspectos más críticos de la supervisión de la seguridad deberían permitir que un operador realizara las siguientes operaciones con rapidez:

- detectar intentos de intrusión de una entidad no autenticada;
- identificar intentos de entidades que quieren realizar operaciones en datos a los que no se le ha concedido acceso;
- determinar si el sistema (o alguna parte del mismo) está sufriendo un ataque desde fuera o desde dentro (por ejemplo, un usuario autenticado malintencionado puede estar intentando que el sistema deje de estar disponible).

Para admitir estos requisitos, se debe notificar a un operador:

- De los intentos erróneos repetidos de inicio de sesión que se realicen en una misma cuenta dentro de un período de tiempo especificado.
- Si la misma cuenta autenticada intenta repetidamente obtener acceso a un recurso prohibido durante un período de tiempo especificado.
- Si se produce un gran número de solicitudes no autenticadas o no autorizadas durante un período de tiempo especificado.

La información facilitada a un operador debe incluir la dirección del host del origen de cada solicitud. Si las infracciones de seguridad provienen con regularidad de un determinado intervalo de direcciones, podrían bloquearse estos hosts.

Una parte clave del mantenimiento de la seguridad de un sistema es poder detectar rápidamente las acciones que se desvíen del patrón habitual. Puede mostrarse visualmente información como el número de solicitudes de inicio de sesión correctas o erróneas para ayudar a detectar si hay un aumento en la actividad en un momento inusual (por ejemplo, usuarios que inician la sesión y realizan un gran número de operaciones a las 3 a. m. cuando sus jornadas laborales comienzan a las 9 a. m.). Esta información también puede usarse para ayudar a configurar un escalado automático basado en tiempo; por ejemplo, si un operador observa que un gran número de usuarios normalmente inicia sesión en un momento determinado del día, el operador puede organizarse para iniciar servicios de autenticación adicionales y así controlar el volumen de trabajo y, más tarde, reducir esos servicios adicionales cuando ha pasado el pico.

### Requisitos de colecciones de datos, instrumentación y orígenes de datos
La seguridad es un aspecto universal de la mayoría sistemas distribuidos y es probable que los datos pertinentes se generen en varios puntos a lo largo de un sistema. Considere la posibilidad de adoptar un enfoque de administración de eventos e información de seguridad (SIEM) para recopilar la información relacionada con la seguridad resultante de los eventos que han generado la aplicación, el equipo de red, los servidores, los firewalls, el software antivirus y otros elementos de prevención de intrusiones.

La supervisión de la seguridad puede incorporar datos de las herramientas que no forman parte de la aplicación, como herramientas que identifiquen actividades de análisis de puertos por parte de organismos externos o filtros de red que detecten intentos de acceso no autenticado a la aplicación y los datos.

En todos los casos, los datos recopilados deben permitir que un administrador determine la naturaleza de cualquier ataque y adopte las medidas adecuadas.

### Análisis de los datos de seguridad
Una característica de la supervisión de seguridad es la variedad de orígenes desde los que se generan los datos. Los distintos formatos y el nivel de detalle requieren a menudo un análisis complejo de los datos capturados para unirlos en un subproceso coherente de información. Aparte de los casos más sencillos (por ejemplo, la detección de un gran número de inicios de sesión erróneos o intentos repetidos para obtener acceso no autorizado a recursos esenciales), podría no ser posible realizar ningún procesamiento automatizado complejo de los datos de seguridad y, en su lugar, puede ser preferible escribir estos datos con una marca de tiempo (y todo lo demás en su forma original) en un repositorio seguro para permitir un análisis manual experto.

<a name="SLA-monitoring"></a>

## Supervisión del SLA
Muchos sistemas comerciales que admiten a los clientes de pago ofrecen garantías sobre el rendimiento del sistema en forma de SLA. Básicamente, los SLA especifican que el sistema puede controlar un volumen de trabajo definido en un período de tiempo acordado sin perder la información crítica. La supervisión del SLA se encarga de garantizar que el sistema puede cumplir los SLA medibles.

> [AZURE.NOTE] La supervisión del SLA está estrechamente relacionada con la supervisión del rendimiento, pero mientras que la supervisión del rendimiento se encarga de garantizar que el sistema funciona _de manera óptima_, la supervisión del SLA se rige por una obligación contractual que define lo que significa _de manera óptima_.

Los SLA se definen con frecuencia en términos de:

- Disponibilidad general del sistema. Por ejemplo, una organización puede garantizar que el sistema estará disponible durante un 99,9 % del tiempo; esto equivale a no tener más de 9 horas de tiempo de inactividad al año, lo que corresponde aproximadamente a 10 minutos a la semana.
- Rendimiento operativo. A menudo, este aspecto se expresa como una o más marcas principales de límites máximos; como la garantía de que el sistema será capaz de admitir hasta 100.000 solicitudes de usuario simultáneas o controlar 10.000 transacciones de negocio simultáneas.
- Tiempo de respuesta operativa. El sistema también puede ofrecer garantías relativas a la velocidad a la que se procesan las solicitudes; por ejemplo, que el 99 % de todas las transacciones de negocio se completarán en menos de 2 segundos y que ninguna transacción única tardará más de 10 segundos.

> [AZURE.NOTE] Algunos contratos de sistemas comerciales pueden incluir también SLA relacionados con la asistencia al cliente, como que las solicitudes de asistencia técnica tendrán una respuesta en menos de 5 minutos y que el 99 % de los problemas deben solucionarse por completo en menos de 1 día laborable. Un [seguimiento de problemas](#issue-tracking) eficaz (que se describe más adelante en esta sección) es crucial para cumplir SLA como estos.

### Requisitos de la supervisión del estado
En el nivel más alto, un operador debe ser capaz de determinar de un vistazo si el sistema está cumpliendo los SLA acordados o no y, si no es así, analizar en profundidad y examinar los factores subyacentes para determinar los motivos del rendimiento incorrecto.

Entre los indicadores de alto nivel más habituales que se pueden representar visualmente, se incluyen:

- El porcentaje de tiempo de actividad de servicio.
- El rendimiento de la aplicación (medido en cuanto a transacciones correctas u operaciones por segundo).
- El número de solicitudes correctas y erróneas de la aplicación.
- El número de errores, excepciones y advertencias de la aplicación y del sistema.

Todos estos indicadores deben poder filtrarse para un período de tiempo especificado.

Una aplicación en la nube probablemente constará de un número de componentes y subsistemas. Un operador debe ser capaz de seleccionar un indicador de alto nivel y ver cómo está compuesto por el estado de los elementos subyacentes. Por ejemplo, si el tiempo de actividad del sistema global cae por debajo de un valor aceptable, un operador debe poder ver en profundidad y determinar qué elementos están contribuyendo a dicho error.

> [AZURE.NOTE] El tiempo de actividad del sistema debe definirse con cuidado. En un sistema que usa redundancia para garantizar la máxima disponibilidad, las instancias individuales de los elementos pueden producir un error, pero el sistema puede seguir siendo funcional. El tiempo de actividad del sistema tal y como lo presenta la supervisión del estado debe indicar el tiempo de actividad agregado de cada elemento y no necesariamente si el sistema realmente se detuvo. Además, es posible aislar los errores, por lo que incluso si un sistema concreto no está disponible, el resto del sistema puede permanecer disponible, aunque con una disminución de la funcionalidad (en un sistema de comercio electrónico, un error en el sistema podría impedir que un cliente realizase pedidos, pero el cliente seguiría pudiendo examinar el catálogo de productos).

Para fines de alertas, el sistema debe ser capaz de generar un evento si cualquiera de los indicadores de alto nivel supera un umbral especificado. Los detalles de nivel inferior de los distintos factores que componen el indicador de alto nivel deben estar disponibles como datos contextuales para el sistema de alertas.

### Requisitos de colecciones de datos, instrumentación y orígenes de datos
Los datos sin procesar necesarios para admitir la supervisión del SLA son similares a los que se necesitan para supervisar el rendimiento, junto con algunos aspectos de la supervisión de la disponibilidad y el estado (consulte esas secciones para obtener más detalles). Puede capturar estos datos mediante:

- La realización de supervisión de los extremos
- El registro de excepciones, errores y advertencias.
- El seguimiento de la ejecución de las solicitudes de los usuarios.
- La supervisión de la disponibilidad de los servicios de terceros que usa el sistema.
- El uso de contadores y métricas de rendimiento.

Todos los datos deben medirse en tiempo y disponer de una marca de tiempo.

### Análisis de los datos de SLA
Los datos de instrumentación deben agregarse para generar una imagen del rendimiento general del sistema y para admitir la exploración en profundidad y así habilitar el examen del rendimiento de los subsistemas subyacentes. Por ejemplo, debe ser capaz de:

- Calcular el número total de solicitudes de usuario durante un período determinado y determinar la tasa de solicitudes correctas y erróneas.
- Combinar los tiempos de respuesta de las solicitudes de usuario para generar una vista general de los tiempos de respuesta del sistema.
- Analizar el progreso de la interrupción de las solicitudes de usuario cuando se descompone el tiempo de respuesta de una solicitud en los tiempos de respuesta de los elementos de trabajo individuales de esa solicitud.  
- Determinar la disponibilidad general del sistema como un porcentaje del tiempo de disponibilidad durante un período concreto.
- Analizar el porcentaje del tiempo de disponibilidad de cada uno de los servicios y componentes individuales en el sistema. Esto puede implicar tener que analizar los registros que generan los servicios de terceros.

En muchos sistemas comerciales es necesario informar de las cifras de rendimiento reales con respecto a los SLA acordados durante un período especificado, normalmente un mes. Esta información se puede usar para calcular créditos u otras formas de compensación para los clientes si no se cumplen los SLA durante ese período. Puede calcular la disponibilidad de un servicio mediante la técnica descrita en la sección [Análisis de los datos de disponibilidad](#analyzing-availability-data).

Para fines internos, una organización también podría realizar un seguimiento del número y la naturaleza de los incidentes que produjeron un error en los servicios. Aprender a resolver estos problemas rápidamente o a eliminarlos por completo le ayudará a reducir el tiempo de inactividad y a cumplir los SLA.

## Auditoría
Según la naturaleza de la aplicación, puede haber normativas legales o reglamentarias que especifiquen los requisitos para la auditoría de las operaciones que realizan los usuarios y el registro de todos los accesos a los datos. La auditoría puede ofrecer evidencia que vincule a los clientes con solicitudes concretas; no rechazar es un factor importante en muchos sistemas de comercio electrónico para ayudar a mantener la confianza entre un cliente y la organización responsable de la aplicación o el servicio.

### Requisitos de la auditoría
Un analista debe poder realizar un seguimiento de la secuencia de operaciones de negocios que han llevado a cabo los usuarios, de forma que se puedan reconstruir las acciones de los usuarios. Esto puede ser necesario simplemente como una cuestión de registro, o como parte de una investigación forense.

La información de auditoría tiene una alta confidencialidad, ya que seguramente incluirá datos que identifican a los usuarios del sistema junto con las tareas que están realizando. Por este motivo, lo más probable es que la información de auditoría se visualice como informes que solo están disponibles para los analistas de confianza en lugar de mediante un sistema interactivo que admita operaciones gráficas de obtención de información detallada. Un analista debe ser capaz de generar una variedad de informes; por ejemplo, mostrar un listado de las actividades de todos los usuarios que se produzcan en un período de tiempo concreto, obtener los detalles de la cronología de actividad de un único usuario o enumerar las operaciones de secuencia que se han realizado en uno o más recursos.

### Requisitos de colecciones de datos, instrumentación y orígenes de datos
Los principales orígenes de información de auditoría pueden incluir:

- El sistema de seguridad que administra la autenticación de usuario.
- Registros de seguimiento que almacenan la actividad de los usuarios.
- Registros de seguridad que realizan un seguimiento de todas las solicitudes de red identificables y no identificables.

El formato de los datos de auditoría y la forma en que se almacenan pueden estar controlados por requisitos normativos. Por ejemplo, quizá no sea posible limpiar los datos de ninguna manera (deben registrarse en su formato original) y el acceso al repositorio en que se mantienen debe protegerse para evitar alteraciones.

### Análisis de los datos de auditoría
Un analista debe tener acceso a los datos sin procesar en su totalidad y en su forma original. Además el requisito de generar informes de auditoría comunes, es probable que las herramientas usadas para analizar estos datos sean especializadas y se mantengan de forma externa al sistema.

## Supervisión del uso
La supervisión del uso realiza un seguimiento de cómo se utilizan las características y los componentes de una aplicación. Los datos recopilados se pueden usar para:

- Determinar las características que se utilizan mucho y las posibles zonas activas del sistema. Los elementos de tráfico elevado pueden beneficiarse del particionamiento funcional o incluso de la replicación para distribuir la carga de forma más uniforme. Esta información también se puede usar para determinar las características que se utilizan con poca frecuencia y que son posibles candidatas para retirarlas o sustituirlas en una versión futura del sistema.
- Obtenga información sobre los eventos operativos del sistema con un uso normal. Por ejemplo, en un sitio de comercio electrónico podría registrar información estadística sobre el número de transacciones y el volumen de clientes que son responsables de las mismas. Esta información podría usarse para la planificación de la capacidad a medida que el número de clientes aumenta.
- Detecte (posiblemente indirectamente) la satisfacción de los usuarios con el rendimiento o la funcionalidad del sistema. Por ejemplo, si un gran número de clientes de un sistema de comercio electrónico abandona con frecuencia sus carros de la compra, podría tratarse de un problema con la funcionalidad de finalización de la compra.
- Genere información de facturación. Una aplicación comercial o un servicio multiempresa puede cobrar a los clientes por los recursos que usan.
- Aplique cuotas. Si un usuario de un sistema multiempresa supera la cuota de pago sobre el uso de recursos o el tiempo de procesamiento durante un período concreto, se puede limitar su acceso o el procesamiento.

### Requisitos de la supervisión del uso
Para examinar el uso del sistema, un operador normalmente necesitaría ver cierta información, entre la que se incluye:

- El número de solicitudes que procesa cada subsistema y que se dirige a cada recurso.
- El trabajo que realiza cada usuario.
- El volumen del almacenamiento de datos que ocupa cada usuario.
- Los recursos a los que accede cada usuario.

Un operador también debe ser capaz de generar gráficas, por ejemplo, mediante la visualización de los usuarios que usan más recursos o las características del sistema o los recursos a los que se accede con más frecuencia.

### Requisitos de colecciones de datos, instrumentación y orígenes de datos
El seguimiento de uso se puede realizar en un nivel relativamente alto, teniendo en cuenta los tiempos de inicio y finalización de cada solicitud y la naturaleza de la misma (lectura, escritura, etc., según el recurso en cuestión). Puede obtener esta información mediante:

- El seguimiento de la actividad de usuario.
- La captura de los contadores de rendimiento que miden la utilización de cada recurso.
- La supervisión del consumo de recursos por cada usuario.

Para fines de medición, también deberá ser capaz de identificar qué usuarios son responsables de realizar qué operaciones y los recursos que usan dichas operaciones. La información recopilada debe ser lo suficientemente detallada para permitir una factura precisa.

<a name="issue-tracking"></a>
## Seguimiento de problemas
Los clientes y otros usuarios pueden informar de problemas si se producen eventos o comportamientos inesperados en el sistema. El seguimiento de problemas se ocupa de administrar estos problemas, los asocia a esfuerzos para resolver cualquier problema subyacente del sistema e informa a los clientes de las posibles soluciones.

### Requisitos para el seguimiento de problemas
El seguimiento de problemas se realiza a menudo mediante un sistema independiente que permite que los operadores registren e informen de los detalles de los problemas que los usuarios han notificado. Estos detalles pueden incluir información como las tareas que el usuario estaba intentando llevar a cabo, los síntomas del problema, la secuencia de eventos y los mensajes de error o advertencia que se emitieron.

### Requisitos de colecciones de datos, instrumentación y orígenes de datos
El origen inicial de los datos de seguimiento de problemas es el usuario que informa del problema en primer lugar. El usuario puede ser capaz de ofrecer datos adicionales, como un volcado de memoria (si la aplicación incluye un componente que se ejecuta en el escritorio del usuario), una instantánea de la pantalla y la fecha y hora en que se produjo el error, junto con cualquier otra información del entorno, como su ubicación.

Esta información puede facilitarse al esfuerzo de depuración y ayudar a construir un trabajo pendiente para versiones futuras del software.

### Análisis de los datos de seguimiento de problemas
Distintos usuarios pueden informar del mismo problema y el sistema de seguimiento de problemas debe asociar los informes comunes y juntarlos.

El progreso del esfuerzo de depuración se debe registrar en cada informe de problemas y, cuando se resuelva el problema, se puede informar al cliente de la solución.

Si un usuario informa de un problema reconocido con una solución conocida en el sistema de seguimiento de problemas, el operador debería poder informar al usuario de la solución inmediatamente.

## Seguimiento de las operaciones y depuración de las versiones de software
Cuando un usuario informa de un problema, a menudo solo es consciente del efecto inmediato que tiene en sus operaciones, de forma que solo puede informar de los resultados de su propia experiencia a un operador responsable de mantener el sistema. Estas experiencias normalmente son simplemente un síntoma visible de uno o más problemas fundamentales. En muchos casos, será necesario que un analista indague en la cronología de las operaciones subyacentes para establecer la causa raíz del problema (este proceso se conoce como _análisis de causa raíz_).

> [AZURE.NOTE] El análisis de causa raíz puede descubrir las ineficacias en el diseño de una aplicación. En estas situaciones, quizá sea posible modificar los elementos afectados e implementarlos como parte de una versión posterior. Este proceso requiere un control cuidadoso y los componentes actualizados deben supervisarse atentamente.

### Requisitos para el seguimiento y la depuración
Para realizar un seguimiento de los eventos inesperados y otros problemas, es vital que los datos de supervisión ofrezcan suficiente información no solo de los problemas que se produzcan en el nivel superior, sino que también se incluyan suficientes detalles para permitir que un analista llegue a los orígenes de estos problemas y reconstruya la secuencia de eventos que se produjeron. Esta información debe ser suficiente para permitir que un analista diagnostique la causa raíz de los problemas, de forma que un desarrollador pueda realizar las modificaciones necesarias para impedir que se vuelvan a producir.

### Requisitos de colecciones de datos, instrumentación y orígenes de datos
La solución de problemas puede implicar tener que realizar un seguimiento de todos los métodos (y sus parámetros) que se invoquen como parte de una operación, para crear un árbol que represente el flujo lógico a través del sistema cuando un cliente realice una solicitud concreta. Se deben capturar y registrar las excepciones y advertencias que genera el sistema como resultado de este flujo.

Para admitir la depuración, el sistema puede ofrecer enlaces que permitan que un operador capture información de estado en los puntos esenciales del sistema o que entregue información detallada paso a paso como el progreso de las operaciones seleccionadas. La captura de datos con este nivel de detalle puede imponer una carga adicional en el sistema y debería ser un proceso temporal, que principalmente se use cuando se produzca una serie de eventos muy poco habituales que sean difíciles de replicar, o cuando una nueva versión de uno o varios elementos en un sistema requiera una supervisión cuidadosa para asegurarse de que funcionan según lo esperado.

## La canalización de supervisión y diagnósticos
La supervisión de un sistema distribuido a gran escala plantea un desafío importante y cada uno de los escenarios descritos en la sección anterior no se deben considerar necesariamente de forma aislada. Es probable que sea una superposición significativa en los datos de supervisión y diagnóstico necesarios para cada situación, aunque es posible que estos datos deban procesarse y presentarse de maneras distintas. Por estas razones, debe tener una vista holística de la supervisión y los diagnósticos.

Puede prever el proceso completo de diagnósticos y supervisión como una canalización que comprende las fases que se muestran en la Ilustración 1.

![](media/best-practices-monitoring/Pipeline.png)

_Ilustración 1: Las fases en la canalización de supervisión y diagnósticos_

En la Ilustración 1 se muestra cómo los datos de supervisión y diagnósticos pueden proceder de diversos orígenes de datos. La fase de instrumentación y recopilación se ocupa de la instrumentación; determinando los datos que se deben capturar, cómo hacerlo y el formato que se debe dar a estos datos para que puedan examinarse fácilmente. La fase de análisis y diagnóstico toma los datos sin procesar y los usa para generar información significativa que se puede utilizar para determinar el estado del sistema. Esta información puede usarse para tomar decisiones sobre las posibles acciones que se pueden realizar y los resultados se puede insertar de vuelta en la fase de instrumentación y recopilación. La fase de visualización y alerta presenta una vista consumible del estado del sistema; puede mostrar información casi en tiempo real mediante el uso de una serie de paneles y podría generar informes, gráficos, y gráficas para ofrecer una vista histórica de los datos que pueden ayudar a identificar tendencias a largo plazo. Si la información indica un KPI que es probable que supere los límites aceptables, esta fase también puede desencadenar una alerta en un operador. En algunos casos, una alerta también puede usarse para desencadenar un proceso automatizado que intente realizar acciones correctivas, como el escalado automático.

Tenga en cuenta que estos pasos constituyen un proceso de flujo continuo en el que las fases se producen en paralelo. Lo ideal sería que todas las fases se pudieran configurar dinámicamente; en algunos puntos, especialmente cuando un sistema se ha implementado recientemente o está experimentando problemas, puede ser necesario recopilar datos extendidos con más frecuencia. En otras ocasiones, debe ser posible volver a capturar un nivel básico de información esencial para comprobar que el sistema funciona correctamente.

Además, todo el proceso de supervisión debe considerarse una solución continuada dinámica, que está sujeta a mejoras y ajustes como resultado de los comentarios. Por ejemplo, puede comenzar con la medición de muchos factores para determinar el estado del sistema, pero el análisis en el tiempo podría llevar a un refinamiento a medida que descarte las medidas que no sean relevantes, lo que le permite centrarse con más precisión en los datos que necesita a la vez que minimiza cualquier ruido de fondo.

## Orígenes de los datos de supervisión y diagnóstico
La información que usa el proceso de supervisión puede proceder de varios orígenes, como se muestra en la Ilustración 1. En el nivel de aplicación, información procede de los registros de seguimiento incorporados en el código del sistema. Los desarrolladores deben seguir un enfoque estándar para realizar el seguimiento del flujo de control a través de su código (por ejemplo, al entrar a un método, emitir un mensaje de seguimiento que especifique el nombre del método, la hora actual, el valor de cada parámetro y cualquier otra información pertinente. El registro de las horas de entrada y salida también resultaría útil). Debe registrar todas las excepciones y advertencias y asegurarse de mantener un seguimiento completo de las advertencias y excepciones anidadas. Idealmente, también debería capturar la información que identifica el usuario que ejecuta el código junto con la información de correlación de la actividad (para realizar el seguimiento de solicitudes a medida que pasan a través del sistema) y registrar los intentos de obtener acceso a todos los recursos como colas de mensajes, bases de datos, archivos y otros servicios dependientes; esta información puede usarse para fines de medición y auditoría.

Muchas aplicaciones hacen uso de bibliotecas y marcos de trabajo para realizar tareas comunes como obtener acceso a un almacén de datos o comunicarse a través de una red. Estos marcos pueden configurarse para ofrecer como salida sus propios mensajes de seguimiento e información de diagnóstico sin procesar, como las tasas de transacciones, las transmisiones de datos correctas y erróneas, etc.

> [AZURE.NOTE] Muchos marcos modernos publican automáticamente eventos de seguimiento y rendimiento, y la captura de esta información es solo cuestión de ofrecer un medio para recuperarla y almacenarla donde se pueda procesar y analizar.

El sistema operativo en que se ejecuta la aplicación puede ser una fuente de información de todo el sistema de bajo nivel, como los contadores de rendimiento que indican las tasas de E/S, la utilización de memoria y el uso de CPU. También se pueden notificar los errores del sistema operativo (por ejemplo, el error al abrir correctamente un archivo).

También debe considerar la infraestructura y los componentes subyacentes en los que se ejecuta el sistema. Las máquinas virtuales, las redes virtuales y los servicios de almacenamiento pueden ser orígenes de importantes contadores de rendimiento de nivel de infraestructura y otros datos de diagnóstico.

Si la aplicación usa otros servicios externos, como un servidor web o un sistema de administración de bases de datos, estos servicios pueden publicar sus propios registros, contadores de rendimiento e información de seguimiento. Algunos ejemplos son las vistas de administración dinámica de SQL Server para realizar un seguimiento de las operaciones realizadas en una base de datos de SQL Server y los registros de seguimiento de Internet Information Server para registrar las solicitudes realizadas en un servidor web.

Como los componentes de un sistema se modifican y se implementan nuevas versiones, es importante poder atribuir problemas, eventos y métricas a cada versión. Esta información debe asociarse a la canalización de la versión para que se pueda hacer un rápido seguimiento de los problemas con una versión concreta de un componente y se rectifiquen.

Los problemas de seguridad pueden producirse en cualquier punto del sistema. Por ejemplo, un usuario podría intentar iniciar sesión con un identificador de usuario o una contraseña no válidos; un usuario autenticado podría intentar obtener acceso no autorizado a un recurso; o bien, un usuario puede facilitar una clave no válida o caducada para tener acceso a información cifrada. Siempre se debe registrar la información relacionada con la seguridad de las solicitudes correctas y erróneas.

La sección [Instrumentación de una aplicación](#instrumenting-an-application) contiene más indicaciones sobre la información que debe capturar, pero hay una gran variedad de estrategias que puede usar para recopilar esta información en primer lugar:

- **Supervisión del sistema y la aplicación**. Esta estrategia usa orígenes internos dentro de la aplicación, marcos de aplicaciones, el sistema operativo y la infraestructura. El código de la aplicación en sí mismo puede generar sus propios datos de supervisión en puntos notables durante el ciclo de vida de una solicitud de cliente. La aplicación puede incluir instrucciones de seguimiento que pueden habilitarse o deshabilitarse de forma selectiva, según las circunstancias. También es posible insertar diagnósticos dinámicamente mediante el uso de un marco de diagnósticos. Normalmente, estos marcos ofrecen complementos que pueden adjuntarse a varios puntos de instrumentación en el código y capturar los datos de seguimiento en dichos puntos.

Además, el código y la infraestructura subyacente pueden generar eventos en puntos críticos. La supervisión de los agentes que están configurados para detectar estos eventos puede registrar la información del evento.

- **Supervisión de los usuarios reales**. Este enfoque registra las interacciones entre un usuario y la aplicación y observa el flujo de cada solicitud y respuesta. Esta información puede tener un objetivo doble: se puede utilizar para medir el uso de cada usuario y se puede utilizar para determinar si los usuarios reciben una calidad de servicio adecuada (por ejemplo, tiempos de respuesta rápidos, una latencia baja y que se produzcan unos errores mínimos). Los datos capturados se pueden usar para identificar áreas problemáticas donde se producen errores con más frecuencia y elementos donde el sistema se ralentiza, posiblemente debido a zonas activas en la aplicación o a alguna otra forma de cuello de botella. Si este enfoque se ha implementado cuidadosamente, quizá sea posible reconstruir los flujos de los usuarios a través de la aplicación para fines de depuración y pruebas.

	> [AZURE.IMPORTANT] Los datos capturados mediante la supervisión de los usuarios reales deben considerarse como muy confidenciales, ya que pueden incluir material privado. Si los datos capturados se guardan, se deben almacenar de forma segura. Si se usan los datos para la supervisión del rendimiento o con fines de depuración, toda la información personal que se pueda identificar debe eliminarse primero.

- **Supervisión de usuarios sintéticos**. En este enfoque, escribe su propio cliente de prueba que simula un usuario y realiza una serie de operaciones configurables pero habituales. Puede realizar un seguimiento del rendimiento del cliente de prueba para ayudar a determinar el estado del sistema. También puede usar varias instancias del cliente de prueba como parte de una operación de prueba de carga para establecer la forma en que el sistema responde en situaciones de estrés y el tipo de resultado de supervisión que se genera en estas condiciones.

	> [AZURE.NOTE] Puede implementar la supervisión de usuarios reales y sintéticos mediante la inclusión de código que realice un seguimiento y controle el tiempo de la ejecución de las llamadas a métodos y otras partes esenciales de una aplicación.

- **Generación de perfiles**. Este enfoque está dirigido principalmente a supervisar y mejorar el rendimiento de la aplicación. En lugar de operar en el nivel funcional que emplea la supervisión de los usuarios reales y sintéticos, captura información de nivel inferior cuando se ejecuta la aplicación. La generación de perfiles puede implementarse mediante el muestreo periódico del estado de ejecución de una aplicación (determinar la parte del código que está ejecutando la aplicación en un momento dado en el tiempo) o mediante el uso de instrumentación que inserte sondeos en el código en momentos importantes (por ejemplo, el inicio y el final de una llamada a un método) y registre los métodos que se invocan, en qué momento y el tiempo que tarda cada llamada. Estos datos pueden analizarse para determinar las partes de la aplicación que podrían producir problemas de rendimiento.

- **Supervisión de extremos**. Esta técnica usa uno o varios extremos de diagnóstico que expone la aplicación específicamente para habilitar la supervisión. Un extremo ofrece un camino al código de la aplicación y puede devolver información sobre el estado del sistema. Distintos extremos podrían centrarse en varios aspectos de la funcionalidad. Puede escribir su propio cliente de diagnósticos que envíe solicitudes periódicas a estos extremos y asimile las respuestas. Este enfoque se describe con más detalle con el [patrón de supervisión de los extremos de estado](https://msdn.microsoft.com/library/dn589789.aspx) en el sitio web de Microsoft.

Para conseguir una cobertura máxima, debe usar estas técnicas combinadas entre sí.

<a name="instrumenting-an-application"></a>
## Instrumentación de una aplicación
La instrumentación es una parte fundamental del proceso de supervisión; solo puede tomar decisiones significativas sobre el rendimiento y el estado de un sistema si captura los datos que le permiten tomar estas decisiones en primer lugar. La información que se recopila mediante instrumentación debe ser suficiente para que pueda evaluar el rendimiento, diagnosticar problemas y tomar decisiones sin que tenga que iniciar sesión en un servidor de producción remoto para realizar el seguimiento (y la depuración) manualmente.

Los datos de instrumentación normalmente incluirán información escrita en registros de seguimiento y métricas:

- El contenido de un registro de seguimiento puede ser el resultado de datos de texto que ha escrito la aplicación, datos binarios creados como resultado de un evento de seguimiento (si la aplicación usa Seguimiento de eventos para Windows, "ETW") o se pueden generar desde los registros del sistema que almacenan los eventos resultantes de partes de la infraestructura, como un servidor web. A menudo, los mensajes de registro de texto están diseñados para ser legibles por las personas, pero también se deben escribir en un formato que permita que un sistema automatizado pueda analizarlos fácilmente. También debe clasificar los registros; no escriba todos los datos de seguimiento en un registro único, use registros independientes para almacenar los resultados de seguimiento de distintos aspectos operativos del sistema. Esto le permite filtrar rápidamente los mensajes de registro mediante la lectura desde el registro adecuado, en lugar de tener que procesar un único archivo largo. Nunca escriba información que tenga requisitos de seguridad diferentes (como información de auditoría y datos de depuración) en el mismo registro.

	> [AZURE.NOTE] Un registro puede implementarse como un archivo en el sistema de archivos o puede contenerse en otro formato, como un blob en un almacenamiento de blobs. La información de registro también se puede mantener en un almacenamiento más estructurado, como las filas de una tabla.

- En general, las métricas simplemente serán una medida o un recuento de algún aspecto o recurso del sistema en un momento determinado con una o varias etiquetas o dimensiones asociadas (a veces se denominan una _muestra_). Una única instancia de una métrica no suele ser útil por separado; en su lugar, las métricas se deben capturar en el tiempo. El aspecto fundamental que se debe tener en cuenta es qué métricas se deben registrar y con qué frecuencia. La generación de datos de métricas con demasiada frecuencia puede suponer una carga adicional significativa en el sistema, mientras que la captura de métricas con poca frecuencia puede provocar que se pierdan las circunstancias que dan lugar a un evento significativo. Las consideraciones variarán de una métrica a otra. Por ejemplo, el uso de CPU en un servidor puede variar considerablemente de un segundo a otro, pero una utilización alta solo se convierte en un problema si persiste un tiempo, durante unos cuantos minutos.

<a name="information-for-correlating-data"></a>
### Información para la correlación de datos
Puede supervisar con facilidad los contadores de rendimiento individuales de nivel de sistema, capturar métricas de recursos y obtener información de seguimiento de la aplicación de varios archivos de registro; pero algunas formas de supervisión requieren que la fase de análisis y diagnósticos de la canalización de supervisión haga una correlación de los datos recuperados desde varios orígenes. Estos datos pueden ser de distintas formas en los datos sin procesar y se deben proporcionar suficientes datos de instrumentación al proceso de análisis para que pueda asignar estas formas diferentes. Por ejemplo, en el nivel de marco de aplicación, una tarea puede identificarse mediante un identificador de subproceso, pero dentro de una aplicación, el mismo trabajo se podría asociar con el identificador del usuario que realiza la tarea. Además, es poco probable que haya una asignación 1:1 entre los subprocesos y las solicitudes de usuario, dado que las operaciones asincrónicas pueden reutilizar los mismos subprocesos para realizar operaciones en nombre de más de un usuario. Para complicarlo todo aún más, una única solicitud puede controlarse mediante más de un subproceso a medida que la ejecución fluye a través del sistema. Si es posible, asocie cada solicitud a un identificador de actividad único que se propague a través del sistema como parte del contexto de la solicitud (la técnica para generar e incluir identificadores de actividad en la información de seguimiento dependerá de la tecnología que se use para capturar los datos de seguimiento).

Todos los datos de supervisión deben tener una marca de tiempo del mismo tipo. Para mantener la coherencia, registre todas las fechas y horas según la hora universal coordinada. Eso le ayudará a realizar un seguimiento de las secuencias de eventos con más facilidad.

> [AZURE.NOTE] Es posible que no se puedan sincronizar los equipos que funcionan en redes y zonas horarias diferentes; por tanto, no debería depender solo del uso de las marcas de tiempo para hacer una correlación de los datos de instrumentación que abarquen varias máquinas.

### ¿Qué información deben incluir los datos de instrumentación?
Al decidir qué datos de instrumentación necesita recopilar, tenga en cuenta los siguientes puntos:

- La información que capturan los eventos de seguimiento debe ser legible para las personas y las máquinas. Debe adoptar esquemas bien definidos para esta información a fin de facilitar el procesamiento automatizado de datos de registro entre sistemas y ofrecer coherencia para las operaciones y el personal de ingeniería que lee los registros. Incluya información del entorno, por ejemplo, el entorno de implementación, la máquina en la que se ejecuta el proceso, los detalles del proceso y la pila de llamadas.  
- La generación de perfiles puede suponer una sobrecarga significativa en el sistema y solo debe habilitarse cuando sea necesario. La generación de perfiles mediante los registros de instrumentación registra un evento (por ejemplo, una llamada a un método) cada vez que se produce, mientras que el muestreo solo registra los eventos seleccionados. La selección puede basarse en tiempo (una vez cada N segundos) en o frecuencia (una vez cada N solicitudes). Si los eventos se producen con mucha frecuencia, la generación de perfiles mediante instrumentación puede provocar un exceso de carga y afectar al rendimiento general. En este caso, el enfoque de muestreo puede ser preferible. Sin embargo, si la frecuencia de los eventos es baja, el muestreo podría perderlos y, en este caso, la instrumentación podría ser el mejor enfoque.
- Facilite suficiente contexto para permitir que un desarrollador o administrador determine el origen de cada solicitud. Esto puede incluir alguna tipo de identificador de actividad que permita determinar una instancia concreta de una solicitud, e información que pueda usarse para hacer una correlación de esta actividad con el trabajo de cálculo realizado y los recursos utilizados. Tenga en cuenta que este trabajo puede cruzar los límites de los procesos o las máquinas. Para la medición, el contexto también debe incluir (directa o indirectamente a través de otros información correlacionada) una referencia al cliente que motivó que se realizará la solicitud. Este contexto ofrece información valiosa sobre el estado de la aplicación en el momento en que se capturaron los datos de supervisión.
- Registre todas las solicitudes y las ubicaciones o regiones desde las que se realizan estas solicitudes. Esta información puede ayudar a determinar si hay zona activas específicas de la ubicación y ofrece datos que pueden resultar útiles para determinar si se deben crear particiones de una aplicación o de los datos que utiliza.
- Registre y capture los detalles de las excepciones con cuidado. A menudo, la información de depuración esencial se pierde como resultado de un control de excepciones deficiente. Capture los detalles completos de las excepciones que produce la aplicación, entre los que se deben incluir las excepciones internas y otra información de contexto, con la pila de llamadas si es posible.
- Sea coherente en los datos que los distintos elementos de la aplicación capturan, ya que esto puede ayudarle a analizar los eventos y hacer una correlación de los mismos con las solicitudes de usuario. Considere la posibilidad de usar un paquete de registros completo y configurable para recopilar información, en lugar de depender de que los desarrolladores implementen partes distintas del sistema, todas con el mismo enfoque. Recopile datos de contadores de rendimiento clave, como el volumen de E/S que se procesa, la utilización de la red, el número de solicitudes, el uso de memoria y la utilización de CPU. Algunos servicios de infraestructuras pueden ofrecer sus propios contadores de rendimiento específicos, como el número de conexiones a una base de datos, la velocidad a la que se realizan las transacciones y el número de transacciones que se realizan de forma correcta o errónea. Las aplicaciones también pueden definir sus propios contadores de rendimiento específicos.
- Registre todas las llamadas que se realizan a servicios externos, como los sistemas de bases de datos, los servicios web u otros servicios de nivel de sistema que se ofrezcan como parte de la infraestructura (por ejemplo, el almacenamiento en caché de Azure). Registre información sobre el tiempo necesario para realizar cada llamada y si se realizó correctamente o de forma errónea. Si es posible, capture información sobre todos los reintentos y errores de los errores transitorios que se produzcan.

### Garantizar la compatibilidad con los sistemas de telemetría
En muchos casos, la información que produce la instrumentación se genera como una serie de eventos y pasa a un sistema de telemetría independiente para su procesamiento y análisis. Un sistema de telemetría suele ser independiente de cualquier aplicación o tecnología específicas, pero espera que la información siga un formato determinado que normalmente define un esquema. El esquema especifica eficazmente un contrato que define los campos y tipos de datos que puede recopilar el sistema de telemetría. El esquema debe generalizarse para permitir que los datos procedan de una variedad de plataformas y dispositivos.

Un esquema común debe incluir campos que sean comunes a todos los eventos de instrumentación, como el nombre del evento, la hora del evento, la dirección IP del remitente y los detalles necesarios para hacer una correlación con otros eventos (por ejemplo, un identificador de usuario, un identificador de dispositivo y un identificador de la aplicación). Recuerde que los eventos se pueden generar mediante cualquier número de dispositivos, por lo que el esquema no debe depender del tipo de dispositivo. Además, una serie de dispositivos diferentes puede generar eventos para la misma aplicación; la aplicación podría admitir movilidad o algún otro tipo de distribución entre dispositivos. Idealmente, la mayoría de estos campos deberían asignarse a la salida de la biblioteca de registro que se usa para capturar los eventos.

El esquema también podría incluir campos de dominio que sean relevantes para un determinado escenario común en las distintas aplicaciones. Puede tratarse de información sobre las excepciones, los eventos de inicio y finalización de la aplicación y las llamadas de API de servicios web correctas y erróneas. Todas las aplicaciones que usan el mismo conjunto de campos de dominio deben emitir el mismo conjunto de eventos, lo que permite que se genere un conjunto de informes y análisis comunes.

Por último, un esquema podría contener campos personalizados para capturar los detalles de los eventos específicos de la aplicación.

### Prácticas recomendadas para la instrumentación de aplicaciones
En la lista siguiente se resumen las prácticas recomendadas para instrumentar una aplicación distribuida que se ejecuta en la nube.

- Haga que los registros sean fáciles de leer y de analizar. Use registros estructurados siempre que sea posible. Sea conciso y descriptivo en los mensajes de registro.
- En todos los registros, identifique el origen y ofrezca contexto e información de tiempo conforme se escriba cada entrada del registro.
- Use el mismo formato y la misma zona horaria para todas las marcas de tiempo. Esto le ayudará a hacer una correlación de los eventos para las operaciones que abarcan el hardware y los servicios que se ejecutan en regiones geográficas distintas.
- Clasifique los registros y escriba mensajes en el archivo de registro adecuado.
- No revele información confidencial sobre el sistema o información personal sobre los usuarios. Elimine esta información antes de que se registre, pero asegúrese de que se conservan los detalles relevantes. Por ejemplo, quite el identificador y la contraseña de las cadenas de conexión de la base de datos, pero escriba la información restante en el registro para que un analista pueda determinar si el sistema está accediendo a la base de datos correcta. Registre todas las excepciones críticas, pero permita que el administrador active y desactive el registro de los niveles inferiores de las excepciones y advertencias. Además, capture y registre toda la información lógica de reintentos. Estos datos pueden ser útiles para supervisar el estado transitorio del sistema.
- Realice un seguimiento de las llamadas de proceso, como las solicitudes de bases de datos o servicios web externos.
- No mezcle mensajes de registro con distintos requisitos de seguridad en el mismo archivo de registro. Por ejemplo, no escriba información sobre la depuración y la auditoría en el mismo registro.
- Con la excepción de los eventos de auditoría, todas las llamadas de registro deben ser operaciones que no requieran intervención manual tras su activación y no deben bloquear el progreso de las operaciones de negocio. Los eventos de auditoría son una excepción porque son fundamentales para la empresa y se pueden clasificar como una parte crucial de las operaciones de negocio.
- El registro debe ser extensible y no debe tener ninguna dependencia directa de un destino concreto. Por ejemplo, en lugar de escribir la información mediante _System.Diagnostics.Trace_, defina una interfaz abstracta (como _ILogger_) que exponga métodos de registro y que pueda implementarse mediante cualquier medio adecuado.
- Todos los registros deben ser a prueba de errores y nunca deben desencadenar errores en cascada. El registro no debe producir excepciones.
- Trate la instrumentación como un proceso iterativo continuo y revise los registros con regularidad, no solo cuando haya un problema.

## Recopilación y almacenamiento de datos
La fase de recopilación del proceso de supervisión se ocupa de recuperar la información que genera la instrumentación, dar formato a estos datos para que resulten más fáciles de usar en la fase de análisis y cálculo, y de guardar los datos transformados en un almacenamiento confiable y accesible. Los datos de instrumentación que recopila de distintas partes de un sistema distribuido se podrían mantener en una variedad de ubicaciones y con diferentes formatos. Por ejemplo, el código de su aplicación podría generar archivos de registro de seguimiento y datos de registro de eventos de la aplicación, mientras que los contadores de rendimiento que supervisan aspectos clave de la infraestructura que usa su aplicación podrían capturarse mediante otras tecnologías. Los servicios y componentes de terceros que use su aplicación pueden proporcionar información de instrumentación en formatos diferentes, con archivos de seguimiento independientes, almacenamiento en blobs o incluso un almacén de datos personalizado.

La recopilación de datos se realiza a menudo mediante la implementación de un servicio de recopilación que se puede ejecutar de forma independiente de la aplicación que genera los datos de instrumentación. En la Ilustración 2 se muestra un ejemplo de esta arquitectura, donde se resalta el subsistema de recopilación de datos de instrumentación.

![](media/best-practices-monitoring/TelemetryService.png)

_Ilustración 2: Recopilación de datos de instrumentación_

Tenga en cuenta que se trata de una vista simplificada. El servicio de recopilación no es necesariamente un proceso único y puede constar de varias partes que se ejecuten en equipos diferentes, como se describe en las secciones siguientes. Además, si es necesario realizar el análisis de algunos datos de telemetría rápidamente (análisis en vivo, como se describe en la sección [Compatibilidad con el análisis en caliente, intermedio y en frío](#supporting-hot-warm-and-cold-analysis) que se encuentra más adelante en este documento), los componentes locales que se encuentren fuera del servicio de colección podrían realizar las tareas de análisis inmediatamente. En la Ilustración 2 se ilustra esta situación para los eventos seleccionados; después del procesamiento analítico, los resultados pueden enviarse directamente al subsistema de visualización y alertas. Los datos que se someten a análisis en frío o intermedio se mantienen en el almacenamiento mientras esperan el procesamiento.

Para los servicios y aplicaciones de Azure, Diagnósticos de Azure (WAD) ofrece una solución posible para capturar los datos. WAD recopila datos de los siguientes orígenes para cada nodo de ejecución, los agrupa y, después, los carga en el almacenamiento de Azure:

- Registros IIS
- Registros de solicitudes con error de IIS
- Registros de eventos de Windows
- Contadores de rendimiento
- Volcados de memoria
- Registros de infraestructura de diagnóstico de Azure  
- Registros de errores personalizados
- .NET EventSource
- ETW basado en manifiesto

Para obtener más información, consulte el artículo [Azure: conceptos básicos de la telemetría y solución de problemas](http://social.technet.microsoft.com/wiki/contents/articles/18146.windows-azure-telemetry-basics-and-troubleshooting.aspx) en el sitio web de Microsoft.

### Estrategias para la recopilación de datos de instrumentación
Dada la naturaleza flexible de la nube y para evitar la necesidad de recuperar manualmente los datos de telemetría de todos los nodos del sistema, debe organizar los datos para que se transfieran a una ubicación central y consolidada. En un sistema que abarque varios centros de datos, puede ser útil que en primer lugar se recopilen, consoliden y almacenen los datos en una región de una forma basada en regiones y, después, se agreguen los datos regionales en un solo sistema central.

Para optimizar el uso del ancho de banda, puede elegir transferir datos menos urgentes en fragmentos, como lotes. Sin embargo, los datos no deben retrasarse indefinidamente, especialmente si contienen información confidencial en el tiempo.

#### _Extracción e inserción de datos de instrumentación_
El subsistema de recopilación de datos de instrumentación puede recuperar de forma activa datos de instrumentación de los distintos registros y otros orígenes para cada instancia de la aplicación (el _modelo de extracción_) o puede actuar como receptor pasivo esperando a que los datos se envíen desde los componentes que conforman cada instancia de la aplicación (el _modelo de inserción_).

Un enfoque para implementar el modelo de extracción es usar agentes de supervisión que se ejecuten localmente con cada instancia de la aplicación. Un agente de supervisión es un proceso independiente que recupera periódicamente (extrae) datos de telemetría recopilados en el nodo local y escribe esta información directamente en un almacenamiento centralizado que comparten todas las instancias de la aplicación. Este es el mecanismo que implementa WAD. Cada instancia de un rol de trabajo o web de Azure puede configurarse para que capture información de diagnóstico y otra información de seguimiento, que se almacena localmente. El agente de supervisión que se ejecuta al mismo tiempo copia los datos especificados en el almacenamiento de Azure. La página [Habilitación de diagnósticos en Servicios en la nube y Máquinas virtuales de Azure](cloud-services-dotnet-diagnostics.md) del sitio web de Microsoft ofrece más detalles sobre este proceso. Algunos elementos, como los registros de IIS, los volcados de memoria y los registros de errores personalizados se escriben en el almacenamiento de blobs, mientras que los datos del registro de eventos de Windows, los eventos ETW y los contadores de rendimiento se registran en el almacenamiento de tablas. En la Ilustración 3 se muestra este mecanismo:

![](media/best-practices-monitoring/PullModel.png)

_Ilustración 3: Uso de un agente de supervisión para extraer información y escribir en el almacenamiento compartido_

> [AZURE.NOTE] El uso de un agente de supervisión es ideal para capturar datos de instrumentación que se extraen naturalmente de un origen de datos, como información de las vistas de administración de SQL Server o la longitud de una cola del Bus de servicio de Azure.


Los datos de telemetría para una aplicación a pequeña escala que se ejecuta en un número limitado de nodos se pueden almacenarse de forma factible en una sola ubicación mediante el enfoque descrito anteriormente. Sin embargo, una aplicación de la nube global, compleja y altamente escalable puede generar con facilidad grandes volúmenes de datos de cientos roles web y de trabajo, particiones de base de datos y otros servicios. Esta avalancha de datos podría sobrecargar fácilmente el ancho de banda de E/S disponible en una única ubicación central. Por lo tanto, la solución de telemetría debe ser escalable para evitar que actúe como un cuello de botella a medida que el sistema se amplíe y lo ideal es incorporar un grado de redundancia para reducir el riesgo de perder información importante de supervisión (por ejemplo, los datos de auditoría o de facturación) si se produce un error en una parte del sistema.

Para resolver estos problemas, puede implementar colas. En la Ilustración 4 se muestra esta estructura. En esta arquitectura, el agente de supervisión local (si se puede configurar adecuadamente) o el servicio de recopilación de datos personalizados (si no) publica datos en una cola y un proceso independiente que se ejecuta de forma asincrónica (el servicio de escritura en almacenamiento en la Ilustración 4) toma los datos de esa cola y los escribe en el almacenamiento compartido. Una cola de mensajes es adecuada para este escenario, ya que ofrece semántica al menos una vez, lo que garantiza que una vez quedan registrados, los datos de la cola no se perderán. El servicio de escritura en almacenamiento se puede implementar mediante un rol de trabajo independiente.

![](media/best-practices-monitoring/BufferedQueue.png)

_Ilustración 4: Uso de una cola para almacenar en búfer datos de instrumentación_

El servicio de recopilación de datos locales puede agregar datos a una cola en cuanto se reciben. La cola actúa como un búfer y el servicio de escritura en almacenamiento puede recuperar y escribir los datos a su propio ritmo. De forma predeterminada, una cola funciona según el principio de "el primero que entra es el primero en salir", pero puede dar prioridad a ciertos mensajes para acelerarlos a través de la cola si contienen datos que se deben controlar más rápidamente. Para obtener más información, consulte más adelante el patrón de [Cola prioritaria](https://msdn.microsoft.com/library/dn589794.aspx). Como alternativa, podría usar canales distintos (por ejemplo, los temas del Bus de servicio) para dirigir datos a destinos diferentes según el tipo de procesamiento analítico necesario.

Para la escalabilidad, puede ejecutar varias instancias del servicio de escritura en almacenamiento. Si hay un gran volumen de eventos, puede usar un concentrador de eventos para enviar los datos a recursos de procesamiento diferentes para procesarlos y almacenarlos.

<a name="consolidating-instrumentation-data"></a>
#### _Consolidación de datos de instrumentación_
Los datos de instrumentación que recupera el servicio de recopilación de datos a partir de una única instancia de una aplicación ofrecen una vista localizada del estado y rendimiento de esa instancia. Para evaluar el estado general del sistema, es necesario consolidar algunos aspectos de los datos en las vistas locales juntas. Esto se puede realizar después de que se hayan almacenado los datos, pero en algunos casos también se puede lograr a medida que se recopilan los datos. En lugar de que se escriban directamente en el almacenamiento compartido, los datos de instrumentación podrían pasar a través de un servicio de consolidación de datos independiente que combine los datos y actúe como un proceso de filtrado y limpieza. Por ejemplo, los datos de instrumentación que incluyen la misma información de correlación como un identificador de actividad se pueden combinar (es posible que un usuario comience a realizar una operación de negocio en un nodo y, después, se transfiera a otro nodo en caso de que se produzca un error en el nodo o según cómo esté configurado el equilibrio de carga). Este proceso también puede detectar y quitar los datos duplicados (siempre es una posibilidad, si el servicio de telemetría usa colas de mensajes para insertar datos de instrumentación en el almacenamiento). En la Ilustración 5 se muestra un ejemplo de esta estructura.

![](media/best-practices-monitoring/Consolidation.png)

_Ilustración 5: Uso de un servicio independiente para consolidar y limpiar los datos de instrumentación_

### Almacenamiento de datos de instrumentación
En la información anterior se ha descrito una vista bastante simple de la manera en que se almacenan los datos de instrumentación. En realidad, puede tener sentido almacenar los distintos tipos de información con las tecnologías que sean más adecuadas para la manera en que sea más probable que se use cada tipo. Por ejemplo, el almacenamiento de tablas y blobs de Azure tiene algunas similitudes en la forma en que se obtiene acceso, pero existen limitaciones relativas a las operaciones que puede realizar con ellos y la granularidad de los datos que contienen es muy distinta. Si necesita realizar más operaciones de análisis o precisa capacidades de búsqueda de texto completo en los datos, puede ser más apropiado usar el almacenamiento de datos que ofrezca capacidades que estén optimizadas para tipos concretos de accesos a datos y consultas. Por ejemplo, los datos de los contadores de rendimiento podrían almacenarse en una base de datos SQL para habilitar el análisis ad hoc; los registros de seguimiento podrían almacenarse mejor en Azure DocumentDB; se podría escribir la información de seguridad en HDFS; la información que requiere búsqueda de texto completo podría almacenarse con Elastic Search (que también puede acelerar las búsquedas mediante la indexación enriquecida). Puede implementar un servicio adicional que recupere de forma periódica los datos del almacenamiento compartido, cree particiones de los mismos y los filtre según su propósito para, después, escribirlos en un conjunto de almacenes de datos, como se muestra en la Ilustración 6. Un enfoque alternativo es incluir esta funcionalidad en el proceso de consolidación y limpieza y escribir los datos directamente en esos almacenes a medida que se recuperan, en lugar de guardarlos en un área intermedia de almacenamiento compartido. Cada enfoque tiene sus ventajas e inconvenientes. La implementación de un servicio de particiones independiente reduce la carga en el servicio de consolidación y limpieza y permite que al menos algunos de los datos con particiones se vuelvan a generar si es necesario (según la cantidad de datos que se conserven en el almacenamiento compartido). Sin embargo, consume recursos adicionales y puede haber un retraso entre los datos de instrumentación que se reciben de cada instancia de la aplicación y la conversión de estos datos en información útil.

![](media/best-practices-monitoring/DataStorage.png)

_Ilustración 6. Creación de particiones de los datos según los requisitos analíticos y de almacenamiento_

Es posible que se necesiten los mismos datos de instrumentación para más de un fin. Por ejemplo, los contadores de rendimiento se pueden usar para ofrecer una vista histórica del rendimiento del sistema a lo largo del tiempo, pero esta información también podría combinarse con otros datos de uso para generar información de facturación del cliente. En estas situaciones, los mismos datos se podrían enviar a más de un destino, como una base de datos documental que pueda actuar como un almacén a largo plazo para mantener información de facturación y un almacén multidimensional para controlar los análisis complejos del rendimiento.

También debe considerar la urgencia con que se requieren los datos. Es necesario que el acceso a los datos que proporcionan información para necesidades de alertas sea rápido, de forma que deben mantenerse en almacenamientos de datos rápidos y se deben indexar o estructurar para optimizar las consultas que realiza el sistema de alertas. En algunos casos, puede ser necesario que el servicio de telemetría recopile los datos de cada nodo para dar formato y guardar los datos de forma local, de forma que una instancia local del sistema de alertas pueda notificar rápidamente cualquier problema. Los mismos datos se pueden enviar al servicio de escritura en almacenamiento que se muestra en los diagramas anteriores y se pueden almacenar de forma centralizada si también se necesitan para otros fines.

La información que se usa para análisis más considerados, informes y para detectar las tendencias históricas es menos urgente y se puede almacenar de manera que admita minería de datos y consultas ad hoc. Para obtener más información, consulte la sección [Compatibilidad con el análisis en caliente, intermedio y en frío](#supporting-hot-warm-and-cold-analysis) que se encuentra más adelante en este documento.

#### _Rotación de registros y retención de datos_
La instrumentación puede generar grandes volúmenes de datos. Estos datos se pueden almacenar en varios lugares, comenzando por los archivos de registro sin procesar, los archivos de seguimiento y otra información capturada en cada nodo para las vistas consolidada, limpia y con particiones de los datos que se mantienen en el almacenamiento compartido. En algunos casos, cuando los datos se han procesado y transferido, los datos originales sin procesar se pueden eliminar de cada nodo. En otros casos, puede ser necesario o simplemente útil guardar la información sin procesar. Por ejemplo, es mejor que los datos generados con fines de depuración queden disponibles en el formato sin procesar, pero pueden descartarse después bastante rápido cuando se han subsanados los errores. Los datos de rendimiento a menudo tienen una vida más larga para que puedan usarse para identificar tendencias de rendimiento y para planificar la capacidad. La vista consolidada de estos datos normalmente se mantiene en línea durante un período limitado permitir un acceso rápido, después del cual se pueden archivar o descartar. Es posible que los datos recopilados para la medición y facturación de los clientes se guarden de forma indefinida. Además, los requisitos normativos podrían obligar a que la información recopilada con fines de auditoría y seguridad también se deba almacenar y guardar. Estos datos también son confidenciales y es necesario cifrarlos o aplicarles otro tipo de protección para evitar su manipulación. Nunca debería registrar información como las contraseñas de los usuarios u otra información que se pueda utilizar para cometer fraudes de identidad; dichos detalles deben eliminarse de los datos antes de almacenarlos.

#### _Reducción de la resolución_
Con frecuencia, resulta útil almacenar los datos históricos para poder reconocer tendencias a largo plazo. En lugar de guardar los datos antiguos en su totalidad, es posible realizar una muestra reducida de los datos para disminuir su resolución y ahorrar costos de almacenamiento. Como ejemplo, en lugar de guardar los indicadores de rendimiento minuto a minuto, los datos con una antigüedad superior a un mes se pueden consolidar para formar una vista hora a hora.

### Prácticas recomendadas para recopilar y almacenar información de registro
En la lista siguiente se resumen las prácticas recomendadas para capturar y almacenar información de registro.

- El agente de supervisión o el servicio de recopilación de datos deben ejecutarse como un servicio fuera del proceso y deben ser fáciles de implementar.
- Todos los resultados del agente de supervisión o el servicio de recopilación de datos deben tener un formato independiente de la máquina, del sistema operativo y del protocolo de red. Por ejemplo, emita la información en un formato autodescriptivo como JSON, MessagePack o Protobuf, en lugar de ETL/ETW. El uso de un formato estándar permite que el sistema cree canalizaciones de procesamiento; se pueden integrar fácilmente componentes que leen, transforman y dan como resultados datos en el formato acordado.
- El proceso de supervisión y recopilación de datos debe ser a prueba de errores y no debe desencadenar condiciones de error en cascada.
- Si se produce un error transitorio mientras se envía información a un receptor de datos, el servicio de recopilación de datos o el agente de supervisión deben estar preparados para volver a ordenar los datos de telemetría para que la información más reciente se envíe en primer lugar (el servicio de recopilación de datos o el agente de supervisión pueden optar por quitar los datos más antiguos o guardarlos localmente y transmitirlos más tarde para poner todo al día, según su propio criterio).

## Análisis de los datos y diagnóstico de problemas
Una parte importante del proceso de supervisión y diagnósticos es el análisis de los datos recopilados para obtener una imagen del estado general del sistema. Debería definir sus propios KPI y métricas de rendimiento y es importante entender cómo se pueden estructurar los datos que se han recopilado para cumplir los requisitos de análisis. También es importante comprender cómo se hace la correlación de los datos capturados en distintos archivos de registro y métricas, ya que esta información puede ser clave para realizar un seguimiento de una secuencia de eventos y ayudar a diagnosticar los problemas que surjan.

Como se describe en la sección [Consolidación de los datos de instrumentación](#consolidating-instrumentation-data), los datos de cada parte del sistema se suelen capturar de forma local, pero normalmente tienen que combinarse con datos generados en otros sitios que participen en el sistema. Esta información requiere que se realice una correlación con cuidado para garantizar que los datos se combinan con precisión. Por ejemplo, los datos de uso de una operación pueden abarcar un nodo que hospede un sitio web al que un usuario se conecta, un nodo que ejecuta un servicio independiente al que se accede como parte de esta operación y almacenamiento de datos que se mantiene en un nodo adicional. Esta información debe unirse para ofrecer una visión general del uso de recursos y procesamiento por parte de la operación. Parte del preprocesamiento y el filtrado de los datos puede tener lugar en el nodo en el que se capturan los datos, mientras que es más probable que la agregación y el formato ocurran en un nodo central.

<a name="supporting-hot-warm-and-cold-analysis"></a>
### Compatibilidad con análisis en caliente, intermedio y en frío
El análisis y la operación de formato de los datos para fines de visualización, informes y alertas puede ser un proceso complejo que consume su propio conjunto de recursos. Algunas formas de supervisión son críticas en el tiempo y requieren un análisis inmediato de datos para ser eficaces. Esto se conoce como _análisis en caliente_. Algunos ejemplos son los análisis que se requieren para las alertas y algunos aspectos de la supervisión de la seguridad (por ejemplo, la detección de un ataque en el sistema). Los datos necesarios para estos fines deben estar rápidamente disponibles y estructurarse para un procesamiento eficaz; en algunos casos puede ser necesario mover el procesamiento de análisis a los nodos individuales en los que se mantienen los datos.

Otras formas de análisis son menos críticas en el tiempo y pueden requerir algunos cálculos y agregaciones cuando se han recibido los datos sin procesar. Esto se conoce como _análisis intermedio_. El análisis del rendimiento se encuentra con frecuencia en esta categoría. En este caso, es estadísticamente poco probable que un evento de rendimiento individual y aislado sea significativo (podría deberse a un problema o un pico repentinos), mientras que los datos de una serie de eventos deberían proporcionar una imagen más confiable del rendimiento del sistema. El análisis intermedio también puede usarse para ayudar a diagnosticar problemas de estados. Un evento de estado normalmente se procesa mediante la realización de análisis en caliente y puede generar una alerta inmediatamente. Un operador debe ser capaz de profundizar en los motivos del evento de estado mediante el examen de los datos de la ruta intermedia; estos datos deben contener información sobre los eventos que conducen al problema que provocó el evento de estado.

Algunos tipos de supervisión generan datos a un plazo más largo y el análisis se puede realizar en una fecha posterior, posiblemente según una programación predefinida. En algunos casos, es posible que el análisis necesite realizar un filtrado complejo de grandes volúmenes de datos capturados durante un período de tiempo. Esto se conoce como _análisis en frío_. El requisito clave es que los datos se almacenen de forma segura cuando se hayan capturado. Por ejemplo, la supervisión del uso y la auditoría requieren una imagen precisa del estado del sistema en puntos regulares en el tiempo, pero esta información de estado no tiene que estar disponible para su procesamiento en cuanto se ha recopilado. El análisis en frío también puede usarse para ofrecer los datos para análisis predictivos del estado. La información histórica recopilada durante un período de tiempo especificado puede usarse junto con los datos de estado actuales (recuperados de la ruta caliente) para identificar tendencias que pueden producir próximamente problemas de mantenimiento. En estos casos, puede ser necesario generar una alerta para permitir que se realice la acción correctiva.

### Correlación de datos
Los datos que captura la instrumentación pueden proporcionar una instantánea del estado del sistema, pero el propósito del análisis es que esta información sea procesable. Por ejemplo, ¿qué ha provocado una carga intensa de E/S a nivel del sistema en un momento determinado? ¿Es el resultado de un gran número de operaciones de base de datos? ¿Se refleja eso en los tiempos de respuesta de la base de datos, el número de transacciones por segundo y los tiempos de respuesta de la aplicación en el mismo momento? Si es así, podría usarse una acción correctora que pueda reducir la carga para compartir los datos con más servidores. Además, las excepciones se pueden producir como resultado de un error en cualquier nivel del sistema y una excepción en un nivel a menudo desencadena otro error en el nivel superior. Por estas razones, necesita poder hacer una correlación de los distintos tipos de datos de supervisión de cada nivel para producir una vista general del estado del sistema y las aplicaciones que se ejecutan en él. Puede usar esta información para tomar decisiones sobre si el sistema funciona de forma aceptable o no y determinar lo que se puede hacer para mejorar la calidad del sistema.

Como se describe en la sección [Información para la correlación de datos](#information-for-correlating-data), debe asegurarse de que los datos de instrumentación sin procesar incluyen suficiente información del contexto y el identificador de la actividad para admitir las agregaciones necesarias para hacer una correlación de los eventos. Además, estos datos se pueden mantener en formatos distintos y puede ser necesario analizar esta información para convertirla en un formato estandarizado para fines de análisis.

### Solución de problemas y diagnóstico de problemas
Es necesario que el diagnóstico sea capaz de determinar la causa de los errores o los comportamientos inesperados, incluida la realización del análisis de causa raíz. La información normalmente necesaria incluye:

- Información detallada de los registros de eventos y seguimientos de todo el sistema o de un subsistema concreto durante un período de tiempo especificado.
- Seguimientos de pila completos resultantes de las excepciones y los errores de cualquier nivel especificado que se produzcan dentro del sistema o de un subsistema concreto durante un período de tiempo especificado.
- Volcados de memoria de los procesos erróneos en cualquier lugar del sistema o de un subsistema concreto durante un período de tiempo especificado.
- Registros de actividad que registran las operaciones que realizan todos los usuarios o los usuarios seleccionados durante un período de tiempo especificado.

El análisis de los datos para fines de solución de problemas requiere a menudo unos profundos conocimientos técnicos de la arquitectura del sistema y los distintos componentes que conforman la solución. Como resultado, normalmente requiere un alto grado de intervención manual para interpretar los datos, establecer la causa de los problemas y recomendar una estrategia adecuada para corregirlos. Puede ser apropiado simplemente almacenar una copia de esta información en su formato original que esté disponible para que un experto la analice en frío.

## Visualización de los datos y generación de alertas
Un aspecto importante de cualquier sistema de supervisión es la capacidad para presentar los datos de tal manera que un operador pueda detectar rápidamente las tendencias o los problemas e informar a un operador si se ha producido un evento significativo que pueda requerir atención.

La presentación de los datos puede tener varias formas, incluida la visualización mediante paneles, las alertas y los informes.

### Visualización con paneles
La manera más común para visualizar los datos es usar paneles que puedan mostrar información como una serie de gráficas, gráficos o alguna otra manera visual. Estos elementos podrían parametrizarse y un analista debería ser capaz de seleccionar los parámetros importantes (por ejemplo, el período de tiempo) para cualquier situación concreta. Los paneles pueden organizarse de forma jerárquica, donde los paneles de nivel superior ofrezcan una visión general de cada aspecto del sistema, pero con la posibilidad de que el operador pueda profundizar y ver los detalles. Por ejemplo, un panel que muestre la E/S global del disco del sistema debe permitir que un analista profundice en los datos y vea las tasas de E/S de cada disco individual para determinar si uno o varios dispositivos concretos tienen un volumen de tráfico desproporcionado. Lo ideal es que el panel también muestre la información relacionada, como el origen de cada solicitud (el usuario o la actividad) que está generando esa E/S. Esta información podría usarse entonces para determinar si (y cómo) se puede distribuir la carga uniformemente entre los dispositivos y si el sistema funcionaría mejor si se agregaran más dispositivos. Un panel también puede usar códigos de colores u otro tipo de indicaciones visuales para mostrar los valores que parezcan irregulares o que están fuera de un intervalo esperado. Con el ejemplo anterior, un disco con una tasa de E/S que se acerca a su capacidad máxima durante un período prolongado (un disco caliente) podría destacarse en rojo, un disco con una tasa de E/S que se ejecute periódicamente a su límite máximo durante breves períodos (un disco intermedio) podría aparecer resaltado en amarillo y un disco con un uso normal podría mostrarse en verde.

Tenga en cuenta que para que un sistema de paneles funcione de forma eficaz, debe tener los datos sin procesar con los que trabajar. Si está creando su propio sistema de paneles o usa un panel que ha desarrollado otra organización, debe entender qué datos de instrumentación que necesita recopilar, en qué niveles de granularidad y qué formato se debe dar para que el panel los use.

Un buen panel no solo muestra información, sino que también ofrece un medio para permitir que un analista plantee preguntas ad hoc sobre esa información. Algunos sistemas ofrecen herramientas de administración que un operador puede usar para realizar estas tareas y explorar los datos subyacentes. Como alternativa, según el repositorio utilizado para almacenar esta información, puede ser posible consultar estos datos directamente o importarlos en herramientas como Microsoft Excel para realizar más análisis e informes.

> [AZURE.NOTE] Debe restringir el acceso a los paneles para el personal autorizado; esta información puede ser confidencial a efectos comerciales. También se deben proteger los datos subyacentes que presenta el panel para impedir que los usuarios los cambien.

### Generación de alertas
Las alertas son el proceso de análisis de los datos de supervisión e instrumentación y la generación de una notificación si se detecta un evento significativo.

Las alertas se usan para garantizar que el sistema esté correcto, con capacidad de respuesta y seguro. Es una parte importante de cualquier sistema que ofrezca garantías de rendimiento, disponibilidad y privacidad a los usuarios, y es posible que haya que actuar sobre los datos inmediatamente. Es posible que haya que informar a un operador del evento que desencadenó la alerta. Las alertas también pueden usarse para invocar funciones del sistema como el escalado automático.

Normalmente, las alertas dependen de los siguientes datos de instrumentación:

- Eventos de seguridad. Si los registros de eventos indican que se están produciendo errores de autorización o autenticaciones repetidos, el sistema puede estar sufriendo un ataque y debe informarse a un operador.
- Métricas de rendimiento. El sistema debe responder rápidamente si una métrica de rendimiento determinada supera un umbral especificado.
- Información de disponibilidad. Si se detecta un error, puede ser necesario reiniciar rápidamente uno o varios subsistemas o una conmutación por error a un recurso de copia de seguridad. Los errores repetidos de un subsistema pueden indicar problemas más graves.

Los operadores pueden recibir información de alertas mediante varios canales de entrega como el correo electrónico, un dispositivo buscapersonas o un mensaje de texto SMS. Una alerta también puede incluir una indicación de la gravedad de la situación que se haya producido. Muchos sistemas de alertas admiten grupos de suscriptores y se puede enviar el mismo conjunto de alertas a todos los operadores que sean miembros del mismo grupo.

Un sistema de alertas debe ser personalizable y se puede ofrecer los valores adecuados de los datos de instrumentación subyacente como parámetros. Este enfoque permite que un operador filtre los datos y se centre en los umbrales o combinaciones de valores que sean de interés. Tenga en cuenta que en algunos casos pueden proporcionarse datos de instrumentación sin procesar al sistema de alerta, mientras que en otras situaciones podría ser más apropiado facilitar datos agregados (por ejemplo, una alerta podría activarse si el uso de CPU de un nodo superó el 90 % durante los últimos 10 minutos). Los detalles que se proporcionan al sistema de alertas también deben incluir un resumen adecuado e información del contexto; estos datos pueden ayudar a reducir la posibilidad de eventos por falsos positivos que activan una alerta.

### Informes
Los informes se usan para generar una vista general del sistema y pueden incorporar datos históricos e información actual. Los propios requisitos de los informes se dividen en dos amplias categorías: los informes operativos y los de seguridad.

Normalmente, los informes operativos incluyen los siguientes aspectos:

- Incorporación de estadísticas que le permitan comprender la utilización de recursos del sistema general o subsistemas concretos durante un período de tiempo especificado.
- Identificación de las tendencias de uso de los recursos de todo el sistema o de subsistemas concretos durante un período de tiempo determinado.
- Supervisión de las excepciones que se hayan producido en todo el sistema o en subsistemas concretos durante un período de tiempo determinado.
- Determinación de la eficacia de la aplicación en términos de los recursos implementados y comprender si el volumen de recursos (y su costo asociado) se pueden reducir sin que afecte al rendimiento de forma innecesaria.

Los informes de seguridad están relacionado con el seguimiento del uso del sistema que hace el cliente y pueden incluir:

- Auditoría de las operaciones de usuario; esto requiere el registro de las solicitudes individuales que ha realizado cada usuario junto con las fechas y horas. Los datos deben estructurarse para permitir que un administrador reconstruya rápidamente la secuencia de operaciones que realiza un usuario concreto durante un período de tiempo especificado.
- Seguimiento del uso de los recursos por usuario; para ello, se debe registrar la forma en que cada solicitud de un usuario obtiene acceso a los distintos recursos que componen el sistema y durante cuánto tiempo. Un administrador debe ser capaz de usar estos datos para generar un informe de uso por usuario durante un período de tiempo especificado, posiblemente para fines de facturación.

En muchos casos, los informes se pueden generar mediante procesos por lotes según una programación definida (la latencia normalmente no es un problema), pero también deberán estar disponibles para la generación de forma ad hoc si es necesario. Por ejemplo, si está almacenando datos en una base de datos relacional como la Base de datos SQL de Azure, puede usar una herramienta como SQL Server Reporting Services para extraer y dar formato a los datos y presentarlos como un conjunto de informes.

## Orientación y patrones relacionados
- En la guía de escalado automático se describe cómo disminuir la sobrecarga de administración al reducir la necesidad de que un operador tenga que supervisar continuamente el rendimiento de un sistema y tomar decisiones sobre cómo agregar o quitar recursos.
- El [patrón de supervisión de los extremos de estado](https://msdn.microsoft.com/library/dn589789.aspx) describe cómo se implementan las comprobaciones funcionales dentro de una aplicación a las que herramientas externas puedan tener acceso a través de los extremos expuestos en intervalos regulares.
- El patrón de [cola de prioridad](https://msdn.microsoft.com/library/dn589794.aspx) muestra cómo dar prioridad a los mensajes en cola para que las solicitudes urgentes se reciban y se pueden procesar antes que los mensajes menos urgentes.

## Más información
- El artículo[Supervisión, diagnóstico y solución de problemas de Almacenamiento de Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md) del sitio web de Microsoft.
- El artículo [Azure: conceptos básicos de la telemetría y solución de problemas](http://social.technet.microsoft.com/wiki/contents/articles/18146.windows-azure-telemetry-basics-and-troubleshooting.aspx) del sitio web de Microsoft.
- La página [Habilitación de diagnósticos en Servicios en la nube y Máquinas virtuales de Azure](cloud-services-dotnet-diagnostics.md) del sitio web de Microsoft.
- Las páginas [Caché en Redis de Azure](https://azure.microsoft.com/services/cache/), [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) y [HDInsight](https://azure.microsoft.com/services/hdinsight/) del sitio web de Microsoft.
- La página [cómo usar las colas del Bus de servicio](service-bus-dotnet-how-to-use-queues.md) del sitio web de Microsoft.
- El artículo [Business Intelligence de SQL Server en Máquinas virtuales de Azure](./virtual-machines/virtual-machines-sql-server-business-intelligence.md) del sitio web de Microsoft.
- Las páginas [Recibir notificaciones de alerta](insights-receive-alert-notifications.md) y [Seguimiento del estado del servicio](insights-service-health.md) del sitio web de Microsoft.
- La página de [Application Insights](app-insights-get-started.md) del sitio web de Microsoft.

<!---HONumber=AcomDC_0128_2016-->