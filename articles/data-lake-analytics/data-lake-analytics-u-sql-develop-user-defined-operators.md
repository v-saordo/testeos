<properties 
   pageTitle="Desarrollo de operadores U-SQL definidos por el usuario para trabajos de Análisis de Azure Data Lake | Azure" 
   description="Aprenda a desarrollar operadores definidos por el usuario para usarse y volverse a usar en trabajos de Análisis de Data Lake." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/10/2016"
   ms.author="jgao"/>


# Desarrollo de operadores U-SQL definidos por el usuario para trabajos de Análisis de Azure Data Lake

Aprenda a desarrollar operadores definidos por el usuario para usarse y volverse a usar en trabajos de Análisis de Data Lake. Desarrollará un operador personalizado para convertir los nombres de los países.

##Requisitos previos

- Visual Studio 2015, Visual Studio 2013 Update 4 o Visual Studio 2012 con Visual C++ instalado 
- SDK de Microsoft Azure para .NET versión 2.5 o posterior. Instálelo usando el instalador de plataforma web.
- Una cuenta de Análisis de Data Lake. Consulte [Introducción a Análisis de Azure Data Lake mediante el portal de Azure](data-lake-analytics-get-started-portal.md).
- Realice el tutorial [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md)
- Conéctese a Azure y consulte [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md#connect-to-azure). 
- Descargue los datos de origen y consulte [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md#upload-source-data-files). 

## Definición y uso del operador definido por el usuario en U-SQL

**Para crear y enviar un trabajo de U-SQL**

1. En el menú **Archivo**, haga clic en **Nuevo** y, después, en **Proyecto**.
2. Seleccione el tipo de **proyecto U-SQL**.

	![nuevo proyecto de Visual Studio U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Haga clic en **Aceptar**. Visual Studio crea una solución con un archivo Script.usql.
4. En el **Explorador de soluciones**, expanda Script.usql y haga doble clic en **Script.usql.cs**.
5. Pegue el código siguiente en el archivo:

		using Microsoft.Analytics.Interfaces;
		using System.Collections.Generic;
		
		namespace USQL_UDO
		{
			public class CountryName : IProcessor
			{
				private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
				{
					{
						"Deutschland", "Germany"
					},
					{
						"Schwiiz", "Switzerland"
					},
					{
						"UK", "United Kingdom"
					},
					{
						"USA", "United States of America"
					},
					{
						"中国", "PR China"
					}
				};
		
				public override IRow Process(IRow input, IUpdatableRow output)
				{
		
					string UserID = input.Get<string>("UserID");
					string Name = input.Get<string>("Name");
					string Address = input.Get<string>("Address");
					string City = input.Get<string>("City");
					string State = input.Get<string>("State");
					string PostalCode = input.Get<string>("PostalCode");
					string Country = input.Get<string>("Country");
					string Phone = input.Get<string>("Phone");
		
					if (CountryTranslation.Keys.Contains(Country))
					{
						Country = CountryTranslation[Country];
					}
					output.Set<string>(0, UserID);
					output.Set<string>(1, Name);
					output.Set<string>(2, Address);
					output.Set<string>(3, City);
					output.Set<string>(4, State);
					output.Set<string>(5, PostalCode);
					output.Set<string>(6, Country);
					output.Set<string>(7, Phone);
		
					return output.AsReadOnly();
				}
			}
		}

5. Abra Script.usql y pegue el siguiente script de U-SQL:

		@drivers =
			EXTRACT UserID      string,
					Name        string,
					Address     string,
					City        string,
					State       string,
					PostalCode  string,
					Country     string,
					Phone       string
			FROM "/Samples/Data/AmbulanceData/Drivers.txt"
			USING Extractors.Tsv(Encoding.Unicode);
		
		@drivers_CountryName =
			PROCESS @drivers
			PRODUCE UserID string,
					Name string,
					Address string,
					City string,
					State string,
					PostalCode string,
					Country string,
					Phone string
			USING new USQL_UDO.CountryName();    
		
		OUTPUT @drivers_CountryName
			TO "/Samples/Outputs/Drivers.csv"
			USING Outputters.Csv(Encoding.Unicode);

6. En el **Explorador de soluciones**, haga clic con el botón derecho en **Script.usql** y después haga clic en **Compilar script**.
6. En el **Explorador de soluciones**, haga clic con el botón derecho en **Script.usql** y después haga clic en **Enviar script**.
7. Si no se ha conectado a su suscripción de Azure, se le avisará que especifique sus credenciales de la cuenta de Azure.
7. Haga clic en **Enviar**. Los resultados del envío y el vínculo del trabajo están disponibles en la ventana de resultados cuando se completa el envío.
8. Puede hacer clic en el botón Actualizar para ver el estado de trabajo más reciente y actualizar la pantalla.

**Para ver la salida del trabajo**

1. En el **Explorador de servidores**, expanda **Azure**, **Análisis de Data Lake**, su cuenta de Análisis de Data Lake y **Cuentas de almacenamiento**, haga clic con el botón derecho en el almacén predeterminado y, finalmente, haga clic en **Explorador**. 
2. Expanda Ejemplos, expanda Salidas y, finalmente, haga doble clic en **Drivers.csv**.


##Consulte también

- [Introducción a Análisis de Data Lake mediante PowerShell](data-lake-analytics-get-started-powershell.md)
- [Introducción a Análisis de Data Lake mediante el Portal de Azure](data-lake-analytics-get-started-portal.md)
- [Uso de Data Lake Tools for Visual Studio para desarrollar aplicaciones de U-SQL](data-lake-analytics-data-lake-tools-get-started.md)

<!---HONumber=AcomDC_0218_2016-->