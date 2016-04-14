<properties
	pageTitle="Generación de recomendaciones mediante Mahout y HDInsight basado en Linux | Microsoft Azure"
	description="Aprenda a usar la biblioteca de aprendizaje automático de Apache Mahout para generar recomendaciones de películas con HDInsight basado en Linux (Hadoop)."
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
	ms.date="01/28/2016"
	ms.author="larryfr"/>

#Generación de recomendaciones de películas mediante Apache Mahout con Hadoop basado en Linux en HDInsight

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Aprenda a usar la biblioteca de aprendizaje automático de [Apache Mahout](http://mahout.apache.org) con HDInsight de Azure para generar recomendaciones de películas con HDInsight.

Mahout es una biblioteca de [aprendizaje automático][ml] para Apache Hadoop. Mahout contiene algoritmos para el procesamiento de datos, como filtrado, clasificación y agrupación en clústeres. En este artículo usará un motor de recomendaciones para generar recomendaciones de películas que se basan en películas que sus amigos han visto.

> [AZURE.NOTE] Los pasos de este documento requieren un clúster de Hadoop basado en Linux en HDInsight. Para obtener información sobre el uso de Mahout con un cliente basado en Windows, vea [Generar recomendaciones de películas mediante Apache Mahout con Hadoop basado en Windows en HDInsight](hdinsight-mahout.md).

##Requisitos previos

* Un clúster de Hadoop basado en Linux en HDInsight. Para obtener información sobre cómo crear uno, vea [Introducción al uso de Hadoop basado en Linux en HDInsight][getstarted].

##Control de versiones de Mahout

Para obtener más información sobre la versión de Mahout incluida con el clúster de HDInsight, consulte [Versiones de HDInsight y componentes de Hadoop](hdinsight-component-versioning.md).

> [AZURE.WARNING] Aunque es posible cargar una versión diferente de Mahout en el clúster de HDInsight, los componentes ofrecidos con HDInsight son totalmente compatibles. Además, el soporte técnico de Microsoft le ayudará a aislar y resolver problemas relacionados con estos componentes.
>
> Los componentes personalizados reciben soporte técnico comercialmente razonable para ayudarle a solucionar el problema. Esto podría resolver el problema o pedirle que forme parte de los canales disponibles para las tecnologías de código abierto donde se encuentra la más amplia experiencia para esa tecnología. Por ejemplo, hay diversos sitios de la comunidad que se pueden usar, como el [foro de MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/es-ES/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Además, los proyectos de Apache tienen sitios del proyecto en [http://apache.org](http://apache.org), por ejemplo, [Hadoop](http://hadoop.apache.org/) y [Spark](http://spark.apache.org/).

##<a name="recommendations"></a>Descripción de recomendaciones

Una de las funciones que proporciona Mahout es un motor de recomendaciones. Este motor acepta datos en formato de `userID`, `itemId` y `prefValue` (la preferencia de los usuarios por el elemento). Mahout puede realizar entonces análisis de ocurrencias conjuntas para determinar: _los usuarios que tienen predilección por un elemento también la tienen por estos otros elementos_. Mahout determinará los usuarios con preferencias de elementos similares, lo que se puede usar para realizar recomendaciones.

A continuación se muestra un ejemplo muy sencillo que usa películas:

* __Ocurrencia conjunta__: a José, Alicia y Roberto les gusta _La Guerra de las galaxias_, _El imperio contraataca_ y _El retorno del Jedi_. Mahout determina que a los usuarios que les gusta alguna de estas películas también les gustan las otras dos.

* __Ocurrencia conjunta__: a Roberto y Alicia también les gusta _La amenaza fantasma_, _El ataque de los clones_ y _La venganza de los Sith_. Mahout determina que a los usuarios que les gustan las tres películas anteriores también les gustan estas tres.

* __Recomendación basada en similitud__: como a José le gustan las tres primeras películas, Mahout examina películas que a otros usuarios con preferencias similares les han gustado, pero que José no ha visto (gustado/valorado). En este caso, Mahout recomendaría _La amenaza fantasma_, _El ataque de los clones_ y _La venganza de los Sith_.

##Carga de los datos

Para su comodidad, [GroupLens Research][movielens] proporciona calificaciones de películas en un formato compatible con Mahout. Siga estos pasos para descargar los datos y luego cárguelos en el almacenamiento predeterminado para el clúster:

1. Use SSH para conectarse al clúster de HDInsight basado en Linux. La dirección que se usará al conectarse es `CLUSTERNAME-ssh.azurehdinsight.net` y el puerto es `22`.

	Para obtener más información sobre el uso de SSH para conectarse a HDInsight, vea los siguientes documentos:

    * **Clientes de Linux, Unix y OS X**: vea [Conectarse a un clúster de HDInsight basado en Linux desde Linux, OS X o Unix](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)

    * **Clientes Windows**: vea [Conectarse a un clúster de HDInsight basados en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)

2. Descargue el archivo MovieLens 100k, que contiene 100.000 calificaciones de 1000 usuarios para 1700 películas.

        curl -O http://files.grouplens.org/datasets/movielens/ml-100k.zip

3. Extraiga el archivo con el comando siguiente:

        unzip ml-100k.zip

    De esta manera se extraerá el contenido a una nueva carpeta denominada **ml-100k**.

4. Use los siguientes comandos para copiar los datos al almacenamiento de HDInsight:

        cd ml-100k
        hdfs dfs -put u.data /example/data


    Los datos contenidos en este archivo tienen una estructura de `userID`, `movieID`, `userRating` y `timestamp`, que nos indica con que puntuación clasificó cada usuario una película. A continuación se muestra un ejemplo de los datos:


		196	242	3	881250949
		186	302	3	891717742
		22	377	1	878887116
		244	51	2	880606923
		166	346	1	886397596

##Ejecución del trabajo

Use el siguiente comando para ejecutar el trabajo de recomendación:

	mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /example/data/u.data -o /example/data/mahoutout --tempDir /temp/mahouttemp

> [AZURE.NOTE] El trabajo puede tardar varios minutos en completarse y puede ejecutar varios trabajos de MapReduce.

##Visualización de la salida

1. Cuando finalice el trabajo, use el siguiente comando para ver la salida generada.

		hdfs dfs -text /example/data/mahoutout/part-r-00000

	La salida aparecerá de la siguiente manera:

		1	[234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
		2	[282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
		3	[284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
		4	[690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

	La primera columna es `userID`. Los valores contenidos en "[" y "]" son `movieId`:`recommendationScore`.

2. Algunos de los otros datos incluidos en el directorio **ml-100k** se pueden usar para que los datos sean más descriptivos. En primer lugar, descargue los datos mediante el siguiente comando:

		hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt

	Esto copiará los datos de salida a un archivo denominado **recommendations.txt** del directorio actual.

3. Use el siguiente comando para crear un nuevo script de Python que buscará nombres de película para los datos en la salida de recomendaciones:

		nano show_recommendations.py

	Cuando se abra el editor, use lo siguiente como contenido del archivo:

        #!/usr/bin/env python
        
        import sys
        
        if len(sys.argv) != 5:
                print "Arguments: userId userDataFilename movieFilename recommendationFilename"
                sys.exit(1)
        
        userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]
        
        print "Reading Movies Descriptions"
        movieFile = open(movieFilename)
        movieById = {}
        for line in movieFile:
                tokens = line.split("|")
                movieById[tokens[0]] = tokens[1:]
        movieFile.close()
        
        print "Reading Rated Movies"
        userDataFile = open(userDataFilename)
        ratedMovieIds = []
        for line in userDataFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        ratedMovieIds.append((tokens[1],tokens[2]))
        userDataFile.close()
        
        print "Reading Recommendations"
        recommendationFile = open(recommendationFilename)
        recommendations = []
        for line in recommendationFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        movieIdAndScores = tokens[1].strip("[]\n").split(",")
                        recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
                        break
        recommendationFile.close()
        
        print "Rated Movies"
        print "------------------------"
        for movieId, rating in ratedMovieIds:
                print "%s, rating=%s" % (movieById[movieId][0], rating)
        print "------------------------"
        
        print "Recommended Movies"
        print "------------------------"
        for movieId, score in recommendations:
                print "%s, score=%s" % (movieById[movieId][0], score)
        print "------------------------"

	Presione **Ctrl-X**, **Y**, y finalmente **Entrar** para guardar los datos.

3. Utilice el siguiente comando para que el archivo sea ejecutable:

		chmod +x show_recommendations.py

4. Ejecute el script de Python. A continuación se supone que está en el directorio ml-100k, donde se encuentran los archivos `u.data` y `u.item`:

		./show_recommendations.py 4 u.data u.item recommendations.txt

	Examinará las recomendaciones generadas para el usuario con id 4.

	* El archivo **u.data** se usa para recuperar películas que el usuario ha calificado
	* El archivo **u.item** se usa para recuperar los nombres de las películas
	* El archivo **recommendations.txt** se usa para recuperar las recomendaciones de películas para este usuario

	La salida de este comando será similar a la siguiente:

		Reading Movies Descriptions
		Reading Rated Movies
		Reading Recommendations
		Rated Movies
		------------------------
		Mimic (1997), rating=3
		Ulee's Gold (1997), rating=5
		Incognito (1997), rating=5
		One Flew Over the Cuckoo's Nest (1975), rating=4
		Event Horizon (1997), rating=4
		Client, The (1994), rating=3
		Liar Liar (1997), rating=5
		Scream (1996), rating=4
		Star Wars (1977), rating=5
		Wedding Singer, The (1998), rating=5
		Starship Troopers (1997), rating=4
		Air Force One (1997), rating=5
		Conspiracy Theory (1997), rating=3
		Contact (1997), rating=5
		Indiana Jones and the Last Crusade (1989), rating=3
		Desperate Measures (1998), rating=5
		Seven (Se7en) (1995), rating=4
		Cop Land (1997), rating=5
		Lost Highway (1997), rating=5
		Assignment, The (1997), rating=5
		Blues Brothers 2000 (1998), rating=5
		Spawn (1997), rating=2
		Wonderland (1997), rating=5
		In & Out (1997), rating=5
		------------------------
		Recommended Movies
		------------------------
		Seven Years in Tibet (1997), score=5.0
		Indiana Jones and the Last Crusade (1989), score=5.0
		Jaws (1975), score=5.0
		Sense and Sensibility (1995), score=5.0
		Independence Day (ID4) (1996), score=5.0
		My Best Friend's Wedding (1997), score=5.0
		Jerry Maguire (1996), score=5.0
		Scream 2 (1997), score=5.0
		Time to Kill, A (1996), score=5.0
		Rock, The (1996), score=5.0
		------------------------

##Eliminar datos temporales

Los trabajos de Mahout no eliminan los datos temporales creados durante el procesamiento del trabajo. El parámetro `--tempDir` se especifica en el trabajo de ejemplo para aislar los archivos temporales en una ruta de acceso específica de forma que sea fácil eliminarlos. Para quitar los archivos temporales, use el siguiente comando:

	hdfs dfs -rm -f -r /temp/mahouttemp

> [AZURE.WARNING] Si desea volver a ejecutar el comando, también debe eliminar el directorio de salida. Use lo siguiente para eliminar este directorio:
>
> ```hdfs dfs -rm -f -r /example/data/mahoutout```

##Pasos siguientes

Ahora que ha aprendido a usar a Mahout, descubra otras formas de trabajar con datos en HDInsight:

* [Hive con HDInsight](hdinsight-use-hive.md)
* [Pig con HDInsight](hdinsight-use-pig.md)
* [MapReduce con HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 

<!---HONumber=AcomDC_0218_2016-->