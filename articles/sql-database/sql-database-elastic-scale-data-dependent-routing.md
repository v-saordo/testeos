<properties 
	pageTitle="Enrutamiento dependiente de los datos | Microsoft Azure" 
	description="Cómo usar la clase ShardMapManager en aplicaciones .NET para el enrutamiento dependiente de los datos, una característica de bases de datos elásticas para Base de datos SQL de Azure" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="torsteng" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/04/2016" 
	ms.author="torsteng;sidneyh"/>

#Enrutamiento dependiente de los datos

**Enrutamiento dependiente de los datos** es la posibilidad de utilizar los datos de una consulta para enrutar la solicitud a una base de datos adecuada. Se trata de un patrón fundamental cuando se trabaja con bases de datos particionadas. El contexto de solicitud también puede utilizarse para enrutar la solicitud, en especial si la clave de particionamiento no forma parte de la consulta. Cada consulta o transacción específica en una aplicación que usa enrutamiento dependiente de los datos tiene restringido el acceso a una base de datos única por solicitud. En las herramientas de Base de datos elástica de SQL Azure, este enrutamiento se efectúa con la **[clase ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)** en aplicaciones ADO.NET.

La aplicación no necesita realizar el seguimiento de las distintas cadenas de conexión o ubicaciones de base de datos asociadas con diferentes segmentos de datos en el entorno particionado. Por el contrario, el [Administrador de mapas de particiones](sql-database-elastic-scale-shard-map-management.md) abre las conexiones a las bases de datos correctas cuando es necesario, en función de los datos del mapa de particiones y del valor de la clave de particionamiento que es el destino de la solicitud de la aplicación. (Esta clave normalmente es *customer\_id*, *tenant\_id*, *date\_key* o algún otro identificador específico que es un parámetro fundamental de la solicitud de base de datos).

Para más información, consulte [Scaling Out SQL Server with Data Dependent Routing](https://technet.microsoft.com/library/cc966448.aspx) (Escalado horizontal de SQL Server con enrutamiento dependiente de los datos).

## Descarga de la biblioteca de cliente

Para obtener la clase, instale la [biblioteca de cliente de Base de datos elástica](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## Usar un ShardMapManager en una aplicación de enrutamiento dependiente de datos 

Las aplicaciones deben crear una instancia de **ShardMapManager** durante la inicialización, mediante la llamada de fábrica **[GetSQLShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)**. En este ejemplo, se inicializan **ShardMapManager** y un elemento **ShardMap** específico que contiene. En este ejemplo se muestran los métodos GetSqlShardMapManager y [GetRangeShardMap](https://msdn.microsoft.com/library/azure/dn824173.aspx).

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, 
                      ShardMapManagerLoadPolicy.Lazy);
    RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 

### Uso de credenciales de menor privilegio posible para obtener el mapa de particiones

Si una aplicación no manipula el propio mapa de particiones, las credenciales utilizadas en el método de fábrica deben tener simplemente permisos de solo lectura en la base de datos del **mapa de particiones global**. Estas credenciales normalmente son distintas de las credenciales que se usan para abrir conexiones con el administrador de mapa de particiones. Consulte también [Credenciales usadas para acceder a la biblioteca de cliente de bases de datos elásticas](sql-database-elastic-scale-manage-credentials.md).

## Llamada al método OpenConnectionForKey

El método **[ShardMap.OpenConnectionForKey](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx))** devuelve una conexión ADO.Net lista para emitir comandos a la base de datos adecuada según el valor del parámetro **key**. La información de particionamiento la almacena **ShardMapManager** en la caché de la aplicación, de modo que las solicitudes no implican normalmente una búsqueda de base de datos contra la base de datos **Mapa de particiones global**.

	// Syntax: 
	public SqlConnection OpenConnectionForKey<TKey>(
		TKey key,
		string connectionString,
		ConnectionOptions options
	)


* El parámetro **key** se usa como clave de búsqueda en el mapa de particiones para determinar la base de datos adecuada para la solicitud. 

* El elemento **connectionString** se usa para pasar únicamente las credenciales de usuario para la conexión deseada. Ningún nombre de base de datos o de servidor se incluye en esta *connectionString* dado que el método determinará la base de datos y el servidor usando el **ShardMap**.

* El valor de **[connectionOptions](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions.aspx)** se debe establecer en **ConnectionOptions.Validate** si se trata de un entorno donde los mapas de particiones pueden cambiar y las filas pueden moverse a otras bases de datos como resultado de operaciones de división o combinación. Esto implica una breve consulta al mapa de particiones local en la base de datos de destino (no al mapa de particiones global) antes de que se entregue la conexión a la aplicación.

Si falla la validación contra el mapa de particiones local (lo que indica que el caché es incorrecto), el Administrador de mapa de particiones consultará el mapa de particiones global para obtener el nuevo valor correcto de la consulta, actualizar la caché y obtener y devolver la conexión de base de datos adecuada.

Utilice **ConnectionOptions.None** solo cuando no se esperen cambios de asignación de particiones mientras una aplicación está en línea. En ese caso, los valores en caché se pueden asumir como correctos siempre y la llamada de validación de ida y vuelta adicional a la base de datos de destino se puede omitir sin problemas. Esto reduce el tráfico de la base de datos. **connectionOptions** también se puede definir a través de un valor en un archivo de configuración para indicar si se esperan o no cambios en el particionamiento durante un período.

En este ejemplo se utiliza el valor de una clave de entero **CustomerID**, mediante un objeto **ShardMap** denominado **customerShardMap**.

    int customerId = 12345; 
    int newPersonId = 4321; 

    // Connect to the shard for that customer ID. No need to call a SqlConnection 
	// constructor followed by the Open method.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, 
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command. 
        SqlCommand cmd = conn.CreateCommand(); 
        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 
    }  

El método **OpenConnectionForKey** devuelve una nueva conexión ya abierta a la base de datos correcta. Las conexiones utilizadas de esta manera seguirán aprovechando completamente las agrupaciones de conexiones de ADO.Net. Siempre y cuando las transacciones y las solicitudes puedan verse satisfecha por una partición a la vez, esta debiera ser la única modificación necesaria en una aplicación utilizando ya ADO.Net.

El método **[OpenConnectionForKeyAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync.aspx)** también está disponible si su aplicación hace uso de programación asincrónica con ADO.Net. Su comportamiento es equivalente al enrutamiento dependiente de los datos del método ****[Connection.OpenAsync] de ADO.Net (https://msdn.microsoft.com/library/hh223688(v=vs.110).aspx)**.

## Integración con el control de errores transitorios 

Un procedimiento recomendado para el desarrollo de aplicaciones de acceso a datos en la nube es tener la seguridad de que la aplicación es capaz de capturar los errores transitorios y que las operaciones se reintentan varias veces antes de presentarse un error. El control de errores transitorios para aplicaciones en la nube se describe en [Control de errores transitorios](https://msdn.microsoft.com/library/dn440719(v=pandp.60).aspx).
 
El control de errores transitorios puede coexistir naturalmente con el patrón de Enrutamiento dependiente de los datos. El requisito clave es volver a intentar la solicitud de acceso a los datos completa, incluido el bloque **using** que obtuvo la conexión de enrutamiento dependiente de los datos. El ejemplo anterior se podría reescribir de la siguiente manera (observe el cambio resaltado).

### Ejemplo: enrutamiento dependiente de los datos con control de errores temporales 

<pre><code>int customerId = 12345; 
int newPersonId = 4321; 

<span style="background-color:  #FFFF00">Configuration.SqlRetryPolicy.ExecuteAction(() => </span> 
<span style="background-color:  #FFFF00">    { </span>
        // Connect to the shard for a customer ID. 
        using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId,  
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
        { 
            // Ejecutar un comando simple 
            SqlCommand cmd = conn.CreateCommand(); 

            cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

            cmd.Parameters.AddWithValue("@customerID", customerId); 
            cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
            cmd.ExecuteNonQuery(); 

            Console.WriteLine("Actualización completada"); 
        } 
<span style="background-color:  #FFFF00">    }); </span> 
</code></pre>


Los paquetes necesarios para implementar el control de errores transitorio se descargan automáticamente cuando crea la aplicación de ejemplo de base de datos elástica. Los paquetes también están disponibles por separado en [Biblioteca de información empresarial: bloque de aplicación Control de errores transitorios](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/). Use la versión 6.0 o posterior.

## Coherencia de las transacciones 

Las propiedades de las transacciones están garantizadas para todas las operaciones locales respecto a una partición. Por ejemplo, las transacciones enviadas a través del enrutamiento dependiente de los datos se ejecutan dentro del ámbito de la partición de destino de la conexión. En este momento, no se proporcionan funcionalidades para alistar conexiones múltiples en una transacción y, por lo tanto, no hay garantías de transacciones para las operaciones realizadas a través de las particiones.

## Pasos siguientes
Para desasociar una partición, o volver a adjuntar una partición, consulte [Uso de la clase RecoveryManager para solucionar problemas de mapas de particiones](sql-database-elastic-database-recovery-manager.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=AcomDC_0211_2016-->