<properties
    pageTitle="Desarrollo de acciones de script con HDInsight basado en Linux | Microsoft Azure"
    description="Obtenga información sobre cómo personalizar clústeres de HDInsight basados en Linux mediante la acción de script."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/05/2016"
    ms.author="larryfr"/>

# Desarrollo de la acción de script con HDInsight

Las acciones de script son una manera de personalizar los clústeres de HDInsight de Azure especificando opciones de configuración de clúster durante la instalación, o instalando servicios adicionales, herramientas u otro software en el clúster.

> [AZURE.NOTE] La información contenida en este documento es específica de los clústeres de HDInsight basados en Linux. Para obtener información sobre el uso de las acciones de script con clústeres basados en Windows, consulte [Desarrollo de la acción de script con HDInsight (Windows)](hdinsight-hadoop-script-actions.md).

## ¿Qué son las acciones de script?

Las acciones de script son scrpits de Bash que se ejecutan en los nodos de clúster durante el aprovisionamiento. Una acción de script se ejecuta como raíz y proporciona derechos de acceso completo a los nodos del clúster.

La acción de script puede usarse al aprovisionar un clúster mediante el __Portal de Azure__, __Azure PowerShell__ o __SDK .NET de HDInsight__.

Para obtener un tutorial sobre personalización de un clúster mediante acciones de script, consulte [Personalización de los clústeres de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Prácticas recomendadas para el desarrollo de scripts

Al desarrollar un script personalizado para un clúster de HDInsight, hay varios procedimientos recomendados que hay que tener en cuenta:

- [Concentrarse en la versión de Hadoop](#bPS1)
- [Proporcionar vínculos estables a los recursos de script](#bPS2)
- [Usar recursos compilados previamente](#bPS4)
- [Asegurarse de que el script de personalización del clúster es idempotente](#bPS3)
- [Asegurar una alta disponibilidad de la arquitectura de clúster](#bPS5)
- [Configurar los componentes personalizados para usar el almacenamiento de blobs de Azure](#bPS6)
- [Escribir información en STDOUT y STDERR](#bPS7)
- [Guardar los archivos como ASCII con el fin de línea LF](#bps8)

> [AZURE.IMPORTANT] Las acciones de script se tienen que completar dentro de un periodo de 60 minutos o superarán el tiempo de espera. Durante el aprovisionamiento del nodo, el script se ejecuta a la vez con otros procesos de instalación y de configuración. La competición por los recursos, como el ancho de banda de red o el tiempo de CPU puede ocasionar que el script tarde más en terminar que en el entorno de desarrollo.

### <a name="bPS1"></a>Concentrarse en la versión de Hadoop

Las distintas versiones de HDInsight tienen distintas versiones de componentes y servicios de Hadoop instalados. Si su script espera una versión específica de un servicio o componente, debe usar solamente el script con la versión de HDInsight que incluye los componentes necesarios. Puede encontrar información sobre las versiones de los componentes incluidos con HDInsight mediante el documento [Versiones de HDInsight y componentes](hdinsight-component-versioning.md).

### <a name="bPS2"></a>Proporcionar vínculos estables a los recursos de script

Los usuarios deben asegurarse de que todos los scripts y recursos que se usan en los scripts permanecen disponibles durante la vigencia del clúster y de que las versiones de estos archivos no cambian para la duración. Estos recursos son necesarios si se recrean las imágenes de los nodos del clúster.

La práctica recomendada es descargar y archivar todo el contenido de una cuenta de Almacenamiento de Azure en su suscripción.

> [AZURE.IMPORTANT] La cuenta de almacenamiento usada tiene que ser la cuenta de almacenamiento predeterminada del clúster o un contenedor público de solo lectura en cualquier otra cuenta de almacenamiento.

Por ejemplo, los ejemplos proporcionados por Microsoft se almacenan en la cuenta de almacenamiento [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/), que es un contenedor público, de solo lectura mantenido por el equipo de HDInsight.

### <a name="bPS4"></a>Uso de recursos compilados previamente

Para minimizar el tiempo necesario para ejecutar el script, evite las operaciones que compilan los recursos a partir del código fuente. En su lugar, compile previamente los recursos y almacene la versión binaria en el almacenamiento de blobs de Azure para que se pueda descargar rápidamente en el clúster desde el script.

### <a name="bPS3"></a>Asegurarse de que el script de personalización del clúster es idempotente

Debe esperar que los nodos de un clúster de HDInsight renueven la imagen durante la vigencia del clúster y que el script de personalización de clúster se usará cuando esto pasa. El script tiene que estar diseñado para ser idempotente en el sentido de que tras la recreación de imagen, el script debe asegurarse de que el clúster se devuelve al mismo estado cada vez que se ejecuta.

Por ejemplo, si un script personalizado instaló una aplicación en /usr/local/bin en su primera ejecución, en cada ejecución posterior el script debe comprobar si la aplicación ya existe en la ubicación /usr/local/bin antes de continuar con otros pasos del script.

### <a name="bPS5"></a>Asegurar una alta disponibilidad de la arquitectura de clúster

Los clústeres de HDInsight basados en Linux proporcionan dos nodos principales que están activos dentro del clúster y las acciones de script se ejecutan para ambos nodos. Si los componentes que instala esperan un único nodo principal, tiene que diseñar un script que instale el componente en uno de los dos nodos principales del clúster.

> [AZURE.IMPORTANT] Los servicios predeterminados instalados como parte de HDInsight están diseñados para conmutar por error entre los dos nodos principales según sea necesario, pero esta funcionalidad no se extiende a componentes personalizados instalados a través de las acciones de script. Si necesita que los componentes instalados a través de una acción de script tengan una alta disponibilidad, tiene que implementar su propio mecanismo de conmutación por error que use los dos nodos principales disponibles.

### <a name="bPS6"></a>Configurar los componentes personalizados para usar el almacenamiento de blobs de Azure

Los componentes que instaló en el clúster podrían tener una configuración predeterminada que usa el almacenamiento Sistema de archivos distribuido de Hadoop (HDFS). En una recreación de imagen del clúster, el sistema de archivos HDFS se formatea y todos los datos almacenados allí se perderán. Debe cambiar la configuración para usar el almacenamiento de blobs de Azure (WASB) en su lugar, ya que este es el almacenamiento predeterminado para el clúster y se conserva incluso cuando se elimina el clúster.

Por ejemplo, lo siguiente copia el archivo giraph-examples.jar del sistema de archivos local a WASB:

    hadoop fs -copyFromLocal /usr/hdp/current/giraph/giraph-examples.jar /example/jars/

### <a name="bPS7"></a>Escribir información en STDOUT y STDERR

La información escrita en STDOUT y STDERR se registra y se puede ver después de aprovisionar el clúster mediante la interfaz de usuario web de Ambari.

La mayoría de utilidades y paquetes de instalación ya escriben la información en STDOUT y STDERR, pero puede que desee agregar un registro adicional. Para enviar texto a STDOUT use `echo`. Por ejemplo:

        echo "Getting ready to install Foo"

De forma predeterminada, `echo` enviará la cadena a STDOUT. Para dirigirla a STDERR, agregue `>&2` antes de `echo`. Por ejemplo:

        >&2 echo "An error occured installing Foo"

Esto redirige la información que se envía a STDOUT (1, que es el predeterminado, por lo que no se muestran aquí,) a STDERR (2). Para obtener más información sobre la redirección de E/S, consulte [http://www.tldp.org/LDP/abs/html/io-redirection.html](http://www.tldp.org/LDP/abs/html/io-redirection.html).

Para obtener más información sobre la visualización de información registrada por las acciones de script, consulte [Personalización de los clústeres de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting).

###<a name="bps8"></a>Guardar los archivos como ASCII con el fin de línea LF

Los scripts de Bash deben almacenarse con formato ASCII, con líneas terminadas con LF. Si los archivos se almacenan como UTF-8, que puede incluir una marca BOM al principio del archivo, o con fin de línea de CRLF, que es común para los editores de Windows, a continuación el script dará errores similares al siguiente:

    $'\r': command not found
    line 1: #!/usr/bin/env: No such file or directory

## <a name="helpermethods"></a>Métodos auxiliares para scripts personalizados

Los métodos auxiliares de la acción de script son utilidades que puede usar al escribir scripts personalizados. Se definen en [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh), y se pueden incluir en los scripts mediante:

    # Import the helper method module.
    wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh

Esto hace que las siguientes aplicaciones auxiliares estén disponibles para su uso en el script:

| Uso de la aplicación auxiliar | Descripción |
| ------------ | ----------- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` | Descarga un archivo de la dirección URL de origen en la ruta de acceso de archivo especificada. De forma predeterminada, no sobrescribirá un archivo existente. |
| `untar_file TARFILE DESTDIR` | Extrae un archivo tar (mediante `-xf`,) en el directorio de destino. |
| `test_is_headnode` | Si se ejecutaba en un nodo principal del clúster, devuelve 1; en caso contrario, es 0. |
| `test_is_datanode` | Si el nodo actual es un nodo de datos (trabajo), devuelve un 1; en caso contrario, es 0. |
| `test_is_first_datanode` | Si el nodo actual es el primer nodo de datos (trabajo), (llamado workernode0), devuelve un 1; en caso contrario, es 0. |

## <a name="commonusage"></a>Patrones de uso común

Esta sección proporciona instrucciones sobre cómo implementar algunos de los patrones de uso común que podría encontrar mientras escribe su propio script personalizado.

### Paso de parámetros a un script

En algunos casos, un script puede requerir parámetros. Por ejemplo, puede que necesite la contraseña de administrador para el clúster con el fin de recuperar información de la API de REST de Ambari.

Los parámetros que se pasan al script se conocen como _parámetros posicionales_, y se asignan a `$1` para el primer parámetro, `$2` para el segundo y así sucesivamente. `$0` contiene el nombre del script.

Los valores que se pasan a un script como parámetros se deben encerrar entre comillas simples (') para que el valor pasado se trate como un valor literal y no se un tratamiento especial para incluir caracteres como '!'.

### Establecimiento de variables de entorno

Establecer una variable de entorno se realiza de la forma siguiente:

    VARIABLENAME=value

Donde VARIABLENAME es el nombre de la variable. Para obtener acceso a la variable una vez hecho esto, use `$VARIABLENAME`. Por ejemplo, para asignar un valor proporcionado por un parámetro posicional como una variable de entorno denominada PASSWORD, use lo siguiente:

    PASSWORD=$1

Para el acceso posterior a la información puede usar `$PASSWORD`.

Las variables de entorno establecidas dentro del script solo existen en el ámbito del script. En algunos casos, puede que necesite agregar variables de entorno de todo el sistema que se conservarán una vez finalizado el script. Normalmente, esto es para que los usuarios que se conectan al clúster a través de SSH puedan usar los componentes instalados por el script. Para ello agregue la variable de entorno a `/etc/environment`. Por ejemplo, lo siguiente agrega __HADOOP\_CONF\_DIR__:

    echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment

### Acceso a ubicaciones donde se almacenan los scripts personalizados

Los scripts usados para personalizar un clúster tienen que encontrarse en la cuenta de almacenamiento predeterminada del clúster o, si se encuentran en otra cuenta de almacenamiento, en un contenedor público de solo lectura. Si el script obtiene acceso a recursos ubicados en cualquier otra parte, estos recursos deben encontrarse también en una ubicación a la que se pueda tener acceso de manera pública (al menos, con acceso público de solo lectura). Por ejemplo puede querer descargar un archivo en el clúster con `download_file`.

Almacenar el archivo en una cuenta de Almacenamiento de Azure que sea accesible al clúster (por ejemplo, la cuenta de almacenamiento predeterminada), proporcionará un acceso rápido, ya que este almacenamiento está dentro de la red de Azure.

## <a name="deployScript"></a>Lista de comprobación para implementar una acción de script

Estos son los pasos que se llevaron a cabo al prepararse para implementar estos scripts:

- Coloque los archivos que contengan los scripts personalizados en un lugar que sea accesible por los nodos del clúster durante la implementación. Este puede ser la cuenta de almacenamiento predeterminada o alguna de las cuentas de almacenamiento adicionales especificadas en el momento de la implementación del clúster, o bien, cualquier otro contenedor de almacenamiento con acceso público.

- Agregue comprobaciones en los scripts para asegurarse de que se ejecutan de manera idempotente, para que el script se pueda ejecutar varias veces en el mismo nodo.

- Use un directorio de archivo temporal /tmp para conservar los archivos descargados que usan los scripts y, a continuación, y realice una limpieza una vez que se ejecuten los scripts.

- En el caso de que los archivos de configuración de servicio de Hadoop o la configuración en el nivel de SO hayan cambiado, es posible que desee reiniciar los servicios de HDInsight para que puedan escoger cualquier configuración en el nivel de SO, tal como las variables de entorno definidas en los scripts.

## <a name="runScriptAction"></a>Ejecución de una acción de script

Puede usar las acciones de script para personalizar clústeres de HDInsight mediante el Portal de Azure, Azure PowerShell o el SDK de .NET de HDInsight. Para obtener instrucciones, consulte [Uso de acción de script](hdinsight-hadoop-customize-cluster-linux.md#howto).

## <a name="sampleScripts"></a>Ejemplos de scripts personalizados

Microsoft proporciona scripts de ejemplo para instalar los componentes en un clúster de HDInsight. Los scripts de muestra e instrucciones sobre cómo utilizarlos están disponibles en los vínculos siguientes:

- [Instalación y uso de Hue en clústeres de HDInsight](hdinsight-hadoop-hue-linux.md)
- [Instalación y uso de Spark en clústeres de HDInsight](hdinsight-hadoop-spark-install-linux.md)
- [Instalación y uso de R en clústeres de Hadoop para HDInsight](hdinsight-hadoop-r-scripts-linux.md)
- [Instalación y uso de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install-linux.md)
- [Instalación y uso de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install-linux.md)  

> [AZURE.NOTE] Los documentos con enlaces anteriores son específicos de los clústeres de HDInsight basados en Linux. Para un script que funcione con HDInsight basado en Windows, consulte [Desarrollo de la acción de script con HDInsight (Windows)](hdinsight-hadoop-script-actions.md) o use los vínculos disponibles en la parte superior de cada artículo.

##Solución de problemas

Estos son errores que pueden producirse al usar los scripts desarrollados:

__Error__: `$'\r': command not found`. A veces seguido de `syntax error: unexpected end of file`.

_Causa_: este error se produce si las líneas en un script terminan con CRLF. Los sistemas UNIX esperan solo LF como final de la línea.

Este problema suele producirse cuando se crea el script en un entorno Windows, ya que CRLF es una línea común final para muchos editores de texto en Windows.

_Resolución_: si es una opción en el editor de texto, seleccione formato UNIX o LF para el final de la línea. También puede usar los siguientes comandos en un sistema UNIX para cambiar un CRLF por un LF:

> [AZURE.NOTE] Los siguientes comandos son aproximadamente equivalentes en que deben cambiar los finales de línea CRLF a LF. Seleccione uno en función de las utilidades disponibles en el sistema.

| Comando | Notas |
| ------- | ----- |
| `unix2dos -b INFILE` | Se realizará una copia de seguridad del archivo original con una extensión .BAK |
| `tr -d '\r' < INFILE > OUTFILE` | OUTFILE contendrá una versión con finales solo LF |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Esto modificará el archivo directamente sin crear un nuevo archivo |
| ```sed 's/$'"/`echo \\r`/" INFILE > OUTFILE``` | OUTFILE contendrá una versión con finales solo LF.

__Error__: `line 1: #!/usr/bin/env: No such file or directory`.

_Causa_: este error se produce cuando el script se guarda como UTF-8 con una marca de orden de bytes (BOM).

_Resolución_: guarde el archivo como ASCII o UTF-8 sin una marca BOM. También puede usar el siguiente comando en un sistema Linux o UNIX para crear un archivo nuevo sin marca BOM:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Para el comando anterior, reemplace __INFILE__ por el archivo que contiene la marca BOM. __OUTFILE__ tiene que ser un nombre de archivo nuevo, que contendrá el script sin la marca BOM.

## <a name="seeAlso"></a>Otras referencias

[Personalizar los clústeres de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster-linux.md)

<!---HONumber=AcomDC_0211_2016-->