<properties
	pageTitle="Copia de una base de datos SQL de Azure| Microsoft Azure"
	description="Creación de una copia de una base de datos SQL de Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="01/20/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Copiar una base de datos SQL de Azure

**Base de datos única**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [SQL](sql-database-copy-transact-sql.md)

En los siguientes pasos se muestra cómo copiar una base de datos SQL con el [Portal de Azure](https://portal.azure.com). La operación de copia de la base de datos crea una nueva base de datos SQL. La copia es una copia de seguridad de instantánea de la base de datos que crea en el mismo servidor o en un servidor diferente.

> [AZURE.NOTE] La Base de datos SQL de Azure crea y mantiene automáticamente copias de seguridad para cada base de datos de usuario. Para obtener detalles, vea [Información general sobre la continuidad del negocio](sql-database-business-continuity.md).

Cuando se completa el proceso de copia, la nueva base de datos es una base de datos totalmente operativa que es independiente de la base de datos de origen. La nueva base de datos es transaccionalmente coherente con la base de datos de origen en el momento en que se completa la copia. El nivel de rendimiento y el nivel de servicio (nivel de precios) de la copia de base de datos son los mismos que la base de datos de origen. Cuando se complete la copia, esta se convierte en una base de datos independiente y completamente funcional. Los inicios de sesión, usuarios y permisos pueden administrarse de forma independiente.


Al copiar una base de datos en el mismo servidor lógico, los mismos inicios de sesión se pueden usar en ambas bases de datos. La entidad de seguridad que usa para copiar la base de datos se convierte en el propietario de la base de datos (DBO) en la nueva base de datos. Todos los usuarios de base de datos, sus permisos y sus identificadores de seguridad (SID) se copian en la copia de la base de datos.


Para copiar una base de datos SQL, necesita lo siguiente:

- Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
- Una base de datos SQL para copiar. Si no tiene ninguna base de datos SQL, siga los pasos de este artículo para crear una: [Creación de la primera base de datos SQL de Azure](sql-database-get-started.md).



## Copiar la base de datos SQL

Abra la hoja de base de datos SQL correspondiente a la base de datos que desea copiar:

1.	Vaya al [Portal de Azure](https://portal.azure.com).
2.	Vaya a la base de datos que quiere copiar: Examinar > Bases de datos SQL
3.	En la hoja de base de datos SQL, haga clic en **Copiar** para abrir la hoja **Copiar**:

    ![copiar base de datos][1]

1.  Escriba un nombre para la copia de la base de datos. Se proporciona un nombre predeterminado, pero puede cambiarlo si quiere.
2.  Seleccione un **Servidor de destino**. El servidor de destino es donde se creará la copia de la base de datos. Puede crear un nuevo servidor o seleccionar un servidor existente en la lista.
3.  Haga clic en **Aceptar** para iniciar el proceso de copia.

    ![nombre de la base de datos y del servidor][2]




## Supervisar el progreso de la operación de copia

2.	Después de iniciar la copia, haga clic en la notificación de portal para obtener información.


    ![notificación][3]

 
    ![monitor][4]





## Comprobar que la base de datos está disponible en el servidor

- Haga clic en **EXAMINAR** > **Bases de datos SQL** y compruebe que la nueva base de datos está **En línea**.



## Pasos siguientes

- [Conexión a la Base de datos SQL con SQL Server Management Studio y realización de una consulta de T-SQL de ejemplo](sql-database-connect-query-ssms.md)
- [Exportar la base de datos a un BACPAC](sql-database-export.md)



## Recursos adicionales

- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Documentación de la base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-copy/copy.png
[2]: ./media/sql-database-copy/copy-ok.png
[3]: ./media/sql-database-copy/copy-notification.png
[4]: ./media/sql-database-copy/monitor-copy.png

<!---HONumber=AcomDC_0211_2016-->