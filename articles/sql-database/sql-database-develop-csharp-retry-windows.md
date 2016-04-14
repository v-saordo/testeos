<properties
	pageTitle="Lógica de reintentos de C# para la conexión a la Base de datos de SQL | Microsoft Azure"
	description="La muestra de C# incluye lógica de reintentos para interactuar de manera fiable con la base de datos de SQL de Azure."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="genemi"/>


# Ejemplo de código: lógica de reintento en C# para conectarse a la Base de datos SQL



[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]



Este tema ofrece un ejemplo de código C# que muestra la lógica de reintento personalizada. La lógica de reintento está diseñada para procesar correctamente los errores temporales o *errores transitorios* que tienden a desaparecer si el programa espera unos segundos y vuelve a intentarlo.


Las clases de ADO.NET que se usan para conectarse a su Microsoft SQL Server local también pueden conectarse a la Base de datos SQL de Azure. Sin embargo, por sí mismas las clases ADO.NET no pueden proporcionar la solidez y confiabilidad necesarias en el uso de producción. Su programa cliente puede encontrar errores transitorios de los que debería recuperarse de forma silenciosa y correcta por sí mismo.


A continuación se muestran un par de ejemplos de errores transitorios:


- Una conexión a través de Internet está sujeta a breves interrupciones de red, tras las cuales se puede volver a crear la conexión.

- La informática en la nube implica el equilibrio de carga que puede bloquear brevemente intentos de conectarse o realizar consultas.


## Identificar errores transitorios


El programa debe distinguir entre errores transitorios frente a errores persistentes. Si se programa tiene un error ortográfico en el nombre de base de datos de destino, el error "No se encontró esa base de datos" persistirá y se repetirá cada vez que vuelva a ejecutar el programa.


La lista de números de error que se clasifican como errores transitorios está disponible en:


- [Mensajes de error para los programas de cliente de base de datos SQL](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - Vea la sección superior sobre *Errores transitorios, errores de pérdida de conexión*.


## Ejemplo de código C#


El ejemplo de código C# del tema actual contiene la lógica de reintento y detección personalizada para controlar errores transitorios. En el ejemplo se da por supuesto que está instalado .NET Framework 4.5.1 o posterior.


El ejemplo de código sigue unas recomendaciones o directrices básicas que se aplican sin tener en cuenta la tecnología que se usa para interactuar con la Base de datos SQL de Azure. Puede ver las recomendaciones generales en:


- [Conexión a la base de datos SQL: vínculos, prácticas recomendadas y directrices de diseño](sql-database-connect-central-recommendations.md)


La muestra de código C# consta de un archivo denominado Program.cs. Su código se pega en la sección siguiente.


### Captura y compilación de la muestra de código


Puede compilar el ejemplo con los pasos siguientes:


1. En la [edición gratuita de Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs), cree un nuevo proyecto desde la plantilla de aplicación de consola de C#.
 - Archivo > Nuevo > Proyecto > Instalado > Plantillas > Visual C# > Windows > Escritorio clásico > Aplicación de consola
 - Asigne al proyecto el nombre **RetryAdo2**.

2. Abra el panel del Explorador de soluciones.
 - Vea el nombre del proyecto.
 - Vea el nombre del archivo Program.cs.

3. Abra el archivo Program.cs.

4. Reemplace el contenido del archivo Program.cs por el código siguiente:

5. Haga clic en el menú Compilar > Compilar solución.


#### Código fuente de C# para pegar


Pegue este código en su archivo **Program.cs**.


A continuación, debe modificar las cadenas del nombre de servidor, la contraseña y así sucesivamente. Encontrará estas cadenas en el método denominado **GetSqlConnectionStringBuilder**.


```
using System;   // C#
using G = System.Collections.Generic;
using D = System.Data;
using C = System.Data.SqlClient;
using X = System.Text;
using H = System.Threading;

namespace RetryAdo2
{
	class Program
	{
		static void Main(string[] args)
		{
			Program program = new Program();
			bool returnBool;

			returnBool = program.Run(args);
			if (returnBool == false)
			{
				Console.WriteLine("Something failed.  :-( ");
			}
			return;
		}

		bool Run(string[] _args)
		{
			C.SqlConnectionStringBuilder sqlConnectionSB;
			C.SqlConnection sqlConnection;
			D.IDbCommand dbCommand;
			D.IDataReader dataReader;
			X.StringBuilder sBuilder = new X.StringBuilder(256);
			int retryIntervalSeconds = 10;
			bool returnBool = false;

			for (int tries = 1; tries <= 5; tries++)
			{
				try
				{
					if (tries > 1)
					{
						H.Thread.Sleep(1000 * retryIntervalSeconds);
						retryIntervalSeconds = Convert.ToInt32(retryIntervalSeconds * 1.5);
					}
					this.GetSqlConnectionStringBuilder(out sqlConnectionSB);

					sqlConnection = new C.SqlConnection(sqlConnectionSB.ToString());

					dbCommand = sqlConnection.CreateCommand();
					dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";

					sqlConnection.Open();
					dataReader = dbCommand.ExecuteReader();

					while (dataReader.Read())
					{
						sBuilder.Length = 0;
						sBuilder.Append(dataReader.GetString(0));
						sBuilder.Append("\t");
						sBuilder.Append(dataReader.GetString(1));

						Console.WriteLine(sBuilder.ToString());
					}
					returnBool = true;
					break;
				}

				catch (C.SqlException sqlExc)
				{
					if (this.m_listTransientErrorNumbers.Contains(sqlExc.Number) == true)
					{ continue; }
					else
					{ throw sqlExc; }
				}
			}
			return returnBool;
		}

		void GetSqlConnectionStringBuilder(out C.SqlConnectionStringBuilder _sqlConnectionSB)
		{
			// Prepare the connection string to Azure SQL Database.
			_sqlConnectionSB = new C.SqlConnectionStringBuilder();

			// Change these values to your values.
			_sqlConnectionSB["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
			_sqlConnectionSB["User ID"] = "MyLogin";  // "@yourservername"  as suffix sometimes.
			_sqlConnectionSB["Password"] = "MyPassword";
			_sqlConnectionSB["Database"] = "MyDatabase";

			// Adjust these values if you like. (.NET 4.5.1 or later.)
			_sqlConnectionSB["ConnectRetryCount"] = 3;
			_sqlConnectionSB["ConnectRetryInterval"] = 10;  // Seconds.

			// Leave these values as they are.
			_sqlConnectionSB["Trusted_Connection"] = false;
			_sqlConnectionSB["Integrated Security"] = false;
			_sqlConnectionSB["Encrypt"] = true;
			_sqlConnectionSB["Connection Timeout"] = 30;
		}

		Program()   // Constructor.
		{
			int[] arrayOfTransientErrorNumbers =
				{ 4060, 40197, 40501, 40613, 49918, 49919, 49920
					//,11001   // 11001 for testing, pretend network error is transient.
				};
			m_listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
		}

		private G.List<int> m_listTransientErrorNumbers;
	}
}
```


### Ejecución del programa


El archivo ejecutable **RetryAdo2.exe** no emite ningún parámetro. Para ejecutar el archivo .exe en Visual Studio:


1. Establezca un punto de interrupción en la instrucción **return;** del método **Main**.

2. Haga clic en el botón de flecha de inicio de color verde. Se muestra una ventana de consola.

3. Cuando el depurador se detiene al final de **Main**, cambie a la ventana de la consola.

4. Quizás pueda ver tres filas idénticas a las siguientes:


```
database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535
```


## Probar su lógica de reintento


A continuación se muestra una forma práctica de probar la lógica de prueba:


1. Agregue temporalmente **11001** a la colección de valores **SqlConnection.Number** que se considerarán como errores transitorios.

2. Vuelva a compilar el programa.

3. Desconecte el equipo cliente de la red.

4. Ejecute el programa en un depurador, con un punto de interrupción en el bucle.
 - El primer bucle producirá el error 11001.

5. Cuando el programa se encuentre en un punto de interrupción durante el segundo bucle, vuelva a conectar el equipo a la red.

6. Reanude la ejecución del programa. La ejecución se realizará correctamente durante el segundo bucle.


### Otra opción de prueba


Otra posibilidad que existe es la de agregar lógica a su programa para que reconozca un valor de parámetro de la línea de comandos de "test". En respuesta al parámetro, el programa debería hacer lo siguiente:


1. Anexar temporalmente letras no deseadas para que el nombre del servidor de la base de datos SQL esté mal escrito.

2. Agregar temporalmente **40615** a la lista de los errores transitorios.

3. Al principio del segundo bucle, lo que implica el primer bucle de reintentos, el programa debería:
 - Deshacer el error de escritura del nombre del servidor.
 - Quitar el valor 40615 de la lista de errores transitorios.


Ejecute el programa con el parámetro "test" y compruebe en primer lugar si se produce un error, pero se ejecuta correctamente en el segundo bucle.


## Vínculos relacionados


- [Ejemplos de código de inicio rápido de cliente para Base de datos SQL](sql-database-develop-quick-start-client-code-samples.md)

- [Prueba de Base de datos SQL: Use C# para crear una Base de datos SQL con la biblioteca de Base de datos SQL para .NET](sql-database-get-started-csharp.md)

<!---HONumber=AcomDC_0107_2016-->