<properties 
   pageTitle="Query Performance Insight de Base de datos SQL de Azure" 
   description="La supervisión del rendimiento de las consultas identifica las consultas que más CPU consumen en una base de datos SQL de Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="02/03/2016"
   ms.author="sstein"/>

# Query Performance Insight de Base de datos SQL de Azure


La administración y ajuste del rendimiento de las bases de datos relacionales son tareas difíciles que requieren una gran inversión de tiempo y muchos conocimientos. Query Performance Insight permite dedicar menos tiempo a la solución de problemas de rendimiento de bases de datos, ya que proporciona:

- Información más detallada sobre el consumo de recursos (DTU) de las bases de datos. 
- Las consultas que más CPU consumen, que potencialmente pueden ajustarse para mejorar el rendimiento. 
- La capacidad de profundizar en los detalles de una consulta.

## Requisitos previos

- Query Performance Insight solo está disponible con Base de datos de SQL Azure V12.
- Query Performance Insight requiere que el [almacén de consultas](https://msdn.microsoft.com/library/dn817826.aspx) se ejecuta en la base de datos. El portal le pedirá que active el almacén de consultas si no se está ejecutando todavía.

 
## Permisos

Los siguientes permisos de [control de acceso basado en roles](../active-directory/role-based-access-control-configure.md) se requieren para usar Query Performance Insight:

- Los permisos de **lector**, **propietario**, **colaborador**, **colaborador de base de datos de SQL** o **colaborador de SQL Server** se requieren para ver las consultas y gráficos que más recursos consumen. 
- Los permisos de **propietario**, **colaborador**, **colaborador de base de datos de SQL** o **colaborador de SQL Server** se requieren para ver el texto de la consulta.



## Uso de Query Performance Insight

Query Performance Insight es fácil de usar:

- Revisar la lista de consultas que más recursos consumen. 
- Seleccione una consulta individual para ver sus detalles.
- Haga clic en **Configuración** para personalizar la forma en que se muestran los datos o para mostrar otro período.
- Abra [Asesor de índices](sql-database-index-advisor.md) y compruebe si las recomendaciones están disponibles.



> [AZURE.NOTE] Para proporcionar información sobre el rendimiento de las consultas, es preciso que Almacén de consultas para Base de datos SQL capture un par de horas de datos. Si la base de datos no tiene actividad o Almacén de consultas no estuvo activo durante un período determinado, los gráficos estará vacíos al mostrar dicho período. Puede habilitar el almacén de consulta en cualquier momento si no se está ejecutando.



## Revisión de las consultas que más CPU consumen

En el [portal](http://portal.azure.com), realice estas acciones:

1. Navegue a una base de datos SQL y haga clic en **Query Performance Insight**. 

    ![Query Performance Insight][1]

    Se abre la vista de las principales consultas y aparece la lista de las consultas principales que más CPU consumen.

1. Para obtener más información, haga clic alrededor del gráfico.<br>La línea superior muestra el porcentaje general de DTU de la base de datos, mientras que las barras muestran el porcentaje de CPU consumido por las consultas seleccionadas durante el intervalo seleccionado (por ejemplo, si se selecciona **semana anterior**, cada barra representa un día).

    ![principales consultas][2]

    La cuadrícula inferior representa información agregada para las consultas visibles.

    -	Promedio de CPU por consulta durante el intervalo observable. 
    -	Duración total por consulta.
    -	Número total de ejecuciones para una consulta determinada.


	Seleccione o anule la selección de las consultas individuales para incluirlas o excluirlas del gráfico.


1. Si los datos se quedan obsoletos, haga clic en el botón **Actualizar**.
1. Opcionalmente, haga clic en **Configuración** para personalizar la forma en que se muestran los datos de consumo de CPU o para mostrar otro período.

    ![configuración](./media/sql-database-query-performance/settings.png)

## Visualización de los detalles de las consultas individuales

Para ver los detalles de una consulta:

1. Haga clic en cualquiera de las consultas de la lista de las principales consultas.

    ![detalles](./media/sql-database-query-performance/details.png)

4. Se abre la vista de detalles y el consumo de CPU de las consultas se desglosa por tiempo.
3. Para más información, haga clic alrededor del gráfico.<br>La línea superior muestra el porcentaje general de CPU, mientras que las barras muestran el porcentaje de CPU consumido por la consulta seleccionada.
4. Revise los datos para ver métricas detalladas, entre las que se incluyen la duración, el número de ejecuciones y el porcentaje de uso de los recursos de cada intervalo en los que se ejecutó la consulta.
    
    ![detalles de consulta][3]

1. Opcionalmente, haga clic en **Configuración** para personalizar la forma en que se muestran los datos de consumo de CPU o para mostrar otro período.


## 	Optimización de la configuración del almacén de consultas para obtener información de rendimiento de consultas

Durante el uso de información de rendimiento de consultas, puede que encuentre los siguientes mensajes del almacén de consultas:

- “El almacén de consultas ha alcanzado su capacidad máxima y no va a recopilar nuevos datos”.
- “El almacén de consultas de esta base de datos está en modo de solo lectura y no va a recopilar información de rendimiento”.
- "Los parámetros del almacén de consultas no se establecen de manera óptima para la información de rendimiento de consultas".

Estos mensajes suelen aparecer cuando el almacén de consultas no puede recopilar datos nuevos. Para solucionar este problema tiene dos opciones:

-	Cambiar la directiva de retención y captura del almacén de consultas
-	Aumentar el tamaño del almacén de consultas 
-	Borrar el almacén de consultas

### Directiva de retención y captura recomendada

Hay dos tipos de directivas de retención:

- Basada en tamaño: si se establece en AUTO, los datos se borrarán automáticamente cuando el tamaño casi haya alcanzado el tamaño máximo.
- Basada en tiempo: de forma predeterminada, estableceremos esta opción en 30 días, lo que significa que, si el almacén de consultas se queda sin espacio, eliminará la información de consulta anterior a los 30 días. 

La directiva de capturas se podría establecer como:

- **Todas**: captura todas las consultas. Esta es la opción predeterminada.
- **Automática** : se ignoran las consultas poco frecuentes y las consultas con una duración de ejecución y compilación insignificantes. Los umbrales para la duración del tiempo de ejecución y de compilación y para el recuento de ejecuciones se determinan internamente.
- **Ninguna**: el almacén de consultas deja de capturar consultas nuevas.
	
Se recomienda establecer todas las directivas en AUTO y la directiva de limpieza en 30 días:

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);
    	
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));
    
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Aumentar el tamaño del almacén de consultas. Esto puede realizarse mediante la conexión a una base de datos y la emisión de la consulta siguiente:

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Borrar el almacén de consultas. Tenga en cuenta que esto eliminará toda la información actual del almacén de consultas:

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## Resumen

Query Performance Insight le ayuda a comprender el impacto de la carga de trabajo de las consultas y su relación con el consumo de recursos de base de datos. Con esta característica, conocerá las consultas que más consumen e identificará fácilmente las que deben corregirse antes de que conviertan en un problema. Haga clic en **Información de rendimiento de consultas** en una base de datos para ver las consultas que más recursos (CPU) consumen.




## Pasos siguientes

Las cargas de trabajo de bases de datos son dinámicas y cambian con frecuencia. Supervise las consultas y ajústelas para mejorar el rendimiento.

Para obtener recomendaciones adicionales para mejorar el rendimiento de la base de datos SQL, haga clic en [Asesor de índices](sql-database-index-advisor.md) en la hoja **Información de rendimiento de consultas**.

![Index Advisor](./media/sql-database-query-performance/ia.png)


<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png

<!---HONumber=AcomDC_0224_2016-->