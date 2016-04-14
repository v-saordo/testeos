<properties
   pageTitle="Implementación y administración de topologías de Apache Storm en HDInsight | Microsoft Azure"
   description="Aprenda a implementar, supervisar y administrar topologías de Apache Storm mediante el panel de Storm en HDInsight. Utilice herramientas de Hadoop para Visual Studio"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/22/2016"
   ms.author="larryfr"/>

#Implementación y administración de topologías de Apache Storm en HDInsight basado en Windows

El panel de Storm permite implementar y ejecutar fácilmente topologías de Apache Storm en el clúster de HDInsight mediante el explorador web. También puede utilizar el panel para supervisar y administrar topologías de ejecución. Si utiliza Visual Studio, las herramientas de HDInsight para Visual Studio proporcionan características similares en Visual Studio.

El panel de Storm y las características de Storm de las herramientas de HDInsight dependen de la API de REST de Storm, que se puede usar para crear sus propias soluciones de supervisión y administración.

> [AZURE.IMPORTANT] Los pasos de este documento requieren un clúster Storm basado en Windows en HDInsight. Para obtener información sobre el uso de un clúster basado en Linux, consulte [Implementación y administración de topologías de Apache Storm en HDInsight basado en Linux](hdinsight-storm-deploy-monitor-topology-linux.md)

##Requisitos previos

* **Apache Storm en HDInsight**: consulte <a href="../hdinsight-storm-getting-started/" target="_blank">Introducción a Apache Storm en HDInsight</a> para conocer los pasos para la creación de un clúster.

* Para el **panel de Storm**: un explorador web moderno que admite HTML5.

* Para **Visual Studio**: Azure SDK 2.5.1 o versiones más recientes y las herramientas de HDInsight para Visual Studio. Consulte <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Introducción a las herramientas de HDInsight</a> para Visual Studio para instalar y configurar las herramientas de HDInsight para Visual Studio.

	Una de las siguientes versiones de Visual Studio:

	* Visual Studio 2012 con <a href="http://www.microsoft.com/download/details.aspx?id=39305" target="_blank">Update 4</a>

	* Visual Studio 2013 con <a href="http://www.microsoft.com/download/details.aspx?id=44921" target="_blank">Update 4</a> o <a href="http://go.microsoft.com/fwlink/?LinkId=517284" target="_blank">Visual Studio Community 2013</a>

	* <a href="http://visualstudio.com/downloads/visual-studio-2015-ctp-vs" target="_blank">Visual Studio 2015 CTP6</a>

	> [AZURE.NOTE] Actualmente las herramientas de HDInsight para Visual Studio solo admiten Storm en la versión 3.2 del clúster de HDInsight.

##Panel de Storm

El panel de Storm es una página web disponible en el clúster de Storm. La dirección URL es **https://&lt;clustername>.azurehdinsight.net/**, donde **clustername** es el nombre del clúster de Storm en HDInsight.

En la parte superior del panel de Storm, seleccione **Enviar topología**. Siga las instrucciones de la página para ejecutar una topología de muestra o cargar y ejecutar una topología que haya creado.

![la página de envío de topología][storm-dashboard-submit]

###UI de Storm

En el panel de Storm, seleccione el vínculo **IU de Storm**. Se mostrará información sobre el clúster, así como las topologías que estén en ejecución.

![la interfaz de usuario de storm][storm-dashboard-ui]

> [AZURE.NOTE] Con algunas versiones de Internet Explorer, es posible que descubra que la interfaz de usuario de Strom no se actualiza después de visitarla por primera vez. Por ejemplo, puede que no se muestren las topologías nuevas que haya enviado o puede que se muestre una topología como activa, aunque anteriormente la desactivase. Microsoft conoce este problema y está trabajando para conseguir una solución.

####Página principal

La página principal de la interfaz de usuario de Storm ofrece la siguiente información:

* **Resumen del clúster**: información básica sobre el clúster de Storm.

* **Resumen de las topologías**: una lista de las topologías en ejecución. Use los vínculos de esta sección para obtener más información sobre las topologías específicas.

* **Resumen de supervisor**: información acerca del supervisor de Storm.

* **Configuración de Nimbus**: configuración de Nimbus del clúster.

####Resumen de la topología

Si selecciona un vínculo desde la sección **Resumen de la topología**, se mostrará la siguiente información sobre la topología.

* **Resumen de la topología**: información básica sobre la topología.

* **Acciones de topología**: acciones de administración que puede realizar para la topología.

	* **Activar**: reanuda el procesamiento de una topología desactivada.

	* **Desactivar**: pausa una topología en ejecución.

	* **Reequilibrar**: ajusta el paralelismo de la topología. Debe volver a equilibrar las topologías en ejecución después de haber cambiado el número de nodos del clúster. Esto permite que la topología ajuste el paralelismo para compensar el mayor o menor número de nodos del clúster.

		Para obtener más información, consulte <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Descripción del paralelismo de una topología de Storm</a>.

	* **Eliminar**: finaliza una topología de Storm tras el tiempo de espera especificado.

* **Estadísticas de topología**: estadísticas sobre la topología. Use los vínculos de la columna **Ventana** para establecer el plazo de tiempo para las entradas restantes en la página.

* **Spouts**: los spouts que usa la topología. Use los vínculos de esta sección para obtener más información acerca de spouts específicos.

* **Bolts**: los bolts que usa la topología. Use los vínculos de esta sección para obtener más información acerca de bolts específicos.

* **Configuración de la topología**: la configuración de la topología seleccionada.

####Resumen de spouts y bolts

Si se selecciona un spout en la sección **Spouts** o **Bolts**, se muestra la siguiente información sobre el elemento seleccionado:

* **Resumen de componentes**: información básica acerca del spout o bolt.

* **Estadísticas de spouts/bolts**: estadísticas sobre el spout o bolt. Use los vínculos de la columna **Ventana** para establecer el plazo de tiempo para las entradas restantes en la página.

* **Estadísticas de entrada** (solo bolt): información sobre las secuencias de entrada consumidas por el bolt.

* **Estadísticas de salida**: información sobre las secuencias emitidas por este spout o bolt

* **Ejecutores**: información sobre las instancias del spout o bolt. Seleccione la entrada **Puerto** de un ejecutor específico para ver un registro de información de diagnóstico generado por esta instancia.

* **Errores**: cualquier información de error de este spout o bolt.

##Herramientas de HDInsight para Visual Studio

Las herramientas de HDInsight puede utilizarse para enviar las topologías de C# o híbridas al clúster de Storm. Los pasos siguientes usan una aplicación de muestra. Para obtener información sobre cómo crear sus propias topologías con las herramientas de HDInsight, consulte [Desarrollo de las topologías de C# mediante las herramientas de HDInsight para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Utilice los siguientes pasos para implementar una muestra en el clúster de Storm en HDInsight y, a continuación, ver y administrar la topología.

1. Si todavía no tiene instalada la versión más reciente de las herramientas de HDInsight para Visual Studio, consulte <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Introducción al uso de las herramientas de HDInsight para Visual Studio</a>.

2. Abra Visual Studio, seleccione **Archivo** > **Nuevo** > **Proyecto**.

3. En el cuadro de diálogo **Nuevo proyecto**, expanda **Instalado** > **Plantillas** y seleccione **HDInsight**. En la lista de plantillas, seleccione **Muestra de Storm**. En la parte inferior del cuadro de diálogo, escriba un nombre para la aplicación.

	![imagen](./media/hdinsight-storm-deploy-monitor-topology/sample.png)

1. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto y seleccione **Enviar a Storm en HDInsight**.

	> [AZURE.NOTE] Si se le solicita, escriba las credenciales de inicio de sesión de su suscripción de Azure. Si tiene más de una suscripción, inicie sesión en la que contenga el clúster de Storm en HDInsight.

2. Seleccione el clúster de Storm en HDInsight desde el menú desplegable **Clúster de Storm** y, después, seleccione **Enviar**. Puede supervisar si el envío es correcto mediante la ventana **Salida**.

3. Cuando la topología se envíe correctamente, deben aparecer las **topologías de Storm** del clúster. Seleccione la topología de la lista para ver información acerca de la topología de ejecución.

	![supervisión de visual studio](./media/hdinsight-storm-deploy-monitor-topology/vsmonitor.png)

	> [AZURE.NOTE] También puede ver las **topologías de Storm** en el **Explorador de servidores** expandiendo **Azure** > **HDInsight** y, después, haciendo clic con el botón derecho en un clúster de Storm en HDInsight y seleccionando **Ver topologías de Storm**.

	Seleccione la forma de los spouts o bolts para ver información sobre estos componentes. Se abrirá una ventana nueva para cada elemento seleccionado.
    
    > [AZURE.NOTE] El nombre de la topología es el nombre de clase de la topología (en este caso, `HelloWord`) con una marca de tiempo adjunta.

4. Desde la vista **Resumen de la topología**, seleccione **Eliminar** para detener la topología.

	> [AZURE.NOTE] Las topologías de Storm continúan ejecutándose hasta que se detengan o se elimine el clúster.

##API de REST

La interfaz de usuario de Storm se basa en la API de REST, lo que permite realizar una funcionalidad similar de administración y supervisión mediante la API de REST. Puede usar la API de REST para crear herramientas personalizadas para administrar y supervisar las topologías de Storm.

Para más información, vea la [API de REST de la IU de Storm](https://github.com/apache/storm/blob/0.9.3-branch/STORM-UI-REST-API.md). La siguiente información es específica para usar la API de REST con Apache Storm en HDInsight.

###URI base

El URI base para la API de REST en los clústeres de HDInsight es **https://&lt;clustername>.azurehdinsight.net/stormui/api/v1/**, donde **clustername** es el nombre del clúster de Storm en HDInsight.

###Autenticación

Las solicitudes a la API de REST deben usar la **autenticación básica**; use el nombre y la contraseña de administrador del clúster de HDInsight.

> [AZURE.NOTE] Dado que la autenticación básica se envía mediante texto no cifrado, **siempre** debe usar HTTPS para proteger las comunicaciones con el clúster.

###Valores devueltos

La información que se devuelve de la API de REST solo se puede utilizar desde dentro del clúster o de máquinas virtuales en la misma red virtual de Azure que el clúster. Por ejemplo, no se podrá obtener el acceso desde Internet al nombre de dominio completo (FQDN) devuelto para servidores de Zookeeper.

##Pasos siguientes

Ahora que ha aprendido cómo implementar y supervisar las topologías mediante el panel Storm, aprenda cómo:

* [Ejecutar topologías de C# mediante las herramientas de HDInsight para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

* [Desarrollar topologías basadas en Java con Maven](hdinsight-storm-develop-java-topology.md)

Para obtener una lista con más topologías de ejemplo, consulte [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md).

[hdinsight-dashboard]: ./media/hdinsight-storm-deploy-monitor-topology/dashboard-link.png
[storm-dashboard-submit]: ./media/hdinsight-storm-deploy-monitor-topology/submit.png
[storm-dashboard-ui]: ./media/hdinsight-storm-deploy-monitor-topology/storm-ui-summary.png

<!---HONumber=AcomDC_0224_2016-->