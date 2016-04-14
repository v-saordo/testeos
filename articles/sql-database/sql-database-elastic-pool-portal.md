<properties
	pageTitle="Creación de grupos de bases de datos elásticas escalables | Microsoft Azure"
	description="Cómo agregar un grupo de bases de datos elásticas escalables a la configuración de la Base de datos SQL para una administración y un uso compartido de los recursos más sencillos entre varias bases de datos."
	keywords="base de datos escalable,configuración de base de datos"
	services="sql-database"
	documentationCenter=""
	authors="sidneyh"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/12/2016"
	ms.author="sidneyh"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# Creación de un grupo de bases de datos elásticas escalables para Bases de datos SQL en el Portal de Azure

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-elastic-pool-csharp.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

En este artículo se muestra cómo crear un [grupo de bases de datos elásticas](sql-database-elastic-pool.md) escalable con el Portal de Azure. Una configuración de Base de datos SQL con grupos de base de datos elásticas simplifica la administración y el uso compartido de los recursos entre varias bases de datos.

> [AZURE.NOTE] Los grupos de bases de datos elásticas están actualmente en vista previa y solo estarán disponibles en servidores con bases de datos SQL V12. Si tiene un servidor de Base de datos SQL V11, puede [usar PowerShell para actualizar a V12 y crear un grupo](sql-database-upgrade-server-powershell.md) en un solo paso.


## Requisitos previos

* Una base de datos en un servidor de Base de datos SQL V12. Si no la tiene, consulte [Creación de la primera Base de datos SQL de Azure](sql-database-get-started.md) para crear una en menos de cinco minutos. 
* O, si ya tiene un servidor de Base de datos SQL V11, puede [actualizar a V12 en el Portal](sql-database-v12-plan-prepare-upgrade.md) y, después, vuelva y siga estas instrucciones para crear un grupo.


## Paso 1: Crear un nuevo grupo

Cree un grupo de bases de datos elásticas agregando un nuevo grupo a un servidor. Puede agregar varios grupos a un servidor, pero no puede agregar bases de datos de servidores diferentes al mismo grupo.


1. En el [Portal de Azure](http://portal.azure.com/), haga clic en **Servidores SQL Server**.
2. Seleccione el servidor que contenga las bases de datos que desea agregar al grupo.
3. Haga clic en **Nuevo grupo** (o si ve un mensaje que indica que hay un grupo recomendado, haga clic en él para revisar y crear fácilmente un grupo optimizado para las bases de datos del servidor). A continuación encontrará más información acerca de las recomendaciones.


    ![Adición de un grupo a un servidor](./media/sql-database-elastic-pool-portal/new-pool.png)


4. En la hoja **Grupo de bases de datos elásticas** puede dejar el nombre predeterminado o escribir un nombre para el nuevo grupo.

    ![Configuración de grupos elásticos](./media/sql-database-elastic-pool-portal/configure-elastic-pool.png)


### Grupos de bases de datos elásticas recomendados

Vaya a un servidor de Base de datos SQL y podrá ver un mensaje que indica que existen grupos de bases de datos elásticas recomendados para el servidor (solo la versión 12).

### ¿Por qué recibo una recomendación?

El servicio Base de datos SQL evalúa el historial de uso y recomienda uno o varios grupos de bases de datos elásticas cuando sea más económico que usar bases de datos únicas.

![grupo recomendado](./media/sql-database-elastic-pool-portal/recommended-pool.png)

Cada recomendación se configura con un subconjunto único de las bases de datos del servidor que mejor se ajustan al grupo.

Todas las recomendaciones del grupo contienen:

- Nivel de precios del grupo (Basic, Standard o Premium).
- Cantidad adecuada de unidades eDTU del grupo.
- La configuración de eDTU mín./máx. de las bases de datos elásticas.
- Lista de bases de datos recomendadas.

El servicio tiene en cuenta los últimos 30 días de telemetría al recomendar grupos de bases de datos elásticas. Para que una base de datos se considere candidata para un grupo de bases de datos elásticas, debe tener una existencia mínima de siete días. Las bases de datos que ya están en un grupo de bases de datos elásticas no se consideran candidatas para las recomendaciones de grupos de bases de datos elásticas.

El servicio evalúa las necesidades de recursos y la rentabilidad de mover las bases de datos únicas de cada nivel de servicio a grupos de bases de datos elásticas del mismo nivel. Por ejemplo, se evalúan todas las bases de datos Standard en un servidor para que quepan en un bloque de bases de datos elásticas Standard. Esto significa que el servicio no hace recomendaciones entre niveles como, por ejemplo, mover una base de datos Standard a un grupo Premium.


### Creación de un grupo recomendado

1. Haga clic en el mensaje para ver una lista de los grupos recomendados.
1. Haga clic en un grupo para ver los detalles de la recomendación.
2. Revise toda la configuración de la recomendación y acéptela tal como está, o bien edite la recomendación de modo que se ajuste a sus necesidades empresariales específicas.


## Paso 2: Elegir un plan de tarifa

El plan de tarifa del grupo determina las características disponibles para las bases de datos elásticas del grupo, además de la cantidad máxima de eDTU (eDTU MÁX.) y el almacenamiento (GB) disponibles para cada base de datos. Si desea obtener detalles, consulte [Niveles de servicio](sql-database-service-tiers.md#Service-tiers-for-elastic-database-pools).

>[AZURE.NOTE] Actualmente en la vista previa, no se puede cambiar el nivel de precios de un grupo de bases de datos elásticas una vez creado. Para cambiar el nivel de precios de un grupo de bases de datos elásticas existente, cree un nuevo grupo de bases de datos elásticas en el nivel de precios que quiera y migre las bases de datos elásticas al nuevo grupo.

   ![Elija un plan de tarifa.](./media/sql-database-elastic-pool-portal/pricing-tier.png)

### Configuración del grupo

Después de establecer el plan de tarifa, haga clic en Configurar grupo donde agregar bases de datos, establezca las EDTU y el almacenamiento (GB del grupo) del grupo y el lugar en que se establecen las EDTU mínima y máxima para las bases de datos elásticas del grupo.

## Paso 3: Agregar bases de datos al grupo

Puede agregar o quitar bases de datos de un grupo en cualquier momento.

1. Durante la creación del grupo, haga clic en **Agregar bases de datos** en la hoja **Configurar grupo**.
2. Seleccione las bases de datos que desea agregar al grupo:

    ![Adición de bases de datos](./media/sql-database-elastic-pool-portal/add-databases.png)

    Cuando selecciona una base de datos para agregarla a un grupo, deben cumplirse las condiciones siguientes:

    - El grupo debe tener espacio para la base de datos (no puede contener el número máximo de bases de datos). Más específicamente, el grupo debe tener suficientes eDTU disponibles para cubrir el número garantizado de eDTU por base de datos (por ejemplo, si el número garantizado de eDTU para el grupo es de 400 y el número garantizado de eDTU para cada base de datos es 10, el número máximo de bases de datos que se permiten en el grupo es de 40 (400 eDTU/10 eDTU garantizadas por base de datos = 40 bases de datos como máximo).
    - Las características actuales que utiliza la base de datos deben estar disponibles en el grupo.

### Recomendaciones dinámicas:

Después de agregar las bases de datos al grupo, las recomendaciones se generarán dinámicamente basándose en el historial de uso de las bases de datos que se han seleccionado. Estas recomendaciones se mostrarán en el gráfico de uso de eDTU y GB, así como en un mensaje emergente de recomendación en la parte superior de la hoja **Configurar grupo**. Estas recomendaciones están pensadas para ayudarle a crear un grupo optimizado para sus bases de datos concretas.


 ![recomendaciones dinámicas](./media/sql-database-elastic-pool-portal/dynamic-recommendation.png)


## Paso 4: Establecimiento de las características de rendimiento del grupo

El rendimiento del grupo se configura estableciendo los parámetros de rendimiento para el grupo y las bases de datos elásticas del grupo. Tenga en cuenta que la **configuración de Base de datos elástica** se aplica a todas las bases de datos del grupo.

 ![Configuración de grupos elásticos](./media/sql-database-elastic-pool-portal/configure-performance.png)

Existen tres parámetros que puede establecer y que definen el rendimiento del grupo: el número garantizado de eDTU del grupo, así como el eDTU MIN y el eDTU MAX de bases de datos elásticas del grupo. La tabla siguiente describe cada uno de estos parámetros y ofrece algunas indicaciones sobre cómo configurarlos. Para saber la configuración de valor disponible específica, consulte la [referencia de grupo de bases de datos elásticas](sql-database-elastic-pool-reference.md).

| Parámetro de rendimiento | Descripción |
| :--- | :--- |
| **POOL eDTU**: garantía de eDTU para el grupo | El número garantizado de eDTU para el grupo es el número garantizado de eDTU disponibles y compartidas por todas las bases de datos del grupo. <br> El tamaño específico del número garantizado de eDTU para un grupo debe aprovisionarse teniendo en cuenta el uso histórico de eDTU del grupo. Como alternativa, este valor se puede establecer según el número garantizado de DTU por base de datos y el uso de bases de datos activas al mismo tiempo. El número garantizado de DTU para el grupo también guarda relación con la cantidad de almacenamiento disponible para el grupo, ya que por cada DTU que se asigne al grupo se obtiene una cantidad fija de almacenamiento de base de datos. <br> **¿En qué valor debo establecer el número garantizado de eDTU del grupo?** <br>Como mínimo, debe establecer el número garantizado de eDTU del grupo en un valor equivalente al resultado de la operación siguiente: ([n.º de bases de datos] x [promedio de uso de DTU por base de datos]). |
| **eDTU MIN**: número garantizado de eDTU para cada base de datos | El número garantizado de eDTU por base de datos es el número de eDTU que se garantiza en una base de datos única del grupo. <br>**¿En qué valor debo establecer el número garantizado de eDTU por base de datos?** <br>Normalmente, el número garantizado de eDTU por base de datos (eDTU MIN) se establece en cualquier valor entre 0 y el ([promedio de uso por base de datos]). El número garantizado de eDTU por base de datos es un ajuste global que establece el número garantizado de eDTU para todas las bases de datos del grupo. |
| **eDTU MAX**: capacidad de eDTU por base de datos | El valor eDTU MAX por base de datos indica el número máximo de eDTU que puede usar cada una de las bases de datos del grupo. Establezca la capacidad de eDTU por base de datos en un valor lo suficientemente alto como para soportar las fluctuaciones de demanda de las bases de datos. Puede establecer esta capacidad en el límite del sistema, que dependerá del nivel de precios del grupo (1000 eDTU en el caso de Premium). La capacidad específica debe soportar los picos de utilización de las bases de datos del grupo. Se admite un cierto grado de exceso de asignación de recursos, ya que el grupo suele basarse en patrones de uso en frío y caliente de las bases de datos, cuando en realidad los picos de demanda no tienen lugar en todas las bases de datos a la vez.<br> **¿En qué valor debo establecer la capacidad de eDTU por base de datos?** <br> Establezca el valor eDTU MAX o capacidad de eDTU por base de datos en ([uso pico de base de datos)]. Por ejemplo, suponga que el pico de utilización de cada base de datos es de 50 DTU y solo el 20% de las 100 bases de datos del grupo registran simultáneamente un pico de rendimiento. Si la capacidad de eDTU de cada base de datos se establece en 50 eDTU, deberá asignar una cantidad de recursos cinco veces mayor y establecer el número garantizado de eDTU para el grupo en 1000 eDTU. También debe tener en cuenta que la capacidad de eDTU no es un número garantizado de recursos para las bases de datos, sino que es un valor máximo que puede llegar a alcanzarse si está disponible. |


## Adición y eliminación de bases de datos de un grupo

Una vez creado el grupo, puede agregar o quitar bases de datos existentes dentro y fuera del grupo al agregar o quitar bases de datos en la página **Bases de datos elásticas** (busque su grupo y haga clic en el vínculo **Bases de datos elásticas** en **Essentials**).

- Haga clic en **Agregar bases de datos** para abrir la lista de bases de datos que se pueden agregar al grupo.

    O bien

- Seleccione las bases de datos que desea quitar del grupo y haga clic en **Eliminar bases de datos**.

![grupo recomendado](./media/sql-database-elastic-pool-portal/add-remove-databases.png)

## Creación de una nueva base de datos en un grupo

Cree una nueva base de datos en un grupo mediante la búsqueda del grupo deseado y haciendo clic en **Crear base de datos**.

La base de datos SQL ya está configurada para el servidor y el grupo correctos, por lo que debe escribir un nombre y seleccionar las opciones de la base de datos y haga clic en **Aceptar** para crear la nueva base de datos:


   ![creación de bases de datos elásticas](./media/sql-database-elastic-pool-portal/create-database.png)



## Supervisión y administración de un grupo de bases de datos elásticas

Después de crear un grupo de bases de datos elásticas, podrá supervisar y administrar el grupo en el portal examinando la lista de grupos existentes y seleccionando el grupo deseado.

Después de crear un grupo, podrá:

- Seleccione la opción **Configurar grupo** para cambiar la configuración de número de eDTU del grupo y número de eDTU por base de datos.
- Seleccionar **Crear trabajo** y administrar las bases de datos del grupo mediante la creación de trabajos elásticos. Los trabajos elásticos le permiten ejecutar scripts de Transact-SQL con cualquier número de bases de datos en el grupo. Para obtener más información, vea [Información general de los trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md).
- Seleccione **Administrar trabajos** para administrar los trabajos elásticos existentes.
- Haga clic en **Crear base de datos** para crear una base de datos nueva en el grupo.
- Haga clic en el vínculo de configuración del grupo para ajustar las eDTU y GB del grupo por grupo
- Haga clic en el vínculo de las bases de datos para agregar o quitar bases de datos del grupo.
- Haga clic en el vínculo de configuración de la base de datos para ajustar las eDTU mínima y máxima de las bases de datos elásticas del grupo. 

![Supervisión de grupos elásticos](./media/sql-database-elastic-pool-portal/pool-tasks.png)


Al seleccionar un grupo existente, podrá ver la utilización de recursos del grupo. Haga clic en el gráfico **Utilización de recursos** para abrir la hoja **Métrica**, donde podrá personalizar el gráfico y configurar alertas.

![Supervisión de grupos elásticos][4] ![Utilización de recursos][6]

Haga clic en **Editar gráfico** para agregar parámetros para ver fácilmente los datos de telemetría del grupo.


![Edición de gráficos][7]


## Pasos siguientes
Después de crear un grupo de bases de datos elásticas, puede administrar las bases de datos del grupo creando trabajos elásticos. Los trabajos elásticos facilitan la ejecución de secuencias de comandos de Transact-SQL con cualquier número de bases de datos en el grupo. Para obtener más información, vea [Información general sobre los trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md).



## Recursos adicionales

- [Grupo elástico de bases de datos SQL](sql-database-elastic-pool.md)
- [Creación de un grupo elástico de bases de datos SQL con PowerShell](sql-database-elastic-pool-powershell.md)
- [Creación y administración de Base de datos SQL con C#](sql-database-client-library.md)
- [Referencia de bases de datos elásticas](sql-database-elastic-pool-reference.md)


<!--Image references-->
[4]: ./media/sql-database-elastic-pool-portal/monitor-elastic-pool.png
[6]: ./media/sql-database-elastic-pool-portal/metric.png
[7]: ./media/sql-database-elastic-pool-portal/edit-chart.png
[10]: ./media/sql-database-elastic-pool-portal/star.png

<!---HONumber=AcomDC_0218_2016-->