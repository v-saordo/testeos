<properties
	pageTitle="Guía de optimización y rendimiento de la actividad de copia"
	description="Conozca los factores más importantes que afectan al rendimiento del movimiento de datos en Factoría de datos de Azure mediante la actividad de copia."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="spelluru"/>


# Guía de optimización y rendimiento de la actividad de copia
En este artículo se describen los factores más importantes que afectan al rendimiento del movimiento de datos (actividad de copia) en Factoría de datos de Azure. También muestra el rendimiento observado durante las pruebas internas y trata diversas maneras de optimizar el rendimiento de la actividad de copia.

## Información general del movimiento de datos en Factoría de datos de Azure
La actividad de copia realiza el movimiento de datos en Factoría de datos de Azure y la actividad funciona con un [servicio de movimiento de datos con disponibilidad general](data-factory-data-movement-activities.md#global) que puede copiar datos entre [varios almacenes de datos](data-factory-data-movement-activities.md#supported-data-stores-for-copy-activity) de forma segura, confiable y escalable y con un mayor rendimiento. El servicio de movimiento de datos elige automáticamente la región más óptima para realizar la operación de movimiento de datos basándose en la ubicación de los almacenes de datos de origen y receptor. Actualmente se utiliza la región más cercana al almacén de datos receptor.

Analicemos cómo se produce este movimiento de datos en escenarios diferentes.

### Copia de datos entre dos almacenes de datos en la nube
Cuando los almacenes de datos de origen y receptor (destino) residen en la nube, la actividad de copia pasa por las siguientes fases para copiar o mover datos desde el origen hasta el receptor.

1.	lee datos del almacén de datos de origen,
2.	realiza tareas de serialización y deserialización, compresión y descompresión, asignación de columnas y conversión de tipos en función de las configuraciones del conjunto de datos de entrada, del conjunto de datos de salida y la actividad de copia,
3.	escribe datos en el almacén de datos de destino

![Copia de datos entre dos almacenes de datos en la nube](./media/data-factory-copy-activity-performance/copy-data-between-two-cloud-stores.png)

**Nota:** las formas con líneas discontinuas (compresión, asignación de columnas, etc.) son las capacidades que pueden usarse o no en su caso.


### Copia de datos entre un almacén de datos local y un almacén de datos en la nube
Para [mover datos entre un almacén de datos local y un almacén de datos en la nube](data-factory-move-data-between-onprem-and-cloud.md), necesitará instalar Data Management Gateway, que es un agente que permite el procesamiento y movimiento de datos híbridos, en el equipo local. En este escenario, Data Management Gateway realiza las tareas de serialización y deserialización, compresión y descompresión, asignación de columnas y conversión de tipos en función de las configuraciones del conjunto de datos de entrada, del conjunto de datos de salida y la actividad de copia.

![Copia de datos entre un almacén de datos local y un almacén de datos en la nube](./media/data-factory-copy-activity-performance/copy-data-between-onprem-and-cloud.png)

## Pasos de optimización del rendimiento
A continuación se indican los pasos típicos sugeridos para optimizar el rendimiento de su solución de Factoría de datos de Azure con actividad de copia.

1.	**Establecimiento de una línea base.** Durante la fase de desarrollo, pruebe la canalización con la actividad de copia con los datos de una muestra representativa. Puede aprovechar el [modelo de segmentación](data-factory-scheduling-and-execution.md#time-series-datasets-and-data-slices) de Factoría de datos de Azure para limitar la cantidad de datos con los que trabaja.

	Recopile el tiempo de ejecución y las características de rendimiento; para ello, consulte la hoja "segmento de datos" y la hoja "detalles de ejecución de la actividad" del conjunto de datos de salida en el Portal de vista previa de Azure, que muestra la duración de la actividad de copia y el tamaño de los datos copiados.

	![Detalles de ejecución de actividad](./media/data-factory-copy-activity-performance/activity-run-details.png)

	Puede comparar el rendimiento y las configuraciones de su escenario con la [referencia de rendimiento](#performance-reference) de la actividad de copia publicada siguiente, que se basa en observaciones internas.
2. **Diagnóstico y optimización de rendimiento** Si el rendimiento que observe es inferior a sus expectativas, debe identificar cuellos de botella de rendimiento y realizar optimizaciones para eliminar o reducir el impacto de los cuellos de botella. La descripción completa de las pruebas de diagnóstico del rendimiento queda fuera del ámbito de este artículo, pero a continuación enumeramos algunas consideraciones habituales.
	- [Origen](#considerations-on-source)
	- [Sink](#considerations-on-sink)
	- [Serialización y deserialización](#considerations-on-serializationdeserialization)
	- [Compresión](#considerations-on-compression)
	- [Asignación de columnas](#considerations-on-column-mapping)
	- [Data Management Gateway](#considerations-on-data-management-gateway)
	- [Otras consideraciones](#other-considerations)
3. **Expanda la configuración a todos los datos** Una vez que esté satisfecho con los resultados y el rendimiento de la ejecución, puede expandir la definición del conjunto de datos y el período activo de la canalización para incluir todos los datos en la imagen.

## Referencia de rendimiento
> [AZURE.IMPORTANT] **Aviso de declinación de responsabilidades:** los datos siguientes se publicaron únicamente con fines de orientación y planificación general. Se da por hecho que el ancho de banda, el hardware, la configuración, etc. son de los mejores de su clase. Use esto solo como referencia. La capacidad de proceso del movimiento de datos que observe se verá afectada por diversas variables. Consulte las secciones siguientes para obtener información acerca de cómo optimizar y lograr un mejor rendimiento para sus necesidades de movimiento de datos. Estos datos se actualizará a medida que se agreguen características y mejoras del rendimiento.

![Matriz de rendimiento](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

> [AZURE.NOTE] **Próximamente:** estamos en proceso de mejora de las características de rendimiento base y en breve verá más y mejores cifras de capacidad de proceso en la tabla anterior.

Puntos a tener en cuenta:

- La capacidad de proceso se calcula con la siguiente fórmula: [tamaño de los datos leídos del origen]/[duración de la ejecución de la actividad de copia]
- Se usó el conjunto de datos [TPC-H](http://www.tpc.org/tpch/) para calcular los números anteriores.
- En el caso de los almacenes de datos de Microsoft Azure, el origen y el receptor se encuentran en la misma región de Azure.
- En el caso del movimiento de datos híbrido (local a nube o nube a local), Data Management Gateway (instancia única) se hospedó en una máquina diferente que el almacén de datos local, con la siguiente configuración. Tenga en cuenta que cuando se ejecuta una sola actividad en la puerta de enlace, la operación de copia solo consume una pequeña parte del ancho de banda de red y de los recursos de CPU y de memoria de esta máquina.
	<table>
	<tr>
		<td>CPU</td>
		<td>Intel Xeon ® E5-2660 v2 de 32 núcleos, 2,20 GHz </td>
	</tr>
	<tr>
		<td>Memoria</td>
		<td>128 GB</td>
	</tr>
	<tr>
		<td>Red</td>
		<td>Interfaz de Internet: 10 Gbps; interfaz de intranet: 40 Gbps</td>
	</tr>
	</table>

## Consideraciones sobre el origen
### General
Asegúrese de que el almacén de datos subyacente no está desbordado por otras cargas de trabajo que se estén ejecutando en él o que lo estén usando para su ejecución, incluida la actividad de copia, entre otras.

Para los almacenes de datos de Microsoft, consulte los [temas sobre supervisión y optimización](#appendix-data-store-performance-tuning-reference) específicos del almacén de datos, que pueden ayudarle a comprender las características de rendimiento del almacén de datos, a minimizar los tiempos de respuesta y a maximizar la capacidad de proceso.

### Almacenes de datos basados en archivos
*(Incluye Blob de Azure, Azure Data Lake, sistema de archivos local)*

- **Tamaño de archivo medio y número de archivos**: la actividad de copia transfiere datos archivo por archivo. Con la misma cantidad de datos para mover, la capacidad de proceso general será mayor si los datos se componen de un gran número de archivos pequeños en lugar de un número reducido de archivos de mayor tamaño debido a la fase de arranque de cada archivo. Por lo tanto, si es posible, combine archivos pequeños en archivos de mayor tamaño para obtener una capacidad de proceso mayor.
- **Formato de archivo y compresión**: consulte las secciones [Consideraciones sobre la serialización y deserialización](#considerations-on-serializationdeserialization) y [Consideraciones sobre la compresión](#considerations-on-compression) para conocer más formas de mejorar el rendimiento.
- Además, para el escenario de **sistema de archivos local**, en el que el uso de **Data Management Gateway** es necesario, consulte la sección [Consideraciones sobre la puerta de enlace](#considerations-on-data-management-gateway).

### Almacenes de datos relacionales
*(Incluye Base de datos SQL de Azure, Almacenamiento de datos SQL de Azure, Base de datos de SQL Server, Base de datos de Oracle, Base de datos MySQL, Base de datos DB2, Base de datos de Teradata, Base de datos de Sybase, Base de datos de PostgreSQL)*

- **Patrón de datos**: el esquema de la tabla afecta a la capacidad de proceso de la copia. Para copiar la misma cantidad de datos, un tamaño de fila grande proporcionará un mejor rendimiento que un tamaño de fila pequeño porque la base de datos recupera de forma más eficiente menos lotes de datos que contienen un menor número de filas.
- **Consulta o procedimiento almacenado**: optimice la lógica de la consulta o del procedimiento almacenado que se especifica en el origen de la actividad de copia para que capture los datos de forma más eficiente.
- Además, para el escenario de **bases de datos relacionales locales**, como SQL Server y Oracle, en el que el uso de **Data Management Gateway** es necesario, consulte la sección [Consideraciones sobre la puerta de enlace](#considerations-on-data-management-gateway).

## Consideraciones sobre el receptor

### General
Asegúrese de que el almacén de datos subyacente no está desbordado por otras cargas de trabajo que se estén ejecutando en él o que lo estén usando para su ejecución, incluida la actividad de copia, entre otras.

Para los almacenes de datos de Microsoft, consulte los [temas sobre supervisión y optimización](#appendix-data-store-performance-tuning-reference) específicos del almacén de datos, que pueden ayudarle a comprender las características de rendimiento del almacén de datos, a minimizar los tiempos de respuesta y a maximizar la capacidad de proceso.

### Almacenes de datos basados en archivos
*(Incluye Blob de Azure, Azure Data Lake, sistema de archivos local)*

- **Comportamiento de copia**: si va a copiar datos desde otro almacén de datos basado en archivos, la actividad de copia proporciona tres tipos de comportamiento mediante la propiedad "copyBehavior": conservar la jerarquía, aplanar la jerarquía y combinar archivos. Conservar o aplanar la jerarquía supone poca o ninguna sobrecarga para el rendimiento, mientras que combinar archivos provoca una sobrecarga adicional en el rendimiento.
- **Formato de archivo y compresión**: consulte las secciones [Consideraciones sobre la serialización y deserialización](#considerations-on-serializationdeserialization) y [Consideraciones sobre la compresión](#considerations-on-compression) para conocer más formas de mejorar el rendimiento.
- Para **Blob de Azure**, actualmente solo se admiten blobs en bloques para obtener una transferencia de datos y una capacidad de proceso óptimas.
- Además, para los escenarios de **sistema de archivos local**, en el que el uso de **Data Management Gateway** es necesario, consulte la sección [Consideraciones sobre la puerta de enlace](#considerations-on-data-management-gateway).

### Almacenes de datos relacionales
*(Incluye Base de datos SQL de Azure, Almacenamiento de datos SQL de Azure, Base de datos de SQL Server)*

- **Comportamiento de copia**: según las propiedades configuradas para "sqlSink", la actividad de copia escribirá los datos en la base de datos de destino de maneras diferentes:
	- De forma predeterminada, el servicio de movimiento de datos usa la API de copia masiva para insertar datos en modo de anexión, que proporciona el mejor rendimiento.
	- Si configura un procedimiento almacenado en el receptor, la base de datos aplicará los datos fila por fila en lugar de realizar una carga masiva, por lo que el rendimiento se reducirá considerablemente. Si el tamaño de los datos es grande, cuando corresponda, considere la posibilidad de usar la propiedad "sqlWriterCleanupScript" (ver abajo) en su lugar.
	- Si configura la propiedad "sqlWriterCleanupScript", para cada actividad de copia que se ejecute, el servicio desencadenará primero el script y luego usará la API de copia masiva para insertar los datos. Por ejemplo, para sobrescribir toda la tabla con los datos más recientes, puede especificar un script para eliminar primero todos los registros antes de realizar la carga masiva de los nuevos datos desde el origen.
- **Patrón de datos y tamaño de lote**:
	- el esquema de la tabla afectará a la capacidad de proceso de la copia. Para copiar la misma cantidad de datos, un tamaño de fila grande proporcionará un mejor rendimiento que un tamaño de fila pequeño porque la base de datos puede confirmar de forma más eficiente menos lotes de datos.
	- La actividad de copia inserta datos en una serie de lotes, en la que el número de filas incluidas en un lote se pueden establecer usando la propiedad "writeBatchSize". Si los datos tienen filas de tamaño pequeño, puede establecer la propiedad "writeBatchSize" con un valor superior para beneficiarse de una sobrecarga menor de los lotes y aumentar la capacidad de proceso. Si el tamaño de fila de los datos es grande, tenga cuidado al aumentar el valor de writeBatchSize: un valor grande puede producir un error de copia debido a la sobrecarga de la base de datos.
- Además, para el escenario de **bases de datos relacionales locales**, como SQL Server y Oracle, en el que el uso de **Data Management Gateway** es necesario, consulte la sección [Consideraciones sobre la puerta de enlace](#considerations-on-data-management-gateway).


### Almacenes NoSQL
*(Incluyendo Tabla de Azure, Azure DocumentDB)*

- Para **Tabla de Azure**:
	- **Partición**: escribir datos en particiones intercaladas reducirá drásticamente el rendimiento. Puede ordenar los datos de origen por la clave de partición para que los datos se inserten en una partición después de otra de forma eficiente, o bien, puede ajustar la lógica para escribir los datos en una sola partición.
- Para **Azure DocumentDB**:
	- **Tamaño de lote**: la propiedad "writeBatchSize" indica el número de solicitudes paralelas al servicio de DocumentDB para crear documentos. Puede esperar un rendimiento mejor si aumenta el valor de “writeBatchSize” porque se envían más solicitudes en paralelo a DocumentDB. Sin embargo, tenga cuidado con la limitación al escribir en DocumentDB (mensaje de error "La tasa de solicitudes es grande"). La limitación puede ocurrir debido a diversos factores, incluido el tamaño de los documentos, el número de términos en los documentos y la directiva de indexación de la colección de destino. Para lograr una mayor capacidad de proceso de la copia, considere la posibilidad de usar una colección mejor (por ejemplo, S3).

## Consideraciones sobre la serialización y deserialización
La serialización y deserialización pueden ocurrir cuando el conjunto de datos de entrada o el conjunto de datos de salida es un archivo. Actualmente, la actividad de copia admite los formatos de datos Avro y texto (por ejemplo, CSV y TSV).

**Comportamientos de copia:**

- Al copiar archivos entre almacenes de datos basados en archivos:
	- Cuando los conjuntos de datos tanto de entrada como de salida tienen la misma configuración de formato de archivo, o ninguna, el servicio de movimiento de datos ejecutará una copia binaria sin realizar ninguna serialización ni deserialización. Por lo tanto, observará una capacidad de proceso mayor en comparación con el escenario en el que la configuración del formato de archivo del origen y el receptor es diferente.
	- Cuando los conjuntos de datos tanto de entrada como de salida están en formato de texto y solo el tipo de codificación es diferente, el servicio de movimiento de datos solo realizará la conversión de codificación sin realizar ninguna serialización o deserialización, lo que produce una sobrecarga en el rendimiento en comparación con la copia binaria.
	- Cuando los conjuntos de datos de entrada y salida tienen formatos de archivo o configuraciones diferentes (por ejemplo, delimitadores), el servicio de movimiento de datos deserializará los datos de origen en un flujo, los transformará y, después, los serializará en el formato de salida deseable. Esto provocará una sobrecarga mucho mayor en el rendimiento en comparación con los escenarios anteriores.
- Al copiar archivos hacia o desde un almacén de datos no basado en archivos (por ejemplo, un almacén basado en archivos en un almacén relacional), se requerirá el paso de serialización o deserialización y el resultado será una sobrecarga considerable en el rendimiento.

**Formato de archivo:** la elección del formato de archivo puede afectar al rendimiento de la copia. Por ejemplo, Avro es un formato binario compacto que almacena metadatos con datos y es ampliamente admitido en el ecosistema de Hadoop para procesamiento y consulta. Sin embargo, Avro es más costoso para serialización o deserialización, lo que resulta en una menor capacidad de proceso de copia en comparación con el formato de texto. La elección del formato de archivo que se va a usar en el flujo de procesamiento debe realizarse de manera holística, empezando por la forma en que los datos se almacenan en los almacenes de datos de origen o se extraen de los sistemas externos, el mejor formato de almacenamiento, procesamiento analítico y consultas, y en qué formato se deben exportar los datos a los repositorios de datos para las herramientas de informes y visualización. A veces, un formato de archivo que no es óptimo para el rendimiento de lectura y escritura puede terminar siendo adecuado si se tiene en cuenta el proceso analítico general.

## Consideraciones sobre la compresión
Cuando el conjunto de datos de entrada o de salida es un archivo, puede configurar la actividad de copia para realizar la compresión y descompresión a medida que se escriben los datos en el destino. Al habilitar la compresión, se equilibra entre E/S y CPU: la compresión de los datos costará recursos de proceso adicionales de costo pero, a cambio, reducirá la E/S de red y el almacenamiento lo que, dependiendo de los datos, podría suponer una mejora en la capacidad de proceso general de la copia.

**Códec:** se admiten los tipos de compresión GZIP, BZIP2 y Deflate. HDInsight de Azure pueden consumir los tres tipos para procesamiento. Cada códec de compresión tiene su característica particular. Por ejemplo, BZIP2 tiene la menor capacidad de proceso de la copia pero obtendrá el mejor rendimiento para las consultas Hive porque se puede dividir para procesamiento; GZIP proporciona la opción más equilibrada y el que se usa con mayor frecuencia. Debe elegir el códec más adecuado para su escenario de principio a fin.

**Nivel:** para cada códec de compresión, puede elegir entre dos opciones: compresión más rápida y compresión más óptima. La operación de compresión más rápida comprime los datos tan pronto como sea posible, incluso si el archivo resultante no se comprime de forma óptima. La opción de compresión óptima dedicará más tiempo a la compresión para obtener una cantidad de datos mínima. Puede probar ambas opciones para ver cuál proporciona un mejor rendimiento general en su caso.

**Una consideración:** para copiar un tamaño grande de datos entre un almacén local y la nube en casos donde el ancho de banda de la red corporativa y Azure son a menudo el factor limitante, y desea que los conjuntos de datos de entrada y de salida estén en un formato sin comprimir, puede usar un **Blob de Azure provisional** con la compresión. Concretamente, puede dividir una actividad de copia en dos actividades de copia: una primera actividad de copia que copia desde el origen al blob provisional o intermedio en formato comprimido, y la segunda actividad de copia que copia los datos comprimidos desde el blob provisional y los descomprime al escribir en el receptor.

## Consideraciones sobre la asignación de columnas
La propiedad "columnMappings" en la actividad de copia puede usarse para asignar todas las columnas de entrada, o un subconjunto de ellas, a las columnas de salida. Después de leer los datos del origen, el servicio de movimiento de datos debe realizar la asignación de columnas en los datos antes de escribirlos en el receptor. Este procesamiento adicional reduce la capacidad de proceso de la copia.

Si el almacén de datos de origen tiene capacidad para realizar consultas, por ejemplo, es un almacén relacional como Azure SQL o SQL Server o un almacén NoSQL como Tabla de Azure o Azure DocumentDB, puede considerar insertar la lógica de filtrado o reordenación de columnas en la propiedad de consulta en lugar de usar la asignación de columnas. Como resultado se realiza la proyección durante la lectura de los datos del almacén de datos de origen y es mucho más eficiente.

## Consideraciones sobre Data Management Gateway
Para ver las recomendaciones de configuración de la puerta de enlace, consulte [Consideraciones para usar Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md#Considerations-for-using-Data-Management-Gateway).

**Entorno de la máquina de puerta de enlace:** recomendamos usar un equipo dedicado para hospedar Data Management Gateway. Use herramientas como Monitor de rendimiento para examinar el uso de CPU, memoria y ancho de banda durante una operación de copia en la máquina de puerta de enlace. Cambie a un equipo con más capacidad si la CPU, la memoria o el ancho de banda de red se convierten en un cuello de botella.

**Ejecuciones de actividades de copia simultáneas:** una única instancia de Data Management Gateway puede atender varias ejecuciones de actividades de copia al mismo tiempo; es decir, una puerta de enlace puede ejecutar varios trabajos de copia al mismo tiempo (el número de trabajos simultáneos se calcula en función de la configuración de hardware de la máquina de puerta de enlace). Los trabajos de copia adicional se ponen en la cola hasta que la puerta de enlace los selecciona o hasta que se agota el tiempo del trabajo, lo que ocurra primero. Para evitar la contención de recursos en la puerta de enlace, puede organizar por fases la programación de sus actividades para reducir la cantidad de trabajos de copia que hay en cola a la vez, o considere la posibilidad de dividir la carga entre varias puertas de enlace.


## Otras consideraciones
Si el tamaño de los datos que se van a copiar es bastante grande, puede ajustar la lógica empresarial para particionar los datos aún más con el mecanismo de segmentación de Factoría de datos de Azure y programar la actividad de copia con más frecuencia para reducir el tamaño de los datos que cada actividad de copia ejecuta.

Tenga cuidado con el número de conjuntos de datos y actividades de copia que llegan al mismo almacén de datos en un momento dado. Un gran número de trabajos de copia simultáneos puede limitar un almacén de datos y provocar una disminución del rendimiento, reintentos internos del trabajo de copia y, en algunos casos, errores de ejecución.

## Caso práctico: copia de una instancia de SQL Server local a Blob de Azure
**Escenario:** se crea una canalización para copiar los datos de una instancia de SQL Server local a Blob de Azure en formato CSV. Para acelerar la copia, se especifica que los archivos CSV se deben comprimir en formato BZIP2.

**Análisis y pruebas:** se observa que la capacidad de proceso de la actividad de copia es inferior a 2 MB/s, mucho menor que los valores de referencia de rendimiento.

**Análisis y optimización del rendimiento:** para solucionar el problema de rendimiento, primero veremos cómo se procesan y se mueven los datos:

1.	**Lectura de datos:** la puerta de enlace abre la conexión a SQL Server y envía la consulta. SQL Server responde enviando el flujo de datos a la puerta de enlace a través de la intranet.
2.	La puerta de enlace **serializa** el flujo de datos en un formato CSV y los **comprime** en un flujo BZIP2.
3.	**Escritura de datos:** la puerta de enlace carga el flujo BZIP2 a Blob de Azure a través de Internet.

Como puede ver, los datos se procesan y se mueven en una transmisión de manera secuencial: SQL Server -> LAN -> Puerta de enlace -> WAN -> Blob de Azure; **el rendimiento general está determinado por la capacidad de proceso mínima en la canalización**.

![flujo de datos](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Uno o varios de los siguientes factores podrían ser el cuello de botella del rendimiento:

1.	**Origen:** el propio SQL Server tiene un rendimiento bajo debido a cargas intensas.
2.	**Data Management Gateway:**
	1.	**LAN:** la puerta de enlace está lejos de SQL Server con una conexión de ancho de banda bajo
	2.	La **carga en la máquina de puerta de enlace** ha alcanzado su límite para llevar a cabo lo siguiente:
		1.	**Serialización:** la serialización del flujo de datos a CSV tiene una capacidad de proceso baja
		2.	**Compresión:** se eligió el códec de compresión lenta (por ejemplo, BZIP2 que es de 2,8 MB/s con Core i7)
	3.	**WAN:** bajo ancho de banda entre la red corporativa y Azure (por ejemplo, T1=1544 kbps, T2=6312 kbps)
4.	**Receptor:** Blob de Azure tiene una capacidad de proceso baja (aunque es bastante improbable porque su SLA garantiza un mínimo de 60 MB/s).

En este caso, la compresión de datos BZIP2 podría estar ralentizando toda la canalización. Cambiar a un códec de compresión GZIP podría aliviar este cuello de botella.

## Apéndice: Referencia para la optimización del rendimiento del almacén de datos
Estas son algunas referencias para la supervisión y la optimización del rendimiento para algunos de los almacenes de datos compatibles:

- Almacenamiento de Azure (incluidos Blob de Azure y Tabla de Azure): [Objetivos de escalabilidad de Almacenamiento de Azure](../storage/storage-scalability-targets.md) y [Lista de comprobación de rendimiento y escalabilidad de Almacenamiento de Microsoft Azure](../storage//storage-performance-checklist.md)
- Base de datos SQL de Azure: puede [supervisar el rendimiento](../sql-database/sql-database-service-tiers.md#monitoring-performance) y comprobar el porcentaje de unidades de transacción de base de datos (DTU).
- Almacenamiento de datos SQL de Azure: su capacidad se mide por unidades de almacenamiento de datos (DWU). Consulte [Rendimiento y escala flexibles con Almacenamiento de datos SQL](../sql-data-warehouse/sql-data-warehouse-performance-scale.md).
- Azure DocumentDB: [Performance level in DocumentDB](../documentdb/documentdb-performance-levels.md).
- Instancia de SQL Server local: [Supervisión y optimización del rendimiento](https://msdn.microsoft.com/library/ms189081.aspx).
- Servidor de archivos local: [Performance Tuning for File Servers](https://msdn.microsoft.com/library/dn567661.aspx)

<!---HONumber=AcomDC_0302_2016-->