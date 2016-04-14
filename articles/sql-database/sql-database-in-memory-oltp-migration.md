<properties
	pageTitle="In-Memory OLTP mejora el rendimiento de transacciones SQL | Microsoft Azure"
	description="Adopción de In-Memory OLTP para mejorar el rendimiento transaccional en una Base de datos SQL ya existente."
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jhubbard"
	editor="MightyPen"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="jodebrui"/>


# Uso de In-Memory OLTP (vista previa) para mejorar el rendimiento de la aplicación de Base de datos SQL de Azure

[In-Memory OLTP](sql-database-in-memory.md) puede usarse para mejorar el rendimiento de la carga de trabajo de OLTP en las bases de datos SQL de Azure [Premium](sql-database-service-tiers.md) sin aumentar el nivel de rendimiento.

Siga estos pasos para adoptar In-Memory OLTP en la base de datos existente.

## Paso 1: Asegurarse de que la base de datos Premium admite In-Memory OLTP

Las bases de datos Premium creadas en noviembre de 2015 o después son compatibles con la característica In-Memory. Puede determinar si la base de datos Premium admite la característica In-Memory mediante la ejecución de la siguiente instrucción de Transact-SQL. Se admite In-Memory si el resultado devuelto es 1 (no 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* son las siglas de *Extreme Transaction Processing*.

Si se debe mover la base de datos a una nueva base de datos V12 Premium, puede usar las técnicas siguientes para exportar y después importar los datos.

#### Pasos de exportación

Exporte la base de datos de producción a un bacpac, mediante:

- La funcionalidad de [exportación](sql-database-export.md) en el [portal](https://portal.azure.com/).

- La funcionalidad **Exportar aplicación de capa de datos** en un [SSMS.exe actualizado](http://msdn.microsoft.com/library/mt238290.aspx) (SQL Server Management Studio).
 1. En el **Explorador de objetos**, expanda el nodo **Bases de datos**.
 2. Haga clic con el botón derecho en el nodo de la base de datos.
 3. Haga clic en **Tareas** > **Exportar aplicación de capa de datos**.
 4. Trabaje en la ventana del asistente que se muestra.


#### Pasos de importación

Importe el archivo bacpac en una nueva Base de datos Premium.

1. En el [Portal](https://portal.azure.com/) de Azure,
 - Vaya al servidor.
 - Seleccione la opción [Importar base de datos](sql-database-import.md).
 - Seleccione un plan de tarifa Premium.

2. Use SSMS para importar el bacpac:
 - En el **Explorador de objetos**, haga clic con el botón derecho en el nodo **Bases de datos**.
 - Haga clic en **Importar aplicación de capa de datos**.
 - Trabaje en la ventana del asistente que se muestra.


## Paso 2: identificar objetos para migrar a In-Memory OLTP

SSMS incluye un informe de **información general del análisis de rendimiento de transacciones** que se pueden ejecutar en una base de datos con una carga de trabajo activo. El informe identifica las tablas y los procedimientos almacenados que son candidatos para la migración a In-Memory OLTP.

En SSMS, para generar el informe: - En el **Explorador de objetos**, haga clic con el botón derecho en el nodo de la base de datos. - Haga clic en **Informes** > **Informes estándar** > **Información general de análisis de rendimiento de transacciones**.

Para obtener más información, consulte [Determinar si una tabla o un procedimiento almacenado se debe pasar a In-Memory OLTP](http://msdn.microsoft.com/library/dn205133.aspx).


## Paso 3: Crear una base de datos de prueba comparables

Supongamos que el informe indica que la base de datos tiene una tabla que se beneficiaría de convertirse en una tabla optimizada en memoria. Se recomienda que la pruebe primero para confirmar la indicación.

Necesitará una copia de prueba de la base de datos de producción. La base de datos de prueba debe estar en el mismo nivel de servicio que la base de datos de producción.

Para facilitar las pruebas, ajuste la base de datos de prueba de la forma siguiente:

1. Conéctese a la base de datos de prueba con SSMS.

2. Para evitar la necesidad de la opción WITH (SNAPSHOT) en las consultas, establezca la opción de base de datos tal como se muestra en la siguiente instrucción T-SQL: ```
ALTER DATABASE CURRENT
	SET
		MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
```


## Paso 4: Migrar tablas

Debe crear y rellenar una copia optimizada en memoria de la tabla que desea probar. Se puede crear mediante:

- El práctico Asistente para optimización de memoria en SSMS.
- T-SQL manual.


#### Asistente para optimización de memoria en SSMS

Para usar esta opción de migración:

1. Conéctese a la base de datos de prueba con SSMS.

2. En el **Explorador de objetos**, haga clic con el botón derecho en la tabla y después haga clic en **Asistente de optimización de memoria**.
 - Se muestra el asistente **Asesor del optimizador de memoria de tablas**.

3. En el asistente, haga clic en **Validación de migración** (o en el botón **Siguiente**) para ver si la tabla tiene las características no admitidas en las tablas optimizadas en memoria. Para más información, consulte:
 - La *lista de comprobación de optimización de memoria* en [Asesor de optimización de memoria](http://msdn.microsoft.com/library/dn284308.aspx).
 - [Construcciones de transact-SQL no admitidas por In-Memory OLTP](http://msdn.microsoft.com/library/dn246937.aspx).
 - [Migración a In-Memory OLTP](http://msdn.microsoft.com/library/dn247639.aspx).

4. Si la tabla no tiene características no admitidas, el asesor puede realizar el esquema real y la migración de datos.


#### T-SQL manual

Para usar esta opción de migración:

1. Conéctese a la base de datos de prueba mediante SSMS (o una utilidad similar).

2. Obtenga el script T-SQL completo para la tabla y sus índices.
 - En SSMS, haga clic con el botón derecho en el nodo de tabla.
 - Haga clic en **Incluir tabla como** > **Crear en** > **Nueva ventana de consulta**.

3. En la ventana de script, agregue WITH (MEMORY\_OPTIMIZED = ON) a la instrucción CREATE TABLE.

4. Si hay un índice CLUSTERD, cámbielo a NONCLUSTERED.

5. Cambie el nombre de la tabla existente mediante SP\_RENAME.

6. Cree la nueva copia de la tabla optimizada en memoria mediante la ejecución del script CREATE TABLE editado.

7. Copie los datos en la tabla optimizada en memoria mediante INSERT... SELECT * INTO:
	
```
INSERT INTO <new_memory_optimized_table>
		SELECT * FROM <old_disk_based_table>;
```


## Paso 5 (opcional): Migrar los procedimientos almacenados

La característica In-Memory también puede modificar un procedimiento almacenado para mejorar el rendimiento.


### Consideraciones con procedimientos almacenados compilados de forma nativa

Un procedimiento almacenado compilado de forma nativa debe tener las siguientes opciones en su cláusula T-SQL WITH:

- NATIVE\_COMPILATION

- SCHEMABINDING: son las tablas cuyas definiciones de columna no puede cambiar de ninguna forma el procedimiento almacenado que pueda afectar al procedimiento almacenado, a menos que quite el procedimiento almacenado.


Un módulo nativo debe usar un gran [bloque ATOMIC](http://msdn.microsoft.com/library/dn452281.aspx) para la administración de transacciones. No hay ningún rol para una instrucción BEGIN TRANSACTION o ROLLBACK TRANSACTION explícita. Si el código detecta una infracción de una regla de negocio, puede finalizar el bloque ATOMIC con una instrucción [THROW](http://msdn.microsoft.com/library/ee677615.aspx).


### CREATE PROCEDURE típico para compilar de forma nativa

Normalmente el T-SQL para crear un procedimiento almacenado compilado de forma nativa es similar a la siguiente plantilla:

```
CREATE PROCEDURE schemaname.procedurename
	@param1 type1, …
	WITH NATIVE_COMPILATION, SCHEMABINDING
	AS
		BEGIN ATOMIC WITH
			(TRANSACTION ISOLATION LEVEL = SNAPSHOT,
			LANGUAGE = N'your_language__see_sys.languages'
			)
		…
		END;
```

- Para TRANSACTION\_ISOLATION\_LEVEL, SNAPSHOT es el valor más común para el procedimiento almacenado compilado de forma nativa. Sin embargo, también se admite un subconjunto de los demás valores:
 - REPEATABLE READ
 - SERIALIZABLE


- El valor LANGUAGE debe estar presente en la vista sys.languages.


### Migración de un procedimiento almacenado

Los pasos de migración son los siguientes:


1. Obtenga el script CREATE PROCEDURE para el procedimiento almacenado regular interpretado.

2. Vuelva a escribir el encabezado para que coincida con la plantilla anterior.

3. Determine si el código T-SQL del procedimiento almacenado usa las características que no se admiten para los procedimientos almacenados compilados de forma nativa. Implemente soluciones alternativas si es necesario.
 - Para obtener información detallada, consulte [Problemas de migración para los procedimientos almacenados compilados de forma nativa](http://msdn.microsoft.com/library/dn296678.aspx).

4. Cambie el nombre del procedimiento almacenado anterior por SP\_RENAME. O bien, simplemente quítelo con la instrucción DROP.

5. Ejecute el script CREATE PROCEDURE T-SQL editado.


## Paso 6: Ejecutar la carga de trabajo en la prueba

Ejecutar una carga de trabajo en la base de datos de prueba es similar a la carga de trabajo que se ejecuta en la base de datos de producción. Esto debería mostrar la mejora del rendimiento conseguida mediante el uso de la característica In-Memory para tablas y procedimientos almacenados.

Los atributos principales de la carga de trabajo son los siguientes:

- Número de conexiones simultáneas.

- Relación de lectura/escritura.


Para personalizar y ejecutar la carga de trabajo de prueba, considere el uso de la práctica herramienta ostress.exe, que se muestra [aquí](sql-database-in-memory.md).


Para minimizar la latencia de red, ejecute la prueba en la misma región geográfica de Azure donde existe la base de datos.


## Paso 7: Supervisión postimplementación

Considere la posibilidad de supervisar los efectos de rendimiento de las implementaciones In-Memory en producción:

- [Supervisión del almacenamiento In-Memory](sql-database-in-memory-oltp-monitoring.md).

- [Supervisión de Base de datos SQL de Azure con vistas de administración dinámica](sql-database-monitoring-with-dmvs.md)


## Vínculos relacionados

- [In-Memory OLTP (optimización In-Memory)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Introducción a los procedimientos almacenados compilados de forma nativa](http://msdn.microsoft.com/library/dn133184.aspx)

- [Asesor de optimización en memoria](http://msdn.microsoft.com/library/dn284308.aspx)

<!---HONumber=AcomDC_0224_2016-->