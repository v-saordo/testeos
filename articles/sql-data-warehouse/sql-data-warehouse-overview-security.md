<properties
   pageTitle="Proteger una base de datos en Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para proteger una base de datos en Almacenamiento de datos SQL de Azure para desarrollar soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="sahajs;barbkess;sonyama"/>

# Proteger una base de datos en Almacenamiento de datos SQL

En este artículo se describen los fundamentos de la protección de una base de datos de Almacenamiento de datos SQL de Azure. En concreto, este artículo le ayudará a empezar a trabajar con los recursos para limitar el acceso, proteger los datos y supervisar las actividades en una base de datos.

## Seguridad de conexión

Seguridad de conexión hace referencia a cómo restringir y proteger las conexiones a la base de datos mediante reglas de firewall y cifrado de las conexiones.

Las reglas de firewall las usan tanto el servidor como la base de datos para rechazar los intentos de conexión desde direcciones IP que no se hayan incluido explícitamente en la lista blanca. Para permitir que la aplicación o la dirección IP pública del equipo cliente intente conectarse a una nueva base de datos, primero debe crear una regla de firewall de nivel de servidor mediante el Portal de Azure clásico, API de REST o PowerShell. Como práctica recomendada, debe restringir los intervalos de direcciones IP que se permite que atraviesen el firewall del servidor tanto como sea posible. Para obtener más información, vea [Firewall de Base de datos SQL de Azure][].


## Autenticación

La autenticación indica a cómo demostrar su identidad al conectarse a la base de datos. Actualmente, Almacenamiento de datos SQL admite la autenticación de SQL con un nombre de usuario y una contraseña.

Al crear el servidor lógico de la base de datos, especificó un inicio de sesión de "administrador de servidor" con un nombre de usuario y una contraseña. Con estas credenciales, puede autenticarse en cualquier base de datos en ese servidor como propietario de la base de datos, o "dbo".

Sin embargo, como práctica recomendada, los usuarios de su organización deben usar una cuenta diferente para autenticar. De esta manera puede limitar los permisos concedidos a la aplicación y reducir los riesgos de actividad malintencionada en caso de que el código de aplicación sea vulnerable a ataques de inyección SQL. Para crear un usuario de base de datos basado en el inicio de sesión de servidor:

En primer lugar, conéctese a la base de datos maestra en el servidor con su inicio de sesión de administrador de servidor y cree un nuevo inicio de sesión de servidor.

```
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'strong_password';

```

Luego, conéctese a la base de datos del Almacenamiento de datos de SQL con el inicio de sesión de administrador de servidor y cree un usuario de base de datos basado en el inicio de sesión de servidor que acaba de crear.

```

-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;

```

Para obtener más información sobre la autenticación en Base de datos SQL, consulte [Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure][].


## Autorización

La autorización hace referencia a lo que se puede hacer en la base de datos de Almacenamiento de datos SQL de Azure, y la controlan los permisos y las pertenencias del rol de la cuenta de usuario. Como procedimiento recomendado, debe conceder a los usuarios los privilegios mínimos necesarios. Almacenamiento de datos SQL de Azure facilita la administración con roles en T-SQL:

```
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

La cuenta de administrador de servidor con la que se está conectando forma parte de db\_owner, que tiene autoridad para realizar cualquier acción en la base de datos. Guarde esta cuenta para implementar las actualizaciones de los esquemas y otras operaciones de administración. Utilice la cuenta "ApplicationUser" con permisos más limitados para conectarse desde la aplicación a la base de datos con los privilegios mínimos que necesita la aplicación.

Existen varias formas de limitar aún más lo que los usuarios pueden hacer con Base de datos SQL de Azure:

- Los [roles de base de datos][] que no sean db\_datareader y db\_datawriter se pueden utilizar para crear cuentas de usuario de aplicación más eficaces o cuentas de administración menos eficaces.
- Los [permisos][] granulares permiten control qué operaciones se pueden realizar en columnas individuales, tablas, vistas, procedimientos y otros objetos de la base de datos.
- Los [procedimientos almacenados][] puede utilizarse para limitar las acciones que se pueden realizar en la base de datos.

La administración de bases de datos y servidores lógicos desde el Portal de Azure clásico o mediante la API del Administrador de recursos de Azure la controlan las asignaciones de roles de su cuenta de usuario del portal. Para obtener más información sobre este tema, consulte [Control de acceso basado en rol en el Portal de Azure][].



## Cifrado

Almacenamiento de datos SQL de Azure puede ayudar a proteger los datos mediante el cifrado de los mismos cuando estén "en reposo" o almacenados en archivos de base de datos y copias de seguridad, con el [cifrado de datos transparente][]. Para cifrar la base de datos, conéctese a la base de datos maestra en el servidor y ejecute:


```

ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;

```

También puede habilitar el cifrado de datos transparente de la configuración de la base de datos en el [Portal de Azure clásico][].



## Auditoría

La auditoría y el seguimiento de eventos de la base de datos pueden ayudarle a mantener el cumplimiento de las reglamentaciones y a identificar cualquier actividad sospechosa. La auditoría de Almacenamiento de datos SQL permite grabar los eventos de la base de datos en un registro de auditoría de una cuenta de Almacenamiento de Azure. La auditoría de Almacenamiento de datos SQL también se integra con Microsoft Power BI, con el fin de facilitar la generación de análisis e informes detallados. Para obtener más información, consulte [Introducción a la auditoría de Base de datos SQL][].



## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md


<!--MSDN references-->
[Firewall de Base de datos SQL de Azure]: https://msdn.microsoft.com/library/ee621782.aspx
[roles de base de datos]: https://msdn.microsoft.com/library/ms189121.aspx
[Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure]: https://msdn.microsoft.com/library/ee336235.aspx
[permisos]: https://msdn.microsoft.com/library/ms191291.aspx
[procedimientos almacenados]: https://msdn.microsoft.com/library/ms190782.aspx
[cifrado de datos transparente]: http://go.microsoft.com/fwlink/?LinkId=526242
[Introducción a la auditoría de Base de datos SQL]: sql-database-auditing-get-started.md
[Portal de Azure clásico]: https://portal.azure.com/

<!--Other Web references-->
[Control de acceso basado en rol en el Portal de Azure]: http://azure.microsoft.com/documentation/articles/role-based-access-control-configure.aspx

<!---HONumber=AcomDC_0114_2016-->