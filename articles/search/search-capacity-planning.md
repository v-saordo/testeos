<properties
	pageTitle="Planeación de la capacidad en Búsqueda de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="La capacidad de Búsqueda de Azure se crea con particiones y réplicas, donde cada una es una unidad de búsqueda facturable."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="02/28/2016"
	ms.author="heidist"/>

# Planeación de la capacidad en Búsqueda de Azure

En Búsqueda de Azure, puede ajustar incrementalmente la capacidad de determinados recursos de proceso. Puede aumentar las particiones si necesita más almacenamiento o E/S, o réplicas para mejorar las consultas y el indexado.

La escalabilidad está disponible cuando se aprovisiona un servicio en un [nivel básico](http://aka.ms/azuresearchbasic) o uno de los [niveles estándar](search-limits-quotas-capacity.md).

Para ambos niveles, la capacidad se adquiere en incrementos de *unidades de búsqueda* (SU), donde cada partición y réplica cuenta como una SU.

- El nivel básico proporciona hasta 3 SU por servicio.
- El nivel estándar proporciona hasta 36 SU por servicio.

Tendrá que elegir una combinación de particiones y réplicas que permanezcan por debajo del límite del nivel. Por ejemplo, no podría escalar verticalmente su servicio estándar hasta 12 particiones y 6 réplicas porque hacerlo le requeriría 72 unidades de búsqueda (12 x 6), lo que supera el límite de 36 unidades de búsqueda por servicio.

Como norma general, las aplicaciones de búsqueda tienden a necesitar más réplicas que particiones. Consulte la sección sobre [alta disponibilidad](#HA) para obtener más información.

**Cálculo de las unidades de búsqueda para combinaciones de recursos específicas: R X P = SU**

La fórmula para calcular cuántas SU se necesitan es réplicas multiplicadas por particiones. Por ejemplo, 3 réplicas multiplicadas por 3 particiones se factura como 9 unidades de búsqueda.

Ambos niveles comienzan con una réplica y una partición, que cuentan como una unidad de búsqueda (SU). Este es el único caso en que una réplica y una partición se cuentan como una unidad de búsqueda. Cada recurso adicional, ya sea una réplica o una partición, se cuenta como una SU independiente.

El nivel determina el costo por SU. El costo por SU es menor para el nivel básico que para el nivel Estándar. Las tarifas de cada nivel se encuentran en [Detalles de precios](https://azure.microsoft.com/pricing/details/search/).

## Nivel básico: combinaciones de particiones y réplicas

Un servicio básico puede tener 1 partición y hasta 3 réplicas, para un límite máximo de 3 SU.

<table cellspacing="0" border="1">
<tr><td><b>3 réplicas</b></td><td>3 unidades de búsqueda</td></tr>
<tr><td><b>2 réplicas</b></td><td>2 unidades de búsqueda</td></tr>
<tr><td><b>1 réplica</b></td><td>1 unidad de búsqueda</td></tr>
<tr><td></td><td><b>1 partición</b></td></tr>
</table>

<a id="chart"></a>
## Nivel Estándar: combinaciones de particiones y réplicas

Esta tabla muestra las unidades de búsqueda necesarias para admitir combinaciones de réplicas y particiones, con el límite de 36 unidades de búsqueda (SU).

<table cellspacing="0" border="1">
<tr><td><b>12 réplicas</b></td><td>12 unidades de búsqueda</td><td>24 unidades de búsqueda</td><td>36 unidades de búsqueda</td><td>N/D</td><td>N/D</td><td>N/D</td></tr>
<tr><td><b>6 réplicas</b></td><td>6 unidades de búsqueda</td><td>12 unidades de búsqueda</td><td>18 unidades de búsqueda</td><td>24 unidades de búsqueda</td><td>36 unidades de búsqueda</td><td>N/D</td></tr>
<tr><td><b>5 réplicas</b></td><td>5 unidades de búsqueda</td><td>10 unidades de búsqueda</td><td>15 unidades de búsqueda</td><td>20 unidades de búsqueda</td><td>30 unidades de búsqueda</td><td>N/D</td></tr>
<tr><td><b>4 réplicas</b></td><td>4 unidades de búsqueda</td><td>8 unidades de búsqueda</td><td>12 unidades de búsqueda</td><td>16 unidades de búsqueda</td><td>24 unidades de búsqueda</td><td>N/D </td></tr>
<tr><td><b>3 réplicas</b></td><td>3 unidades de búsqueda</td><td>6 unidades de búsqueda</td><td>9 unidades de búsqueda</td><td>12 unidades de búsqueda</td><td>18 unidades de búsqueda</td><td>36 unidades de búsqueda</td></tr>
<tr><td><b>2 réplicas</b></td><td>2 unidades de búsqueda</td><td>4 unidades de búsqueda</td><td>6 unidades de búsqueda</td><td>8 unidades de búsqueda</td><td>12 unidades de búsqueda</td><td>24 unidades de búsqueda</td></tr>
<tr><td><b>1 réplica</b></td><td>1 unidad de búsqueda</td><td>2 unidades de búsqueda</td><td>3 unidades de búsqueda</td><td>4 unidades de búsqueda</td><td>6 unidades de búsqueda</td><td>12 unidades de búsqueda</td></tr>
<tr><td>N/D</td><td><b>1 partición</b></td><td><b>2 particiones</b></td><td><b>3 particiones</b></td><td><b>4 particiones</b></td><td><b>6 particiones</b></td><td><b>12 particiones</b></td></tr>
</table>

En el sitio web de Azure se explican con detalle la capacidad, los precios y las unidades de búsqueda. Para más información, consulte [Detalles de precios](https://azure.microsoft.com/pricing/details/search/).

> [AZURE.NOTE] El número de réplicas y particiones debe dividirse en 12 uniformemente (de manera específica, 1, 2, 3, 4, 6, 12). Esto se debe a que la Búsqueda de Azure divide previamente cada índice en 12 particiones para que se pueda repartir entre las particiones. Por ejemplo, si su servicio tiene tres particiones y crea un nuevo índice, cada partición contendrá 4 particiones del índice. La manera en que la Búsqueda de Azure particiona un índice es un detalle de implementación, sujeto a cambios en la futura versión. Aunque el número es 12 hoy, no debe esperar que ese número se siempre 12 en el futuro.

<a id="HA"></a>
## Elegir una combinación de particiones y de réplicas para alta disponibilidad

Dado que es sencillo y relativamente rápido escalar verticalmente, generalmente se recomienda que comience con una partición y una o dos réplicas, y que después escale verticalmente conforme se crean volúmenes de consulta. Para muchas implementaciones, una partición proporciona suficiente almacenamiento y E/S (en 15 millones de documentos por partición).

Sin embargo, las cargas de trabajo de consultas se basan en las réplicas. Podría requerir réplicas adicionales si necesita más rendimiento o alta disponibilidad.

Las recomendaciones generales para alta disponibilidad son:

- 2 réplicas para alta disponibilidad de cargas de trabajo de solo lectura (consultas)
- 3 o más réplicas para alta disponibilidad de cargas de trabajo de lectura-escritura (consultas e indización)

En la actualidad no hay ningún mecanismo integrado para la recuperación ante desastres. La adición de particiones o réplicas sería la estrategia equivocada para cumplir los objetivos de recuperación ante desastres. En su lugar, podría considerar la adición de redundancia en el nivel de servicio. Para una explicación más detallada de las soluciones alternativas, consulte [esta publicación del foro](https://social.msdn.microsoft.com/Forums/ee108a26-00c5-49f6-b1ff-64c66c8b828a/dr-and-high-availability-for-azure-search?forum=azuresearch).

> [AZURE.NOTE] Recuerde que la escalabilidad y los acuerdos de nivel de servicio son características del servicio estándar. El servicio gratuito se ofrece en un nivel de recurso fijo, con las réplicas y las particiones compartidas por varios suscriptores. Si ha iniciado con el servicio gratuito y ahora quiere actualizar, deberá crear un nuevo servicio de Búsqueda de Azure en el nivel estándar y luego volver a cargar los índices y los datos en el nuevo servicio. Consulte [Creación de un servicio Búsqueda de Azure en el Portal](search-create-service-portal.md) para obtener instrucciones sobre el aprovisionamiento del servicio.

## Acerca de particiones y réplicas

Las **particiones** ofrecen almacenamiento y E/S. Un único servicio de búsqueda puede tener un máximo de 12 particiones. Cada partición se incluye con un límite máximo de 15 millones de documentos o 25 GB de almacenamiento, lo que ocurra primero. Si agrega particiones, su servicio de búsqueda puede cargar más documentos. Por ejemplo, un servicio con una partición única que almacena inicialmente hasta 25 GB de datos puede almacenar 50 GB cuando se agrega una segunda partición al servicio.

Las **réplicas** son copias del motor de búsqueda. Un servicio de búsqueda único puede tener un máximo de 6 réplicas. Necesita al menos 2 réplicas para disponibilidad (consultas) de lectura y al menos 3 réplicas para disponibilidad de lectura y escritura (consultas, indización).

Se ejecuta una copia de cada índice en cada réplica. Conforme agrega réplicas, se ponen en línea copias adicionales del índice para admitir mayores cargas de trabajo de consultas y para equilibrar la carga de las solicitudes por las diversas réplicas. Si tiene varios índices, digamos 6, y 3 réplicas, cada réplica tendrá una copia de todos los 6 índices.

Tenga en cuenta que no ofrecemos estimaciones finales sobre consultas por segundo (QPS), ya que la ejecución de consultas puede variar considerablemente según la complejidad de la consulta y las cargas de trabajo competitivas. De media, una réplica puede dar servicio a unas 15 QPS, pero el rendimiento será un algo mayor o menor en función de la complejidad de la consulta (las consultas por facetas son más complejas) y la latencia de red. Además, es importante reconocer que, aunque la adición de réplicas agregará definitivamente escala y rendimiento, el resultado final no será estrictamente lineal: la adición de 3 réplicas no garantiza el triple rendimiento. La latencia de consultas es un indicador de que se pueden necesitar réplicas adicionales. Para obtener información sobre QPS, incluidos los métodos para estimar el valor de QPS para las cargas de trabajo, consulte [Administración del servicio de búsqueda](search-manage.md).

<!---HONumber=AcomDC_0302_2016-->