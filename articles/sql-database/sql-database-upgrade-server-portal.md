<properties 
	pageTitle="Actualización a la Base de datos SQL V12 de Azure mediante el Portal de Azure | Microsoft Azure" 
	description="Explicación acerca de cómo actualizar a la Base de datos SQL V12 de Azure, incluido cómo actualizar las bases de datos de tipo Web y Business y cómo actualizar un servidor V11 migrando sus bases de datos directamente a un grupo de bases de datos elásticas mediante el Portal de Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg"
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-management" 
	ms.date="02/23/2016" 
	ms.author="sstein"/>


# Actualización a la base de datos SQL V12 de Azure con el Portal de Azure


> [AZURE.SELECTOR]
- [Azure portal](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


La versión V12 de la Base de datos SQL es la versión más reciente, por lo que le recomendamos que realice esta actualización para la Base de datos SQL V12. La versión V12 de la Base de datos SQL le ofrece muchas más [ventajas con respecto a la versión anterior](sql-database-v12-whats-new.md), entre las que se incluyen:

- Compatibilidad mejorada con SQL Server.
- Rendimiento de tipo Premium mejorado y nuevos niveles de rendimiento.
- [Grupos de bases de datos elásticas](sql-database-elastic-pool.md).

Este artículo proporciona instrucciones para actualizar los servidores y las bases de datos existentes de la Base de datos SQL V11 a la versión V12 de la misma.

Durante el proceso de actualización a la versión V12, podrá actualizar cualquier base de datos de tipo Web y Business a un nuevo nivel de servicio, por lo que se incluyen instrucciones para actualizar este tipo de bases de datos.

Asimismo, la migración a un [grupo de bases de datos elásticas](sql-database-elastic-pool.md) puede resultarle más rentable que actualizar a niveles de rendimiento individuales (planes de tarifas) de bases de datos únicas. De igual manera, los grupos le permitirán simplificar la administración de sus bases de datos, ya que realmente solo necesita administrar la configuración de rendimiento del grupo, en vez de dedicarse a administrar independientemente los niveles de rendimiento de las bases de datos individuales. Si tiene bases de datos en varios servidores, puede considerar la posibilidad de colocarlas todas en el mismo servidor y aprovecharse de las ventajas que supondría implementarlas en un grupo. Puede [migrar de forma automática y con facilidad las bases de datos de los servidores V11 a grupos de bases de datos elásticas mediante PowerShell](sql-database-upgrade-server-powershell.md). Asimismo, también puede usar el portal para migrar bases de datos de V11 en un grupo, pero recuerde que en el portal ya debe tener un servidor V12 para poder crear un grupo. Si tiene [bases de datos que se pueden beneficiar de un grupo](sql-database-elastic-pool-guidance.md), más adelante encontrará instrucciones para que pueda crear el grupo una vez actualizado el servidor.

Tenga en cuenta que las bases de datos permanecerán en línea y seguirán funcionando durante toda la operación de actualización. En el momento en que realice la transición al nuevo nivel de rendimiento, es posible que se produzca una caída temporal de las conexiones a la base de datos, aunque no durarán mucho; normalmente oscilarán entre los 90 segundos y los 5 minutos. Si su aplicación tiene una [gestión de errores temporal para terminaciones de conexiones](sql-database-connect-central-recommendations.md), entonces es suficiente con que tome precauciones contra las interrupciones de la conexión al final de la actualización.

Recuerde que la actualización a la Base de datos SQL V12 no se puede deshacer. Después de realizar la actualización, no podrá revertir el servidor a la versión V11.

Después de realizar la actualización a V12, las [recomendaciones de nivel de servicio](sql-database-service-tier-advisor.md) y las [recomendaciones de grupos elásticos](sql-database-elastic-pool-portal.md#step-2-choose-a-pricing-tier) no estarán disponibles de forma inmediata; para ello, deberá esperar a que el servicio evalúe las cargas de trabajo en el nuevo servidor. El historial de recomendaciones del servidor V11 no se aplica a los servidores V12, así que no se conservará.


## Preparación de la actualización

- **Actualizar todas las bases de datos de tipo Web y Business**: consulte la sección [Actualizar todas las bases de datos de tipo Web y Business](sql-database-upgrade-server-portal.md#upgrade-all-web-and-business-databases) que tiene a continuación o use [PowerShell para actualizar las bases de datos y el servidor](sql-database-upgrade-server-powershell.md).
- **Revisar y suspender la replicación geográfica**: si la Base de datos SQL de Azure está configurada para replicación geográfica, debe documentar la configuración actual y [detener la replicación geográfica](sql-database-geo-replication-portal.md#remove-secondary-database). Una vez completada la actualización, debe volver a configurar la base de datos para la replicación geográfica.
- **Abra los siguientes puertos si tiene clientes en una máquina virtual de Azure**: si el programa cliente se conecta a la Base de datos SQL V12, mientras el cliente se ejecuta en una máquina virtual (VM) de Azure, debe abrir los intervalos de puerto 11000 a 11999 y de 14000 a 14999 en la máquina virtual. Para obtener más información, consulte [Puertos de la Base de datos SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md).



## Iniciar la actualización

1. En el [Portal de Azure](https://portal.azure.com/), vaya al servidor que desea actualizar seleccionando **EXAMINAR TODO** > **Servidores SQL** y seleccione el servidor deseado.
2. Seleccione **Última actualización de la Base de datos SQL** y, a continuación, seleccione **Actualizar este servidor**.

      ![actualizar el servidor][1]

## Actualización de todas las bases de datos de tipo Web y Business

Si el servidor tiene alguna base de datos Web o Business, debe actualizarla. Durante el proceso de actualización a la Base de datos SQL V12, también deberá actualizar todas las bases de datos Web y Business a un nuevo nivel de servicio.

Para ayudarle con la actualización, el servicio Base de datos SQL recomienda un nivel de servicio apropiado y un nivel de rendimiento (plan de tarifa) para cada base de datos. El servicio le recomendará el nivel que mejor se adapte a usted para ejecutar las cargas de trabajo de la base de datos existente; para ello, analizará el historial de uso de la base de datos.
    
3. En la hoja denominada **Actualizar este servidor**, seleccione cada base de datos para su revisión y seleccione el plan de tarifa recomendado al que desea realizar la actualización. Siempre puede examinar los planes de tarifa disponibles y seleccionar el que mejor se adapte a su entorno.


     ![bases de datos][2]


7. Tras hacer clic en el nivel sugerido, aparecerá la hoja **Elija su plan de tarifa**, donde puede seleccionar un nivel y luego hacer clic en el botón **Seleccionar** para cambiar a ese nivel. Seleccione un nuevo nivel para cada base de datos de tipo Web o Business.

    Es importante que se asegure de que la Base de datos SQL no esté bloqueada en ningún nivel de servicio ni de rendimiento, así, si cambian los requisitos de la base de datos, le será mucho más fácil cambiar el nivel de servicio y de rendimiento, escogiendo entre los que tenga disponibles. De hecho, las bases de datos SQL Basic, Standard y Premium se facturan por hora, y el usuario tiene la capacidad de escalar horizontalmente o reducir verticalmente cada base de datos cuatro veces en 24 horas.

    ![de películas][6]


Cuando todas las bases de datos del servidor sean aptas, estará listo para iniciar la actualización.

## Confirmación de la actualización

3. Cuando todas las bases de datos del servidor sean aptas para su actualización, tendrá que **ESCRIBIR EL NOMBRE DEL SERVIDOR** para confirmar que desea realizar la actualización y, a continuación, hacer clic en **Aceptar**. 

    ![comprobar la actualización][3]


4. La actualización se inicia y muestra la notificación del progreso en curso. Se inicia el proceso de actualización. Dependiendo de los detalles de las bases de datos específicas, la actualización a V12 puede tardar algún tiempo. Durante este tiempo, todas las bases de datos en este servidor permanecerán en línea, pero se limitarán las acciones de administración de base de datos y servidor.

    ![actualización en curso][4]

    En el momento de la transición real al nuevo nivel de rendimiento, puede producirse una caída temporal de las conexiones a la base de datos de muy poca duración (se suele medir en segundos). Si una aplicación tiene gestión de errores temporal (lógica de reintento) para terminaciones de conexiones, es suficiente protegerse frente a las conexiones interrumpidas al final de la actualización.

5. Una vez completada la operación de actualización, el estado de la hoja **Ultima actualización** mostrará el mensaje **Habilitada**.

    ![V12 habilitado][5]

## Movimiento de las bases de datos a un grupo de bases de datos elásticas

En el [Portal de Azure](https://portal.azure.com/), acceda al servidor V12 y haga clic en **Agregar grupo**.

O bien

Si ve un mensaje que le indica **Haga clic aquí para ver los grupos de bases de datos elásticas recomendados de este servidor**, haga clic en él para así crear fácilmente un grupo que esté optimizado para las bases de datos del servidor. Para obtener más información, consulte [Grupos de bases de datos elásticas recomendados](sql-database-elastic-pool-portal.md#recommended-elastic-database-pools).

![Adición de un grupo a un servidor][7]
   
Siga las instrucciones del artículo [Crear un grupo de bases de datos elásticas](sql-database-elastic-pool.md) para terminar de crear el grupo.

## Supervisión de las bases de datos después de actualizar a la Base de datos SQL V12

>[AZURE.IMPORTANT] Actualice a la última versión de SQL Server Management Studio (SSMS) para aprovechar las nuevas capacidades de v12. [Descargue SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
	
Después de la actualización, es recomendable que supervise la base de datos de forma activa para asegurarse de que todas las aplicaciones se están ejecutando correctamente y que tienen el rendimiento adecuado; de esta manera, podrá optimizar el uso según sea necesario.

Además de supervisar las bases de datos individuales, también puede supervisar los grupos de bases de datos elásticas [mediante el portal](sql-database-elastic-pool-portal.md#monitor-and-manage-an-elastic-database-pool) o mediante [PowerShell](sql-database-elastic-pool-powershell.md#monitoring-elastic-databases-and-elastic-database-pools).


**Datos de consumo de recursos**: para las bases de datos de niveles Básico, Estándar y Premium, tiene disponibles datos de consumo de recursos a través de una vista de administración dinámica (DMV) denominada [sys.dm\_ db\_ resource\_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) en la base de datos del usuario. Esta vista de administración dinámica proporciona información de consumo de recursos casi en tiempo real a intervalos de 15 segundos para la hora de funcionamiento anterior. El consumo de porcentaje de DTU para un intervalo se calcula como el consumo de porcentaje máximo de las dimensiones de CPU, E/S y de registro. Esta es una consulta para calcular el consumo medio de porcentaje de DTU durante la última hora:

    SELECT end_time
    	 , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Información de supervisión adicional:

- [Guía de rendimiento de Base de datos SQL de Azure para bases de datos individuales](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Consideraciones de precio y rendimiento para un grupo de bases de datos elásticas](sql-database-elastic-pool-guidance.md).
- [Supervisión de Base de datos SQL de Azure con vistas de administración dinámica](sql-database-monitoring-with-dmvs.md)




**Alertas:** configure “Alertas” en el Portal de Azure para recibir una notificación cuando el consumo de DTU de una base de datos actualizada alcance un determinado nivel. Las alertas de la base de datos pueden configurarse en el Portal de Azure con diferentes métricas de rendimiento como DTU, CPU, E/S y registro. Vaya a la base de datos y seleccione **Reglas de alerta** en la hoja **Configuración**.

Por ejemplo, puede configurar una alerta de correo electrónico en "Porcentaje de DTU" si el valor de porcentaje medio de DTU supera el 75 % en los últimos 5 minutos. Consulte [Recibir notificaciones de alerta](../azure-portal/insights-receive-alert-notifications.md) para obtener más información acerca de cómo configurar las notificaciones de alerta.





## Pasos siguientes

- [Busque recomendaciones para el grupo de bases de datos elásticas](sql-database-elastic-pool-portal.md#recommended-elastic-database-pools).
- [Cree un grupo de bases de datos elásticas](sql-database-elastic-pool-portal.md) y agregue algunas o todas las bases de datos al grupo.
- [Cambie el nivel de servicio y el nivel de rendimiento de su base de datos](sql-database-scale-up.md).



## Vínculos relacionados

- [Novedades de Base de datos SQL V12](sql-database-v12-whats-new.md)
- [Planeación y preparación para realizar la actualización a Base de datos SQL V12](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-upgrade-server-portal/latest-sql-database-update.png
[2]: ./media/sql-database-upgrade-server-portal/upgrade-server2.png
[3]: ./media/sql-database-upgrade-server-portal/upgrade-server3.png
[4]: ./media/sql-database-upgrade-server-portal/online-during-upgrade.png
[5]: ./media/sql-database-upgrade-server-portal/enabled.png
[6]: ./media/sql-database-upgrade-server-portal/recommendations.png
[7]: ./media/sql-database-upgrade-server-portal/new-elastic-pool.png

<!---HONumber=AcomDC_0224_2016-->