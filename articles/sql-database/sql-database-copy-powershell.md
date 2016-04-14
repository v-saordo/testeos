<properties 
    pageTitle="Creación de una copia de una base de datos SQL de Azure con PowerShell | Microsoft Azure" 
    description="Crear copia de una base de datos SQL de Azure con PowerShell" 
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


# Creación de una base de datos SQL de Azure con PowerShell

**Base de datos única**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [SQL](sql-database-copy-transact-sql.md)



En los siguientes pasos se muestra cómo copiar una base de datos SQL con PowerShell. La operación de copia de base de datos copia una base de datos SQL en una nueva base de datos mediante el cmdlet [Start-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720220.aspx). La copia es una copia de seguridad de instantánea de la base de datos que crea en el mismo servidor o en un servidor diferente.

> [AZURE.NOTE]La Base de datos SQL de Azure crea y mantiene automáticamente copias de seguridad para cada base de datos de usuario. Para obtener detalles, vea [Información general sobre la continuidad del negocio](sql-database-business-continuity.md).

Cuando se completa el proceso de copia, la nueva base de datos es una base de datos totalmente operativa que es independiente de la base de datos de origen. La nueva base de datos es transaccionalmente coherente con la base de datos de origen en el momento en que se completa la copia. El nivel de rendimiento y el nivel de servicio (nivel de precios) de la copia de base de datos son los mismos que la base de datos de origen. Cuando se complete la copia, esta se convierte en una base de datos independiente y completamente funcional. Los inicios de sesión, usuarios y permisos pueden administrarse de forma independiente.


Al copiar una base de datos en el mismo servidor lógico, los mismos inicios de sesión se pueden usar en ambas bases de datos. La entidad de seguridad que usa para copiar la base de datos se convierte en el propietario de la base de datos (DBO) en la nueva base de datos. Todos los usuarios de base de datos, sus permisos y sus identificadores de seguridad (SID) se copian en la copia de la base de datos.


Para completar este artículo, necesitará lo siguiente:

- Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
- Una Base de datos SQL de Azure. Si no tiene ninguna base de datos SQL, siga los pasos de este artículo para crear una: [Creación de la primera base de datos SQL de Azure](sql-database-get-started.md).
- Azure PowerShell. Puede descargar e instalar los módulos de Azure PowerShell mediante la ejecución del [Instalador de plataforma web de Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](powershell-install-configure.md).



## Configuración de las credenciales y selección de la suscripción

En primer lugar debe establecer el acceso a su cuenta de Azure; por tanto, inicie PowerShell y luego ejecute el siguiente cmdlet. En la pantalla de inicio de sesión escriba el mismo correo electrónico y la misma contraseña que usa para iniciar sesión en el Portal de Azure clásico.

	Add-AzureAccount

Después de iniciar sesión correctamente, verá información en la pantalla que incluye el identificador con el que ha iniciado sesión y las suscripciones a Azure a las que tiene acceso.


### Selección de su suscripción a Azure

Para seleccionar la suscripción, necesitará el nombre o el identificador de suscripción (**-SubscriptionName**). Puede copiar el identificador de suscripción de la información mostrada en el paso anterior o, si dispone de varias suscripciones y necesita más detalles, puede ejecutar el cmdlet **Get-AzureSubscription** y copiar la información de suscripción que desee del conjunto de resultados. Cuando tenga su suscripción, ejecute el siguiente cmdlet:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Después de ejecutar correctamente **Select-AzureSubscription** volverá al símbolo del sistema de PowerShell. Si tiene más de una suscripción, puede ejecutar **Get-AzureSubscription** y comprobar que la suscripción que desea usar muestra **IsCurrent: True**.


## Configuración de las variables para su entorno específico

Hay algunas variables en las que es necesario reemplazar los valores de ejemplo por los valores específicos de la base de datos y los servidores.

Reemplace los valores de marcador de posición por los valores para su entorno:

    # The name of the server on which the source database resides.
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy). 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server that hosts the target database. This server must be in the same Azure subscription as the source database server. 
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy).
    $PartnerDatabaseName = "partnerDatabaseName" 





## Copiar una base de datos SQL en el mismo servidor

Este comando envía la solicitud de copia de base de datos para el servicio. Según el tamaño de su base de datos, la operación de copia puede tardar algún tiempo en completarse.

    # Copy a database to the same server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerDatabase $PartnerDatabaseName

## Copiar una base de datos SQL en un servidor diferente

Este comando envía la solicitud de copia de base de datos para el servicio. Según el tamaño de su base de datos, la operación de copia puede tardar algún tiempo en completarse.

    # Copy a database to a different server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    

## Supervisar el progreso de la operación de copia

Después de ejecutar **Start-AzureSqlDatabaseCopy**, puede comprobar el estado de la solicitud de copia. Cuando se ejecuta esto justo después de la solicitud, normalmente devolverá **Estado: Pendiente** o **Estado: En ejecución** para que pueda ejecutarlo varias veces hasta que vea **Estado: COMPLETADO** en la salida.


    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName


## Copia de un script de PowerShell de base de datos SQL

    # The name of the server where the source database resides
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy) 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server to host the database copy. This server must be in the same Azure subscription as the source database server
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy)
    $PartnerDatabaseName = "partnerDatabaseName" 


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionName "myAzureSubscriptionName"
      
    # Copy a database to a different server (remove the -PartnerServer parameter to copy to the same server)
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    
    # Monitor the status of the copy
    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName
    

## Pasos siguientes

- [Conexión a la Base de datos SQL con SQL Server Management Studio y realización de una consulta de T-SQL de ejemplo](sql-database-connect-query-ssms.md)
- [Exportar la base de datos a un BACPAC](sql-database-export-powershell.md)


## Recursos adicionales

- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Obtención de detalles de la recuperación ante desastres](sql-database-disaster-recovery-drills.md)
- [Documentación de Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0121_2016-->