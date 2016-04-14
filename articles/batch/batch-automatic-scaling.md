<properties
	pageTitle="Escalado automático de nodos de ejecución en un grupo de Lote de Azure | Microsoft Azure"
	description="Habilite el escalado automático en un grupo en la nube para ajustar de forma dinámica el número de nodos de ejecución del grupo."
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="batch"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="01/08/2016"
	ms.author="marsma"/>

# Escalación automática de los nodos de ejecución en un grupo de Lote de Azure

Con el escalado automático en Lote de Azure, puede agregar o quitar dinámicamente nodos de ejecución en un grupo de Lote durante la ejecución del trabajo para ajustar automáticamente la capacidad de procesamiento que utiliza la aplicación. Este ajuste automático puede ahorrarle tiempo y dinero.

Puede habilitar el escalado automático en un grupo de nodos de ejecución mediante la asociación de una *fórmula de escalado automático* al grupo, como con el método [PoolOperations.EnableAutoScale][net_enableautoscale] en la biblioteca de [.NET de Lote](batch-dotnet-get-started.md). A continuación, el servicio Lote usa esta fórmula para determinar el número de nodos de ejecución que se necesitan para ejecutar la carga de trabajo. El número de nodos de ejecución del grupo, que responde a las muestras de datos de métricas de servicio que se recopilan periódicamente, se ajusta a un intervalo configurable según la fórmula asociada.

Puede habilitar el escalado automático al crear un grupo o bien en un grupo existente. También puede cambiar una fórmula existente en un grupo con el "escalado automático" habilitado. Lote proporciona la capacidad de evaluar las fórmulas antes de asignarlas a grupos y de supervisar el estado de las ejecuciones de escalado automático.

## Fórmulas de escalado automático

Una fórmula de escalado automático es un valor de cadena que contiene una o varias instrucciones que se asignan al elemento [autoScaleFormula][rest_autoscaleformula] (API de REST de Lote) o a la propiedad [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] (API de .NET de Lote) de un grupo. Estas fórmulas las define el usuario. Cuando se asignan a un grupo, determinan el número de nodos de ejecución disponibles en un grupo para el siguiente intervalo de procesamiento (consulte más información sobre intervalos posteriormente). La cadena de fórmula no puede superar los 8 KB y puede incluir hasta 100 instrucciones separadas por punto y coma, y saltos de línea y comentarios.

Puede imaginarse que las fórmulas de escalado automático son un "idioma" de escalado automático de Lote. Las instrucciones de fórmula son expresiones formadas libremente que pueden incluir variables definidas por el sistema y por el usuario, así como constantes. Pueden realizar diversas operaciones en estos valores mediante funciones, operadores y tipos integrados. Por ejemplo, una instrucción podría tener la forma siguiente:

`VAR = Expression(system-defined variables, user-defined variables);`

Las fórmulas suelen tener varias instrucciones que realizan operaciones en los valores obtenidos en las instrucciones anteriores:

```
VAR₀ = Expression₀(system-defined variables);
VAR₁ = Expression₁(system-defined variables, VAR₀);
```

Mediante el uso de las instrucciones en la fórmula, el objetivo es llegar a un número de nodos de ejecución al que se debe escalar el grupo: el número de **destino** de **nodos dedicados**. Este número puede ser mayor, menor o igual que el número actual de nodos del grupo. Lote evalúa la fórmula de escalado automático del grupo según un intervalo específico (a continuación se describen los [intervalos de escalado automático](#interval)). Después, ajustará el número de destino de nodos del grupo al número que la fórmula de escalado automático especifica en el momento de la evaluación.

A modo de ejemplo rápido, esta fórmula de escalado automático de dos líneas especifica que el número de nodos se debe ajustar según el número de tareas activas, hasta un máximo de 10 nodos de ejecución:

```
$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));
$TargetDedicated = min(10, $averageActiveTaskCount);
```

Las siguientes secciones de este artículo tratan de las distintas entidades que conformarán las fórmulas de escalado automático, incluidos variables, operadores, operaciones y funciones. En Lote, encontrará información acerca de cómo obtener varias métricas de recursos y tareas de proceso. Puede usar estas métricas para ajustar de forma inteligente el número de nodos del grupo en función del estado de tareas y el uso de recursos. Después, aprenderá a construir una fórmula y a habilitar el escalado automático en un grupo mediante API de .NET y de REST de Lote. Por último, terminaremos con algunas fórmulas de ejemplo.

> [AZURE.NOTE] Cada cuenta de Lote de Azure se limita a un número máximo de nodos de ejecución que puede utilizarse para su procesamiento. El servicio Lote creará nodos solo hasta ese límite. Por lo tanto, no podría alcanzar el número de destino que se especifica mediante una fórmula. Consulte [Cuotas y límites del servicio de Lote de Azure](batch-quota-limit.md) para obtener información sobre la visualización y aumento de las cuotas de la cuenta.

## <a name="variables"></a>Variables

Puede usar tanto variables definidas por el sistema como variables definidas por el usuario en las fórmulas de escalado automático. En la fórmula de ejemplo de dos líneas anterior, `$TargetDedicated` es una variable definida por el sistema, mientras que `$averageActiveTaskCount` está definida por el usuario. Las tablas siguientes muestran las variables de lectura y escritura y de solo lectura definidas por el servicio Lote.

*Obtenga* y *establezca* los valores de estas **variables definidas por el sistema** para administrar el número de nodos de ejecución de un grupo:

<table>
  <tr>
    <th>Variables (lectura y escritura)</th>
    <th>Descripción</th>
  </tr>
  <tr>
    <td>$TargetDedicated</td>
    <td>Número <b>objetivo</b> de <b>nodos de ejecución dedicados</b> para el grupo. Es el número de nodos de ejecución al que se debe escalar el grupo. Es un número "objetivo" porque es posible que un grupo no alcance el número objetivo de nodos. Esto puede ocurrir si el número de nodos de destino se modifica de nuevo mediante una evaluación posterior de escalado automático antes de que el grupo haya alcanzado el objetivo inicial. También puede ocurrir si se alcanza una cuota de nodos o núcleos de la cuenta de Lote antes de llegar al número de nodos de destino.</td>
  </tr>
  <tr>
    <td>$NodeDeallocationOption</td>
    <td>La acción que se produce cuando se quitan los nodos de ejecución de un grupo. Los valores posibles son:
      <br/>
      <ul>
        <li><p><b>requeue</b>: finaliza las tareas de inmediato y las vuelve a poner en la cola de trabajos para que se vuelvan a programar.</p></li>
        <li><p><b>terminate</b>: finaliza las tareas de inmediato y las quitar de la cola de trabajos.</p></li>
        <li><p><b>taskcompletion</b>: espera a que finalicen las tareas actualmente en ejecución y, a continuación, quita el nodo del grupo.</p></li>
        <li><p><b>retaineddata</b>: espera a que todos los datos que se conservan en la tarea local en el nodo se limpien antes de quitar el nodo del grupo.</p></li>
      </ul></td>
   </tr>
</table>

*Obtenga* el valor de estas **variables definidas por el sistema** para realizar ajustes basados en las métricas del servicio Lote:

<table>
  <tr>
    <th>Variables (solo lectura)</th>
    <th>Descripción</th>
  </tr>
  <tr>
    <td>$CPUPercent</td>
    <td>El porcentaje medio de uso de CPU.</td>
  </tr>
  <tr>
    <td>$WallClockSeconds</td>
    <td>El número de segundos consumidos.</td>
  </tr>
  <tr>
    <td>$MemoryBytes</td>
    <td>La media de megabytes usados.</td>
  <tr>
    <td>$DiskBytes</td>
    <td>La media de gigabytes usados en los discos locales.</td>
  </tr>
  <tr>
    <td>$DiskReadBytes</td>
    <td>El número de bytes leídos.</td>
  </tr>
  <tr>
    <td>$DiskWriteBytes</td>
    <td>El número de bytes escritos.</td>
  </tr>
  <tr>
    <td>$DiskReadOps</td>
    <td>El número de operaciones de disco de lectura realizadas.</td>
  </tr>
  <tr>
    <td>$DiskWriteOps</td>
    <td>El número de operaciones de disco de escritura realizadas.</td>
  </tr>
  <tr>
    <td>$NetworkInBytes</td>
    <td>El número de bytes entrantes.</td>
  </tr>
  <tr>
    <td>$NetworkOutBytes</td>
    <td>El número de bytes salientes.</td>
  </tr>
  <tr>
    <td>$SampleNodeCount</td>
    <td>El número de nodos de ejecución.</td>
  </tr>
  <tr>
    <td>$ActiveTasks</td>
    <td>El número de tareas que se encuentran en estado activo.</td>
  </tr>
  <tr>
    <td>$RunningTasks</td>
    <td>El número de tareas en un estado de ejecución.</td>
  </tr>
  <tr>
    <td>$SucceededTasks</td>
    <td>El número de tareas que finalizó correctamente.</td>
  </tr>
  <tr>
    <td>$FailedTasks</td>
    <td>El número de tareas erróneas.</td>
  </tr>
  <tr>
    <td>$CurrentDedicated</td>
    <td>El número actual de dedicado de nodos de ejecución dedicados.</td>
  </tr>
</table>

> [AZURE.TIP] Las variables de solo lectura definidas por el sistema que se muestran anteriormente son *objetos* que proporcionan varios métodos para acceder a los datos asociados a cada uno de ellos. Consulte la sección [Obtención de datos de muestra](#getsampledata) más adelante para obtener más información.

## Tipos

Estos son los **tipos** que se admiten en las fórmulas.

- double
- doubleVec
- doubleVecList
- cadena
- timestamp: timestamp es una estructura compuesta que contiene los siguientes miembros:
	- year
	- mes (1-12)
	- día (1-31)
	- weekday (en formato de número; p.ej., 1 para lunes)
	- hora (en formato de número de 24 horas; p.ej., 13 significa 1 p.m.)
	- minuto (00-59)
	- segundo (00-59)
- timeinterval
	- TimeInterval\_Zero
	- TimeInterval\_100ns
	- TimeInterval\_Microsecond
	- TimeInterval\_Millisecond
	- TimeInterval\_Second
	- TimeInterval\_Minute
	- TimeInterval\_Hour
	- TimeInterval\_Day
	- TimeInterval\_Week
	- TimeInterval\_Year

## Operaciones

Estas **operaciones** se permiten en los tipos enumerados arriba.

<table>
  <tr>
    <th>Operación</th>
    <th>Operadores permitidos</th>
  </tr>
  <tr>
    <td>double &lt;operator> double => double</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>double &lt;operator> timeinterval => timeinterval</td>
    <td>*</td>
  </tr>
  <tr>
    <td>doubleVec &lt;operator> double => doubleVec</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>doubleVec &lt;operator> doubleVec => doubleVec</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operator> double => timeinterval</td>
    <td>*, /</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operator> timeinterval => timeinterval</td>
    <td>+, -</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operator> timestamp => timestamp</td>
    <td>+</td>
  </tr>
  <tr>
    <td>timestamp &lt;operator> timeinterval => timestamp</td>
    <td>+</td>
  </tr>
  <tr>
    <td>timestamp &lt;operator> timestamp => timeinterval</td>
    <td>-</td>
  </tr>
  <tr>
    <td>&lt;operator>double => double</td>
    <td>-, !</td>
  </tr>
  <tr>
    <td>&lt;operator>timeinterval => timeinterval</td>
    <td>-</td>
  </tr>
  <tr>
    <td>double &lt;operator> double => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>string &lt;operator> string => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>timestamp &lt;operator> timestamp => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operator> timeinterval => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>double &lt;operator> double => double</td>
    <td>&amp;&amp;, ||</td>
  </tr>
  <tr>
    <td>test double only (nonzero is true, zero is false)</td>
    <td>? :</td>
  </tr>
</table>

## Funciones

Estas **funciones** predefinidas están disponibles para que las use al definir fórmulas de escalado automático.

<table>
  <tr>
    <th>Función</th>
    <th>Descripción</th>
  </tr>
  <tr>
    <td>double <b>avg</b>(doubleVecList)</td>
    <td>Devuelve el valor medio de todos los valores de doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>len</b>(doubleVecList)</td>
    <td>Devuelve la longitud del vector creado a partir de doubleVecList.</td>
  <tr>
    <td>double <b>lg</b>(double)</td>
    <td>Devuelve la base logarítmica 2 de double.</td>
  </tr>
  <tr>
    <td>doubleVec <b>lg</b>(doubleVecList)</td>
    <td>Devuelve la base logarítmica de componentes 2 de doubleVecList. Se debe pasar explícitamente un vec(double) para un único parámetro double. En caso contrario, se supone la versión double lg(double).</td>
  </tr>
  <tr>
    <td>double <b>ln</b>(double)</td>
    <td>Devuelve el registro natural de double.</td>
  </tr>
  <tr>
    <td>doubleVec <b>ln</b>(doubleVecList)</td>
    <td>Devuelve la base logarítmica de componentes 2 de doubleVecList. Se debe pasar explícitamente un vec(double) para un único parámetro double. En caso contrario, se supone la versión double lg(double).</td>
  </tr>
  <tr>
    <td>double <b>log</b>(double)</td>
    <td>Devuelve la base logarítmica 10 de double.</td>
  </tr>
  <tr>
    <td>doubleVec <b>log</b>(doubleVecList)</td>
    <td>Devuelve la base logarítmica de componentes 10 de doubleVecList. Se debe pasar explícitamente un vec(double) para un único parámetro double. En caso contrario, se supone la versión double log(double).</td>
  </tr>
  <tr>
    <td>double <b>max</b>(doubleVecList)</td>
    <td>Devuelve el valor máximo de doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>min</b>(doubleVecList)</td>
    <td>Devuelve el valor mínimo de doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>norm</b>(doubleVecList)</td>
    <td>Devuelve la norma de dos del vector creado a partir de doubleVecList.
  </tr>
  <tr>
    <td>double <b>percentile</b>(doubleVec v, double p)</td>
    <td>Devuelve el elemento percentil del vector v.</td>
  </tr>
  <tr>
    <td>double <b>rand</b>()</td>
    <td>Devuelve un valor aleatorio entre 0,0 y 1,0.</td>
  </tr>
  <tr>
    <td>double <b>range</b>(doubleVecList)</td>
    <td>Devuelve la diferencia entre los valores máximo y mínimo en doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>std</b>(doubleVecList)</td>
    <td>Devuelve la desviación de muestra estándar de los valores en doubleVecList.</td>
  </tr>
  <tr>
    <td><b>stop</b>()</td>
    <td>Detiene la evaluación de la expresión de escalado automático.</td>
  </tr>
  <tr>
    <td>double <b>sum</b>(doubleVecList)</td>
    <td>Devuelve la suma de todos los componentes de doubleVecList.</td>
  </tr>
  <tr>
    <td>timestamp <b>time</b>(string dateTime="")</td>
    <td>Devuelve la marca de tiempo de la hora actual si no se pasan los parámetros o la marca de hora de la cadena dateTime si se pasó. Los formatos de dateTime compatibles son W3C-DTF y RFC 1123.</td>
  </tr>
  <tr>
    <td>double <b>val</b>(doubleVec v, double i)</td>
    <td>Devuelve el valor del elemento que está en la ubicación i en el vector v con el índice inicial de cero.</td>
  </tr>
</table>

Algunas de las funciones descritas en la tabla anterior pueden aceptar una lista como argumento. La lista separada por comas es cualquier combinación de *double* y *doubleVec*. Por ejemplo:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

El valor *doubleVecList* se convierte en un *doubleVec* individual antes de la evaluación. Por ejemplo, si `v = [1,2,3]`, entonces, llamar a `avg(v)` es equivalente a llamar a `avg(1,2,3)`. Llamar a `avg(v, 7)` es equivalente a llamar a `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Obtención de datos de muestra

Las fórmulas de escalado automático actúan en datos de métricas (muestras) proporcionados por el servicio Lote. Una fórmula aumenta o reduce el tamaño del grupo según los valores que obtiene del servicio. Las variables definidas por el sistema descritas anteriormente son objetos que proporcionan varios métodos para acceder a los datos que están asociados a cada objeto. Por ejemplo, la siguiente expresión muestra una solicitud para obtener los últimos cinco minutos de uso de CPU:

`$CPUPercent.GetSample(TimeInterval_Minute * 5)`

<table>
  <tr>
    <th>Método</th>
    <th>Descripción</th>
  </tr>
  <tr>
    <td>GetSample()</td>
    <td><p>El método <b>GetSample()</b> devuelve un vector de muestras de datos.
	<p>Un ejemplo tiene un valor de 30 segundos de datos de métrica. En otras palabras, las muestras se obtienen cada 30 segundos. No obstante, tal como se mencionó anteriormente, hay un retraso entre cuándo se recopila una muestra y cuándo está disponible para una fórmula. Por lo tanto, puede que no todos los ejemplos durante un período de tiempo determinado estén disponibles para la evaluación a través de una fórmula.
        <ul>
          <li><p><b>doubleVec GetSample(double count)</b>: especifica el número de muestras que se obtienen a partir de las muestras recogidas más recientes.</p>
				  <p>GetSample (1) devuelve el último ejemplo disponible. Para métricas como $CPUPercent, sin embargo, esto no debe usarse porque es imposible saber <em>cuándo</em> se recopiló la muestra. Puede ser reciente o, debido a problemas del sistema, puede ser mucho más antiguo. En estos casos es mejor usar un intervalo de tiempo como se muestra a continuación.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime [, double samplePercent])</b>: especifica un período de tiempo para recopilar datos de muestra. Opcionalmente, también especifica el porcentaje de muestras que deben estar disponibles en el marco de tiempo solicitado.</p>
          <p><em>$CPUPercent.GetSample(TimeInterval_Minute * 10)</em>, debería devolver 20 ejemplos si todos los ejemplos para los últimos 10 minutos están presentes en el historial de CPUPercent. Si el último minuto del historial no estaba disponible, solo se devolverán, no obstante, 18 muestras. En este caso:<br/>
		  &#160;&#160;&#160;&#160;<em>$CPUPercent.GetSample (TimeInterval_Minute * 10, 95)</em> produciría un error porque solo el 90 por ciento de las muestras están disponibles.<br/>
		  &#160;&#160;&#160;&#160;<em>$CPUPercent.GetSample (TimeInterval_Minute * 10, 80)</em> se realizaría correctamente.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b>: especifica un período para recopilar datos con una hora de inicio y una hora de finalización.</p></li></ul>
		  <p>Como se mencionó anteriormente, hay un retraso entre cuando se recopila un ejemplo y cuando está disponible para una fórmula. Esto debe tenerlo en cuenta cuando use el método <em>GetSample</em>. Consulte <em>GetSamplePercent</em> a continuación.</td>
  </tr>
  <tr>
    <td>GetSamplePeriod()</td>
    <td>Devuelve el período de las muestras tomadas en un conjunto de datos de muestras históricos.</td>
  </tr>
	<tr>
		<td>Count()</td>
		<td>Devuelve el número total de ejemplos en el historial de métrica.</td>
	</tr>
  <tr>
    <td>HistoryBeginTime()</td>
    <td>Devuelve la marca de tiempo de la muestra de datos disponible más antigua para la métrica.</td>
  </tr>
  <tr>
    <td>GetSamplePercent()</td>
    <td><p>Devuelve el porcentaje de muestras que están disponibles para un intervalo de tiempo dado. Por ejemplo:</p>
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b>
	<p>Debido a que se produce un error en el método GetSample si el porcentaje de muestras devueltas es inferior al valor de samplePercent especificado, puede utilizar el método GetSamplePercent para comprobarlo primero. A continuación, puede realizar una acción alternativa si no hay suficientes muestras presentes, sin detener la evaluación de escalado automático.</p></td>
  </tr>
</table>

### Ejemplos, porcentaje de muestras y el método *GetSample()*

La operación principal de una fórmula de escalado automático es la obtención de datos de métricas de tareas y recursos y el ajuste posterior del tamaño de grupo según esos datos. Por lo tanto, es importante tener una idea clara de cómo las fórmulas de escalado automático interactúan con datos de métricas, también denominados "muestras".

**Muestras**

El servicio Lote toma periódicamente *muestras* de métricas de tareas y recursos y las pone a disposición de las fórmulas de escalado automático. El servicio Lote graba estas muestras cada 30 segundos. No obstante, hay algo de latencia que provoca un retraso entre el momento en el que se grabaron esas muestras y el momento en el que se ponen a disposición de las fórmulas de escalado automático y estas pueden leer aquellas. Además, debido a diversos factores como problemas con la red o de infraestructura, es posible que las muestras no se hayan grabado para un intervalo determinado. Esto provoca muestras "desaparecidas".

**Porcentaje de muestras**

Al pasar `samplePercent` al método `GetSample()` o llamar al método `GetSamplePercent()`, "porcentaje" se refiere a una comparación entre el número *posible* total de muestras que graba el servicio Lote y el número de muestras que realmente están *disponibles* para la fórmula de escalado automático.

Echemos un vistazo a un intervalo de tiempo de 10 minutos como ejemplo. Dado que las muestras se graban cada 30 segundos, el número total máximo de muestras que Lote graba en un intervalo de tiempo de 10 minutos habría sido de 20 muestras (2 por minuto). Sin embargo, debido a la latencia inherente del mecanismo de informes o a algún otro problema dentro de la infraestructura de Azure, puede haber solo 15 muestras disponibles para leer para la fórmula de escalado automático. Esto significa que, durante ese período de 10 minutos, solo el **75 por ciento** del número total de muestras que se graban está realmente disponible para la fórmula.

**GetSample() e intervalos de muestra**

Las fórmulas de escalado automático van a aumentar y reducir los grupos, al agregar o quitar nodos. Dado que los nodos cuestan dinero, desea asegurarse de que las fórmulas utilizan un método de análisis inteligente que se basa en un número de datos suficiente. Por lo tanto, recomendamos que use un análisis de tipo tendencias en las fórmulas. Este tipo aumentará y reducirá los grupos basándose en un *intervalo* de muestras recopiladas.

Para ello, utilice `GetSample(interval look-back start, interval look-back end)` para devolver un **vector** de muestras:

`runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);`

Cuando Lote evalúe la línea anterior, devolverá un intervalo de muestras como un vector de valores. Por ejemplo:

`runningTasksSample=[1,1,1,1,1,1,1,1,1,1];`

Una vez recopilado el vector de muestras, puede usar funciones como `min()`, `max()` y `avg()` para derivar valores significativos del intervalo recopilado.

Para mayor seguridad, puede forzar el *error* en una evaluación de fórmula si menos de un determinado porcentaje de muestras está disponible para un período de tiempo determinado. Al forzar el error en una evaluación de fórmula, indica a Lote que deje de seguir evaluando la fórmula si el porcentaje de muestras especificado no está disponible, y no se realizará ningún cambio en el tamaño del grupo. Para especificar un porcentaje necesario de muestras para que la evaluación se realice correctamente, especifíquelo como el tercer parámetro a `GetSample()`. En este caso, se especifica un requisito del 75 por ciento de muestras:

`runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);`

También es importante, debido al retraso mencionado anteriormente en la disponibilidad de las muestras, especificar siempre un intervalo de tiempo con una hora de inicio retrospectiva cuya anterioridad sea superior a un minuto. Esto es porque las muestras tardan aproximadamente un minuto en propagarse por el sistema, por lo que las muestras del intervalo `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` no suelen estar disponibles. De nuevo, puede utilizar el parámetro de porcentaje de `GetSample()` para forzar un requisito de porcentaje de muestra concreto.

> [AZURE.IMPORTANT] Es **muy recomendable** que **evite confiar *solo* en `GetSample(1)` en las fórmulas de escalado automático**. Esto es porque `GetSample(1)` básicamente dice al servicio Lote "Dame la muestra más reciente que tengas, independientemente de cuánto tiempo hace que la tienes." Puesto que es solo una muestra única, y puede ser una muestra más antigua, no puede ser representativa de la imagen más grande del estado reciente de la tarea o el recurso. Si usa `GetSample(1)`, asegúrese de que forma parte de una instrucción más grande y no solo el punto de datos en el que se basa la fórmula.

## Métricas

Puede usar tanto métricas de **recurso** como de **tarea** al definir una fórmula. El número de nodos dedicados de destino se ajusta en el grupo basándose en los datos de métricas que obtenga y evalúe. Consulte la sección [Variables](#variables) anterior para obtener más información sobre cada métrica.

<table>
  <tr>
    <th>Métrica</th>
    <th>Descripción</th>
  </tr>
  <tr>
    <td><b>Recurso</b></td>
    <td><p>Las <b>métricas de recurso</b> se basan en el uso de CPU, ancho de banda y memoria de nodos de ejecución, así como en el número de nodos.</p>
		<p> Estas variables definidas por el sistema se usan para realizar ajustes basados en el número de nodos:</p>
    <p><ul>
      <li>$TargetDedicated</li>
			<li>$CurrentDedicated</li>
			<li>$SampleNodeCount</li>
    </ul></p>
    <p>Estas variables definidas por el sistema se usan para realizar ajustes basados en el uso de recursos de nodo:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Task</b></td>
    <td><p>Las <b>métricas de tarea</b>: están basadas en el estado de las tareas, como Activo, Pendiente y Completado. Las siguientes variables definidas por el sistema se usan para realizar ajustes de tamaño de grupo basados en las métricas de tarea:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
			<li>$FailedTasks</li></ul></p>
		</td>
  </tr>
</table>

## Compilación de una fórmula de escalado automático

Para construir una fórmula de escalado automático, es preciso formar instrucciones mediante los componentes anteriores y combinar dichas instrucciones en una fórmula completa. Por ejemplo, aquí construimos una fórmula, para lo que primero definimos los requisitos para una fórmula que:

1. Aumentará el número de nodos de ejecución de destino en un grupo si el uso de la CPU es alto.
2. Reducirá el número de nodos de ejecución de destino en un grupo cuando el uso de la CPU es bajo.
3. Limitará siempre el número máximo de nodos a 400.

Para que *aumente* el número de nodos durante un uso elevado de la CPU, definimos la instrucción que rellena una variable definida por el usuario ($TotalNodes) con un valor que es un 110 por ciento del número de nodos del destino actual si el uso medio mínimo de la CPU en los 10 últimos minutos es superior al 70 por ciento:

`$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;`

La siguiente instrucción establece la misma variable al 90 por ciento del número de nodos de destino actual si el uso medio de la CPU en los 60 últimos minutos estaba *por debajo* del 20 por ciento. Esto reduce el número de destino durante un uso de la CPU bajo. Tenga en cuenta que esta instrucción también hace referencia a la variable definida por el usuario *$TotalNodes* de la instrucción anterior.

`$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;`

Ahora limite el número de destino de los nodos de ejecución dedicados a un **máximo** de 400:

`$TargetDedicated = min(400, $TotalNodes)`

Esta es la fórmula completa:

```
$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;
$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;
$TargetDedicated = min(400, $TotalNodes)
```

> [AZURE.NOTE] Las fórmulas de escalado automática constan de variables, tipos, operaciones y funciones de la API de [REST de Azure Batch][rest_api]. Úselos en las cadenas de fórmula aunque trabaje con la biblioteca de [.NET de Lote][net_api].

## Creación de un grupo con el escalado automático habilitado

Para habilitar el escalado automático al crear un grupo, use una de las técnicas siguientes:

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx): este cmdlet de Azure PowerShell usa el parámetro AutoScaleFormula para especificar la fórmula de escalado automático.
- [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx): después de llamar a este método .NET para crear un grupo, establecerá las propiedades [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) y [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) del grupo para habilitar el escalado automático.
- [Agregar un grupo a una cuenta](https://msdn.microsoft.com/library/azure/dn820174.aspx): los elementos enableAutoScale y autoScaleFormula se usan en esta solicitud de API de REST para configurar el escalado automático del grupo al crearlo.

> [AZURE.IMPORTANT] Si crea un grupo con el escalado automático habilitado mediante una de las técnicas anteriores, el parámetro *targetDedicated* para el grupo **no** debe especificarse. Tenga en cuenta también que si desea cambiar manualmente el tamaño de un grupo con el escalado automático habilitado (por ejemplo, con [BatchClient.PoolOperations.ResizePool][net_poolops_resizepool]), primero tiene que **deshabilitar** el escalado automático en el grupo y después cambiar su tamaño.

El fragmento de código siguiente muestra la creación de un grupo habilitado para escalado automático ([CloudPool][net_cloudpool]) mediante el uso de la biblioteca de [.NET de Lote][net_api]. La fórmula de escalado automático del grupo establece el número de nodos de destino en cinco los lunes y en uno todos los demás días de la semana. Además, el intervalo de escalado automático se establece en 30 minutos (consulte la sección [Intervalo de escalado automático](#interval) más adelante). En este y en otros fragmentos de código en C# de este artículo, "myBatchClient" es una instancia totalmente inicializada de [BatchClient][net_batchclient].

```
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

### <a name="interval"></a>Intervalo de escalado automático

De forma predeterminada, el servicio Lote ajusta el tamaño de un grupo según su fórmula de escalado automático cada **15 minutos**. No obstante, este intervalo es configurable mediante las siguientes propiedades de grupo:

- API de REST: [autoScaleEvaluationInterval][rest_autoscaleinterval]
- API de .NET: [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval]

Los intervalos mínimo y máximo son cinco minutos y 168 horas, respectivamente. Si se especifica un intervalo fuera de este margen, el servicio Lote devolverá un error de solicitud incorrecta (400).

> [AZURE.NOTE] Actualmente, el escalado automático no está pensado como respuesta inmediata a los cambios, sino para ajustar el tamaño del grupo gradualmente mientras ejecuta una carga de trabajo.

## Habilitación del escalado automático después de crear un grupo

Si ya configuró un grupo con un número específico de nodos de ejecución mediante el parámetro *targetDedicated*, puede actualizar el grupo existente posteriormente para que el escalado se realice de forma automática. Puede realizar esto de una de estas formas:

- [BatchClient.PoolOperations.EnableAutoScale][net_enableautoscale]\: este método .NET requiere el identificador de un grupo existente y la fórmula de escalado automático que se va a aplicar al grupo.
- [Habilitación del escalado automático en un grupo][rest_enableautoscale]\: esta API de REST requiere el identificador del grupo existente en el URI y la fórmula de escalado automático en el cuerpo de la solicitud.

> [AZURE.NOTE] Si se especificó un valor para el parámetro *targetDedicated* cuando se creó el grupo, se ignora cuando se evalúa la fórmula de escalado automático.

Este fragmento de código muestra cómo habilitar el escalado automático mediante la biblioteca de [.NET de Lote][net_api]. Tenga en cuenta que tanto la habilitación como la actualización de la fórmula en un grupo existente usan el mismo método. Por lo tanto, esta técnica *actualizaría* la fórmula en el grupo especificado si el escalado automático ya se había habilitado. El fragmento de código asume que "mypool" es el identificador de un grupo existente ([CloudPool][net_cloudpool]).

		 // Define the autoscaling formula. In this snippet, the  formula sets the target number of nodes to 5 on
		 // Mondays, and 1 on every other day of the week
		 string myAutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";

		 // Update the existing pool's autoscaling formula by calling the BatchClient.PoolOperations.EnableAutoScale
		 // method, passing in both the pool's ID and the new formula.
		 myBatchClient.PoolOperations.EnableAutoScale("mypool", myAutoScaleFormula);

## Evaluación de la fórmula de escalación automática

Siempre es una buena práctica evaluar una fórmula antes de usarla en su aplicación. La fórmula se evalúa realizando una "ejecución de prueba" de la misma en un grupo existente. Para ello, use:

- [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) o [BatchClient.PoolOperations.EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx): estos métodos .NET requieren el identificador de un grupo existente y la cadena que contiene la fórmula del escalado automático. Los resultados de la llamada aparecen en una instancia de la clase [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx).
- [Evaluación de una fórmula de escalado automático](https://msdn.microsoft.com/library/azure/dn820183.aspx): en esta solicitud de API de REST, el identificador del grupo se especifica en el URI. La fórmula de escalado automático se especifica en el elemento *autoScaleFormula* del cuerpo de la solicitud. La respuesta de la operación contiene cualquier información de error que pueda relacionarse con la fórmula.

> [AZURE.NOTE] Para evaluar una fórmula de escalado automático, primero es preciso habilitar el escalado automático en el grupo con una fórmula válida.

En este fragmento de código que usa la biblioteca de [.NET de Lote][net_api], se evalúa una fórmula antes de aplicarla al grupo ([CloudPool][net_cloudpool]).

```
// First obtain a reference to the existing pool
CloudPool pool = myBatchClient.PoolOperations.GetPool("mypool");

// We must ensure that autoscaling is enabled on the pool prior to evaluating a formula
if (pool.AutoScaleEnabled.HasValue && pool.AutoScaleEnabled.Value)
{
	// The formula to evaluate - adjusts target number of nodes based on day of week and time of day
	string myFormula = @"
		$CurTime=time();
		$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
		$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
		$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
		$TargetDedicated=$IsWorkingWeekdayHour?20:10;
	";

	// Perform the autoscale formula evaluation. Note that this does not actually apply the formula to
	// the pool.
	AutoScaleEvaluation eval = client.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

	if (eval.AutoScaleRun.Error == null)
	{
		// Evaluation success - print the results of the AutoScaleRun. This will display the values of each
		// variable as evaluated by the autoscale formula.
		Console.WriteLine("AutoScaleRun.Results: " + eval.AutoScaleRun.Results);

		// Apply the formula to the pool since it evaluated successfully
		client.PoolOperations.EnableAutoScale(pool.Id, myFormula);
	}
	else
	{
		// Evaluation failed, output the message associated with the error
		Console.WriteLine("AutoScaleRun.Error.Message: " + eval.AutoScaleRun.Error.Message);
	}
}
```

La evaluación correcta de la fórmula en este fragmento generará una salida similar a la siguiente:

`AutoScaleRun.Results: $TargetDedicated = 10;$NodeDeallocationOption = requeue;$CurTime = 2015 - 08 - 25T20: 08:42.271Z;$IsWeekday = 1;$IsWorkingWeekdayHour = 0;$WorkHours = 0`

## Obtención de información sobre las ejecuciones de escalado automático

Compruebe periódicamente los resultados de las ejecuciones de escalado automático para garantizar que una fórmula funciona según lo esperado.

- [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx): al utilizar la biblioteca de. NET, esta propiedad de un grupo proporciona una instancia de la clase [AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx). Esta clase proporciona las siguientes propiedades de la ejecución de escalado automático más reciente:
  - [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)
  - [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
  - [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
- [Obtención de información sobre un grupo](https://msdn.microsoft.com/library/dn820165.aspx): esta solicitud de API de REST devuelve información acerca del grupo, lo que incluye la última ejecución del escalado automático.

## <a name="examples"></a>Formulas de ejemplo

Echemos un vistazo a algunos ejemplos que muestran varias de las formas en que pueden usarse las fórmulas para escalar automáticamente los recursos de proceso de un grupo.

### Ejemplo 1: Ajuste basado en la fecha y hora

Quizás desee ajustar el tamaño de un grupo según el día de la semana y la hora del día, y aumentar o reducir el número de nodos del grupo en consecuencia:

```
$CurTime=time();
$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
$TargetDedicated=$IsWorkingWeekdayHour?20:10;
```

En primer lugar, esta fórmula obtiene la hora actual. Si se trata de un día laborable (1-5) y dentro del horario laboral (de 8 a.m. a 6 p.m.), el tamaño del grupo de destino se establece en 20 nodos. De lo contrario, el tamaño del grupo se establece en 10 nodos.

### Ejemplo 2: Ajuste basado en tareas

En este ejemplo, el tamaño del grupo se ajusta en función del número de tareas en la cola. Tenga en cuenta que las cadenas de la fórmulas aceptan tanto comentarios como saltos de línea.

```
// Get pending tasks for the past 15 minutes.
$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point, otherwise we use the maximum of
// last sample point and the history average.
$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise half of current dedicated.
$TargetVMs = $Tasks > 0? $Tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it to 20. This value
// should be adjusted according to your use case.
$TargetDedicated = max(0,min($TargetVMs,20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### Ejemplo 3: Contabilidad para tareas paralelas

Este es otro ejemplo que ajusta el tamaño del grupo en función del número de tareas. Esta fórmula también tiene en cuenta el valor de [MaxTasksPerComputeNode][net_maxtasks] que se ha establecido para el grupo. Esto es especialmente útil en aquellas situaciones en las que la [ejecución de tareas paralelas](batch-parallel-node-tasks.md) se ha habilitado en el grupo.

```
// Determine whether 70 percent of the samples have been recorded in the past 15 minutes; if not, use last sample
$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the MaxTasksPerComputeNode
// property on this pool is set to 4, adjust this number for your use case)
$Cores = $TargetDedicated * 4;
$ExtraVMs = (($Tasks - $Cores) + 3) / 4;
$TargetVMs = ($TargetDedicated+$ExtraVMs);
// Attempt to grow the number of compute nodes to match the number of active tasks, with a maximum of 3
$TargetDedicated = max(0,min($TargetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### Ejemplo 4: Configuración de un tamaño de grupo inicial

En este ejemplo se muestra un fragmento de código de C# con una fórmula de escalado automático que establece el tamaño del grupo en cierto número de nodos durante un período de tiempo inicial. A continuación, se ajusta el tamaño del grupo según el número de tareas activas y en ejecución una vez transcurrido el período de tiempo inicial.

```
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"

	$TargetDedicated = {1};
	lifespan         = time() - time(""{0}"");
	span             = TimeInterval_Minute * 60;
	startup          = TimeInterval_Minute * 10;
	ratio            = 50;

	$TargetDedicated = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicated) : {1});
	", now, 4);
```

La fórmula en el fragmento de código anterior:

- Establece el tamaño inicial del grupo en cuatro nodos.
- No ajusta el tamaño del grupo en los 10 primeros minutos del ciclo de vida del mismo.
- Después de 10 minutos, obtiene el valor máximo del número de tareas activas y en ejecución en los últimos 60 minutos.
  - Si ambos valores son 0 (lo que indica que no hay tareas activas o en ejecución en los últimos 60 minutos) el tamaño del grupo se establece en 0.
  - Si cualquier valor es mayor que cero, no hay cambios.

## Pasos siguientes

1. Para evaluar exhaustivamente la eficiencia de una aplicación, es posible que tenga que acceder a un nodo de ejecución. Para aprovechar el acceso remoto, debe agregarse una cuenta de usuario al nodo al que desea obtener acceso y debe recuperarse un archivo de Protocolo de acceso remoto (RDP) de dicho nodo.
    - Agregue la cuenta de usuario de una de estas maneras:
        * [New-AzureBatchVMUser](https://msdn.microsoft.com/library/mt149846.aspx): este cmdlet de PowerShell toma el nombre del grupo, el nombre del nodo de ejecución, el nombre de la cuenta y la contraseña como parámetros.
        * [BatchClient.PoolOperations.CreateComputeNodeUser](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createcomputenodeuser.aspx): este método .NET crea una instancia de la clase [ComputeNodeUser](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.aspx) en la que se pueden establecer el nombre de la cuenta y la contraseña del nodo de ejecución. A continuación, se llama a [ComputeNodeUser.Commit](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.commit.aspx) en la instancia para crear el usuario en dicho nodo.
        * [Incorporación de una cuenta de usuario a un nodo](https://msdn.microsoft.com/library/dn820137.aspx): el nombre del grupo y el nodo de ejecución se especifican en el URI. El nombre de cuenta y la contraseña se envían al nodo en el cuerpo de esta solicitud de API de REST.
    - Obtención del archivo RDP:
        * [BatchClient.PoolOperations.GetRDPFile](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getrdpfile.aspx): este método .NET requiere el identificador del grupo, el identificador del nodo y el nombre del archivo RDP que se va a crear.
        * [Obtención de un archivo de protocolo de escritorio remoto de un nodo](https://msdn.microsoft.com/library/dn820120.aspx): esta solicitud de API de REST requiere el nombre del grupo y el nombre del nodo de ejecución. La respuesta incorpora el contenido del archivo RDP.
        * [Get-AzureBatchRDPFile](https://msdn.microsoft.com/library/mt149851.aspx): este cmdlet de PowerShell obtiene el archivo RDP del nodo de ejecución especificado y lo guarda en la ubicación del archivo especificada o en una transmisión.
2.	Algunas aplicaciones generan grandes cantidades de datos que pueden ser difíciles de procesar. Una manera de resolver esto es a través de una [consulta de lista eficiente](batch-efficient-list-queries.md).

[net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_batchclient]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_autoscaleformula]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx
[net_cloudpool_autoscaleevalinterval]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval.aspx
[net_enableautoscale]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx
[net_maxtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[net_poolops_resizepool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx

[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_autoscaleformula]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_autoscaleinterval]: https://msdn.microsoft.com/es-ES/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx

<!---HONumber=AcomDC_0218_2016-->