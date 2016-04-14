<properties
	pageTitle="Patrones de aplicaciones de SQL Server en máquinas virtuales | Microsoft Azure"
	description="En este artículo se tratan los patrones de aplicaciones para SQL Server en máquinas virtuales de Azure. Proporciona a los desarrolladores y arquitectos de soluciones una base para lograr un diseño y arquitectura adecuados de las aplicaciones."
	services="virtual-machines"
	documentationCenter="na"
	authors="Selcin"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management,azure-resource-manager" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="12/04/2015"
	ms.author="selcint" />

# Estrategias de desarrollo y patrones de aplicación de SQL Server en máquinas virtuales de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



## Resumen:
La determinación de qué patrón, o patrones, de aplicación se deben usar para las aplicaciones basadas en SQL Server en un entorno de Azure es una decisión de diseño importante y requiere una comprensión perfecta del funcionamiento conjunto de SQL Server y cada uno de los componentes de la infraestructura de Azure. Con SQL Server en los servicios de infraestructura de Azure, es posible migrar, mantener y supervisar fácilmente las aplicaciones existentes de SQL Server compiladas en Windows Server en máquinas virtuales de Azure.

El objetivo de este artículo es proporcionar a los desarrolladores y arquitectos de soluciones una base para la buena arquitectura y diseño de aplicaciones, que pueden seguir al migrar las aplicaciones existentes a Azure, así como al desarrollar nuevas aplicaciones en Azure.

Para cada patrón de aplicación, encontrará un escenario local, su respectiva solución habilitada para la nube y las recomendaciones técnicas relacionadas. Además, el artículo describe estrategias de desarrollo específicas de Azure para que pueda diseñar sus aplicaciones correctamente. Dada la gran cantidad de patrones de aplicación posibles, se recomienda que los arquitectos y desarrolladores elijan el más apropiado para sus aplicaciones y usuarios.

**Colaboradores técnicos:** Luis Carlos Vargas Herring y Madhan Arumugam Ramakrishnan

**Revisores técnicos:** Corey Sanders, Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman y Xin Jin

## Introducción

Puede desarrollar muchos tipos de aplicaciones de n niveles. Para ello, es preciso separar los componentes de las diferentes capas de aplicación en equipos distintos, así como en componentes independientes. Por ejemplo, puede colocar la aplicación cliente y los componentes de las reglas de negocios en una máquina, el nivel de front-end web y el nivel de acceso a datos en otra máquina y un nivel de base de datos back-end en otra máquina. Este tipo de estructura ayuda a aislar cada nivel del resto. Si cambia el lugar de procedencia de los datos, no es preciso cambiar la aplicación web o cliente, solo los componentes del nivel de acceso a datos.

Las aplicaciones de *n niveles* típicas incluyen el nivel de presentación, el nivel de empresa y el nivel de datos:


| Nivel: | Descripción |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Presentación** | El *nivel de presentación* (nivel de web y nivel de front-end) es la capa en la que los usuarios interactúan con las aplicaciones. |
| **Business** | El *nivel de empresa* (nivel intermedio) es la capa que el nivel de presentación y el nivel de datos usan para comunicarse entre sí e incluye la funcionalidad principal del sistema. |
| **Datos** | Básicamente, el *nivel de datos* es el servidor que almacena los datos de una aplicación (por ejemplo, un servidor que ejecuta SQL Server). |

Las capas de aplicación describen las agrupaciones lógicas de la funcionalidad y componentes de una aplicación; mientras que los niveles describen la distribución física de los componentes y funcionalidad en servidores físicos, equipos, redes o ubicaciones remotas independientes. Las capas de una aplicación pueden residir en el mismo equipo físico (el mismo nivel) o pueden estar distribuidas en equipos independientes (n niveles), y los componentes de cada capa se comunican con los de las restantes capas a través de interfaces bien definidas. El término nivel hace referencia a los patrones de distribución física, como dos niveles, tres niveles y n niveles. Un **patrón de aplicación de dos niveles** contiene dos niveles de aplicación: servidor de aplicaciones y servidor de bases de datos. La comunicación directa se produce entre el servidor de aplicaciones y el servidor de bases de datos. El servidor de aplicaciones contiene componentes del nivel de web y del nivel de empresa. En el **patrón de aplicación de tres niveles**, hay tres niveles de aplicación: servidor web, servidor de aplicaciones, que contiene el nivel de lógica empresarial y los componentes de acceso a datos del nivel de empresa, y servidor de base de datos. La comunicación entre el servidor web y el servidor de bases de datos se produce sobre el servidor de aplicaciones. Para obtener información detallada sobre los niveles y las capas de aplicación, consulte la [guía de arquitectura de aplicaciones de Microsoft](https://msdn.microsoft.com/library/ff650706.aspx).

Antes de empezar a leer este artículo, debe tener conocimientos sobre los conceptos básicos de SQL Server y Azure. Para obtener más información, consulte los [libros en pantalla de SQL Server](https://msdn.microsoft.com/library/bb545450.aspx), [SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-infrastructure-services.md) y [Azure.com](https://azure.microsoft.com/).

Este artículo describe varios patrones de aplicación que pueden ser apropiados tanto para aplicaciones sencillas como para aplicaciones empresariales de gran complejidad. Antes entrar en el detalle de cada patrón, es aconsejable que se familiarice con los servicios de almacenamiento de datos disponibles en Azure, como [Almacenamiento de Azure](../storage/storage-introduction.md), [Base de datos SQL de Azure](../sql-database/sql-database-technical-overview.md) y [SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-infrastructure-services.md). Para tomar las mejores decisiones de diseño para sus aplicaciones, es preciso que sepa a la perfección cuándo usar cada uno de los servicios de almacenamiento de datos.

### Elija SQL Server en una máquina virtuales de Azure cuando:

- Necesite control sobre SQL Server y Windows. Por ejemplo, aquí se podrían incluir la versión de SQL Server, las revisiones especiales, la configuración del rendimiento, etc.

- Necesite compatibilidad total con SQL Server local y desee mover las aplicaciones existentes a Azure tal cual.

- Desee aprovechar las funcionalidades del entorno de Azure, pero Base de datos SQL de Azure no admite todas las características que requiere la aplicación. Esto puede incluir las siguientes áreas:

	- **Tamaño de base de datos**: en el momento en que se actualizó este artículo, Base de datos SQL admite bases de datos de hasta 500 GB de datos. Si la aplicación requiere más de 500 GB de datos y no desea implementar soluciones de particionamiento personalizadas, se recomienda usar SQL Server en una máquina virtual de Azure. Para obtener la información más reciente, consulte [Ampliar Bases de datos SQL de Azure](https://msdn.microsoft.com/library/azure/dn495641.aspx) y [Niveles de servicio y niveles de rendimiento de la Base de datos SQL de Azure](../sql-database/sql-database-service-tiers.md).
	- **Cumplimiento de normas HIPAA**: los clientes de atención de la salud y fabricantes de software independientes (ISV) pueden elegir [SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-infrastructure-services.md), en lugar de [Base de datos SQL de Azure](../sql-database/sql-database-technical-overview.md), porque el contrato de asociación comercial (BAA) según las normas HIPAA cubre SQL Server en una máquina virtual de Azure. Para obtener información sobre el cumplimiento, consulte [Centro de confianza de Microsoft Azure: conformidad](https://azure.microsoft.com/support/trust-center/compliance/).
	- **Características de nivel de instancia**: actualmente, Base de datos SQL no admite las características que se encuentran fuera de la base de datos (por ejemplo, servidores vinculados, trabajos de agente, FileStream, Service Broker, etc.). Para obtener más información, consulte [Instrucciones y limitaciones de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## Un nivel (simple): máquina virtual individual

En este patrón de aplicación, se implementa la aplicación y la base de datos de SQL Server en una máquina virtual de independiente de Azure. La misma máquina virtual contiene la aplicación web/cliente, los componentes empresariales, la capa de acceso a datos y el servidor de bases de datos. La presentación, la empresa y el código de acceso a los datos están separados lógicamente, pero están ubicados físicamente en un servidor individual. La mayoría de los clientes comienzan con este patrón de aplicación y, a continuación, lo escalan horizontalmente mediante la adición de más roles web o máquinas virtuales a su sistema.

Este patrón de aplicación resulta útil cuando:

- Desee realizar una migración sencilla a la plataforma Azure para evaluar si la plataforma responde a los requisitos de la aplicación.

- Desee mantener todos los niveles de aplicación hospedados en la misma máquina virtual del mismo centro de datos de Azure para reducir la latencia entre niveles.

- Desee aprovisionar rápidamente entornos de prueba y desarrollo durante periodos breves.

- Desee realizar varias pruebas de esfuerzo con cargas de trabajo distintas, pero al mismo tiempo no se desea poseer y mantener muchas máquinas físicas continuamente.

El siguiente diagrama muestra un escenario local sencillo y cómo puede implementar su solución habilitada para la nube en una sola máquina virtual de Azure.

![Patrón de aplicación de 1 nivel](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728008.png)

Implementación de la capa de empresa (lógica de negocios y componentes de acceso a datos) en el mismo nivel físico que la capa de presentación puede maximizar el rendimiento de la aplicación, a menos deba usar un nivel independiente por problemas de escalabilidad o seguridad.

Dado que es un patrón muy común con el que empezar, puede que el siguiente artículo sobre la migración le resulte útil para mover los datos a la máquina virtual de SQL Server: [Migración de una base de datos a SQL Server en una máquina virtual de Azure](virtual-machines-migrate-onpremises-database.md).

## Tres niveles (simple): varias máquinas virtuales

En este patrón de aplicación, se implementa una aplicación de nivel 3 en Azure, para lo que debe colocar cada nivel de aplicación en una máquina virtual distinta. Esto proporciona un entorno flexible para escenarios fáciles de escalado vertical y horizontal. Cuando una máquina virtual contiene la aplicación web/cliente, la otra aloja los componentes empresariales y la tercera hospeda el servidor de bases de datos.

Este patrón de aplicación resulta útil cuando:

- Desee realizar una migración de aplicaciones de base de datos complejas a máquinas virtuales de Azure.

- Desee hospedar distintos niveles de aplicación en diferentes regiones. Por ejemplo, puede haber compartido bases de datos que se implementan en varias regiones con fines informativos.

- Desee mover aplicaciones empresariales de plataformas virtualizadas locales a máquinas virtuales de Azure. Para obtener información detallada sobre las aplicaciones empresariales, consulte [qué es una aplicación empresarial](https://msdn.microsoft.com/library/aa267045.aspx).

- Desee aprovisionar rápidamente entornos de prueba y desarrollo durante periodos breves.

- Desee realizar varias pruebas de esfuerzo con cargas de trabajo distintas, pero al mismo tiempo no se desea poseer y mantener muchas máquinas físicas continuamente.

El diagrama siguiente muestra cómo se puede colocar una sencilla aplicación de tres niveles en Azure colocando cada capa de aplicación en una máquina virtual distinta.

![Patrón de aplicación de 3 niveles](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728009.png)

En este patrón de aplicación, hay solo una máquina virtual (VM) en cada nivel. Si tiene varias máquinas virtuales en Azure, se recomienda configurar una red virtual. [Red virtual de Azure](../virtual-network/virtual-networks-overview.md) crea un límite de seguridad de confianza y también permite que las máquinas virtuales se comuniquen entre sí a través de la dirección IP privada. Además, asegúrese siempre de que todas las conexiones de Internet solo van al nivel de presentación. Esto significa que debería abrir un extremo público en el nivel de presentación, pero no en los restantes niveles. Al seguir este patrón de aplicación, también tendrá que configurar la lista de control de acceso (ACL) a la red en ese puerto público para permitir el acceso a determinadas direcciones IP. Para obtener más información, consulte [Administración de la ACL en un extremo](virtual-machines-set-up-endpoints.md/#manage-the-acl-on-an-endpoint).

En el diagrama, los protocolos de Internet puede ser TCP, UDP, HTTP o HTTPS.

>[AZURE.NOTE] La configuración de una red virtual en Azure es gratuita. Sin embargo, se cobrará la puerta de enlace de VPN que se conecta a local. Este cargo se basa en la cantidad de tiempo en que la conexión esté aprovisionada y disponible.

## Dos niveles y tres niveles con escalado horizontal del nivel de presentación

En este patrón de aplicación, se implementa una aplicación de base de datos de dos o tres niveles en máquinas virtuales de Azure, y cada nivel de la aplicación se coloca en una máquina virtual distinta. Además, el escalado horizontal del nivel de presentación se debe a un aumento de las solicitudes entrantes del cliente.

Este patrón de aplicación resulta útil cuando:

- Desee mover aplicaciones empresariales de plataformas virtualizadas locales a máquinas virtuales de Azure.

- Desee escalar horizontalmente el nivel de presentación debido a un aumento de las solicitudes entrantes del cliente.

- Desee aprovisionar rápidamente entornos de prueba y desarrollo durante periodos breves.

- Desee realizar varias pruebas de esfuerzo con cargas de trabajo distintas, pero al mismo tiempo no se desea poseer y mantener muchas máquinas físicas continuamente.

- Desee poseer un entorno de infraestructuras que se pueda escalar o reducir verticalmente a petición.

El diagrama siguiente muestra cómo se pueden colocar las capas de aplicación en varias máquinas virtuales de Azure mediante el escalado horizontal del nivel de presentación debido al mayor volumen de solicitudes entrantes del cliente. Como muestra el diagrama, el Equilibrador de carga de Azure es el responsable de distribuir el tráfico entre las distintas máquinas virtuales, así como de determinar el servidor web al que se va a realizar la conexión. La existencia de varias instancias en los servidores web detrás de un equilibrador de carga garantiza la alta disponibilidad del nivel de presentación.

![Patrón de aplicación: escalado horizontal de nivel de presentación](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728010.png)

### Prácticas recomendadas para patrones de dos, tres o n niveles que tienen varias máquinas virtuales en un solo nivel

Se recomienda colocar las máquinas virtuales que pertenecen al mismo nivel en el mismo servicio en la nube y en el mismo conjunto de disponibilidad. Por ejemplo, coloque un conjunto de servidores web en **CloudService1** y **AvailabilitySet1** y un conjunto de servidores de bases de datos en **CloudService2** y **AvailabilitySet2**. Un conjunto de disponibilidad de Azure le permite colocar los nodos de alta disponibilidad en dominios de error independientes y dominios de actualización.

Para sacar provecho de varias instancias de máquina virtual de un nivel, es preciso configurar el Equilibrador de carga de Azure entre los niveles de la aplicación. Para configurarlo en cada nivel, cree un extremo de carga equilibrada en las máquinas virtuales de cada uno de los niveles por separado. En el caso de un nivel concreto, cree primero las máquinas virtuales en el mismo servicio en la nube. Así se asegurará de que todas tienen la misma dirección IP virtual pública. A continuación, cree un extremo en una de las máquinas virtuales de dicho nivel. Seguidamente, asigne el mismo extremo a las demás máquinas virtuales de ese nivel para equilibrar la carga. Al crear un conjunto de carga equilibrada, el tráfico se distribuye entre varias máquinas virtuales y también se permite que el Equilibrador de carga determine qué nodo se debe conectar cuando se produzca un error en un nodo de la máquina virtual de back-end. Por ejemplo, la existencia de varias instancias en los servidores web detrás de un equilibrador de carga garantiza la alta disponibilidad del nivel de presentación.

Un procedimiento recomendado es asegurarse siempre de que todas las conexiones a Internet van primero al nivel de presentación. La capa de presentación obtiene acceso al nivel de empresa y, a continuación, éste accede al nivel de datos. Por ejemplo, abra un extremo en el nivel de presentación. Cada extremo cuenta con un puerto público y uno privado. La máquina virtual usa de manera interna el puerto privado para escuchar el tráfico del extremo. El puerto público es el punto de entrada para la comunicación desde fuera de Azure y lo usa el Equilibrador de carga de Azure. Se recomienda configurar la lista de control de acceso (ACL) a la red para definir reglas que ayuden a aislar y controlar el tráfico entrante en todos los puertos públicos de cualquier extremo público de todos los niveles de aplicación. Para obtener más información, consulte [Administración de la ACL en un extremo](virtual-machines-set-up-endpoints.md/#manage-the-acl-on-an-endpoint).

Tenga en cuenta que en Azure el Equilibrador de carga funciona de manera similar a los equilibradores de carga de un entorno local. Para obtener más información, consulte [Equilibrio de carga para servicios de infraestructura de Azure](virtual-machines-load-balance.md).

Además, se recomienda configurar una red privada para las máquinas virtuales mediante Red virtual de Azure. Esto les permite comunicarse entre sí a través de la dirección IP privada. Para obtener más información, consulte [Información general sobre redes virtuales](../virtual-network/virtual-networks-overview.md).

## Dos niveles y tres niveles con escalado horizontal del nivel de empresa

En este patrón de aplicación, se implementa una aplicación de base de datos de dos o tres niveles en máquinas virtuales de Azure, y cada nivel de la aplicación se coloca en una máquina virtual distinta. Además, puede distribuir los componentes del servidor de aplicaciones en varias máquinas virtuales debido a la complejidad de la aplicación.

Este patrón de aplicación resulta útil cuando:

- Desee mover aplicaciones empresariales de plataformas virtualizadas locales a máquinas virtuales de Azure.

- Desee distribuir los componentes del servidor de aplicaciones en varias máquinas virtuales debido a la complejidad de la aplicación.

- Desee mover aplicaciones de LOB (línea de negocio) locales pesadas de lógica de negocios a máquinas virtuales de Azure. Las aplicaciones de LOB son un conjunto de aplicaciones informáticas críticas que son vitales para el funcionamiento de una empresa, como la contabilidad, los recursos humanos (HR), las nóminas, la administración de la cadena de suministro y las aplicaciones de planeación de recursos.

- Desee aprovisionar rápidamente entornos de prueba y desarrollo durante periodos breves.

- Desee realizar varias pruebas de esfuerzo con cargas de trabajo distintas, pero al mismo tiempo no se desea poseer y mantener muchas máquinas físicas continuamente.

- Desee poseer un entorno de infraestructuras que se pueda escalar o reducir verticalmente a petición.

El siguiente diagrama muestra un escenario local y su solución con la nube habilitada. En este escenario, coloque los niveles de aplicación en varias máquinas virtuales en Azure mediante el escalado horizontal del nivel de empresa, que contiene el nivel de lógica de negocios y los componentes de acceso a datos. Como muestra el diagrama, el Equilibrador de carga de Azure es el responsable de distribuir el tráfico entre las distintas máquinas virtuales, así como de determinar el servidor web al que se va a realizar la conexión. La existencia de varias instancias en los servidores de aplicaciones detrás de un equilibrador de carga garantiza la alta disponibilidad del nivel de empresa. Para obtener más información, consulte [Prácticas recomendadas para patrones de dos, tres o n niveles que tienen varias máquinas virtuales en un solo nivel](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Patrón de aplicación con escalado horizontal de nivel de empresa](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728011.png)

## 2 niveles y 3 niveles con escalabilidad horizontal de los niveles de presentación y de empresa, y HADR

En este patrón de aplicación, se implementa la aplicación de base de datos de 2 niveles o 3 niveles en máquinas virtuales de Azure mediante la distribución de los componentes del nivel de presentación (servidor web) y del nivel de empresa (servidor web) en varias máquinas virtuales. Además, implemente soluciones de recuperación ante desastres y alta disponibilidad para las bases de datos en las máquinas virtuales de Azure.

Este patrón de aplicación resulta útil cuando:

- Desee mover aplicaciones empresariales de plataformas virtualizadas locales a Azure mediante la implementación de las capacidades de recuperación ante desastres y alta disponibilidad de SQL Server.

- Desee escalar horizontalmente el nivel de presentación y el nivel de empresa debido al aumento del volumen de solicitudes de cliente entrantes y a la complejidad de la aplicación.

- Desee aprovisionar rápidamente entornos de prueba y desarrollo durante periodos breves.

- Desee realizar varias pruebas de esfuerzo con cargas de trabajo distintas, pero al mismo tiempo no se desea poseer y mantener muchas máquinas físicas continuamente.

- Desee poseer un entorno de infraestructuras que se pueda escalar o reducir verticalmente a petición.

El siguiente diagrama muestra un escenario local y su solución con la nube habilitada. En este escenario, escale horizontalmente los componentes del nivel de presentación y del nivel de empresa en varias máquinas virtuales en Azure. Además, implemente técnicas de recuperación ante desastres y alta disponibilidad (HADR) para bases de datos de SQL Server en Azure.

Mediante la ejecución de varias copias de una aplicación en distintas máquinas virtuales asegúrese de que equilibra automáticamente la carga de solicitudes entre ellas. Cuando tenga varias máquinas virtuales, tiene que asegurarse de que se puede obtener acceso a todas ellas y que se ejecutan en un momento específico. Si configura el equilibrio de carga, el Equilibrador de carga de Azure realiza un seguimiento del mantenimiento de las máquinas virtuales y dirige correctamente las llamadas entrantes a los nodos de VM correctos que funcionen. Para obtener información acerca de cómo configurar el equilibrio de carga de las máquinas virtuales, consulte [Equilibrio de carga para servicios de infraestructura de Azure](virtual-machines-load-balance.md). La existencia de varias instancias en los servidores web y de aplicaciones detrás de un equilibrador de carga garantiza la alta disponibilidad de los niveles de presentación y de empresa.

![Escalado horizontal y alta disponibilidad](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728012.png)

### Prácticas recomendadas para patrones de aplicación que requieren HADR de SQL

Al configurar las soluciones de alta disponibilidad y recuperación ante desastres de SQL Server en máquinas virtuales de Azure, es obligatorio configurar una red virtual para las máquinas virtuales mediante [Red virtual de Azure](../virtual-network/virtual-networks-overview.md). Las máquinas virtuales de una red virtual tienen una dirección IP privada estable incluso después de un tiempo de inactividad del servicio, por lo que puede evitar el tiempo de actualización necesario para la resolución de nombres DNS. Además, la red virtual le permite ampliar la red local a Azure y crea un límite de seguridad de confianza. Por ejemplo, si la aplicación tiene restricciones de dominio corporativo (como autenticación de Windows o Active Directory), es preciso configurar [Red virtual de Azure](../virtual-network/virtual-networks-overview.md).

La mayoría de los clientes, que ejecutan código de producción en Azure, conservan las replicas principal y secundaria en Azure.

Para obtener información completa y tutoriales sobre las técnicas de alta disponibilidad y recuperación ante desastres, consulte [Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).

## Dos niveles y tres niveles con máquinas virtuales de Azure y servicios en la nube

En este patrón de aplicación, se implementa una aplicación de dos o tres niveles en Azure mediante [Servicios en la nube de Azure](cloud-services-choose-me.md#tellmecs) (roles web y de trabajo: plataforma como servicio (PaaS)) y [Máquinas virtuales de Azure](virtual-machines/) (infraestructura como servicio (IaaS)). El uso de [Servicios en la nube de Azure](https://azure.microsoft.com/documentation/services/cloud-services/) para los niveles de presentación y de empresa, y SQL Server en [Máquinas virtuales de Azure](virtual-machines-about.md) para el nivel de datos, es beneficioso para la mayoría de las aplicaciones que se ejecutan en Azure. El motivo es que el hecho de que una instancia de proceso se ejecute en Servicios en la nube facilita la administración, implementación, supervisión y escalado horizontal.

Con Servicios en la nube, Azure mantiene automáticamente la infraestructura, realiza un mantenimiento periódico, aplica parches a los sistemas operativos e intenta recuperarse de los errores de hardware y del servicio. Cuando la aplicación necesita escalado horizontal, hay disponibles opciones de escalado horizontal manual y automático para el proyecto del servicio en la nube mediante el aumento o disminución del número de instancias o máquinas virtuales que se usa la aplicación. Además, se puede usar Visual Studio local para implementar la aplicación en un proyecto de servicio en la nube en Azure.

En resumen, si no desea tener numerosas tareas administrativas para el nivel de presentación o empresa, y la aplicación no requiere una configuración compleja del software o del sistema operativo, use Servicios en la nube de Azure. Si Base de datos SQL de Azure no admite todas las características que busca, use SQL Server en una máquina virtual de Azure para el nivel de datos. La ejecución de una aplicación en Servicios en la nube de Azure y el almacenamiento de datos en Máquinas virtuales de Azure combina las ventajas de ambos servicios. Para obtener una comparación detallada, consulte la sección de este tema [Comparación de las estrategias de desarrollo de Azure](#comparing-web-development-strategies-in-azure).

En este patrón de aplicación, el nivel de presentación incluye un rol web, que es un componente de Servicios en la nube que se ejecuta en el entorno de ejecución de Azure y se personaliza para la programación de aplicaciones web que admiten IIS y ASP.NET. El nivel de empresa o back-end incluye un rol de trabajo, que es un componente de Servicios en la nube que se ejecuta en el entorno de ejecución de Azure y es útil para el desarrollo generalizado, y puede realizar un procesamiento en segundo plano en un rol web. El nivel de base de datos reside en una máquina virtual de SQL Server en Azure. La comunicación entre el nivel de presentación y el nivel de base de datos se establece directamente o a través de los componentes del nivel de empresa: rol de trabajo.

Este patrón de aplicación resulta útil cuando:

- Desee mover aplicaciones empresariales de plataformas virtualizadas locales a Azure mediante la implementación de las capacidades de recuperación ante desastres y alta disponibilidad de SQL Server.

- Desee poseer un entorno de infraestructuras que se pueda escalar o reducir verticalmente a petición.

- Base de datos SQL de Azure no admite todas las características que necesitan la aplicación o la base de datos.

- Desee realizar varias pruebas de esfuerzo con cargas de trabajo distintas, pero al mismo tiempo no se desea poseer y mantener muchas máquinas físicas continuamente.

El siguiente diagrama muestra un escenario local y su solución con la nube habilitada. En este escenario, el nivel de presentación se coloca en los roles web, el nivel de empresa en los roles de trabajo y el nivel de datos en máquinas virtuales de Azure. La ejecución de varias copias de la capa de presentación en distintos roles web garantiza el equilibro de carga de las solicitudes entre ellas. Al combinar Servicios en la nube de Azure con Máquinas virtuales de Azure, es aconsejable configurar también [Red virtual de Azure](../virtual-network/virtual-networks-overview.md). Con [Red virtual de Azure](../virtual-network/virtual-networks-overview.md), puede tener direcciones IP privadas estables y persistentes dentro del mismo servicio en la nube. Una vez que se define una red virtual para las máquinas virtuales y los servicios en la nube, pueden empezar a comunicarse entre sí a través de la dirección IP privada. Además, tener máquinas virtuales y roles de web y trabajo de Azure en la misma [Red virtual de Azure](../virtual-network/virtual-networks-overview.md) proporciona una conectividad más segura y baja latencia. Para obtener más información, consulte [Cálculo de las opciones de hospedaje proporcionadas por Azure](../cloud-services/fundamentals-application-models.md).

Como muestra el diagrama, el Equilibrador de carga de Azure distribuye el tráfico entre varias máquinas virtuales y determina el servidor web o el servidor de aplicaciones al que se realiza la conexión. La existencia de varias instancias en los servidores web y de aplicaciones detrás de un equilibrador de carga garantiza la alta disponibilidad de los niveles de presentación y de empresa. Para obtener más información, consulte [Prácticas recomendadas para patrones de aplicación que requieren HADR de SQL](#best-practices-for-application-patterns-requiring-sql-hadr).

![Patrones de aplicaciones con servicios en la nube](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728013.png)

Otro enfoque para implementar este patrón de aplicación es usar un rol web consolidado que contenga componentes tanto del nivel de presentación como del nivel de empresa, como se muestra en el siguiente diagrama. Este patrón de aplicación es útil para aplicaciones que requieren diseño con estado. Puesto que Azure proporciona nodos de proceso sin estado en los roles web y de trabajo, se recomienda implementar una lógica para almacenar el estado de la sesión mediante una de las siguientes tecnologías: [Caché de Azure](https://azure.microsoft.com/documentation/services/redis-cache/), [Almacenamiento de tablas de Azure](../storage/storage-dotnet-how-to-use-tables.md) o [Base de datos SQL de Azure](../sql-database/sql-database-technical-overview.md).

![Patrones de aplicaciones con servicios en la nube](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728014.png)

## Patrón con VM de Azure, Base de datos SQL de Azure y Aplicaciones web de Azure

El objetivo principal de este patrón de aplicación es mostrarle cómo combinar los componentes de infraestructura como servicio (IaaS) de Azure con los componentes de plataforma como servicio (PaaS) de Azure en una solución. Este patrón se centra en Base de datos SQL de Azure para el almacenamiento de datos relacionales. No incluye SQL Server en una máquina virtual de Azure, que forma parte de la infraestructura de Azure como oferta de servicio.

En este patrón de aplicación, se implementa una aplicación de base de datos en Azure mediante la colocación de los niveles de presentación y de empresa en la misma máquina virtual y la obtención de acceso a una base de datos en los servidores de Base de datos SQL de Azure (Base de datos SQL). El nivel de presentación mediante se puede implementar con soluciones web tradicionales basadas en IIS. O bien, se puede implementar un nivel combinado de presentación y de empresa con [Aplicaciones web de Azure](https://azure.microsoft.com/documentation/services/app-service/web/).

Este patrón de aplicación resulta útil cuando:

- Ya se dispone de un servidor de Base de datos SQL configurado en Azure y se desea probar la aplicación rápidamente.

- Desee probar las capacidades del entorno de Azure.

- Desee aprovisionar rápidamente entornos de prueba y desarrollo durante periodos breves.

- Los componentes de acceso a datos y lógica de negocios pueden ser independientes dentro de una aplicación web.

El siguiente diagrama muestra un escenario local y su solución con la nube habilitada. En este escenario, los niveles de aplicación se colocan en una única máquina virtual en Azure y se accede a los datos en Base de datos SQL de Azure.

![Patrón de aplicación mixto](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728015.png)

Si elige implementar un nivel de aplicación y web combinado mediante el uso de Aplicaciones web de Azure, se recomienda que mantenga el nivel intermedio o nivel de aplicación como bibliotecas de vínculos dinámicos (DLL) en el contexto de una aplicación web.

Además, para obtener más información acerca de las técnicas de programación, consulte las recomendaciones que se proporcionan en la sección [Comparación de las estrategias de desarrollo web de Azure](#comparing-web-development-strategies-in-azure), que encontrará al final de este artículo.

## Patrón de aplicación híbrido de n niveles

En el patrón de aplicación híbrido de n niveles, la aplicación se implementa en varios niveles distribuidos entre local y Azure. Por lo tanto, se crea un sistema híbrido flexible y reusable, que se puede modificar o en el que se puede agregar un nivel específico sin cambiar los demás. Para expandir la red corporativa a la nube, se usa el servicio [Red virtual de Azure](../virtual-network/virtual-networks-overview.md).

Este patrón de aplicación híbrido resulta útil cuando:

- Desee crear aplicaciones que se ejecuten parcialmente en la nube y parcialmente en local.

- Desee migrar algunos los elementos de una aplicación local existente, o todos ellos, a la nube.

- Desee mover aplicaciones empresariales de plataformas virtualizadas locales a Azure.

- Desee poseer un entorno de infraestructuras que se pueda escalar o reducir verticalmente a petición.

- Desee aprovisionar rápidamente entornos de prueba y desarrollo durante periodos breves.

- Desee realizar copias de seguridad de una forma rentable de las aplicaciones de base de datos empresariales.

El siguiente diagrama muestra un patrón de aplicación híbrido de n niveles que abarca la instalación local y Azure. Como se muestra en el diagrama, la infraestructura local incluye un controlador de dominio de [Servicios de dominio de Active Directory](https://technet.microsoft.com/library/hh831484.aspx) que permite realizar la autenticación y autorización de usuarios. Tenga en cuenta que el diagrama muestra un escenario en el que algunas partes del nivel de datos se encuentran en un centro de datos local, mientras otras se encuentran en Azure. En función de las necesidades de la aplicación, puede implementar otros escenarios híbridos. Por ejemplo, puede mantener el nivel de presentación y el nivel de empresa en un entorno local y el nivel de datos en Azure.

![Patrón de aplicación de n niveles](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728016.png)

En Azure, Active Directory se puede usar como directorio en la nube independiente para una organización, o bien se pueden integrar Active Directory local y [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Como se muestra en el diagrama, los componentes del nivel de empresa pueden tener acceso a varios orígenes de datos, como a [SQL Server en Azure](virtual-machines-sql-server-infrastructure-services.md) a través de una dirección IP interna privada, a SQL Server local a través de [Red virtual de Azure](../virtual-network/virtual-networks-overview.md) o a [Base de datos SQL](../sql-database/sql-database-technical-overview) con las tecnologías de proveedor de datos de .NET Framework. En este diagrama, Base de datos SQL de Azure es un servicio de almacenamiento de datos opcional.

En el patrón de aplicación híbrido de n niveles, se puede implementar el siguiente flujo de trabajo en el orden especificado:

1. Identifique las aplicaciones de base de datos de empresa que deben moverse a la nube, para lo que debe usar [Microsoft Assessment and Planning (MAP) Toolkit](http://microsoft.com/map). MAP Toolkit recopila datos de rendimiento y de inventario de los equipos que está considerando para la virtualización y ofrece recomendaciones sobre la capacidad y el planeamiento de la evaluación.

1. Planee los recursos y la configuración necesarios en la plataforma Azure, como cuentas de almacenamiento y máquinas virtuales.

1. Configure la conectividad de red entre la red corporativa local y [Red virtual de Azure](../virtual-network/virtual-networks-overview.md). Para configurar la conexión entre la red corporativa local y una máquina virtual de Azure, use uno de los dos métodos siguientes:

	1. Establezca una conexión entre local y Azure a través de extremos públicos en una máquina virtual de Azure. Este método ofrece una configuración sencilla y permite usar autenticación de SQL Server en la máquina virtual. Además, configure la lista de control de acceso (ACL) a la red en los puertos públicos para permitir el acceso a determinadas direcciones IP. Para obtener más información, consulte [Administración de la ACL en un extremo](virtual-machines-set-up-endpoints.md/#manage-the-acl-on-an-endpoint).

	1. Establezca una conexión entre local y Azure a través de un túnel de la red privada virtual (VPN) de Azure. Este método permite extender las directivas del dominio a una máquina virtual de Azure. Además, puede configurar las reglas del firewall y usar la autenticación de Windows en la máquina virtual. En la actualidad, Azure admite VPN segura de sitio a sitio y conexiones VPN de punto a sitio:

		- Con una conexión segura de sitio a sitio puede establecer conectividad de red entre una red local y una red virtual de Azure. Se recomienda para conectar un entorno de centro de datos local a Azure.

		- Con una conexión segura de punto a sitio puede establecer conectividad de red entre una red virtual de Azure y los equipos individuales, independientemente de dónde se encuentren. Se recomienda principalmente para desarrollo y pruebas.

	Para obtener información sobre cómo conectarse a SQL Server en Azure, consulte [Conexión a una máquina virtual de SQL Server en Azure](virtual-machines-sql-server-connectivity.md).

1. Configure las alertas y trabajos programados que realizarían las copias de seguridad de los datos locales en un disco de una máquina virtual de Azure. Para obtener más información, consulte [Copia de seguridad y restauración de SQL Server con el servicio de almacenamiento Blob de Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx) y [Copias de seguridad y restauración para SQL Server en Máquinas virtuales de Azure](virtual-machines-sql-server-backup-and-restore.md).

1. Según las necesidades de la aplicación, puede implementar uno de los tres escenarios comunes siguientes:

	1. Puede mantener el servidor web, el servidor de aplicaciones e información no confidencial en un servidor de bases de datos de Azure, pero mantener la información confidencial de forma local.

	1. Puede mantener el servidor web y el servidor de aplicaciones de forma local, pero el servidor de bases de datos en una máquina virtual de Azure.

	1. Puede mantener el servidor de bases de datos, el servidor web y el servidor de aplicaciones de forma local, pero las réplicas de las bases de datos en una máquina virtual de Azure. Esta configuración permite a los servidores de web o a las aplicaciones de informes locales obtener acceso a las réplicas de las bases de datos de Azure. Por consiguiente, se puede conseguir reducir la carga de trabajo de una base de datos local. Se recomienda implementar este escenario tanto para cargas de trabajo en que se realiza mucha lectura como para fines de desarrollo. Para obtener información sobre la creación de réplicas de bases de datos en Azure, consulte la información sobre los grupos de disponibilidad AlwaysOn en [Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).

## Comparación de las estrategias de desarrollo web de Azure

Para implementar y distribuir en Azure una aplicación de niveles múltiples basada en SQL Server, puede usar uno de los dos métodos de programación siguientes:

- Configurar un servidor web tradicional (IIS - Internet Information Services) en Azure y acceder a las bases de datos de SQL Server en Máquinas virtuales de Azure.

- Implementar un servicio en la nube en Azure. A continuación, asegúrese de que este servicio en la nube puede obtener acceso a las bases de datos de SQL Server en Máquinas virtuales de Azure. Los servicios en la nube pueden incluir varios roles web y de trabajo.

La tabla siguiente proporciona una comparación del desarrollo web tradicional con Servicios en la nube de Azure y Aplicaciones web de Azure con respecto a SQL Server en Máquinas virtuales de Azure. La tabla incluye Aplicaciones web de Azure, ya que SQL Server se puede usar en VM de Azure como origen de datos de Aplicaciones web de Azure a través de su dirección IP virtual pública o el nombre DNS.

|Desarrollo web tradicional en Máquinas virtuales de Azure|Servicios en la nube de Azure|Hospedaje web con Aplicaciones web de Azure|
|---|---|---|---|
|**Migración de aplicaciones desde el sistema local**|Las aplicaciones existentes tal cual.|Las aplicaciones necesitan roles web y de trabajo.|Las aplicaciones existentes tal cual, pero adecuadas para aplicaciones web independientes y servicios web que requieren una escalabilidad rápida.|
|**Desarrollo e implementación**|Visual Studio, WebMatrix, Visual Web Developer, Web Deploy, FTP, TFS, Administrador de IIS y PowerShell.|Visual Studio, SDK de Azure, TFS y PowerShell. Cada servicio en la nube tiene dos entornos en los que se pueden implementar el paquete y la configuración del servicio: almacenamiento provisional y producción. Puede implementar un servicio en la nube en el entorno de ensayo para probarlo antes de pasar a producción.|Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, Web Deploy y PowerShell.|
|**Administración y operaciones**|El usuario es el responsable de las tareas administrativas de la aplicación, los datos, las reglas del firewall, la red virtual y el sistema operativo.|El usuario es el responsable de las tareas administrativas de la aplicación, los datos, las reglas del firewall y la red virtual.|El usuario es el responsable solo de las tareas administrativas de la aplicación y los datos.|
|**Alta disponibilidad y recuperación ante desastres (HADR):**|Se recomienda colocar las máquinas virtuales en el mismo conjunto de disponibilidad y en el mismo servicio en la nube. El hecho de mantener las máquinas virtuales en el mismo conjunto de disponibilidad permite a Azure colocar los nodos de alta disponibilidad en dominios de error independientes y dominios de actualización. Del mismo modo, mantener las máquinas virtuales en el mismo servicio en la nube permite el equilibrio de carga y las máquinas virtuales pueden comunicarse directamente entre sí a través de la red local de un centro de datos de Azure.<br/><br/>El usuario es el responsable de implementar una solución de alta disponibilidad y recuperación ante desastres para SQL Server en Máquinas virtuales de Azure para evitar que haya tiempos de inactividad. Para conocer las tecnologías compatibles con HADR, consulte [Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).<br/><br/>El usuario es el responsable de la realización de copias de seguridad de sus propios datos y de la aplicación.<br/><br/>Azure puede mover las máquinas virtuales si se produce un error en el equipo host del centro de datos debido a problemas de hardware. Además, podría haber un tiempo de inactividad planeado de la máquina virtual cuando el equipo host se actualiza por motivos de seguridad o para realizar actualizaciones del software. Por lo tanto, se recomienda mantener al menos dos máquinas virtuales en cada nivel de aplicación para garantizar la disponibilidad continua. Azure no proporciona SLA para máquinas virtuales individuales. Para obtener más información, consulte [Orientación técnica de la continuidad del negocio de Azure](https://msdn.microsoft.com/library/azure/hh873027.aspx).|Azure administra los errores debidos al hardware subyacente o al software del sistema operativo. Para garantizar la alta disponibilidad de la aplicación, se recomienda implementar varias instancias de un rol web o de trabajo. Para obtener información, consulte [Contrato de nivel de servicio de Servicios en la nube, Máquinas virtuales y Red virtual](http://www.microsoft.com/download/details.aspx?id=38427) y [Consideraciones sobre la alta disponibilidad y la recuperación ante desastres para las aplicaciones de Azure](https://msdn.microsoft.com/library/azure/dn251004.aspx)<br/><br/>El usuario es el responsable de la realización de copias de seguridad de sus propios datos y de la aplicación.<br/><br/>En el caso de las bases de datos que residen en una base de datos de SQL Server de una máquina virtual de Azure, el usuario es el responsable de implementar una solución de alta disponibilidad y recuperación ante desastres para evitar los tiempos de inactividad. Para conocer las tecnologías HDAR compatibles, consulte Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure.<br/><br/>**Reflejo de la base de datos de SQL Server**: uso con Servicios en la nube de Azure (roles web/de trabajo). Las máquinas virtuales de SQL Server y un proyecto de servicio en la nube pueden estar en la misma red virtual de Azure. Si la máquina virtuales de SQL Server no está en la misma red virtual, necesitará crear un alias de SQL Server para enrutar la comunicación a la instancia de SQL Server. Además, el nombre de alias debe coincidir con el nombre de SQL Server.|La alta disponibilidad se hereda de los roles de trabajo de Azure, del almacenamiento de blobs de Azure y de la Base de datos SQL de Azure. Por ejemplo, Almacenamiento de Azure mantiene tres réplicas de todos los datos de los blobs, tablas y cola. En cualquier momento, Base de datos SQL de Azure mantiene tres réplicas de los datos en ejecución (una réplica principal y dos réplicas secundarias). Para obtener más información, consulte [Almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/) e [Información general de Base de datos SQL](../sql-database/sql-database-technical-overview.md).<br/><br/>Al usar SQL Server en VM de Azure como origen de datos para Aplicaciones web de Azure, tenga en cuenta que Aplicaciones web de Azure no admite Red virtual de Azure. En otras palabras, todas las conexiones de Aplicaciones web de Azure a máquinas virtuales de SQL Server en Azure deben atravesar los extremos públicos de máquinas virtuales. Esto puede provocar algunas limitaciones en escenarios de alta disponibilidad y recuperación ante desastres. Por ejemplo, la aplicación de cliente de Aplicaciones web de Azure que se conecta a VM de SQL Server con la creación de reflejo de base de datos no podría conectarse al nuevo servidor principal, ya que la creación de reflejo de base de datos requiere que se configure Red virtual de Azure entre máquinas virtuales host de SQL Server en Azure. Por consiguiente, en la actualidad no se admite el uso de **Reflejo de la base de datos de SQL Server** con Aplicaciones web de Azure.<br/><br/>** Grupos de disponibilidad AlwaysOn de SQL Server**: cuando use Aplicaciones web de Azure con máquinas virtuales de SQL Server en Azure, puede configurar grupos de disponibilidad AlwaysOn. Sin embargo, debe configurar el agente de escucha del grupo de disponibilidad AlwaysOn para enrutar la comunicación a la réplica principal a través de puntos de conexión públicos de carga equilibrada.|
|**Conectividad entre sitios**|Puede usar la red virtual de Azure para conectarse a entornos locales.|Puede usar la red virtual de Azure para conectarse a entornos locales.|Se admite Red virtual de Azure. Para obtener más información, consulte [Azure Websites Virtual Network Integration (Integración de red virtual de aplicaciones web)](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/).|
|**Escalabilidad**|El escalado vertical está disponible mediante el aumento del tamaño de las máquinas virtuales o la incorporación de más discos. Para obtener más información acerca de los tamaños de las máquinas virtuales, consulte [Tamaños de máquinas virtuales](virtual-machines-size-specs.md).<br/><br/>**Para servidor de bases de datos**: el escalado horizontal está disponible a través de las técnicas de creación de particiones de bases de datos y los grupos de disponibilidad AlwaysOn de SQL Server.<br/><br/>Para cargas de trabajo con mucha lectura, puede usar [Grupos de disponibilidad AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) en varios nodos secundarios, así como Replicación de SQL Server.<br/><br/>Para cargas de trabajo con mucha escritura, puede implementar datos de creación de particiones horizontales en varios servidores físicos para proporcionar escalado horizontal de las aplicaciones.<br/><br/>Además, puede implementar un escalado horizontal mediante [SQL Server con enrutamiento dependiente de los datos](https://technet.microsoft.com/library/cc966448.aspx). Con el enrutamiento dependiente de los datos (DDR), necesitará implementar el mecanismo de creación de particiones en la aplicación cliente, normalmente en la capa del nivel de empresa, para enrutar las solicitudes de base de datos a varios nodos de SQL Server. El nivel de empresa contiene asignaciones para ver cómo se particionan los datos y qué nodo contiene los datos.<br/><br/>Puede escalar las aplicaciones que ejecutan máquinas virtuales. Para obtener más información, consulte [Escalado automático de una aplicación](../cloud-services/cloud-services-how-to-scale.md).<br/><br/>**Nota importante**: la función **Escalado automático** de Azure permite aumentar o reducir el número de máquinas virtuales que usa la aplicación. Esta función garantiza que la experiencia del usuario final no se ve afectada negativamente durante los períodos de máxima actividad y que las máquinas virtuales se apagan cuando la demanda sea baja. Se recomienda no configurar la opción de escalado automático para el servicio en la nube si este incluye máquinas virtuales de SQL Server. Esto se debe a que la función de escalado automático permite a Azure activar una máquina virtual cuando el uso de la CPU en dicha máquina virtual es superior a un umbral y apagar una máquina virtual cuando el uso de la CPU no llega a dicho umbral. La función de escalado automático es útil para las aplicaciones sin estado, como servidores web, donde cualquier máquina virtual puede administrar la carga de trabajo sin ninguna referencia a los estados anteriores. Sin embargo, la función de escalado automático no es útil para las aplicaciones con estado, como SQL Server, donde solo una instancia permite escribir en la base de datos.|El escalado vertical está disponible mediante el uso de varios roles web y de trabajo. Para obtener más información acerca de los tamaños de las máquinas virtuales tanto para los roles web como para los roles de trabajo, consulte [Tamaños de los servicios en la nube](../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Al usar **Servicios en la nube**, se pueden definir varios roles para distribuir el procesamiento y conseguir un escalado flexible de la aplicación. Cada servicio en la nube incluye uno o varios roles web o roles de trabajo, cada uno con sus propios archivos de aplicación y configuración. Para escalar verticalmente un servicio en la nube, aumente el número de instancias de rol (máquinas virtuales) que se implementan para un rol, mientras que para reducirlo verticalmente, debe reducir el número de instancias de rol. Para obtener más información, consulte [Cálculo de las opciones de hospedaje proporcionadas por Azure](fundamentals-application-models.md).<br/><br/>El escalado horizontal está disponible a través de la compatibilidad integrada de alta disponibilidad de Azure a través de [servicios en la nube, máquinas virtuales y contrato de nivel de servicio de red virtual](http://www.microsoft.com/download/details.aspx?id=38427), y el equilibrador de carga.<br/><br/>En una aplicación de niveles múltiples, se recomienda conectar la aplicación de roles web y de trabajo a las máquinas virtuales del servidor de bases de datos a través de Red virtual de Azure. Además, Azure proporciona equilibro de carga para las máquinas virtuales en el mismo servicio en la nube, lo que significa que distribuye las solicitudes de usuario entre ellas. Las máquinas virtuales conectadas de esta forma se pueden comunicar directamente entre ellas a través de la red local dentro de un centro de datos de Azure.<br/><br/>La función **Escalado automático** se puede configurar en el Portal de Azure clásico, así como las horas de programación. Para obtener más información, consulte [Escalado automático de una aplicación](../cloud-services/cloud-services-how-to-scale.md).|**Cómo realizar el escalado de una aplicación:** puede aumentar o disminuir el tamaño de la instancia (máquina virtual) reservada para un sitio web.<br/><br/>Escalado horizontal: puede agregar más instancias reservadas (máquinas virtuales) a un sitio web.<br/><br/>Puede configurar la función **Escalado automático** en el Portal, así como las horas de programación. Para obtener más información, consulte [Escalado de aplicaciones web](../app-service-web/web-sites-scale.md).|

Para obtener más información sobre cuál de estos métodos de programación elegir, consulte [Comparación entre el Servicio de aplicaciones de Azure, Servicios en la nube y Máquinas virtuales](../app-service-web/choose-web-site-cloud-service-vm.md).

## Pasos siguientes

Para obtener más información sobre cómo ejecutar SQL Server en Máquinas virtuales de Azure, consulte [Información general sobre SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_0128_2016-->