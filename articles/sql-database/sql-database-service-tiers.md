<properties
	pageTitle="Rendimiento de la Base de datos SQL y opciones: niveles de servicio | Microsoft Azure"
	description="Compare las características de rendimiento y de continuidad del negocio de Base de datos SQL de los niveles de servicio para encontrar el equilibrio adecuado entre costo y funcionalidad a medida que escalan."
	keywords="opciones de base de datos, rendimiento de la base de datos"
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor="jeffreyg"/>

<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="02/17/2016"
	ms.author="jeffreyg"/>

# Opciones y rendimiento de Base de datos SQL: comprender lo que está disponible en cada nivel de servicio

[Base de datos SQL de Azure](sql-database-technical-overview.md) proporciona varios niveles de servicio para administrar los diferentes tipos de cargas de trabajo. Puede cambiar los niveles de servicio en cualquier momento sin tiempo de inactividad para la aplicación. También, puede [crear una Base de datos única](sql-database-get-started.md) con características y precios definidos. o bien puede administrar varias bases de datos mediante la [creación de un grupo de bases de datos elásticas](sql-database-elastic-pool-portal.md). En ambos casos, los niveles son: **Basic**, **Standard** y **Premium**. Pero las opciones de la base de datos de estos niveles varían en función de si se va a crear una base de datos individual o una base de datos dentro de un grupo de bases de datos elásticas. Este artículo proporciona más detalles acerca de los niveles de servicio en ambos contextos.

## Niveles de servicio y opciones de la base de datos
Los niveles de servicio Basic, Standard y Premium tienen un SLAde tiempo de actividad del 99,99% y ofrecen un rendimiento predecible, opciones de continuidad del negocio flexibles, características de seguridad y facturación por hora. La tabla siguiente proporciona ejemplos de los niveles más adecuados para las cargas de trabajo de las diferentes aplicaciones.

| Nivel de servicio | Cargas de trabajo de destino |
|---|---|
| **Básica** | Ideal para bases de datos pequeñas, que suelen admitir una sola operación activa en un momento dado. Entre los ejemplos se incluyen las bases de datos utilizadas para desarrollo, pruebas o aplicaciones a pequeña escala que se emplean con poca frecuencia. |
| **Standard** | La opción predilecta para la mayoría de aplicaciones en la nube, admite varias consultas simultáneas. Entre los ejemplos se incluyen aplicaciones web o para grupos de trabajo. |
| **Premium** | Diseñado para altos volúmenes de transacciones, admite un gran número de usuarios simultáneos y requiere el máximo nivel de funcionalidades de continuidad empresarial. Entre los ejemplos se incluyen bases de datos que admiten aplicaciones críticas. |

>[AZURE.NOTE] Se han retirado las versiones Web y Business Edition Si quiere seguir utilizando las versiones Web y Business Edition, lea las [P+F de Sunset](https://azure.microsoft.com/pricing/details/sql-database/web-business/).

### Niveles de servicio de la Base de datos única y niveles de rendimiento
Para las bases de datos únicas hay varios niveles de rendimiento dentro de cada nivel de servicio, tiene la flexibilidad de elegir el nivel que mejor se adapte a las exigencias de la carga de trabajo. Si necesita escalar vertical u horizontalmente, puede cambiar fácilmente los niveles de la base de datos **sin tiempo de inactividad para la aplicación.** Para obtener información más detallada, consulte [Modificación de niveles de servicio y de rendimiento de la base de datos](sql-database-scale-up.md).

Las características de rendimiento que se enumeran aquí se aplican a las bases de datos creadas con la [Base de datos SQL V12](sql-database-v12-whats-new.md). En aquellas situaciones donde el hardware subyacente de Azure hospeda varias Bases de datos SQL, la base de datos seguirá recibiendo un conjunto garantizado de recursos y las características de rendimiento esperado de la base de datos individual no se ven afectadas.

[AZURE.INCLUDE [Tabla de niveles de servicio de datos de la Base de datos SQL](../../includes/sql-database-service-tiers-table.md)]


Para comprender mejor las DTU, consulte la [sección DTU](#understanding-dtus) de este tema.

>[AZURE.NOTE] Para obtener una explicación detallada de todas las demás filas de esta tabla de niveles de servicio, consulte [Límites y capacidades de nivel de servicio](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).

### Niveles de servicio del grupo de bases de datos elásticas y rendimiento en las eDTU
Además de crear y escalar una base de datos única, también es posible administrar varias bases de datos dentro de un [grupo de bases de datos elásticas](sql-database-elastic-pool.md). Todas las bases de datos de un grupo de bases de datos elásticas comparten un conjunto común de recursos. Las características de rendimiento se miden por *Unidades de transmisión de bases de datos elásticas* (eDTU). Como ocurre con las bases de datos únicas, los grupos de bases de datos elásticas tienen tres niveles de servicio: **Básico**, **Estándar** y **Premium**. En el caso de las bases de datos elásticas, estos tres niveles también definen los límites de rendimiento global y varias características.

Los grupos de bases de datos elásticas permiten que estas bases de datos compartan y consuman recursos de DTU sin que sea preciso de asignar un nivel de rendimiento específicos a las bases de datos del grupo. Por ejemplo, una base de datos de un grupo Standard puede usar desde 0 eDTU al número máximo de eDTU de la base de datos (ya sean los 100 eDTU definidos por el nivel de servicio o un número personalizado que se configure). Esto permite que varias bases de datos con diferentes cargas de trabajo usen de forma eficaz los recursos de eDTU disponibles para todo el grupo.

En la tabla siguiente se describen las características de los niveles de servicio de los grupos de bases de datos elásticas. Consulte [Referencia de grupos de bases de datos elásticas de Base de datos SQL](sql-database-elastic-pool-reference.md) para más información sobre las definiciones y más detalles.

[AZURE.INCLUDE [Tabla de niveles de servicio de Base de datos SQL para bases de datos elásticas](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Todas las bases de datos de un grupo también se ajustan a las características de base de datos única de ese nivel. Por ejemplo, el grupo Basic tiene un límite de número máximo de sesiones por grupo de 2400 – 28800, pero una base de datos individual de dicho grupo tiene un límite de base de datos de 300 sesiones (el límite de una base de datos Basic especificado en la sección anterior).

### Descripción de las DTU

[AZURE.INCLUDE [Descripción de DTU de base de datos SQL](../../includes/sql-database-understanding-dtus.md)]

## Supervisión del rendimiento de la base de datos
La supervisión del rendimiento de una base de datos SQL comienza con la supervisión del uso de recursos, en relación con el nivel de rendimiento elegido para la base de datos. Estos datos relevantes se exponen de las siguientes formas:

1.	El Portal de Azure.

2.	En las vistas de administración dinámica de la base de datos de usuario y en la base de datos maestra del servidor que contiene la base de datos de usuario.

En el [Portal de Azure](https://portal.azure.com/), puede supervisar la utilización de una base de datos única; para ello, debe seleccionar la base de datos y hacer clic en el gráfico **Supervisión**. Al hacer esto, se abrirá la ventana **Métrica** que se puede cambiar haciendo clic en el botón **Editar gráfico**. Agregue las siguientes métricas:

- Porcentaje de CPU
- Porcentaje de DTU
- Porcentaje de E/S de datos
- Porcentaje de almacenamiento

Una vez agregadas estas métricas, podrá verlas en el gráfico **Supervisión**; si desea ver más detalles, podrá hacerlo en la ventana **Métrica**. Las cuatro métricas muestran el porcentaje de uso medio, en relación con la **DTU** de la base de datos.

![Supervisión del nivel de servicio del rendimiento de la base de datos.](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

También se pueden configurar alertas en las métricas de rendimiento. Haga clic en el botón **Agregar alerta** de la ventana **Métrica**. Siga el asistente para configurar la alerta. Tiene la opción de que se genere una alerta si la métrica supera un umbral determinado, o si no llega a él.

Por ejemplo, si espera que crezca la carga de trabajo de una base de datos, puede configurar una alerta por correo electrónico cada vez que la base de datos alcance el 80% en cualquiera de las métricas de rendimiento. Esto se puede usar como advertencia prematura para saber cuándo puede tener que cambiar al nivel de rendimiento inmediatamente superior.

Las métricas de rendimiento también pueden ayudarle a determinar si puede cambiar a un nivel de rendimiento inferior. Suponga que usa una base de datos Estándar S2 y todas las métricas de rendimiento muestran que, de media, la base de datos no usa más del 10% en ningún momento. Es probable que la base de datos funcione bien en Estándar S1. Sin embargo, tenga en cuenta las cargas de trabajo que tienen picos o fluctúan antes de tomar la decisión de cambiar a un nivel de rendimiento inferior.

Las mismas métricas que se exponen en el portal están también disponibles a través de las vistas del sistema: [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) en la base de datos **maestra** lógica del servidor y [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) en la base de datos de usuario (**sys.dm\_db\_resource\_stats** se crea en cada base de datos de usuario Basic, Standard y Premium. Las bases de datos Web y Business Edition devuelven un conjunto de resultados vacío). Use **sys.resource\_stats** si necesita supervisar datos menos pormenorizados en un periodo de tiempo más amplio. Use **sys.dm\_db\_resource\_stats** si necesita supervisar datos más pormenorizados en un período de tiempo más breve. Para obtener más información, consulte la [Guía de rendimiento de la Base de datos SQL de Azure](sql-database-performance-guidance.md#monitoring-resource-use-with-sysresourcestats).

En los grupos de bases de datos elásticas, puede supervisar las bases de datos individuales del grupo con las técnicas descritas en esta sección. Sin embargo, también puede supervisar el grupo en conjunto. Para obtener información, consulte el artículo [Supervisión y administración de un grupo de bases de datos elásticas](sql-database-elastic-pool-portal.md#monitor-and-manage-an-elastic-database-pool).

## Pasos siguientes
Para obtener más información sobre los precios de estos niveles, consulte [Precios de Base de datos SQL](https://azure.microsoft.com/pricing/details/sql-database/).

Si desea administrar varias bases de datos como un grupo, consulte [Grupos de bases de datos elásticas](sql-database-elastic-pool-guidance.md) y el artículo asociado [Consideraciones de precio y rendimiento para un grupo de bases de datos elásticas](sql-database-elastic-pool-guidance.md).

Ahora que conoce los niveles de Base de datos SQL, pruébelos con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/) y aprenda a [crear su primera base de datos SQL](sql-database-get-started.md).

<!---HONumber=AcomDC_0218_2016-->