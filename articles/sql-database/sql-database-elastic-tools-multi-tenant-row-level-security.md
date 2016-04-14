<properties 
	pageTitle="Aplicaciones de múltiples inquilinos con herramientas de bases de datos elásticas y seguridad de nivel de fila" 
	description="Aprenda a usar herramientas de bases de datos elásticas junto con la seguridad de nivel de fila para crear una aplicación con un nivel de datos altamente escalable en Base de datos de SQL de Azure que admite particiones de varios inquilinos." 
	metaKeywords="azure sql database elastic tools multi tenant row level security rls" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="tmullaney"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2016" 
	ms.author="thmullan;torsteng;sidneyh" />

# Aplicaciones de múltiples inquilinos con herramientas de bases de datos elásticas y seguridad de nivel de fila 

Las [herramientas de bases de datos elásticas](sql-database-elastic-scale-get-started.md) y la [seguridad de nivel de fila (RLS)](https://msdn.microsoft.com/library/dn765131) ofrecen un conjunto eficaz de capacidades para el escalado de manera flexible y eficaz del nivel de datos de una aplicación de múltiples inquilinos con Base de datos de SQL Azure. Este artículo muestra cómo usar estas tecnologías conjuntamente para crear una aplicación con un nivel de datos altamente escalable que admite particiones de múltiples inquilinos, con **ADO.NET SqlClient** o **Entity Framework**.

* Las **herramientas de bases de datos elásticas** permiten a los desarrolladores escalar horizontalmente el nivel de datos de una aplicación a través de las prácticas de particionamiento estándar del sector, mediante un conjunto de bibliotecas de .NET y plantillas de servicio de Azure. La administración de particiones con el uso de la biblioteca cliente de la base de datos elástica le ayuda a automatizar y simplificar muchas de las tareas de infraestructura asociadas típicamente con el particionamiento. 

* La **seguridad de nivel de fila** permite a los desarrolladores almacenar datos para varios inquilinos en la misma base de datos con las directivas de seguridad para filtrar las filas que no pertenecen al inquilino mediante la ejecución de una consulta. La centralización de la lógica de acceso con RLS dentro de la base de datos, en lugar de en la aplicación, simplifica el mantenimiento y reduce el riesgo de error a medida que crece el código base de la aplicación. RLS requiere la actualización más reciente de [Base de datos SQL de Azure (V12)](../sql-database-v12-whats-new.md).

Con todas estas características, una aplicación puede beneficiarse de mejoras de ahorro y la eficacia de costos al almacenar los datos para varios inquilinos en la misma base de datos de la partición. Al mismo tiempo, una aplicación todavía tiene la flexibilidad para ofrecer particiones aisladas de un único inquilino para los inquilinos "premium" que requieren garantías de rendimiento más estrictas ya que las particiones de varios inquilinos no garantizan la distribución equitativa de los recursos entre los inquilinos.

En resumen, las API de [enrutamiento dependiente de datos](sql-database-elastic-scale-data-dependent-routing.md) de la biblioteca cliente de la base de datos elástica conectan automáticamente los inquilinos a la base de datos de la partición correcta que contiene su clave de particionamiento (generalmente "TenantId"). Una vez conectado, una directiva de seguridad RLS dentro de la base de datos garantiza que los inquilinos solo pueden acceder a las filas que contienen su TenantId. Se supone que todas las tablas contienen una columna TenantId para indicar qué filas pertenecen a cada inquilino.

![Arquitectura de la aplicación de creación de blogs][1]

## Descarga del proyecto de ejemplo

### Requisitos previos
* Uso de Visual Studio (2012 o posterior) 
* Creación de tres Bases de datos SQL de Azure 
* Descargue el proyecto de ejemplo: [Herramientas de bases de datos elásticas para SQL de Azure - Particiones con varios inquilinos](http://go.microsoft.com/?linkid=9888163)
  * Rellene la información para las bases de datos al comienzo de **Program.cs** 

Este proyecto amplía el descrito en [Herramientas de bases de datos elásticas para SQL de Azure - Integración de Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) mediante la adición de compatibilidad con bases de datos de partición de varios inquilinos. Genera una aplicación de consola sencilla para la creación de blogs y publicaciones, con cuatro inquilinos y dos bases de datos de partición de varios inquilinos, tal como se muestra en el diagrama anterior.

Compile y ejecute la aplicación. Arrancará el administrador de mapas de particiones de las herramientas de bases de datos elásticas y ejecutará las siguientes pruebas:

1. Con Entity Framework y LINQ, cree un nuevo blog y muestre todos los blogs para cada inquilino.
2. Con ADO.NET SqlClient, muestre todos los blogs para un inquilino.
3. Intente insertar un blog del inquilino erróneo para comprobar que se produce un error.  

Tenga en cuenta que como todavía no se ha habilitado RLS en las bases de datos de la partición, cada una de estas pruebas revela un problema: los inquilinos pueden consultar los blogs que no pertenecen a ellos y la aplicación no impide insertar un blog del inquilino incorrecto. El resto de este artículo describe cómo resolver estos problemas mediante la imposición de aislamiento de inquilinos con RLS. Hay dos pasos:

1. **Capa de aplicación**: modifique el código de aplicación para establecer siempre el TenantId actual en SESSION\_CONTEXT después de abrir una conexión. El proyecto de ejemplo ya lo ha hecho. 
2. **Capa de datos**: cree una directiva de seguridad RLS en cada base de datos de partición para filtrar las filas en función del TenantId almacenado en SESSION\_CONTEXT. Deberá hacerlo para cada una de las bases de datos de la partición; en caso contrario, no se filtrarán las filas en las particiones de varios inquilinos. 


## Paso 1) Capa de aplicación: establezca TenantId en SESSION\_CONTEXT

Después de conectarse a una base de datos de partición mediante la API de enrutamiento dependiente de datos de la biblioteca cliente de la base de datos elástica, la aplicación todavía necesita indicar a la base de datos el TenantId que utiliza esa conexión para que una directiva de seguridad RLS puede filtrar las filas que pertenecen a otros inquilinos. El método recomendado para pasar esta información es almacenar el TenantId actual para esa conexión en [SESSION\_CONTEXT](https://msdn.microsoft.com/library/mt590806.aspx). (Nota: como alternativa, podría usar [CONTEXT\_INFO](https://msdn.microsoft.com/library/ms180125.aspx), pero SESSION\_CONTEXT es mejor opción porque es más fácil de usar, devuelve NULL de manera predeterminada y admite pares clave-valor).

### Entity Framework

Para las aplicaciones que usan Entity Framework, el enfoque más sencillo es establecer SESSION\_CONTEXT dentro de la invalidación de ElasticScaleContext descrita en [Enrutamiento dependiente de datos con DbContext de EF](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md/#data-dependent-routing-using-ef-dbcontext). Antes de devolver la conexión de intermediación a través de enrutamiento dependiente de datos, basta con crear y ejecutar un SqlCommand que establece "TenantId" en SESSION\_CONTEXT en la shardingKey especificada para esa conexión. De este modo, solo deberá escribir el código una vez para establecer SESSION\_CONTEXT.

```
// ElasticScaleContext.cs 
// ... 
// C'tor for data dependent routing. This call will open a validated connection routed to the proper 
// shard by the shard map manager. Note that the base class c'tor call will fail for an open connection 
// if migrations need to be done and SQL credentials are used. This is the reason for the  
// separation of c'tors into the DDR case (this c'tor) and the internal c'tor for new shards. 
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(OpenDDRConnection(shardMap, shardingKey, connectionStr), true /* contextOwnsConnection */)
{
}

public static SqlConnection OpenDDRConnection(ShardMap shardMap, T shardingKey, string connectionStr)
{
    // No initialization
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(shardingKey, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
} 
// ... 
```

Ahora SESSION\_CONTEXT se establece automáticamente con el TenantId especificado cada vez que se invoca ElasticScaleContext:

```
// Program.cs 
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
	using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))   
	{     
		var query = from b in db.Blogs
	                orderby b.Name
	                select b;
		
		Console.WriteLine("All blogs for TenantId {0}:", tenantId);     
		foreach (var item in query)     
		{       
			Console.WriteLine(item.Name);     
		}   
	} 
}); 
```

### SqlClient de ADO.NET 

Para aplicaciones que usan SqlClient de ADO.NET, el enfoque recomendado es crear una función de contenedor alrededor de ShardMap.OpenConnectionForKey() que establezca automáticamente "TenantId" en SESSION\_CONTEXT en el TenantId correcto antes de devolver una conexión. Para asegurarse de que SESSION\_CONTEXT esté siempre establecido, debe abrir conexiones únicamente con esta función de contenedor.

```
// Program.cs
// ...

// Wrapper function for ShardMap.OpenConnectionForKey() that automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection. As a best practice, you should only open connections using this 
// method to ensure that SESSION_CONTEXT is always set before executing a query.
public static SqlConnection OpenConnectionForTenant(ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key
        conn = shardMap.OpenConnectionForKey(tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient
// If row-level security is enabled, only Tenant 4's blogs will be listed
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine("--\nAll blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);
        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## Paso 2) Capa de datos: cree una directiva de seguridad de nivel de fila

### Creación de una directiva de seguridad para filtrar las filas a las que puede acceder cada inquilino

Ahora que la aplicación establece SESSION\_CONTEXT con el TenantId actual antes de consultar, una directiva de seguridad RLS puede filtrar las consultas y excluir las filas que tengan un TenantId diferente.

RLS se implementa en T-SQL: una función definida por el usuario define la lógica de acceso y una directiva de seguridad enlaza esta función a cualquier número de tablas. Para este proyecto, la función simplemente comprueba que la aplicación (en lugar de otro usuario de SQL) está conectada a la base de datos y que el "TenantId" almacenado en SESSION\_CONTEXT coincide con el TenantId de una fila determinada. Un predicado de filtro permitirá que las filas que cumplan estas condiciones pasen el filtro para consultas SELECT, UPDATE y DELETE; y un predicado de bloqueo impedirá que las filas que infringen estas condiciones se inserten o actualicen. Si no se estableció SESSION\_CONTEXT, devolverá NULL y no habrá filas visibles ni que puedan insertarse.

Para habilitar RLS, ejecute la siguiente instrucción T-SQL en todas las particiones con Visual Studio (SSDT), SSMS o el script de PowerShell incluido en el proyecto (o si está usando los [trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md), se pueden usar para automatizar la ejecución de este código T-SQL en todas las particiones):

```
CREATE SCHEMA rls -- separate schema to organize RLS objects 
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)     
	RETURNS TABLE     
	WITH SCHEMABINDING
AS
	RETURN SELECT 1 AS fn_accessResult          
		WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- the user in your application’s connection string (dbo is only for demo purposes!)         
		AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
	ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
	ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts
GO 
```

> [AZURE.TIP] Para los proyectos más complejos que deben agregar el predicado en cientos de tablas, puede usar un procedimiento almacenado auxiliar que genera automáticamente una directiva de seguridad mediante la adición de un predicado en todas las tablas de un esquema. Consulte [Apply Row-Level Security to all tables – helper script (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script) (Aplicación de la seguridad de nivel de fila a todas las tablas - script auxiliar (blog)).

Ahora, si vuelve a ejecutar la aplicación de ejemplo, los inquilinos verán solamente las filas que les pertenecen. Además, la aplicación no puede insertar las filas que pertenezcan a inquilinos diferentes del que esté está conectado en ese momento a la base de datos de la partición, ni puede actualizar las filas visibles para cambiar a otro TenantId. Si la aplicación intenta realizar una, se generará una excepción DbUpdateException.

Si agrega después una nueva tabla, solo tiene que modificar la directiva de seguridad y agregar predicados de filtro y bloqueo en la nueva tabla:

```
ALTER SECURITY POLICY rls.tenantAccessPolicy     
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
	ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable
GO 
```

### Adición de restricciones DEFAULT para rellenar automáticamente el TenantId para las inserciones 

Puede colocar una restricción predeterminada en cada tabla para rellenar automáticamente el TenantId con el valor almacenado actualmente en SESSION\_CONTEXT al insertar filas. Por ejemplo:

```
-- Create default constraints to auto-populate TenantId with the value of SESSION_CONTEXT for inserts 
ALTER TABLE Blogs     
	ADD CONSTRAINT df_TenantId_Blogs      
	DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO

ALTER TABLE Posts     
	ADD CONSTRAINT df_TenantId_Posts      
	DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO 
```

Ahora la aplicación no necesita especificar un TenantId al insertar filas:

```
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
	using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
	{
		var blog = new Blog { Name = name }; // default constraint sets TenantId automatically     
		db.Blogs.Add(blog);     
		db.SaveChanges();   
	} 
}); 
```

> [AZURE.NOTE] Si utiliza las restricciones DEFAULT para un proyecto de Entity Framework, se recomienda que NO incluyen la columna de TenantId en el modelo de datos EF. Esto es porque las consultas de Entity Framework proporcionan automáticamente los valores predeterminados que invalidarán las restricciones DEFAULT creadas en T-SQL que usan SESSION\_CONTEXT. Para utilizar las restricciones DEFAULT en el proyecto de ejemplo, por ejemplo, debe quitar el TenantId de DataClasses.cs (y ejecutar Add-Migration en la consola de administrador de paquetes) y usar T-SQL para asegurarse de que el campo solo existe en las tablas de base de datos. De este modo, EF no proporcionará automáticamente los valores predeterminados incorrectos al insertar datos.

### (Opcional) Habilitar un "superusuario" para tener acceso a todas las filas
Es posible que algunas aplicaciones deseen crear un "superusuario" que puede tener acceso a todas las filas; por ejemplo, para habilitar los informes en todos los inquilinos de todas las particiones, o bien para realizar operaciones de división y combinación en particiones que implican el desplazamiento de filas de inquilinos entre las bases de datos. Para habilitar esto, debe crear un nuevo usuario de SQL ("superusuario" en este ejemplo) en la base de datos de cada partición. Luego modifique la directiva de seguridad con una nueva función de predicado que permita al usuario tener acceso a todas las filas:

```
-- New predicate function that adds superuser logic
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult 
        WHERE 
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- note, should not be dbo!
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        ) 
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        )
GO

-- Atomically swap in the new predicate function on each table
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts
GO
```


### Mantenimiento 

* **Agregar particiones nuevas**: debe ejecutar el script T-SQL para habilitar RLS en las nuevas particiones; en caso contrario, no se filtrarán las consultas en esas particiones.

* **Agregar nuevas tablas**: debe agregar un predicado de filtro y bloqueo a la directiva de seguridad en todas las particiones siempre que se crea una nueva tabla; de lo contrario, no se filtrarán las consultas en la nueva tabla. Esto se puede automatizar mediante un desencadenador DDL, tal como se describe en [Apply Row-Level Security automatically to newly created tables (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx) (Aplicación de la seguridad de nivel de fila a las tablas recientemente creadas (blog)).


## Resumen 

Las herramientas de base de datos elásticas y la seguridad de nivel de fila pueden usarse juntas para escalar horizontalmente el nivel de datos de una aplicación con compatibilidad para particiones de un solo inquilino y de varios. Las particiones de varios inquilinos pueden utilizarse para almacenar datos de manera más eficaz (especialmente en casos donde un gran número de inquilinos solo tienen unas pocas filas de datos), mientras que las particiones de un único inquilino pueden usarse para admitir los inquilinos premium con requisitos más estrictos de aislamiento y rendimiento. Para obtener más información, vea la [referencia sobre la seguridad de nivel de fila](https://msdn.microsoft.com/library/dn765131).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->

<!---HONumber=AcomDC_0204_2016-->