<properties 
	pageTitle="Ejemplos de código de inicio rápido de cliente para Base de datos SQL | Microsoft Azure" 
	description="Proporciona ejemplos de código y controladores para Node.js en Linux, Python en Mac OS, Java y Windows, Enterprise Library y muchos más, todos para clientes de Base de datos SQL de Azure."
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/10/2016" 
	ms.author="genemi"/>


# Ejemplos de código de inicio rápido de cliente para Base de datos SQL


Este tema proporciona vínculos a ejemplos de código de inicio rápido que puede usar para conectarse a la base de datos SQL de Azure.


- Conexión y consulta de ejemplos cortos.
- Reintento de la conexión y consulta de ejemplos, pero reintento automático si un error encontrado se clasifica como [*error transitorio*](sql-database-develop-error-messages.md#bkmk_connection_errors) (como un tiempo de espera de conexión).


Los ejemplos abarcan:


- Una amplia variedad de lenguajes de programación.
- Los sistemas operativos Windows, Linux y Mac OS como sistemas en los que puede ejecutarse el programa cliente.
- Vínculos para descargas de controladores de conexión necesarios.
- Ejemplos breves de código de inicio rápido.
- Ejemplos más largos que contienen el control de errores transitorios en forma de lógica de reintento automática.
- Ejemplos de código que permiten convertir conjuntos de resultados relacionales en un formato orientado a objetos.


> [AZURE.NOTE] Se están preparando ejemplos de código en más lenguajes cuyos vínculos se agregarán al presente tema.


## Clientes de Linux


Esta sección proporciona vínculos a temas de ejemplo de código para programas cliente que se ejecutan en Linux.


| Lenguaje | Breve ejemplo | Ejemplo de reintento | Relacional al objeto |
| :-- | :-- | :-- | :-- |
| Node.js | [Tedious](sql-database-develop-nodejs-simple-linux.md) | . | . |
| Python | [FreeTDS, pymssql](sql-database-develop-python-simple-ubuntu-linux.md) | . | . |
| Ruby | [FreeTDS, TinyTDS](sql-database-develop-ruby-simple-linux.md) | . | . |


## Clientes de Mac OS


Esta sección proporciona vínculos a temas de ejemplo de código para programas clientes que se ejecutan en Mac OS.


| Lenguaje | Breve ejemplo | Ejemplo de reintento | Relacional al objeto |
| :-- | :-- | :-- | :-- |
| Python | [pymssql](sql-database-develop-python-simple-mac-osx.md) | . | . |
| Ruby | [Homebrew<br/>FreeTDS, TinyTDS](sql-database-develop-ruby-simple-mac-osx.md) | . | . |


## Clientes de Windows


Esta sección proporciona vínculos a temas de ejemplo de código para programas cliente que se ejecutan en Windows.


| Lenguaje | Breve ejemplo | Ejemplo de reintento | Relacional al objeto |
| :-- | :-- | :-- | :-- |
| C# | [ADO.NET](sql-database-develop-dotnet-simple.md) | [ADO.NET personalizado](sql-database-develop-csharp-retry-windows.md)<br/><br/>[Enterprise Library](sql-database-develop-entlib-csharp-retry-windows.md) | (Entity Framework) |
| C++ | [Controlador ODBC](http://msdn.microsoft.com/library/azure/hh974312.aspx) | . | . |
| Java | [Java. JDBC, JDK. Insert, Transaction, Select.](sql-database-develop-java-simple-windows.md) | . | . |
| Node.js | [msnodesql](sql-database-develop-nodejs-simple-windows.md) | . | . |
| PHP | [ODBC](sql-database-develop-php-simple-windows.md) | [ODBC personalizado](sql-database-develop-php-retry-windows.md) | . |
| Python | [pymssql](sql-database-develop-python-simple-windows.md) | . | . |


## Consulte también


- [Descargas de SDK y herramientas para varios lenguajes y plataformas](https://azure.microsoft.com/downloads/#cmd-line-tools)
- [Bibliotecas de conexiones para la base de datos SQL y SQL Server](sql-database-libraries.md)
- [Lista de códigos numéricos para errores transitorios](sql-database-develop-error-messages.md#bkmk_connection_errors)<br/>&nbsp;
- [Desarrollo de base de datos SQL de Azure: temas de procedimientos](http://msdn.microsoft.com/library/azure/ee621787.aspx)
- [Conexión a la base de datos SQL: vínculos, prácticas recomendadas y directrices de diseño](sql-database-connect-central-recommendations.md)
- [Creación de la primera base de datos SQL de Azure](sql-database-get-started.md)
- [Entity Framework 6 aquí, EF 7 en GitHub](http://entityframework.codeplex.com/)

<!---HONumber=AcomDC_0128_2016-->