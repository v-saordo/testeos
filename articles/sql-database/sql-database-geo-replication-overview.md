<properties
	pageTitle="Replicación geográfica activa para Base de datos SQL de Azure"
	description="En este tema se explica Replicación geográfica activa para Base de datos SQL y sus usos."
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
	ms.date="10/21/2015"
	ms.author="jroth" />

# Replicación geográfica activa para Base de datos SQL de Azure

## Información general
La característica Replicación geográfica activa implementa un mecanismo para proporcionar redundancia de base de datos en la misma región de Microsoft Azure o en distintas regiones (redundancia geográfica). Replicación geográfica activa replica de forma asincrónica las transacciones confirmadas desde una base de datos en hasta cuatro copias de la base de datos en servidores diferentes. La base de datos original se convierte en la base de datos principal de la copia continua. Cada copia continua se denomina base de datos secundaria en línea. La base de datos principal replica de forma asincrónica las transacciones confirmadas en cada una de las bases de datos secundarias en línea. Aunque, en un momento dado, los datos secundarios en línea pueden estar algo retrasados respecto a la base de datos principal, se garantiza que los datos secundarios en línea guarden siempre coherencia transaccional con los cambios confirmados en la base de datos principal. Replicación geográfica activa admite hasta cuatro bases de datos secundarias en línea, o hasta tres bases de datos secundarias en línea y una secundaria sin conexión.

Una de las principales ventajas de Replicación geográfica activa es que ofrece una solución de recuperación ante desastres en el nivel de base de datos. Con Replicación geográfica activa, puede configurar una base de datos de usuario en el nivel de servicio Premium para replicar las transacciones en bases de datos ubicadas en diferentes servidores de Base de datos SQL de Microsoft Azure dentro de las mismas regiones o en regiones distintas. La redundancia entre regiones permite que las aplicaciones se recuperen de la pérdida permanente de un centro de datos causada por desastres naturales, errores humanos catastróficos o actos malintencionados.

Otra ventaja clave es que las bases de datos secundarias en línea son legibles. Por lo tanto, una base de datos secundaria en línea puede actuar como equilibrador de carga para cargas de trabajo de lectura tales como informes. Aunque puede crear una base de datos secundaria en línea en una región distinta para usarla en una posible recuperación ante desastres, también podría tener una base de datos secundaria en línea en la misma región en un servidor diferente. Ambas bases de datos secundarias en línea se pueden usar para equilibrar cargas de trabajo de solo lectura para clientes distribuidos entre varias regiones.

Algunos otros escenarios donde se puede usar Replicación geográfica activa son:

- **Migración de base de datos**: puede usar Replicación geográfica activa para migrar una base de datos de un servidor a otro en línea con un tiempo de inactividad mínimo.
- **Actualizaciones de aplicaciones**: puede usar la base de datos secundaria en línea como opción de conmutación por recuperación.

Para lograr una verdadera continuidad empresarial, agregar redundancia entre centros de datos al almacenamiento relacional es solo parte de la solución. Para recuperar una aplicación (un servicio) de un extremo a otro tras un error desastroso, es necesario recuperar todos los componentes que constituyen el servicio y cualquier servicio dependiente. Algunos ejemplos de estos componentes son el software cliente (por ejemplo, un explorador con JavaScript personalizado), los front-end web, el almacenamiento y DNS. Es fundamental que todos los componentes sean resistentes a los mismos errores y que estén disponibles en el plazo del objetivo de tiempo de recuperación (RTO) de la aplicación. Por lo tanto, debe identificar todos los servicios dependientes y comprender las garantías y capacidades que ofrecen. A continuación, debe seguir los pasos adecuados para asegurarse de que el servicio funcione durante la conmutación por error de los servicios de los que depende. Para obtener más información sobre el diseño de soluciones para la recuperación ante desastres, consulte [Diseño de aplicaciones de nube para la continuidad de negocio mediante replicación geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## Funcionalidad de replicación geográfica activa
La característica Replicación geográfica activa ofrece la funcionalidad esencial siguiente:

- **Replicación asincrónica automática**: después de la propagación de una base de datos secundaria en línea, las actualizaciones en la base de datos principal se copian de forma asincrónica a la base de datos secundaria en línea automáticamente. Esto significa que las transacciones se confirman en la base de datos principal antes de que se copien a la base de datos secundaria en línea. Sin embargo, después de la propagación, la base de datos secundaria en línea guarda coherencia transaccional en cualquier momento dado. 

	>[AZURE.NOTE] La replicación asincrónica permite la latencia que tipifica a las redes de área extensa mediante las que los centros de datos remotos están conectados.

- **Varias bases de datos secundarias en línea**: dos o más bases de datos secundarias en línea aumentan la redundancia y la protección de la base de datos principal y la aplicación. Si existen varias bases de datos secundarias en línea, la aplicación seguirá estando protegida incluso si se produce un error en una de ellas. Si solo hay una base de datos secundaria en línea y se produce un error, la aplicación está expuesta a un mayor riesgo hasta que se crea una nueva base de datos secundaria en línea.

- **Bases de datos secundarias en línea legibles**: una aplicación puede acceder a una base de datos secundaria en línea para las operaciones de solo lectura con las mismas entidades de seguridad que las usadas para acceder a la base de datos principal. Las operaciones de copia continua en la base de datos secundaria en línea tienen prioridad sobre el acceso a la aplicación. Además, si las consultas en la base de datos secundaria en línea prolongan el bloqueo de las tablas, las transacciones podrían terminar por producir un error en la base de datos principal.

- **Finalización controlada por el usuario para conmutación por error**: antes de poder conmutar una aplicación por error a una base de datos secundaria en línea, debe finalizar la relación de copia continua con la base de datos principal. Para finalizar la relación de copia continua, es necesario que la aplicación o un script administrativo lleven a cabo una acción explícita, o bien que se realice manualmente a través del portal. Después de la finalización, la base de datos secundaria en línea se convierte en una base de datos independiente. Pasa a ser una base de datos de lectura y escritura a menos que la base de datos principal sea una base de datos de solo lectura. Se describen dos formas de [finalización de una relación de copia continua](#termination-of-a-continuous-copy-relationship) más adelante en este tema.

>[AZURE.NOTE] Solo se admite Replicación geográfica activa para las bases de datos del nivel de servicio Premium. Esto se aplica tanto a la base de datos principal como a las secundarias en línea. Se debe configurar la base de datos secundaria en línea para que tenga un nivel de rendimiento igual o mayor que el de la principal. Los cambios en los niveles de rendimiento de la base de datos principal no se replican automáticamente en las bases de datos secundarias. Todas las actualizaciones se deben realizar en las bases de datos secundarias en primer lugar y, por último, en la base de datos principal. Para obtener más información sobre el cambio de nivel de rendimiento, consulte [Cambio del nivel de servicio y del nivel de rendimiento (nivel de precios) de una base de datos SQL](sql-database-scale-up.md). Existen dos razones principales por las que la base de datos secundaria en línea debería ser como mínimo del mismo tamaño que la base de datos principal. La base de datos secundaria debe tener capacidad suficiente para procesar las transacciones replicadas a la misma velocidad que la principal. Si la base de datos secundaria no tiene, como mínimo, la misma capacidad para procesar las transacciones entrantes, se podría retrasar y terminar por afectar a la disponibilidad de la principal. Si la secundaria no tiene la misma capacidad que la principal, la conmutación por error podría degradar el rendimiento y la disponibilidad de la aplicación.

## Conceptos de la relación de copia continua
La redundancia de datos local y la recuperación operativa son características estándar de Base de datos SQL de Azure. Cada base de datos posee una base de datos principal y dos bases de datos de réplica locales que residen en el mismo centro de datos, lo que proporciona gran disponibilidad dentro de ese centro de datos. Esto significa que las bases de datos de Replicación geográfica activa también cuentan con réplicas redundantes. Tanto la base de datos principal como las secundarias en línea poseen dos réplicas secundarias. Sin embargo, la réplica principal de la base de datos secundaria se actualiza directamente mediante el mecanismo de copia continua y no acepta actualizaciones iniciadas por las aplicaciones. En la ilustración siguiente, se muestra cómo Replicación geográfica activa amplía la redundancia de base de datos en dos regiones de Azure. La región que hospeda la base de datos principal se conoce como región primaria. La región que hospeda la base de datos secundaria en línea se conoce como región secundaria. En esta ilustración, Europa del Norte es la región primaria. Europa Occidental es la región secundaria.

![Relación de copia continua](./media/sql-database-active-geo-replication/continuous-copy-relationships.gif)

Si la base de datos principal deja de estar disponible, al finalizar la relación de copia continua para una determinada base de datos secundaria en línea, esta se convierte en una base de datos independiente. La base de datos secundaria en línea hereda el modo de solo lectura o de lectura y escritura de la base de datos principal, que no cambia debido a la finalización. Por ejemplo, si la base de datos principal es de solo lectura, después de la finalización la base de datos secundaria en línea se convierte en una base de datos de solo lectura. En este punto, la aplicación puede conmutar por error y seguir usando la base de datos secundaria en línea. Para proporcionar resistencia en caso de error catastrófico del centro de datos o una interrupción prolongada en la región primaria, es necesario que al menos una base de datos secundaria en línea resida en una región distinta.

## Creación de una copia continua
Solo se puede crear una copia continua de una base de datos existente. La creación de una copia continua de una base de datos existente es útil para agregar redundancia geográfica. También se puede crear una copia continua para copiar una base de datos existente a un servidor diferente de Base de datos SQL de Azure. Una vez creada, la base de datos secundaria se rellena con los datos copiados de la base de datos principal. Este proceso se conoce como propagación. Una vez completada la propagación, se replica cada nueva transacción después de que se confirme en la base de datos principal.

Para obtener información sobre cómo crear una copia continua de una base de datos existente, consulte [Cómo habilitar la replicación geográfica](sql-database-business-continuity-design.md#how-to-enable-geo-replication).

## Evitar la pérdida de datos críticos
Debido a la elevada latencia de las redes de área extensa, la copia continua usa un mecanismo de replicación asincrónica. Esto hace inevitable cierta pérdida de datos si se produce un error. Sin embargo, es posible que algunas aplicaciones exijan que no se pierdan datos. Para proteger estas actualizaciones críticas, un desarrollador de aplicaciones puede llamar al procedimiento del sistema [sp\_wait\_for\_database\_copy\_sync](https://msdn.microsoft.com/library/dn467644.aspx) inmediatamente después de confirmar la transacción. La llamada a **sp\_wait\_for\_database\_copy\_sync** bloquea el subproceso de llamada hasta que se replique la última transacción confirmada en la base de datos secundaria en línea. El procedimiento esperará hasta que la base de datos secundaria en línea confirme todas las transacciones en cola. El ámbito de **sp\_wait\_for\_database\_copy\_sync** es un vínculo de copia continua específico. Cualquier usuario con derechos de conexión para la base de datos principal puede llamar a este procedimiento.

>[AZURE.NOTE] El retraso causado por una llamada al procedimiento **sp\_wait\_for\_database\_copy\_sync** puede ser considerable. El retraso depende de la longitud de la cola y el ancho de banda disponible. Evite llamar a este procedimiento a menos que sea absolutamente necesario.

## Finalización de una relación de copia continua
La relación de copia continua se puede finalizar en cualquier momento. Al finalizarse una relación de copia continua, no se elimina la base de datos secundaria. Existen dos métodos para finalizar las relaciones de copia continua:

- **Finalización planeada**: útil para las operaciones planeadas en las que la pérdida de datos es inaceptable. Una finalización planeada solo puede realizarse en la base de datos principal, después de propagarse la base de datos secundaria en línea. En una finalización planeada, todas las transacciones confirmadas en la base de datos principal se replican en la base de datos secundaria en línea en primer lugar y después se finaliza la relación de copia continua. Esto impide la pérdida de datos en la base de datos secundaria.
- **Finalización no planeada (forzada)**: pensada para responder ante la pérdida de la base de datos principal o una de sus bases de datos secundarias en línea. Se puede realizar una finalización forzada en la base de datos principal o en la base de datos secundaria. La finalización forzada siempre causa la pérdida irreversible de la relación de replicación entre la base de datos principal y la base de datos secundaria en línea asociada. Además, la finalización forzada causa la pérdida de las transacciones que no se replicaran desde la base de datos principal. La finalización forzada finaliza inmediatamente la relación de copia continua. Las transacciones en curso no se replican en la base de datos secundaria en línea. Por tanto, la finalización forzada ocasiona una pérdida de las transacciones que no se replicaran desde la base de datos principal.

>[AZURE.NOTE] Si la base de datos principal solo tiene una relación de copia continua, tras la finalización las actualizaciones de la base de datos principal dejarán de estar protegidas.

Para obtener más información acerca de cómo finalizar una relación de copia continua, consulte [Recuperación de una base de datos SQL de Azure tras una interrupción](sql-database-disaster-recovery.md).

## Pasos siguientes
Para obtener más información sobre Replicación geográfica activa y otras características de continuidad empresarial de Base de datos SQL, consulte [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md).

<!---HONumber=AcomDC_0121_2016-->
