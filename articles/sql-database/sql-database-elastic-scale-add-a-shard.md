<properties 
	pageTitle="Incorporación de una partición con herramientas de Base de datos elástica | Microsoft Azure" 
	description="Establece cómo usar las API de escala elástica para agregar particiones nuevas a un conjunto de particiones." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="ddove" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/27/2016" 
	ms.author="ddove;sidneyh"/>

# Incorporación de una partición con herramientas de bases de datos elásticas

## Para agregar una partición para un nuevo intervalo o clave  

Con frecuencia, las aplicaciones simplemente necesitan agregar nuevas particiones para controlar los datos que se esperan de nuevas claves o intervalos de claves, en un mapa de particiones que ya existe. Por ejemplo, es posible que una aplicación particionada por identificador de inquilino necesite aprovisionar una nueva partición para un nuevo inquilino o que datos particionados mensualmente necesiten que se aprovisione una nueva partición antes del inicio de cada nuevo mes.

Si el nuevo intervalo de valores de clave no forma parte todavía de una asignación existente, es muy sencillo agregar la nueva partición y asociar la nueva clave o el nuevo intervalo a dicha partición.

### Ejemplo: incorporación de una partición y su intervalo a una asignación de partición existente
En este ejemplo se utilizan los métodos [TryGetShard](https://msdn.microsoft.com/library/azure/dn823929.aspx), [CreateShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx) y [CreateRangeMapping] (https://msdn.microsoft.com/library/azure/dn807221.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeShardMap`1.CreateRangeMapping(Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeMappingCreationInfo{`0})) y se crea una instancia de la clase [ShardLocation](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation.shardlocation.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardLocation.). En el ejemplo siguiente, se ha creado una base de datos denominada **sample\_shard\_2** y todos los objetos de esquema necesarios en su interior para contener el intervalo [300, 400).

    // sm is a RangeShardMap object.
    // Add a new shard to hold the range being added. 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Create the mapping and associate it with the new shard 
    sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                            (new Range<long>(300, 400), shard2, MappingStatus.Online)); 


Como alternativa, puede usar Powershell para crear un nuevo Administrador de mapas de particiones. Hay un ejemplo disponible [aquí](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).
## Para agregar una partición para un área vacía de un intervalo existente  

En algunas circunstancias, tiene ya asignado un intervalo a una partición y se rellena parcialmente con datos, pero ahora desea que los próximos datos se dirijan a una partición diferente. Por ejemplo, realiza particiones por intervalo de días y ya ha asignado 50 días a una partición, pero en el día 24 decide que desea que los datos futuros lleguen a una partición diferente. La [herramienta de división y combinación](sql-database-elastic-scale-overview-split-and-merge.md) de la base de datos elástica puede realizar esta operación, pero si el movimiento de datos no es necesario (por ejemplo, los datos para el intervalo de días [25, 50), es decir, desde el día 25 inclusive hasta el 50 exclusive, aún no existen) es posible realizar esta operación por completo utilizando directamente las API de administración de mapas de particiones.

### Ejemplo: división de un intervalo y asignación de la parte vacía a una partición recién agregada

Se ha creado una base de datos denominada "sample\_shard\_2" y todos los objetos de esquema necesarios en su interior.

 
    // sm is a RangeShardMap object.
    // Add a new shard to hold the range we will move 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
    
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Split the Range holding Key 25 

    sm.SplitMapping(sm.GetMappingForKey(25), 25); 

    // Map new range holding [25-50) to different shard: 
    // first take existing mapping offline 
    sm.MarkMappingOffline(sm.GetMappingForKey(25)); 
    // now map while offline to a different shard and take online 
    RangeMappingUpdate upd = new RangeMappingUpdate(); 
    upd.Shard = shard2; 
    sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd)); 

**Importante**: use esta técnica solo si está seguro de que el intervalo para la asignación actualizada está vacío. Los métodos anteriores no comprueban los datos para el intervalo que se va a mover, por lo que es mejor incluir comprobaciones en el código. Si existen filas en el intervalo que se va a mover, la distribución de datos real no coincidirá con el mapa de particiones actualizado. Use la [herramienta de división y combinación](sql-database-elastic-scale-overview-split-and-merge.md) para realizar la operación en su lugar en estos casos.


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=AcomDC_0128_2016-->