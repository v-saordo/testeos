<properties 
    pageTitle="Creación de una copia de una base de datos SQL de Azure con Transact-SQL" 
    description="Crear una copia de una base de datos SQL de Azure con Transact-SQL" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/23/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Creación de una copia de una base de datos SQL de Azure con Transact-SQL

**Base de datos única**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [SQL](sql-database-copy-transact-sql.md)



En los siguientes pasos se muestran cómo usar una base de datos SQL con Transact-SQL. La operación de copia de base de datos copia una base de datos SQL en una nueva base de datos mediante la instrucción [CREATE DATABASE](). La copia es una copia de seguridad de instantánea de la base de datos que crea en el mismo servidor o en un servidor diferente.


> [AZURE.NOTE] La Base de datos SQL de Azure crea y mantiene automáticamente copias de seguridad para cada base de datos de usuario. Para obtener detalles, vea [Información general sobre la continuidad del negocio](sql-database-business-continuity.md).


Cuando se completa el proceso de copia, la nueva base de datos es una base de datos totalmente operativa que es independiente de la base de datos de origen. La nueva base de datos es transaccionalmente coherente con la base de datos de origen en el momento en que se completa la copia. El nivel de rendimiento y el nivel de servicio (nivel de precios) de la copia de base de datos son los mismos que la base de datos de origen. Cuando se complete la copia, esta se convierte en una base de datos independiente y completamente funcional. Los inicios de sesión, usuarios y permisos pueden administrarse de forma independiente.


Al copiar una base de datos en el mismo servidor lógico, los mismos inicios de sesión se pueden usar en ambas bases de datos. La entidad de seguridad que usa para copiar la base de datos se convierte en el propietario de la base de datos (DBO) en la nueva base de datos. Todos los usuarios de base de datos, sus permisos y sus identificadores de seguridad (SID) se copian en la copia de la base de datos.


Necesitará lo siguiente para completar los pasos de este artículo:

- Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
- Una Base de datos SQL de Azure. Si no tiene ninguna base de datos SQL, siga los pasos de este artículo para crear una: [Creación de la primera base de datos SQL de Azure](sql-database-get-started.md).
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms174173.aspx). Si no dispone de SSMS, o si no están disponibles las características descritas en este artículo, [descargue la versión más reciente](https://msdn.microsoft.com/library/mt238290.aspx).




## Copiar la base de datos SQL

Inicie sesión en la base de datos maestra mediante el inicio de sesión de entidad de seguridad de nivel de servidor o el inicio de sesión que creó la base de datos que quiere copiar. Los inicios de sesión que no sonde la entidad de seguridad de nivel de servidor deben ser miembros del rol dbmanager para copiar bases de datos. Para obtener más información sobre inicios de sesión y la conexión al servidor, vea Administrar bases de datos, inicios de sesión y usuarios en la Base de datos SQL de Azure y desarrollo de la Base de datos SQL de Azure: temas de procedimientos, respectivamente.

Inicie la copia de la base de datos de origen con la instrucción CREATE DATABASE. Con la ejecución de esta instrucción se inicia el proceso de copia de la base de datos. Dado que copiar una base de datos es un proceso asincrónico, se devuelve la instrucción CREATE DATABASE antes de que la base de datos complete el proceso de copia.


### Copiar una base de datos SQL en el mismo servidor

Inicie sesión en la base de datos maestra mediante el inicio de sesión de entidad de seguridad de nivel de servidor o el inicio de sesión que creó la base de datos que quiere copiar. Los inicios de sesión que no sonde la entidad de seguridad de nivel de servidor deben ser miembros del rol dbmanager para copiar bases de datos.

Este comando copia Base de datos1 en una base de datos nueva denominada Base de datos2 en el mismo servidor. Según el tamaño de su base de datos, la operación de copia puede tardar algún tiempo en completarse.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### Copiar una base de datos SQL en un servidor diferente

Inicie sesión en la base de datos maestra del servidor de destino, el servidor de Base de datos SQL de Azure donde se creará la nueva base de datos. Use un inicio de sesión que tenga el mismo nombre y contraseña que el propietario de la base de datos (DBO) de la base de datos de origen en el servidor de Base de datos SQL de Azure de origen. El inicio de sesión en el servidor de destino también debe ser miembro del rol dbmanager o ser el inicio de sesión de entidad de seguridad de nivel de servidor.

Este comando copia Base de datos1 en servidor1- en un nueva base de datos denominada Base de datos2 en servidor2. Según el tamaño de su base de datos, la operación de copia puede tardar algún tiempo en completarse.


    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;
    

## Supervisar el progreso de la operación de copia

Supervise el proceso de copia consultando las vistas sys.databases y sys.dm\_database\_copies. Mientras que la copia esté en curso, la columna state\_desc de la vista sys.databases para la nueva base de datos se establece en COPYING.


- Si se produce un error en el proceso de copia, la columna state\_desc de la vista sys.databases para la nueva base de datos se establece en SUSPECT. En este caso, ejecute la instrucción DROP en la nueva base de datos e inténtelo de nuevo más tarde.
- Si el proceso de copia es correcto, la columna state\_desc de la vista sys.databases para la nueva base de datos se establece en ONLINE. En este caso, se completa la copia y la nueva base de datos es una base de datos normal, que se puede modificar independientemente de la base de datos de origen.



## Pasos siguientes


- Si decide cancelar la copia mientras está en curso, ejecute la instrucción [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) en la nueva base de datos. Como alternativa, al ejecutar la instrucción DROP DATABASE en la base de datos de origen también se cancelará el proceso de copia.
- Después de que la nueva base de datos esté en línea en el servidor de destino, use la instrucción [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) para volver a asignar los usuarios de la nueva base de datos a inicios de sesión en el servidor de destino. Todos los usuarios de la nueva base de datos mantienen los permisos que tenían en la base de datos de origen. El usuario que inició la copia de la base de datos se convierte en el propietario de la base de datos de la nueva base de datos y se le asigna un nuevo identificador de seguridad (SID). Cuando la copia se realiza correctamente y antes de que se reasignen otros usuarios, solo el inicio de sesión que inició la copia, el propietario de la base de datos (DBO), puede iniciar sesión en la nueva base de datos.




## Recursos adicionales

- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Obtención de detalles de la recuperación ante desastres](sql-database-disaster-recovery-drills.md)
- [Documentación de Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0224_2016-->