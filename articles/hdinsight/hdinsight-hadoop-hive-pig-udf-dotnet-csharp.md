<properties
	pageTitle="Uso de C# con Hive y Pig en Hadoop en HDInsight | Microsoft Azure"
	description="Vea cómo usar funciones definidas por el usuario de C# con el streaming de Hive y Pig en HDInsight de Azure."
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
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="larryfr"/>


#Usar funciones definidas por el usuario de C# con el streaming de Hive y Pig en Hadoop de HDInsight

Hive y Pig resultan excelentes para trabajar con datos en Azure HDInsight, pero en ocasiones se necesita un lenguaje con una finalidad más general. Hive y Pig permiten efectuar llamadas a código externo a través de funciones definidas por el usuario (UDF) o de streaming.

Con este documento, aprenderá a usar C# con Hive y Pig.

##Requisitos previos

* Windows 7, Windows 8 o Windows 8.1

* Visual Studio con las siguientes versiones:

	* Visual Studio 2012 Professional/Premium/Ultimate con [actualización 4](http://www.microsoft.com/download/details.aspx?id=39305)

	* Visual Studio 2013 Comunidad/Professional/Premium/Ultimate con [actualización 4](https://www.microsoft.com/download/details.aspx?id=44921)

	* Visual Studio 2015 en versión de vista previa

* Hadoop en un clúster de HDInsight, consulte [Aprovisionamiento de un clúster de HDInsight](hdinsight-provision-clusters.md) para conocer los pasos que le permitirán crear un clúster.

* Herramientas de Hadoop para Visual Studio Consulte [Introducción al uso de herramientas de Hadoop de HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) para conocer los pasos que le permitirán instalar y configurar las herramientas.

##.NET en HDInsight

Common Language Runtime (CLR) de .NET y los marcos se instalan de forma predeterminada en los clústeres de HDInsight basados en Windows. Esto permite utilizar las aplicaciones de C# con el streaming de Hive y Pig (los datos se pasan entre Hive/Pig y la aplicación de C# a través de stdin y stdout).

Actualmente, no se pueden ejecutar aplicaciones de .NET Framework en clústeres de HDInsight basados en Linux.

##.NET y streaming

El streaming obliga a Hive y Pig a pasar datos a una aplicación externa a través de stdout y a recibir los resultados a través de stdin. En el caso de las aplicaciones de C#, esto se consigue más fácilmente a través de `Console.ReadLine()` y `Console.WriteLine()`.

Dado que Hive y Pig deben invocar la aplicación en el tiempo de ejecución, hay que utilizar la plantilla de **aplicación de consola** en los proyectos de C#.

##Hive y C&#35;

###Creación de un proyecto de C#

1. Abra Visual Studio y cree una nueva solución. Para el tipo de proyecto, elija **Aplicación de consola**. Asigne al nuevo proyecto el nombre **HiveCSharp**.

2. Reemplace el contenido de **Program.cs** por lo siguiente:

        using System;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;

        namespace HiveCSharp
        {
            class Program
            {
                static void Main(string[] args)
                {
                    string line;
                    // Read stdin in a loop
                    while ((line = Console.ReadLine()) != null)
                    {
                        // Parse the string, trimming line feeds
                        // and splitting fields at tabs
                        line = line.TrimEnd('\n');
                        string[] field = line.Split('\t');
                        string phoneLabel = field[1] + ' ' + field[2];
                        // Emit new data to stdout, delimited by tabs
                        Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                    }
                }
                /// <summary>
                /// Returns an MD5 hash for the given string
                /// </summary>
                /// <param name="input">string value</param>
                /// <returns>an MD5 hash</returns>
                static string GetMD5Hash(string input)
                {
                    // Step 1, calculate MD5 hash from input
                    MD5 md5 = System.Security.Cryptography.MD5.Create();
                    byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                    byte[] hash = md5.ComputeHash(inputBytes);

                    // Step 2, convert byte array to hex string
                    StringBuilder sb = new StringBuilder();
                    for (int i = 0; i < hash.Length; i++)
                    {
                        sb.Append(hash[i].ToString("x2"));
                    }
                    return sb.ToString();
                }
            }
        }

3. Compile el proyecto.

###Carga en el almacenamiento

1. En Visual Studio, abra el **Explorador de servidores**.

3. Expanda **Azure** y, a continuación, haga lo propio con **HDInsight**.

4. Si se le solicitan, escriba sus credenciales de suscripción de Azure y, a continuación, haga clic en **Iniciar sesión**.

5. Expanda el clúster de HDInsight en el que desea implementar esta aplicación y, a continuación, expanda **Cuenta de almacenamiento predeterminada**.

	![Explorador de servidores en el que se muestra la cuenta de almacenamiento para el clúster](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

6. Haga doble clic en **Contenedor predeterminado** para el clúster. Se abrirá una nueva ventana en la que se muestra el contenido del contenedor predeterminado.

7. Haga clic en el icono de carga y, a continuación, vaya a la carpeta **bin\\debug** del proyecto **HiveCSharp**. Por último, elija el archivo **HiveCSharp.exe** y haga clic en **Aceptar**.

	![icono para cargar](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/upload.png)

8. Una vez que haya finalizado la carga, podrá usar la aplicación desde una consulta de Hive.

###Consulta de Hive

1. En Visual Studio, abra el **Explorador de servidores**.

2. Expanda **Azure** y, a continuación, haga lo propio con **HDInsight**.

5. Haga clic con el botón derecho en el clúster en el que ha implementado la aplicación **HiveCSharp** y, a continuación, elija **Escribir una consulta de Hive**.

6. Utilice lo siguiente para la consulta de Hive:

		add file wasb:///HiveCSharp.exe;

		SELECT TRANSFORM (clientid, devicemake, devicemodel)
		USING 'HiveCSharp.exe' AS
		(clientid string, phoneLabel string, phoneHash string)
		FROM hivesampletable
		ORDER BY clientid LIMIT 50;

    Este comando selecciona los campos `clientid`, `devicemake` y `devicemodel` de `hivesampletable`, y los pasa a la aplicación HiveCSharp.exe. La consulta espera que la aplicación devuelva tres campos, que están almacenados como `clientid`, `phoneLabel` y `phoneHash`. La consulta también espera que HiveCSharp.exe esté en la raíz del contenedor de almacenamiento predeterminado (`add file wasb:///HiveCSharp.exe`).

5. Haga clic en **Enviar** para enviar el trabajo al clúster de HDInsight. Se abrirá la ventana **Resumen del trabajo de Hive**.

6. Haga clic en **Actualizar** para actualizar el resumen hasta que el valor de **Estado del trabajo** cambie a **Completado**. Para ver la salida del trabajo, haga clic en **Salida de trabajo**.

##Pig y C&#35;

###Creación de un proyecto de C#

1. Abra Visual Studio y cree una nueva solución. Para el tipo de proyecto, elija **Aplicación de consola**. Asigne al nuevo proyecto el nombre **PigUDF**.

2. Reemplace el contenido del archivo **Program.cs** por el código siguiente:

		using System;

		namespace PigUDF
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
		            string line;
				    // Read stdin in a loop
				    while ((line = Console.ReadLine()) != null)
		            {
		                // Fix formatting on lines that begin with an exception
		                if(line.StartsWith("java.lang.Exception"))
		                {
		                    // Trim the error info off the beginning and add a note to the end of the line
		                    line = line.Remove(0, 21) + " - java.lang.Exception";
		                }
		                // Split the fields apart at tab characters
		                string[] field = line.Split('\t');
		                // Put fields back together for writing
		                Console.WriteLine(String.Join("\t",field));
		            }
		        }
		    }
		}

	Esta aplicación analizará las líneas que se envían desde Pig y cambiará el formato aquellas que comiencen por `java.lang.Exception`.

3. Guarde **Program.cs** y, a continuación, compile el proyecto.

###Cargue la aplicación.

1. El streaming de Pig espera que la aplicación sea local en el sistema de archivos del clúster. Habilite el Escritorio remoto para el clúster de HDInsight y conéctese a él siguiendo las instrucciones dadas en [Conexión a los clústeres de HDInsight con RDP](hdinsight-administer-use-management-portal.md#rdp).

2. Una vez conectado, copie **PigUDF.exe** desde el directorio **bin/debug** para el proyecto PigUDF en el equipo local y péguelo en el directorio **% PIG\_HOME %** del clúster.

###Uso de la aplicación desde Pig Latin

1. Desde la sesión de escritorio remoto, use el icono de **línea de comandos de Hadoop** del escritorio para iniciar la línea de comandos de Hadoop.

2. Utilice lo siguiente para iniciar la línea de comandos de Pig:

		cd %PIG_HOME%
		bin\pig

	Aparecerá un símbolo del sistema de `grunt>`.

3. Escriba lo siguiente para ejecutar un trabajo de Pig simple mediante la aplicación de .NET Framework:

		DEFINE streamer `pigudf.exe` SHIP('pigudf.exe');
		LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
		LOG = FILTER LOGS by LINE is not null;
		DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
		DUMP DETAILS;

	La instrucción `DEFINE` crea un alias de `streamer` para las aplicaciones de pigudf.exe, y `SHIP` lo distribuye entre los nodos del clúster. Más adelante, `streamer` se usa con el operador `STREAM` para procesar las líneas individuales incluidas en el registro y devolver los datos como una serie de columnas.

> [AZURE.NOTE] El nombre de la aplicación que se usa para el streaming debe estar encerrado entre caracteres ` (acento grave) cuando se usa como alias, y entre caracteres ' (comilla sencilla) cuando se utiliza con `SHIP`.

3. Después de escribir la última línea, debe iniciarse el trabajo. Finalmente, se devolverán unos resultados similares a los siguientes:

		(2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
		(2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
		(2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
		(2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
		(2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

##Resumen

En este documento, ha aprendido a utilizar una aplicación de .NET Framework desde Hive y Pig en HDInsight. Si desea obtener información sobre cómo utilizar Python con Hive y Pig, consulte [Uso de Python con Hive y Pig en HDInsight](hdinsight-python.md).

Para conocer otras formas de usar Pig y Hive y para obtener información acerca del uso de MapReduce, consulte lo siguiente:

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)

* [Uso de Pig con HDInsight](hdinsight-use-pig.md)

* [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0211_2016-->