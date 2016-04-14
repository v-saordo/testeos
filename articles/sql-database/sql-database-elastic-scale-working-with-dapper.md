<properties 
	pageTitle="Uso de la biblioteca de cliente de Base de datos elástica con Dapper | Microsoft Azure" 
	description="Uso de la biblioteca de cliente de bases de datos elásticas con Dapper." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="torsteng"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/01/2016" 
	ms.author="torsteng;sidneyh"/>

# Uso de la biblioteca de cliente de bases de datos elásticas con Dapper 

Este documento está dirigido a desarrolladores que utilizan Dapper para compilar aplicaciones, pero que también desean adaptar las [herramientas de bases de datos elásticas](sql-database-elastic-scale-introduction.md) para crear aplicaciones que implementen el particionamiento para escalar horizontalmente su capa de datos. Este documento muestra los cambios que es necesario realizar en las aplicaciones basadas en Dapper para su integración con las herramientas de bases de datos elásticas. Nuestro enfoque se centra en componer la administración de particiones de bases de datos elásticas y el enrutamiento dependiente de los datos con Dapper.

**Código de ejemplo**: [Herramientas de bases de datos elásticas para Base de datos SQL de Azure: integración con Dapper](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).
 
La integración de **Dapper** y **DapperExtensions** con la biblioteca de cliente de bases de datos elásticas para Base de datos SQL de Azure es sencilla. Las aplicaciones pueden usar el enrutamiento dependiente de los datos cambiando la creación y apertura de nuevos objetos [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) para que usen la llamada [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) desde la [biblioteca de cliente](http://msdn.microsoft.com/library/azure/dn765902.aspx). Esto limita los cambios en la aplicación a solo cuando se crean y se abren conexiones nuevas.

## Información general sobre Dapper
**Dapper** es un asignador relacional de objetos. Asigna objetos .NET desde su aplicación a una base de datos relacional (y viceversa). La primera parte del código de ejemplo muestra cómo se puede integrar la biblioteca de cliente de bases de datos elásticas con aplicaciones basadas en Dapper. La segunda parte del código de ejemplo muestra cómo se puede integrar cuando se usa Dapper y DapperExtensions.

La funcionalidad del asignador en Dapper proporciona métodos de extensión en las conexiones de base de datos que simplifican el envío de instrucciones T-SQL para la ejecución o las consultas de la base de datos. Por ejemplo, Dapper facilita la asignación entre los objetos .NET y los parámetros de las instrucciones SQL para las llamadas **Execute** o para consumir los resultados de las consultas SQL en objetos .NET mediante llamadas **Query** desde Dapper.

Cuando se utiliza DapperExtensions, ya no es necesario proporcionar las instrucciones SQL. Los métodos de extensión, por ejemplo, **GetList** o **Insert** sobre la conexión de la base de datos, crean las instrucciones SQL en segundo plano.
 
Otra ventaja de Dapper y también de DapperExtensions es que la aplicación controla la creación de la conexión de la base de datos. Esto permite interactuar con la biblioteca de cliente de bases de datos elásticas que negocia las conexiones de las bases de datos basadas en la asignación de shardlets a las bases de datos.

Para obtener los ensamblados de Dapper, consulte [Dapper punto net](http://www.nuget.org/packages/Dapper/). Para las extensiones de Dapper, consulte [DapperExtensions](http://www.nuget.org/packages/DapperExtensions).

## Una vista rápida a la biblioteca de cliente de bases de datos elásticas

Con la biblioteca de cliente de bases de datos elásticas, define las particiones de los datos de aplicación llamadas *shardlets*, las asigna a bases de datos y las identifica mediante *claves de particionamiento*. Puede tener tantas bases de datos como necesite y distribuir los shardlets por dichas bases de datos. La asignación de valores de clave de particionamiento a las bases de datos se almacena en un mapa de particiones que proporcionan las API de la biblioteca. Esta funcionalidad se llama **administración de mapas de particiones**. El mapa de particiones también funciona como el agente de conexiones de base de datos para las solicitudes que llevan una clave de particionamiento. Esta funcionalidad se conoce como **enrutamiento dependiente de los datos**.

![Mapas de particiones y enrutamiento dependiente de los datos][1]

El administrador de mapas de particiones protege a los usuarios frente a vistas incoherentes en datos de shardlet que se pueden generar cuando se producen operaciones de administración de shardlets simultáneas en las bases de datos. Para hacerlo, los mapas de particiones negocian las conexiones de base de datos de una aplicación compilada con la biblioteca. En el caso de que las operaciones de administración de particiones puedan afectar al shardlet, la funcionalidad de asignación de particiones puede eliminar automáticamente una conexión de base de datos.

En lugar de usar la forma tradicional de crear conexiones para Dapper, hay que usar el método [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn824099.aspx). Esto garantiza que se lleva a cabo toda la validación y que las conexiones se administran correctamente cuando los datos se mueven entre las particiones.

### Requisitos para la integración de Dapper

Cuando se trabaja con la biblioteca de cliente de bases de datos elásticas y las API de Dapper, conviene conservar las propiedades siguientes:

* **Ampliación**: es necesario poder agregar o quitar bases de datos de la capa de datos de la aplicación particionada cuando las demandas de capacidad de la aplicación lo requieran. 

-    **Coherencia**: dado que nuestra aplicación se amplía mediante particionamiento, necesitamos realizar el enrutamiento dependiente de datos. Para ello, queremos usar las funciones de enrutamiento dependiente de los datos de la biblioteca. En particular, queremos conservar las garantías de coherencia y validación que proporcionan las conexiones administradas mediante el administrador de mapas de particiones para evitar daños o que se generen unos resultados incorrectos de la consulta. Esto garantiza que las conexiones a un shardlet determinado se rechazan o se detienen si, por ejemplo, el shardlet se mueve actualmente a una partición diferente mediante las API de división y combinación.

-    **Asignación de objetos**: queremos conservar la comodidad de las asignaciones de Dapper para traducir entre las clases de la aplicación y las estructuras de base de datos subyacentes.

La siguiente sección proporciona orientación sobre estos requisitos para aplicaciones basadas en **Dapper** y **DapperExtensions**.

## Orientación técnica
### Enrutamiento dependiente de los datos con Dapper 

Con Dapper, la aplicación suele ser responsable de crear y abrir las conexiones con la base de datos subyacente. Si la aplicación muestra un tipo T, Dapper devuelve los resultados de la consulta como colecciones .NET de tipo T. Dapper realiza la asignación de las filas de resultados de T-SQL a los objetos de tipo T. De forma similar, Dapper asigna objetos .NET a valores SQL o a parámetros para instrucciones DML (lenguaje de manipulación de datos). Dapper ofrece esta funcionalidad a través de métodos de extensión en el objeto [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) regular desde las bibliotecas de cliente SQL .NET de ADO. La conexión SQL devuelta por las API de Escalado elástico para DDR también son objetos [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) regulares. Esto nos permite utilizar directamente las extensiones Dapper en el tipo devuelto por la API de DDR de la biblioteca de cliente, ya que también es una conexión de cliente SQL simple.

Estas observaciones hacen que sea muy sencillo usar conexiones negociadas por la biblioteca de cliente de bases de datos elásticas para Dapper.

Este ejemplo de código (del ejemplo adjunto) muestra el enfoque donde la aplicación proporciona la clave de particionamiento a la biblioteca para negociar la conexión con la partición adecuada.

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1, 
                     connectionString: connStrBldr.ConnectionString, 
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }

La llamada a la API [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) reemplaza la creación predeterminada y la apertura de una conexión de cliente SQL. La nueva llamada [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) adopta los argumentos necesarios para el enrutamiento dependiente de los datos:

-    El mapa de particiones para tener acceso a las interfaces de enrutamiento dependiente de datos
-    La clave de particionamiento para identificar el shardlet
-    Las credenciales (nombre de usuario y contraseña) para conectarse a la partición

El objeto de mapa de particiones crea una conexión con la partición que contiene el shardlet para la clave de particionamiento especificada. Las API de cliente de base de datos elástica también etiquetan la conexión para implementar sus garantías de coherencia. Como la llamada a [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) devuelve un objeto de conexión de cliente SQL regular, la siguiente llamada al método de extensión **Execute** desde Dapper sigue la práctica estándar de Dapper.

Las consultas funcionan de forma similar en gran medida: primero, se abre la conexión mediante [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) desde la API de cliente. A continuación, se usan los métodos de extensión de Dapper regulares para asignar los resultados de la consulta SQL a los objetos .NET:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate ))
    {    
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT * 
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }

Tenga en cuenta que el hecho de **usar** el bloque con la conexión DDR abarca todas las operaciones de la base de datos dentro del bloque hasta la partición concreta donde se guarda tenantId1. La consulta devuelve solo blogs almacenados en la partición actual, pero no los almacenados en otras particiones.

## Enrutamiento dependiente de datos con Dapper y DapperExtensions

Dapper incluye un ecosistema de extensiones adicionales que puede proporcionar más comodidad y abstracción a partir de la base de datos al desarrollar aplicaciones de base de datos. DapperExtensions es un ejemplo.

El hecho de usar DapperExtensions en la aplicación no cambia la forma en que se crean y administran las conexiones de base de datos. Abrir las conexiones sigue siendo responsabilidad de la aplicación. Además, los métodos de extensión esperan objetos de conexión de cliente SQL regulares. Podemos confiar en [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx), tal y como se indicó. Como muestran los siguientes ejemplos de código, el único cambio es que ya no tenemos que escribir las instrucciones T-SQL:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

Y este es el código de ejemplo para la consulta:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }

### Control de errores transitorios

El equipo de Microsoft Patterns & Practices publicó el [bloque de aplicación de control de errores transitorios](http://msdn.microsoft.com/library/hh680934.aspx) para ayudar a los desarrolladores a mitigar las condiciones de errores transitorios que se producen al realizar ejecuciones en la nube. Si desea obtener más información, consulte: [La perseverancia, el secreto de todos los triunfos: uso del bloque de aplicaciones de control de errores transitorios](http://msdn.microsoft.com/library/dn440719.aspx).

El ejemplo de código está basado en la biblioteca de errores transitorios para protegerse frente a errores transitorios.

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn = 
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** en el código anterior se define como **SqlDatabaseTransientErrorDetectionStrategy** con un número de reintentos de 10 y un tiempo de espera de 5 segundos entre reintentos. Si utiliza transacciones, asegúrese de que el ámbito de reintentos vuelva al principio de la transacción en el caso de un error transitorio.

## Limitaciones

Los métodos descritos en este documento implican un par de limitaciones:

* El código de ejemplo de este documento no muestra cómo administrar el esquema entre las particiones.
* Dada una solicitud, se supone que todo el procesamiento de la base de datos está contenido en una sola partición que se identifica con la clave de particionamiento especificada por la solicitud. Sin embargo, esta suposición no siempre es posible, por ejemplo, cuando no se puede disponer de una clave de particionamiento. Para solucionar este problema, la biblioteca de cliente de bases de datos elásticas incluye la [clase MultiShardQuery](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). La clase implementa una abstracción de la conexión para realizar consultas en varias particiones. El uso de MultiShardQuery en combinación con Dapper está fuera del ámbito de este documento.

## Conclusión

Las aplicaciones que usan Dapper y DapperExtensions pueden beneficiarse fácilmente de las herramientas de bases de datos elásticas para Base de datos SQL de Azure. Mediante los pasos descritos en este documento, esas aplicaciones pueden usar la funcionalidad de la herramienta para el enrutamiento dependiente de los datos cambiando la creación y apertura de nuevos objetos [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) para usar la llamada [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) de la biblioteca de cliente de bases de datos elásticas. Esto limita los cambios requeridos en la aplicación a solo cuando se crean y se abren conexiones nuevas.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
 

<!---HONumber=AcomDC_0204_2016-->