<properties
	pageTitle="Uso de R en HDInsight para personalizar clústeres | Microsoft Azure"
	description="Obtenga información sobre cómo instalar R mediante la acción de script y usar R en clústeres de HDInsight."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/14/2016"
	ms.author="jgao"/>

# Instalación y uso de R en clústeres de Hadoop para HDInsight


Obtenga información sobre cómo personalizar un clúster de HDInsight basado en Windows con R mediante la acción de script, y cómo usar R en clústeres de HDInsight. Para obtener información sobre el uso de R con un clúster basado en Linux, consulte [Instalación y uso de R en clústeres de Hadoop para HDinsight (Linux)](hdinsight-hadoop-r-scripts-linux.md)
 
Puede instalar R en cualquier tipo de clúster (Hadoop, Storm, HBase, Spark) en HDInsight de Azure mediante la *acción de script*. Hay un script de ejemplo para instalar R en un clúster de HDInsight disponible desde un blob de almacenamiento de Azure de solo lectura en [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

**Artículos relacionados**

- [Instalación y uso de R en clústeres de Hadoop para HDInsight (Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [Creación de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md): información general sobre la creación de clústeres de HDInsight.
- [Personalización de un clúster de HDInsight mediante la acción de script][hdinsight-cluster-customize]\: información general sobre la personalización de clústeres de HDInsight mediante la acción de script.
- [Desarrollo de la acción de script con HDInsight](hdinsight-hadoop-script-actions.md)

## ¿Qué es R?

El <a href="http://www.r-project.org/" target="_blank">proyecto R para el cálculo estadístico</a> es un entorno y lenguaje de código abierto para el cálculo estadístico. R proporciona cientos de de funciones estadísticas integradas y su propio lenguaje de programación que combina aspectos de la programación funcional y orientada a objetos. También proporciona amplias capacidades gráficas. R es el entorno de programación preferido para los científicos y los estadísticos más profesionales de una amplia variedad de campos.

R es compatible con el almacenamiento de blobs de Azure (WASB) para que los datos que se almacenan allí se puedan procesar mediante R en HDInsight.

## Instalar R

Un [script de ejemplo](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) para instalar R en un clúster de HDInsight se encuentra disponible desde un blob de solo lectura en almacenamiento de Azure. Esta sección proporciona instrucciones sobre cómo usar el script de ejemplo al crear el clúster mediante el Portal de Azure.

> [AZURE.NOTE] El script de ejemplo se introdujo con el clúster de HDInsight versión 3.1. Para obtener más información acerca de las versiones de clústeres de HDInsight, consulte las [versiones de clústeres de HDInsight](../hdinsight-component-versioning/).

1. Cuando aprovisione un clúster de HDInsight desde el Portal, haga clic en **Configuración opcional** y luego en **Acciones de script**.
2. En la página **Acciones de script**, escriba los siguientes valores:

	![Uso de la acción de script para personalizar un clúster](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "Uso de la acción de script para personalizar un clúster")

	<table border='1'>
	<tr><th>Propiedad</th><th>Valor</th></tr>
	<tr><td>Nombre</td>
		<td>Especifique un nombre para la acción de script, por ejemplo, <b>Instalar R</b>.</td></tr>
	<tr><td>URI de script</td>
		<td>Especifique el URI al script que se invoca para personalizar el clúster, por ejemplo, <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
	<tr><td>Tipo de nodo</td>
		<td>Especifique los nodos en los que se ejecuta el script de personalización. Puede elegir <b>Todos los nodos</b>, <b>Solo los nodos principales</b> o <b>Solo los nodos de trabajo</b>.
	<tr><td>Parámetros</td>
		<td>Especifique los parámetros, si lo requiere el script. Sin embargo, el script para instalar R no requiere ningún parámetro, por lo que puede dejarlo en blanco.</td></tr>
</table>Puede agregar más de una acción de script para instalar varios componentes en el clúster. Una vez agregados los scripts, haga clic en la marca de verificación para comenzar a crear el clúster.

También puede utilizar el script para instalar R en HDInsight con Azure PowerShell o el SDK de .NET de HDInsight. Más adelante en este artículo se proporcionan instrucciones para estos procedimientos.

## Ejecutar scripts de R
En esta sección se describe cómo ejecutar un script de R en el clúster de Hadoop con HDInsight.

1. **Establezca una conexión de Escritorio remoto con el clúster**: en el Portal, habilite el Escritorio remoto para el clúster que creó con R instalado y, a continuación, conéctese al clúster. Para obtener instrucciones, vea [Conexión a los clústeres de HDInsight con RDP](hdinsight-administer-use-management-portal.md#rdp).

2. **Abra la consola de R**: la instalación de R coloca un vínculo a la consola de R en el escritorio del nodo principal. Haga clic en él para abrir la consola de R.

3. **Ejecute el script de R**: el script de R se puede ejecutar directamente desde la consola de R pegándolo, seleccionándolo y presionando ENTRAR. Este es un script de ejemplo simple que genera los números del 1 al 100 y, a continuación, los multiplica por 2.

		library(rmr2)
		library(rhdfs)
		ints = to.dfs(1:100)
		calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
		from.dfs(calc)

Las dos primeras líneas llaman a las bibliotecas de RHadoop instaladas con R. La línea final imprime los resultados en la consola. El resultado debe ser similar al siguiente:

	[1,]  1 2
	[2,]  2 4
	.
	.
	.
	[98,]  98 196
	[99,]  99 198
	[100,] 100 200


## Instalación de R mediante Azure PowerShell

Consulte [Personalización de clústeres de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell). El ejemplo muestra cómo instalar Spark con Azure PowerShell. Deberá personalizar el script para usar [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

## Instalación de R con .NET SDK

Consulte [Personalización de clústeres de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). El ejemplo muestra cómo instalar Spark con .NET SDK. Deberá personalizar el script para usar [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps11).


## Consulte también

- [Instalación y uso de R en clústeres de Hadoop para HDInsight (Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [Creación de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md): información general sobre la creación de clústeres de HDInsight.
- [Personalización de un clúster de HDInsight mediante la acción de script][hdinsight-cluster-customize]\: información general sobre la personalización de clústeres de HDInsight mediante la acción de script.
- [Desarrollo de la acción de script con HDInsight](hdinsight-hadoop-script-actions.md)
- [Instalación y uso de Spark en clústeres de HDInsight][hdinsight-install-spark]\: ejemplo de acción de script sobre la instalación de Spark.
- [Instalación de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install.md): ejemplo de acción de script sobre la instalación de Giraph.
- [Instalación de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install-linux.md): ejemplo de acción de script sobre la instalación de Solr.

[powershell-install-configure]: powershell-install-configure.md
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install-linux.md

<!---HONumber=AcomDC_0218_2016-->