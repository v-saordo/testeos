
<properties
   pageTitle="Ejecución de pruebas automatizadas de rendimiento de Elasticsearch | Microsoft Azure"
   description="Descripción de cómo ejecutar las pruebas de rendimiento en su propio entorno."
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
   
# Ejecución de pruebas automatizadas de rendimiento de Elasticsearch

Este artículo [forma parte de una serie](guidance-elasticsearch.md).

Los documentos [Ajuste del rendimiento de ingesta de datos para Elasticsearch en Azure] y [Ajuste del rendimiento de consultas y agregación de datos para Elasticsearch en Azure] describen una serie de pruebas de rendimiento que se ejecutaron en un clúster de Elasticsearch de ejemplo.

Estas pruebas se diseñaron con un script para permitir que se ejecutaran de forma automática. En este documento, se describe cómo repetir las pruebas en su propio entorno.

## Requisitos previos

Las pruebas automatizadas requieren los siguientes elementos:

-  Un clúster de Elasticsearch.

- Una configuración de entorno JMeter, como se describe en el documento [Creación de un entorno de pruebas de rendimiento para Elasticsearch en Azure].

- El siguiente software solo se instala en la máquina virtual maestra de JMeter.

    [Python 3.5.1](https://www.python.org/downloads/release/python-351/)


## Cómo funcionan las pruebas
Las pruebas se ejecutan mediante JMeter. Un servidor maestro JMeter carga un plan de pruebas y lo pasa a un conjunto de servidores subordinados JMeter que realmente ejecutan las pruebas. El servidor maestro JMeter coordina los servidores subordinados JMeter y acumula los resultados.

Se ofrecen los siguientes planes de pruebas.

* [elasticsearchautotestplan3nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan3nodes.jmx). Este plan de pruebas ejecuta la prueba de ingesta en un clúster de tres nodos.

* [elasticsearchautotestplan6nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodes.jmx). Este plan de pruebas ejecuta la prueba de ingesta en un clúster de seis nodos.

* [elasticsearchautotestplan6qnodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6qnodes.jmx). Este plan de pruebas ejecuta la prueba de ingesta y consultas en un clúster de seis nodos.

* [elasticsearchautotestplan6nodesqueryonly.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodesqueryonly.jmx). Este plan de pruebas ejecuta la prueba de solo consulta en un clúster de seis nodos.


Puede usar estos planes de pruebas como base para sus propios escenarios si necesita más o menos nodos.

Los planes de pruebas usan una muestra de JUnit Request para generar y cargar los datos de prueba. El plan de pruebas JMeter crea y ejecuta esta muestra y supervisa los datos de rendimiento en cada uno de los nodos de Elasticsearch. Para más información, consulte los apéndices a los documentos [Ajuste del rendimiento de ingesta de datos para Elasticsearch en Azure] y [Ajuste del rendimiento de consultas y agregación de datos para Elasticsearch en Azure].

## Compilación e implementación del JUnit JAR y dependencias
Antes de ejecutar las pruebas de resistencia, debe descargar, compilar e implementar las pruebas de JUnit que se encuentran en la carpeta performance/junitcode. Se hace referencia a estas pruebas en el plan de pruebas de JMeter. Para más información, consulte el procedimiento Importing an Existing JUnit Test Project into Eclipse (Importación de un proyecto de prueba de JUnit existente en Eclipse) en el documento [Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance] (Implementación de una muestra de JUnit de JMeter para probar el rendimiento de Elasticsearch).

Hay dos versiones de las pruebas JUnit:- [Elasticsearch1.73](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch1.73). Utilice este código para realizar las pruebas de ingesta. Estas pruebas utilizan Elasticsearch 1.73 - [Elasticsearch2](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch2). Utilice este código para realizar las pruebas de consulta. Estas pruebas usan Elasticsearch 2.1 y versiones posteriores.

Copie el archivo JAR correspondiente junto con el resto de las dependencias en las máquinas de JMeter. Este proceso se describe en [Implementación de una muestra de JUnit en JMeter para probar el rendimiento de Elasticsearch][].

> **Importante** Después de implementar una prueba de JUnit, use JMeter para cargar y configurar los planes de pruebas que hacen referencia a esta prueba JUnit y asegúrese de que el grupo de subprocesos *BulkInsertLarge* hace referencia al archivo JAR, al nombre de clase JUnit y al método de prueba correctos:
> 
> ![](./media/guidance-elasticsearch/performance-tests-image1.png)
> 
> Guarde los planes de pruebas actualizados antes de ejecutar las pruebas.

## Creación de los índices de prueba
Cada prueba realiza la ingesta o las consultas en un índice único que se especifica al ejecutar la prueba. Debe crear el índice con los esquemas descritos en los apéndices a los documentos [Ajuste del rendimiento de ingesta de datos para Elasticsearch en Azure] y [Ajuste del rendimiento de consultas y agregación de datos para Elasticsearch en Azure] y configurarlos según su escenario de prueba (valores de documento habilitados o deshabilitados, varias réplicas, etc.) Tenga en cuenta que los planes de pruebas se basan en que el índice consta de un solo tipo denominado *ctip*.

## Configuración de los parámetros de script de prueba
Copie los siguientes archivos de parámetros de prueba en la máquina del servidor JMeter:

* [run.properties](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/run.properties). Este archivo especifica el número de subprocesos de la prueba de JMeter que se utilizan, la duración de la prueba (en segundos), la dirección IP de un nodo (o un equilibrador de carga) del clúster Elasticsearch y el nombre del clúster:

  ```ini
  nthreads=3
  duration=300
  elasticip=<IP Address or DNS Name Here>
  clustername=<Cluster Name Here>
  ```
  
  Edite este archivo y especifique los valores correspondientes para la prueba y el clúster.

* [query-config-win.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-win.ini) y [query-config-nix.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-nix.ini). Estos dos archivos contienen la misma información; el archivo *win* tiene el formato de nombres de archivo y rutas de acceso de Windows y el archivo *nix* tiene el formato de nombres de archivos y rutas de acceso de Linux:

  ```ini
  [DEFAULT]
  debug=true #si true, muestra los registros de consola.

  [RUN]
  pathreports=C:\\Users\\administrator1\\jmeter\\test-results\\ #ruta de acceso donde se guardan los resultados de pruebas.
  jmx=C:\\Users\\administrator1\\testplan.jmx #ruta de acceso al plan de pruebas JMeter.
  machines=10.0.0.1,10.0.02,10.0.0.3 #direcciones IP de los nodos de datos de Elasticsearch separadas por comas.
  reports=aggr,err,tps,waitio,cpu,network,disk,response,view #Nombre de los informes separados por comas.
  tests=idx1 #Nombre del índice de Elasticsearch sometido a prueba.
  properties=run.properties #Nombre del archivo de propiedades.
  ```

  Edite este archivo para especificar las ubicaciones de los resultados de pruebas, el nombre del plan de pruebas JMeter que se va a ejecutar, las direcciones IP de los nodos de datos de Elasticsearch, los informes que contienen los datos de rendimiento sin procesar que se van a generar y el nombre (o los nombres) del índice sometido a prueba. Si el archivo *run.properties* se encuentra en una carpeta o un directorio diferente, especifique la ruta de acceso completa de este archivo.

## Ejecución de las pruebas

* Copie el archivo [query-test.py](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-test.py) en la máquina del servidor JMeter, en la misma carpeta que los archivos *run.properties* y *query-config-win.ini* (*query-config-nix.ini*).

* Asegúrese de que *jmeter.bat* (Windows) o *jmeter.sh* (Linux) se encuentran en la ruta de acceso del archivo ejecutable para su entorno.

* Ejecute el script *query-test.py* desde la línea de comandos para realizar las pruebas:

  ```cmd
  py query-test.py
  ```

* Una vez finalizada la prueba, los resultados se almacenan como el conjunto de archivos CSV especificado en el archivo *query-config-win.ini* (*query-config-nix.ini*). Puede usar Excel para analizar y representar gráficamente estos datos.


[Ajuste del rendimiento de ingesta de datos para Elasticsearch en Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Ajuste del rendimiento de consultas y agregación de datos para Elasticsearch en Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Creación de un entorno de pruebas de rendimiento para Elasticsearch en Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
[Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Implementación de una muestra de JUnit en JMeter para probar el rendimiento de Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0224_2016-->
