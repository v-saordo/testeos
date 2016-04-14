<properties
    pageTitle="Configuración de la replicación geográfica de la Base de datos SQL de Azure con Transact-SQL | Microsoft Azure"
    description="Configuración de la replicación geográfica de la Base de datos SQL de Azure con Transact-SQL"
    services="sql-database"
    documentationCenter=""
    authors="carlrabeler"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management"
    ms.date="02/12/2016"
    ms.author="carlrab"/>

# Configuración de la replicación geográfica para una base de datos SQL de Azure con Transact-SQL



> [AZURE.SELECTOR]
- [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


En este artículo se muestra cómo configurar la replicación geográfica para una base de datos SQL de Azure mediante Transact-SQL.


La replicación geográfica permite crear hasta cuatro bases de datos de réplica (secundarias) en distintas ubicaciones de centros de datos (regiones). Las bases de datos secundarias están disponibles en caso de una interrupción del centro de datos o de imposibilidad para conectarse a la base de datos principal.

La replicación geográfica solo está disponible para las bases de datos Estándar y Premium.

Las bases de datos Estándar pueden tener una base de datos secundaria no legible y deben usar la región recomendada. Las bases de datos Premium pueden tener hasta cuatro bases de datos secundarias legibles en cualquiera de las regiones disponibles.


Para configurar la replicación geográfica, necesita lo siguiente:

- Una suscripción de Azure: si no tiene una suscripción de Azure, simplemente haga clic en **EVALUACIÓN GRATUITA** en la parte superior de esta página y, a continuación, vuelva para terminar de leer este artículo.
- Un servidor lógico de Base de datos SQL de Azure <MyLocalServer> y una base de datos SQL <MyDB>: la base de datos principal que quiere replicar en una región geográfica diferente.
- Uno o varios servidores lógicos de Base de datos SQL de Azure <MySecondaryServer(n)>: servidores lógicos que serán los servidores asociados en los que se crearán bases de datos secundarias de replicación geográfica.
- Un inicio de sesión que es DBManager en la base de datos principal (tiene db\_ownership de la base de datos local que va a replicar geográficamente) y que es DBManager en los servidores asociados en los que se configurará la replicación geográfica.
- La versión más reciente de SQL Server Management Studio: para obtener la versión más reciente de SQL Server Management Studio (SSMS), vaya a [Descarga de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Para obtener información sobre el uso de SQL Server Management Studio para administrar los servidores lógicos y las bases de datos de Base de datos SQL de Azure, consulte [Administración de Base de datos SQL de Azure con SQL Server Management Studio](sql-database-manage-azure-ssms.md)

## Agregar una base de datos secundaria

Puede usar la instrucción **ALTER DATABASE** para crear una base de datos secundaria con replicación geográfica en un servidor asociado. Ejecute esta instrucción en la base de datos maestra del servidor que contiene la base de datos que se va a replicar. La base de datos con replicación geográfica (la "base de datos principal") tendrá el mismo nombre que la base de datos que se replica y, de forma predeterminada, tendrá el mismo nivel de servicio que la base de datos principal. La base de datos secundaria puede ser legible o no legible, y puede ser una base de datos única o elástica. Para obtener más información, consulte [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) y [Niveles de servicio](sql-database-service-tiers.md). Después de crear e inicializar la base de datos secundaria, los datos comenzarán a replicarse de manera asincrónica desde la base de datos principal. Los pasos siguientes describen cómo configurar la replicación geográfica con Management Studio. Se proporcionan pasos para crear bases de datos secundarias legibles y no legibles con una base de datos única o elástica.

> [AZURE.NOTE] Si la base de datos secundaria existe en el servidor asociado especificado (por ejemplo, porque ya existe o existía anteriormente una relación de replicación geográfica), el comando producirá un error.


### Incorporación de una base de datos secundaria no legible (base de datos única)

Use los pasos siguientes para crear una base de datos secundaria no legible como base de datos única.

1. Use la versión 13.0.600.65 o posterior de SQL Server Management Studio.

 	 > [AZURE.IMPORTANT] Descargue la versión [más reciente](https://msdn.microsoft.com/library/mt238290.aspx) de SQL Server Management Studio. Le recomendamos usar siempre la versión más reciente de Management Studio para que pueda estar siempre al día de las últimas actualizaciones del Portal de Azure.


2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y, a continuación, haga clic en **Nueva consulta**.

3. Use la instrucción **ALTER DATABASE** para convertir una base de datos local en una base de datos principal con replicación geográfica que tenga una base de datos secundaria no legible en un servidor MySecondaryServer1 donde MySecondaryServer1 sea el nombre descriptivo del servidor.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);

4. Haga clic en **Ejecutar** para ejecutar la consulta.


### Incorporación de una base de datos secundaria legible (base de datos única)
Use los siguientes pasos para crear una base de datos secundaria legible como base de datos única.

1. En Management Studio, conéctese al servidor lógico de Base de datos SQL de Azure.

2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y, a continuación, haga clic en **Nueva consulta**.

3. Use la instrucción **ALTER DATABASE** para convertir una base de datos local en una base de datos principal con replicación geográfica, que posea una base de datos secundaria legible en un servidor secundario.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);

4. Haga clic en **Ejecutar** para ejecutar la consulta.



### Agregue una base de datos secundaria no legible (base de datos elástica)
Use los pasos siguientes para crear una base de datos secundaria no legible como base de datos elástica.

1. En Management Studio, conéctese al servidor lógico de Base de datos SQL de Azure.

2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y, a continuación, haga clic en **Nueva consulta**.

3. Use la instrucción **ALTER DATABASE** para convertir una base de datos local en una base de datos principal con replicación geográfica, que posea una base de datos secundaria no legible en un servidor secundario de un grupo elástico.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool1));

4. Haga clic en **Ejecutar** para ejecutar la consulta.



### Agregue de una base de datos secundaria legible (base de datos elástica)
Use los siguientes pasos para crear una base de datos secundaria legible como base de datos elástica.

1. En Management Studio, conéctese al servidor lógico de Base de datos SQL de Azure.

2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y, a continuación, haga clic en **Nueva consulta**.

3. Use la instrucción **ALTER DATABASE** para convertir una base de datos local en una base de datos principal con replicación geográfica, que posea una base de datos secundaria legible en un servidor secundario de un grupo elástico.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = ALL
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool2));

4. Haga clic en **Ejecutar** para ejecutar la consulta.



## Elimine una base de datos secundaria

Puede usar la instrucción **ALTER DATABASE** para terminar definitivamente la asociación de replicación entre una base de datos secundaria y su base de datos principal. Esta instrucción se ejecuta en la base de datos maestra en la que reside la base de datos principal. Después de terminarse la relación, la base de datos secundaria se convierte en una base de datos normal de lectura y escritura. Si se interrumpe la conectividad con la base de datos secundaria, el comando se ejecuta correctamente pero la base de datos secundaria será de lectura y escritura después de restaurarse la conectividad. Para obtener más información, consulte [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) y [Niveles de servicio](sql-database-service-tiers.md).

Use los pasos siguientes para quitar la base de datos secundaria con replicación geográfica de una asociación de replicación geográfica.

1. En Management Studio, conéctese al servidor lógico de Base de datos SQL de Azure.

2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y, a continuación, haga clic en **Nueva consulta**.

3. Use la instrucción **ALTER DATABASE** para quitar una base de datos secundaria con replicación geográfica.

        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;

4. Haga clic en **Ejecutar** para ejecutar la consulta.


## Inicio de una conmutación por error planeada que promueve una base de datos secundaria a la nueva principal

Puede usar la instrucción **ALTER DATABASE** para promover una base de datos secundaria para que sea la nueva base de datos principal de forma planeada; de ese modo, se degrada la base de datos principal ya existente y se convierte en secundaria. Esta instrucción se ejecuta en la base de datos maestra en el servidor lógico de Base de datos SQL Azure en el que reside la base de datos secundaria con replicación geográfica que se está promocionando. Esta funcionalidad está diseñada para la conmutación por error planeada, por ejemplo, durante las exploraciones de DR, y requiere que esté disponible la base de datos principal.

El comando ejecuta el siguiente flujo de trabajo:

1. Cambia temporalmente la replicación al modo sincrónico, lo que hace que todas las transacciones pendientes se vacíen en la secundaria y se bloqueen todas las nuevas transacciones.

2. Cambia los roles de las dos bases de datos de la asociación de replicación geográfica.

Esta secuencia garantiza que no se producirá ninguna pérdida de datos. Hay un breve período durante el que ambas bases de datos no están disponibles (del orden de 0 a 25 segundos) mientras se cambian los roles. En circunstancias normales, toda la operación debería tardar menos de un minuto en completarse. Para obtener más información, consulte [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) y [Niveles de servicio](sql-database-service-tiers.md).


> [AZURE.NOTE] Si la base de datos principal no está disponible cuando se emite el comando, este generará un error con un mensaje que indica que el servidor principal no está disponible. En contadas ocasiones, es posible que la operación no pueda completarse y que aparezca detenida. En este caso, el usuario puede ejecutar el comando para forzar la conmutación por error y aceptar la pérdida de datos.

Use los pasos siguientes para iniciar una conmutación por error planeada.

1. En Management Studio, conéctese al servidor lógico de Base de datos SQL de Azure en el que reside una base de datos secundaria con replicación geográfica.

2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y, a continuación, haga clic en **Nueva consulta**.

3. Use la instrucción **ALTER DATABASE** para convertir la base de datos con replicación geográfica en una base de datos principal con replicación geográfica, que posea una base de datos secundaria legible en <MySecondaryServer4> de <ElasticPool2>.

        ALTER DATABASE <MyDB> FAILOVER;

4. Haga clic en **Ejecutar** para ejecutar la consulta.



## Inicio de una conmutación por error no planeada desde la base de datos principal a la base de datos secundaria

Puede usar la instrucción **ALTER DATABASE** para promover una base de datos secundaria para que sea la nueva base de datos principal de forma no planeada, y así forzar la degradación de la base de datos principal ya existente a una base de datos secundaria, en un momento en el que la base de datos principal ya no esté disponible. Esta instrucción se ejecuta en la base de datos maestra en el servidor lógico de Base de datos SQL Azure en el que reside la base de datos secundaria con replicación geográfica que se está promocionando.

Esta funcionalidad está diseñada para situaciones de recuperación ante desastres en los que resulta fundamental restaurar la disponibilidad de la base de datos y la pérdida de algunos datos resulta aceptable. Cuando se invoca la conmutación por error forzada, la base de datos secundaria especificada se convierte inmediatamente en la base de datos principal y comienza a aceptar transacciones de escritura. Tan pronto como la base de datos principal original puede volver a conectar con esta nueva base de datos principal, se realiza una copia de seguridad incremental en la base de datos principal original y la base de datos principal anterior se convierte en una base de datos secundaria para la nueva base de datos principal; por consiguiente, es simplemente una réplica de sincronización de la nueva principal.

Sin embargo, como la restauración a un momento dado no se admite en las bases de datos secundarias, si el usuario desea recuperar datos comprometidos en la base de datos principal anterior que no se habían replicado en la nueva base de datos principal antes de que se produjera la conmutación por error forzosa, el usuario deberá acudir al soporte técnico para recuperar estos datos perdidos.

> [AZURE.NOTE] Si el comando se emite cuando las bases de datos principal y secundaria están en línea, la principal anterior se convertirá en la nueva secundaria pero no se intentará la sincronización de los datos. De modo que es posible que se pierdan algunos datos.


Si la base de datos principal tiene varias bases de datos secundarias, el comando solo funcionará en el servidor secundario en el que se ejecutó. Sin embargo, las otras bases de datos secundarias no sabrán que se produjo la conmutación por error forzosa. El usuario tendrá que reparar manualmente esta configuración mediante una API "quitar secundaria" y luego volver a configurar la replicación geográfica en estas bases de datos secundarias adicionales.

Use los pasos siguientes para quitar a la fuerza la base de datos secundaria con replicación geográfica de una asociación de replicación geográfica.

1. En Management Studio, conéctese al servidor lógico de Base de datos SQL de Azure en el que reside una base de datos secundaria con replicación geográfica.

2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y, a continuación, haga clic en **Nueva consulta**.

3. Use la instrucción **ALTER DATABASE** para convertir <MyLocalDB> en una base de datos principal con replicación geográfica que posea una base de datos secundaria legible en <MySecondaryServer4> de <ElasticPool2>.

        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;

4. Haga clic en **Ejecutar** para ejecutar la consulta.

## Supervisión y mantenimiento de la configuración de la replicación geográfica

Las tareas de supervisión incluyen la supervisión de la configuración de replicación geográfica y la supervisión del mantenimiento de la replicación de los datos. Puede usar la vista de administración dinámica **sys.dm\_geo\_replication\_links** de la base de datos maestra, para devolver información sobre todos los vínculos de replicación existentes de cada base de datos que se encuentre en el servidor lógico de la Base de datos SQL de Azure. Esta vista contiene una fila para cada vínculo de replicación entre bases de datos principales y secundarias. Puede usar la vista de administración dinámica **sys.dm\_replication\_status**, para devolver una fila de cada Base de datos SQL de Azure que participe en un vínculo de replicación. Aquí se incluyen tanto las bases de datos principales como secundarias. Si existe más de un vínculo de replicación continua para una base de datos principal dada, esta tabla contiene una fila para cada una de las relaciones. La vista se crea en todas las bases de datos, incluida la maestra lógica. Sin embargo, al consultar esta vista en la maestra lógica se devuelve un conjunto vacío. Puede usar la vista de administración dinámica **sys.dm\_operation\_status**, para mostrar el estado de todas las operaciones de bases de datos, incluido el estado de los vínculos de replicación. Para obtener más información, consulte [sys.geo\_replication\_links (Base de datos SQL de Azure)](https://msdn.microsoft.com/library/mt575501.aspx), [sys.dm\_geo\_replication\_link\_status (Base de datos SQL de Azure)](https://msdn.microsoft.com/library/mt575504.aspx) y [sys.dm\_operation\_status (Base de datos SQL de Azure)](https://msdn.microsoft.com/library/dn270022.aspx).

Use los pasos siguientes para supervisar una asociación de replicación geográfica.

1. En Management Studio, conéctese al servidor lógico de Base de datos SQL de Azure.

2. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **maestra** y, a continuación, haga clic en **Nueva consulta**.

3. Use la siguiente instrucción para mostrar todas las bases de datos con vínculos de replicación geográfica.

        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;

4. Haga clic en **Ejecutar** para ejecutar la consulta.
5. Abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón derecho en **MyDB** y, a continuación, haga clic en **Nueva consulta**.
6. Use la siguiente instrucción para mostrar los retrasos de replicación y la hora de la última replicación de mis bases de datos secundarias de MyDB.

        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status

7. Haga clic en **Ejecutar** para ejecutar la consulta.
8. Use la siguiente instrucción para mostrar las operaciones de replicación geográfica más recientes asociadas a la base de datos MyDB.

        SELECT * FROM sys.dm_operation_status where major_resource_is = 'MyDB'
        ORDER BY start_time DESC

9. Haga clic en **Ejecutar** para ejecutar la consulta.


## Pasos siguientes

- [Obtención de detalles de la recuperación ante desastres](sql-database-disaster-recovery-drills.md)


## Recursos adicionales

- [Lo más destacado de las nuevas funcionalidades de replicación geográfica](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [Diseño de aplicaciones de nube para la continuidad del negocio mediante replicación geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Documentación de Base de datos SQL](https://azure.microsoft.com/services/sql-database/)

<!---HONumber=AcomDC_0218_2016-->