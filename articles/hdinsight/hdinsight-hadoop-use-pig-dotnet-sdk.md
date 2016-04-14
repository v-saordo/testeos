<properties
   pageTitle="Uso de Pig con Hadoop con .NET en HDInsight | Microsoft Azure"
   description="Aprenda a usar el SDK de .NET de Hadoop para enviar trabajos de Pig a Hadoop en HDInsight."
   services="hdinsight"
   documentationCenter=".net"
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
   ms.date="02/05/2016"
   ms.author="larryfr"/>

#Ejecución de trabajos de Pig con el SDK de .NET para Hadoop en HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Este documento proporciona un ejemplo de uso del SDK de .NET para Hadoop para enviar trabajos de Pig a un Hadoop en clúster de HDInsight.

El SDK .NET de HDInsight proporciona bibliotecas de cliente .NET que facilitan el trabajo con los clústeres de HDInsight en .NET. Pig le permite crear operaciones de MapReduce al modelar una serie de transformaciones de datos. Aprenderá a utilizar una aplicación básica de C# para enviar un trabajo de Pig para un clúster de HDInsight.

[AZURE.INCLUDE [azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Ejecución de trabajos de Pig con el SDK de .NET para Hadoop en HDInsight](hdinsight-hadoop-use-pig-dotnet-sdk-v1.md)

##<a id="prereq"></a>Requisitos previos

Necesitará lo siguiente para completar los pasos de este artículo.

* Un clúster de HDInsight de Azure (Hadoop en HDInsight) (Windows o Linux)

* Visual Studio 2012, 2013 o 2015

##<a id="certificate"></a>Creación de un certificado de administración

Para autenticar la aplicación a HDInsight de Azure, debe crear un certificado autofirmado, instalarlo en su estación de trabajo de desarrollo y también cargarlo en su suscripción de Azure.

Para obtener instrucciones sobre cómo hacerlo, vea [Creación de un certificado autofirmado](http://go.microsoft.com/fwlink/?LinkId=511138).

> [AZURE.NOTE] Al crear el certificado, asegúrese de tener en cuenta el nombre descriptivo que utilice, ya que lo utilizará más adelante.

##<a id="subscriptionid"></a>Búsqueda del identificador de suscripción

Cada suscripción de Azure se identifica por un valor GUID, conocido como el identificador de suscripción. Utilice los pasos siguientes para encontrar este valor.

1. Visite el [portal de Azure] [portal de vista previa].

2. En la barra de la izquierda del portal, seleccione __EXAMINAR TODO__ y después seleccione __Suscripciones__ en la hoja __Examinar__.

3. En la información que se presenta en la hoja __Suscripciones__, busque la suscripción que desea usar y anote el valor de la columna **Id. de suscripción**.

Guarde el identificador de suscripción, pues se utilizará más adelante.

##<a id="create"></a>Creación de la aplicación

1. Apertura de Visual Studio 2012 o 2013
2. En el menú **Archivo**, seleccione **Nuevo** y, a continuación, seleccione **Proyecto**.
3. Para el nuevo proyecto, escriba o seleccione los valores siguientes.

	<table>
<tr>
<th>Propiedad</th>
<th>Valor</th>
</tr>
<tr>
<th>Categoría</th>
<th>Plantillas/Visual C#/Windows</th>
</tr>
<tr>
<th>Plantilla</th>
<th>Aplicación de consola</th>
</tr>
<tr>
<th>Nombre</th>
<th>SubmitPigJob</th>
</tr>
</table>
4. Haga clic en **Aceptar** para crear el proyecto.
5. En el menú **Herramientas**, seleccione **Administrador de paquetes de biblioteca** o **Administrador de paquetes de Nuget** y, a continuación, seleccione **Consola del administrador de paquetes**.
6. Ejecute el siguiente comando en la consola para instalar los paquetes de SDK de .NET.

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

7. En el Explorador de soluciones, haga doble clic en **Program.cs** para abrirlo. Reemplace el código existente por lo siguiente.

        using System;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
        
        namespace HDInsightSubmitPigJobsDotNet
        {
            class Program
            {
                static void Main(string[] args)
                {
                    var ExistingClusterName = "<HDInsightClusterName>";
                    var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                    var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
                    var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
        
                    // The Pig Latin statements to run
                    string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
                        "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
                        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
                        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
                        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
                        "RESULT = order FREQUENCIES by COUNT desc;" +
                        "DUMP RESULT;";
        
        
                    HDInsightJobManagementClient _hdiJobManagementClient;
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
        
                    // Define the Pig job
                    var parameters = new PigJobSubmissionParameters()
                    {
                        UserName = ExistingClusterUsername,
                        Query = queryString,
                    };
        
                    System.Console.WriteLine("Submitting the Pig job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                    Console.WriteLine("Press ENTER to continue ...");
                    Console.ReadLine();
                }
            }
        }

7. Presione **F5** para iniciar la aplicación.
8. Presione **ENTRAR** para salir de la aplicación.

##<a id="summary"></a>Resumen

Como puede observar, el SDK de .NET para Hadoop le permite crear aplicaciones .NET que envíen trabajos de Pig a un clúster de HDInsight, supervisar el estado del trabajo y recuperar el resultado.

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre Pig en HDInsight.

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

Para obtener información sobre otras maneras en que puede trabajar con Hadoop en HDInsight.

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

* [Uso de MapReduce con Hadoop en HDInsight ](hdinsight-use-mapreduce.md) 
[portal de vista previa]: https://portal.azure.com/

<!---HONumber=AcomDC_0211_2016-->