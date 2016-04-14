<properties
	pageTitle="Cambio del nivel de servicio y del nivel de rendimiento de una base de datos SQL de Azure"
	description="El cambio del nivel de servicio y del nivel de rendimiento de una base de datos SQL de Azure muestra cómo escalar o reducir verticalmente dicha base de datos. Cambio del nivel de precios de una base de datos SQL de Azure."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/02/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Cambio del nivel de servicio y del nivel de rendimiento (nivel de precios) de una base de datos SQL

**Base de datos única**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)

En este artículo se muestra cómo cambiar el nivel de servicio y el nivel de rendimiento de la base de datos SQL con el [Portal de Azure](https://portal.azure.com).

Utilice la información de [Actualización de las bases de datos SQL Web o Business a niveles de servicio nuevos](sql-database-upgrade-server-portal.md) y [Niveles de servicio y niveles de rendimiento de la Base de datos SQL de Azure](sql-database-service-tiers.md) para determinar el nivel de capa y el rendimiento de servicio adecuado para la Base de datos SQL de Azure.

> [AZURE.IMPORTANT] El cambio del nivel de servicio y del nivel de rendimiento de una base de datos SQL es una operación en línea. Esto significa que la base de datos permanecerá en línea y disponible durante toda la operación sin tiempo de inactividad.

- Para degradar una base de datos, esta no debe alcanzar el tamaño máximo permitido del nivel de servicio de destino. 
- Al actualizar una base de datos con [Replicación geográfica estándar](https://msdn.microsoft.com/library/azure/dn758204.aspx) o [Replicación geográfica activa](https://msdn.microsoft.com/library/azure/dn741339.aspx) habilitado, primero debe actualizar sus bases de datos secundarias en el nivel de rendimiento deseado antes de actualizar la base de datos principal.
- Al realizar una degradación desde un nivel de servicio Premium, primero es preciso finalizar todas las relaciones de Replicación geográfica. Puede seguir los pasos que se describen en el tema [Terminar una relación de copia continua](https://msdn.microsoft.com/library/azure/dn741323.aspx) para detener el proceso de replicación entre la base de datos principal y las bases de datos secundarias activas.
- Las ofertas del servicio de restauración son diferentes para los distintos niveles de servicio. Si cambia a un nivel inferior, puede perder la capacidad de restaurar a un momento dado o tener un período de retención de copias de seguridad más breve. Para obtener más información, consulte [Copia de seguridad y restauración de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/jj650016.aspx).
- Cambiar el plan de tarifa de la base de datos no cambia el tamaño máximo de la base de datos. Para cambiar el tamaño máximo de la base de datos use [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- Puede realizar un máximo de cuatro cambios de bases de datos individuales (nivel de servicio o niveles de rendimiento) en un periodo de 24 horas.
- Las nuevas propiedades de la base de datos no se aplican hasta que se completan los cambios.


**Para completar este artículo, necesitará lo siguiente:**

- Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
- una base de datos SQL de Azure. Si no tiene una base de datos SQL, cree una siguiendo los pasos de este artículo: [Creación de la primera Base de datos SQL de Azure](sql-database-get-started.md).


## Cambio del nivel de servicio y del nivel de rendimiento de su base de datos


Abra la hoja Base de datos SQL correspondiente a la base de datos que desea escalar o reducir verticalmente:

1.	Vaya al [Portal de Azure](https://portal.azure.com).
2.	Haga clic en **EXAMINAR TODO**.
3.	Haga clic en **Bases de datos SQL**.
2.	Haga clic en la base de datos que desee cambiar.
3.	En la hoja de la Base de datos SQL, haga clic en **Toda la configuración** y luego en **Plan de tarifa (escalar DTU)**.

    ![plan de tarifa][1]


1.  Seleccione un nuevo nivel y haga clic en **Seleccionar**:

    Al hacer clic en **Seleccionar** se envía una solicitud de escala para cambiar el nivel de base de datos. Según el tamaño de la base de datos, la operación de escala puede tardar algún tiempo en completarse. Haga clic en la notificación para obtener detalles y el estado de la operación de escala.

    > [AZURE.NOTE] Cambiar el plan de tarifa de la base de datos no cambia el tamaño máximo de la base de datos. Para cambiar el tamaño máximo de la base de datos use [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).

    ![seleccione nivel de precios][2]

3.	En la cinta de la izquierda, haga clic en **Notificaciones**:

    ![notificaciones][3]

## Compruebe que la base de datos está en el nivel de precios seleccionado

   Una vez completada la operación de escalado inspeccione y confirme que la base de datos está en el nivel deseado:

2.	Haga clic en **EXAMINAR TODO**.
3.	Haga clic en **Bases de datos SQL**.
2.	Haga clic en la base de datos que cargó.
3.	Compruebe el **Plan de tarifa** y confirme que está establecido en el nivel correcto.

    ![nuevo precio][4]


## Pasos siguientes

- Para cambiar el tamaño máximo de la base de datos, use [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- [Escalar y reducir horizontalmente](sql-database-elastic-scale-get-started.md)
- [Conectarse a Base de datos SQL y consultar dicha base de datos con SSMS](sql-database-connect-query-ssms.md)
- [Exportar una base de datos SQL de Azure](sql-database-export.md)

## Recursos adicionales

- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Documentación de la base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-scale-up/pricing-tile.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png

<!---HONumber=AcomDC_0224_2016-->