<properties
	pageTitle="Configuración de seguridad para Replicación geográfica activa o estándar"
	description="En este tema, se explican las consideraciones de seguridad para administrar escenarios de Replicación geográfica activa o estándar para Base de datos SQL."
	services="sql-database"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="10/22/2015"
	ms.author="jroth" />

# Configuración de seguridad para Replicación geográfica activa o estándar

## Información general
En este tema se describen los requisitos de autenticación para configurar y controlar [Replicación geográfica activa y estándar](sql-database-geo-replication-overview.md) y los pasos necesarios para configurar el acceso de usuario a la base de datos secundaria. Para obtener más información sobre el uso de la replicación geográfica, consulte [Recuperación de una base de datos SQL de Azure tras una interrupción](sql-database-disaster-recovery.md).

## Uso de usuarios independientes
Con la [versión V12 de Base de datos SQL de Azure](sql-database-v12-whats-new.md), Base de datos SQL admite ahora los usuarios independientes. A diferencia de los usuarios tradicionales, que deben asignarse a inicios de sesión en la base de datos maestra, un usuario independiente se administra completamente en la base de datos, lo que ofrece dos ventajas. En el escenario de replicación geográfica, los usuarios pueden proceder a conectarse a la base de datos secundaria sin ninguna configuración adicional, ya que la base de datos administra los usuarios. También existen ventajas potenciales de escalabilidad y rendimiento con esta configuración desde la perspectiva del inicio de sesión. Para obtener más información, consulte [Usuarios de base de datos independiente: hacer que la base de datos sea portátil](https://msdn.microsoft.com/library/ff929188.aspx).

Con los usuarios independientes, si tiene varias bases de datos que usan el mismo inicio de sesión, debe administrar ese usuario por separado para cada base de datos (por ejemplo, para un cambio de contraseña), en lugar de administrar el inicio de sesión en el servidor.

>[AZURE.NOTE]Si desea cambiar el acceso de lectura de la base de datos principal y la secundaria por separado, debe usar usuarios e inicios de sesión tradicionales. No se pueden administrar los usuarios independientes en la base de datos secundaria con independencia de la principal.

## Uso de usuarios e inicios de sesión tradicionales
Si usa usuarios e inicios de sesión tradicionales (en lugar de usuarios independientes), debe realizar pasos adicionales para asegurarse de que existan los mismos inicios de sesión en el servidor de la base de datos secundaria. En las secciones siguientes, se describen los pasos necesarios y otras consideraciones.

### Configuración del acceso de usuario para la base de datos secundaria en línea
Para que la base de datos secundaria se pueda usar como base de datos de solo lectura (secundaria en línea) o una copia de base de datos viable en una situación de conmutación por error, debe tener la configuración de seguridad adecuada.

El administrador del servidor es el único que puede completar correctamente todos los pasos descritos en este tema. Los permisos específicos para cada paso se describen más adelante en este tema.

La preparación del acceso de usuario a la base de datos secundaria en línea de Replicación geográfica activa puede realizarse en cualquier momento. Consta de los tres pasos descritos a continuación:

1. Determinar los inicios de sesión con acceso a la base de datos principal;
2. Buscar al SID de estos inicios de sesión en el servidor de origen;
3. Generar los inicios de sesión en el servidor de destino con el SID correspondiente del servidor de origen.

#### 1\. Determinar los inicios de sesión con acceso a la base de datos principal:
El primer paso del proceso es determinar qué inicios de sesión se deben duplicar en el servidor de destino. Esto se logra con un par de instrucciones SELECT, una en la base de datos maestra lógica del servidor de origen y otra, en la base de datos principal en sí.

El administrador del servidor o un miembro del rol de servidor **LoginManager** son los únicos que pueden determinar los inicios de sesión en el servidor de origen con la siguiente instrucción SELECT.

	SELECT [name], [sid] 
	FROM [sys].[sql_logins] 
	WHERE [type_desc] = 'SQL_Login'

Solo un miembro del rol de base de datos db\_owner, el usuario dbo o el administrador del servidor pueden determinar todas las entidades de seguridad de usuario de base de datos en la base de datos principal.

	SELECT [name], [sid]
	FROM [sys].[database_principals]
	WHERE [type_desc] = 'SQL_USER'

#### 2\. Buscar al SID de los inicios de sesión que identificó en el paso 1:
Al comparar los resultados de las consultas de la sección anterior y cotejar los SID, puede asignar el inicio de sesión de servidor a un usuario de base de datos. Los inicios de sesión que cuenten con un usuario de base de datos con un SID coincidente tienen acceso de usuario a esa base de datos como entidad de seguridad de usuario de base de datos.

La consulta siguiente se puede usar para ver todas las entidades de seguridad de usuario y sus SID en una base de datos. Solo un miembro del rol de servidor db\_owner o el administrador del servidor pueden ejecutar esta consulta.

	SELECT [name], [sid]
	FROM [sys].[database_principals]
	WHERE [type_desc] = 'SQL_USER'

>[AZURE.NOTE]Los usuarios de **sys** e **INFORMATION\_SCHEMA** tienen SID *NULL*, mientras que el SID de **guest** es **0x00**. El SID de **dbo** puede empezar por *0x01060000000001648000000000048454* si el creador de la base de datos era el administrador del servidor en lugar de un miembro de **DbManager**.

#### 3\. Generar los inicios de sesión en el servidor de destino:
El último paso consiste en ir al servidor o los servidores de destino y generar los inicios de sesión con los SID correspondientes. Esta es la sintaxis básica.

	CREATE LOGIN [<login name>]
	WITH PASSWORD = <login password>,
	SID = <desired login SID>

>[AZURE.NOTE]Si desea conceder acceso de usuario a la base de datos secundaria, pero no a la principal, puede hacerlo modificando el inicio de sesión de usuario en el servidor principal con la sintaxis siguiente.
>
>ALTER LOGIN <login name> DISABLE
>
>DISABLE no cambia la contraseña, por lo que siempre puede habilitarlo si es necesario.

## Configuración del acceso de usuario tras la finalización de una relación de copia continua
En caso de conmutación por error, se debe detener la relación de copia continua entre la base de datos principal y las secundarias. Para obtener más información sobre este proceso, consulte [Recuperación de una base de datos SQL de Azure tras una interrupción](sql-database-disaster-recovery.md).

En el caso de la replicación geográfica estándar, el usuario no puede acceder a la base de datos secundaria sin conexión, por lo que los cambios en las cuentas de usuario deben realizarse tras la finalización de la relación de copia continua.

Si los SID de inicio de sesión no se duplican en el servidor de destino, el acceso a la base de datos secundaria después de la finalización se restringe únicamente al administrador del servidor. Si el usuario que inicia la replicación es un DbManager, no tendrá acceso a la base de datos secundaria a menos que se duplique su SID de inicio de sesión desde el servidor de origen. Esto es así mientras dure el proceso de replicación.

Cuando se finaliza la replicación, como parte del proceso de finalización, se cambiará la entidad de seguridad de usuario [dbo] para que coincida con el SID de inicio de sesión del usuario que inició la replicación y se restaurará el acceso de ese usuario. Esto no se aplica a otros usuarios de la base de datos.

La cuenta de usuario y el inicio de sesión asociado que se usó para iniciar la operación de finalización deben estar presentes en el servidor de destino y la base de datos para asegurarse de que la cuenta de usuario pueda acceder a la base de datos secundaria una vez completada la finalización.

Para obtener más información sobre los pasos necesarios después de la conmutación por error, consulte [Finalización de una base de datos SQL de Azure recuperada](sql-database-recovered-finalize.md).

## Pasos siguientes
Para obtener más información sobre la replicación geográfica y otras características de continuidad empresarial de Base de datos SQL, consulte [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md).

<!---HONumber=Oct15_HO4-->