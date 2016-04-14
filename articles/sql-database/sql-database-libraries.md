<properties
	pageTitle="Bibliotecas de conexiones para Base de datos SQL y SQL Server"
	description="Muestra el número de versión mínimo para cada controlador que pueden usar los programas cliente para conectarse a Base de datos SQL de Azure o a Microsoft SQL Server. Se proporciona un vínculo con información acerca de los controladores publicados por la comunidad y no por Microsoft."
	services="sql-database"
	documentationCenter=""
	authors="pehteh"
	manager="jeffreyg"
	editor="genemi"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="pehteh"/>

# Bibliotecas de conexiones para Base de datos SQL y SQL Server

En este tema se muestra el número de versión mínimo para cada biblioteca o controlador que los programas cliente pueden usar para conectarse a Base de datos SQL de Azure o a Microsoft SQL Server.

## Tabla de bibliotecas de controladores publicadas por Microsoft

En la tabla siguiente se muestran las bibliotecas publicadas por Microsoft. La columna **Bibliotecas** proporciona vínculos que puede usar para descargar cada biblioteca. La columna **Versión** muestra la versión mínima recomendada para interactuar con Base de datos de SQL Azure y Microsoft SQL Server.

| Plataforma | Oper Sys | Bibliotecas <br/>para descarga | Versión <br/>de controlador | Descripción <br/>de controlador | Más <br/>información |
| :--- | :--- | :--- | :--- | :--- | :-- |
| .NET | Multiplataforma (. NET) | [ADO.NET, System .Data .SqlClient](http://www.microsoft.com/download/details.aspx?id=30653) | 4\.5 + | Proveedor de SQL Server para .NET Framework | . |
| PHP | Windows | [PHP para SQL Server](http://www.microsoft.com/download/details.aspx?id=20098) | 2\.0+ | Controlador PHP para SQL Server | [Vínculo](http://msdn.microsoft.com/library/dn865013.aspx) |
| Java | Windows | [JDBC para SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) | 2\.0+ | Controlador JDBC de tipo 4 que proporciona conectividad de base de datos a través de la API de JDBC estándar | [Vínculo](https://msdn.microsoft.com/library/mt654048.aspx) |
| ODBC | Windows | [ODBC para SQL Server](http://www.microsoft.com/download/details.aspx?id=36434) | 11\.0+ | Controlador ODBC de Microsoft para SQL Server | [Vínculo](http://msdn.microsoft.com/library/jj730308.aspx) |
| ODBC | Suse Linux | [ODBC para SQL Server](http://www.microsoft.com/download/details.aspx?id=34687) | 11\.0+ | Controlador ODBC de Microsoft para SQL Server | [Vínculo](https://msdn.microsoft.com/es-ES/library/hh568451.aspx) |
| ODBC | Redhat Linux | [ODBC para SQL Server](http://www.microsoft.com/download/details.aspx?id=34687) | 11\.0+ | Controlador ODBC de Microsoft para SQL Server | [Vínculo](https://msdn.microsoft.com/es-ES/library/hh568451.aspx) |

### Compatibilidad con ODBC

Al utilizar el asistente para el nombre de origen de datos (DSN) para definir un origen de datos para la Base de datos SQL de Azure, haga clic en la opción **Con autenticación de SQL Server mediante un identificador de inicio de sesión y una contraseña escritos por el usuario** y seleccione **Conectar con SQL Server para obtener la configuración predeterminada de las opciones de configuración adicionales**. Escriba su nombre de usuario y contraseña para conectarse a su servidor de Base de datos SQL de Azure como **Id. de inicio de sesión** y **Contraseña**. Desactive la casilla **Conectar con SQL Server para obtener la configuración predeterminada de las opciones de configuración adicionales**. Haga clic en **Establecer la siguiente base de datos como predeterminada:** y escriba el nombre de la Base de datos SQL de Azure, incluso si no se muestra en la lista. Tenga en cuenta que el asistente muestra varios idiomas en la lista **Establecer el siguiente idioma para los mensajes del sistema de SQL Server:**.

En esta versión, Base de datos SQL de Microsoft Azure solo se admite en inglés, así que seleccione inglés como idioma. Base de datos SQL de Microsoft Azure no admite **servidor reflejado** o **Adjuntar base de datos**, así que deje estos elementos vacíos. Haga clic en **Probar conexión**.

Cuando utilice el controlador ODBC de SQL Server 2008 Native Client, el botón **Probar conexión** puede producir un error que señala que **master.dbo.syscharsets con** no se admite. Ignore este error, guarde el DSN y utilícelo.

### OLEDB para DB2 y SQL Server, para diseño DRDA

Proveedor OLE DB de Microsoft para DB2 Versión 5.0 (proveedor de datos) le permite crear aplicaciones distribuidas dirigidas a bases de datos de IBM DB2. El proveedor de datos utiliza la arquitectura de acceso a los datos de Microsoft SQL Server en conjunto con un cliente de redes de Microsoft para DB2 que funciona como solicitante de la aplicación Arquitectura distribuida de bases de datos relacionales (DRDA). El proveedor de datos convierte los comandos OLE DB del Modelo de objetos componentes (COM) de Microsoft y los tipos de datos en formatos de datos y puntos de código de protocolo DRDA.

Para más información, consulte:

- [Proveedor OLE DB de Microsoft para DB2 Versión 5.0](http://msdn.microsoft.com/library/dn745875.aspx)
- [Proveedor OLE DB de Microsoft para DB2 v4.0 para Microsoft SQL Server 2012](http://www.microsoft.com/download/details.aspx?id=29100)

## Bibliotecas de terceros

> [AZURE.IMPORTANT] En la tabla siguiente se muestran las bibliotecas publicadas por terceros bajo los términos de licencia de terceros. El usuario es responsable de comprobar y cumplir con las licencias de terceros relevantes para poder usar estas bibliotecas. Asume el riesgo del uso de estas bibliotecas. Microsoft no otorga ninguna garantía, expresa o implícita, con respecto a la información proporcionada aquí y simplemente ha proporcionado la información por razones de conveniencia para los usuarios. Nada en este documento implica cualquier tipo de aprobación por Microsoft. <br/><br/>Depende de la comunidad pública de desarrolladores la actualización y el mantenimiento de la información que se encuentra en esta sección "Bibliotecas de terceros", mediante el uso del repositorio [azure-content](http://github.com/Azure/azure-content/) propiedad de **Azure** en GitHub.com. Microsoft anima a los desarrolladores a actualizar esta sección. *No* está previsto que el personal de Microsoft mantenga la información de esta sección, en parte porque otras personas son más expertas con las bibliotecas de terceros que nosotros. Gracias.

En la tabla siguiente se muestran las bibliotecas publicadas por terceros como de otras compañías o por la comunidad. Las bibliotecas publicadas por Microsoft están restringidas a la sección anterior de este tema.

| Plataforma | Bibliotecas |
| :-- | :-- |
| Ruby | [tinytds *(org, stable)*](https://rubygems.org/gems/tiny_tds/versions/0.7.0) |
| GO | [go-mssqldb *(org, stable)*](https://github.com/denisenkom/go-mssqldb) |
| Python | [pymssql *(org, stable)*](http://pymssql.org/en/stable/) |
| Node.js | [Node-MSSQL *(npmjs)*](https://www.npmjs.com/package/node-mssql)<br/><br/>[Node-MSSQL-Connector *(npmjs)*](https://www.npmjs.com/package/node-mssql-connector) |
| C++ | [FreeTDS *(org)*](http://www.freetds.org/) |



<!--
https://en.wikipedia.org/wiki/Draft:Microsoft_SQL_Server_Libraries/Drivers
-->

<!---HONumber=AcomDC_0302_2016-->