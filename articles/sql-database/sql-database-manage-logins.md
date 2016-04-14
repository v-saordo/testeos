<properties
   pageTitle="Administración de seguridad de la Base de datos SQL: seguridad de inicio de sesión | Microsoft Azure"
   description="Obtenga información acerca de la administración de seguridad de la Base de datos SQL, específicamente el modo de administrar la seguridad del inicio de sesión y el acceso a la base de datos con la cuenta de entidad de seguridad a nivel de servidor."
   keywords="seguridad de la Base de datos SQL, administración de seguridad de la base de datos, seguridad de inicio de sesión, seguridad de la base de datos, acceso a la base de datos"
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
   ms.date="02/01/2016"
   ms.author="rickbyh"/>

# Seguridad de la Base de datos SQL: administrar la seguridad del inicio de sesión y el acceso a la base de datos  

Obtenga información acerca de la administración de seguridad de la Base de datos SQL, específicamente el modo de administrar la seguridad del inicio de sesión y el acceso a la base de datos con la cuenta de entidad de seguridad a nivel de servidor. Comprenda algunas diferencias y similitudes entre las opciones de seguridad del inicio de sesión de la Base de datos SQL y las de un servidor local de SQL Server.

## Aprovisionamiento de la base de datos e inicio de sesión de la entidad de seguridad a nivel de servidor

En la Base de datos SQL de Microsoft Azure, al registrarse para el servicio, el proceso de aprovisionamiento crea un servidor de Base de datos de SQL Azure, una base de datos denominada **principal** y un inicio de sesión que es la entidad de seguridad de nivel de servidor de su servidor de Base de datos SQL de Azure. Ese inicio de sesión es similar a la entidad de seguridad de nivel de servidor, **sa**, para una instancia local de SQL Server.

La cuenta principal de nivel de servidor de Base de datos SQL de Azure siempre tiene permiso para administrar toda la seguridad de servidor y de base de datos. En este tema se describe cómo puede usar la entidad de seguridad de nivel de servidor y otras cuentas para administrar los inicios de sesión y bases de datos de la Base de datos SQL.

Los usuarios de Azure que acceden a Base de datos SQL a través del Control de acceso basado en roles de Azure y la API de REST del Administrador de recursos de Azure reciben permisos de sus roles de Azure. El motor de base de datos ejecuta las acciones de los miembros de roles de Azure. A ellos no les afecta el modelo de permisos del motor de base de datos, por lo que no se tratan en este tema. Para obtener más información, vea [RBAC: Roles integrados]( https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/#sql-db-contributor).

> [AZURE.IMPORTANT] Base de datos SQL V12 permite a los usuarios autenticarse en la base de datos mediante el uso de los usuarios de la base de datos independiente. Los usuarios de la base de datos independiente no requieren inicios de sesión. Esto provoca que las bases de datos resulten más portátiles, pero reduce la capacidad de la entidad de seguridad de nivel de servidor de controlar el acceso a la base de datos. Permitir a los usuarios de bases de datos independientes tiene repercusiones importantes en la seguridad. Para obtener más información, vea [Usuarios de base de datos independiente - Conversión de la base de datos en portátil](https://msdn.microsoft.com/library/ff929188.aspx), [Bases de datos independientes](https://technet.microsoft.com/library/ff929071.aspx), [CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx) y [Conexión a Base de datos SQL con autenticación de Azure Active Directory](sql-database-aad-authentication.md).

## Información general sobre la administración de seguridad de la Base de datos SQL

La administración de seguridad de la Base de datos SQL es similar a la de una instancia local de SQL Server. Administrar la seguridad en el nivel de base de datos resulta casi igual, con algunas diferencias únicamente en los parámetros disponibles. Dado que las Bases de datos SQL pueden escalarse a uno o más equipos físicos, Base de datos SQL de Azure usa una estrategia diferente para la administración de nivel de servidor. En la tabla siguiente se resume en qué medida la administración de seguridad de la base de datos de un servidor local de SQL Server es diferente a la de la Base de datos SQL de Azure.

| Punto de diferencia | SQL Server local | Base de datos SQL de Azure |
|------------------------------------------------|-----------------------------------------------------------------------------|--------------------------------------------------|
| Dónde se administra la seguridad de nivel de servidor | Carpeta **Seguridad** del explorador de objetos de SQL Server Management Studio | Base de datos **maestra** y a través del Portal de Azure |
| Autenticación de Windows | Identidades de Active Directory | Identidades de Azure Active Directory |
| Rol de seguridad de nivel de servidor para crear inicios de sesión | Rol fijo de servidor **securityadmin** | Rol de base de datos **loginmanager** en la base de datos **maestra** |
| Comandos para administrar los inicios de sesión | CREATE LOGIN, ALTER LOGIN, DROP LOGIN | CREATE LOGIN, ALTER LOGIN, DROP LOGIN (hay algunas limitaciones en los parámetros y se debe estar conectado a la base de datos **maestra**). |
| Vista que muestra todos los inicios de sesión | sys.server\_principals | sys.sql\_logins (se debe estar conectado a la base de datos **maestra**).|
| Rol de nivel de servidor para crear bases de datos | Rol fijo de base de datos **dbcreator** | Rol de base de datos **dbmanager** en la base de datos **maestra** |
| Comando para crear una base de datos | CREATE DATABASE | CREATE DATABASE (hay algunas limitaciones en los parámetros y se debe estar conectado a la base de datos **maestra**). |
| Vista que muestra todas las bases de datos | Sys.Databases | sys.databases (se debe estar conectado a la base de datos **maestra**). |

## Administración de nivel de servidor y base de datos maestra

Su servidor de Base de datos SQL de Azure es una abstracción que define una agrupación de bases de datos. Es posible que las bases de datos asociadas con su servidor de Base de datos SQL de Azure resida en equipos físicos independientes en el centro de datos de Microsoft. Realice la administración de nivel de servidor para todas ellas con una base de datos única llamada **maestra**.

La base de datos **maestra** realiza un seguimiento de los inicios de sesión y de cuáles tienen permiso para crear bases de datos u otros inicios de sesión. Se debe estar conectado a la base de datos **maestra** siempre que se creen, modifiquen o quiten inicios de sesión o bases de datos. La base de datos **maestra** también tiene las vistas ``sys.sql_logins`` y ``sys.databases`` que puede usar para ver los inicios de sesión y las bases de datos, respectivamente.

> [AZURE.NOTE] No se admite el comando ``USE`` para cambiar de una base de datos a otra. Establezca una conexión directa con la base de datos de destino.

Puede administrar la seguridad de nivel de base de datos para los usuarios y objetos de Base de datos SQL de Azure del mismo modo que con las instancias locales de SQL Server. Solamente hay diferencias en los parámetros disponibles en los comandos correspondientes. Para obtener más información, vea [Instrucciones y limitaciones de seguridad de Base de datos SQL de Azure](sql-database-security-guidelines.md).

## Administración de usuarios de base de datos independiente

Cree el primer usuario de la base de datos independiente en una base de datos mediante la conexión a la base de datos con la entidad de seguridad de nivel de servidor. Use las instrucciones ``CREATE USER``, ``ALTER USER`` o ``DROP USER``. En el ejemplo siguiente se crea un usuario denominado user1.

```
CREATE USER user1 WITH password='<Strong_Password>';
```

> [AZURE.NOTE] Debe usar una contraseña segura al crear un usuario de la base de datos independiente. Para obtener más información, consulte [Contraseñas seguras](https://msdn.microsoft.com/library/ms161962.aspx).

Cualquier usuario con el permiso **ALTER ANY USER** puede crear usuarios de base de datos independiente adicionales.

Base de datos de SQL V12 admite identidades de Azure Active Directory como usuarios de base de datos independiente, como característica de vista previa. Para obtener más información, vea [Conexión a Base de datos SQL con autenticación de Azure Active Directory](sql-database-aad-authentication.md).

Microsoft recomienda usar los usuarios de la base de datos independiente con Base de datos SQL. Para obtener más información, vea [Usuarios de base de datos independiente - Conversión de la base de datos en portátil](https://msdn.microsoft.com/library/ff929188.aspx).

## Administración de inicios de sesión

Administre inicios de sesión con el inicio de sesión principal de nivel de servidor mediante la conexión a la base de datos maestra. Puede usar las instrucciones ``CREATE LOGIN``, ``ALTER LOGIN`` o ``DROP LOGIN``. En el ejemplo siguiente se crea un inicio de sesión denominado **login1**:

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
```

> [AZURE.NOTE] Debe usar una contraseña segura al crear un inicio de sesión. Para obtener más información, consulte [Contraseñas seguras](https://msdn.microsoft.com/library/ms161962.aspx).

#### Uso de nuevos inicios de sesión

Para conectarse a Base de datos SQL de Microsoft Azure con los inicios de sesión que cree, debe conceder primero permisos de nivel de base de datos a cada inicio de sesión con el comando ``CREATE USER``. Para obtener más información, consulte la sección **Conceder acceso de base de datos a un inicio de sesión** que tiene a continuación.

Dado que algunas herramientas implementan los flujos de datos tabulares (TDS) de manera diferente, es posible que tenga que anexar el nombre del servidor de la Base de datos SQL de Azure al inicio de la sesión de la cadena de conexión, mediante la notación ``<login>@<server>``. En estos casos, debe separar el inicio de sesión y el nombre del servidor de la Base de datos SQL de Azure mediante el símbolo ``@``. Por ejemplo, si el inicio de sesión se denomina **login1** y el nombre completo del servidor de la Base de datos SQL de Azure es **servername.database.windows.net**, el parámetro del nombre de usuario de la cadena de conexión debe ser: ****login1@servername**. Esta restricción impone limitaciones sobre el texto que puede elegir para el nombre de inicio de sesión. Para obtener más información, consulte [CREATE LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx).

## Conceder permisos de nivel de servidor a un inicio de sesión

Para que solo los inicios de sesión principales de nivel de servidor administren la seguridad del nivel de servidor, la Base de datos SQL de Azure ofrece dos roles de seguridad: **loginmanager**, para crear inicios de sesión y **dbmanager** para crear bases de datos. Solo pueden agregarse usuarios de la base de datos **maestra** a estos roles de base de datos.

> [AZURE.NOTE] Para crear inicios de sesión o bases de datos, debe estar conectado a la base de datos **maestra** (la cual es una representación lógica de **maestra**).

### El rol loginmanager

Al igual que el rol fijo de servidor **securityadmin** de una instancia local de SQL Server, el rol de base de datos **loginmanager** de la Base de datos SQL de Azure, tiene permiso para crear inicios de sesión. Solo el inicio de sesión principal de nivel de servidor (creado por el proceso de aprovisionamiento) o los miembros del rol de base de datos **loginmanager** pueden crear inicios de sesión.

### El rol dbmanager

El rol de base de datos **dbmanager** de Base de datos SQL de Microsoft Azure es similar al rol fijo de servidor **dbcreator** para una instancia local de SQL Server. Solo el inicio de sesión principal de nivel de servidor (creado por el proceso de aprovisionamiento) o los miembros del rol de base de datos **dbmanager** pueden crear bases de datos. Una vez que un usuario es miembro del rol de base de datos **dbmanager**, este puede crear una base de datos con el comando ``CREATE DATABASE`` de la Base de datos SQL de Azure, pero tenga en cuenta que ese comando se debe ejecutar en la base de datos maestra. Para obtener más información, consulte [CREATE DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/dn268335.aspx).

### Cómo asignar roles de nivel de servidor de Base de datos SQL

Para crear un inicio de sesión y un usuario asociado que pueda crear bases de datos u otros inicios de sesión, realice los pasos siguientes:

1. Conéctese a la base de datos **maestra** con las credenciales del inicio de sesión de entidad de seguridad a nivel de servidor (creado por el proceso de aprovisionamiento) o las credenciales de un miembro existente del rol de base de datos **loginmanager**.
2. Crear un inicio de sesión con el comando ``CREATE LOGIN``. Para obtener más información, consulte [CREAR INICIO DE SESIÓN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx).
3. Cree un nuevo usuario para ese inicio de sesión en la base de datos maestra usando el comando ``CREATE USER``. Para obtener más información, consulte [CREAR USUARIO (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).
4. Use el procedimiento almacenado ``sp_addrolememeber`` para agregar un nuevo usuario al rol de base de datos **dbmanager**, al rol de base de datos loginmanager, o a ambos.

En el ejemplo de código siguiente se muestra cómo crear un inicio de sesión denominado **login1**, y un usuario de base de datos correspondiente denominado **login1User** que es capaz de crear bases de datos u otros inicios de sesión mientras está conectado a la base de datos **maestra**:

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
CREATE USER login1User FROM LOGIN login1;
EXEC sp_addrolemember 'dbmanager', 'login1User';
EXEC sp_addrolemember 'loginmanager', 'login1User';
```

> [AZURE.NOTE] Debe usar una contraseña segura al crear un inicio de sesión. Para obtener más información, consulte [Contraseñas seguras](https://msdn.microsoft.com/library/ms161962.aspx).

## Concesión de acceso de base de datos a un inicio de sesión

Todos los inicios de sesión deben crearse en la base de datos **maestra**. Después de crear un inicio de sesión, puede crear una cuenta de usuario en otra base de datos para ese inicio de sesión. Base de datos SQL de Azure también admite roles de base de datos en la misma forma que una instancia local de SQL Server.

Para crear una cuenta de usuario en otra base de datos, suponiendo que no se ha creado un inicio de sesión o una base de datos, realice los pasos siguientes:

1. Conéctese a la base de datos **maestra** (con un inicio de sesión que tenga los roles **loginmanager** y **dbmanager**).
2. Crear un inicio de sesión mediante el comando ``CREATE LOGIN``. Para obtener más información, vea [CREATE LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx). La autenticación de Windows no es compatible.
3. Cree una base de datos con el comando ``CREATE DATABASE``. Para obtener más información, consulte [CREAR BASE DE DATOS (Transact-SQL)](https://msdn.microsoft.com/library/dn268335.aspx).
4. Establezca una conexión con la nueva base de datos (con el inicio de sesión que creó la base de datos).
5. Cree un usuario en la nueva base de datos con el comando ``CREATE USER``. Para obtener más información, consulte [CREAR USUARIO (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

En el ejemplo de código siguiente se muestra cómo crear un inicio de sesión denominado **login1** y una base de datos denominada **database1**:

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
CREATE DATABASE database1;
```

> [AZURE.NOTE] Debe usar una contraseña segura al crear un inicio de sesión. Para obtener más información, consulte [Contraseñas seguras](https://msdn.microsoft.com/library/ms161962.aspx).

En el ejemplo siguiente se muestra cómo crear un usuario de base de datos denominado **login1User** en la base de datos **database1** que corresponde al inicio de sesión **login1**. Para ejecutar el ejemplo siguiente, primero debe crear una nueva conexión a database1, usando un inicio de sesión con el permiso **ALTER ANY USER** en esa base de datos. Cualquier usuario que se conecte como miembro del rol **db\_owner** obtendrá ese permiso, como el inicio de sesión que creó la base de datos.

```
-- Establish a new connection to the database1 database
CREATE USER login1User FROM LOGIN login1;
```

Este modelo de permisos de nivel de base de datos de Base de datos SQL de Azure es el mismo que una instancia local de SQL Server. Para obtener información, consulte los temas siguientes en las referencias de los Libros en pantalla de SQL Server.

- [Temas de procedimientos de administración de inicios de sesión, usuarios y esquemas](https://msdn.microsoft.com/library/aa337552.aspx)
- [Lección 2: Configuración de permisos en objetos de base de datos](https://msdn.microsoft.com/library/ms365345.aspx)

> [AZURE.NOTE] Las instrucciones de Transact-SQL relacionadas con la seguridad en la Base de datos SQL de Azure pueden diferir ligeramente en los parámetros que están disponibles. Para obtener más información, consulte instrucciones específicas en la sintaxis de Libros en pantalla.

## Visualización de los inicios de sesión y las bases de datos


Para ver los inicios de sesión y las bases de datos en el servidor de la Base de datos SQL de Azure, use las vistas ``sys.sql_logins`` y ``sys.databases``, respectivamente, de la base de datos maestra. En el ejemplo siguiente se muestra cómo mostrar una lista de todos los inicios de sesión y las bases de datos del servidor de Base de datos SQL de Azure.

```
-- first, connect to the master database
SELECT * FROM sys.sql_logins;
SELECT * FROM sys.databases;
```

## Consulte también

[Instrucciones y limitaciones de seguridad de la Base de datos SQL de Azure](sql-database-security-guidelines.md) [Conexión a la Base de datos SQL mediante la autenticación de Azure Active Directory](sql-database-aad-authentication.md)

<!----HONumber=AcomDC_0204_2016-->