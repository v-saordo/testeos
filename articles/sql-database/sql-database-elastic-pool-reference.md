<properties
	pageTitle="Referencia de grupo de bases de datos elásticas para la Base de datos SQL | Microsoft Azure" 
	description="Esta referencia proporciona vínculos y detalles de artículos de grupos elásticos de bases de datos, así como información de programación."
	keywords="eDTU"
	services="sql-database"
	documentationCenter=""
	authors="sidneyh"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/17/2016"
	ms.author="sidneyh"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Referencia de grupos de bases de datos elásticas de Base de datos SQL

Para desarrolladores de SaaS que tienen decenas, cientos o incluso miles de bases de datos, los [grupos de bases de datos elásticas](sql-database-elastic-pool.md) simplifican el proceso de creación, mantenimiento y administración del rendimiento y el costo de todo el grupo de bases de datos.

## Requisitos previos para crear y administrar grupos de bases de datos elásticas

- Los grupos de bases de datos elásticas solo están disponibles en los servidores de Base de datos SQL V12 de Azure. Para actualizar a V12 y migrar las bases de datos directamente a un grupo, consulte [Actualización a Base de datos SQL V12 de Azure](sql-database-upgrade-server-powershell.md).
- Solo se admite la creación y administración de grupos de bases de datos elásticas con el [Portal de Azure](https://portal.azure.com), [PowerShell](sql-database-elastic-pool-powershell.md) y una biblioteca cliente de .NET (solo el Administrador de recursos de Azure). No se admiten el [Portal clásico](https://manage.windowsazure.com/) ni los comandos de administración de servicios.
- Además, es posible crear nuevas bases de datos elásticas y mover bases de datos existentes dentro y fuera de los grupos de bases de datos elásticas mediante [Transact-SQL](#transact-sql).


## Lista de artículos

Los artículos siguientes le ayudarán a empezar a usar bases de datos y trabajos elásticos:

| Artículo | Descripción |
| :-- | :-- |
| [Grupos de bases de datos SQL elásticas](sql-database-elastic-pool.md) | Información general sobre los grupos de bases de datos elásticas |
| [Consideraciones sobre precios y rendimiento](sql-database-elastic-pool-guidance.md) | Evaluación de si el uso de un grupo de bases de datos elásticas es rentable |
| [Creación y administración de un grupo de bases de datos SQL elásticas con el portal de Azure](sql-database-elastic-pool-portal.md) | Creación y administración de grupos de bases de datos elásticas mediante el Portal de Azure |
| [Creación y administración de un grupo de bases de datos SQL elásticas con PowerShell](sql-database-elastic-pool-powershell.md) | Creación y administración de grupos de bases de datos elásticas mediante cmdlets de PowerShell |
| [Creación y administración de una base de datos SQL con la biblioteca de Base de datos SQL de Azure para .NET](sql-database-elastic-pool-csharp.md) | Creación y administración de grupos de bases de datos elásticas mediante C# |
| [Información general de los trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md) | Información general acerca del servicio de trabajos elásticos, que permite ejecutar secuencias de comandos de T-SQL en todas las bases de datos elásticas de un grupo. |
| [Instalación del componente de trabajo de la base de datos elástica](sql-database-elastic-jobs-service-installation.md) | Instalación del servicio de trabajo de base de datos elástica |
| [Protección de bases de datos SQL](sql-database-security.md) | Para ejecutar una secuencia de comandos de trabajo de la base de datos elástica, deberá agregar un usuario con los permisos adecuados para cada base de datos del grupo. |
| [Desinstalación de los componentes de trabajo de la base de datos elástica](sql-database-elastic-jobs-uninstall.md) | Recuperación de errores al intentar instalar el servicio de trabajo de la base de datos elástica |



## Detalles del espacio de nombres y del extremo
Un grupo de bases de datos elásticas es un recurso del Administrador de recursos de Azure de tipo "ElasticPool" en la Base de datos SQL de Microsoft Azure.

- **Espacio de nombres**: Microsoft.Sql/ElasticPool
- **management-endpoint** para llamadas a la API de REST (Administrador de recursos): https://management.azure.com



## Propiedades del grupo de bases de datos elásticas

| Propiedad | Descripción |
| :-- | :-- |
| creationDate | Fecha en que se crea el grupo. |
| databaseDtuMax | Número máximo de eDTU que puede usar una base de datos única del grupo. El número máximo de eDTU de la base de datos no es un número garantizado de recursos. El número máximo de eDTU se aplica a todas las bases de datos del grupo. |
| databaseDtuMin | Número mínimo de eDTU que se garantiza en una base de datos única del grupo. El número mínimo eDTU de la base de datos se puede establecer en 0. El número mínimo de eDTU se aplica a todas las bases de datos del grupo. Tenga en cuenta que el producto del número de bases de datos del grupo y el número mínimo de eDTU de la base de datos no puede superar el número de eDTU del propio grupo. |
| Dtu | Número de eDTU que comparten todas las bases de datos del grupo. |
| edition | Nivel de servicio del grupo. Todas las bases de datos del grupo tienen esta edición. |
| elasticPoolId | GUID de la instancia del grupo. |
| elasticPoolName | Nombre del grupo. El nombre es exclusivo en relación a su servidor principal. |
| location | Ubicación del centro de datos en el que se creó el grupo. |
| state | Estado es "Deshabilitado" si no se ha efectuado el pago de la factura para la suscripción. El estado será "Listo" en los demás casos. |
| storageMB | Límite de almacenamiento en MB del grupo. El total de almacenamiento utilizado por todas las bases de datos del grupo no puede superar este límite de grupo. |


## Límites de almacenamiento y de eDTU para grupos de bases de datos elásticas y bases de datos elásticas


[AZURE.INCLUDE [Tabla de niveles de servicio de Base de datos SQL para bases de datos elásticas](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]



## Limitaciones del Administrador de recursos de Azure

Los servidores de Base de datos SQL V12 de Azure se encuentran en grupos de recursos.

- Cada grupo de recursos puede tener un máximo de 800 servidores.
- Cada servidor puede tener un máximo de 800 grupos elásticos.


## Latencia de las operaciones de grupos elásticos

- El cambio del número garantizado de eDTU por base de datos (databaseDtuMin) o del número máximo de eDTU por base de datos (databaseDtuMax) suele completarse en cinco minutos o menos.
- El cambio del límite de eDTU o de almacenamiento (storageMB) del grupo depende de la cantidad total de espacio que usen todas las bases de datos del grupo. Los cambios tienen un duración media de 90 minutos o menos por cada 100 GB. Por ejemplo, si el espacio total que usan todas las bases de datos del grupo es de 200 GB, la latencia esperada para el cambio del límite de eDTU o de almacenamiento es de tres horas o menos.



## PowerShell, API de REST y la biblioteca de cliente de .NET

Hay varios comandos de API de REST y cmdlets de PowerShell que están disponibles para crear y administrar grupos elásticos. Para información detallada y ejemplos de código, consulte [Creación y administración de un grupo de bases de datos elásticas de Base de datos SQL con PowerShell](sql-database-elastic-pool-powershell.md) y [Creación y administración de Base de datos SQL con C#](sql-database-client-library.md).


| [Cmdlets de PowerShell](https://msdn.microsoft.com/library/azure/mt574084.aspx) | [Comandos de las API de REST](https://msdn.microsoft.com/library/mt163571.aspx) |
| :-- | :-- |
| [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) | [Creación de un grupo de bases de datos elásticas](https://msdn.microsoft.com/library/mt163596.aspx) |
| [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx) | [Establecer la configuración de rendimiento de un grupo de bases de datos elásticas](https://msdn.microsoft.com/library/mt163641.aspx) |
| [Remove-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619355.aspx) | [Eliminar un grupo de bases de datos elásticas](https://msdn.microsoft.com/library/mt163672.aspx) |
| [Get-AzureRMSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603517.aspx) | [Obtiene los grupos de bases de datos elásticas y sus valores de propiedad](https://msdn.microsoft.com/library/mt163646.aspx) |
| [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) | [Obtención del estado de las operaciones de grupos de bases de datos elásticas](https://msdn.microsoft.com/library/mt163669.aspx) |
| [Get-AzureRmSqlElasticPoolDatabase](https://msdn.microsoft.com/library/azure/mt619484.aspx) | [Obtención de bases de datos en un grupo de bases de datos elásticas](https://msdn.microsoft.com/library/mt163646.aspx) |
| [Get-AzureRmSqlElasticPoolDatabaseActivity]() | [Obtiene el estado de la introducción y extracción de bases de datos de un grupo](https://msdn.microsoft.com/library/mt163669.aspx) |

## Transact-SQL

Puede usar Transact-SQL para realizar las siguientes tareas de administración de bases de datos elásticas:

| Tarea | Detalles |
| :-- | :-- |
| Crear una nueva base de datos elástica (directamente en un grupo) | [CREAR BASE DE DATOS (Base de datos SQL de Azure)](https://msdn.microsoft.com/library/dn268335.aspx) |
| Mover bases de datos existentes dentro y fuera de un grupo | [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/ms174269.aspx) |
| Obtener estadísticas de uso de recursos de un grupo | [sys.elastic\_pool\_resource\_stats (Base de datos SQL de Azure)](https://msdn.microsoft.com/library/mt280062.aspx) |


## Información de precios y facturación

Los grupos de bases de datos elásticos se facturan según la características siguientes:

- Los grupos elásticos se facturan desde su creación, incluso si no contiene ninguna base de datos.
- Los grupos elásticos se facturan por horas. Se trata de la misma frecuencia de medición que la de los niveles de rendimiento de las bases de datos únicas.
- Si se cambia el tamaño de un grupo elástico a una nueva cantidad de eDTU, el grupo no se factura según la nueva cantidad de eDTU hasta que la operación de cambio de tamaño se complete. Esto sigue el mismo patrón que el cambio de nivel de rendimiento de las bases de datos independientes.


- El precio de un grupo elástico se basa en el número de eDTU del grupo. El precio de un grupo elástico es independiente de la utilización de las bases de datos elásticas de dentro de él.
- El precio se calcula por (número de eDTU de grupo) x (precio unitario por eDTU).

El precio unitario de eDTU de un grupo elástico es mayor que el precio unitario de eDTU para una base de datos independiente del mismo nivel de servicio. Para obtener información detallada, vea [Precios de bases de datos SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## Errores de grupos de bases de datos elásticos

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX\_RESOURCE | El grupo elástico ha alcanzado su límite de almacenamiento. El uso del almacenamiento del grupo elástico no puede superar (%d) MB. | Límite de espacio del grupo elástico en MB. | Se ha intentado escribir datos en una base de datos cuando se ha alcanzado el límite de almacenamiento del grupo elástico. | Considere la posibilidad de incrementar el número de DTU del grupo elástico si es posible para aumentar su límite de almacenamiento, reduzca el almacenamiento usado por las bases de datos individuales del grupo elástico o quite bases de datos del grupo elástico. |
| 10929 | EX\_USER | La garantía mínima de %s es de %d, el límite máximo es %d y el uso actual de la base de datos es %d. Sin embargo, el servidor está demasiado ocupado en estos momentos para admitir solicitudes mayores que %d para esta base de datos. Vea [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637) para obtener ayuda. De lo contrario, Inténtelo de nuevo más tarde. | Número mínimo de DTU por cada base de datos; número máximo de DTU por base de datos. | El número total de trabajadores simultáneos (solicitudes) de todas las bases de datos del grupo elástico intentó superar el límite del grupo. | Considere la posibilidad de incrementar el número de DTU del grupo elástico si es posible para aumentar el límite de trabajadores, o bien quite bases de datos del grupo elástico. |
| 40844 | EX\_USER | La base de datos '%ls' del servidor '%ls' es una base de datos de la versión '%ls' de un grupo elástico y no puede tener una relación de copia continua. | Nombre de la base de datos, versión de base de datos, nombre del servidor. | Se emitió un comando StartDatabaseCopy para una base de datos que no es Premium de un grupo elástico. | Próximamente |
| 40857 | EX\_USER | Grupo elástico no encontrado para el servidor: '%ls', nombre del grupo elástico: '%ls'. | Nombre del servidor; nombre del grupo elástico. | El grupo elástico especificado no existe en el servidor especificado. | Especifique un nombre de grupo elástico válido. |
| 40858 | EX\_USER | El grupo elástico '%ls' ya existe en el servidor: '%ls' | Nombre de grupo elástico, nombre del servidor. | El grupo elástico especificado ya existe en el servidor lógico especificado. | Proporcione un nuevo nombre de grupo elástico. |
| 40859 | EX\_USER | El grupo elástico no admite el nivel de servicio '%ls'. | Nivel de servicio de grupo elástico. | El nivel de servicio especificado no se admite para el aprovisionamiento de grupo elástico. | Especifique la versión correcta o deje el nivel de servicio en blanco para usar el nivel de servicio predeterminado. |
| 40860 | EX\_USER | La combinación del grupo elástico '%ls' y del objetivo de servicio '%ls' no es válida. | Nombre del grupo flexible; nombre del objetivo de nivel de servicio. | El grupo elástico y el objetivo de servicio pueden especificarse juntos solo si se especifica el objetivo de servicio como "ElasticPool". | Especifique la combinación correcta de grupo elástico y objetivo de servicio. |
| 40861 | EX\_USER | La edición de la base de datos '%.*ls' no puede ser diferente del nivel de servicio de grupo elástico que es '%.*ls'. | edición de base de datos, nivel de servicio de grupo elástico | La edición de base de datos es diferente del nivel de servicio de grupo elástico. | No especifique una edición de base de datos que sea diferente del nivel de servicio de grupo elástico. Tenga en cuenta que no es necesario especificar la edición de la base de datos. |
| 40862 | EX\_USER | Se debe especificar el nombre del grupo elástico si se especifica el objetivo del servicio del grupo elástico. | Ninguno | El objetivo del servicio del grupo elástico no identifica de manera única un grupo elástico. | Especifique el nombre del grupo elástico si usa el objetivo del servicio del grupo elástico. |
| 40864 | EX\_USER | Las DTU para el grupo elástico deben ser como mínimo (%d) DTU para nivel de servicio '%.*ls'. | DTU para grupo elástico; nivel de servicio de grupo elástico. | Intentando establecer las DTU para el grupo elástico por debajo del límite mínimo. | Vuelva a intentar establecer las DTU para el grupo elástico en al menos el límite mínimo. |
| 40865 | EX\_USER | Las DTU para el grupo elástico no pueden superar (%d) DTU para el nivel de servicio '%.*ls'. | DTU para grupo elástico; nivel de servicio de grupo elástico. | Intentando establecer las DTU para el grupo elástico por encima del límite máximo. | Vuelva a intentar establecer las DTU para el grupo elástica en un número no superior al límite máximo. |
| 40867 | EX\_USER | El máximo de DTU por base de datos debe ser al menos (%d) para el nivel de servicio '%.*ls'. | Número máximo de DTU por base de datos; nivel de servicio de grupo elástico | Intentando establecer el máximo de DTU por base de datos por debajo del límite admitido. | Considere la posibilidad de usar el nivel de servicio de grupo elástico que admita la configuración deseada. |
| 40868 | EX\_USER | El máximo de DTU por base de datos no puede superar (%d) para el nivel de servicio '%.*ls'. | Máximo de DTU por base de datos; nivel de servicio de grupo elástico. | Intentando establecer el máximo de DTU por base de datos más allá del límite admitido. | Considere la posibilidad de usar el nivel de servicio de grupo elástico que admita la configuración deseada. |
| 40870 | EX\_USER | El mínimo de DTU no puede superar (%d) para el nivel de servicio '%. * ls'. | Mínimo de DTU por base de datos; nivel de servicio de grupo elástico. | Intentando establecer el mínimo de DTU por base de datos más allá del límite admitido. | Considere la posibilidad de usar el nivel de servicio de grupo elástico que admita la configuración deseada. |
| 40873 | EX\_USER | El número de bases de datos (%d) y el mínimo de DTU por base de datos (%d) no puede superar las DTU del grupo flexible (%d). | Número de bases de datos en grupo elástico; mínimo de DTU por base de datos; DTU de grupo elástico. | Intentando especificar mínimo de DTU para bases de datos en el grupo elástico que supera las DTU del grupo flexible. | Considere la posibilidad de aumentar las DTU del grupo elástico o de reducir el mínimo de DTU por base de datos, o de reducir el número de bases de datos del grupo elástico. |
| 40877 | EX\_USER | No se puede eliminar un grupo elástico a menos que no contenga ninguna base de datos. | Ninguno | El grupo elástico contiene una o más bases de datos y, por tanto, no se puede eliminar | Quite las bases de datos del grupo flexible para poder eliminarlo. |
| 40881 | EX\_USER | El grupo elástico '%. * ls' ha alcanzado su límite de número de bases de datos. El límite del número de bases de datos para el grupo elástico no puede superar (%d) para un grupo elástico con (%d) DTU. | Nombre del grupo elástico; límite del número de bases de datos del grupo elástico; DTU para el grupo de recursos. | Intentando crear o agregar base de datos a grupo elástico cuando se ha alcanzado el límite del número de bases de datos del grupo elástico. | Considere la posibilidad de aumentar las DTU del grupo elástico si es posible para aumentar su límite de bases de datos o quitar bases de datos del grupo elástico. |
| 40889 | EX\_USER | No se pueden reducir las DTU ni el límite de almacenamiento para el grupo elástico '%.*ls' puesto que eso no daría espacio de almacenamiento suficiente para sus bases de datos. | Nombre de grupo elástico. | Intentando reducir el límite de almacenamiento del grupo elástico por debajo de su uso de almacenamiento. | Considere la posibilidad de reducir el uso de almacenamiento de bases de datos individuales en el bloque elástico o quitar las bases de datos del grupo para reducir sus DTU o el límite de almacenamiento. |
| 40891 | EX\_USER | El mínimo de DTU por base de datos (%d) no puede superar el máximo de DTU por base de datos (%d). | Mínimo de DTU por base de datos; máximo de DTU por base de datos. | Intentando establecer el mínimo de DTU por base de datos por encima del máximo de DTU por base de datos. | Asegúrese de que el mínimo de DTU por bases de datos no supera el máximo de DTU por base de datos. |
| Por determinar | EX\_USER | El tamaño de almacenamiento para una base de datos individual en un grupo elástico no puede superar el tamaño máximo permitido por el grupo elástico del nivel de servicio '%.*ls'. | nivel de servicio del grupo elástico | El tamaño máximo de la base de datos supera el tamaño máximo permitido por el nivel de servicio del grupo elástico. | Establezca el tamaño máximo de la base de datos dentro de los límites del tamaño máximo permitido por el nivel de servicio del grupo elástico. |

<!---HONumber=AcomDC_0218_2016-->