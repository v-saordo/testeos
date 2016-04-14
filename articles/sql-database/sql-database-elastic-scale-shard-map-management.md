<properties 
	pageTitle="Administración de mapas de particiones | Microsoft Azure" 
	description="Cómo usar ShardMapManager, la biblioteca de cliente de bases de datos elásticas" 
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
	ms.date="02/05/2016" 
	ms.author="ddove;sidneyh"/>

# Administración de mapas de particiones.

En un entorno de base de datos particionada, un [**mapa de particiones**](sql-database-elastic-scale-glossary.md) mantiene información que permite que una aplicación se conecte a la base de datos correcta en función del valor de la **clave de particionamiento**. Comprender cómo se construyen estos mapas es fundamental para la administración de los mapas de particiones. Para Base de datos SQL de Azure, use la [clase ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx), que se encuentra en la [biblioteca de cliente de Base de datos elástica](sql-database-elastic-database-client-library.md) para administrar los mapas de particiones.
 

## Mapas de particiones y asignaciones de particiones
 
### Tipos .NET admitidos para claves de particionamiento

Escalado elástico admite los siguientes tipos de .Net Framework como claves de particionamiento:

* integer
* long
* guid
* byte  
* datetime
* timespan
* datetimeoffset

### Mapas de particiones de lista y de intervalo
Los mapas de particiones se pueden construir mediante **listas de valores individuales de clave de particionamiento**, o por medio de **intervalos de valores de clave de particionamiento**.

###Mapas de particiones de lista
Las **particiones** contienen **shardlets** y la asignación de shardlets a particiones se mantiene mediante un mapa de particiones. Un **mapa de particiones de lista** es una asociación entre los valores de clave individuales que identifican los shardlets y las bases de datos que funcionan como particiones. Las **asignaciones de lista** son valores de clave explícitos y diferentes que se pueden asignar a la misma base de datos. Por ejemplo, la clave 1 se asigna a la Base de datos A y los valores de clave 3 y 6 hacen referencia ambos a la Base de datos B.

| Clave | Ubicación de la partición |
|-----|----------------|
| 1 | Database\_A |
| 3 | Database\_B |
| 4 | Database\_C |
| 6 | Database\_B |
| ... | ... |
 

### Mapas de particiones de intervalo 
En un **mapa de particiones de intervalo**, el intervalo de claves se describe mediante un par **[valor bajo, valor alto)** donde el *valor bajo* es la clave mínima de el intervalo y el *valor alto* es el primer valor superior del intervalo.

Por ejemplo **[0, 100)** incluye todos los números enteros que son mayores o iguales que 0 y menores que 100. Tenga en cuenta que varios intervalos pueden apuntar a la misma base de datos y que se admiten intervalos separados (por ejemplo, [100,200) y [400,600) ambos apuntan a la base de datos C en el ejemplo siguiente).

| Clave | Ubicación de la partición |
|-----------|----------------|
| [1,50) | Database\_A |
| [50,100) | Database\_B |
| [100,200) | Database\_C |
| [400,600) | Database\_C |
| ... | ...            

Cada una de las tablas mostradas anteriormente es un ejemplo conceptual de un objeto **ShardMap**. Cada fila es un ejemplo simplificado de un objeto **PointMapping** (para el mapa de particiones de lista) o **RangeMapping** (para el mapa de particiones de intervalo).

## Administrador de mapas de particiones 

En biblioteca de cliente, el Administrador de mapas de particiones es una colección de mapas de particiones. Los datos administrados por una instancia **ShardMapManager** se mantienen en tres lugares:

1. **Mapa de particiones global (GSM)**: se especifica una base de datos que funcione como repositorio de todos los mapas de particiones y sus asignaciones. Automáticamente se crean procedimientos almacenados y tablas especiales para administrar la información. Suele ser una base de datos pequeña y con menos acceso, pero no debe utilizarse para otras necesidades de la aplicación. Las tablas se encuentran en un esquema especial llamado **\_\_ShardManagement**.

2. **Mapa de particiones local (LSM)**: cada base de datos que especifique para que sea una partición se modificará para que contenga varias tablas pequeñas y procedimientos almacenados especiales que contienen y administran información de mapa de particiones específica de esa partición. Esta información es redundante con la información del GSM, pero permite que la aplicación valide información de mapa de particiones en caché sin colocar ninguna carga en el GSM; la aplicación usa el LSM para determinar si una asignación en caché sigue siendo válida. Las tablas correspondientes al LSM en cada partición se encuentran también en el esquema **\_\_ShardManagement**.

3. **Caché de aplicación**: cada instancia de la aplicación que accede a un objeto **ShardMapManager** mantiene una caché en memoria local de sus asignaciones. Almacena información de enrutamiento que recientemente se ha recuperado.

## Construcción de un ShardMapManager

Un objeto **ShardMapManager** se construye mediante un patrón de [fábrica](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx). El método **[ShardMapManagerFactory.GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)** toma las credenciales (lo que incluye el nombre del servidor y el nombre de la base de datos que contienen el GSM) en forma de una instancia de **ConnectionString** y devuelve una instancia de **ShardMapManager**.

**Nota**: Solo se debe crear una instancia de **ShardMapManager** una vez por dominio de aplicación, dentro del código de inicialización de una aplicación. La creación de instancias adicionales de ShardMapManager en el mismo dominio de aplicación dará lugar a un aumento en el uso de memoria y CPU de la aplicación. Un objeto **ShardMapManager** puede contener cualquier número de mapas de particiones. Aunque un solo mapa de particiones puede ser suficiente para muchas aplicaciones, hay veces en que se usan diferentes conjuntos de bases de datos para un esquema diferente o con fines únicos y, en esos casos, puede que sea preferible usar varios mapas de particiones.

En este código, una aplicación intenta abrir una instancia de **ShardMapManager** existente con el [método TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx). Si aún no existen objetos que representan una instancia de **ShardMapManager** (GSM) global dentro de la base de datos, la biblioteca de cliente los crea mediante el método [CreateSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx).

    // Try to get a reference to the Shard Map Manager 
 	// via the Shard Map Manager database.  
    // If it doesn't already exist, then create it. 
    ShardMapManager shardMapManager; 
    bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString, 
                                        ShardMapManagerLoadPolicy.Lazy, 
                                        out shardMapManager); 

    if (shardMapManagerExists) 
     { 
        Console.WriteLine("Shard Map Manager already exists");
    } 
    else
    {
        // Create the Shard Map Manager. 
        ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
        Console.WriteLine("Created SqlShardMapManager"); 

        shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString, 
            ShardMapManagerLoadPolicy.Lazy);

        // The connectionString contains server name, database name, and admin credentials 
        // for privileges on both the GSM and the shards themselves.
    } 
 
Como alternativa, puede usar Powershell para crear un nuevo Administrador de mapas de particiones. Hay un ejemplo disponible [aquí](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

### Credenciales de administración de mapas de particiones

Normalmente, las aplicaciones que administran y manipulan mapas de particiones son diferentes de las que usan los mapas de particiones para enrutar conexiones.

En el caso de aplicaciones que administran mapas de particiones (agregar o cambiar particiones, mapas de particiones, asignaciones de particiones, etc.), se deben crear instancias de **ShardMapManager** mediante **credenciales que tengan privilegios de lectura y escritura en la base de datos GSM y en cada base de datos que funcione como partición**. Las credenciales deben permitir escrituras en las tablas del GSM y el LSM cuando se especifica o se cambia información de mapa de particiones, así como al crear tablas del LSM en nuevas particiones.

### Solo los metadatos afectados 

Los métodos usados para rellenar o cambiar datos de **ShardMapManager** no modifican los datos de usuario almacenados en las propias particiones. Por ejemplos, métodos como **CreateShard**, **DeleteShard**, **UpdateMapping**, etc. afectan solo a los metadatos del mapa de particiones. No quitan, agregan ni modificar los datos contenidos en las particiones. En su lugar, estos métodos están diseñados para usarse conjuntamente con operaciones independientes que se lleven a cabo para crear o quitar bases de datos reales, o para mover filas de una partición a otra con el fin de reequilibrar un entorno particionado. (La herramienta de **división y combinación** incluida en las herramientas de bases de datos elásticas usa estas API junto con la coordinación del movimiento de datos real entre particiones). Consulte [Escalado con la herramienta de división y combinación de bases de datos elásticas](sql-database-elastic-scale-overview-split-and-merge.md).

## Ejemplo de cómo rellenar un mapa de particiones
 
A continuación se muestra una secuencia de operaciones de ejemplo para rellenar un mapa de particiones específico. El código realiza estos pasos:

1. Se crea un mapa de particiones en un administrador de mapas de particiones. 
2. Los metadatos de dos particiones diferentes se agregan al mapa de particiones. 
3. Se agregan diversas asignaciones de intervalo de claves, y se muestra el contenido global del mapa de particiones. 

El código está escrito para que el método se pueda volver a ejecutar si se produce un error. Cada solicitud comprueba si ya existe una partición o una asignación, antes de intentar crearla. El siguiente código asume que las bases de datos llamadas **sample\_shard\_0**, **sample\_shard\_1** y **sample\_shard\_2** ya se han creado en el servidor al que hace referencia la cadena **shardServer**.

    public void CreatePopulatedRangeMap(ShardMapManager smm, string mapName) 
        {            
            RangeShardMap<long> sm = null; 

            // check if shardmap exists and if not, create it 
            if (!smm.TryGetRangeShardMap(mapName, out sm)) 
            { 
                sm = smm.CreateRangeShardMap<long>(mapName); 
            } 

            Shard shard0 = null, shard1=null; 
            // Check if shard exists and if not, 
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetShard(new ShardLocation(
	                                 shardServer, 
	                                 "sample_shard_0"), 
	                                 out shard0)) 
            { 
                Shard0 = sm.CreateShard(new ShardLocation(
	                                        shardServer, 
	                                        "sample_shard_0")); 
            } 

            if (!sm.TryGetShard(new ShardLocation(
	                                shardServer, 
	                                "sample_shard_1"), 
	                                out shard1)) 
            { 
                Shard1 = sm.CreateShard(new ShardLocation(
	                                         shardServer, 
	                                        "sample_shard_1"));  
            } 

            RangeMapping<long> rmpg=null; 

            // Check if mapping exists and if not,
            // create it (Idempotent / tolerant of re-execute) 
            if (!sm.TryGetMappingForKey(0, out rmpg)) 
            { 
                sm.CreateRangeMapping(
	                      new RangeMappingCreationInfo<long>
	                      (new Range<long>(0, 50), 
	                      shard0, 
	                      MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(50, out rmpg)) 
            { 
                sm.CreateRangeMapping(
	                     new RangeMappingCreationInfo<long> 
                         (new Range<long>(50, 100), 
	                     shard1, 
	                     MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(100, out rmpg)) 
            { 
                sm.CreateRangeMapping(
	                     new RangeMappingCreationInfo<long>
                         (new Range<long>(100, 150), 
	                     shard0, 
	                     MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(150, out rmpg)) 
            { 
                sm.CreateRangeMapping(
	                     new RangeMappingCreationInfo<long> 
                         (new Range<long>(150, 200), 
	                     shard1, 
	                     MappingStatus.Online)); 
            } 

            if (!sm.TryGetMappingForKey(200, out rmpg)) 
            { 
               sm.CreateRangeMapping(
	                     new RangeMappingCreationInfo<long> 
                         (new Range<long>(200, 300), 
	                     shard0, 
	                     MappingStatus.Online)); 
            } 

            // List the shards and mappings 
            foreach (Shard s in sm.GetShards()
                         .OrderBy(s => s.Location.DataSource)
                         .ThenBy(s => s.Location.Database))
            { 
               Console.WriteLine("shard: "+ s.Location); 
            } 

            foreach (RangeMapping<long> rm in sm.GetMappings()) 
            { 
                Console.WriteLine("range: [" + rm.Value.Low.ToString() + ":" 
                        + rm.Value.High.ToString()+ ")  ==>" +rm.Shard.Location); 
            } 
        } 
 
También puede usar scripts de PowerShell para lograr el mismo resultado. Algunos de los ejemplos de PowerShell de ejemplo están disponibles [aquí](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Una vez que se han rellenado los mapas de particiones, se pueden crear o adaptar aplicaciones de acceso a datos para trabajar con los mapas. No es necesario rellenar o manipular los mapas de nuevo hasta que haya que cambiar el **diseño de mapa**.

## Enrutamiento dependiente de los datos 

El administrador de mapas de particiones se usará principalmente en aplicaciones que requieren conexiones de base de datos para realizar las operaciones de datos específicas de la aplicación. Esas conexiones deben estar asociadas con la base de datos correcta. Esto se conoce como **Enrutamiento dependiente de los datos**. En estas aplicaciones, cree instancias de un objeto de administrador de mapas de particiones predeterminado con credenciales que tengan acceso de solo lectura en la base de datos GSM. Las solicitudes individuales de conexiones posteriores proporcionan las credenciales necesarias para conectarse a la base de datos de particiones adecuada.

Tenga en cuenta que estas aplicaciones (que usan **ShardMapManager** abierto con credenciales de solo lectura) no podrán realizar cambios en los mapas ni las asignaciones. Para cubrir esas necesidades, cree aplicaciones específicas de administración o scripts de PowerShell que proporcionen credenciales con más privilegios, como se explicó anteriormente. Consulte [Credenciales usadas para acceder a la biblioteca de cliente de bases de datos elásticas](sql-database-elastic-scale-manage-credentials.md).

Para obtener más detalles, consulte [Enrutamiento dependiente de datos](sql-database-elastic-scale-data-dependent-routing.md).

## Modificación de un mapa de particiones 

Un mapa de particiones puede cambiarse de distintas maneras. Los siguientes métodos modifican los metadatos que describen las particiones y sus asignaciones, pero no modifican físicamente los datos incluidos en las particiones ni crean o eliminan las bases de datos reales. Algunas de las operaciones en el mapa de particiones que se describen a continuación, deben coordinarse con acciones administrativas que muevan físicamente los datos o que agreguen y quiten bases de datos que actúan como particiones.

Estos métodos funcionan en conjunto como los bloques de creación disponibles para modificar la distribución global de los datos en el entorno de base de datos particionada.

* Para agregar o quitar particiones, use **[CreateShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx)** y **[DeleteShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard.aspx)** de la clase [Shardmap](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.aspx). 
    
    El servidor y la base de datos que representa la partición de destino deben existir ya para que estas operaciones se ejecuten. Estos métodos no tienen ningún efecto en las propias bases de datos, solo en los metadatos del mapa de particiones.

* Para crear o quitar puntos o intervalos que se asignan a las particiones, use **[CreateRangeMapping](https://msdn.microsoft.com/library/azure/dn841993.aspx)**, **[DeleteMapping](https://msdn.microsoft.com/library/azure/dn824200.aspx)** de la clase [RangeShardMapping](https://msdn.microsoft.com/library/azure/dn807318.aspx) y **[CreatePointMapping](https://msdn.microsoft.com/library/azure/dn807218.aspx)** de la clase [ListShardMap](https://msdn.microsoft.com/library/azure/dn842123.aspx)
    
    Pueden asignarse muchos puntos o intervalos distintos a la misma partición. Estos métodos solo afectan a los metadatos, no afectan a los datos que puedan existir ya en particiones. Si es necesario quitar datos de la base de datos para mantener la coherencia con operaciones de **DeleteMapping**, deberá realizar esas operaciones por separado pero conjuntamente con el uso de estos métodos.

* Para dividir en dos los intervalos existentes o combinar en uno los intervalos adyacentes, use **[SplitMapping](https://msdn.microsoft.com/library/azure/dn824205.aspx)** y **[MergeMappings](https://msdn.microsoft.com/library/azure/dn824201.aspx)**.

    Tenga en cuenta que las operaciones de división y combinación **no cambian la partición a la que se asignan los valores de clave**. Una división divide un rango existente en dos partes, pero deja ambas partes como asignadas a la misma partición. Una combinación opera en dos intervalos adyacentes que ya están asignados a la misma partición, fusionándolos en un solo intervalo. El movimiento de puntos o intervalos propiamente dichos entre particiones debe coordinarse mediante **UpdateMapping** junto con el movimiento de datos real. Puede utilizar el servicio de **División y combinación** que forma parte de la vista previa de Escalado elástico para coordinar los cambios de mapas de particiones con el movimiento de datos, cuando se requiere movimiento.

* Para volver a asignar (o mover) puntos o intervalos individuales a particiones diferentes, use **[UpdateMapping](https://msdn.microsoft.com/library/azure/dn824207.aspx)**.

    Puesto que es posible que necesite mover datos de una partición a otra para ser coherente con las operaciones de **UpdateMapping**, deberá realizar dicho movimiento por separado pero conjuntamente con el uso de estos métodos.

* Para conectar y desconectar las asignaciones, use **[MarkMappingOffline](https://msdn.microsoft.com/library/azure/dn824202.aspx)** y **[MarkMappingOnline](https://msdn.microsoft.com/library/azure/dn807225.aspx)** para controlar el estado de conexión de una asignación.

    Solo se permite realizar determinadas operaciones en las asignaciones de particiones cuando una asignación se encuentra en un estado "sin conexión", incluidas **UpdateMapping** y **DeleteMapping**. Cuando una asignación está sin conexión, una solicitud dependiente de datos basada en una clave incluida en esa asignación devolverá un error. Además, cuando un intervalo se queda sin conexión por primera vez, todas las conexiones a la partición afectada se terminan automáticamente para evitar resultados incoherentes o incompletos para las consultas dirigidas a los intervalos que se están cambiando.

Las asignaciones son objetos inmutables en .NET Todos los métodos mencionados que cambian las asignaciones también invalidan las referencias a ellos en el código. Para facilitar la ejecución de secuencias de operaciones que cambian el estado de una asignación, todos los métodos que cambian una asignación devuelven una referencia de la asignación nueva, por lo que las operaciones se pueden encadenar. Por ejemplo, para eliminar una asignación existente en shardmap sm que contiene la clave 25, puede ejecutar lo siguiente:

        sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));

## Incorporación de una partición 

Con frecuencia, las aplicaciones simplemente necesitan agregar nuevas particiones para controlar los datos que se esperan de nuevas claves o intervalos de claves, en un mapa de particiones que ya existe. Por ejemplo, es posible que una aplicación particionada por identificador de inquilino necesite aprovisionar una nueva partición para un nuevo inquilino o que datos particionados mensualmente necesiten que se aprovisione una nueva partición antes del inicio de cada nuevo mes.

Si el nuevo intervalo de valores de clave ya no forma parte de una asignación existente y no se requiere ningún movimiento de datos, es muy sencillo agregar la nueva partición y asociar la nueva clave o el nuevo intervalo a dicha partición. Para obtener más información sobre cómo agregar nuevas particiones, consulte [Incorporación de una nueva partición](sql-database-elastic-scale-add-a-shard.md).

Sin embargo, para escenarios que requieren movimiento de datos, se necesita la herramienta de división y combinación para coordinar el movimiento de datos entre particiones con las actualizaciones necesarias de mapas de particiones. Para obtener más información sobre cómo usar la herramienta de división y combinación, consulte [Información general sobre división y combinación](sql-database-elastic-scale-overview-split-and-merge.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=AcomDC_0302_2016-->