<properties 
	pageTitle="Problemas conocidos de Apache Spark en HDInsight | Microsoft Azure" 
	description="Problemas conocidos de Apache Spark en HDInsight." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2016" 
	ms.author="nitinme"/>

# Problemas conocidos de Apache Spark en HDInsight de Azure (Linux)

En este documento se hace un seguimiento de todos los problemas conocidos de la versión preliminar pública de HDInsight Spark.

##Sesión interactiva con pérdidas de Livy
 
**Síntoma:**

Cuando se reinicia Livy con una sesión interactiva (desde Ambari o debido al reinicio de la máquina virtual del nodo principal 0) activa, se perderá una sesión de trabajo interactiva. Por este motivo, los nuevos trabajos pueden bloquearse en el estado Aceptado y no se pueden iniciar.

**Mitigación:**

Utilice el procedimiento siguiente para encontrar una solución alternativa para el problema:

1. SSH en el nodo principal. 
2. Ejecute el siguiente comando para buscar los identificadores de aplicación de los trabajos interactivos iniciados mediante Livy. 

        yarn application –list

    Los nombres predeterminados de los trabajos serán Livy si se han iniciado con una sesión interactiva de Livy sin nombres explícitos especificados; en caso de que la sesión de Livy se haya iniciado mediante el cuaderno de Jupyter, el nombre del trabajo empezará por remotesparkmagics\_*.

3. Ejecute el comando siguiente para terminar esos trabajos.

        yarn application –kill <Application ID>

Los nuevos trabajos empezarán a ejecutarse.

##No se ha iniciado el servidor de historial de Spark 

**Síntoma:**
 
El servidor de historial de Spark no se inicia automáticamente después de crear un clúster.

**Mitigación:**

Inicie el servidor de historial manualmente desde Ambari.

##Error al cargar un bloc de notas con tamaños mayores

**Síntoma:**

Es posible que aparezca un error **`Error loading notebook`** al cargar blocs de notas que tengan un tamaño mayor.

**Mitigación:**

El hecho de recibir este error no implica que los datos estén dañados o perdidos. Los blocs de notas están todavía en el disco en `/var/lib/jupyter`, y puede ejecutar SSH en el clúster para tener acceso a ellos. Puede copiar los blocs de notas del clúster en el equipo local (mediante SCP o WinSCP) como copia de seguridad para evitar la pérdida de datos importantes del bloc de notas. A continuación, puede aplicar túneles SSH al nodo principal del puerto 8001 para tener acceso a Jupyter sin pasar por la puerta de enlace. Desde ahí, puede borrar la salida del bloc de notas y volver a guardarla para minimizar el tamaño del bloc de notas.

Para evitar que este error ocurra en el futuro, debe seguir algunos procedimientos recomendados:

* Es importante mantener reducido el tamaño del bloc de notas. Las salidas de los trabajos de Spark que se envían a Jupyter de vuelta se conservan en el bloc de notas. Es una práctica recomendada con Jupyter en general evitar la ejecución de `.collect()` en RDD o tramas de datos de gran tamaño; en su lugar, si desea inspeccionar el contenido de un RDD, considere la posibilidad de ejecutar `.take()` o `.sample()` para que la salida no sea demasiado grande.
* Además, cuando guarde un bloc de notas, desactive todas las celdas de la salida para reducir el tamaño.



##El primer inicio del cuaderno tarda más de lo esperado 

**Síntoma:**

La primera instrucción del cuaderno de Jupyter con la instrucción mágica de Spark podría tardar más de un minuto.

**Explicación:**
 
Esto sucede cuando se ejecuta la primera celda de código. En segundo plano se inicia la configuración de la sesión y se establecen los contextos de Spark, SQL y Hive. Una vez establecidos estos contextos, se ejecuta la primera instrucción y esto da la impresión de que la instrucción tardó mucho tiempo en completarse.

##Tiempo de espera del cuaderno de Jupyter en la creación de la sesión

**Síntoma:**

Cuando el clúster Spark se está quedando sin recursos, el tiempo de espera de los kernels Spark y Pyspark del cuaderno de Jupyter se agotará al tratar de crear la sesión.

**Mitigaciones:**

1. Libere algunos recursos del clúster Spark de la siguiente forma:

    - Detenga otros cuadernos de Spark en el menú Cerrar y detener o haga clic en Apagar en el explorador del cuaderno.
    - Detenga otras aplicaciones de Spark desde YARN.

2. Reinicie el cuaderno que trataba de iniciar. Debe haber suficientes recursos para crear una sesión ahora.

## Problemas con permisos en el directorio de registros de Spark 

**Síntoma:**
 
Cuando hdiuser envía un trabajo con spark-submit, hay un error java.io.FileNotFoundException: /var/log/spark/sparkdriver\_hdiuser.log (permiso denegado) y el registro del controlador no se ha escrito.

**Mitigación:**
 
1. Agregue hdiuser al grupo de Hadoop. 
2. Proporcione 777 permisos en /var/log/spark después de crear el clúster. 
3. Actualice la ubicación del registro spark con Ambari para que sea un directorio con 777 permisos.  
4. Ejecute spark-submit como sudo. 

##Consulte también

- [Introducción a Apache Spark en HDInsight de Azure (Linux)](hdinsight-apache-spark-overview.md)
- [Introducción: aprovisionamiento de Apache Spark en Azure HDInsight (Linux) y ejecución de consultas interactivas mediante Spark SQL](hdinsight-apache-spark-jupyter-spark-sql.md)

<!---HONumber=AcomDC_0224_2016-->