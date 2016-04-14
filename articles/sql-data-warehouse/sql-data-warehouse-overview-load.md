   <properties
   pageTitle="Carga de datos en Almacenamiento de datos SQL | Microsoft Azure"
   description="Conozca los escenarios comunes para la carga de datos en Almacenamiento de datos SQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Carga de datos en Almacenamiento de datos SQL
El Almacenamiento de datos SQL ofrece numerosas opciones para la carga de datos entre las que se incluyen:

- PolyBase
- Factoría de datos de Azure
- Utilidad de línea de comandos BCP
- SQL Server Integration Services (SSIS)
- Herramientas de carga de datos de terceros

Aunque todos los métodos descritos anteriormente se puede usar con Almacenamiento de datos SQL, la capacidad de PolyBase para paralelizar de forma transparente las cargas desde Almacenamiento de blobs de Azure la convierte en la herramienta más rápida para cargar datos. Consulte más información sobre la [carga con PolyBase][]. Además, como muchos de nuestros usuarios están estudiando cargas iniciales en el intervalo de 100s de gigabytes a 10s de terabytes de los orígenes locales, en las siguientes secciones se proporciona alguna orientación sobre la carga de datos iniciales.

## Carga inicial en el Almacenamiento de datos SQL desde SQL Server 
Al cargar en el Almacenamiento de datos SQL desde una instancia de SQL Server local, se recomiendan los siguientes pasos:

1. **BCP**: datos de SQL Server en archivos planos 
2. Usar **AZCopy** o **Importación/Exportación** (para conjuntos de datos grandes) para mover los archivos a Azure.
3. Configurar PolyBase para leer sus archivos desde la cuenta de almacenamiento
4. Crear nuevas tablas y cargar los datos con **PolyBase**.

En las secciones siguientes echaremos un vistazo a cada paso en profundidad y daremos ejemplos del proceso.

> [AZURE.NOTE] Antes de mover datos desde un sistema, como SQL Server, sugerimos revisar los artículos [Migración de esquemas][] y [Migración de código][] de nuestra documentación.

## Exportación de archivos con BCP

Para preparar los archivos para el traslado a Azure, necesitará exportarlos a archivos planos. Esto se realiza mejor con la utilidad de la línea de comandos de BCP. Si no dispone aún de la utilidad, se puede descargar con las [Utilidades de línea de comandos de Microsoft para SQL Server][]. Un comando BCP de ejemplo podría ser similar al siguiente:

```
bcp "select top 10 * from <table>" queryout "<Directory><File>" -c -T -S <Server Name> -d <Database Name> -- Export Query
or
bcp <table> out "<Directory><File>" -c -T -S <Server Name> -d <Database Name> -- Export Table
```

Para maximizar el rendimiento, puede intentar poner en paralelo el proceso mediante la ejecución de varios comandos BCP simultáneos para tablas independientes o particiones independientes en una sola tabla. Esto le permitirá distribuir la CPU consumida por BCP entre varios núcleos del servidor donde se ejecuta BCP. Si va a extraer de un sistema PDW o DW de SQL, debe agregar el argumento - q, identificador entre comillas, al comando BCP. También deberá agregar -U y -P para especificar el nombre de usuario y la contraseña si en su entorno no se utiliza Active Directory.

Además, conforme realizaremos la carga mediante PolyBase, tenga en cuenta que PolyBase no admite todavía con UTF-16, y todos los archivos deben estar en UTF-8. Esto se puede lograr con facilidad mediante la inclusión de la marca '-c' en su comando BCP o también puede convertir los archivos */*planos de UTF-16 en UTF-8 mediante el siguiente código:

```
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```
 
Cuando haya exportado correctamente los datos a los archivos, será el momento de moverlos a Azure. Esto se puede lograr con AZCopy o con el servicio de importación y exportación, tal como se describe en la siguiente sección.

## Carga en Azure con AZCopy o importación y exportación
Si va a mover datos en el intervalo de 5 a 10 terabytes o superior, recomendamos que use nuestro servicio de [Importación/Exportación][] para efectuar el traslado. Sin embargo, en nuestro estudios, hemos logrados mover datos en el rango de TB de dígito único cómodamente mediante Internet pública con AZCopy. Este proceso también puede acelerarse o ampliarse con ExpressRoute.

En los siguientes pasos se detalla cómo mover datos locales desde local a una cuenta de Almacenamiento de Azure mediante AZCopy. Si no dispone de una cuenta de Almacenamiento de Azure en la misma región puede crear una siguiendo la [Documentación del Almacenamiento de Azure][]. También puede cargar datos desde una cuenta de almacenamiento en una región distinta, pero el rendimiento en este caso no será óptimo.

> [AZURE.NOTE] Esta documentación supone que ha instalado la utilidad de la línea de comandos de AZCopy y que puede ejecutarla con Powershell. Si este no es el caso, siga las [instrucciones de instalación de AZCopy][].

Ahora, dado un conjunto de archivos que ha creado con BCP, AzCopy puede ejecutarse simplemente Azure Powershell o ejecutando un script Powershell. En un nivel alto, el símbolo del sistema necesario para ejecutar AZCopy adoptará la forma:

```
AZCopy /Source:<File Location> /Dest:<Storage Container Location> /destkey:<Storage Key> /Pattern:<File Name> /NC:256
```

Además de la básica, recomendamos las siguientes prácticas recomendadas para la carga con AZCopy:


+ **Conexiones simultáneas**: además de aumentar el número de operaciones de AZCopy que se ejecutan al mismo tiempo, la propia operación de AZCopy se puede poner aún más en paralelo estableciendo el parámetro/NC, que abre varias conexiones simultáneas al destino. Mientras el parámetro puede ser en 512 como máximo, descubrimos que la transferencia de datos óptima tuvo lugar en 256 y recomendamos que se prueba un número de valores para encontrar lo que es óptimo para su configuración.

+ **ExpressRoute**: como se indicó anteriormente, este proceso se puede acelerar si la ruta rápida está habilitada. Encontrará información general de ExpressRoute y pasos para configurarla en la [Documentación de ExpressRoute][].

+ **Estructura de carpetas**: para facilitar la transferencia con PolyBase, asegúrese de que cada tabla se asigna a su propia carpeta. Esto minimizará y simplificará sus pasos al cargar con PolyBase más adelante. Dicho eso, no hay ningún impacto si una tabla se divide en varios archivos o incluso subdirectorios dentro de la carpeta.
	 

## Configuración de PolyBase 

Ahora que los datos se encuentran en blobs de almacenamiento de Azure, los importaremos en la instancia del Almacenamiento de datos SQL mediante PolyBase. Los pasos indicados a continuación son solo para configuración de y muchos de ellos solo tendrán que completarse una vez por cada cuenta de instancia del Almacenamiento de datos SQL, usuario o cuenta de almacenamiento. Estos pasos también se han descrito con mayor detalle en nuestra documentación [Carga con PolyBase][].

1. **Creación de clave maestra de base de datos.** Esta operación solo tendrá que completarse una vez por cada base de datos. 

2. **Creación de una credencial con ámbito de base de datos.** Esta operación solo debe crearse si desea crear una nueva credencial/usuario; de lo contrario, se puede usar una credencial creada anteriormente.

3. **Creación de un formato de archivo externo.** Además, los formatos de archivos externos también son reutilizables; solo necesitará crear uno si está cargando un nuevo tipo de archivo.

4. **Creación de un origen de datos externo.** Cuando se señala a una cuenta de almacenamiento, se puede usar un origen de datos externo al cargar desde el mismo contenedor. Para su parámetro 'LOCATION', use una ubicación con el formato: 'wasbs://mycontainer@ test.blob.core.windows.net/path’.

```
-- Creating master key
CREATE MASTER KEY;

-- Creating a database scoped credential
CREATE DATABASE SCOPED CREDENTIAL <Credential Name> 
WITH 
    IDENTITY = '<User Name>'
,   Secret = '<Azure Storage Key>'
;

-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH 
(
    FORMAT_TYPE = DELIMITEDTEXT 
,   FORMAT_OPTIONS  (
                        FIELD_TERMINATOR ='|' 
                    )
);

--Creating an external data source
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH 
(
    TYPE = HADOOP 
,   LOCATION ='wasbs://<Container>@<Blob Path>'
,   CREDENTIAL = <Credential Name>
)
;
```

Ahora que su cuenta de almacenamiento está correctamente configurada, puede continuar cargando sus datos en el Almacenamiento de datos SQL.

## Carga de datos con PolyBase 
Después de configurar PolyBase, puede cargar datos directamente en el Almacenamiento de datos SQL, creando simplemente una tabla externa que apunta a los datos del almacenamiento y asignando luego esos datos a una nueva tabla en el Almacenamiento de datos SQL. Esto se puede lograr con los dos comandos sencillos a continuación.

1. Use el comando 'CREATE EXTERNAL TABLE' para definir la estructura de sus datos. Para asegurarse de que captura el estado de los datos de forma rápida y eficaz, se recomienda generar script de la tabla de SQL Server en SSMS y ajustar luego manualmente para representar las diferencias de la tabla externa. Después de crear una tabla externa en Azure continuará señalando la misma ubicación, incluso si los datos se actualizan o se agregan datos adicionales.  

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE <External Table Name> 
(
    <Column name>, <Column type>, <NULL/NOT NULL>
)
WITH 
(   LOCATION='<Folder Path>'
,   DATA_SOURCE = <Data Source>
,   FILE_FORMAT = <File Format>      
);
```

2. Cargar datos con una instrucción 'CREATE TABLE...AS SELECT'. 

```
CREATE TABLE <Table Name> 
WITH 
(
	CLUSTERED COLUMNSTORE INDEX,
	DISTRIBUTION = <HASH(<Column Name>)>/<ROUND_ROBIN>
)
AS 
SELECT  * 
FROM    <External Table Name>
;
```

Tenga en cuenta que también puede cargar una subsección de las filas desde una tabla mediante una instrucción SELECT más detallada. Sin embargo, como PolyBase no inserta un proceso adicional a las cuentas de almacenamiento en este momento, si carga una subsección con una instrucción SELECT no será más rápido que cargar el conjunto de datos completo.

Además de la instrucción `CREATE TABLE...AS SELECT`, también puede cargar datos de tablas externas en tablas preexistentes con una instrucción "INSERT...INTO".

##  Crear estadísticas de los datos recién cargados 

Almacenamiento de datos SQL de Azure todavía no permite crear ni actualizar automáticamente las estadísticas. Con la finalidad de obtener el mejor rendimiento a partir de las consultas, es importante crear estadísticas en todas las columnas de todas las tablas después de la primera carga o después de que se realiza cualquier cambio importante en los datos. Si desea ver una explicación detallada de las estadísticas, consulte el tema [Estadísticas][] en el grupo de temas relacionados con el desarrollo. A continuación, puede ver un ejemplo rápido de cómo crear estadísticas sobre los datos cargados y organizados en tablas que aparecen en este ejemplo.


```
create statistics [<name>] on [<Table Name>] ([<Column Name>]);
create statistics [<another name>] on [<Table Name>] ([<Another Column Name>]);
```

## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[carga con PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md
[Migración de esquemas]: sql-data-warehouse-migrate-schema.md
[Migración de código]: sql-data-warehouse-migrate-code.md
[Estadísticas]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141237.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

<!--Other Web references-->
[instrucciones de instalación de AZCopy]: https://azure.microsoft.com/es-ES/documentation/articles/storage-use-azcopy/
[Utilidades de línea de comandos de Microsoft para SQL Server]: http://www.microsoft.com/es-ES/download/details.aspx?id=36433
[Importación/Exportación]: https://azure.microsoft.com/es-ES/documentation/articles/storage-import-export-service/
[Documentación del Almacenamiento de Azure]: https://azure.microsoft.com/es-ES/documentation/articles/storage-create-storage-account/
[Documentación de ExpressRoute]: http://azure.microsoft.com/documentation/services/expressroute/

<!---HONumber=AcomDC_0302_2016-->