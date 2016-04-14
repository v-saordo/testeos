<properties
pageTitle="Usar la vista de Tez de Ambari con HDInsight | Azure"
description="Obtenga información sobre cómo usar la vista de Tez de Ambari para depurar trabajos de Tez en HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="02/16/2016"
ms.author="larryfr"/>

# Usar vistas de Ambari para depurar trabajos de Tez en HDInsight

La interfaz de usuario web de Ambari para HDInsight contiene una vista de Tez que puede usarse para comprender y depurar los trabajos que usan Tez como motor de ejecución. La vista de Tez permite visualizar el trabajo como un gráfico de elementos conectados, profundizar en cada elemento y recuperar las estadísticas y la información de registro.

> [AZURE.NOTE] La información contenida en este documento es específica de los clústeres de HDInsight basados en Linux. Para obtener información sobre la depuración de trabajos de Tez con HDInsight basado en Windows, consulte [Use the Tez UI to debug Tez jobs on Windows-based HDInsight](hdinsight-debug-tez-ui.md) (Usar la interfaz de usuario de Tez para depurar trabajos de Tez en HDInsight basado en Windows).

##Requisitos previos

* Un clúster de HDInsight basado en Linux Para obtener información sobre cómo crear un clúster, consulte [Introducción al uso de HDInsight en Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

* Un explorador web moderno que sea compatible con HTML5.

##Descripción de Tez

Tez es un marco de trabajo extensible para el procesamiento de datos en Hadoop que proporciona una mayor velocidad que el procesamiento tradicional de MapReduce. Para los clústeres de HDInsight basados en Linux, es el motor predeterminado de Hive.

Cuando se envía un trabajo a Tez, este crea un grafo acíclico dirigido (DAG) que describe el orden de ejecución de las acciones requeridas por el trabajo. Las acciones individuales se denominan vértices y ejecutan una parte del trabajo total. La ejecución real del trabajo descrito por un vértice se denomina tarea, y puede distribuirse por varios nodos del clúster.

###Descripción de la vista de Tez

La vista de Tez proporciona información sobre los procesos que se están ejecutando o que se ejecutaron previamente mediante Tez. Permite ver el DAG generado por Tez, la manera en que se distribuye por los clústeres, contadores como la memoria usada por tareas y vértices, e información de error. Puede ofrecer información útil en los escenarios siguientes:

* Supervisión de procesos de ejecución prolongada, visualización del progreso de asignación y reducción de las tareas.

* Análisis de datos históricos de procesos correctos o con errores para obtener información sobre cómo se puede mejorar el procesamiento o sobre la causa del error.

##Generar un DAG

La vista de Tez solo contendrá datos si se está ejecutando actualmente un trabajo que usa el motor de Tez, o bien si se ejecutó anteriormente. Las consultas de Hive sencillas normalmente se pueden resolver sin usar Tez, pero las consultas más complejas con filtrado, agrupación, ordenación, uniones, etc. suelen requerir Tez.

Siga los pasos que se indican a continuación para realizar una consulta de Hive que se ejecutará mediante Tez.

1. En un explorador web, vaya a https://CLUSTERNAME.azurehdinsight.net, donde __CLUSTERNAME__ es el nombre del clúster de HDInsight.

2. En el menú que aparece en la parte superior de la página, seleccione el icono __Vistas__. Tiene el aspecto de un conjunto de cuadrados. En la lista desplegable que aparece, seleccione __Vista de Hive__.

    ![Seleccionar la vista de Hive](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Cuando se cargue la vista de Hive, pegue lo siguiente en el Editor de consultas y luego haga clic en __Ejecutar__.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
    
    Cuando se haya completado el trabajo, debería ver el resultado en la sección __Resultados del proceso de consulta__. Los resultados deberían parecerse a lo siguiente.
    
        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        
4. Seleccione la pestaña __Registro__. Verá información similar a la siguiente:
    
        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Guarde el valor del __Id. de la aplicación__, ya que se usará en la sección siguiente.

##Usar la vista de Tez

1. En el menú que aparece en la parte superior de la página, seleccione el icono __Vistas__. En la lista desplegable que aparece, seleccione __Vista de Tez__.

    ![Seleccionar la vista de Tez](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Cuando se cargue la vista de Tez, verá una lista de los DAG que se están ejecutando actualmente o que se ejecutaron anteriormente en el clúster. La vista predeterminada incluye el nombre del DAG, el identificador, el remitente, el estado, la hora de inicio, la hora de finalización, la duración, el identificador de la aplicación y la cola. Puede agregar más columnas mediante el icono de engranaje que se encuentra a la derecha de la página.

    ![Todos los DAG](./media/hdinsight-debug-ambari-tez-view/alldags.png)

3. Si solo tiene una entrada, será la de la consulta que ejecutó en la sección anterior. Si tiene varias entradas, puede realizar una búsqueda escribiendo el identificador de la aplicación en el campo __Id. de la aplicación__ y presionando Entrar.

4. Seleccione el __Nombre del DAG__. Se mostrará información sobre el DAG, así como la opción de descargar un archivo ZIP con archivos JSON que contienen información sobre el DAG.

    ![Detalles del DAG](./media/hdinsight-debug-ambari-tez-view/dagdetails.png)

5. Encima de __Detalles del DAG__ aparecen varios vínculos que pueden usarse para mostrar información sobre el DAG.

    * __Contadores del DAG__ muestra información de los contadores de este DAG.
    
    * __Vista gráfica__ muestra una representación gráfica de este DAG.
    
    * __Todos los vértices__ muestra una lista de los vértices de este DAG.
    
    * __Todas las tareas__ muestra una lista de las tareas de todos los vértices de este DAG.
    
    * __Todos los intentos de tarea__ muestra información sobre los intentos de ejecutar las tareas de este DAG.
    
    > [AZURE.NOTE] Si se desplaza por la presentación de columna de Vértices, Tareas e Intentos de tarea, verá que hay vínculos para ver los __contadores__ y para __ver o descargar los registros__ de cada fila.

    Si se produjo un error en el trabajo, los detalles del DAG mostrarán un estado de error, junto con vínculos a información sobre la tarea con error. Debajo de los detalles del DAG, se mostrará información de diagnóstico.
    
    ![Pantalla de detalles del DAG que muestra un error con detalles](./media/hdinsight-debug-ambari-tez-view/faileddag.png)

7. Seleccione __Vista gráfica__. Muestra una representación gráfica del DAG. Puede colocar el mouse sobre cada vértice de la vista para mostrar su información.

    ![Vista gráfica](./media/hdinsight-debug-ambari-tez-view/dagdiagram.png)

8. Al hacer clic en un vértice, se cargarán los __Detalles del vértice__ de ese elemento. Haga clic en el vértice __Asignación 1__ para mostrar los detalles de ese elemento.

    ![Detalles del vértice](./media/hdinsight-debug-ambari-tez-view/vertexdetails.png)

9. Observe que ahora aparecen vínculos en la parte superior de la página que están relacionados con las tareas y los vértices.

    > [AZURE.NOTE] También puede llegar a esta página si regresa a __Detalles del DAG__, selecciona __Detalles del vértice__ y selecciona el vértice __Asignación 1__.

    * __Contadores del vértice__ muestra información de los contadores de este vértice.
    
    * __Tareas__ muestra las tareas de este vértice.
    
    * __Intentos de tarea__ muestra información sobre los intentos de ejecutar las tareas de este vértice.
    
    * __Orígenes y receptores__ muestra los orígenes de datos y los receptores de este vértice.

    > [AZURE.NOTE] Al igual que en el menú anterior, puede desplazarse por la presentación de columna de Tareas, Intentos de tarea y Orígenes y receptores para que aparezcan vínculos a información adicional sobre cada elemento.

10. Seleccione __Tareas__ y luego seleccione el elemento denominado __00\_000000__. De este modo se mostrarán los __Detalles de la tarea__ de esta tarea. En esta pantalla, puede ver los __Contadores de tarea__ y los __Intentos de tarea__.

    ![Detalles de la tarea](./media/hdinsight-debug-ambari-tez-view/taskdetails.png)

##Pasos siguientes

Ahora que ha aprendido a usar la vista de Tez, obtenga más información sobre el [Uso de Hive en HDInsight](hdinsight-use-hive.md).

Para obtener información técnica más detallada sobre Tez, consulte la [página de Tez en Hortonworks](http://hortonworks.com/hadoop/tez/).

Para obtener más información sobre el uso de Ambari con HDInsight, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md).

<!---HONumber=AcomDC_0218_2016-->