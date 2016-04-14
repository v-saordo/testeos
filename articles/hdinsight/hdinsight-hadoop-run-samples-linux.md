<properties
	pageTitle="Ejecución de ejemplos de Hadoop MapReduce en HDInsight basado en Linux | Microsoft Azure"
	description="Introducción al uso de ejemplos de MapReduce con HDInsight basado en Linux. Use SSH para conectarse al clúster y, a continuación, use el comando de Hadoop para ejecutar trabajos de ejemplo."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
    tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="larryfr"/>




#Ejecución de ejemplos de Hadoop en HDInsight

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Los clústeres de HDInsight basado en Linux proporcionan un conjunto de ejemplos de MapReduce que sirven para familiarizarse con la ejecución de trabajos de MapReduce de Hadoop. En este documento, aprenderá acerca de los ejemplos disponibles y verá cómo se ejecutan algunos de ellos.

##Requisitos previos

- **Suscripción a Azure**: consulte [Obtener una versión de evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un clúster de HDInsight basado en Linux**: consulte [Introducción al uso de Hadoop con Hive en HDInsight en Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

- **Un cliente SSH**: para obtener información sobre el uso de SSH con HDInsight, consulte los siguientes artículos:

    - [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## Las muestras ##

**Ubicación**: los ejemplos se encuentran en el clúster de HDInsight en **/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar**.

**Contenido**: se incluyen los siguientes ejemplos en este archivo:

- **aggregatewordcount**: programa de asignación/reducción basado en agregación que cuenta las palabras de los archivos de entrada.
- **aggregatewordhist**: programa de asignación/reducción basado en agregación que calcula el histograma de las palabras de los archivos de entrada.
- **bbp**: programa de asignación/reducción que usa una fórmula Bailey-Borwein-Plouffe para calcular los dígitos exactos de Pi.
- **dbcount**: trabajo de ejemplo que cuenta los recuentos de vistas de página desde una base de datos.
- **distbbp**: programa de asignación/reducción que usa una fórmula de tipo BBP para calcular los bits exactos de Pi.
- **grep**: programa de asignación/reducción que cuenta las coincidencias de una regex en la entrada.
- **join**: trabajo que efectúa una unión de conjuntos de datos ordenados con particiones equiparables.
- **multifilewc**: trabajo que cuenta las palabras de varios archivos.
- **pentomino**: programa de asignación/reducción para la colocación de mosaicos con el fin de encontrar soluciones a problemas de pentominó.
- **pi**: programa de asignación/reducción que calcula Pi mediante un método cuasi Monte Carlo.
- **randomtextwriter**: programa de asignación/reducción que escribe 10 GB de datos textuales aleatorios por nodo.
- **randomwriter**: programa de asignación/reducción que escribe 10 GB de datos aleatorios por nodo.
- **secondarysort**: ejemplo que define una ordenación secundaria para la reducción.
- **sort**: programa de asignación/reducción que ordena los datos escritos por el escritor aleatorio.
- **sudoku**: solucionador de sudokus.
- **teragen**: genera datos para la ordenación de terabytes (terasort).
- **terasort**: Ejecuta la ordenación de terabytes (terasort).
- **teravalidate**: comprueba los resultados de la ordenación de terabytes (terasort).
- **wordcount**: programa de asignación/reducción que cuenta las palabras de los archivos de entrada.
- **wordmean**: programa de asignación/reducción que cuenta la longitud media de las palabras de los archivos de entrada.
- **wordmedian**: programa de asignación/reducción que cuenta la mediana de longitud de las palabras de los archivos de entrada.
- **wordstandarddeviation**: programa de asignación/reducción que cuenta la desviación estándar de la longitud de las palabras de los archivos de entrada.

**Código fuente**: el código fuente de estos ejemplos se incluye en el clúster de HDInsight en **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples**.

> [AZURE.NOTE] El `2.2.4.9-1` en la ruta de acceso es la versión de Hortonworks Data Platform para el clúster de HDInsight y puede cambiar cuando se actualice HDInsight.

## Ejecución de las muestras ##

1. Conéctese a HDInsight con SSH, tal y como se describe en los siguientes artículos:

    - [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. En el símbolo `username@#######:~$`, use el siguiente comando para mostrar los ejemplos:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

    Esto generará la lista de ejemplos de la sección anterior de este documento.

3. Use el siguiente comando para obtener ayuda sobre un ejemplo concreto. En este caso, el ejemplo **wordcount**:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount

    Debería recibir el siguiente mensaje:

        Usage: wordcount <in> [<in>...] <out>

    Esto indica que puede proporcionar varias rutas de acceso de entrada para los documentos de origen y la última ruta de acceso es donde se ubicará la salida (recuento de palabras en los documentos de origen).

4. Use lo siguiente para contar todas las palabras en los cuadernos de Leonardo Da Vinci, que se proporcionan como datos de ejemplo con su clúster:

    	yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount

    La entrada de este trabajo se lee desde ****wasb:///example/data/gutenberg/davinci.txt**.

    La salida de este ejemplo se almacenará en ****wasb:///example/data/davinciwordcount**.

    > [AZURE.NOTE] Como se indica en la Ayuda del ejemplo wordcount, también puede especificar varios archivos de entrada. Por ejemplo, `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` contaría las palabras de davinci.txt y ulysses.txt.

5. Una vez completado el trabajo, use el siguiente comando para ver la salida:

        hdfs dfs -cat /example/data/davinciwordcount/*

    Esto concatena todos los archivos de salida generados por el trabajo y los muestra. Para este ejemplo básico solo hay un archivo; sin embargo, si hubiera más, este comando podría iterarse para todos ellos.

    Verá un resultado similar al siguiente con este comando:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Cada línea representa una palabra y el número de veces que aparece en los datos de entrada.

## Sudoku

El ejemplo sudoku proporciona instrucciones de uso no muy útiles para incluir un rompecabezas en la línea de comandos.

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) es un rompecabezas lógico compuesto por nueve cuadrículas de 3 × 3. Algunas celdas de la cuadrícula tienen números y otras están en blanco; el objetivo es resolver las celdas en blanco. El vínculo anterior contiene más información sobre el rompecabezas, pero el propósito de este ejemplo consiste en resolver las celdas en blanco. Así que nuestra entrada debe ser un archivo en el formato siguiente:

- Nueve filas de nueve columnas;

- Cada columna puede contener un número o `?` (que indica una celda en blanco);

- Las celdas se separan por un espacio.

Existe una forma determinada de construir rompecabezas sudoku para que no se repita un número en una columna o fila. Afortunadamente, hay un ejemplo en el clúster de HDInsight que se ha creado correctamente. Se encuentra en **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta** y contiene lo siguiente:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

> [AZURE.NOTE] Puede que la parte `2.2.4.9-1` de la ruta de acceso cambie cuando se actualice el clúster de HDInsight.

Para ejecutar esto con el ejemplo sudoku, use el comando siguiente:

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/2.2.9.1-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta

Los resultados deberían parecerse a los siguientes:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## Pi (π)

El ejemplo pi usa un método estadístico (cuasi Monte Carlo) para calcular el valor de pi. Los puntos colocados en el interior aleatorio de un cuadrado unitario también entran dentro de un círculo inscrito dentro de ese cuadrado con una probabilidad igual al área del círculo, pi/4. El valor de pi se puede estimar a partir del valor de 4R, donde R es la proporción de la cantidad de puntos contados dentro del círculo con respecto al número total de puntos que se encuentran dentro del cuadrado. Mientras más grande sea la muestra de puntos usada, mejor resulta el valor calculado.

El asignador de este ejemplo genera un número de puntos de forma aleatoria en el interior de un cuadrado unitario y, a continuación, cuenta la cantidad de esos puntos que se encuentran dentro del círculo.

Después, el reductor acumula los puntos contados por los asignadores y, a continuación, calcula el valor de pi a partir de la fórmula 4R, donde R es la proporción de la cantidad de puntos contados dentro del círculo con respecto al número total de puntos que se encuentran dentro del cuadrado.

Use el siguiente comando para ejecutar este ejemplo. Se usan 16 asignaciones con 10 millones de ejemplos cada una para calcular el valor de pi:

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000

El valor devuelto debería asemejarse a **3,14159155000000000000**. Como referencia, las primeras 10 posiciones decimales de pi son 3,1415926535.

##Greysort de 10 GB

GraySort es un tipo de banco de pruebas cuya métrica es la velocidad de ordenación (TB/minuto) que se logra después de ordenar enormes volúmenes de datos, normalmente 100 TB como mínimo.

Este ejemplo utiliza solo 10 GB de datos, para así poder ejecutarlo relativamente rápido. En él se emplean las aplicaciones de MapReduce, desarrolladas por Owen O'Malley y Arun Murthy, que ganaron el estándar de comparación anual de ordenación de terabytes de fin general ("daytona") en 2009 con una velocidad de 0,578 TB/min (100 TB en 173 minutos). Para obtener más información sobre este y otros estándares de comparación de ordenación, consulte el sitio [Sortbenchmark](http://sortbenchmark.org/).

Este ejemplo utiliza tres conjuntos de programas de MapReduce:

- **TeraGen**: programa de MapReduce que genera filas de datos que se van a ordenar.

- **TeraSort**: toma una muestra de los datos de entrada y usa MapReduce para ordenar los datos de manera absoluta.

    TeraSort es un ordenamiento estándar de funciones de MapReduce, con la excepción de un particionador personalizado que utiliza una lista ordenada de N-1 claves de muestra que definen el rango de claves para cada reducción. En concreto, todas las claves, como esa muestra[i-1] <= clave < muestra[i] se envían a la reducción i. Esto garantiza que las salidas de la reducción i sean todas menores que la salida de la reducción i+1.

- **TeraValidate**: programa de MapReduce que valida que la salida se ordene de manera global.

    Crea una asignación por archivo en el directorio de salida y cada asignación asegura que cada clave es menor o igual que la anterior. La función de asignación también genera registros de la primera y la última clave de cada archivo, y la función de reducción garantiza que la primera clave del archivo i es mayor que la última clave de archivo i-1. Los problemas se notifican como una salida de la reducción con las claves que no están en orden.

Utilice los siguientes pasos para generar datos, ordenarlos y, a continuación, validar el resultado:

1. Genere 10 GB de datos, que se guardarán en el almacenamiento predeterminado del clúster de HDInsight en ****wasb:///example/data/10GB-sort-input**:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input

	`-Dmapred.map.tasks` indica a Hadoop cuántas tareas de asignación se usarán para el trabajo. Los dos parámetros finales indican al trabajo que cree 10 GB de datos y los almacene en ****wasb:///example/data/10GB-sort-input**.

2. Use el siguiente comando para ordenar los datos:

		yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output

	`-Dmapred.reduce.tasks` indica a Hadoop cuántas tareas de reducción se usarán para el trabajo. Los dos parámetros finales son simplemente las ubicaciones de entrada y salida de los datos.

3. Use lo siguiente para validar los datos generados por la ordenación:

		yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate

##Pasos siguientes ##

En este artículo, ha obtenido información acerca de cómo ejecutar los ejemplos incluidos en los clústeres de HDInsight basado en Linux. Para obtener acceso a tutoriales sobre cómo usar Pig, Hive y MapReduce con HDInsight, consulte los siguientes temas:

* [Uso de Pig con Hadoop en HDInsight][hdinsight-use-pig]
* [Uso de Hive con Hadoop en HDInsight][hdinsight-use-hive]
* [Uso de MapReduce con Hadoop en HDInsight][hdinsight-use-mapreduce]



[hdinsight-errors]: hdinsight-debug-jobs.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

<!---HONumber=AcomDC_0211_2016-->