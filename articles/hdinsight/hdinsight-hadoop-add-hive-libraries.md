<properties
pageTitle="Incorporación de bibliotecas de Hive durante la creación de clústeres de HDInsight | Azure"
description="Aprenda a agregar bibliotecas de Hive (archivos JAR) a un clúster de HDInsight durante la creación del clúster."
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

#Incorporación de bibliotecas de Hive durante la creación de clústeres de HDInsight

Este documento contiene información sobre el uso de una acción de script para cargar previamente bibliotecas durante la creación de un clúster, por lo que puede resultarle interesante si dispone de bibliotecas que utiliza con frecuencia con Hive en HDInsight. Con ello se posibilita que las bibliotecas estén disponibles globalmente en Hive (sin necesidad de usar [ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) para cargarlas).

##Cómo funciona

Cuando cree un clúster, también tiene la posibilidad de especificar opcionalmente una acción de script que ejecute un script en los nodos del clúster mientras estos se crean. El script de este documento acepta un único parámetro, que es una ubicación de WASB que contiene las bibliotecas (almacenadas como archivos JAR) que se cargarán previamente.

Durante la creación del clúster, el script enumera los archivos, los copia en el directorio `/usr/lib/customhivelibs/` de los nodos principal y de trabajo y luego los agrega a la propiedad `hive.aux.jars.path` en el archivo `core-site.xml`. En los clústeres basados en Linux, también actualiza el archivo `hive-env.sh` con la ubicación de los archivos.

> [AZURE.NOTE] El uso de las acciones de script de este artículo permite que las bibliotecas estén disponibles en las situaciones siguientes:
>
> * __HDInsight basado en Linux__: cuando se usa la __línea de comandos de Hive__, __WebHCat__ (Templeton) y __HiveServer2__.
> * __HDInsight basado en Windows__: cuando se usa la __línea de comandos de Hive__ y __WebHCat__ (Templeton).

##La secuencia de comandos

__Ubicación del script__

En los __clústeres basados en Linux__: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

En los __clústeres basados en Windows__: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

__Requisitos__

* Las scripts deben aplicarse tanto a los __nodos principales__ como a los __nodos de trabajo__.

* Los archivos JAR que se van a instalar deben almacenarse en el Almacenamiento de blobs de Azure en un __único contenedor__.

* La cuenta de almacenamiento que contiene la biblioteca de archivos JAR __debe__ vincularse al clúster de HDInsight durante la creación. Esto puede realizarse de dos maneras:

    * Mediante su ubicación en un contenedor de la cuenta de almacenamiento predeterminada para el clúster.
    
    * Mediante su ubicación en un contenedor de almacenamiento vinculado. Por ejemplo, en el Portal puede agregar almacenamiento adicional mediante __Configuración opcional__, __Cuentas de almacenamiento vinculadas__.

* La ruta de acceso de WASB al contenedor debe especificarse como un parámetro para la acción de script. Por ejemplo, si los archivos JAR se almacenan en un contenedor denominado __libs__ en una cuenta de almacenamiento denominada __mystorage__, el parámetro sería \_\___wasb://libs@mystorage.blob.core.windows.net/__.

    > [AZURE.NOTE] En este documento se supone que ha creado ya una cuenta de almacenamiento, contenedora de blobs, y ha cargado los archivos en ella.
    >
    > Si no ha creado una cuenta de almacenamiento, puede hacerlo a través del [Portal de Azure](https://portal.azure.com). A continuación, puede usar una utilidad como el [Explorador de almacenamiento de Azure](http://storageexplorer.com/) para crear un nuevo contenedor en la cuenta y cargar archivos en él.

##Creación de un clúster mediante el script

> [AZURE.NOTE] Al seguir estos pasos, se crea un nuevo clúster de HDInsight basado en Linux. Para crear un nuevo clúster basado en Windows, seleccione __Windows__ como sistema operativo del clúster en el momento de su creación y use el script de Windows (PowerShell) en lugar del script de Bash.
> 
> También puede usar Azure PowerShell o el SDK de .NET para HDInsight para crear un clúster mediante este script. Para obtener más información sobre el uso de estos métodos, consulte [Personalización de clústeres de HDInsight mediante acciones de script](hdinsight-hadoop-customize-cluster-linux.md).

1. Inicie el aprovisionamiento de un clúster siguiendo los pasos que se describen en [Aprovisionamiento de clústeres de HDInsight en Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), pero no complete la operación.

2. En la hoja **Configuración opcional**, seleccione **Acciones de script** y proporcione la información tal y como se muestra a continuación:

    * __NOMBRE__: escriba un nombre descriptivo para la acción de script.
    * __URI DE SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh
    * __PRINCIPAL__: active esta opción.
    * __TRABAJO__: active esta opción.
    * __ZOOKEEPER__: déjelo en blanco.
    * __PARÁMETROS__: escriba la dirección WASB que dirige al contenedor y la cuenta de almacenamiento que contiene los archivos JAR. Por ejemplo, \_\___wasb://libs@mystorage.blob.core.windows.net/__.

3. En la parte inferior de **Acciones de script**, use el botón **Seleccionar** para guardar la configuración.

4. En la hoja **Configuración opcional**, seleccione __Cuentas de almacenamiento vinculadas__ y luego el vínculo __Agregar una clave de almacenamiento__. Seleccione la cuenta de almacenamiento que contiene los archivos JAR y, a continuación, utilice los botones de __selección__ para guardar la configuración y volver a la hoja __Configuración opcional__.

5. Use el botón **Seleccionar** situado en la parte inferior de la hoja **Configuración opcional** para guardar la información de configuración opcional.

6. Continúe aprovisionando el clúster tal como se describe en [Aprovisionamiento de clústeres de HDInsight n Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

Una vez finalizada la creación del clúster, podrá utilizar los archivos JAR agregados a través de este script desde Hive sin tener que utilizar la instrucción `ADD JAR`.

##Pasos siguientes

En este documento ha aprendido a agregar bibliotecas de Hive contenidas en archivos JAR a un clúster de HDInsight durante la creación del clúster. Para obtener más información acerca del trabajo con Hive, consulte [Use Hive with HDInsight](hdinsight-use-hive.md) (Uso de Hive con HDInsight).

<!---HONumber=AcomDC_0218_2016-->