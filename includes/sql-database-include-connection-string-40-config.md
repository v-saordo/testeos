
<!--
includes/sql-database-include-connection-string-40-config.md

Latest Freshness check:  2015-09-04 , GeneMi.

## Connection string
-->


### Archivo de configuración de ejemplo para la seguridad de la cadena de conexión


Es incorrecto colocar la cadena de conexión como literales en el código de C#. Es mejor colocar la cadena de conexión en un archivo de configuración. Allí puede editar la cadena en cualquier momento sin necesidad de volver a compilar.

Supongamos que el programa de C# compilado se denomina **ConsoleApplication1.exe** y que este .exe se encuentra en un directorio **bin\\debug**.

En este ejemplo, la mayoría de las partes de su cadena de conexión se almacenan en un archivo de configuración denominado exactamente **ConsoleApplication1.exe.config**. Este archivo de configuración también debe encontrarse en **bin\\debug**.

En el XML del siguiente archivo de configuración aparece una cadena de conexión denominada **ConnectionString4NoUserIDNoPassword**. El código de C# busca esta cadena.

Debe editar nombres reales para los marcadores de posición:

- {su\_nombreServidor\_aquí}
- {su\_nombreBaseDatos\_aquí}



		<?xml version="1.0" encoding="utf-8" ?>
		<configuration>
		    <startup> 
		        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		    </startup>
		
		    <connectionStrings>
		        <clear />
		        <add name="ConnectionString4NoUserIDNoPassword"
		        providerName="System.Data.ProviderName"
		
		        connectionString=
				"Server=tcp:{your_serverName_here}.database.windows.net,1433;
				Database={your_databaseName_here};
				Connection Timeout=30;
				Encrypt=True;
				TrustServerCertificate=False;" />
		    </connectionStrings>
		</configuration>



Para esta ilustración, elegimos omitir dos parámetros:

- Id. de usuario={su\_nombreUsuario\_aquí};
- Contraseña={su\_contraseña\_aquí};


Puede incluirlos, pero a veces es mejor que el programa obtenga esos valores de la entrada de teclado por parte del usuario. Depende.



<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=Oct15_HO3-->