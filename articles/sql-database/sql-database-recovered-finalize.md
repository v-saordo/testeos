<properties
   pageTitle="Finalización de una base de datos SQL de Azure recuperada"
   description="Restauración a un momento dado, base de datos de Microsoft Azure SQL, restaurar base de datos, recuperar base de datos, Portal de Azure clásico, Portal de Azure clásico"
   services="sql-database"
   documentationCenter=""
   authors="elfisher"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery"
   ms.date="02/09/2016"
   ms.author="elfish"/>

# Finalización de una base de datos SQL de Azure recuperada

## Información general

En este artículo se proporciona una lista de comprobación de las tareas que se deben realizar antes de devolver Base de datos SQL de Azure a producción. Esta lista de comprobación se aplica a las bases de datos recuperadas de la conmutación por error de replicación geográfica, restauración de bases de datos eliminadas, restauración a un momento dado o restauración geográfica.

## Actualización de cadenas de conexión

Compruebe que las cadenas de conexión de la aplicación apuntan a la base de datos recién recuperada. Actualice las cadenas de conexión si se aplica cualquiera de las siguiente situaciones:

  + La base de datos recuperada utiliza un nombre diferente del nombre de la base de datos de origen
  + La base de datos recuperada está en un servidor diferente del servidor de origen

Para obtener más información acerca de cómo cambiar las cadenas de conexión, consulte [Instrucciones para conectar con Base de datos SQL de Azure mediante programación](https://msdn.microsoft.com/library/azure/ee336282.aspx) y [Conexiones a la Base de datos SQL: Recomendaciones centrales ](sql-database-connect-central-recommendations.md).
 
## Modificación de reglas de firewall
Compruebe las reglas de firewall tanto en el nivel de servidor como en el nivel de base de datos y asegúrese de que están habilitadas las conexiones desde los equipos cliente o Azure al servidor y a la base de datos recién recuperada. Para obtener más información, consulte [Firewall de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx) y [Procedimiento: Establecer la configuración de firewall (Base de datos SQL de Azure)](https://msdn.microsoft.com/library/azure/jj553530.aspx).

## Comprobación de inicios de sesión de servidor y usuarios de base de datos

Compruebe si todos los inicios de sesión que usa la aplicación existen en el servidor que hospeda la base de datos recuperada. Vuelva a crear los inicios de sesión que falten y concédales los permisos adecuados en la base de datos recuperada. Para obtener más información, consulte [Administración de bases de datos, inicios de sesión y usuarios en Base de datos SQL de Microsoft Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

Compruebe si cada usuario de la base de datos recuperada está asociado a un inicio de sesión de servidor válido. Utilice la instrucción ALTER USER para asignar un inicio de sesión de servidor válido al usuario. Para obtener más información, consulte [ALTER USER](http://go.microsoft.com/fwlink/?LinkId=397486).


## Configuración de alertas de telemetría

Compruebe si la configuración de las reglas de alerta existente se ha asignado a la base de datos recuperada. Actualice la configuración si se da cualquiera de las situaciones siguientes:

  + La base de datos recuperada utiliza un nombre diferente del nombre de la base de datos de origen
  + La base de datos recuperada está en un servidor diferente del servidor de origen

Para obtener más información sobre las reglas de alerta de bases de datos, consulte [Procedimiento: Recepción de notificaciones de alerta y administración de reglas de alerta en Azure](https://msdn.microsoft.com/library/azure/dn306638.aspx).


## Habilitar auditoría

Si se requiere una auditoría para tener acceso a una base de datos, será preciso habilitar Auditoría tras la recuperación de la base de datos. Un buen indicador de que es necesaria una auditoría es que las aplicaciones cliente usen cadenas de conexión seguras en un patrón de *.database.secure.windows.net. Para obtener más información, consulte [Introducción a la auditoría de Base de datos SQL](sql-database-auditing-get-started.md).
 

<!---HONumber=AcomDC_0211_2016-->