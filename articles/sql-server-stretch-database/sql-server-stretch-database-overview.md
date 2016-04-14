<properties
	pageTitle="Información general sobre Stretch Database | Microsoft Azure"
	description="Sepa como Stretch Database migra los datos históricos de forma transparente y segura a la nube de Microsoft Azure."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglasl"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Información general de Stretch Database

Stretch Database migra los datos históricos de forma transparente y segura a la nube de Microsoft Azure.

## ¿Cuáles son las ventajas de Stretch Database?
Stretch Database ofrece las siguientes ventajas:

**Proporciona disponibilidad rentable para datos inactivos**. Stretch Database activa y desactiva datos transaccionales dinámicamente desde SQL Server en Microsoft Azure mediante SQL Server Stretch Database. A diferencia del almacenamiento habitual de datos inactivos, los datos siempre están en línea y disponibles para la consulta. Puede proporcionar escalas de tiempo de retención de datos más largas sin romper el banco de tablas grandes como la del Historial de pedidos del cliente. Se beneficia del bajo costo de Azure en lugar de escalar un almacenamiento local costoso. Le permite elegir el plan de tarifa y configurar opciones en el Portal de Azure para mantener el control sobre el precio y la velocidad de acceso a los datos. Escalado o reducción vertical según sea necesario. Visite la página de [precios de Base de datos SQL](https://azure.microsoft.com/pricing/details/sql-database/) para conocer los detalles.

**No requiere cambios en las consultas o aplicaciones**. Acceda a los datos de SQL Server sin problemas independientemente de que esté trabajando de forma local o en la nube. Establezca la directiva que determina dónde se almacenan los datos y SQL Server controlará el movimiento de datos en segundo plano. La tabla completa siempre está en línea y lista para consultarla. Stretch Database no requiere ningún cambio en las consultas existentes ni en las aplicaciones y la ubicación de los datos es completamente transparente para la aplicación.

**Simplifica el mantenimiento de datos local**. Reduzca el mantenimiento y almacenamiento local de los datos. Se ejecutan automáticamente copias de seguridad de la parte de la nube que contiene los datos. Las copias de seguridad de los datos locales se ejecutan más rápidamente y terminan dentro de la ventana de tiempo de mantenimiento. Se reducen significativamente sus necesidades de almacenamiento local. Almacenamiento de Azure puede ser un 80% más barato que agregar a SSD local.

**Mantiene los datos seguros incluso durante la migración**.Disfrute de la tranquilidad de ajustar las aplicaciones importantes de forma segura a la nube. Always Encrypted de SQL Server proporciona cifrado para los datos en movimiento. La seguridad de nivel de fila (RLS) y otras características avanzadas de seguridad de SQL Server también funcionan junto con Stretch Database para proteger los datos.

## ¿Qué hace Stretch Database?
Después de habilitar Stretch Database para una instancia de SQL Server, una base de datos y al menos una tabla, empieza a migrar en modo silencioso los datos históricos a Azure.

-   Si almacena los datos históricos en una tabla independiente, puede migrar toda la tabla.

-   Si la tabla contiene datos históricos y actuales, puede especificar un predicado de filtro para seleccionar las filas que desea migrar.

Stretch Database garantiza que no se perderán datos si se produce un error durante la migración. También tiene una lógica de reintento para tratar los problemas de conexión que se pueden producir durante la migración. Una vista de administración dinámica proporciona el estado de la migración.

Puede pausar la migración de datos para solucionar problemas en el servidor local o para maximizar el ancho de banda de red disponible.

No tiene que cambiar las consultas ni las aplicaciones cliente existentes. Sigue teniendo acceso sin interrupción a los datos locales y remotos, incluso durante la migración de datos. Hay una pequeña cantidad de latencia para las consultas remotas, pero solo encuentra esta latencia al consultar los datos históricos.

![Información general de Stretch Database][StretchOverviewImage1]

## ¿Es Stretch Database útil para usted?
Si reúne los siguientes requisitos, Stretch Database puede ayudarle a satisfacer sus necesidades y solucionar los problemas.

|Si es la persona que toma las decisiones|Si es una persona jurídica|
|------------------------------|-------------------|
|Debo mantener los datos transaccionales durante mucho tiempo.|El tamaño de las tablas empieza a estar fuera de control.|
|En ocasiones, debo consultar los datos históricos.|Mis usuarios afirman que quieren acceder a los datos históricos, pero lo hacen solo en raras ocasiones.|
|Tengo aplicaciones, incluidas aplicaciones anteriores, que no quiero actualizar.|Tengo que seguir comprando y agregando más espacio de almacenamiento.|
|Quiero encontrar una manera de ahorrar costos en almacenamiento.|No puedo realizar la copia de seguridad o restaurar tablas tan grandes con el contrato de nivel de servicio actual.|

## ¿Qué tipos de bases de datos y tablas son candidatas a Stretch Database?
Stretch Database está pensado para bases de datos transaccionales con grandes cantidades de datos históricos que normalmente están almacenados en un pequeño número de tablas. Estas tablas pueden contener más de mil millones de filas.

Si utiliza la característica de tabla temporal de SQL Server 2016, use Stretch Database para migrar toda la tabla histórica asociada o parte de ella a un almacenamiento rentable en Azure. Para más información, consulte [Manage Retention of Historical Data in System-Versioned Temporal Tables (Administración de la retención de datos históricos en tablas temporales con versión del sistema)](https://msdn.microsoft.com/library/mt637341.aspx).

Use Stretch Database Advisor, una característica del Asesor de actualizaciones de SQL Server 2016, para identificar las bases de datos y tablas para Stretch Database. Para más información, consulte [Identificación de bases de datos y tablas para Stretch Database](sql-server-stretch-database-identify-databases.md). Para más información acerca de posibles problemas de bloqueo, consulte [Limitaciones de área expuesta y problemas de bloqueo de Stretch Database](sql-server-stretch-database-limitations.md).

## <a name="FAQ"></a>Preguntas más frecuentes sobre Stretch Database
**¿Funciona Stretch Database con &lt;Nombre de la característica de SQL Server&gt;?**
-   Para obtener una lista de características de SQL Server que hacen ilegible una tabla para Stretch Database, consulte [Limitaciones de área expuesta y problemas de bloqueo de Stretch Database](sql-server-stretch-database-limitations.md).

-   Para identificar las bases de datos y las tablas que son candidatas a Stretch Database, descargue el Asesor de actualizaciones de SQL Server 2016 y ejecute Stretch Database Advisor. Para más información, consulte [Identificación de bases de datos y tablas para Stretch Database](sql-server-stretch-database-identify-databases.md).

**¿Puedo destinar otra instancia local de SQL Server para Stretch Database?** No. Stretch Database no admite otra instancia de SQL Server local como el punto de conexión remoto.

**¿Puedo deshabilitar Stretch Database y devolver los datos migrados a la tabla local?** Sí. Para más información, consulte [Deshabilitación de Stretch Database y devolución de datos remotos](sql-server-stretch-database-disable.md).

## Términos
**Base de datos local**. La base de datos de SQL Server local

**Punto de conexión remoto**. La ubicación de Microsoft Azure que contiene los datos remotos de la base de datos.

**Datos locales**. Los datos de una base de datos con Stretch Database habilitado que no se moverán a Azure en función de la configuración de las tablas de la base de datos definida para Stretch Database.

**Datos elegibles**. Los datos de una base de datos con Stretch Database habilitado que aún no se han movido, pero se moverán a Azure en función de la configuración de las tablas de la base de datos definida para Stretch Database.

**Datos remotos**. Los datos de una base de datos con Stretch Database habilitado que ya se han movido a Azure.

## Arquitectura
Stretch Database aprovecha los recursos de Microsoft Azure para descargar el almacenamiento de datos de archivo y el procesamiento de consultas.

Cuando habilita Stretch Database en una base de datos, crea una definición de servidor vinculada con seguridad en el servidor SQL Server local. Esta definición de servidor vinculada tiene el punto de conexión remoto como destino. Cuando habilita Stretch Database en una tabla en la base de datos, este aprovisiona recursos remotos y empieza a migrar datos elegibles si está habilitada la migración.

Se ejecutan automáticamente consultas en tablas con Stretch Database habilitado en la base de datos local y en el punto de conexión remoto. Stretch Database aprovecha la capacidad de procesamiento de Azure para ejecutar consultas en datos remotos mediante la reescritura de la consulta. Puede ver esta reescritura como un operador de "consulta remota" en el nuevo plan de consulta.

![Arquitectura de Stretch Database][StretchOverviewImage2]

## Seguridad y permisos

### Habilitación y deshabilitación de Stretch Database para una instancia de SQL Server
Para empezar a configurar las bases de datos de Stretch Database, primero debe cambiar la opción de configuración de nivel de instancia de "archivo de datos remotos" mediante sp\_configure. Esta operación requiere privilegios sysadmin o serveradmin. Con esta opción habilitada, puede configurar las bases de datos de Stretch Database, migrar datos y consultar los datos en el punto de conexión remoto.

### Habilitación y deshabilitación de Stretch Database para una tabla o una base de datos
Para configurar una base de datos de Stretch Database, debe tener el permiso CONTROL DATABASE. Además, tendrá que proporcionar credenciales de administrador para el punto de conexión remoto.

Para configurar una tabla de Stretch Database, debe tener el privilegio ALTER sobre la tabla y la base de datos debe estar ya configurada para Stretch Database.

### Acceso a datos
Solo los procesos del sistema pueden tener acceso a la definición de servidor vinculada subyacente a Stretch Database. Los inicios de sesión de los usuarios no pueden emitir consultas a través de la definición de servidor vinculada al punto de conexión remoto.

Stretch Database no cambia el modelo de permisos de una base de datos ya existente. Los inicios de sesión de los usuarios pueden consultar los datos de una tabla con Stretch Database habilitado a través de la base de datos local. La base de datos local realiza comprobaciones de permisos para todas las acciones iniciadas por el usuario de la misma manera que lo hace para los demás objetos. Si está autorizado para acceder a la tabla con Stretch Database habilitado, tendrá acceso a todo su contenido, para el que está autorizado sin importar dónde residan físicamente los datos.

![Modelo de acceso a datos de Stretch Database][StretchOverviewImage3]

## Comprobación de Stretch Database
**Pruebe Stretch Database con la base de datos de ejemplo de AdventureWorks.** Para obtener la base de datos de ejemplo de AdventureWorks, descargue al menos el archivo de base de datos y el archivo de ejemplos y scripts de [aquí](https://www.microsoft.com/download/details.aspx?id=49502). Después de restaurar la base de datos de ejemplo en una instancia de SQL Server 2016, descomprima el archivo de ejemplos y abra el archivo de ejemplos de Stretch Database desde la carpeta correspondiente. Ejecute los scripts en este archivo para comprobar el espacio utilizado por los datos antes y después de habilitar Stretch Database, para realizar un seguimiento del progreso de la migración de datos y para confirmar que aún puede consultar los datos existentes e insertar nuevos datos durante la migración y después de la misma.

## Paso siguiente
**Identificación de las bases de datos y las tablas que son candidatas a Stretch Database.** Para identificar las bases de datos y las tablas que son candidatas a Stretch Database, descargue el Asesor de actualizaciones de SQL Server 2016 y ejecute Stretch Database Advisor. Stretch Database Advisor también identifica problemas de bloqueo. Para más información, consulte [Identificación de bases de datos y tablas para Stretch Database](sql-server-stretch-database-identify-databases.md).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png

<!---HONumber=AcomDC_0302_2016-->