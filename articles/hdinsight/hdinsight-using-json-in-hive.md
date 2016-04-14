<properties
   pageTitle="Análisis y procesamiento de documentos JSON con Hive en HDInsight | Microsoft Azure"
   description="Aprenda a utilizar documentos JSON y analizarlos mediante Hive en HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/22/2015"
   ms.author="rashimg"/>


# Proceso y análisis de documentos JSON mediante Hive en HDInsight

Aprenda a procesar y a analizar archivos JSON mediante Hive en HDInsight. En el tutorial se usará el siguiente documento JSON.

	{
	    "StudentId": "trgfg-5454-fdfdg-4346",
	    "Grade": 7,
	    "StudentDetails": [
	        {
	            "FirstName": "Peggy",
	            "LastName": "Williams",
	            "YearJoined": 2012
	        }
	    ],
	    "StudentClassCollection": [
	        {
	            "ClassId": "89084343",
	            "ClassParticipation": "Satisfied",
	            "ClassParticipationRank": "High",
	            "Score": 93,
	            "PerformedActivity": false
	        },
	        {
	            "ClassId": "78547522",
	            "ClassParticipation": "NotSatisfied",
	            "ClassParticipationRank": "None",
	            "Score": 74,
	            "PerformedActivity": false
	        },
	        {
	            "ClassId": "78675563",
	            "ClassParticipation": "Satisfied",
	            "ClassParticipationRank": "Low",
	            "Score": 83,
	            "PerformedActivity": true
	        }
	    ]
	}

El archivo se encuentra en wasb://processjson@hditutorialdata.blob.core.windows.net/. Para obtener más información sobre cómo usar el almacenamiento de blobs de Azure con HDInsight, consulte [Uso del almacenamiento de blobs de Azure compatibles con HDFS con Hadoop en HDInsight](hdinsight-hadoop-use-blob-storage.md). Si lo desea, puede copiar el archivo en el contenedor predeterminado del clúster.

En este tutorial, utilizará la consola de Hive. Para obtener instrucciones sobre cómo abrir la consola de Hive, consulte [Uso de Hive con Hadoop en HDInsight con Escritorio remoto](hdinsight-hadoop-use-hive-remote-desktop.md).

##Acoplamiento de documentos JSON

Los métodos enumerados en la siguiente sección requieren que el documento JSON esté en una sola fila. Por lo tanto, debe acoplar el documento JSON a una cadena. Si el documento JSON ya está acoplado, puede omitir este paso e ir directamente a la sección siguiente sobre Análisis de los datos JSON.

	DROP TABLE IF EXISTS StudentsRaw;
	CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";
	
	DROP TABLE IF EXISTS StudentsOneLine;
	CREATE EXTERNAL TABLE StudentsOneLine
	(
	  json_body string
	)
	STORED AS TEXTFILE LOCATION '/json/students';
	
	INSERT OVERWRITE TABLE StudentsOneLine
	SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON 
	      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
	      GROUP BY INPUT__FILE__NAME;
	
	SELECT * FROM StudentsOneLine

El archivo sin formato de JSON se encuentra en ****wasb://processjson@hditutorialdata.blob.core.windows.net/**. La tabla de Hive *StudentsRaw* apunta al documento JSON sin formato y no acoplado.

La tabla de Hive *StudentsOneLine* almacenará los datos en el sistema de archivos predeterminado de HDInsight en la ruta de acceso */json/students/*.

La instrucción INSERT rellena la tabla StudentOneLine con los datos de JSON no acoplados.

La instrucción SELECT solo devolverá una fila.

Este es el resultado de la instrucción SELECT:

![Acoplamiento del documento JSON.][image-hdi-hivejson-flatten]

##Análisis de documentos JSON en Hive

Hive proporciona tres mecanismos distintos para ejecutar consultas en documentos JSON:

- usar la función definida por el usuario (UDF) GET\_JSON\_OBJECT
- usar la UDF JSON\_TUPLE
- usar el SerDe personalizado
- escribir la propia UDF con Python u otros lenguajes Consulte [este artículo][hdinsight-python] para obtener información sobre cómo ejecutar su propio código de Python con Hive. 

### Usar la UDF get\_json\_object
Hive proporciona una UDF integrada llamada [get json object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) que puede realizar consultas JSON en tiempo de ejecución. Este método toma dos argumentos: el nombre de la tabla y el nombre del método que tiene el documento JSON acoplado y el campo JSON que debe analizarse. Veamos un ejemplo para ver cómo funciona esta UDF.

Obtener el nombre y el apellido de cada estudiante

	SELECT 
	  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'), 
	  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName') 
	FROM StudentsOneLine;

Este es el resultado cuando se ejecuta esta consulta en la ventana de la consola.

![UDF get\_json\_object][image-hdi-hivejson-getjsonobject]

Existen algunas limitaciones de la UDF de get-json\_object.

- Como cada campo de la consulta requiere volver a analizar la consulta, afecta al rendimiento.
- GET\_JSON\_OBJECT() devuelve la representación de cadena de una matriz. Para convertir esto en una matriz de Hive, tendrá que utilizar expresiones regulares para reemplazar los corchetes '[' y ']' y, a continuación, llamar también a split para obtener la matriz.


Este es el motivo de que el sitio wiki de Hive recomiende el uso de json\_tuple.

### Use la UDF JSON\_TUPLE.

Otra UDF proporcionada por Hive se denomina [json\_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple) y es más eficaz que [get\_ json \_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Este método toma un conjunto de claves y una cadena JSON y devuelve una tupla de valores mediante una función. La siguiente consulta devuelve el identificador y el curso del estudiante del documento JSON:

    SELECT q1.StudentId, q1.Grade 
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

Salida de este script en la consola de Hive:

![UDF json\_tuple][image-hdi-hivejson-jsontuple]

JSON\_TUPLE usa la sintaxis [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) de Hive que permite que json\_tuple cree una tabla virtual aplicando la función UDT a cada fila de la tabla original. Los JSON complejos se vuelven demasiado difíciles de manejar debido al uso repetido de LATERAL VIEW. Además, JSON\_TUPLE no puede controlar los JSON anidados.


###Usar el SerDe personalizado

SerDe es la mejor opción para analizar documentos JSON anidados ya que le permite definir el esquema JSON y usar el esquema para analizar los documentos. En este tutorial, usará uno de los SerDe más conocidos desarrollado por [rcongiu](https://github.com/rcongiu).

**Para usar el SerDe personalizado:**

1. Instale el [Kit de desarrollo de Java SE 7u55 JDK 1.7.0\_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR). Si va a usar la implementación de Windows de HDInsight, elija la versión Windows x 64 del JDK.

	>[AZURE.WARNING]JDK 1.8 no funciona con este SerDe.

	Una vez completada la instalación, agregue una nueva variable de entorno de usuario:

	1. Abra **Ver la configuración avanzada del sistema** desde la pantalla de Windows.
	2. Haga clic en **Variables de entorno**.  
	3. Agregue una nueva variable de entorno **JAVA\_HOME** que apunte a **C:\\Program Files\\Java\\jdk1.7.0\_55** o a donde esté instalado el JDK.

	![Configuración de los valores correctos para JDK][image-hdi-hivejson-jdk]

2. Instalación de [Maven 3.3.1](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip)

	Agregue la carpeta Bin a la ruta de acceso; para ello, vaya a Panel de control--> Editar las variables del sistema para su cuenta Variables de entorno. La captura de pantalla siguiente muestra cómo hacerlo.

	![Configuración de Maven][image-hdi-hivejson-maven]

3. Clone el proyecto del sitio de github [Hive-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master). Para ello, haga clic en el botón "Download Zip" (Descargar archivo comprimido) como se muestra en la captura de pantalla siguiente.

	![Clonación del proyecto][image-hdi-hivejson-serde]

4: Vaya a la carpeta donde ha descargado este paquete y escriba "mvn.package". Esta acción debe crear los archivos jar necesarios que luego puede copiar en el clúster.

5: Vaya a la carpeta de destino bajo la carpeta raíz donde descargó el paquete. Cargue el archivo json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar en el nodo principal del clúster. Yo lo coloco normalmente bajo la carpeta de archivos binarios de Hive: C:\\apps\\dist\\hive-0.13.0.2.1.11.0-2316\\bin o algo similar.
 
6: En el símbolo del sistema de Hive, escriba "add jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar". Como en mi caso el archivo jar está en la carpeta C:\\apps\\dist\\hive-0.13.x\\bin, puedo agregarlo directamente con el nombre tal como se muestra a continuación:

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

	![Adding JAR to your project][image-hdi-hivejson-addjar]

Ahora, ya está listo para usar el SerDe para ejecutar consultas en el documento JSON.

La siguiente instrucción crea una tabla con un esquema definido

    DROP TABLE json_table;
    CREATE EXTERNAL TABLE json_table (
      StudentId string, 
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
    LOCATION '/json/students';

Mostrar el nombre y el apellido del alumno

    SELECT StudentDetails.FirstName, StudentDetails.LastName FROM json_table;

Este es el resultado de la consola de Hive.

![Consulta SerDe 1][image-hdi-hivejson-serde_query1]

Calcular la suma de puntuaciones del documento JSON

    SELECT SUM(scores)
    FROM json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;
       
La consulta anterior utiliza la UDF [lateral view explode](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) para expandir la matriz de puntuaciones de manera que se puedan sumar.

Este es el resultado de la consola de Hive.

![Consulta SerDe 2][image-hdi-hivejson-serde_query2]

Buscar en qué materias un alumno determinado ha obtenido más de 80 puntos, SELECT jt.StudentClassCollection.ClassId FROM json\_table jt lateral view explode(jt.StudentClassCollection.Score) collection as score where score > 80;
      
La consulta anterior devuelve una matriz de Hive a diferencia de get\_json\_object que devuelve una cadena.

![Consulta SerDe 3][image-hdi-hivejson-serde_query3]

Si desea eliminar documentos JSON con formato incorrecto, como se explica en la [página wiki](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) de este SerDe, puede hacerlo escribiendo el siguiente código:

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");




##Resumen
En conclusión, el tipo de operador JSON en Hive que elija depende de su escenario. Si tiene un documento JSON sencillo y solo tiene un campo por el que buscar, puede elegir usar la UDF de Hive get\_json\_object. Si tiene varias claves por las que buscar, puede utilizar json\_tuple. Si tiene un documento anidado, debe utilizar el SerDe de JSON.

Para ver otros artículos relacionados, consulte

- [Usar Hive y HiveQL con Hadoop en HDInsight para analizar un archivo log4j de Apache de muestra](hdinsight-use-hive.md)
- [Análisis de datos de retraso de vuelos con Hive en HDInsight](hdinsight-analyze-flight-delay-data.md)
- [Análisis de datos de Twitter con Hive en HDInsight](hdinsight-analyze-twitter-data.md)
- [Ejecución de un trabajo de Hadoop con DocumentDB y HDInsight](documentdb-run-hadoop-with-hdinsight.md)

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png
 

<!---HONumber=Oct15_HO3-->