<properties
	pageTitle="Reintento de EntLib para la conexión a Base de datos SQL | Microsoft Azure"
	description="Enterprise Library está diseñado para simplificar muchas tareas para programas cliente de servicios en la nube, incluida la integración de la lógica de reintento en caso de errores transitorios."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor="" />


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="genemi"/>


# Ejemplo de código: lógica de reintento de Enterprise Library 6, en C&#x23; para conectarse a Base de datos SQL

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Este tema presenta un ejemplo de código completo que muestra Enterprise Library (EntLib). EntLib simplifica muchas tareas para programas cliente que interactúan con servicios en la nube como Base de datos SQL de Azure. Nuestro ejemplo se centra en la tarea importante de incluir la lógica de reintento en caso de errores transitorios.


Las clases de EntLib están diseñadas para distinguir dos categorías de errores de tiempo de ejecución:

- Errores que nunca se corregirán automáticamente, como un nombre de servidor mal escrito.
- Errores transitorios, como la suspensión del servidor durante varios segundos y su aceptación de nuevas conexiones, mientras el sistema de Azure equilibra cargas.


Enterprise Library 6 (EntLib60) es la versión más reciente y se lanzó en abril de 2013.

- Microsoft ha publicado el código fuente para el público.
- Microsoft no tiene planes para mantener aún más el código fuente.


## Requisitos previos


#### .NET Framework 4.0 o superior


Debe estar instalado Microsoft .NET Framework 4.0 o una versión superior. En el momento de la redacción de ese documento, se encuentra disponible la versión 4.6. Recomendamos usar la versión más reciente.


#### Edición Visual Studio Community. Es gratis


Necesita una manera para compilar el código fuente de este ejemplo. Una manera es instalar la edición gratis de [Microsoft Visual Studio *Community*](http://www.visualstudio.com/products/free-developer-offers-vs.aspx).


Es posible que deba registrar su dirección de correo electrónico con MSDN. Los pasos son similares a los siguientes:


1. [Vaya a MSDN](http://msdn.microsoft.com/).
2. Haga clic en **Suscripciones a MSDN** cerca de la parte superior.
3. Haga clic en **Regístrese ahora**.
4. Rellene el formulario con su información.
5. Haga clic en **Crear una cuenta** en la parte inferior.


#### Enterprise Library 6 (EntLib60)


Formas de instalar EntLib60:


- Use la característica de Administrador de paquetes *NuGet* en Visual Studio:
 - En NuGet, busque **enterpriselibrary**.


- En el [tema de documentación principal de EntLib60](http://msdn.microsoft.com/library/dn169621.aspx), encuentre la fila etiquetada como **Descargas** y luego haga clic en [Microsoft Enterprise Library 6](http://go.microsoft.com/fwlink/?linkid=290898) para descargar los archivos de ensamblado .DLL binarios.


EntLib60 tiene varios archivos de ensamblado .DLL con nombres que comienzan con el mismo prefijo **Microsoft.Practices.EnterpriseLibrary.&#x2a;.dll**, pero a este código de ejemplo solo le interesan los siguientes dos ensamblados:

- Microsoft.Practices.EnterpriseLibrary.**TransientFaultHandling**.dll
- Microsoft.Practices.EnterpriseLibrary.**TransientFaultHandling.Data**.dll


## Cómo encajan las clases de EntLib


Las clases de EntLib se usan para construir otras clases de EntLib. En este ejemplo de código, la secuencia de construcción y uso es como aparece a continuación:


1. Construya un objeto **ExponentialBackoff**.
2. Construya un objeto **SqlDatabaseTransientErrorDetectionStrategy**.
3. Construya un objeto **RetryPolicy**. Los parámetros de entrada son:
 - Objeto **ExponentialBackoff**.
 - Objeto **SqlDatabaseTransientErrorDetectionStrategy**.
4. Construya un objeto **ReliableSqlConnection**. Los parámetros de entrada son:
 - Un objeto **String**, con el nombre de servidor y demás información de conexión.
 - Objeto **RetryPolicy**.
5. Llamada a conexión, a través del método **RetryPolicy .ExecuteAction**.
6. Llame al método **ReliableSqlConnection .CreateCommand**.
 - Devuelve un objeto **System.SqlClient.Data.DbCommand**, parte de ADO.NET.
7. Llamada a consulta, a través del método **RetryPolicy .ExecuteAction**.


## Compilar y ejecutar el ejemplo de código


El ejemplo de código fuente de Program.cs aparece más adelante en este tema. Puede compilar y ejecutar el ejemplo con los pasos siguientes:


1. En Visual Studio, cree un nuevo proyecto desde una plantilla de aplicación de consola de C#.

2. En el panel del Explorador de soluciones, edite el archivo Program.cs casi vacío. Para ello, reemplace el contenido de inicio por el código de Program.cs proporcionado más adelante en este tema.

3. Use el menú Compilar > Compilar solución en Visual Studio para compilar el proyecto.

4. En una ventana de comandos cmd.exe, ejecute el programa como se muestra a continuación. También se muestra la salida real de una ejecución:


```
[C:\MyVS\EntLib60Retry\EntLib60Retry\bin\Debug]
>> EntLib60Retry.exe

database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535

[C:\MyVS\EntLib60Retry\EntLib60Retry\bin\Debug]
>>
```


&nbsp;


## Código fuente de Program.cs


El siguiente archivo Program.cs contiene todo el código fuente de este ejemplo de EntLib.


```
using     System;   // C#
using G = System.Collections.Generic;
using D = System.Data;
using C = System.Data.SqlClient;
using X = System.Text;
using H = System.Threading;
using Y = Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;

namespace EntLib60Retry
{
   class Program
   {
      static void Main(string[] args)
      {
         Program program = new Program();

         if (program.Run(args) == false)
         {
            Console.WriteLine("Something was unable to complete.  :-( ");
         }
      }

      bool Run(string[] _args)
      {
         int retryIntervalSeconds = 10;
         bool returnBool = false;

         this.InitializeEntLibObjects();

         for (int tries = 1; tries <= 3; tries++)
         {
            if (tries > 1)
            {
               H.Thread.Sleep(1000 * retryIntervalSeconds);
               retryIntervalSeconds = Convert.ToInt32(retryIntervalSeconds * 1.5);
            }

            try
            {
               this.reliableSqlConnection = new Y.ReliableSqlConnection(
                  this.sqlConnectionSB.ToString(),
                  this.retryPolicy, null
                  );
               this.retryPolicy.ExecuteAction(this.OpenTheConnection_action);  // Open the connection.

               this.dbCommand = this.reliableSqlConnection.CreateCommand();
               this.dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";

               // We retry connection .Open after transient faults, but
               // we do not retry commands that use the connection.
               this.IssueTheQuery();  // Run the query, loop through results.

               returnBool = true;
               break;
            }

            catch (C.SqlException sqlExc)
            {
               if (false == this.sqlDatabaseTransientErrorDetectionStrategy.IsTransient(sqlExc))
               {
                  throw sqlExc;
               }
            }
         }
         return returnBool;
      }

      void OpenTheConnection()  // Called by .ExecuteAction.
      {
         this.reliableSqlConnection.Open();
      }

      void IssueTheQuery()      // Called by .ExecuteAction.
      {
         X.StringBuilder sBuilder = new X.StringBuilder(256);

         this.dataReader = this.dbCommand.ExecuteReader();

         while (this.dataReader.Read())
         {
            sBuilder.Length = 0;
            sBuilder.Append(this.dataReader.GetString(0));
            sBuilder.Append("\t");
            sBuilder.Append(this.dataReader.GetString(1));

            Console.WriteLine(sBuilder.ToString());
         }
      }

      void InitializeEntLibObjects()
      {
         this.InitializeSqlConnectionStringBuilder();

         this.exponentialBackoff = new Y.ExponentialBackoff(
            "exponentialBackoff",
             3,                    // Maximum number of times to retry, then let exception bubble up.
             TimeSpan.FromMilliseconds(10000), // Minimum interval between retries.
             TimeSpan.FromMilliseconds(30000), // Maximum interval between retries.
             TimeSpan.FromMilliseconds( 2000)  // For random differences in interval between retries.
            );
         this.sqlDatabaseTransientErrorDetectionStrategy =
            new Y.SqlDatabaseTransientErrorDetectionStrategy();
         this.retryPolicy = new Y.RetryPolicy(
               this.sqlDatabaseTransientErrorDetectionStrategy,
               this.exponentialBackoff
               );
         this.OpenTheConnection_action = delegate() { this.OpenTheConnection(); };
      }

      void InitializeSqlConnectionStringBuilder()
      {
         // Prepare the connection string to Azure SQL Database.
         this.sqlConnectionSB = new C.SqlConnectionStringBuilder();

         // Change these values to your values.
         this.sqlConnectionSB["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
         this.sqlConnectionSB["User ID"] = "MyLogin";  // "@yourservername"  as suffix sometimes.
         this.sqlConnectionSB["Password"] = "MyPassword";
         this.sqlConnectionSB["Database"] = "MyDatabase";

         // Leave these values as they are.
         this.sqlConnectionSB["Trusted_Connection"] = false;
         this.sqlConnectionSB["Integrated Security"] = false;
         this.sqlConnectionSB["Encrypt"] = true;
         this.sqlConnectionSB["Connection Timeout"] = 30;
      }

      Program()   // Constructor.
      {
         int[] arrayOfTransientErrorNumbers =
                { 4060, 10928, 10929, 40197, 40501, 40613, 49918, 49919, 49920
                    //,11001   // 11001 for testing, pretend network error is transient.
                    //,18456   // 18456 for testing, purposely misspell database name.
                };
         listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
      }

      // Fields.
      private G.List<int> listTransientErrorNumbers;

      private Y.ReliableSqlConnection reliableSqlConnection;
      private Y.ExponentialBackoff exponentialBackoff;
      private Y.SqlDatabaseTransientErrorDetectionStrategy
                   sqlDatabaseTransientErrorDetectionStrategy;
      private Y.RetryPolicy retryPolicy;

      private Action OpenTheConnection_action;

      private C.SqlConnectionStringBuilder sqlConnectionSB;
      private D.IDbCommand dbCommand;
      private D.IDataReader dataReader;
   }
}
```


&nbsp;


## Vínculos relacionados


- Existen varios vínculos a información adicional en: [Enterprise Library 6: abril de 2013](http://msdn.microsoft.com/library/dn169621.aspx)
 - Este tema tiene un botón en la parte superior que ofrece [descargar el código fuente de EntLib60 ](http://go.microsoft.com/fwlink/p/?LinkID=290898), si le interesa ver el código fuente.


- Libro electrónico gratuito de Microsoft en formato PDF:[ Guía del desarrollador para Microsoft Enterprise Library, segunda edición](http://www.microsoft.com/download/details.aspx?id=41145).


- [Espacio de nombres Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling](http://msdn.microsoft.com/library/microsoft.practices.enterpriselibrary.transientfaulthandling.aspx)


- [Referencia de Biblioteca de clases de Enterprise Library 6](http://msdn.microsoft.com/library/dn170426.aspx)


- [Ejemplo de código: lógica de reintento en C# para conectarse a la Base de datos SQL con ADO.NET](sql-database-develop-csharp-retry-windows.md)


- [Ejemplos de código de inicio rápido de cliente para Base de datos SQL](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=AcomDC_0107_2016-->