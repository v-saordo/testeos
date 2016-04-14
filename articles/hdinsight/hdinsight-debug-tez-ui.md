<properties
pageTitle="Usar la interfaz de usuario de Tez con HDInsight basado en Windows | Azure"
description="Obtener más información sobre cómo usar la interfaz de usuario de Tez para depurar trabajos de Tez en HDInsight basado en Windows"
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

# Usar la interfaz de usuario de Tez para depurar trabajos de Tez en HDInsight basado en Windows

La interfaz de usuario de Tez es una página web que se puede usar para comprender y depurar los trabajos que usan Tez como motor de ejecución en clústeres de HDInsight basados en Windows. La interfaz de usuario de Tez permite visualizar el trabajo como un gráfico de elementos conectados, profundizar en cada elemento y recuperar las estadísticas y la información de registro.

> [AZURE.NOTE] La información contenida en este documento es específica de los clústeres de HDInsight basados en Windows. Para obtener información sobre cómo ver y depurar Tez en HDInsight basado en Linux, consulte [Use Ambari Views to debug Tez jobs on HDInsight](hdinsight-debug-ambari-tez-view.md) (Usar las vistas de Ambari para depurar trabajos de Tez en HDInsight).

##Requisitos previos

* Un clúster de HDInsight basado en Windows Para obtener información sobre cómo crear un clúster, consulte [Introducción al uso de HDInsight basado en Windows](hdinsight-hadoop-tutorial-get-started-windows.md).

    > [AZURE.IMPORTANT] La interfaz de usuario de Tez solo está disponible en los clústeres de HDInsight basado en Windows creados después del 8 de febrero de 2016.

* Un cliente de escritorio remoto basado en Windows.

##Descripción de Tez

Tez es un marco de trabajo extensible para el procesamiento de datos en Hadoop que proporciona una mayor velocidad que el procesamiento tradicional de MapReduce. Para los clústeres de HDInsight basado en Windows, es un motor opcional que se puede habilitar para Hive mediante el comando siguiente como parte de la consulta de Hive:

    set hive.execution.engine=tez;

Cuando se envía un trabajo a Tez, este crea un grafo acíclico dirigido (DAG) que describe el orden de ejecución de las acciones requeridas por el trabajo. Las acciones individuales se denominan vértices y ejecutan una parte del trabajo total. La ejecución real del trabajo descrito por un vértice se denomina tarea, y puede distribuirse por varios nodos del clúster.

###Descripción de la interfaz de usuario de Tez

La interfaz de usuario de Tez es una página web que proporciona información sobre los procesos que se están ejecutando o que se ejecutaron previamente mediante Tez. Permite ver el DAG generado por Tez, la manera en que se distribuye por los clústeres, contadores como la memoria usada por tareas y vértices, e información de error. Puede ofrecer información útil en los escenarios siguientes:

* Supervisión de procesos de ejecución prolongada, visualización del progreso de asignación y reducción de las tareas.

* Análisis de datos históricos de procesos correctos o con errores para obtener información sobre cómo se puede mejorar el procesamiento o sobre la causa del error.

##Generar un DAG

La interfaz de usuario de Tez solo contendrá datos si se está ejecutando actualmente un trabajo que usa el motor de Tez, o bien si se ejecutó anteriormente. Las consultas de Hive sencillas normalmente se pueden resolver sin usar Tez, pero las consultas más complejas con filtrado, agrupación, ordenación, uniones, etc. suelen requerir Tez.

Siga los pasos que se indican a continuación para realizar una consulta de Hive que se ejecutará mediante Tez.

1. En un explorador web, vaya a https://CLUSTERNAME.azurehdinsight.net, donde __CLUSTERNAME__ es el nombre del clúster de HDInsight.

2. En el menú que aparece en la parte superior de la página, seleccione el __Editor de Hive__. Se mostrará una página con la siguiente consulta de ejemplo.

        Select * from hivesampletable

    Borre la consulta de ejemplo y reemplácela por lo siguiente.

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

3. Seleccione el botón __Enviar__. En la sección __Sesión de trabajo__ que aparece en la parte inferior de la página se mostrará el estado de la consulta. Cuando el estado cambie a __Completado__, seleccione el vínculo __Ver detalles__ para ver los resultados. La __Salida del trabajo__ debe ser similar a la siguiente:
        
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

##Usar la interfaz de usuario de Tez

> [AZURE.NOTE] La interfaz de usuario de Tez solo está disponible en el escritorio de los nodos principales del clúster, por lo que debe usar Escritorio remoto para conectarse a los nodos principales.

1. En el [Portal de Azure](https://portal.azure.com), seleccione el clúster de HDInsight. En la parte superior de la hoja de HDInsight, seleccione el icono __Escritorio remoto__. Esto mostrará la hoja de Escritorio remoto.

    ![Icono de Escritorio remoto](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)

2. En la hoja de Escritorio remoto, seleccione __Conectar__ para conectarse al nodo principal del clúster. Cuando se le solicite, use el nombre de usuario y la contraseña de Escritorio remoto del clúster para autenticar la conexión.

    ![Icono de conexión a Escritorio remoto](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

    > [AZURE.NOTE] Si no ha habilitado la conectividad de Escritorio remoto, proporcione un nombre de usuario, una contraseña y una fecha de expiración y luego seleccione __Habilitar__ para habilitar Escritorio remoto. Una vez habilitado, siga los pasos anteriores para conectarse.

3. Cuando se haya conectado, abra Internet Explorer en el escritorio remoto, seleccione el icono de engranaje en la esquina superior derecha del explorador y seleccione __Configuración de Vista de compatibilidad__.

4. En la parte inferior de __Configuración de Vista de compatibilidad__, desactive la casilla __Mostrar sitios de la intranet en Vista de compatibilidad__ y __Usar listas de compatibilidad de Microsoft__ y luego seleccione __Cerrar__.

5. En Internet Explorer, vaya a http://headnodehost:8188/tezui/#/. Esta acción mostrará la interfaz de usuario de Tez.

    ![Interfaz de usuario de Tez](./media/hdinsight-debug-tez-ui/tezui.png)

    Cuando se cargue la interfaz de usuario de Tez, verá una lista de DAG que se están ejecutando actualmente o que se ejecutaron anteriormente en el clúster. La vista predeterminada incluye el nombre del DAG, el identificador, el remitente, el estado, la hora de inicio, la hora de finalización, la duración, el identificador de la aplicación y la cola. Puede agregar más columnas mediante el icono de engranaje que se encuentra a la derecha de la página.

    Si solo tiene una entrada, será la de la consulta que ejecutó en la sección anterior. Si tiene varias entradas, puede realizar una búsqueda especificando criterios de búsqueda en los campos situados encima de los DAG y presionando __ENTRAR__.

4. Seleccione el __Nombre del DAG__ de la entrada de DAG más reciente. Se mostrará información sobre el DAG, así como la opción de descargar un archivo ZIP con archivos JSON que contienen información sobre el DAG.

    ![Detalles del DAG](./media/hdinsight-debug-tez-ui/dagdetails.png)

5. Encima de __Detalles del DAG__ aparecen varios vínculos que pueden usarse para mostrar información sobre el DAG.

    * __Contadores del DAG__ muestra información de los contadores de este DAG.
    
    * __Vista gráfica__ muestra una representación gráfica de este DAG.
    
    * __Todos los vértices__ muestra una lista de los vértices de este DAG.
    
    * __Todas las tareas__ muestra una lista de las tareas de todos los vértices de este DAG.
    
    * __Todos los intentos de tarea__ muestra información sobre los intentos de ejecutar las tareas de este DAG.
    
    > [AZURE.NOTE] Si se desplaza por la presentación de columna de Vértices, Tareas e Intentos de tarea, verá que hay vínculos para ver los __contadores__ y para __ver o descargar los registros__ de cada fila.

    Si se produjo un error en el trabajo, los detalles del DAG mostrarán un estado de error, junto con vínculos a información sobre la tarea con error. Debajo de los detalles del DAG, se mostrará información de diagnóstico.

7. Seleccione __Vista gráfica__. Muestra una representación gráfica del DAG. Puede colocar el mouse sobre cada vértice de la vista para mostrar su información.

    ![Vista gráfica](./media/hdinsight-debug-tez-ui/dagdiagram.png)

8. Al hacer clic en un vértice, se cargarán los __Detalles del vértice__ de ese elemento. Haga clic en el vértice __Asignación 1__ para mostrar los detalles de ese elemento. Seleccione __Confirmar__ para confirmar la navegación.

    ![Detalles del vértice](./media/hdinsight-debug-tez-ui/vertexdetails.png)

9. Observe que ahora aparecen vínculos en la parte superior de la página que están relacionados con las tareas y los vértices.

    > [AZURE.NOTE] También puede llegar a esta página si regresa a __Detalles del DAG__, selecciona __Detalles del vértice__ y selecciona el vértice __Asignación 1__.

    * __Contadores del vértice__ muestra información de los contadores de este vértice.
    
    * __Tareas__ muestra las tareas de este vértice.
    
    * __Intentos de tarea__ muestra información sobre los intentos de ejecutar las tareas de este vértice.
    
    * __Orígenes y receptores__ muestra los orígenes de datos y los receptores de este vértice.

    > [AZURE.NOTE] Al igual que en el menú anterior, puede desplazarse por la presentación de columna de Tareas, Intentos de tarea y Orígenes y receptores para que aparezcan vínculos a información adicional sobre cada elemento.

10. Seleccione __Tareas__ y luego seleccione el elemento denominado __00\_000000__. De este modo se mostrarán los __Detalles de la tarea__ de esta tarea. En esta pantalla, puede ver los __Contadores de tarea__ y los __Intentos de tarea__.

    ![Detalles de la tarea](./media/hdinsight-debug-tez-ui/taskdetails.png)

##Pasos siguientes

Ahora que ha aprendido a usar la vista de Tez, obtenga más información sobre el [Uso de Hive en HDInsight](hdinsight-use-hive.md).

Para obtener información técnica más detallada sobre Tez, consulte la [página de Tez en Hortonworks](http://hortonworks.com/hadoop/tez/).

<!---HONumber=AcomDC_0218_2016-->