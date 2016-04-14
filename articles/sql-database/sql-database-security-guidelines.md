<properties
   pageTitle="Instrucciones y limitaciones de seguridad de Base de datos SQL de Azure | Microsoft Azure"
   description="Obtenga más información acerca de las instrucciones y limitaciones de la Base de datos SQL de Microsoft Azure relacionadas con la seguridad."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="02/16/2016"
   ms.author="rickbyh"/>

# Instrucciones y limitaciones de seguridad de Base de datos SQL de Azure

En este tema se describen las instrucciones y limitaciones de la Base de datos SQL de Microsoft Azure relacionadas con la seguridad. Tenga en cuenta los siguientes puntos al administrar la seguridad de las Bases de datos SQL de Azure.

## Firewall

El servicio de Base de datos SQL de Azure solo está disponible a través del puerto TCP 1433. Para tener acceso a una Base de datos SQL desde el equipo, asegúrese de que el firewall permite la comunicación TCP saliente en el puerto TCP 1433. Como parte del proceso de conexión, las conexiones de máquinas virtuales de Azure se redirigen a una dirección IP y un puerto diferente, único para cada rol de trabajo. El número de puerto estará comprendido en el rango del 11000 al 11999.

Para poder conectarse al servidor de Base de datos SQL de Azure por primera vez, debe usar el [Portal de Azure clásico](https://portal.azure.com) o el [Portal clásico de la plataforma de Azure](https://manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/All/dashboard) para configurar el firewall de la Base de datos SQL de Azure. Necesitará crear una configuración de firewall de nivel de servidor que permita los intentos de conexión desde su equipo o Azure al servidor de Base de datos SQL de Azure. Además, si desea controlar el acceso a determinadas bases de datos en el servidor de Base de datos SQL de Azure, cree reglas de firewall de nivel de base de datos para las bases de datos respectivas. Para obtener más información, vea [Firewall de Base de datos SQL de Azure](sql-database-firewall-configure.md).

## Cifrado de la conexión y validación de certificados

Todas las comunicaciones entre la Base de datos SQL y su aplicación requieren cifrado (SSL) en todo momento. Si la aplicación cliente no valida los certificados tras la conexión, la conexión a la Base de datos SQL es susceptible de recibir ataques de tipo "man in the middle".

Para validar certificados con código de aplicación o herramientas, solicite explícitamente una conexión cifrada y no confíe en los certificados de servidor. Si el código de su aplicación o las herramientas no solicitan una conexión cifrada, seguirán recibiendo conexiones cifradas. Sin embargo, es posible que no validen los certificados de servidor, por lo que serán susceptibles de recibir ataques de tipo "man in the middle".

Para validar certificados con código de aplicación ADO.NET, establezca ``Encrypt=True`` y ``TrustServerCertificate=False`` en la cadena de conexión de la base de datos. Para obtener más información, consulte [Ejemplo de código: lógica de reintento para conectarse a la Base de datos SQL de Azure con ADO.NET](https://msdn.microsoft.com/library/azure/ee336243.aspx).

SQL Server Management Studio también admite la validación de certificados. En el cuadro de diálogo **Conectar con el servidor**, haga clic en **Cifrar conexión** en la pestaña **Propiedades de conexión**.

> [AZURE.NOTE] SQL Server Management Studio no admite las conexiones con la Base de datos SQL en versiones anteriores a SQL Server 2008 R2.

Aunque SQLCMD admitía la Base de datos SQL a partir de SQL Server 2008, no admite la validación de certificados en las versiones anteriores a SQL Server 2008 R2. Para validar certificados con SQLCMD a partir de SQL Server 2008 R2, use la opción de línea de comandos ``-N`` y no use la opción ``-C``. Mediante la opción -N, SQLCMD solicita una conexión cifrada. Si no se usa la opción ``-C``, SQLCMD no confía implícitamente en el certificado de servidor y se ve obligado a validar el certificado.

Para obtener información técnica complementaria, consulte el artículo [Seguridad de conexión de Base de datos de SQL de Azure](http://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#comment-4847) en el sitio Wiki de TechNet.

## Autenticación

La autenticación de Active Directory (seguridad integrada) está disponible como versión preliminar en Base de datos SQL V12. Para obtener más información sobre cómo configurar la autenticación de AD, vea [Conexión a Base de datos SQL mediante autenticación de Azure Active Directory](sql-database-aad-authentication.md). Cuando no utilizan la versión preliminar, los usuarios deben proporcionar credenciales (nombre de usuario y contraseña) cada vez que se conectan a una Base de datos SQL. Para obtener más información acerca de la autenticación de SQL Server, consulte [Elegir un modo de autenticación](https://msdn.microsoft.com/library/ms144284.aspx) en los Libros en pantalla de SQL Server.

[Base de datos SQL V12](sql-database-v12-whats-new.md) permite a los usuarios autenticarse en la base de datos con los usuarios de la base de datos independiente. Para obtener más información, consulte [Usuarios de base de datos independiente: hacer que la base de datos sea portátil](https://msdn.microsoft.com/library/ff929188.aspx), [CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx) y [Bases de datos independientes](https://technet.microsoft.com/library/ff929071.aspx).

> [AZURE.NOTE] Microsoft recomienda usar los usuarios de la base de datos independiente para mejorar la escalabilidad.

El motor de la base de datos cierra las conexiones que permanecen inactivas durante más de 30 minutos. La conexión debe volver a iniciar sesión para poder usarse.

Las conexiones con la Base de datos SQL que están constantemente activas requieren volver a ser autorizadas (acción realizada por el motor de la base de datos) al menos cada 10 horas. El motor de la base de datos intenta repetir la autorización con la contraseña que se envió inicialmente y no se requiere ninguna acción del usuario. Por motivos de rendimiento, cuando se restablece una contraseña en Base de datos SQL, la conexión no se volverá a autenticar, incluso si esta se restablece debido a la agrupación de conexiones. Esto difiere del comportamiento del SQL Server local. Si se ha cambiado la contraseña desde que se autorizó la conexión inicialmente, deberá terminarse la conexión y establecerse una nueva conexión con la nueva contraseña. Los usuarios con el permiso KILL DATABASE CONNECTION pueden terminar explícitamente una conexión con la Base de datos SQL mediante el uso del comando [KILL](https://msdn.microsoft.com/library/ms173730.aspx).

## Inicios de sesión y usuarios

Al administrar los inicios de sesión y los usuarios de la Base de datos SQL, hay restricciones.

Para el inicio de sesión de entidad de seguridad de nivel de servidor, se aplican las siguientes restricciones:

- El usuario de la base de datos principal correspondiente al inicio de sesión principal de nivel de servidor no se puede alterar ni quitar. 
- Aunque el inicio de sesión principal del nivel de servidor no es miembro de los dos roles de base de datos **dbmanager** y **loginmanager** de la base de datos **maestra**, concede todos los permisos a estos dos roles.

> [AZURE.NOTE] Este inicio de sesión se crea durante el aprovisionamiento de servidores y es similar al inicio de sesión **sa** de una instancia de SQL Server.

Para todos los inicios de sesión, se aplican las siguientes restricciones:

- El idioma predeterminado es el inglés de Estados Unidos.
- Para tener acceso a la base de datos **maestra**, cada inicio de sesión debe estar asignado a una cuenta de usuario en la base de datos **maestra**. La base de datos **maestra** no admite usuarios de base de datos independiente.
- Si no especifica una base de datos en la cadena de conexión, se conectará a la base de datos **maestra** de forma predeterminada.
- Debe estar conectado a la base de datos **maestra** al ejecutar las instrucciones ``CREATE/ALTER/DROP LOGIN`` y ``CREATE/ALTER/DROP DATABASE``. 
- Al ejecutar las instrucciones ``CREATE/ALTER/DROP LOGIN`` y ``CREATE/ALTER/DROP DATABASE`` en una aplicación ADO.NET, no se permite el uso de comandos con parámetros. Para obtener más información, consulte [Comandos y parámetros](https://msdn.microsoft.com/library/ms254953.aspx).
- Al ejecutar las instrucciones ``CREATE/ALTER/DROP DATABASE`` y ``CREATE/ALTER/DROP LOGIN``, cada una de ellas deben ser la única instrucción de un lote de Transact-SQL. De lo contrario, se produce un error. Por ejemplo, la instrucción Transact-SQL siguiente comprueba si existe la base de datos. Si existe, se llama a una instrucción ``DROP DATABASE`` para quitar la base de datos. Dado que la instrucción ``DROP DATABASE`` no es la única instrucción del lote, la ejecución de la siguiente instrucción Transact-SQL producirá un error.

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

- Cuando se ejecuta la instrucción ``CREATE USER`` con la opción ``FOR/FROM LOGIN``, debe ser la única instrucción de un lote de Transact-SQL.
- Cuando se ejecuta la instrucción ``ALTER USER`` con la opción ``WITH LOGIN``, deben ser la única instrucción de un lote de Transact-SQL.
- Solamente el inicio de sesión principal y los miembros del nivel de servidor del rol de la base de datos **dbmanager** de la base de datos **maestra** tienen permiso para ejecutar las instrucciones ``CREATE DATABASE`` y ``DROP DATABASE``.
- Solamente el inicio de sesión principal del nivel de servidor y los miembros del rol de base de datos **loginmanager** de la base de datos **maestra** tienen permiso para ejecutar las instrucciones ``CREATE LOGIN``, ``ALTER LOGIN`` y ``DROP LOGIN``.
- Para ``CREATE/ALTER/DROP`` un usuario requiere el permiso ``ALTER ANY USER`` de la base de datos.
- Cuando el propietario de un rol de base de datos intenta agregar otro usuario de base de datos a ese rol o bien quitarlo, puede producirse el siguiente error: **El usuario o el rol “Nombre” no existe en la base de datos.** Este error se produce porque el usuario no es visible para el propietario. Para resolver este problema, conceda al propietario del rol el permiso ``VIEW DEFINITION`` del usuario. 

Para obtener más información sobre los inicios de sesión y los usuarios, consulte [Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure](sql-database-manage-logins.md).

## Prácticas recomendadas de seguridad

Tenga en cuenta los siguientes puntos para que las aplicaciones de Base de datos SQL de Azure resulten menos vulnerables a amenazas de seguridad:

- Use siempre las actualizaciones más recientes: cuando se conecte a la Base de datos SQL, use siempre la versión más reciente de las herramientas y las bibliotecas para evitar vulnerabilidades de seguridad.
- Bloquee las conexiones entrantes en el puerto TCP 1433: solo se necesitan las conexiones salientes del puerto TCP 1433 para que las aplicaciones se comuniquen con la Base de datos SQL. Si las demás aplicaciones de ese equipo no necesitan comunicaciones entrantes, asegúrese de que el firewall continúa bloqueando las conexiones entrantes en el puerto TCP 1433.
- Evitar vulnerabilidades por inyección: para asegurarse de que las aplicaciones no tienen vulnerabilidades por inyección de SQL, use consultas parametrizadas siempre que sea posible. Asimismo, asegúrese de revisar el código detenidamente y de ejecutar una prueba de penetración antes de implementar su aplicación.


## Consulte también

[Firewall de Base de datos SQL de Azure](sql-database-firewall-configure.md)

[Configuración del firewall (Base de datos SQL de Azure)](sql-database-configure-firewall-settings.md)

[Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure](sql-database-manage-logins.md)

[Centro de seguridad para el Motor de base de datos de SQL Server y Base de datos SQL Azure](https://msdn.microsoft.com/library/bb510589)

<!---HONumber=AcomDC_0218_2016-->