<properties
   pageTitle="Carga de trabajo del almacenamiento de datos"
   description="La elasticidad del servicio Almacenamiento de datos SQL permite aumentar, reducir o pausar la capacidad de proceso mediante el uso de una escala móvil de unidades de almacenamiento de datos (DWU). En este artículo se explican las métricas de almacenamiento de datos y cómo se relacionan con las DWU."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="barbkess;mausher;jrj;sonyama"/>


# Carga de trabajo del almacenamiento de datos
Una carga de trabajo de almacenamiento de datos hace referencia a todas las operaciones que tienen lugar en un almacenamiento de datos. La carga de trabajo del almacenamiento de datos abarca todo el proceso de carga de datos en el almacén, la realización de análisis e informes, la administración de los datos y la exportación de los mismos. La profundidad y amplitud de estos componentes suelen ir en paralelo con el nivel de madurez del almacenamiento de datos.


## ¿No conoce el almacenamiento de datos?
Un almacenamiento de datos es una colección de datos que se carga desde uno o varios orígenes y que se usa para realizar tareas de inteligencia empresarial, por ejemplo, informes y análisis de datos.

Los almacenamientos de datos se caracterizan por consultas que analizan un mayor número de filas, grandes intervalos de datos y devuelven resultados relativamente grandes con fines de creación de análisis e informes. Los almacenamientos de datos también se caracterizan por cargas de datos relativamente grandes frente a pequeñas inserciones, actualizaciones y eliminaciones en el nivel de transacción.

- Un almacenamiento de datos funciona mejor cuando los datos se almacenan de manera que se optimizan las consultas que necesitan analizar grandes cantidades de filas o de intervalos de datos. Este tipo de análisis funciona mejor cuando los datos se almacenan y se buscan por columnas, en lugar de por filas.

>[AZURE.NOTE] El índice de almacén de columnas en memoria, que usa el almacenamiento por columnas, proporciona hasta 10 veces más ganancias de compresión y 100 veces el rendimiento de las consultas, en comparación con los árboles binarios tradicionales, para consultas de informes y análisis. Consideramos los índices de almacén de columnas como el estándar para almacenar y analizar datos de gran tamaño en un almacenamiento de datos.

- Un almacenamiento de datos tiene requisitos diferentes de un sistema que se optimiza para el procesamiento de transacciones en línea (OLTP). El sistema OLTP tiene muchas operaciones de inserción, actualización y eliminación. Estas operaciones buscan en filas específicas de la tabla. Las búsquedas en tablas son más eficaces cuando los datos se almacenan por filas. Los datos se pueden ordenar rápidamente y hacer búsquedas por partes, en un enfoque denominado búsqueda binaria de árbol.


## Carga de datos
La carga de datos es una parte importante de la carga de trabajo del almacenamiento de datos. Las compañías suelen tener un sistema OLTP ocupado que hace el seguimiento de los cambios a lo largo del día, cuando los clientes están generando transacciones comerciales. Periódicamente, en general por la noche durante un período de mantenimiento, las transacciones se mueven o copian en el almacenamiento de datos. Una vez que los datos están en el almacenamiento de datos, se pueden hacer análisis y tomar decisiones empresariales sobre los datos.

- Tradicionalmente, el proceso de carga se denomina ETL (extracción, transformación y carga). Normalmente, los datos deben transformarse para mantener la coherencia con otros datos del almacenamiento de datos. Anteriormente, las empresas usaban servidores dedicados de ETL para estas transformaciones. Ahora, gracias al rápido procesamiento masivo paralelo, es posible cargar datos en Almacenamiento de datos SQL en primer lugar y, a continuación, hacer las transformaciones. Este proceso se denomina extraer, cargar y transformar (ELT) y se está convirtiendo en un nuevo estándar para la carga de trabajo de almacenamiento de datos.

> [AZURE.NOTE] Con SQL Server CTP2, ahora es posible realizar análisis en tiempo real en una tabla OLTP. Esto no reemplaza la necesidad de un almacenamiento de datos para almacenar y analizar datos, pero ofrece un modo de hacer el análisis en tiempo real.

### Consultas de informes y análisis
Las consultas de informes y análisis a menudo se clasifican como pequeñas, medianas y grandes en función de diversos criterios, pero normalmente están basadas en el tiempo. En la mayoría de los almacenamientos de datos, hay una carga de trabajo mixta de ejecución rápida frente a consultas de larga ejecución. En cada caso, es importante determinar esta mezcla y determinar su frecuencia (cada hora, diariamente, a fin de mes, al final del trimestre, etcétera). Es importante comprender que la carga de trabajo de consulta mixta, junto con la simultaneidad, lleva al planeamiento de un almacenamiento de datos con la capacidad adecuada.

- El planeamiento de la capacidad puede ser una tarea compleja para una carga de trabajo de consulta mixta, especialmente cuando se necesita mucho tiempo para agregar capacidad al almacenamiento de datos. Almacenamiento de datos SQL elimina la urgencia del planeamiento de capacidad, ya que es posible aumentar y reducir la capacidad de proceso en cualquier momento, puesto que el tamaño del almacenamiento y la capacidad de proceso se ajustan de forma independiente.

### Administración de datos
La administración de datos es importante, sobre todo cuando se sabe que es posible agotar el espacio en disco en un futuro próximo. Normalmente, los almacenamientos de datos dividen los datos en intervalos significativos que se almacenan como particiones en una tabla. Todos los productos basados en SQL Server permiten mover particiones dentro y fuera de la tabla. Esta conmutación de las particiones permite mover los datos más antiguos a un almacenamiento menos costoso y mantener los datos más recientes disponibles en el almacenamiento en línea.

- Los índices de almacén de columnas admiten tablas con particiones. Para los índices de almacén de columnas, las tablas con particiones se utilizan para la administración y el archivado de los datos. En las tablas almacenadas por filas, las particiones desempeñan una función mayor en el rendimiento de las consultas.  

- PolyBase desempeña un papel importante en la administración de datos. Con PolyBase, existe la opción de archivar datos antiguos en almacenamiento de blobs de Azure o de Hadoop. Esto proporciona muchas opciones, ya que los datos aún están en línea. Es posible que se tarde más tiempo en recuperar datos de Hadoop, pero la contrapartida en tiempo de recuperación puede ser mayor que el costo de almacenamiento.

### Exportación de datos
Un modo de hacer que los datos estén disponibles para la creación de informes y análisis es enviar los datos desde el almacenamiento de datos hasta los servidores dedicados para la ejecución de informes y análisis. Estos servidores se denominan puestos de datos. Por ejemplo, podría procesar previamente los datos de los informes y, a continuación, exportarlos del almacén de datos a varios servidores de todo el mundo, para ponerlos a disposición de una gran cantidad de clientes y analistas.

- Para generar informes, cada noche podría rellenar los servidores de informes de solo lectura con una instantánea de los datos diarios. Esto proporciona más ancho de banda para los clientes, a la vez que reduce la necesidad de recursos de proceso en el almacenamiento de datos. Desde el punto de vista de la seguridad, los puestos de datos permiten reducir el número de usuarios que tienen acceso al almacenamiento de datos.
- Para los análisis, se puede generar un cubo de análisis en el almacenamiento de datos y ejecutar el análisis, o bien procesar previamente los datos y exportarlos al servidor de análisis para análisis posteriores.

## Pasos siguientes
Para empezar a desarrollar el almacenamiento de datos, vea [Introducción al desarrollo][].

## Libros
[Big Data Warehousing](https://www.manning.com/books/big-data-warehousing) (Almacenamiento de macrodatos) por Karthik Ramachandran, Istvan Szededi y Richard L. Saltzer (Manning Publications). [Capítulo 1](https://manning-content.s3.amazonaws.com/download/e/3d94acd-9512-46c8-b0b0-8c9c3c6a303b/BDW_MEAP_ch1.pdf)

<!--Image references-->

<!--Article references-->
[Introducción al desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other web references-->

<!---HONumber=AcomDC_0309_2016-->