<properties
	pageTitle="Guía de rendimiento de Base de datos SQL de Azure"
	description="Este tema proporciona instrucciones para ayudarle a determinar qué nivel de servicio es el adecuado para su aplicación y proporciona recomendaciones para optimizar la aplicación y para obtener el máximo partido de Base de datos SQL de Azure."
	services="sql-database"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="11/03/2015"
	ms.author="jroth" />

# Guía de rendimiento de Base de datos SQL de Azure

## Información general 

Base de datos SQL de Microsoft Azure tiene tres [niveles de servicio](sql-database-service-tiers.md), Básico, Estándar y Premium. Todos aíslan de forma estricta el recurso proporcionado a Base de datos SQL Azure y garantizan un rendimiento predecible. El rendimiento garantizado para la base de datos aumenta de Básico, pasando por Estándar, hasta Premium.

>[AZURE.NOTE] Los niveles de servicio Business y Web se retirarán en septiembre de 2015. Para obtener más información, consulte [Preguntas más frecuentes sobre la retirada de las ediciones Web y Business](https://msdn.microsoft.com/library/azure/dn741330.aspx). Para obtener información detallada sobre cómo actualizar las bases de datos Web o Business existentes a los nuevos niveles de servicio, consulte [Actualización de las bases de datos SQL Web o Business a niveles de servicio nuevos](sql-database-upgrade-new-service-tiers.md).

Este documento proporciona instrucciones para ayudarle a determinar qué nivel de servicio es el adecuado para su aplicación y proporciona recomendaciones para optimizar la aplicación para obtener el máximo partido de Base de datos SQL de Azure.

>[AZURE.NOTE] Este artículo se centra en la guía de rendimiento para bases de datos única en Base de datos SQL. Para obtener instrucciones sobre rendimiento relativa a los grupos de bases de datos elásticas, consulte [Consideraciones de precio y rendimiento para un grupo de bases de datos elásticas](sql-database-elastic-pool-guidance.md). Sin embargo, tenga en cuenta que muchas de las recomendaciones de optimización de este artículo para una base de datos única pueden aplicarse a las bases de datos de un grupo elástico con ventajas de rendimiento similares.

**Autores:** Conor Cunningham, Kun Cheng, Ene Engelsberg

**Revisores técnicos:** Morgan Oslake, Joanne Marone, Keith Elmore, José Batista-Neto, Rohit Nayak

## Información general sobre Base de datos SQL de Azure

Para comprender cómo los niveles de servicio Básico, Estándar y Premium mejoran el servicio Base de datos SQL de Azure, es conveniente tener un buen conocimiento general de Base de datos SQL de Azure. Puede elegir Base de datos SQL de Azure por varias razones. Una razón es evitar el largo ciclo de compra e instalación de hardware. Base de datos SQL de Azure permite crear y quitar bases de datos sobre la marcha sin tener que esperar que se apruebe un pedido de compra, que lleguen los equipos, que se actualicen los sistemas de alimentación y refrigeración o se realice la instalación. Microsoft aborda estos desafíos y reduce en gran medida el tiempo necesario para pasar de la idea a la solución gracias al aprovisionamiento previo del hardware según la demanda agregada de cada uno de nuestros centros de datos. Esto puede ahorrar semanas o meses a su negocio ya que evita tener que comprar e implementar el hardware manualmente.

Microsoft también incluye muchas características de administración automática en Base de datos SQL de Azure, tales como alta disponibilidad automática y administración integrada.

### Alta disponibilidad automática 
 Base de datos SQL de Azure mantiene al menos tres réplicas de cada base de datos de usuario e incorpora lógica para confirmar automáticamente cada cambio de forma sincrónica en un quórum de las réplicas. Esto garantiza que un error único en un equipo no ocasionará la pérdida de datos. Además, cada réplica se coloca en diferentes bastidores de hardware para que las interrupciones de la alimentación o las conmutaciones de red no afecten a la base de datos. Por último, hay lógica para regenerar las réplicas automáticamente si se pierde un equipo para que el sistema conserve automáticamente las propiedades de mantenimiento deseadas aunque un equipo esté en mal estado. Estos mecanismos impiden el laborioso proceso que se necesita actualmente para instalar y configurar soluciones de alta disponibilidad. Tener una solución de alta disponibilidad preconfigurada para los datos suprime otro quebradero de cabeza que es la creación de una solución de base de datos crítica mediante técnicas tradicionales.

### Administración integrada 
 Base de datos SQL de Azure se ejecuta como un servicio. Esto significa que hay objetivos de tiempo de actividad definidos para cada base de datos, lo que evita los largos períodos de inactividad por mantenimiento. Microsoft proporciona una solución de un único proveedor para el servicio, lo que significa que solo hay una compañía a la que llamar en caso de que surja algún problema. Microsoft también actualiza continuamente el servicio, agrega características y capacidad, y busca maneras de mejorar su experiencia en todas las actualizaciones que hacemos. Las actualizaciones se realizan de forma transparente y sin tiempo de inactividad, lo que significa que se integran en nuestro mecanismo normal de conmutación por error de alta disponibilidad. Esto permite aprovechar de inmediato las nuevas características cada vez que se anuncia su disponibilidad en lugar de tener que esperar a que un servidor se actualice durante un tiempo de inactividad futuro.

Todas estas funcionalidades se entregan en todos los niveles de servicio, comenzando con un precio inicial bajo al mes, que es mucho menos que lo que le costaría adquirir y ejecutar su propio servidor, y significa que incluso el proyecto más pequeño puede sacar partido de Azure sin gastar mucho dinero.

## ¿Qué diferencia hay en los niveles de servicio?
Información general sobre los niveles de servicio Básico, Estándar y Premium. Cada nivel de servicio tiene uno o más niveles de rendimiento que proporcionan la capacidad para ejecutar las bases de datos de una manera predecible. La capacidad se describe en [Unidades de transacción de base de datos (DTU)](sql-database-technical-overview.md#understand-dtus).

El nivel de servicio Básico está diseñado para predecir mejor el rendimiento de cada base de datos hora a hora. La DTU de una base de datos básica está diseñada para proporcionar recursos suficientes para que bases de datos pequeñas sin solicitudes simultáneas tengan un buen rendimiento.

El nivel de servicio Estándar ofrece una capacidad mejorada para predecir el rendimiento y aumenta el nivel para bases de datos con solicitudes simultáneas, tales como aplicaciones web y de grupo de trabajo. El uso de una base de datos de nivel de servicio Estándar permite ajustar el tamaño de su aplicación de base de datos en función de un rendimiento predecible minuto a minuto.

La capacidad más destacada con respecto al rendimiento del nivel de servicio Premium es el rendimiento predecible segundo a segundo para cada base de datos Premium. Usar el nivel de servicio Premium permite ajustar el tamaño de la aplicación de base de datos en función de la carga máxima de esa base de datos y elimina aquellos casos en los que la variación de rendimiento puede hacer que consultas pequeñas tarden más tiempo del esperado en operaciones sensibles a la latencia. Este modelo puede simplificar en gran medida los ciclos de desarrollo y validación del producto necesarios para aquellas aplicaciones que necesitan hacer declaraciones firmes sobre las necesidades máximas de recursos, variación de rendimiento o latencia de las consultas.

De forma similar al nivel de servicio Estándar, el nivel de servicio Premium ofrece diferentes niveles de rendimiento en función del aislamiento deseado para un cliente.

La configuración del nivel de rendimiento en los niveles de servicio Estándar y Premium permite pagar solo por la capacidad que necesita y ampliar o reducir la capacidad a medida que cambie una carga de trabajo. Por ejemplo, si la carga de trabajo de la base de datos está ocupado durante la temporada de compras de vuelta al colegio, puede aumentar el nivel de rendimiento de la base de datos durante ese período de tiempo y reducirlo una vez que termine. Esto permite optimizar su entorno de nube para la estacionalidad de su negocio y así minimizar lo que paga. Este modelo también funciona bien para los ciclos de lanzamiento de productos de software. Un equipo de pruebas puede asignar capacidad mientras realiza series de pruebas y liberar esa capacidad una vez completadas las pruebas. Estas solicitudes de capacidad encajan bien con el modelo de pago por la capacidad que se necesita y evitar gastar en recursos dedicados que se usan con poca frecuencia. Esto proporciona una experiencia de rendimiento mucho más cercana al modelo tradicional de hardware dedicado que muchos clientes de Microsoft han usado con SQL Server. Esto debe permitir que un conjunto mayor de aplicaciones se ejecute más fácilmente en Base de datos SQL de Azure.

Para obtener más información sobre los niveles de servicio y los niveles de rendimiento, consulte [Niveles de servicio y niveles de rendimiento de Base de datos SQL de Azure](sql-database-service-tiers.md).

## Razones para usar los niveles de servicio

Aunque cada carga de trabajo puede variar, el propósito de los niveles de servicio es proporcionar una previsibilidad de alto rendimiento en diversos niveles de rendimiento. Permite a los clientes con elevados requisitos de recursos para sus bases de datos trabajar en un entorno informático más dedicado.

### Casos de uso del nivel de servicio Básico:

- **Introducción a Base de datos SQL de Azure**: las aplicaciones en desarrollo a menudo no necesitan altos niveles de rendimiento. Las bases de datos básicas proporcionan un entorno ideal para el desarrollo de bases de datos a un bajo precio.
- **Base de datos con un solo usuario**: las aplicaciones que asocian un solo usuario a una base de datos normalmente no tienen grandes requisitos de simultaneidad y rendimiento. Las aplicaciones con estos requisitos son candidatas al nivel de servicio Básico.

### Casos de uso del nivel de servicio Estándar:

- **Base de datos con varias solicitudes simultáneas**: las aplicaciones que dan servicio a más de un usuario a la vez, como los sitios web con tráfico moderado o las aplicaciones departamentales que requieren una mayor cantidad de recursos, son buenas candidatas para el nivel de servicio Estándar.

### Casos de uso del nivel de servicio Premium:

- **Carga máxima elevada**: una aplicación que requiere mucha CPU, memoria o E/S para completar sus operaciones. Por ejemplo, si una operación de base de datos se sabe que consume varios núcleos de CPU durante un largo período de tiempo, es una candidata para el uso de bases de datos Premium.
- **Muchas solicitudes simultáneas**: algunas aplicaciones de base de datos atienden muchas solicitudes simultáneas, por ejemplo, cuando dan servicio a un sitio web con alto volumen de tráfico. Los niveles de servicio Básico y Estándar tienen límites en la cantidad de solicitudes simultáneas. Las aplicaciones que requieren más conexiones necesitarían elegir un tamaño de reserva suficiente para controlar el número máximo de solicitudes necesarias.
- **Baja latencia**: algunas aplicaciones necesitan garantizar una respuesta de la base de datos en un tiempo mínimo. Si se llama a un procedimiento almacenado determinado como parte de una operación de cliente más amplia, podría ser necesario volver de esa llamada en no más de 20 milisegundos el 99 % del tiempo. Este tipo de aplicación se beneficiará de las bases de datos Premium para asegurarse de que hay capacidad de proceso disponible.

El nivel exacto que necesitará depende de los requisitos de carga máxima para cada dimensión de recursos. Algunas aplicaciones pueden usar pocas cantidades de un recurso pero tener requisitos significativos en otro.

Para obtener más información sobre los niveles de servicio, consulte [Niveles de servicio y niveles de rendimiento de Base de datos SQL de Azure](sql-database-service-tiers.md).

## Límites y capacidades de nivel de servicio
Cada nivel de servicio y nivel de rendimiento están asociados a distintos límites y características de rendimiento. La tabla siguiente describe estas características para una sola base de datos.

[AZURE.INCLUDE [Tabla de niveles de servicio de datos de la Base de datos SQL](../../includes/sql-database-service-tiers-table.md)]

Las secciones siguientes proporcionan más información sobre cada área de la tabla anterior.

### Tamaño máximo de la base de datos

**Tamaño máximo de la base de datos** es simplemente el límite del tamaño de la base de datos en GB.

### DTU

**DTU** son las unidades de transacción de Base de datos. Es la unidad de medida en Base de datos SQL que representa la potencia relativa de bases de datos en base a una medida real: la transacción de base de datos. Consiste en tomar un conjunto de operaciones que son típicas de una solicitud de procesamiento de transacción en línea (OLTP), y medirlas en base a cuántas transacciones se podrían completar por segundo en condiciones de carga total. Para obtener más información sobre las DTU, consulte [Descripción de las DTU](sql-database-technical-overview.md#understand-dtus). Para obtener más información sobre cómo se midieron las DTU, consulte la [Información general sobre la prueba comparativa](sql-database-benchmark-overview.md).

### Restauración a un momento dado

**Restauración a un momento dado** es la capacidad de restaurar la base de datos a un momento anterior en el tiempo. El nivel de servicio determina el número de días que puede retroceder en el tiempo. Para más información consulte [Recuperar una Base de datos SQL de Azure de un error de usuario](sql-database-user-error-recovery.md).

### Recuperación ante desastres

**Recuperación ante desastres** hace referencia a la capacidad de recuperarse de una interrupción en la Base de datos SQL principal.

*Restauración geográfica* está disponible para todos los niveles de servicio sin ningún costo adicional. En el caso de una interrupción, puede usar la copia de seguridad con redundancia geográfica más reciente para restaurar la base de datos en cualquier región de Azure.

La replicación geográfica activa y la estándar proporcionan similares características de recuperación ante desastres, pero con un objetivo de punto de recuperación (RPO) mucho menor. Por ejemplo, con la restauración geográfica, el RPO es de menos de una hora (es decir, la copia de seguridad puede ser de hasta una hora antes). Pero en la replicación geográfica , el RPO es inferior a 5 segundos.

Para obtener más información, consulte [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md).

### Almacenamiento máximo de In-Memory OLTP
**Almacenamiento máximo de In-Memory OLTP** se refiere a la cantidad máxima de almacenamiento disponible en la [vista previa de In-Memory OLTP](sql-database-in-memory.md) para bases de datos Premium. También se conoce a veces como *almacenamiento en memoria de XTP*. Puede usar el Portal de Azure clásico o la vista **sys.dm\_db\_resource\_stats** para supervisar el uso del almacenamiento de In-Memory. Para obtener más información sobre la supervisión, consulte [Supervisión del almacenamiento de In-Memory OLTP](sql-database-in-memory-oltp-monitoring.md).

>[AZURE.NOTE] La vista previa de In-Memory OLTP actualmente admite solamente bases de datos únicas y no bases de datos dentro de grupos de bases de datos elásticas.

### Máximo de solicitudes simultáneas

**Máximo de solicitudes simultáneas** se trata del número máximo de solicitudes simultáneas de usuario/aplicación que se ejecutan al mismo tiempo en la base de datos. Para ver el número de solicitudes simultáneas, ejecute la siguiente consulta Transact-SQL en la Base de datos SQL:

	SELECT COUNT(*) AS [Concurrent_Requests] 
	FROM sys.dm_exec_requests R

Si está analizando la carga de trabajo de una base de datos de SQL Server local, debería modificar esta consulta para filtrar en la base de datos específica que se va a analizar. Por ejemplo, si tiene una base de datos local denominado MyDatabase, la siguiente consulta Transact-SQL devolverá el número de solicitudes simultáneas en esa base de datos.

	SELECT COUNT(*) AS [Concurrent_Requests] 
	FROM sys.dm_exec_requests R
	INNER JOIN sys.databases D ON D.database_id = R.database_id
	AND D.name = 'MyDatabase'

Tenga en cuenta que se trata simplemente de una instantánea en un solo punto en el tiempo. Para obtener una mejor comprensión de la carga de trabajo, tendrá que recopilar muchas muestras durante un periodo de tiempo para comprender los requisitos de solicitudes simultáneas.

### Máximo de inicios de sesión simultáneos

**Máximo de inicios de sesión simultáneos** representa el límite del número de usuarios o de aplicaciones que intentan iniciar sesión en la base de datos al mismo tiempo. Tenga en cuenta que, aunque estos clientes usen la misma cadena de conexión, el servicio autentica cada inicio de sesión. Así que si diez usuarios se conectan todos de forma simultanea a la base de datos con el mismo nombre de usuario y contraseña, serán diez conexiones simultáneas. Este límite se aplica solo a la duración del inicio de sesión y autenticación. Así que si los mismos diez usuarios se conectan en secuencia a la base de datos, el número de inicios de sesión simultáneos nunca será superior a uno.

>[AZURE.NOTE] Este límite no se aplica actualmente a las bases de datos en grupos de bases de datos elásticas.

No hay ninguna consulta o DMV que pueda mostrar los recuentos o el historial de los inicios de sesión simultáneos. Puede analizar los patrones de usuario y aplicación para hacerse una idea de la frecuencia de los inicios de sesión. También podría ejecutar cargas reales en un entorno de prueba para asegurarse de que no está alcanzando este u otros límites descritos en este tema.

### Máximo de sesiones

**Máximo de sesiones** es el número máximo de conexiones simultáneas abiertas con la base de datos. Cuando un usuario inicia sesión, se establece una sesión y permanece activa hasta que la sesión expire o se cierre. Para ver el número actual de sesiones activas, ejecute la siguiente consulta Transact-SQL en la Base de datos SQL:

	SELECT COUNT(*) AS [Sessions]
	FROM sys.dm_exec_connections

Si va a analizar una carga de trabajo de SQL Server local, modifique la consulta para centrarse en una base de datos específica. Esto le ayudará a determinar las posibles necesidades de sesión para esa base de datos si la moviese a Base de datos SQL Azure.

	SELECT COUNT(*)  AS [Sessions]
	FROM sys.dm_exec_connections C
	INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
	INNER JOIN sys.databases D ON (D.database_id = S.database_id)
	WHERE D.name = 'MyDatabase'

Aquí también, las consultas devuelven el recuento de un punto en el tiempo, por ello recopilar varias muestras durante un periodo de tiempo le ayuda a comprender mejor el uso de las sesiones.

Para el análisis de Base de datos SQL, también puede consultar **sys.resource\_stats** a fin de obtener estadísticas históricas sobre las sesiones usando la columna **active\_session\_count**. En la siguiente sección de supervisión se proporciona más información sobre cómo usar esta vista.

## Supervisión del uso de recursos
Hay dos vistas que permiten supervisar el uso de recursos para una Base de datos SQL con respecto a su nivel de servicio:

- [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [Sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx)

>[AZURE.NOTE] También es posible usar el Portal de Azure clásico para ver el uso de los recursos. Para obtener un ejemplo, consulte [Niveles de servicio. Supervisión del rendimiento](sql-database-service-tiers.md#monitoring-performance).

### Uso de sys.dm\_db\_resource\_stats
La vista [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) existe en cada Base de datos SQL y proporciona datos sobre el uso de recursos recientes en relación con el nivel de servicio. Porcentajes medios para CPU, E/S de datos, escritura de registros y memoria, se registran cada 15 segundos y se mantienen durante una hora.

Dado que esta vista proporciona una panorámica más granular del uso de recursos, debe usar primero **sys.dm\_db\_resource\_stats ** para cualquier análisis o la solución de problemas de estado actual. Por ejemplo, la consulta siguiente muestra el uso de recursos promedio y máximo para la base de datos actual durante la última hora:

	SELECT  
	    AVG(avg_cpu_percent) AS 'Average CPU Utilization In Percent', 
	    MAX(avg_cpu_percent) AS 'Maximum CPU Utilization In Percent', 
	    AVG(avg_data_io_percent) AS 'Average Data IO In Percent', 
	    MAX(avg_data_io_percent) AS 'Maximum Data IO In Percent', 
	    AVG(avg_log_write_percent) AS 'Average Log Write Utilization In Percent', 
	    MAX(avg_log_write_percent) AS 'Maximum Log Write Utilization In Percent', 
	    AVG(avg_memory_usage_percent) AS 'Average Memory Usage In Percent', 
	    MAX(avg_memory_usage_percent) AS 'Maximum Memory Usage In Percent' 
	FROM sys.dm_db_resource_stats;  

Para otras consultas, consulte los ejemplos de [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### Uso de sys.resource\_stats

La vista [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) en la base de datos **maestra** proporciona información adicional para supervisar el uso del rendimiento de Base de datos SQL dentro de su nivel de rendimiento y nivel de servicio específicos. Los datos se recopilan cada cinco minutos y se mantienen durante aproximadamente 14 días. Esta vista es más útil para realizar análisis históricos a largo plazo sobre el uso de recursos de Base de datos SQL.

El siguiente gráfico muestra el uso de recursos de CPU para la base de datos Premium con un nivel de rendimiento P2 durante cada hora en una semana. Este gráfico en concreto empieza el lunes, muestra 5 días laborables y, a continuación, un fin de semana donde hay mucha menos actividad en la aplicación.

![Uso de recursos de Base de datos SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Según los datos, esta base de datos tiene actualmente una carga máxima de CPU superior al 50 % de uso de la CPU respecto al nivel de rendimiento P2 (mediodía del martes). Si la CPU es el factor dominante en el perfil de recursos de la aplicación, puede decidir que P2 es el nivel de rendimiento adecuado para garantizar que la carga de trabajo siempre encaje. Si se espera que una aplicación crezca con el tiempo, tiene sentido permitir cierta reserva de recursos adicional para que la aplicación no supere nunca el límite superior. Esto le ayudará a evitar errores visibles para el cliente debidos a que la base de datos no tiene suficiente capacidad para procesar las solicitudes de manera eficiente, especialmente en entornos sensibles a la latencia (como una base de datos que da soporte a una aplicación que dibuja páginas web basándose en los resultados de las llamadas de base de datos).

Merece la pena mencionar que otros tipos de aplicaciones pueden interpretar el mismo gráfico de manera diferente. Por ejemplo, si una aplicación intentara procesar datos de nóminas todos los días y tuviera el mismo gráfico, este tipo de modelo de "trabajo por lotes" podría funcionar bien en un nivel de rendimiento P1. El nivel de rendimiento P1 tiene 100 DTU en comparación con las 200 DTU del nivel de rendimiento P2. Esto significa que el nivel de rendimiento P1 proporciona la mitad de rendimiento que el nivel de rendimiento P2. Hasta un 50 % de uso de CPU en P2 es igual al 100 % de uso de CPU en P1. Siempre que la aplicación no agote los tiempos de espera, quizás no importe si un trabajo grande tarda 2 o 2,5 horas en completarse siempre que se lleve a cabo hoy. Una aplicación en esta categoría puede usar probablemente un nivel de rendimiento P1. Puede aprovechar el hecho de que hay períodos de tiempo durante el día en que el uso de recursos es menor, lo que significa que las "cargas elevadas" podrían retrasarse a uno de esos momentos más tarde. El nivel de rendimiento P1 puede ser adecuado para este tipo de aplicación (y ahorrar dinero) siempre que los trabajos puedan completarse a tiempo cada día.

Base de datos SQL de Azure muestra información sobre los recursos consumidos para cada base de datos activa en la vista **sys.resource\_stats** de la base de datos **maestra** de cada servidor. Los datos de la tabla se agregan en intervalos de 5 minutos. Con los niveles de servicio Básico, Estándar y Premium, los datos pueden tardar más de 5 minutos en aparecer en la tabla, lo que significa que estos datos son mejores para el análisis histórico que para análisis en tiempo real. Al consultar la vista **sys.resource\_stats**, se muestra el historial reciente de una base de datos para validar si la reserva seleccionada proporcionó el rendimiento deseado cuando era necesario.

>[AZURE.NOTE] Tiene que estar conectado a la base de datos **maestra** de su servidor lógico de Base de datos SQL para poder consultar **sys.resource\_stats** en los ejemplos siguientes.

En el ejemplo siguiente se muestra cómo se exponen los datos en esta vista:

	SELECT TOP 10 * 
	FROM sys.resource_stats 
	WHERE database_name = 'resource1' 
	ORDER BY start_time DESC

![estadísticas de recursos del sistema](./media/sql-database-performance-guidance/sys_resource_stats.png)

En el ejemplo siguiente, se muestran las diferentes formas a través de las cuales puede entender el uso de recursos de Base de datos SQL, mediante la vista de catálogo **sys.resource\_stats**.

>[AZURE.NOTE] Algunas de las columnas de **sys.resource\_stats** han cambiado en las bases de datos V12 actuales, por lo que las consultas de ejemplo en los ejemplos siguientes podrían generar errores. Las actualizaciones futuras de este tema proporcionarán nuevas versiones de las consultas que resuelven este problema.

1. Por ejemplo, para buscar el uso de recursos durante la semana anterior de la base de datos "userdb1", puede ejecutar la consulta siguiente.
	
		SELECT * 
		FROM sys.resource_stats 
		WHERE database_name = 'userdb1' AND 
		      start_time > DATEADD(day, -7, GETDATE())
		ORDER BY start_time DESC;
	
2. Para evaluar si la carga de trabajo encaja bien en el nivel de rendimiento, tiene que explorar en profundidad cada aspecto de las métricas de recursos: CPU, lecturas, escrituras, número de trabajos y número de sesiones. Esta es una consulta revisada que usa sys.resource\_stats para notificar los valores promedio y máximo de estas métricas de recursos.
	
		SELECT 
		    avg(avg_cpu_percent) AS 'Average CPU Utilization In Percent',
		    max(avg_cpu_percent) AS 'Maximum CPU Utilization In Percent',
		    avg(avg_physical_data_read_percent) AS 'Average Physical Data Read Utilization In Percent',
		    max(avg_physical_data_read_percent) AS 'Maximum Physical Data Read Utilization In Percent',
		    avg(avg_log_write_percent) AS 'Average Log Write Utilization In Percent',
		    max(avg_log_write_percent) AS 'Maximum Log Write Utilization In Percent',
		    avg(active_session_count) AS 'Average # of Sessions',
		    max(active_session_count) AS 'Maximum # of Sessions',
		    avg(active_worker_count) AS 'Average # of Workers',
		    max(active_worker_count) AS 'Maximum # of Workers'
		FROM sys.resource_stats 
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
	
3. Con la información anterior sobre los valores promedio y máximo de cada métrica de recursos, puede evaluar si la carga de trabajo encaja bien en el nivel de rendimiento que eligió. En la mayoría de los casos, los valores promedio de sys.resource\_stats son una buena base de referencia para el tamaño de destino. Debería ser su vara de medida principal. Por ejemplo, si usa el nivel de servicio Estándar con el nivel de rendimiento S2, los porcentajes de uso promedio de CPU, lecturas y escrituras están por debajo de 40 %, el promedio de trabajos es inferior a 50 y el número promedio de sesión es inferior a 200, la carga de trabajo podría encajar bien en el nivel de rendimiento S1. Es fácil ver si la base de datos se ajusta a los límites de trabajos y sesiones. Para ver si una base de datos encaja en un nivel de rendimiento inferior con respecto a la CPU, lecturas y escrituras, divida el número de DTU del nivel de rendimiento inferior por el número de DTU de su nivel de rendimiento actual y multiplique el resultado por 100:
	
	**S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
	
	El resultado es la diferencia porcentual de rendimiento relativa entre los dos niveles de rendimiento. Si su uso no supera este porcentaje, la carga de trabajo podría encajar en el nivel de rendimiento inferior. Sin embargo, debe examinar todos los intervalos de valores de uso de recursos y determinar, según el porcentaje, con qué frecuencia encajaría la carga de trabajo de la base de datos en el nivel de rendimiento inferior. La siguiente consulta proporciona el porcentaje de ajuste por dimensión de recursos según el umbral del 40 % calculado anteriormente.
	
		SELECT 
		    (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
		    ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
		    ,(COUNT(database_name) - SUM(CASE WHEN avg_physical_data_read_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data Read Fit Percent'
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
	
	En función de su objetivo de nivel de servicio (SLO) de base de datos, puede decidir si la carga de trabajo encaja en el nivel de rendimiento inferior. Si el SLO de la carga de trabajo de base de datos es del 99,9 % y la consulta anterior devuelve valores mayores que 99,9 para las tres dimensiones de recursos, es muy probable que la carga de trabajo quepa en el nivel de rendimiento inferior.
	
	El porcentaje de ajuste también ofrece información detallada sobre si debe pasar al siguiente nivel de rendimiento superior para cumplir su SLO. Por ejemplo, "userdb1" muestra el siguiente uso durante la semana pasada.
	
	| Porcentaje promedio de CPU | Porcentaje máximo de CPU |
	|---|---|
	| 24,5 | 100,00 |
	
	El promedio de CPU es aproximadamente un cuarto del límite del nivel de rendimiento, que encajaría bien en el nivel de rendimiento de la base de datos. Sin embargo, el valor máximo muestra que la base de datos alcanza el límite del nivel de rendimiento. ¿Necesita pasar al siguiente nivel de rendimiento superior? De nuevo, hay que mirar cuántas veces la carga de trabajo alcanza el 100 % y compararlo con el SLO de la carga de trabajo de base de datos.
	
		SELECT 
		(COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
		,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent’
		,(COUNT(database_name) - SUM(CASE WHEN avg_physical_data_read_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data Read Fit Percent'
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
	
	Si la consulta anterior devuelve un valor menor que 99,9 para cualquiera de las tres dimensiones de recursos, debe plantearse pasar al siguiente nivel de rendimiento superior o usar técnicas de optimización de aplicaciones para reducir la carga en Base de datos SQL de Azure.
	
4. El ejercicio anterior también debe tener en cuenta el aumento de la carga de trabajo previsto en el futuro.

## Optimización de la aplicación

En servidores locales tradicionales de SQL Server, el proceso de planeación inicial de la capacidad suele está separado del proceso de ejecución de una aplicación en producción. En otras palabras, la adquisición de hardware y las licencias asociadas para ejecutar SQL Server se realizan por adelantado, mientras que la optimización del rendimiento se hace después. Cuando se usa Base de datos SQL de Azure, por lo general se recomienda (y, puesto que se factura cada mes, es deseable) intercalar el proceso de ejecución y optimización de una aplicación. El modelo de pago por capacidad a petición permite optimizar la aplicación para que use los recursos mínimos necesarios ahora en lugar de aprovisionar en exceso el hardware en función de las estimaciones de los planes de crecimiento futuro para una aplicación (que a menudo son incorrectos porque tienen que predecir muy lejos en el futuro). Tenga en cuenta que algunos clientes pueden decidir no optimizar una aplicación y, en su lugar, eligen aprovisionar en exceso los recursos de hardware. Este enfoque podría tener sentido si no desea cambiar una aplicación clave durante un período de ocupación de la aplicación. La optimización de una aplicación puede minimizar los requisitos de recursos y reducir las facturas mensuales cuando se usan los niveles de servicio de Base de datos SQL de Azure.

### Características de la aplicación

Aunque los niveles de servicio están diseñados para mejorar la estabilidad de rendimiento y capacidad de predicción de una aplicación, hay algunos procedimientos recomendados para optimizar la aplicación y poder aprovechar mejor las ventajas de la característica. Si bien muchas aplicaciones verán importantes mejoras de rendimiento simplemente cambiando a un nivel de rendimiento o nivel de servicio superior, no todas las aplicaciones pueden beneficiarse tanto sin una optimización adicional. Las aplicaciones que tienen las siguientes características deben plantearse también optimizaciones adicionales para mejorar aún más el rendimiento cuando se usa Base de datos SQL de Azure.

- **Aplicaciones que tienen un rendimiento lento debido a un comportamiento "conversador"**: esto incluye aplicaciones que realizan un exceso de operaciones de acceso a datos que son sensibles a la latencia de red. Estas aplicaciones pueden requerir modificaciones para reducir el número de operaciones de acceso de datos en Base de datos SQL de Azure. Por ejemplo, la aplicación se puede mejorar mediante técnicas como el procesamiento por lotes de consultas ad-hoc o el traslado de las consultas a procedimientos almacenados. Para obtener más información, consulte la sección 'Procesamiento de consultas por lotes', a continuación.
- **Bases de datos con una carga de trabajo intensivo que un solo equipo no puede admitir**: bases de datos que superan los recursos del nivel de rendimiento Premium más alto no son buenas candidatas. Estas bases de datos pueden beneficiarse del escalado horizontal de la carga de trabajo. Para obtener más información, consulte las secciones 'Particionamiento entre bases de datos' y 'Particionamiento funcional', a continuación.
- **Aplicaciones que contienen consultas no óptimas**: las aplicaciones, especialmente en la capa de acceso a datos, que tienen consultas poco optimizadas pueden no beneficiarse de la elección de un nivel de rendimiento mayor según lo esperado. Esto incluye consultas que carecen de una cláusula WHERE, con índices que faltan o tienen estadísticas anticuadas. Estas aplicaciones se beneficiarán de las técnicas de optimización del rendimiento de consultas estándar. Para obtener más información, consulte las secciones 'Índices que faltan' y 'Optimización/sugerencias de consulta', a continuación.
- **Aplicaciones con un diseño de acceso a datos no óptimo**: las aplicaciones que tienen problemas inherentes de simultaneidad de acceso a los datos, como los interbloqueos, pueden no beneficiarse de la elección de un nivel de rendimiento mayor. Los desarrolladores de aplicaciones deben plantearse reducir los viajes de ida y vuelta a Base de datos SQL de Azure de los datos en caché en el lado del cliente usando el servicio de almacenamiento en caché de Azure u otras tecnologías de almacenamiento en caché. Consulte la sección Almacenamiento en caché de la capa de aplicación, a continuación.

## Técnicas de optimización
En esta sección se explican algunas técnicas que puede usar para optimizar Base de datos SQL de Azure para obtener el mejor rendimiento de la aplicación y que pueda ejecutarla en el menor nivel de rendimiento posible. Varias de las técnicas coinciden con los procedimientos recomendados de optimización tradicionales de SQL Server, pero otras son específicas de Base de datos SQL de Azure. En algunos casos, las técnicas tradicionales de SQL Server se pueden ampliar para que funcionen también en Base de datos SQL de Azure, examinando para ello los recursos consumidos para una base de datos con el fin de encontrar áreas de mayor optimización.

### Query Performance Insight y Asesor de índice
Base de datos SQL ofrece dos herramientas en el Portal de Azure clásico para analizar y corregir problemas de rendimiento de la base de datos:

- [Query Performance Insight](sql-database-query-performance.md)
- [Index Advisor](sql-database-index-advisor.md)

Consulte los vínculos anteriores para obtener más información sobre cada una de las herramientas y sobre cómo usarlas. Las dos secciones siguientes sobre índices que faltan y la optimización de consultas, proporcionan otras formas de buscar manualmente y corregir los problemas de rendimiento similares. Se recomienda probar las herramientas en el portal para diagnosticar y corregir problemas de manera más eficiente. Use la optimización manual para casos especiales.

### Índices que faltan
Un problema común del rendimiento de las bases de datos OLTP está relacionado con el diseño físico de la base de datos. A menudo, los esquemas de base de datos se diseñan y se entregan sin realizar pruebas a escala (ya sea en la carga o en el volumen de datos). Lamentablemente, el rendimiento de un plan de consultas puede ser aceptable a pequeña escala, pero se puede degradar sustancialmente cuando se enfrenta a los volúmenes de datos del nivel de producción. El origen más común de este problema es la falta de índices adecuados para satisfacer los filtros u otras restricciones en una consulta. A menudo, esto se manifiesta como un recorrido de tabla cuando podría ser suficiente una búsqueda de índice.

En el ejemplo siguiente se crea un caso donde el plan de consulta seleccionado contiene un recorrido cuando bastaría con una búsqueda.

	DROP TABLE dbo.missingindex;
	CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
	DECLARE @a int = 0;
	SET NOCOUNT ON;
	BEGIN TRANSACTION
	WHILE @a < 20000
	BEGIN
	    INSERT INTO dbo.missingindex(col2) VALUES (@a);
	    SET @a += 1;
	END
	COMMIT TRANSACTION;
	GO
	SELECT m1.col1 
	FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1) 
	WHERE m1.col2 = 4;

![plan de consulta con índices que faltan](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Base de datos SQL de Azure contiene funcionalidad que ayuda a los administradores de bases de datos de sugerencias a encontrar y corregir casos frecuentes de índices que faltan. Las vistas de administración dinámica (DMV) integradas en Base de datos SQL de Azure miran la compilación de consultas en la que un índice reduciría considerablemente el costo estimado para ejecutar una consulta. Durante la ejecución de las consultas, hace un seguimiento de cuándo se ejecuta cada plan de consulta, así como de la diferencia estimada entre el plan de consulta en ejecución y uno imaginado en el que exista ese índice. Esto permite a un administrador de bases de datos adivinar rápidamente qué cambios de diseño de la base de datos física pueden mejorar el costo total de la carga de trabajo para una base de datos específica y su carga de trabajo real.

>[AZURE.NOTE] Antes de usar las DMV para buscar índices que faltan, revise la sección sobre [Query Performance Insight y Index Advisor](query-performance-insight-and-index-advisor.md).

La consulta siguiente puede usarse para evaluar posibles índices que faltan.

	SELECT CONVERT (varchar, getdate(), 126) AS runtime, 
	    mig.index_group_handle, mid.index_handle, 
	    CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact * 
	            (migs.user_seeks + migs.user_scans)) AS improvement_measure, 
	    'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' + 
	              CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + ' 
	              (' + ISNULL (mid.equality_columns,'') 
	              + CASE WHEN mid.equality_columns IS NOT NULL 
	                          AND mid.inequality_columns IS NOT NULL 
	                     THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
	              + ')' 
	              + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement, 
	    migs.*, 
	    mid.database_id, 
	    mid.[object_id]
	FROM sys.dm_db_missing_index_groups AS mig
	INNER JOIN sys.dm_db_missing_index_group_stats AS migs 
	    ON migs.group_handle = mig.index_group_handle
	INNER JOIN sys.dm_db_missing_index_details AS mid 
	    ON mig.index_handle = mid.index_handle
	ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

En este ejemplo, se recomienda el siguiente índice.

	CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Una vez creado, esa misma instrucción SELECT elige un plan diferente que usa una búsqueda en lugar de un recorrido y se ejecuta más eficazmente, como se muestra en el siguiente plan de consulta.

![plan de consulta con índices corregidos](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

El principal dato es que la capacidad de E/S de un sistema compartido es generalmente más limitada que la de un equipo servidor dedicado. Por tanto, es obligatorio minimizar la E/S innecesaria para sacar el máximo partido del sistema dentro de la DTU de cada nivel de rendimiento de los niveles de servicio en Base de datos SQL de Azure. La elección adecuada de las opciones de diseño de bases de datos físicas puede mejorar considerablemente la latencia de las consultas individuales, la capacidad de procesamiento de solicitudes simultáneas que puede realizar por unidad de escala y minimizar los costos necesarios para satisfacer la consulta. Para obtener más información acerca de las DMV de índices que faltan, consulte [sys.dm\_db\_missing\_index\_details](https://msdn.microsoft.com/library/ms345434.aspx).

### Optimización/sugerencias de consulta
El optimizador de consultas de Base de datos SQL de Azure es muy similar al optimizador de consultas de SQL Server tradicional. Muchos de los procedimientos recomendados para optimizar consultas y entender el razonamiento las limitaciones del modelo para el optimizador de consultas se aplican también a Base de datos SQL de Azure. La optimización de consultas en Base de datos SQL de Azure puede tener la ventaja adicional de reducir las demandas de recursos agregados y permitir que una aplicación se ejecute con un costo menor que una equivalente sin optimizar, porque se puede ejecutar en un nivel de rendimiento inferior.

Un ejemplo común en SQL Server que también se aplica a Base de datos SQL de Azure tiene que ver con cómo se examinan los parámetros durante la compilación para intentar crear un plan más óptimo. El examen de parámetros es que un proceso por el que el optimizador de consultas considera el valor actual del parámetro al compilar una consulta con la esperanza de generar un plan de consulta más óptimo. Aunque esta estrategia puede llevar a un plan de consulta que considerablemente más rápido que un plan compilado sin el conocimiento de los valores de parámetro, el comportamiento actual de SQL Server/Base de datos SQL de Azure es imperfecto: hay casos en los que el parámetro no se examina y hay casos en los que el parámetro se examina pero el plan generado no es el óptimo para el conjunto completo de valores de parámetro de una carga de trabajo. Microsoft incluye sugerencias de consulta (directivas) para que pueda especificar más deliberadamente la intención y reemplazar el comportamiento predeterminado de examen de parámetros. A menudo, el uso de sugerencias puede corregir los casos en los que el comportamiento predeterminado de Base de datos SQL de Azure/SQL Server es imperfecto para una carga de trabajo de un cliente determinado.

En el ejemplo siguiente se muestra cómo el procesador de consultas puede generar un plan que no es óptimo para los requisitos de rendimiento y de recursos, y cómo el uso de una sugerencia de consulta puede reducir el tiempo de ejecución de consultas y los requisitos de recursos en Base de datos SQL de Azure.

La siguiente es una configuración de ejemplo:

	DROP TABLE psptest1;
	CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));
	
	DECLARE @a int = 0;
	SET NOCOUNT ON;
	BEGIN TRANSACTION
	WHILE @a < 20000
	BEGIN
	    INSERT INTO psptest1(col2) values (1);
	    INSERT INTO psptest1(col2) values (@a);
	    SET @a += 1;
	END
	COMMIT TRANSACTION
	CREATE INDEX i1 on psptest1(col2);
	GO
	
	CREATE PROCEDURE psp1 (@param1 int)
	AS
	BEGIN
	    INSERT INTO t1 SELECT * FROM psptest1 
	    WHERE col2 = @param1
	    ORDER BY col2;
	END
	GO
	
	CREATE PROCEDURE psp2 (@param2 int)
	AS
	BEGIN
	    INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
	    ORDER BY col2
	    OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
	END
	GO
	
	CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
	GO

El código de configuración crea una tabla que contiene una distribución de datos sesgados. El plan de consulta óptimo varía en función de qué parámetro se seleccione. Lamentablemente, el comportamiento del almacenamiento en caché del plan no siempre recompila la consulta según el valor de parámetro más común, lo que significa que es posible que un plan poco óptimo se almacene en caché y se use para muchos valores, aunque un plan diferente fuera un plan promedio mejor. A continuación, crea dos procedimientos almacenados que son idénticos, salvo porque uno contiene una sugerencia de consulta especial (el razonamiento se explica más adelante).

**Ejemplo (parte 1):**

	-- Prime Procedure Cache with scan plan
	EXEC psp1 @param1=1;
	TRUNCATE TABLE t1;
	
	-- Iterate multiple times to show the performance difference
	DECLARE @i int = 0;
	WHILE @i < 1000
	BEGIN
	    EXEC psp1 @param1=2;
	    TRUNCATE TABLE t1;
	    SET @i += 1;
	END

**Ejemplo (parte 2; por favor, espere 10 minutos antes de intentar esta parte para que sea claramente diferente en los datos de telemetría resultantes):**

	EXEC psp2 @param2=1;
	TRUNCATE TABLE t1;
	
	DECLARE @i int = 0;
	WHILE @i < 1000
	BEGIN
	    EXEC psp2 @param2=2;
	    TRUNCATE TABLE t1;
	    SET @i += 1;
	END

Cada parte de este ejemplo intenta ejecutar 1000 veces una instrucción insert parametrizada (para generar una carga suficiente para que destaque en un conjunto de datos de prueba). Al ejecutar los procedimientos almacenados, el procesador de consultas examina el valor de los parámetros pasados al procedimiento durante su primera compilación (lo que se conoce como examinar parámetros). El plan resultante se almacena en caché y se usa para invocaciones posteriores, aunque el valor del parámetro sea diferente. Como resultado, el plan óptimo no puede usarse en todos los casos. A veces, los clientes necesitan guiar al optimizador para elegir un plan que sea mejor para el caso promedio en lugar del caso que se dio cuando la consulta se compiló por primera vez. En este ejemplo, el plan inicial generará un plan de "recorrido" que lee todas las filas para encontrar todos los valores que coinciden con el parámetro.

![Optimización de consultas](./media/sql-database-performance-guidance/query_tuning_1.png)

Como el procedimiento se ejecuta con el valor 1, el plan resultante era óptimo para 1 pero poco óptimo para todos los demás valores de la tabla. Es probable que el comportamiento resultante no sea el comportamiento deseado si elige cada plan aleatoriamente, porque la ejecución del plan será más lenta y consumirá más recursos.

Al ejecutar la prueba con "SET STATISTICS IO ON", se muestra el trabajo de recorrido lógico realizado en este ejemplo en segundo plano: puede ver que el plan realizó 1148 lecturas (lo que no resulta eficaz si el caso promedio es devolver una sola fila).

![Optimización de consultas](./media/sql-database-performance-guidance/query_tuning_2.png)

La segunda parte del ejemplo usa una sugerencia de consulta para indicar al optimizador que use un valor específico durante el proceso de compilación. En este caso, obliga al procesador de consultas a omitir el valor que se pasa como parámetro y, en su lugar, asume un valor "UNKNOWN", lo que significa un valor que tiene la frecuencia promedio en la tabla (se omite el sesgo). El plan resultante es un plan basado en búsquedas que es más rápido y usa menos recursos, en promedio, que el plan de la parte 1 del ejemplo.

![Optimización de consultas](./media/sql-database-performance-guidance/query_tuning_3.png)

El impacto de esto puede verse examinando la tabla **sys.resource\_stats**. (Nota: habrá un retraso desde el momento en que se ejecuta la prueba hasta el momento en que se rellenan los datos de la tabla). En este ejemplo, la parte 1 se ejecutó en el período de tiempo de 22:25:00 y la parte 2 se ejecutó en 22:35:00. Tenga en cuenta que el período de tiempo anterior usó más recursos en ese período de tiempo que el posterior (debido a las mejoras de eficiencia del plan).

	SELECT TOP 1000 * 
	FROM sys.resource_stats 
	WHERE database_name = 'resource1' 
	ORDER BY start_time DESC

![Optimización de consultas](./media/sql-database-performance-guidance/query_tuning_4.png)

>[AZURE.NOTE] Aunque el ejemplo usado aquí es intencionadamente pequeño, el impacto de los parámetros poco óptimos puede ser considerable, especialmente en bases de datos más grandes. La diferencia, en casos extremos, puede estar entre segundos y horas para los casos rápidos y lentos.

Puede examinar **sys.resource\_stats** para determinar si el recurso de una prueba determinada usa más o menos recursos que otra prueba. Al comparar los datos, separe las pruebas el tiempo suficiente para que no estén agrupadas en el mismo período de tiempo de 5 minutos en la vista **sys.resource\_stats**. Además, tenga en cuenta que el objetivo del ejercicio es minimizar los recursos totales usados, no minimizar los recursos máximos en sí. Por lo general, al optimizar la latencia de un fragmento de código también se reduce el consumo de recursos. Asegúrese de que los cambios previstos en cualquier aplicación realmente se necesitan y no afectan negativamente a la experiencia del cliente para los usuarios de una aplicación cuando usan sugerencias de consulta.

Si una carga de trabajo contiene un conjunto de consultas repetidas, a menudo tiene sentido capturar y validar la idoneidad de esas elecciones del plan ya que probablemente controlarán la unidad de tamaño mínima de los recursos necesaria para hospedar la base de datos. Una vez validado, una revisión ocasional de esos planes permite asegurar que no se han degradado. Para obtener más información acerca de las sugerencias de consulta, consulte [Sugerencias de consulta (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### Particionamiento entre bases de datos
Como Base de datos SQL de Azure se ejecuta en hardware estándar, generalmente los límites de capacidad son inferiores para una base de datos única que para una instalación local de SQL Server tradicional. Por lo tanto, algunos clientes usan técnicas de particionamiento para repartir las operaciones de base de datos entre varias bases de datos cuando no entran en los límites de una base de datos única en Base de datos SQL de Azure. La mayoría de los clientes que usan técnicas de particionamiento en Base de datos SQL de Azure divide sus datos en una única dimensión en varias bases de datos. Este enfoque implica entender que, con frecuencia, las aplicaciones OLTP realizan transacciones que solo se aplican a una fila o a un pequeño grupo de filas del esquema.

>[AZURE.NOTE] Tenga en cuenta que Base de datos SQL ahora proporciona una biblioteca para ayudar con el particionamiento. Para obtener más información, consulte [Información general de la biblioteca de cliente de bases de datos elásticas](sql-database-elastic-database-client-library.md).

Por ejemplo, si una base de datos contiene el cliente, el pedido y los detalles del pedido (tal y como se muestra en la base de datos de ejemplo tradicional Northwind incluida en SQL Server), estos datos pueden dividirse en varias bases de datos agrupando a un cliente con la información relacionada sobre el pedido y los detalles del pedido, y garantizar que permanecen en una única base de datos. La aplicación dividiría los distintos clientes entre las bases de datos, repartiendo la carga eficazmente entre varias bases de datos. Esto permite a los clientes no solo evitar el límite de tamaño máximo de la base de datos, también permite a Base de datos SQL de Azure procesar cargas de trabajo que son mucho mayores que los límites de los distintos niveles de rendimiento, siempre y cuando cada base de datos se ajuste a sus DTU.

Aunque el particionamiento de la base de datos no reduce la capacidad total de los recursos para una solución, esta técnica es muy eficaz para admitir soluciones muy grandes repartidas entre varias bases de datos y permite que cada base de datos se ejecute en un nivel de rendimiento diferente para admitir bases de datos "eficaces" muy grandes con requisitos elevados de recursos.

### Creación de particiones funcional
Los usuarios de SQL Server suelen combinar varias funciones en una base de datos única. Por ejemplo, si una aplicación contiene lógica para administrar el inventario de un almacén, esa base de datos podría contener lógica asociada con el inventario, el seguimiento de los pedidos de compra, los procedimientos almacenados y las vistas indizadas o materializadas que administran los informes de fin de mes y otras funciones. Esta técnica tiene la ventaja de poder administrar fácilmente la base de datos para operaciones tales como copia de seguridad, pero también requiere ajustar el tamaño del hardware para administrar picos de carga en todas las funciones de una aplicación.

Dentro de una arquitectura de escalado horizontal usada dentro de Base de datos SQL de Azure, suele ser beneficioso repartir las distintas funciones de una aplicación entre bases de datos diferentes. Esto permite escalar cada uno de forma independiente. A medida que una aplicación realiza una actividad mayor (y obtiene más carga en la base de datos), el administrador puede elegir niveles de rendimiento independientemente para cada función dentro de una aplicación. En el límite, esta arquitectura reparte la carga entre varios equipos lo que permite a una aplicación hacerse mayor de lo que un solo equipo estándar puede administrar.

### Consultas por lotes
Para las aplicaciones que tienen acceso a datos en forma de consultas ad hoc frecuentes, una gran parte del tiempo de respuesta se dedica a la comunicación de red entre la capa de aplicación y la capa de Base de datos SQL de Azure. Incluso si la aplicación y Base de datos SQL de Azure residen en el mismo centro de datos, la latencia de red entre los dos podría verse aumentada por el elevado número de operaciones de acceso a datos. Para reducir los viajes de ida y vuelta en la red para estas operaciones de acceso a datos, el desarrollador de aplicaciones debería plantearse el procesamiento por lotes de las consultas ad hoc o compilarlas en procedimientos almacenados. El procesamiento por lotes de las consultas ad hoc puede enviar varias consultas en un lote grande en un solo viaje a Base de datos SQL de Azure. Compilar consultas ad hoc en un procedimiento almacenado podría lograr el mismo resultado que el procesamiento por lotes. El uso de un procedimiento almacenado también ofrece la ventaja de aumentar las posibilidades de almacenar en caché los planes de consulta en Base de datos SQL de Azure para las ejecuciones posteriores del mismo procedimiento almacenado.

Algunas aplicaciones requieren operaciones de escritura intensivas. En ocasiones se puede reducir la carga total de E/S en una base de datos si se procesan por lotes las operaciones de escritura. Esto suele ser tan sencillo como usar transacciones explícitas en lugar de transacciones de confirmación automática dentro de los procedimientos almacenados y lotes ad hoc. Puede encontrar una evaluación de las distintas técnicas que se pueden usar en [Técnicas de procesamiento por lotes para aplicaciones de Base de datos SQL en Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Experimente con su propia carga de trabajo para encontrar el modelo adecuado para el procesamiento por lotes, teniendo cuidado de entender que un modelo determinado puede ofrecer garantías de coherencia transaccional ligeramente diferentes. Para encontrar la carga de trabajo adecuada que minimiza el uso de recursos, es necesario encontrar la combinación correcta entre coherencia y rendimiento.

### Almacenamiento en caché de la capa de aplicación
Algunas aplicaciones de base de datos contienen cargas de trabajo con operaciones de lectura intensivas. Es posible usar capas de almacenamiento en caché para reducir la carga en la base de datos y para reducir el nivel de rendimiento necesario para admitir una base de datos con Base de datos SQL de Azure. [Caché en Redis de Azure](https://azure.microsoft.com/services/cache/) permite a un cliente con una carga de trabajo con muchas operaciones de lectura leer los datos una vez (o una vez por cada equipo de capa de aplicación, según cómo esté configurada) y almacenar esos datos fuera de Base de datos SQL de Azure. Esto permite reducir la carga de la base de datos (CPU y E/S de lectura), pero afecta a la coherencia transaccional porque los datos que se leen de la memoria caché podrían estar sin actualizar respecto a los datos de la base de datos. Aunque hay muchas aplicaciones donde es aceptable una cierta cantidad de incoherencias, esto no es así para todas las cargas de trabajo. Debe comprender totalmente los requisitos de una aplicación antes de emplear una estrategia de almacenamiento en caché de la capa de aplicación.

## Conclusión

Los niveles de servicio de Base de datos SQL de Azure le permiten elevar el listón de los tipos de aplicaciones que puede compilar en la nube. Cuando se combina con la optimización de aplicaciones, puede obtener un rendimiento eficaz y confiable para su aplicación. Este documento describe las técnicas recomendadas para optimizar el consumo de recursos de la base de datos que encaja perfectamente en uno de los niveles de rendimiento. La optimización es un ejercicio continuado en el modelo de nube, y los niveles de servicio y sus niveles de rendimiento permiten a los administradores maximizar el rendimiento y minimizar los costos en la plataforma de Microsoft Azure.

<!---HONumber=AcomDC_0128_2016-->