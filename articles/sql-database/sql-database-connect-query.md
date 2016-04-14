<properties
	pageTitle="Conexión a Base de datos SQL con una consulta en C# | Microsoft Azure"
	description="Escribir un programa en C# para realizar consultas y conectarse a Base de datos SQL. Información acerca de direcciones IP, cadenas de conexión, inicio de sesión seguro y Visual Studio gratuito."
	services="sql-database"
	keywords="consulta de base de datos en c#, consulta en c#, conectarse a base de datos, SQL C#"
	documentationCenter=""
	authors="MightyPen"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="02/05/2016"
	ms.author="genemi"/>


# Escritura de un programa en C&#x23; para consultar y conectarse a una Base de datos SQL

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Aprenda a escribir un programa en C# para consultar y conectarse a una Base de datos SQL de Azure en la nube.

En este artículo se describen todos los pasos para las personas sin conocimientos de Base de datos SQL de Azure, C# y ADO.NET. Otras personas que tienen experiencia con C# y Microsoft SQL Server pueden omitir algunos pasos y centrarse en los que son específicos de Base de datos SQL.


## Requisitos previos


Para ejecutar el ejemplo de código de consulta en C#, debe tener:


- Una cuenta y una suscripción de Azure. Puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).


- Una base de datos de demostración **AdventureWorksLT** en el servicio Base de datos SQL de Azure.
 - [Creación de la base de datos de demostración](sql-database-get-started.md) en minutos.


- Visual Studio 2013, actualización 4 o posterior. Microsoft ahora ofrece Visual Studio Community *gratis*.
 - [Visual Studio Community, descarga](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [Más opciones para Visual Studio gratis](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)
 - O bien, deje que el [paso](#InstallVSForFree) posterior de este tema describa la forma en que el [Portal de Azure](https://portal.azure.com/) le guía en la instalación de Visual Studio.


<a name="InstallVSForFree" id="InstallVSForFree"></a>

&nbsp;

## Paso 1: Instalación de Visual Studio Community gratis


Si necesita instalar Visual Studio, puede:

- Para instalar Visual Studio Community gratis, en el explorador, vaya a las páginas web del producto Visual Studio en las que puede descargarlo gratis y otras opciones; o bien
- Deje que el [Portal de Azure](https://portal.azure.com/) le guíe a la página web de descarga, que se describe a continuación.


### Visual Studio a través del Portal de Azure


1. Inicie sesión mediante el [Portal de Azure](https://portal.azure.com/), http://portal.azure.com/.

2. Haga clic en **EXAMINAR* TODO** > **Bases de datos SQL**. Se abre una hoja que busca bases de datos.

3. En el cuadro de texto de filtro que se encuentra cerca de la parte superior, empiece a escribir el nombre de la base de datos **AdventureWorksLT**.

4. Cuando vea la fila de la base de datos en el servidor, haga clic en la fila. Se abre una hoja para la base de datos.

5. Para mayor comodidad, haga clic en el control de minimizar de cada una de las hojas anteriores.

6. Haga clic en el botón **Abrir en Visual Studio**, cerca de la parte superior de la hoja de la base de datos. Se abre una nueva hoja acerca de Visual Studio con vínculos para instalar ubicaciones para Visual Studio.

	![Botón Abrir en Visual Studio][20-OpenInVisualStudioButton]

7. Haga clic en el vínculo **Community (gratis)** o un vínculo similar. Se agrega una página web nueva.

8. Use los vínculos de la página web nueva para instalar Visual Studio.

9. Después de instalar Visual Studio, en la hoja **Abrir en Visual Studio**, haga clic en el botón **Abrir en Visual Studio**. Se abre Visual Studio.

10. Para aprovechar su panel **Explorador de objetos de SQL Server**, Visual Studio le pedirá que rellene los campos de las cadenas de conexión en un cuadro de diálogo.
 - Elija **Autenticación de SQL Server**, no **Autenticación de Windows**.
 - No olvide especificar su base de datos **AdventureWorksLT** (**Opciones** > **Propiedades de conexión** en el cuadro de diálogo).

11. En el **Explorador de objetos de SQL Server**, expanda el nodo de la base de datos.


## Paso 2: Creación de un proyecto en Visual Studio


En Visual Studio, cree un proyecto nuevo basado en la plantilla de inicio para C# > Windows > **Aplicación de consola**.


1. Haga clic en **Archivo** > **Nuevo** > **Proyecto**. Aparece el cuadro de diálogo ***.

2. En **Instalado**, expanda a C# y Windows, para que la opción **Aplicación de consola** aparezca en el panel central.

	![Cuadro de diálogo Nuevo proyecto][30-VSNewProject]

2. En **Nombre**, escriba **ConnectAndQuery\_Example**. Haga clic en **Aceptar**.


## Paso 3: Incorporación de una referencia de ensamblado para el procesamiento de configuración


Nuestro ejemplo de C# usa el ensamblado de .NET Framework **System.Configuration.dll**, por lo que vamos a agregar una referencia a él.


1. En el panel **Explorador de soluciones**, haga clic con el botón derecho en **Referencias** > **Agregar referencia**. Se muestra la ventana **Administrador de referencias**.

2. Expanda **Ensamblados** > **Framework**.

3. Desplácese por ellos y haga clic para resaltar **System.Configuration**. Asegúrese de que la casilla está seleccionada.

4. Haga clic en **Aceptar**.

5. Compile el programa mediante el menú **COMPILAR** > **Compilar solución**.


## Paso 4: Obtención de la cadena de conexión


Use el [Portal de Azure](https://portal.azure.com/) para copiar la cadena de conexión necesaria para conectarse a la Base de datos SQL.

Su primer uso será conectar Visual Studio a la base de datos **AdventureWorksLT** de Base de datos SQL de Azure.


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]


## Paso 5: Incorporación de la cadena de conexión al archivo App.config


1. En Visual Studio, abra el archivo App.config en el panel Explorador de soluciones.

2. Agregue un elemento **&#x3c;configuration&#x3e; &#x3c;/configuration&#x3e;**, como se muestra en el siguiente código de ejemplo App.config.
 - Reemplace *{your\_placeholders}* por los valores reales:

```
	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>

		<connectionStrings>
			<clear />
			<add name="ConnectionString4NoUserIDNoPassword"
			connectionString="Server=tcp:{your_serverName_here}.database.windows.net,1433; Database={your_databaseName_here}; Connection Timeout=30; Encrypt=True; TrustServerCertificate=False;"
			/>
		</connectionStrings>
	</configuration>
```

3. Guarde el archivo App.config.

4. En el panel Explorador de soluciones, haga clic en el nodo **App.config** y, a continuación, haga clic en **Propiedades**.

5. Establezca **Copiar en el directorio de resultados** en **Copiar siempre**.
 - Esto hace que el contenido del archivo App.config reemplace el contenido del archivo &#x2a;.exe.config, en el directorio donde se genera el archivo &#x2a;.exe. La sustitución se produce cada vez que vuelve a compilar &#x2a;.exe.
 - El archivo &#x2a;.exe.config se lee cuando se ejecuta el programa de C# de ejemplo.

	![Copiar en el directorio de resultados = Copiar siempre][50-VSCopyToOutputDirectoryProperty]


## Paso 6: Pegado en el código C# de ejemplo


1. En Visual Studio, use el panel **Explorador de soluciones** para abrir su archivo **Program.cs**.

	![Pegar en el código de la consulta en C# de ejemplo][40-VSProgramCsOverlay]

2. Para sobrescribir todo el código de inicio en Program.cs, pegue el siguiente código de C# de ejemplo.
 - Si desea un código de ejemplo más corto, puede asignar la cadena de conexión completa como un literal para la variable **SQLConnectionString**. A continuación, puede borrar los dos métodos **GetConnectionStringFromExeConfig** y **GatherPasswordFromConsole**.


```
using System;  // C#
using G = System.Configuration;   // System.Configuration.dll
using D = System.Data;            // System.Data.dll
using C = System.Data.SqlClient;  // System.Data.dll
using T = System.Text;

namespace ConnectAndQuery_Example
{
	class Program
	{
		static void Main()
		{
			string connectionString4NoUserIDNoPassword,
				password, userName, SQLConnectionString;

			// Get most of the connection string from ConnectAndQuery_Example.exe.config
			// file, in the same directory where ConnectAndQuery_Example.exe resides.
			connectionString4NoUserIDNoPassword = Program.GetConnectionStringFromExeConfig
				("ConnectionString4NoUserIDNoPassword");
			// Get the user name from keyboard input.
			Console.WriteLine("Enter your User ID, without the trailing @ and server name: ");
			userName = Console.ReadLine();
			// Get the password from keyboard input.
			password = Program.GatherPasswordFromConsole();

			SQLConnectionString = "Password=" + password + ';' +
				"User ID=" + userName + ";" + connectionString4NoUserIDNoPassword;

			// Create an SqlConnection from the provided connection string.
			using (C.SqlConnection connection = new C.SqlConnection(SQLConnectionString))
			{
				// Formulate the command.
				C.SqlCommand command = new C.SqlCommand();
				command.Connection = connection;

				// Specify the query to be executed.
				command.CommandType = D.CommandType.Text;
				command.CommandText = @"
					SELECT TOP 9 CustomerID, NameStyle, Title, FirstName, LastName
					FROM SalesLT.Customer;  -- In AdventureWorksLT database.
					";
				// Open a connection to database.
				connection.Open();

				// Read data returned for the query.
				C.SqlDataReader reader = command.ExecuteReader();
				while (reader.Read())
				{
					Console.WriteLine("Values:  {0}, {1}, {2}, {3}, {4}",
						reader[0], reader[1], reader[2], reader[3], reader[4]);
				}
			}
			Console.WriteLine("View the results here, then press any key to finish...");
			Console.ReadKey(true);
		}
		//----------------------------------------------------------------------------------

		static string GetConnectionStringFromExeConfig(string connectionStringNameInConfig)
		{
			G.ConnectionStringSettings connectionStringSettings =
				G.ConfigurationManager.ConnectionStrings[connectionStringNameInConfig];

			if (connectionStringSettings == null)
			{
				throw new ApplicationException(String.Format
					("Error. Connection string not found for name '{0}'.",
					connectionStringNameInConfig));
			}
				return connectionStringSettings.ConnectionString;
		}

		static string GatherPasswordFromConsole()
		{
			T.StringBuilder passwordBuilder = new T.StringBuilder(32);
			ConsoleKeyInfo key;
			Console.WriteLine("Enter your password: ");
			do
			{
				key = Console.ReadKey(true);
				if (key.Key != ConsoleKey.Backspace)
				{
					passwordBuilder.Append(key.KeyChar);
					Console.Write("*");
				}
				else  // Backspace char was entered.
				{
					// Retreat the cursor, overlay '*' with ' ', retreat again.
					Console.Write("\b \b");
					passwordBuilder.Length = passwordBuilder.Length - 1;
				}
			}
			while (key.Key != ConsoleKey.Enter); // Enter key will end the looping.
			Console.WriteLine(Environment.NewLine);
			return passwordBuilder.ToString();
		}
	}
}
```


### Compilación del programa


1. En Visual Studio, compile el programa haciendo clic en el menú **Compilar** > **Compilar solución**.


### Resumen de acciones en el programa de ejemplo


1. Lee la mayor parte de la cadena de conexión de SQL desde un archivo de configuración.

2. Recopila el nombre de usuario y la contraseña desde el teclado y los agrega para completar la cadena de conexión.

3. Usa la cadena de conexión y las clases de ADO.NET para conectarse a la base de datos de demostración **AdventureWorksLT** en Base de datos SQL.

4. Emite una instrucción **SELECT** de SQL que se lee desde la tabla **SalesLT**.

5. Imprime las filas devueltas en la consola.


Tratamos de mantener el código de C# de ejemplo corto. Sin embargo, hemos agregado código para leer un archivo de configuración para respetar varias solicitudes de clientes como usted. Estamos de acuerdo que los programas de calidad de producción deberían usar archivos de configuración en lugar de literales codificados en el archivo .exe.


> [AZURE.WARNING] Para mantener la brevedad del código, hemos optado por no incluir código de control de excepciones y lógica de reintento en este ejemplo educativo. Sin embargo, los programas de producción que interactúan con una base de datos en la nube deben incluir ambos.
>
> [Aquí](sql-database-develop-csharp-retry-windows.md) hay un vínculo a un código de ejemplo con la lógica de reintento.


## Paso 7: Incorporación del intervalo de direcciones IP permitidas en el firewall


Su programa cliente en C# no se puede conectar a Base de datos SQL hasta que la dirección IP del equipo cliente se agregue al firewall de Base de datos SQL. El programa fallará con un mensaje de error útil que indica la dirección IP necesaria.


Para agregar la dirección IP se puede usar el [Portal de Azure](https://portal.azure.com/).



[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]



Para obtener más información, consulte<br/> [Configuración del firewall en Base de datos SQL](sql-database-configure-firewall-settings.md).



## Paso 8: Ejecución del programa


1. En Visual Studio, ejecute el programa de consulta en C# mediante el menú **DEPURAR** > **Iniciar depuración**. Se muestra una ventana de consola.

2. Escriba su nombre de usuario y contraseña, tal como se indica.
 - Algunas herramientas de conexión requieren que su nombre de usuario tenga "@{su\_nombreservidor\_aquí}" anexado, pero para ADO.NET este sufijo es opcional. No se moleste en escribir el sufijo.

3. Se muestran las filas de datos.


## Vínculos relacionados


- [Ejemplos de código de inicio rápido de cliente para Base de datos SQL](sql-database-develop-quick-start-client-code-samples.md)

- Si su programa cliente se ejecuta en una máquina virtual de Azure, obtenga información acerca de los puertos TCP, excepto el puerto 1433, en:<br/>[Puertos más allá de 1433 para ADO.NET 4.5 y Base de datos SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md).



<!-- Image references. -->

[20-OpenInVisualStudioButton]: ./media/sql-database-connect-query/connqry-free-vs-e.png

[30-VSNewProject]: ./media/sql-database-connect-query/connqry-vs-new-project-f.png

[40-VSProgramCsOverlay]: ./media/sql-database-connect-query/connqry-vs-program-cs-overlay-g.png

[50-VSCopyToOutputDirectoryProperty]: ./media/sql-database-connect-query/connqry-vs-appconfig-copytoputputdir-h.png

<!---HONumber=AcomDC_0211_2016-->