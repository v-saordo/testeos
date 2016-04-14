<properties
	pageTitle="Instrucciones de implementación de los servicios de infraestructura de Azure"
	description="Obtenga información sobre las directrices clave de diseño e implementación para implementar una carga de trabajo de TI en los servicios de infraestructura de Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="squillace"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/21/2015"
	ms.author="rasquill"/>

# Instrucciones de implementación de los servicios de infraestructura de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure es una excelente plataforma para implementar configuraciones de desarrollo o pruebas o de prueba de concepto, ya que requiere muy poca inversión para probar un determinado enfoque para la implementación de las soluciones. Sin embargo, debe ser capaz de distinguir las prácticas fáciles para un entorno de desarrollo y pruebas de las prácticas más difíciles y detalladas para una implementación completamente funcional y lista para la producción de una carga de trabajo de TI.

En esta guía se identifican muchas áreas en las que la planificación es la clave del éxito de una carga de trabajo de TI en Azure. Además, la planificación proporciona un orden para la creación de los recursos necesarios. Aunque hay cierta flexibilidad, se recomienda aplicar el orden indicado en este artículo para la planificación y la toma de decisiones.

Este artículo está adaptado del contenido de la entrada de blog [Azure Implementation Guidelines](http://blogs.msdn.com/b/thecolorofazure/archive/2014/05/13/azure-implementation-guidelines.aspx) (Directrices de implementación de Azure). Gracias a Santiago Cánepa (Director de desarrollo de aplicaciones de Microsoft) y Hugo Salcedo (Director de desarrollo de aplicaciones de Microsoft) por su material original.

> [AZURE.NOTE]Los grupos de afinidad han quedado en desuso. Aquí no se describe su uso. Para obtener más información, consulte [Acerca de las redes virtuales regionales y los grupos de afinidad](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

## 1\. Convenciones de nomenclatura

Debe existir una buena convención de nomenclatura antes de crear cualquier elemento en Azure. Una convención de nomenclatura garantiza que todos los recursos tienen un nombre predecible, lo que ayuda a reducir la carga administrativa asociada a la administración de esos recursos.

Puede seguir un conjunto específico de convenciones de nomenclatura definidas para toda la organización, o bien para una suscripción o cuenta específica de Azure. Aunque es fácil para las personas de una organización establecer reglas implícitas al trabajar con recursos de Azure, cuando un equipo necesita trabajar en un proyecto en Azure ese modelo no se adapta bien.

Debe acordar por adelantado el conjunto de convenciones de nomenclatura. Existen algunas consideraciones sobre las convenciones de nomenclatura que trascienden los conjuntos de reglas que componen dichas convenciones.

### Afijos

Al crear ciertos recursos, Azure usará algunos valores predeterminados para simplificar la administración de los recursos asociados a dichos recursos. Por ejemplo, al crear la primera máquina virtual para un nuevo servicio en la nube, el Portal de Azure clásico intentará usar el nombre de la máquina virtual como nombre de un nuevo servicio en la nube para la máquina virtual.

Por lo tanto, conviene identificar los tipos de recursos que necesitan un afijo para identificar dicho tipo. Además, especifique claramente si el afijo se pondrá:

- Al principio del nombre (prefijo)
- Al final del nombre (sufijo)

Por ejemplo, a continuación se muestran dos nombres posibles para un grupo de recursos que hospeda un motor de cálculo:

- Rg-CalculationEngine (prefijo)
- CalculationEngine-Rg (sufijo)

Los afijos pueden hacer referencia a distintos aspectos que describen los recursos concretos. En la tabla siguiente se muestran algunos ejemplos que suelen usarse.

Aspecto | Ejemplos | Notas
--- | --- | ---
Environment | dev, stg, prod | Según el propósito y el nombre de cada entorno.
Ubicación | usw (Oeste de EE. UU.), use (Este de EE. UU. 2) | Según la región del centro de datos o la región de la organización.
Componente, servicio o producto de Azure | Rg para grupo de recursos, Svc para servicio en la nube, VNet para red virtual | Según el producto para el que proporciona compatibilidad el recurso.
Rol | sql, ora, sp, iis | Según el rol de la máquina virtual.
Instance | 01, 02, 03, etc. | Para los recursos que pueden tener más de una instancia. Por ejemplo, servidores web de carga equilibrada en un servicio en la nube.

Al establecer las convenciones de nomenclatura, asegúrese de que indiquen claramente qué afijos se deben usar para cada tipo de recurso y en qué posición (prefijo o sufijo).

### Fechas

A menudo es importante determinar la fecha de creación a partir del nombre de un recurso. Se recomienda el formato de fecha AAAAMMDD. Este formato garantiza no solo que se registre la fecha completa, sino que dos recursos cuyos nombres difieren solo en la fecha se ordenen alfabética y cronológicamente al mismo tiempo.

### Asignación de nombres a recursos

Debe definir cada tipo de recurso en la convención de nomenclatura, que debería tener reglas que definan cómo asignar nombres a cada recurso que se cree. Estas reglas se deben aplicar a todos los tipos de recursos, por ejemplo:

- Suscripciones
- Cuentas
- Cuentas de almacenamiento
- Redes virtuales
- Subredes
- Conjuntos de disponibilidad
- Grupos de recursos
- Servicios en la nube
- Máquinas virtuales
- Extremos
- Grupos de seguridad de red
- Roles

Para asegurarse de que el nombre proporciona información suficiente para determinar el recurso al que hace referencia, es preciso usar nombres descriptivos.

### Nombres de equipo

Cuando los administradores crean una máquina virtual, Microsoft Azure les pedirá que proporcionen un nombre de máquina virtual de hasta 15 caracteres. Azure usará el nombre de la máquina virtual como nombre del recurso de la máquina virtual de Azure. Azure usará el mismo nombre como nombre de equipo para el sistema operativo instalado en la máquina virtual. Sin embargo, estos nombres podrían no ser siempre iguales.

En los casos en que una máquina virtual se crea a partir de un archivo de imagen .VHD que ya contiene un sistema operativo, el nombre de la máquina virtual en Azure puede diferir del nombre de equipo del sistema operativo de la máquina virtual. Esta situación se desaconseja, ya que puede agregar cierta dificultad a la administración de máquinas virtuales. Asigne al recurso de la máquina virtual de Azure el mismo nombre que el del equipo que asigne al sistema operativo de dicha máquina virtual.

Se recomienda que el nombre de la máquina virtual de Azure sea el mismo que el del equipo del sistema operativo subyacente. Por esta razón, debe seguir las reglas de nomenclatura de NetBIOS que se describen en [Convenciones de nomenclatura NetBIOS de equipos de Microsoft](https://support.microsoft.com/kb/188997/).

### Nombres de cuentas de almacenamiento

Las cuentas de almacenamiento tienen reglas especiales que regulan sus nombres. Sólo puede utilizar minúsculas y números. Para obtener más información, vea [Creación de una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account). Además, el nombre de la cuenta de almacenamiento, en combinación con core.windows.net, debe ser un nombre DNS único y válido globalmente. Por ejemplo, si la cuenta de almacenamiento se llama mystorageaccount, los siguientes nombres DNS deben ser únicos:

- mystorageaccount.blob.core.windows.net
- mystorageaccount.table.core.windows.net
- mystorageaccount.queue.core.windows.net


### Nombres de bloques de creación de Azure

Los bloques de creación de Azure son servicios de nivel de aplicación que ofrece Azure, normalmente para las aplicaciones que aprovechan las características de PaaS, aunque los recursos de IaaS también pueden aprovechar algunas, como Base de datos SQL de Azure, el Administrador de tráfico y otros.

Estos servicios se basan en una matriz de artefactos que se crean y registran en Azure. También deben tenerlos en cuenta en las convenciones de nomenclatura.

### Resumen de las directrices de implementación para las convenciones de nomenclatura

Decisión:

- ¿Cuáles son sus convenciones de nomenclatura para los recursos de Azure?

Tarea:

- Defina las convenciones de nomenclatura en términos de afijos, jerarquía, valores de cadena y otras directivas de los recursos de Azure.

## 2\. Suscripciones y cuentas

Para trabajar con Azure, necesita una o más suscripciones a Azure. Los recursos, como servicios en la nube o máquinas virtuales, existen en el contexto de las suscripciones.

- Los clientes empresariales suelen tener una inscripción Enterprise, que es el recurso de nivel superior en la jerarquía y está asociado a una o varias cuentas.
- Para los consumidores y clientes que no tienen una inscripción Enterprise, el recurso de nivel superior es la cuenta.
- Las suscripciones están asociadas a las cuentas y puede haber una o más suscripciones por cuenta. Azure registra la información de facturación por suscripción.

Debido al límite de dos niveles de jerarquía en la relación de cuenta/suscripción, es importante adaptar la convención de nomenclatura de las cuentas y las suscripciones a las necesidades de facturación. Por ejemplo, si una empresa internacional usa Azure, puede optar por tener una cuenta por región y administrar las suscripciones a nivel regional.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/sub01.png)

Por ejemplo, podría usar esta estructura.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/sub02.png)

Siguiendo con el mismo ejemplo, si una región decide tener más de una suscripción asociada a un grupo determinado, la convención de nomenclatura debe incorporar un método para codificar la parte adicional del nombre de cuenta o de suscripción. Esta organización permite manipular los datos de facturación para generar los nuevos niveles de jerarquía durante los informes de facturación.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/sub03.png)

La organización podría tener este aspecto.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/sub04.png)

Microsoft proporciona una facturación detallada a través de un archivo descargable para una sola cuenta o para todas las cuentas de un contrato Enterprise. Puede procesar este archivo, por ejemplo, con Microsoft Excel. Este proceso introduce los datos, particiona los recursos que codifican más de un nivel de la jerarquía en columnas independientes y usa una tabla dinámica o PowerPivot para proporcionar la funcionalidad de informes dinámicos.

### Resumen de las directrices de implementación para suscripciones y cuentas

Decisión:

- ¿Qué conjunto de suscripciones y cuentas necesita para hospedar su infraestructura o carga de trabajo de TI?

Tarea:

- Cree el conjunto de suscripciones y cuentas usando su convención de nomenclatura.

## 3\. Almacenamiento

Almacenamiento de Azure es una parte integral de muchas soluciones de Azure. Almacenamiento de Azure proporciona servicios para almacenar datos de archivos, datos sin estructura y mensajes, y también forma parte de la infraestructura que da soporte a las máquinas virtuales.

Existen dos tipos de cuentas de almacenamiento disponibles en Azure. Una cuenta de almacenamiento estándar proporciona acceso al almacenamiento de blobs (que se usa para almacenar discos de máquinas virtuales de Azure), al almacenamiento de tablas, de colas y de archivos. El almacenamiento Premium está diseñado para aplicaciones de alto rendimiento, como servidores SQL Server en un clúster de AlwaysOn, y actualmente solo admite discos de máquina virtual de Azure.

Las cuentas de almacenamiento están vinculadas a objetivos de escalabilidad. Vea [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md#storage-limits) para familiarizarse con los límites actuales de almacenamiento de Azure. Vea también [Objetivos de escalabilidad y rendimiento del almacenamiento en Azure](../storage-scalability-targets.md).

Azure crea máquinas virtuales con un disco del sistema operativo, un disco temporal y ninguno o varios discos de datos opcionales. Tanto el disco del sistema operativo como los discos de datos son blobs en páginas de Azure, mientras que el disco temporal se almacena localmente en el nodo en que reside el equipo. Esto hace que el disco temporal no sea apto para datos que deben mantenerse durante un reciclaje del sistema, ya que el equipo podría migrarse en modo silencioso de un nodo a otro, con lo que se perderían los datos de dicho disco. No almacene nada en la unidad temporal.

Los discos del sistema operativo y los discos de datos tienen un tamaño máximo de 1023 GB, ya que el tamaño máximo de un blob es de 1024 gigabytes (GB) y debe contener los metadatos (pie de página) del archivo VHD (un GB equivale a 1024<sup>3</sup> bytes). Puede implementar la creación de bandas en el disco en Windows para superar este límite.

### Discos con bandas
Además de ofrecer la posibilidad de crear discos de más de 1023 GB, en muchos casos la creación de bandas en los discos de datos mejorará el rendimiento, ya que permite que varios blobs respalden el almacenamiento de un solo volumen. Con la creación de bandas, las operaciones de E/S necesarias para escribir y leer datos de un único disco lógico se realizan en paralelo.

Azure impone límites en la cantidad de discos de datos y el ancho de banda disponible, en función del tamaño de la máquina virtual. Para obtener más información, vea [Tamaños de máquinas virtuales](virtual-machines-size-specs.md).

Si usa la creación de bandas en discos de datos de Azure, tenga en cuenta las siguientes directrices:

- Los discos de datos siempre deben tener el tamaño máximo (1.023 GB)
- Conecte los discos de datos máximos que se permiten para el tamaño de la máquina virtual
- Use la configuración de espacios de almacenamiento
- Use la configuración de creación de bandas de almacenamiento
- Evite el uso de opciones de almacenamiento en caché del disco de datos de Azure (directiva de almacenamiento en caché = ninguna)

Para obtener más información, consulte [Espacios de almacenamiento - Diseño para el rendimiento](http://social.technet.microsoft.com/wiki/contents/articles/15200.storage-spaces-designing-for-performance.aspx).

### Cuentas de almacenamiento múltiples

El uso de varias cuentas de almacenamiento para respaldar los discos asociados a varias máquinas virtuales garantiza que la E/S agregada de esos discos esté por debajo de los objetivos de escalabilidad para cada una de las cuentas de almacenamiento.

Se recomienda comenzar con la implementación de una máquina virtual por cada cuenta de almacenamiento.

### Diseño del almacenamiento

Para aplicar estas estrategias con el fin de implementar el subsistema de disco de las máquinas virtuales con un buen rendimiento, una carga de trabajo o infraestructura de TI normalmente aprovecha la existencia de muchas cuentas de almacenamiento. Estas hospedan muchos blobs de VHD. En algunos casos, hay más de un blob asociado a un solo volumen de una máquina virtual.

Esta situación puede agregar cierta complejidad a las tareas de administración. La clave consiste en diseñar una sólida estrategia de almacenamiento, que incluya una nomenclatura adecuada para los discos subyacentes y los blobs de VHD asociados.

### Resumen de las directrices de implementación para el almacenamiento

Decisiones:

- ¿Necesita la creación de bandas en los discos para crear discos de más de 500 terabytes(TB)?
- ¿Necesita la creación de bandas en los discos para lograr un rendimiento óptimo para la carga de trabajo?
- ¿Qué conjunto de cuentas de almacenamiento necesita para hospedar su infraestructura o carga de trabajo de TI?

Tarea:

- Cree el conjunto de cuentas de almacenamiento usando su convención de nomenclatura. Puede usar el Portal de Azure, el Portal de Azure clásico o el cmdlet **New-AzureStorageAccount** de PowerShell.

## 4\. Servicios en la nube

Los servicios en la nube son un pilar fundamental en la administración de servicios de Azure, tanto para los servicios de PaaS como de IaaS. Para PaaS, los servicios en la nube representan una asociación de roles cuyas instancias pueden comunicarse entre sí. Los servicios en la nube están asociados a una dirección IP virtual pública (VIP) y un equilibrador de carga, que toma el tráfico entrante de Internet y equilibra la carga en los roles configurados para recibir ese tráfico.

En el caso de IaaS, los servicios en la nube ofrecen una funcionalidad similar, aunque en la mayoría de los casos la funcionalidad de equilibrador de carga se usa para reenviar el tráfico de los puertos TCP o UDP específicos desde Internet a las diversas máquinas virtuales dentro de ese servicio en la nube.

> [AZURE.NOTE]Los servicios en la nube no existen en el Administrador de recursos de Azure. Para obtener una introducción a las ventajas del Administrador de recursos, vea [Proveedores de procesos, redes y almacenamiento de Azure en el Administrador de recursos de Azure](../articles/virtual-machines/virtual-machines-azurerm-versus-azuresm.md).

Los nombres del servicio en la nube son especialmente importantes en IaaS, ya que Azure los usa como parte de la convención predeterminada de nomenclatura de los discos. El nombre del servicio en la nube solo puede contener letras, números y guiones. El primer y el último carácter del campo deben ser una letra o un número.

Azure expone los nombres de los servicios en la nube, ya que están asociados a la VIP, en el dominio “cloudapp.net”. Para una mejor experiencia de usuario de la aplicación, debe configurarse un nombre personalizado según sea necesario para reemplazar el nombre completo del servicio en la nube. Esto suele hacerse con un registro CNAME en el DNS público que asigna el nombre DNS público del recurso (por ejemplo, www.contoso.com) al nombre DNS del servicio en la nube que hospeda el recurso (por ejemplo, el servicio en la nube que hospeda los servidores web de www.contoso.com).

Además, es posible que la convención de nomenclatura usada para los servicios en la nube deba tolerar las excepciones, ya que los nombres de los servicios en la nube deben ser únicos entre todos los demás servicios en la nube de Microsoft Azure, independientemente del inquilino de Microsoft Azure.

Una limitación importante de los servicios en la nube a tener en cuenta es que solo se puede realizar una operación de administración de máquinas virtuales a la vez para todas las máquinas virtuales del servicio en la nube. Al realizar una operación de administración de máquina virtual en una máquina virtual en el servicio en la nube, debe esperar hasta que se complete para poder realizar una nueva operación de administración en otra máquina virtual. Por lo tanto, el número de máquinas virtuales de un servicio en la nube debe ser bajo.

Las suscripciones a Azure pueden admitir un máximo de 200 servicios en la nube.

### Resumen de las directrices de implementación para los servicios en la nube

Decisión:

- ¿Qué conjunto de servicios en la nube necesita para hospedar su infraestructura o carga de trabajo de TI?

Tarea:

- Cree el conjunto de servicios en la nube usando su convención de nomenclatura. Puede usar el Portal de Azure clásico o el cmdlet **New-AzureService** de PowerShell.

## 5\. Redes virtuales

El siguiente paso lógico es crear las redes virtuales necesarias para admitir las comunicaciones entre las máquinas virtuales de la solución. Aunque es posible hospedar varias máquinas virtuales de una carga de trabajo de TI en un solo servicio en la nube, se recomiendan las redes virtuales.

Las redes virtuales son un contenedor de máquinas virtuales para las que también puede especificar subredes, direccionamiento personalizado y opciones de configuración de DNS. Las máquinas virtuales dentro de la misma red virtual pueden comunicarse directamente con otros equipos de la misma red virtual, independientemente del servicio en la nube al que pertenezcan. Dentro de la red virtual, esta comunicación sigue siendo privada y no es necesario que pase por los extremos públicos. Esta comunicación puede producirse a través de la dirección IP o por nombre, mediante un servidor DNS instalado en la red virtual o de manera local, si la máquina virtual está conectada a la red corporativa.

### Conectividad de sitio
Si los equipos y usuarios locales no requieren conectividad continua a máquinas virtuales de una red virtual de Azure, cree una red virtual solo en la nube.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/vnet01.png)

Normalmente está destinada a cargas de trabajo con conexión a Internet, como un servidor web basado en Internet. Puede administrar estas máquinas virtuales usando conexiones de Escritorio remoto, sesiones remotas de PowerShell, conexiones de Shell seguro (SSH) y conexiones VPN de punto a sitio.

Dado que no se conectan a la red local, las redes virtuales solo en la nube pueden usar cualquier parte del espacio de direcciones IP privado.

Si los equipos y usuarios locales necesitan conectividad continua a máquinas virtuales en una red virtual de Azure, cree una red virtual entre locales y conéctela a la red local con ExpressRoute o con una conexión VPN de sitio a sitio.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/vnet02.png)

En esta configuración, la red virtual de Azure es esencialmente una extensión basada en la nube de su red local.

Dado que se conectan a la red local, las redes virtuales entre locales deben utilizar solo una parte del espacio de direcciones usado por la organización que sea única y la infraestructura de enrutamiento debe admitir el enrutamiento del tráfico a dicha parte reenviándolo al dispositivo VPN local.

Para permitir que los paquetes viajen de la red virtual entre locales a la red local, debe configurar el conjunto de prefijos pertinentes de direcciones locales como parte de la definición de la red local para la red virtual. En función del espacio de direcciones de la red virtual y del conjunto de ubicaciones locales pertinentes, puede haber varios prefijos de direcciones en la red local.

Puede convertir una red virtual solo en la nube a una red virtual entre locales, pero probablemente tendrá que volver a numerar el espacio de direcciones de la red virtual, las subredes y las máquinas virtuales que usan direcciones IP estáticas asignadas por Azure, conocidas como direcciones IP dinámicas (DIP). Por lo tanto, considere cuidadosamente el tipo de redes virtuales que necesita (solo en la nube o entre locales) antes de crearlas.

### Subredes
Las subredes permiten organizar los recursos que están relacionados, ya sea lógicamente (por ejemplo, una subred para máquinas virtuales asociadas a la misma aplicación) o físicamente (por ejemplo, una subred por cada servicio en la nube), o bien emplear técnicas de aislamiento de subred para mayor seguridad.

Para las redes virtuales entre locales, debe diseñar subredes con las mismas convenciones que usa para los recursos locales, teniendo en cuenta que **Azure siempre usa las tres primeras direcciones IP del espacio de direcciones de cada subred**. Para determinar el número de direcciones necesarias para la subred, cuente el número de máquinas virtuales que necesita en este momento, calcule el crecimiento futuro y use la tabla siguiente para determinar el tamaño de la subred.

Número de máquinas virtuales necesarias | Número de bits de host necesarios | Tamaño de la subred
--- | --- | ---
1-3 | 3 | /29
4-11 | 4 | /28
12-27 | 5 | /27
28-59 | 6 | /26
60-123 | 7 | /25

> [AZURE.NOTE]Para las subredes locales normales, el número máximo de direcciones de host para una subred con n bits de host es 2<sup>n</sup> – 2. Para una subred de Azure, el número máximo de direcciones de host para una subred con n bits de host es 2<sup>n</sup> – 5 (2 más 3 para las direcciones que Azure usa en cada subred).

Si elige un tamaño de subred demasiado pequeño, tendrá que volver a numerar y a implementar las máquinas virtuales en la subred.

### Resumen de las directrices de implementación para las redes virtuales

Decisiones:

- ¿Qué tipo de red virtual necesita para hospedar su infraestructura o carga de trabajo de TI (solo en la nube o entre locales)?
- Para las redes virtuales entre locales, ¿cuánto espacio de direcciones necesita para hospedar ahora las subredes y las máquinas virtuales y durante un tiempo razonable en el futuro?

Tareas:

- Defina el espacio de direcciones para la red virtual.
- Defina el conjunto de subredes y el espacio de direcciones para cada uno.
- Para las redes virtuales entre locales, defina el conjunto de espacios de direcciones de redes locales para las ubicaciones locales que las máquinas virtuales de la red virtual deben alcanzar.
- Cree la red virtual usando su convención de nomenclatura. Puede usar el Portal de Azure o el Portal de Azure clásico.

## 6\. Conjuntos de disponibilidad

En PaaS de Azure, los servicios en la nube contienen uno o varios roles que ejecutan código de aplicación. Los roles pueden tener una o más instancias de máquina virtual que el tejido aprovisiona automáticamente. En cualquier momento, Azure puede actualizar las instancias de estos roles, pero como forman parte del mismo rol, Azure sabe que no debe actualizarlo todo al mismo tiempo para evitar una interrupción del servicio para el rol.

En IaaS de Azure, el concepto de rol no es importante, ya que cada máquina virtual de IaaS representa un rol con una sola instancia. Para indicarle a Azure que no desactive dos o más equipos asociados al mismo tiempo (por ejemplo, para las actualizaciones del sistema operativo del nodo en el que residen), se introdujo el concepto de conjuntos de disponibilidad. Un conjunto de disponibilidad le indica a Azure que no desactive al mismo tiempo todos los equipos del mismo conjunto de disponibilidad para evitar una interrupción del servicio. Los miembros de la máquina virtual de un conjunto de disponibilidad tienen un contrato de nivel de servicio de tiempo de actividad del 99,95%.

Los conjuntos de disponibilidad deben formar parte de la planificación de alta disponibilidad de la solución. Un conjunto de disponibilidad se define como el conjunto de máquinas virtuales dentro de un servicio en la nube que tienen el mismo nombre de conjunto de disponibilidad. Puede crear conjuntos de disponibilidad después de crear servicios en la nube.

### Resumen de las directrices de implementación para los conjuntos de disponibilidad

Decisión:

- ¿Cuántos conjuntos de disponibilidad necesita para los distintos roles y niveles de su infraestructura o carga de trabajo de TI?

Tarea:

- Defina el conjunto de conjuntos de disponibilidad usando su convención de nomenclatura. Puede asociar una máquina virtual a un conjunto de disponibilidad al crear las máquinas virtuales, o bien puede asociar una máquina virtual a un conjunto de disponibilidad después de crear la máquina virtual.

## 7\. Máquinas virtuales

En PaaS de Azure, Azure administra las máquinas virtuales y sus discos asociados. Debe crear y asignar un nombre a los servicios en la nube y los roles. Después, Azure creará instancias asociadas a dichos roles. En el caso de IaaS de Azure, depende de usted proporcionar un nombre a los servicios en la nube, las máquinas virtuales y los discos asociados.

Para reducir la carga administrativa, el Portal de Azure clásico usará el nombre del equipo como sugerencia para el nombre predeterminado del servicio en la nube asociado (en caso de que el cliente decida crear un nuevo servicio en la nube como parte del Asistente para la creación de una máquina virtual).

Además, Azure asigna un nombre a los discos y a sus blobs de VHD auxiliares combinando el nombre del servicio en la nube, el nombre del equipo y la fecha de creación.

En general, el número de discos será mucho mayor que la cantidad de máquinas virtuales. Debe tener cuidado al manipular las máquinas virtuales para evitar la creación de discos huérfanos. Además, los discos pueden eliminarse sin eliminar el blob auxiliar. Si este es el caso, el blob permanecerá en la cuenta de almacenamiento hasta que se elimine manualmente.

### Resumen de las directrices de implementación para las máquinas virtuales

Decisión:

- ¿Cuántas máquinas virtuales necesita proporcionar para la infraestructura o carga de trabajo de TI?

Tareas:

- Defina el nombre de cada máquina virtual usando su convención de nomenclatura.
- Cree las máquinas virtuales con el Portal de Azure, el Portal de Azure clásico, el cmdlet **New-AzureVM** de PowerShell, la CLI de Azure o las plantillas del Administrador de recursos.

## Ejemplo de carga de trabajo de TI: el motor de análisis financiero de Contoso

Contoso Corporation ha desarrollado un motor de análisis financiero de última generación con algoritmos patentados de vanguardia para ayudar en futuras transacciones comerciales. Su objetivo consiste en poner este motor a disposición de los clientes como un conjunto de servidores en Azure, que constan de:

- Dos (más en un futuro) servidores web basados en IIS que ejecutan servicios web personalizados en un nivel web.
- Dos (más en un futuro) servidores de aplicaciones basados en IIS que realizan los cálculos en un nivel de aplicación.
- Un clúster de SQL Server 2014 con grupos de disponibilidad AlwaysOn (dos servidores SQL Server y un testigo de nodos de mayoría) que almacena datos históricos y de cálculo continuo en un nivel de base de datos.
- Dos controladores de dominio de Active Directory para un dominio y bosque independiente en el nivel de autenticación, que es necesario para clústeres de SQL Server.
- Todos los servidores se encuentran en dos subredes; una subred de front-end para los servidores web y una subred de back-end para los servidores de aplicaciones, un clúster de SQL Server 2014 y controladores de dominio.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/example-tiers.png)

El tráfico web seguro entrante de los clientes de Contoso en Internet debe ser de carga equilibrada entre los servidores web. El tráfico de solicitud de cálculo en forma de solicitudes HTTP procedente de los servidores web debe equilibrarse entre los servidores de aplicaciones. Además, el motor debe diseñarse para alta disponibilidad.

El diseño resultante incluirá:

- Una suscripción y una cuenta de Azure de Contoso
- Cuentas de almacenamiento
- Una red virtual con dos subredes
- Conjuntos de disponibilidad para los conjuntos de servidores con un rol similar
- Máquinas virtuales
- Un único grupo de recursos

Todo lo anterior seguirá estas convenciones de nomenclatura de Contoso:

- Contoso usa [carga de trabajo de TI]-[ubicación]-[recurso de Azure] como prefijo. En este ejemplo, “azfae” (motor de análisis financiero de Azure) es el nombre de la carga de trabajo de TI y “use” (Este de EE. UU. 2) es la ubicación, ya que la mayoría de los clientes iniciales de Contoso se encuentran en la costa Este de Estados Unidos.
- Las cuentas de almacenamiento usan contosoazfaeusesa[descripción]. Tenga en cuenta que se agregó contoso al prefijo para proporcionar exclusividad y que los nombres de cuentas de almacenamiento no admiten el uso de guiones.
- Las redes virtuales usan AZFAE-USE-VN[número].
- Los conjuntos de disponibilidad usan azfae-use-as-[rol].
- Los nombres de máquinas virtuales usan zfae-use-vm-[nombrevm].

### Suscripciones y cuentas de Azure

Contoso usa la suscripción Enterprise, denominada Contoso Enterprise Subscription, para proporcionar la facturación de esta carga de trabajo de TI.

### Cuentas de almacenamiento

Contoso determinó que necesitaba dos cuentas de almacenamiento:

- **contosoazfaeusesawebapp** para el almacenamiento estándar de los servidores web, los servidores de aplicaciones y los controladores de dominio y sus discos de datos adicionales.
- **contosoazfaeusesasqlclust** para el almacenamiento Premium de los servidores de clústeres de SQL Server y sus discos de datos adicionales.

### Una red virtual con subredes

Dado que la red virtual no necesita una conectividad continua con la red local de Contoso, Contoso optó por una red virtual solo en la nube.

Creó una red virtual solo en la nube con la siguiente configuración a través del Portal de Azure:

- Nombre: AZFAE-USE-VN01
- Ubicación: Este de EE. UU. 2
- Espacio de direcciones de red virtual: 10.0.0.0/8
- Primera subred:
	- Nombre: FrontEnd
	- Espacio de direcciones: 10.0.1.0/24
- Segunda subred:
	- Nombre: BackEnd
	- Espacio de direcciones: 10.0.2.0/24

### Conjuntos de disponibilidad

Para mantener la alta disponibilidad de los cuatro niveles de su motor de análisis financiero, Contoso optó por cuatro conjuntos de disponibilidad:

- **azfae-use-as-dc** para los controladores de dominio
- **azfae-use-as-web** para los servidores web
- **azfae-use-as-app** para los servidores de aplicaciones
- **azfae-use-as-sql** para los servidores del clúster de SQL Server

Estos conjuntos de disponibilidad se crearán junto con las máquinas virtuales.

### Máquinas virtuales

Contoso decidió los siguientes nombres para sus máquinas virtuales de Azure:

- **azfae-use-vm-dc01** para el primer controlador de dominio
- **azfae-use-vm-dc02** para el segundo controlador de dominio
- **azfae-use-vm-web01** para el primer servidor web
- **azfae-use-vm-web02** para el segundo servidor web
- **azfae-use-vm-app01** para el primer servidor de aplicaciones
- **azfae-use-vm-app02** para el segundo servidor de aplicaciones
- **azfae-use-vm-sql01** para el primer servidor de SQL Server en el clúster de SQL Server
- **azfae-use-vm-sql02** para el segundo servidor de SQL Server en el clúster de SQL Server
- **azfae-use-vm-sqlmn01** para el testigo de nodos de mayoría en el clúster de SQL Server

Aquí está la configuración resultante.

![](./media/virtual-machines-infrastructure-services-implementation-guidelines/example-config.png)

Esta configuración incluye:

- Una red virtual solo en la nube con dos subredes (FrontEnd y BackEnd)
- Dos cuentas de almacenamiento
- Cuatro conjuntos de disponibilidad, uno para cada nivel del motor de análisis financiero
- Las máquinas virtuales para los cuatro niveles
- Un conjunto externo de carga equilibrada para el tráfico web basado en HTTPS de Internet a los servidores web
- Un conjunto interno de carga equilibrada para el tráfico web sin cifrar de los servidores web a los servidores de aplicaciones
- Un único grupo de recursos

## Recursos adicionales

[Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md#storage-limits)

[Tamaños de máquinas virtuales](virtual-machines-size-specs.md)

[Objetivos de escalabilidad y rendimiento del almacenamiento de Azure](../storage-scalability-targets.md)

[Marco de integración de plataformas en la nube (patrones de arquitectura de Azure)](../azure-architectures-cpif-overview.md)

[Diagrama de arquitectura de referencia de extensión del centro de datos](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84)

[Proveedores de proceso, red y almacenamiento de Azure en el Administrador de recursos de Azure](../articles/virtual-machines/virtual-machines-azurerm-versus-azuresm.md)

<!---HONumber=AcomDC_1203_2015-->