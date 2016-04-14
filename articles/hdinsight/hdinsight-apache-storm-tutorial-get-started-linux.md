<properties
	pageTitle="Tutorial de Apache Storm: Introducción a Storm basado en Linux en HDInsight | Microsoft Azure"
	description="Introducción al análisis de macrodatos con Apache Storm y los ejemplos de storm-starter en HDInsight basado en Linux. Aprenda a usar Storm para procesar datos en tiempo real."
	keywords="Storm de Apache, tutorial de Storm de Apache, análisis de macrodatos, inicio de Storm"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/07/2016"
   ms.author="larryfr"/>


# Tutorial de Apache Storm: introducción a las muestras de Inicio de Storm para análisis de grandes cantidades de datos en HDInsight

Apache Storm es un sistema de cálculo distribuido, escalable, con tolerancia a errores y en tiempo real para el procesamiento de secuencias de datos. Con Storm en HDInsight de Azure, puede crear un clúster de Storm basado en la nube que realice análisis en tiempo real de grandes cantidades de datos en tiempo real.

> [AZURE.NOTE] En los pasos de este artículo se crea un clúster de HDInsight basado en Linux. A fin de conocer los pasos para crear un clúster de Storm en HDInsight basado en Windows, consulte [Tutorial de Apache Storm: Introducción a las muestras de inicio de Storm con análisis de datos en HDInsight](hdinsight-apache-storm-tutorial-get-started.md).

## Antes de empezar

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Debe cumplir los siguientes requisitos previos para poder completar correctamente este tutorial sobre Apache Storm.

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Familiaridad con SSH y SCP**. Para obtener más información sobre el uso de SSH y SCP con HDInsight, vea lo siguiente:

    - **Clientes Linux, Unix u OS X**: vea [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X (vista previa)](hdinsight-hadoop-linux-use-ssh-unix.md).

	- **Clientes Windows**: consulte [Uso de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## Creación de un clúster de Storm

Storm en HDInsight usa el almacenamiento de blobs de Azure para almacenar archivos de registro y topologías enviadas al clúster. Siga estos pasos para crear una cuenta de almacenamiento de Azure para usarla con el clúster:

1. Inicie sesión en el [Portal de Azure][preview-portal].

2. Seleccione **NUEVO**, __Análisis de datos__ y __HDInsight__

	![Creación de un clúster en el Portal de Azure](./media/hdinsight-apache-storm-tutorial-get-started-linux/new-cluster.png)

3. Escriba un __Nombre de clúster__ y, a continuación, seleccione __Storm__ para el __Tipo de clúster__. Si está disponible, aparecerá una marca de verificación verde junto al __Nombre de clúster__.

	![Nombre del clúster, tipo de clúster y tipo de sistema operativo](./media/hdinsight-apache-storm-tutorial-get-started-linux/clustername.png)

	Seleccione __Ubuntu__ para crear un clúster de HDInsight basado en Linux.
    
    > [AZURE.NOTE] Deje el campo __Versión__ con el valor predeterminado cuando realice los pasos de este documento.
	
4. Si tienes más de una suscripción, selecciona la entrada __Suscripción__ entrada para seleccionar la suscripción de Azure que se usará para el clúster.

5. Para el __Grupo de recursos__, puedes seleccionar la entrada para ver una lista de grupos de recursos existentes y después seleccionar en el que quieras crear el clúster. También puede seleccionar __Crear nuevo__ y escribir el nombre del nuevo grupo de recursos. Aparecerá una marca de verificación verde para indicar si el nuevo nombre de grupo está disponible.

	> [AZURE.NOTE] Esta entrada se establecerá de manera predeterminada en uno de sus grupos de recursos existentes, si hay alguno disponible.

6. Selecciona __Credenciales__ y después escribe una __Contraseña de inicio de sesión de clúster__ y un __Nombre de usuario de inicio de sesión de clúster__. También tienes que especificar un __Nombre de usuario de SSH__ y una __CONTRASEÑA__ o una __CLAVE PÚBLICA__, que se usará para autenticar al usuario de SSH. Por último, use el botón __Seleccionar__ para establecer las credenciales.

	![Hoja Credenciales de clúster](./media/hdinsight-administer-use-portal-linux/clustercredentials.png)

	Para obtener más información sobre el uso de SSH con HDInsight, vea uno de los siguientes artículos:

	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

6. Como __Origen de datos__, puede seleccionar la entrada para elegir un origen de datos existente o crear uno nuevo.

	![Hoja Origen de datos](./media/hdinsight-apache-storm-tutorial-get-started-linux/datasource.png)
	
	Actualmente puede seleccionar una cuenta de almacenamiento de Azure como origen de datos para un clúster de HDInsight. Usa lo siguiente para comprender las entradas de la hoja de __Origen de datos__.
	
	- __Método de selección__: establézcalo en __De todas las suscripciones__ para habilitar la exploración de cuentas de almacenamiento en sus suscripciones. Establézcalo como __Tecla de acceso__ si desea especificar el __Nombre de almacenamiento__ y la __Tecla de acceso__ de una cuenta de almacenamiento existente.
    
    - __Seleccionar una cuenta de almacenamiento__: si ya existe una cuenta de almacenamiento para su suscripción, úsela para seleccionar la cuenta que se utilizará para el clúster.
	
	- __Crear nuevo__: use esta opción para crear una nueva cuenta de almacenamiento. Use el campo que aparece para especificar el nombre de la cuenta de almacenamiento. Si el nombre está disponible, aparecerá una marca de verificación verde.
	
	- __Elegir contenedor predeterminado__: use esta opción para escribir el nombre del contenedor predeterminado que se usará para el clúster. Aunque se puede escribir cualquier nombre aquí, se recomienda usar el mismo nombre que el del clúster para que pueda reconocer fácilmente que el contenedor se usa para este clúster concreto.
	
	- __Ubicación__: la región geográfica en la que se encontrará o donde se creará la cuenta de almacenamiento.
	
		> [AZURE.IMPORTANT] Al seleccionar la ubicación del origen de datos predeterminado también establecerá la ubicación del clúster de HDInsight. El origen de datos predeterminado y el clúster deben encontrarse en la misma región.
    
    - __Identidad de AAD del clúster__: utilice esta opción para seleccionar una identidad de Azure Active Directory que usará el clúster para acceder al Almacén de Azure Data Lake.
    
        > [AZURE.NOTE] No se usa en este documento y puede dejarse en su valor predeterminado. Para obtener más información sobre el uso de esta entrada y del Almacén de Azure Data Lake con HDInsight, consulte [Creación de un clúster de HDInsight con el Almacén de Data Lake mediante el Portal de Azure](data-lake-store-hdinsight-hadoop-use-portal.md).
		
	- __Seleccionar__: use esta opción para guardar la configuración del origen de datos.
	
7. Seleccione __Planes de tarifa de nodo__ para mostrar información sobre los nodos que se crearán para este clúster. De forma predeterminada, el número de nodos de trabajo se establecerá en __4__. El costo estimado del clúster se mostrará en la parte inferior de esta hoja.

	![Hoja Niveles de precios de nodo](./media/hdinsight-apache-storm-tutorial-get-started-linux/nodepricingtiers.png)
	
    Puede seleccionar cada tipo de nodo para cambiar el tipo de máquina virtual usado por estos nodos en el clúster. Deje la configuración predeterminada cuando realice los pasos de este documento.
    
	Use el botón __Seleccionar__ para guardar la información de los __Niveles de precios de nodo__.

8. Seleccione __Configuración opcional__. Esta hoja le permite unir el clúster a una __red virtual__, usar __acciones de script__ para personalizar el clúster o usar una __tienda de metadatos personalizada__ para almacenar los datos de Hive y Oozie.

	![Hoja Configuración opcional](./media/hdinsight-apache-storm-tutorial-get-started-linux/optionalconfiguration.png)
    
    Deje esta configuración como __Sin configurar__ cuando realice los pasos de este documento.

9. Asegúrese de que la opción __Anclar a Panel de inicio__ está seleccionada y luego elija __Crear__. Esto creará el clúster y agregará un icono para él en el panel de inicio de su Portal de Azure. El icono indicará que el clúster está aprovisionando y cambiará para mostrar el icono de HDInsight cuando se haya completado el proceso.

	| Durante el aprovisionamiento | Aprovisionamiento completado |
	| ------------------ | --------------------- |
	| ![Indicador de aprovisionamiento en el panel de inicio](./media/hdinsight-apache-storm-tutorial-get-started-linux/provisioning.png) | ![Icono de clúster aprovisionado](./media/hdinsight-apache-storm-tutorial-get-started-linux/provisioned.png) |

	> [AZURE.NOTE] El clúster tardará algo de tiempo en crearse, normalmente unos 15 minutos. Use el icono del Panel de inicio o la entrada __Notificaciones__ en la parte izquierda de la página para comprobar el proceso de aprovisionamiento.

##Ejecución de una muestra de inicio de Storm en HDInsight

Los ejemplos de [storm-starter](https://github.com/apache/storm/tree/master/examples/storm-starter) se incluyen en el clúster de HDInsight. En los pasos siguientes, ejecutará el ejemplo de WordCount.

1. Conéctese al clúster de HDInsight con SSH:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	Si usó una contraseña para proteger la cuenta de usuario SSH, se le pedirá la contraseña. Si usa una clave pública, tal vez tenga que usar el parámetro `-i` para especificar la ruta de acceso a la correspondiente clave privada. Por ejemplo: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
		
	Para obtener más información sobre el uso de SSH con HDInsight basado en Linux, vea los siguientes artículos:
	
	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows)

2. Use el comando siguiente para iniciar una topología de ejemplo:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology wordcount
		
	> [AZURE.NOTE] La parte `0.9.3.2.2.4.9-1` del nombre del archivo puede cambiar conforme HDinsight se actualiza con las versiones más recientes de Storm.

    Esto iniciará la topología de WordCount de ejemplo en el clúster, con un nombre descriptivo de "wordcount". Generará frases aleatoriamente y contará la aparición de cada palabra en las oraciones.

    > [AZURE.NOTE] Al enviar la topología al clúster, primero debe copiar el archivo jar que contiene el clúster antes de usar el comando `storm`. Esto se puede lograr mediante el comando `scp` desde el cliente donde se encuentra el archivo. Por ejemplo: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
    >
    > El ejemplo de WordCount, y otros ejemplos de inicio de Storm, ya están incluidos en el clúster en `/usr/hdp/current/storm-client/contrib/storm-starter/`.

##Supervisión de la topología

La interfaz de usuario de Storm ofrece una interfaz web para trabajar con topologías en ejecución y se incluye en el clúster de HDInsight.

Siga estos pasos para supervisar la topología mediante la interfaz de usuario de Storm:

1. Abra un navegador web y vaya a https://CLUSTERNAME.azurehdinsight.net/stormui, donde __CLUSTERNAME__ es el nombre del clúster. Se abrirá la interfaz de usuario de Storm.

	> [AZURE.NOTE] Si se le pide que ofrezca un nombre de usuario y una contraseña, escriba el administrador de clústeres (admin) y la contraseña que usó al crear el clúster.

2. En el **resumen de la topología**, seleccione la entrada **wordcount** de la columna **Nombre**. Se mostrará más información sobre la topología.

	![Panel de Storm con la información de topología de WordCount de inicio de Starter.](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)

	Esta página ofrece la siguiente información:

	* **Estadísticas de topología**: información básica sobre el rendimiento de la topología, organizada en ventanas de tiempo.

		> [AZURE.NOTE] Al seleccionar una ventana de tiempo específica, se cambia la ventana de tiempo de la información que aparece en otras secciones de la página.

	* **Spouts**: información básica sobre spouts, entre la que se incluye el último error que ha devuelto cada spout.

	* **Bolts**: información básica sobre bolts.

	* **Configuración de la topología**: información detallada sobre la configuración de la topología.

	Esta página también ofrece acciones que se pueden realizar en la topología:

	* **Activar**: reanuda el procesamiento de una topología desactivada.

	* **Desactivar**: pausa una topología en ejecución.

	* **Reequilibrar**: ajusta el paralelismo de la topología. Debe volver a equilibrar las topologías en ejecución después de haber cambiado el número de nodos del clúster. Esto permite que la topología ajuste el paralelismo para compensar el mayor o menor número de nodos del clúster. Para más información, consulte la entrada de blog [Understanding the parallelism of a Storm topology](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

	* **Eliminar**: finaliza una topología de Storm tras el tiempo de espera especificado.

3. En esta página, seleccione una entrada en la sección **Spouts** o **Bolts**. Se mostrará información sobre el componente seleccionado.

	![Panel de Storm con información acerca de los componentes seleccionados.](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)

	En esta página se muestra la siguiente información:

	* **Estadísticas de spouts y bolts**: información básica sobre el rendimiento de los componentes, organizada en ventanas de tiempo.

		> [AZURE.NOTE] Al seleccionar una ventana de tiempo específica, se cambia la ventana de tiempo de la información que aparece en otras secciones de la página.

	* **Estadísticas de entrada** (solo bolt): información sobre los componentes que generan los datos que consume el bolt.

	* **Estadísticas de salida**: información sobre los datos que emite este bolt.

	* **Ejecutores**: información sobre las instancias de este componente.

	* **Errores**: errores que ha generado este componente.

4. Al visualizar los detalles de un spout o bolt, seleccione una entrada de la columna **Puerto** en la sección **Ejecutores** para ver los detalles de una instancia específica del componente.

		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

	A partir de estos datos, puede ver que la palabra **seven** se ha repetido 1493957 veces. Ese es el número de veces que se ha encontrado desde que se inició esta topología.

##Detención de la topología

Vuelva a la página **Resumen de la topología** de la topología de recuento de palabras y, después, seleccione el botón **Eliminar** de la sección **Acciones de topología**. Cuando se le solicite, escriba 10 como los segundos de espera antes de detener la topología. Tras el período de tiempo de espera, ya no aparecerá la topología cuando visite la sección **IU de Storm** del panel.

##Eliminación del clúster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##Resumen

En este tutorial de Apache Storm, usó el inicio de Storm para aprender a crear un clúster de Storm en HDInsight y usar el panel de Storm para implementar, supervisar y administrar topologías de Storm.

##<a id="next"></a>Pasos siguientes

* El documento siguiente contiene una lista de otros ejemplos que se pueden usar con Storm en HDInsight:

	* [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0309_2016-->