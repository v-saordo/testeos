<properties
 pageTitle="Poner en correlación eventos con el tiempo con Storm y HBase en HDInsight"
 description="Aprenda a poner en correlación los eventos que llegan en distintos momentos con Storm y HBase en HDInsight."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
 tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="02/01/2016"
 ms.author="larryfr"/>

# Poner en correlación eventos con el tiempo con Storm y HBase en HDInsight

Si utiliza un almacén de datos persistente con Apache Storm, puede poner en correlación entradas de datos que llegan en distintos momentos. Por ejemplo, puede vincular eventos de inicio y cierre de sesión de una sesión de usuario para calcular cuánto tiempo duró la sesión.

En este documento, obtendrá información sobre cómo crear una topología básica de Storm de C# que realice un seguimiento de los eventos de inicio y cierre de sesión de las sesiones de usuario y calcule la duración de la sesión. La topología utiliza HBase como un almacén de datos persistente. HBase también permite realizar consultas por lotes en los datos históricos para generar información adicional, como la cantidad de sesiones de usuario que se han iniciado o terminado durante un período de tiempo específico.

[AZURE.INCLUDE [windows-only](../../includes/hdinsight-windows-only.md)]

## Requisitos previos

-	Herramientas de HDInsight para Visual Studio: vea [Introducción al uso de herramientas de HDInsight para Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md) para obtener información acerca de la instalación.

-	Clúster de Apache Storm en HDInsight

-	Clúster de Apache HBase en HDInsight

## Arquitectura

![Diagrama del flujo de datos a través de la topología](./media/hdinsight-storm-correlation-topology/correlationtopology.png)

La correlación de eventos requiere un identificador común para el origen del evento. Por ejemplo, un id. de usuario, un id. de sesión u otra parte de los datos que a) sea única y (b) se incluya en todos los datos enviados a Storm. En este ejemplo se utiliza un valor GUID para representar un identificador de sesión.

Este ejemplo consta de dos clústeres de HDInsight:

-	HBase: almacén de datos persistente para los datos históricos

-	Storm: se utiliza para introducir datos de entrada

Los datos se generan aleatoriamente mediante la topología de Storm y constan de los siguientes elementos:

-	Id. de sesión: un GUID que identifica de forma única cada sesión

-	Evento: evento de INICIO o FINALIZACIÓN. En este ejemplo, INICIO siempre tiene lugar antes de la FINALIZACIÓN

-	Hora: hora del evento.

Estos datos se procesan y almacenan en HBase.

### Topología de Storm

Cuando se inicia una sesión, la topología recibe un evento de **INICIO** y este se registra en HBase. Cuando se recibe el evento de **FINALIZACIÓN**, la topología recupera el evento de **INICIO** y calcula el tiempo entre los dos eventos. Luego, este valor de **Duración** se almacena en HBase junto con la información del evento de **FINALIZACIÓN**.

> [AZURE.IMPORTANT] Aunque esta topología muestra el patrón básico, una solución de producción debería tener diseño para los escenarios siguientes:
>
> - Eventos que llegan sin orden
> - Eventos duplicados
> - Eventos quitados

La topología de ejemplo consta de los siguientes componentes:

- 	Session.cs: simula una sesión de usuario al crear un identificador de sesión aleatorio, una hora de inicio y una duración de la sesión.

- 	Spout.cs: crea 100 sesiones, emite un evento de INICIO, espera el tiempo de espera aleatorio para cada sesión y, a continuación, emite un evento de FINALIZACIÓN. A continuación, recicla las sesiones finalizadas para generar otras nuevas.

-	HBaseLookupBolt.cs: usa el identificador de sesión para buscar información de sesión de HBase. Cuando se procesa un evento de FINALIZACIÓN, busca el evento de INICIO correspondiente y calcula la duración de la sesión.

-	HBaseBolt.cs: almacena información en HBase.

-	TypeHelper.cs: ayuda con la conversión de tipos al leer desde HBase o escribir en él.

### Esquema de HBase

En HBase, los datos se almacenan en una tabla con la configuración y el esquema siguiente:

-	Clave de fila: el identificador de sesión se utiliza como clave para las filas de esta tabla

-	Familia de columnas: el nombre de familia es 'cf'. Las columnas almacenadas en esta familia son:

	- 	evento: INICIO o FINALIZACIÓN

	- 	hora: hora en milisegundos en la que se produjo el evento

	-	duración: la duración entre los eventos de INICIO y FINALIZACIÓN

-	VERSIONES: la familia 'cf' se establece para conservar 5 versiones de cada fila

	> [AZURE.NOTE] Las versiones son un registro de los valores anteriores almacenados para una clave de fila específica. De forma predeterminada, HBase solo devuelve el valor de la versión más reciente de una fila. En este caso, se utiliza la misma fila para todos los eventos (INICIO y FINALIZACIÓN). Cada versión de una fila se identifica mediante el valor de marca de tiempo. Esto proporciona una vista histórica de eventos registrados para un identificador concreto.

## Descarga del proyecto

Se puede descargar un proyecto de ejemplo en [https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation](https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation).

Esta descarga contiene los proyectos de C# siguientes:

-	CorrelationTopology: topología de Storm de C# que emite aleatoriamente eventos de inicio y finalización para las sesiones de usuario. Cada sesión dura entre 1 y 5 minutos.

-	SessionInfo: aplicación de consola de C# que crea la tabla de HBase y proporciona consultas de ejemplo para devolver información acerca de los datos de sesión almacenados.

## Cree la tabla

1. Abra el proyecto **SessionInfo** en Visual Studio.

2. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **SessionInfo** y seleccione **Propiedades**.

	![Captura de pantalla del menú con propiedades seleccionadas](./media/hdinsight-storm-correlation-topology/selectproperties.png)

3. Seleccione **Configuración** y, a continuación, establezca los valores siguientes:

	- 	HBaseClusterURL: la dirección URL del clúster de HBase. Por ejemplo, https://myhbasecluster.azurehdinsight.net

	- 	HBaseClusterUserName: cuenta de usuario de administrador/HTTP para el clúster

	- 	HBaseClusterPassword: contraseña de la cuenta de usuario de administrador/HTTP

	- 	HBaseTableName: nombre de la tabla que se debe utilizar con este ejemplo

	-	HBaseTableColumnFamily: nombre de la familia de columnas

	![Imagen del cuadro de diálogo de configuración](./media/hdinsight-storm-correlation-topology/settings.png)

5. Ejecute la solución. Cuando se le pida, seleccione la tecla 'c' para crear la tabla en el clúster de HBase.

## Compilar e implementar la topología de Storm

1.	Abra la solución **CorrelationTopology** en Visual Studio.

2.	En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **CorrelationTopology** y seleccione Propiedades.

3.	En la ventana Propiedades, seleccione **Configuración** y proporcione la información siguiente. Los cinco primeros valores debe ser los mismos que usa el proyecto **SessionInfo**:

	- 	HBaseClusterURL: la dirección URL del clúster de HBase. Por ejemplo, https://myhbasecluster.azurehdinsight.net

	- 	HBaseClusterUserName: cuenta de usuario de administrador/HTTP para el clúster

	- 	HBaseClusterPassword: contraseña de la cuenta de usuario de administrador/HTTP

	- 	HBaseTableName: nombre de la tabla que se debe utilizar con este ejemplo. Debe contener el mismo nombre de tabla que se utiliza en el proyecto SessionInfo

	- 	HBaseTableColumnFamily: nombre de la familia de columnas. Debe contener el mismo nombre de familia de columnas que se utiliza en el proyecto SessionInfo

	> [AZURE.IMPORTANT] No cambie HBaseTableColumnNames, ya que los valores predeterminados son los nombres que utiliza **SessionInfo** para recuperar los datos.

4.  Guarde las propiedades y, a continuación, compile el proyecto.

5.	En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Enviar a Storm en HDInsight**. Si se le solicita, introduzca las credenciales de su suscripción de Azure.

	![Imagen del envío al elemento de menú de Storm](./media/hdinsight-storm-correlation-topology/submittostorm.png)

6.	En el cuadro de diálogo **Enviar topología**, seleccione el clúster de Storm que ejecutará esta topología.

	> [AZURE.NOTE] La primera vez que envíe una topología, es posible que se tarde unos segundos en recuperar el nombre de los clústeres de HDInsight.

7.	Una vez que se ha cargado y enviado la topología al clúster, se abrirá la **Vista de topología de Storm** y se mostrará la topología en ejecución. Seleccione **CorrelationTopology** y utilice el botón Actualizar de la parte superior derecha de la página para actualizar la información de topología.

	![Imagen de la vista de topología](./media/hdinsight-storm-correlation-topology/topologyview.png)

	Cuando la topología comience a generar datos, el valor de la columna **Emitida** incrementará.

	> [AZURE.NOTE] Si la **Vista de topología de Storm** no se abre automáticamente, siga estos pasos para abrirla:
	>
	> 1. Desde el **Explorador de soluciones**, expanda **Azure** y, a continuación, expanda **HDInsight**.
	>
	> 2. Haga clic con el botón derecho en el clúster de Storm que está ejecutando la topología y, a continuación, seleccione **Ver topologías de Storm**

## Consultar los datos

Una vez que se han emitido los datos, siga los pasos siguientes para consultar los datos.

1. Vuelva al proyecto **SessionInfo**. Si no está ejecutando, inicie una nueva instancia de este.

2. Cuando se le pida, seleccione **s** para buscar el evento de INICIO. Se le pedirá que escriba una hora de inicio y finalización para definir un intervalo de tiempo. Solo se devolverán los eventos que se encuentren entre estas dos horas.

	Utilice el siguiente formato al introducir las horas de inicio y finalización: HH: MM y 'am' o 'pm'. Por ejemplo, 11:20 pm.

	Dado que la topología acaba de iniciarse, utilice una hora de inicio anterior a la implementación y una hora de finalización actual. Esto debería capturar la mayoría de eventos de INICIO que se generaron cuando se inició. Cuando se ejecute la consulta, verá una lista de entradas similares a lo siguiente:

		Session e6992b3e-79be-4991-afcf-5cb47dd1c81c started at 6/5/2015 6:10:15 PM. Timestamp = 1433527820737

La búsqueda de eventos de FINALIZACIÓN funciona igual que para los eventos de INICIO. Sin embargo, los eventos de FINALIZACIÓN se generan aleatoriamente entre 1 y 5 minutos después del evento de INICIO. Por lo tanto, es posible que tenga que probar algunos intervalos de tiempo para encontrar los eventos de FINALIZACIÓN. Los eventos de FINALIZACIÓN también contendrán la duración de la sesión; la diferencia entre la hora de INICIO del evento y la hora de FINALIZACIÓN del evento. Este es un ejemplo de datos de eventos de FINALIZACIÓN:

	Session fc9fa8e6-6892-4073-93b3-a587040d892e lasted 2 minutes, and ended at 6/5/2015 6:12:15 PM

> [AZURE.NOTE] Aunque especifique los valores de tiempo en la hora local, la consulta devolverá las horas en UTC.

##Detención de la topología

Cuando esté listo para detener la topología, vuelva al proyecto **CorrelationTopology** proyecto en Visual Studio. En la **Vista de topología de Storm**, seleccione la topología y, a continuación, utilice el botón **Cerrar** situado en la parte superior de la vista de topología.

##Pasos siguientes

Para obtener más ejemplos de Storm, vea [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md).
 

<!---HONumber=AcomDC_0204_2016-->