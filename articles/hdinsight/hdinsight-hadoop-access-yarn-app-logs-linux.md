<properties
	pageTitle="Acceso a registros de aplicación de YARN de Hadoop en HDInsight basado en Linux | Microsoft Azure"
	description="Obtenga información acerca de cómo tener acceso a los registros de aplicaciones de YARN en un clúster de HDInsight (Hadoop) basado en Linux, mediante la línea de comandos y un explorador web."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
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

# Acceso a registros de aplicación de YARN en HDInsight basado en Linux

En este documento se explica cómo acceder a los registros de aplicaciones de YARN (del inglés Yet Another Resource Negotiator) que finalicen en un clúster Hadoop en HDInsight de Azure.

> [AZURE.NOTE] La información contenida en este documento es específica de los clústeres de HDInsight basados en Linux. Para obtener información sobre los clústeres basados en Windows, vea [Acceso a registros de aplicación de YARN en HDInsight basado en Windows ](hdinsight-hadoop-access-yarn-app-logs.md)

## Requisitos previos

* Un clúster de HDInsight basado en Linux

* Debe [crear un túnel SSH](hdinsight-linux-ambari-ssh-tunnel.md) para poder acceder a la interfaz de usuario web de registros de ResourceManager.

## <a name="YARNTimelineServer"></a>Servidor de escala de tiempo de YARN

El [Servidor de escala de tiempo de YARN](http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) ofrece información genérica sobre las aplicaciones completadas, así como información de aplicaciones específicas del marco a través de dos interfaces diferentes. Concretamente:

* Se ha habilitado el almacenamiento y la recuperación de información de aplicaciones genéricas en clústeres de HDInsight con la versión 3.1.1.374 o superiores.
* El componente de información de aplicaciones específica del marco del servidor de la escala de tiempo no está disponible actualmente en los clústeres de HDInsight.

La información genérica sobre aplicaciones incluye los siguientes tipos de datos:

* El ID de la aplicación, que es el identificador único de una aplicación.
* El usuario que ha iniciado la aplicación.
* La información acerca de los intentos realizados para completar la aplicación.
* Los contenedores usados por cualquier intento de aplicación concreto.

## <a name="YARNAppsAndLogs"></a>Registros y aplicaciones de YARN

YARN admite varios modelos de programación (MapReduce es uno de ellos) al desacoplar la administración de recursos de la programación/supervisión de aplicaciones. Esto se realiza mediante un *Administrador de recursos* (RM) global, por nodo de trabajo *Administradores de nodos* (NM) y por aplicación *Maestros de aplicación* (AM). El AM por aplicación negocia recursos (CPU, memoria, disco, red) para ejecutar la aplicación con el RM. El RM funciona con NM para conceder estos recursos como *contenedores*. El AM es responsable del seguimiento del progreso de los contenedores asignados a él por el RM. Una aplicación puede requerir muchos contenedores según la naturaleza de la aplicación.

Además, cada aplicación puede constar de varios *intentos de aplicación* para completar la aplicación en el caso de bloqueos o debido a la pérdida de comunicación entre un AM y un RM. Por lo tanto, los contenedores se conceden a un intento específico de una aplicación. En cierto sentido, un contenedor ofrece el contexto para la unidad básica de trabajo realizado por una aplicación YARN, y todo el trabajo que se realiza en el contexto de un contenedor se lleva a cabo en el nodo de trabajo concreto en el que se ha asignado el contenedor. Consulte [Conceptos de YARN][YARN-concepts] para obtener más información.

Los registros de aplicación (y los registros de contenedor asociados) son fundamentales en la depuración de las aplicaciones de Hadoop problemáticas. YARN proporciona un buen marco para recopilar, agregar y almacenar registros de aplicaciones con la característica [Agregación de registro][log-aggregation]. La característica Agregación de registro hace que el acceso a los registros de aplicaciones sea más determinante, ya que agrega los registros en todos los contenedores en un nodo de trabajo y los almacena como un archivo de registro agregado por nodo de trabajo en el sistema de archivos predeterminado después de que se complete una aplicación. Su aplicación puede utilizar cientos o miles de contenedores, pero los registros para todos los contenedores que se ejecutan en un nodo de trabajo único se agregarán siempre en un archivo único, lo que da como resultado un archivo de registro por nodo de trabajo usado por la aplicación. La agregación de registro está habilitada de forma predeterminada en los clústeres de HDInsight (versión 3.0 y posteriores). Los registros agregados se pueden encontrar en el contenedor predeterminado del clúster en la siguiente ubicación:

	wasb:///app-logs/<user>/logs/<applicationId>

En dicha ubicación, *usuario* es el nombre del usuario que inició la aplicación. *applicationId*, por su parte, es el identificador único de una aplicación según la asignación del RM de YARN.

Los registros agregados no son legibles directamente tal como se escriben en un [TFile][T-file], [formato binario][binary-format] indizado por el contenedor. Debe usar los registros de ResourceManager de YARN o las herramientas de la CLI para ver estos registros como texto sin formato para aplicaciones o contenedores de interés.

##Herramientas de la CLI de YARN

Para poder usar las herramientas de la CLI de YARN, tiene que conectarse primero al clúster de HDInsight mediante SSH. Para obtener más información sobre el uso de SSH con HDInsight, consulte uno de los siguientes documentos:

- [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

- [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
	
Puede ver estos registros como texto sin formato ejecutando uno de los siguientes comandos:

	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
	
Al ejecutar estos comandos debe usar la siguiente información: &lt;applicationId>, &lt;user-who-started-the-application>, &lt;containerId> y &lt;worker-node-address>.

##Interfaz de usuario de ResourceManager de YARN

La interfaz de usuario de ResourceManager de YARN se ejecuta en el nodo principal del clúster y se puede acceder a ella a través de la interfaz de usuario web de Ambari; pero, para ello, primero tiene que [crear un túnel SSH](hdinsight-linux-ambari-ssh-tunnel.md).

Una vez creado un túnel SSH, siga estos pasos para ver los registros de YYARN:

1. Abra el explorador web y navegue a https://CLUSTERNAME.azurehdinsight.net. Reemplace CLUSTERNAME por el nombre del clúster de HDInsight.

2. En la lista de servicios de la izquierda de la página, seleccione __YARN__.

	![Servicio de YARN seleccionado](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)

3. En la lista desplegable __Vínculos rápidos__, seleccione uno de los nodos del clúster principal y elija __Registro de ResourceManager__.

	![Vínculos rápidos de YARN](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)
	
	Aparecerá una lista de vínculos a los registros de YARN.

[YARN-timeline-server]: http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]: http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]: https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]: https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]: http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/

<!---HONumber=AcomDC_0211_2016-->