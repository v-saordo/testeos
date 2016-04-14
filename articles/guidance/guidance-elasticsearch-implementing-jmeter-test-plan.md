<properties
   pageTitle="Implementación de un plan de pruebas con JMeter para Elasticsearch | Microsoft Azure"
   description="Cómo ejecutar pruebas de rendimiento para Elasticsearch con JMeter."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Implementación de un plan de pruebas con JMeter para Elasticsearch

Este artículo [forma parte de una serie](guidance-elasticsearch.md).

Las pruebas de rendimiento realizadas en Elasticsearch se implementan mediante planes de pruebas de JMeter junto con el código de Java incorporado como una prueba de JUnit para realizar tareas como la carga de datos en el clúster. Los planes de pruebas y el código de JUnit se describen en [Ajuste del rendimiento de introducción de datos para Elasticsearch en Azure][] y [Ajuste de la agregación de datos y el rendimiento de las consultas para Elasticsearch en Azure][].

El propósito de este documento es resumir la experiencia clave obtenida al construir y ejecutar estos planes de pruebas. La página de [procedimientos recomendados de JMeter](http://jmeter.apache.org/usermanual/best-practices.html) del sitio web de Apache JMeter contiene asesoramiento más generalizado sobre el uso eficaz de JMeter.

## Implementación de un plan de prueba de JMeter

En la lista siguiente se resumen los elementos que se deben tener en cuenta al crear un plan de pruebas de JMeter:

- Cree un grupo de subprocesos independiente para cada prueba que desea realizar. Una prueba puede constar de varios pasos, que abarcan controladores lógicos, temporizadores, preprocesadores y posprocesadores, muestras y agentes de escucha.

- Evite crear demasiados subprocesos en un grupo de subprocesos. Un número excesivo de subprocesos provocará un error de JMeter con las excepciones "Memoria insuficiente". Es mejor agregar más servidores subordinados de JMeter cada vez que se ejecuta un número inferior de subprocesos que intentar ejecutar un gran número de subprocesos en un único servidor de JMeter.

![](./media/guidance-elasticsearch/jmeter-testing1.png)

- Para evaluar el rendimiento del clúster, incorpore el complemento [PerfMon Metrics Collector](http://jmeter-plugins.org/wiki/PerfMon/) al plan de pruebas; se trata de un agente de escucha de JMeter que está disponible como uno de los complementos estándares de JMeter. Guarde los datos de rendimiento sin procesar en un conjunto de archivos en formato CSV y procéselos una vez finalizada la prueba. Esto es más eficaz e impone menos presión en JMeter que intentar procesar los datos mientras se capturan. 

Puede utilizar una herramienta tipo Excel para importar los datos y generar una serie de gráficos para fines analíticos.

![](./media/guidance-elasticsearch/jmeter-testing2.png)

Considere la posibilidad de capturar la información siguiente:

- Uso de CPU para cada nodo del clúster de Elasticsearch.

- El número de bytes leídos por segundo del disco para cada nodo.

- Si es posible, el porcentaje de tiempo de espera de la CPU para realizar E/S en cada nodo. Esto no siempre es posible para las máquinas virtuales de Windows, pero en Linux puede crear una métrica personalizada (una métrica EXEC) que ejecute el siguiente comando shell para invocar *vmstat* en un nodo:

```Shell
sh:-c:vmstat 1 5 | awk 'BEGIN { line=0;total=0;}{line=line+1;if(line&gt;1){total=total+\$16;}}END{print total/4}'
```

El campo 16 de la salida de *vmstat* contiene el tiempo de espera de la CPU para realizar E/S. Para obtener más información sobre cómo funciona esta instrucción, vea el [comando vmstat](http://linuxcommand.org/man_pages/vmstat8.html).

- El número de bytes enviados y recibidos a través de la red en cada nodo.

- Utilice agentes de escucha de informe agregado independientes para registrar el rendimiento y la frecuencia de las operaciones correctas e incorrectas. Capture datos correctos e incorrectos en archivos diferentes.

![](./media/guidance-elasticsearch/jmeter-testing3.png)

- Mantenga cada caso de prueba de JMeter de la manera más sencilla posible para poder correlacionar directamente el rendimiento con acciones de prueba específicas. Para casos de prueba que requieren una lógica compleja, considere la posibilidad de encapsular esta lógica en una prueba de JUnit y use la muestra de solicitud de JUnit en JMeter para ejecutar la prueba.

- Utilice la muestra de solicitud HTTP para realizar operaciones HTTP, como GET, POST, PUT o DELETE. Por ejemplo, puede ejecutar búsquedas de Elasticsearch mediante una consulta POST y proporcionar los detalles de la consulta en el cuadro de *datos del cuerpo*:

![](./media/guidance-elasticsearch/jmeter-testing4.png)

- Para facilitar la repetibilidad y la reutilización, parametrice los planes de pruebas de JMeter. A continuación, puede utilizar scripting para automatizar la ejecución de planes de pruebas.

## Implementación de una prueba de JUnit

Puede incorporar código complejo en un plan de pruebas de JMeter mediante la creación de una o varias pruebas de JUnit. Puede escribir una prueba de JUnit mediante un IDE de Java como Eclipse. [Implementación de una muestra de JUnit en JMeter para probar el rendimiento de Elasticsearch][]\: ofrece información sobre cómo configurar un entorno de desarrollo apropiado.

En la lista siguiente se resumen algunos procedimientos recomendados que se deben seguir al escribir el código para una prueba de JUnit:

- Utilice el constructor de clase de prueba para pasar los parámetros de inicialización a la prueba. JMeter puede utilizar un constructor que adopta un único argumento de cadena. En el constructor, analice este argumento en sus elementos individuales, como se muestra en el ejemplo de código siguiente:

```Java
private String hostName = "";
private String indexName = "";
private String typeName = "";
private int port = 0;
private String clusterName = "";
private int itemsPerBatch = 0;

/* JUnit test class constructor */
public ElasticsearchLoadTest2(String params) {
	/* params is a string containing a set of comma separated values for:
		hostName
		indexName
		typeName
		port
		clustername
		itemsPerBatch
	*/

    /* Parse the parameter string into an array of string items */
	String delims = "[ ]*,[ ]*"; // comma surrounded by zero or more spaces
	String[] items = params.split(delims);

    /* Note: Parameter validation code omitted */

	/* Use the parameters to populate variables used by the test */
	hostName = items[0];
	indexName = items[1];
	typeName = items[2];
	port = Integer.parseInt(items[3]);
	clusterName = items[4];
	itemsPerBatch = Integer.parseInt(items[5]);

	if(itemsPerBatch == 0)
		itemsPerBatch = 1000;
}
```

- Evite operaciones caras o de E/S en el constructor o configurar la clase de pruebas, porque se ejecutan cada vez que se inicia la prueba de JUnit (la misma prueba de JUnit puede ejecutarse muchas miles de veces por cada prueba de rendimiento realizada en JMeter).

- Considere la opción de utilizar la configuración única para la inicialización de casos de pruebas caros.

- Si la prueba requiere un gran número de parámetros de entrada, almacene la información de configuración de pruebas en un archivo de configuración independiente y pase la ubicación de este archivo al constructor.

- Evite las rutas de acceso de archivo con codificación de forma rígida en el código de la prueba de carga. Pueden causar errores debido a diferencias entre sistemas operativos, como Windows y Linux.

- Utilice aserciones para indicar errores en los métodos de prueba de JUnit, de forma que pueda realizarles un seguimiento con JMeter y utilizarlas como métricas empresariales. Si es posible, pase la información sobre la causa del error, como se muestra en negrita en el ejemplo de código siguiente:

```Java
@Test
public void bulkInsertTest() throws IOException {
	...
	BulkResponse bulkResponse = bulkRequest.execute().actionGet();
	assertFalse(
		bulkResponse.buildFailureMessage(), bulkResponse.hasFailures());
		...
}
```

[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[Ajuste del rendimiento de introducción de datos para Elasticsearch en Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Implementación de una muestra de JUnit en JMeter para probar el rendimiento de Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Ajuste de la agregación de datos y el rendimiento de las consultas para Elasticsearch en Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md

<!---HONumber=AcomDC_0224_2016-->