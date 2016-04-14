<properties
   pageTitle="Migración: Utilidad de migración de Almacenamiento de datos | Microsoft Azure"
   description="Migración a Almacenamiento de datos SQL"
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


#Utilidad de migración de Almacenamiento de datos (vista previa)
La Utilidad de migración de Almacenamiento de datos es una herramienta diseñada para migrar el esquema y los datos de SQL Server y Base de datos de SQL Azure a Almacenamiento de datos SQL de Azure. Durante la migración del esquema, la herramienta asigna automáticamente el esquema correspondiente del origen al destino. Una vez migrado el esquema, a los usuarios también se les ofrece la opción de mover datos a través de scripts generados automáticamente.

Además de migración de datos y el esquema, esta herramienta ofrece a los usuarios la opción de generar informes de compatibilidad que resumen las incompatibilidades entre las instancias de origen y de destino que podrían impedir la migración optimizada.

##Introducción
Puede descargar la Utilidad de migración de Almacenamiento de datos [aquí][]. Como requisito previo para la instalación, necesitará la utilidad de línea de comandos BCP para ejecutar scripts de migración y Office para ver el informe de compatibilidad. Después de iniciar el archivo ejecutable descargado, se le pedirá que acepte unos términos de licencia estándar antes de que se instale la herramienta.

###Inicio de la herramienta y conexión
La herramienta se puede iniciar fácilmente haciendo clic en el icono del escritorio que aparece después de la instalación. Al abrir la herramienta, se le mostrará una página de conexión inicial donde podrá elegir el origen y destino de la herramienta de migración. En este momento se admiten SQL Server y Base de datos SQL de Azure como orígenes y Almacenamiento de datos SQL como destino. Después de seleccionarlo, se le pedirá que se conecte al servidor de origen rellenando el nombre del servidor y la autenticación y, a continuación, que haga clic en «Conectar».
 
Después de autenticarse, la herramienta mostrará una lista de bases de datos que se encuentran en el servidor que se conectó. Puede comenzar la migración seleccionando la base de datos que desea migrar y después hacer clic en «Migrar seleccionado».
 
##Informe de migración
Si se selecciona «Comprobar compatibilidad de la base de datos» en la herramienta, se generará un informe de resumen de todas las incompatibilidades de los objetos de la base de datos que quiere migrar. Puede encontrar una lista más exhaustiva de algunas de las funciones de SQL Server que no están presentes en el Almacenamiento de datos SQL en nuestra [documentación de migración][]. Una vez generado el informe, podrá guardarlo y abrirlo en Excel.

Tenga en cuenta que cuando se genera el esquema de migración, la mayoría de los problemas identificados como «Objeto» se ajustarán para permitir la migración inmediata de los datos. Revise los cambios para asegurarse de que no desea realizar ajustes adicionales antes de aplicar el esquema.

##Migración del esquema

Después de conectarse, si se selecciona «Migrar esquema», se generará un script de migración de esquema para las tablas seleccionadas. Este script lleva la estructura de la tabla, asigna tipos de datos no compatibles a otros formularios más compatibles y crea las credenciales de seguridad y el esquema si está indicado por el usuario en la configuración de migración. Este código puede ejecutarse en la instancia de Almacenamiento de datos SQL de destino, se puede guardar en un archivo, copiar en el Portapapeles o incluso modificarse en línea antes de realizar otra acción.
 
Como se indicó anteriormente, al realizar la migración, el esquema revisa los cambios en la migración que realizó la herramienta para asegurarse de que se comprendieron completamente.

##Migración de los datos

Si hace clic en la opción «Migrar datos», puede generar scripts BCP que mueven los datos primero a archivos sin formato en el servidor y después directamente a Almacenamiento de datos SQL. Se recomienda este proceso para trasladar pequeñas cantidades de datos y, como no hay reintentos integrados, tenga en cuenta que pueden producirse errores de interrupciones de red. Para ejecutarlo, debe tener instalada la utilidad de línea de comandos BCP y ya se debe haber creado el esquema de los datos.
 
Después de rellenar los parámetros anteriores, basta con que haga clic en la opción de ejecutar migración y se generará un conjunto de dos paquetes en la ubicación especificada. Ejecute el archivo de exportación para exportar datos desde el origen de migración a archivos sin formato y ejecute el archivo de importación para importar los datos en Almacenamiento de datos SQL.

## Pasos siguientes
Ahora que migró algunos datos, aprenda a [desarrollarlos][].

<!--Image references-->

<!--Article references-->
[documentación de migración]: https://azure.microsoft.com/es-ES/documentation/articles/sql-data-warehouse-overview-migrate/
[desarrollarlos]: https://azure.microsoft.com/es-ES/documentation/articles/sql-data-warehouse-overview-develop/
[aquí]: https://migrhoststorage.blob.core.windows.net/sqldwsample/DataWarehouseMigrationUtility.zip

<!---HONumber=AcomDC_0114_2016-->