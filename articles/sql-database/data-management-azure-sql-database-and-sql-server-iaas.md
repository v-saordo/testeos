<properties
	pageTitle="Base de datos (PaaS) SQL y SQL Server en la nube en máquinas virtuales (IaaS) | Microsoft Azure"
	description="Obtenga información acerca de qué opción de SQL Server en la nube se ajusta a su aplicación: Base de datos (PaaS) SQL de Azure o SQL Server en la nube en Máquinas virtuales de Azure."
	services="sql-database, virtual-machines"
	keywords="Nube de SQL Server, SQL Server en la nube, base de datos de PaaS, DBaaS"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor="cjgronlund"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/02/2016"
	ms.author="jeffreyg"/>

# Selección de una opción de SQL Server en la nube: Base de datos (PaaS) SQL de Azure o SQL Server en máquinas virtuales de Azure (IaaS)

Azure proporciona dos opciones para el hospedaje de las cargas de trabajo de SQL Server en la nube:

* [Base de datos SQL de Azure](https://azure.microsoft.com/services/sql-database/): una base de datos de SQL nativa en la nube, conocida también como base de datos de plataforma como servicio (PaaS) o base de datos como servicio (DBaaS), que está optimizada para el desarrollo de aplicaciones de software como servicio (SaaS). Ofrece compatibilidad con la mayoría de las características de SQL Server.
* [SQL Server en Máquinas virtuales de Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/): SQL Server instalado y hospedado en la nube en Máquinas virtuales de Windows Server que se ejecutan en Azure, conocido también como infraestructura como servicio (IaaS).

Obtenga información acerca de qué opción se ajusta a la plataforma de datos de Microsoft y consiga ayuda sobre la opción más adecuada para sus requisitos empresariales. Si asigna mayor prioridad al ahorro o bien antepone la mínima administración a todo lo demás, este artículo puede ayudarle a decidir el enfoque correcto, en función de los requisitos empresariales que más le preocupan.


## Plataforma de datos de Microsoft

Una de las primeras cosas que hay que comprender al comparar Azure con bases de datos SQL Server locales es que puede usarlas todas. La plataforma de datos de Microsoft aprovecha la tecnología de SQL Server y la pone a disposición de los usuarios en máquinas físicas locales, entornos en nubes privadas, entornos en nubes privadas hospedados por terceros y la nube pública. Esto permite satisfacer necesidades únicas y diversas del negocio mediante una combinación de implementaciones locales y hospedadas en nube al tiempo que se usa el mismo conjunto de productos de servidor, herramientas de desarrollo y conocimientos en todos estos entornos.

   ![Opciones de SQL Server en la nube: SQL Server en IaaS o base de datos SQL de SaaS en la nube.](./media/data-management-azure-sql-database-and-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Como se ve en el diagrama, cada oferta puede caracterizarse por el nivel de administración que se tiene sobre la infraestructura (en el eje X) y el grado de relación coste-eficacia obtenido mediante la automatización y la consolidación en el nivel de base de datos (en el eje Y).

Al diseñar una aplicación, existen cuatro opciones básicas para el hospedaje de la parte de SQL Server de la aplicación:

- SQL Server en máquinas físicas no virtualizadas
- SQL Server en máquinas locales virtualizadas (nube privada)
- SQL Server en máquinas virtuales de Azure (nube pública)
- Base de datos SQL de Azure (nube pública)

En las secciones siguientes, se obtendrá información sobre SQL Server en la nube pública: Base de datos SQL de Azure y SQL Server en Máquinas virtuales de Azure. Además, exploraremos factores de motivación comunes del negocio para determinar la opción que mejor funciona en el caso de la aplicación.

## Base de datos SQL de Azure y SQL Server en Máquinas virtuales de Azure en detalle

**Base de datos SQL de Azure** es una base de datos relacional como servicio (DBaaS), hospedada en la nube de Azure, que se engloba en las categorías del sector denominadas *Software como servicio (SaaS)* y *Plataforma como servicio (PaaS)*. Base de datos SQL se compila en hardware y software estandarizados que Microsoft posee, hospeda y mantiene. Con Base de datos SQL, podrá desarrollar directamente en el servicio mediante funcionalidad y características integradas. Al utilizar Base de datos SQL, se emplea el método de pago por uso de opciones para escalar vertical u horizontalmente a fin de aumentar la potencia de forma ininterrumpida.

**SQL Server en Máquinas virtuales de Azure** que se engloba en la categoría del sector denominada *Infraestructura como servicio (IaaS)* y permite ejecutar SQL Server en una máquina virtual en la nube. De manera similar a Base de datos SQL, se compila en hardware estandarizado que Microsoft posee, hospeda y mantiene. Si usa SQL Server en una máquina virtual, puede incorporar su propia licencia de SQL Server o utilizar una imagen con licencia previa de SQL Server del Portal de Azure.

Por lo general, estas dos opciones SQL se optimizan con diferentes fines:

- **Base de datos SQL** se optimiza para reducir al mínimo los costos generales en cuanto al aprovisionamiento y la administración de varias bases de datos. Reduce los costos corrientes de administración porque no es necesario administrar máquinas virtuales, el sistema operativo ni el software de base de datos. Esto incluye las actualizaciones, la alta disponibilidad y las copias de seguridad. Habitualmente, Base de datos SQL de Azure puede aumentar considerablemente el número de bases de datos administradas por un solo recurso de TI o de desarrollo.
- **Ejecución de SQL Server en Máquinas virtuales de Azure** se optimiza para ampliar aplicaciones existentes de SQL Server locales a la nube en un escenario híbrido o para implementar una aplicación existente en Azure en un escenario de migración o de desarrollo/pruebas. Un ejemplo del escenario híbrido es el mantenimiento de réplicas de la base de datos secundaria en Azure mediante [Redes virtuales de Azure](../virtual-network/virtual-networks-overview.md). Con SQL Server en Máquinas virtuales de Azure, tiene todos los derechos administrativos sobre una instancia dedicada de SQL Server y una máquina virtual basada en la nube. Es la elección perfecta cuando una organización ya dispone de recursos de TI para mantener las máquinas virtuales. Con SQL Server en máquinas virtuales, puede compilar un sistema muy personalizado para abordar los requisitos de rendimiento y disponibilidad específicos de la aplicación.

En la siguiente tabla se resumen las principales características de Base de datos SQL y SQL Server en Máquinas virtuales de Azure:

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle"></th>
   <th align="left" valign="middle">Base de datos SQL</th>
   <th align="left" valign="middle">SQL Server en máquinas virtuales de Azure</th>

</tr>
<tr>
   <td valign="middle"><p><b>Más adecuado para</b></p></td>
   <td valign="middle">
          <ul>
          <li type=round>Nuevas aplicaciones diseñadas para la nube que tienen restricciones de tiempo en desarrollo y marketing.
          <li type=round>Aplicaciones que requieren alta disponibilidad, recuperación ante desastres y mecanismos de actualización integrados.
          <li type=round>Equipos que no desean administrar el sistema operativo subyacente ni los valores de configuración.
         <li type=round>Aplicaciones que usan patrones de escalado horizontal.
         <li type=round>Bases de datos de 1&#160;TB de tamaño como máximo.
         <li type=round>Compilación de aplicaciones de software como servicio (SaaS).
  </ul>
</td>
   <td valign="middle">
      <ul>
      <li type=round>Aplicaciones existentes que requieren una rápida migración a la nube con el mínimo de cambios.
      <li type=round>Aplicaciones de SQL Server que requieren acceso a recursos locales (como, por ejemplo, Active Directory) desde Azure a través de un túnel seguro.
      <li type=round>Cuando se requiere un entorno de TI personalizado con derechos administrativos completos.
      <li type=round>Escenarios de desarrollo rápido y pruebas cuando no se desea comprar hardware de SQL Server de no producción local.
      <li type=round>Recuperación ante desastres para aplicaciones de SQL Server locales mediante [Copia de seguridad y restauración de SQL Server con el servicio de almacenamiento Blob de Microsoft Azure](http://msdn.microsoft.com/library/jj919148.aspx) o [réplicas AlwaysOn en Máquinas virtuales de Azure](../virtual-machines/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).
      <li type=round>Bases de datos grandes con más de 1&#160;TB de tamaño.
      </ul></td>
</tr>
<tr>
   <td valign="middle"><p><b>Recursos</b></p></td>
   <td valign="middle">
       <ul>
       <li type=round>No se desea emplear recursos de TI para el soporte y mantenimiento de la infraestructura subyacente.
       <li type=round>Se desea centrarse en el nivel de aplicación.
       </ul></td>
   <td valign="middle"><ul><li type=round>Se cuenta con recursos de TI para soporte y mantenimiento.</ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>Coste total de propiedad</b></p></td>
   <td valign="middle"><ul><li type=round>Elimina costes de hardware. Reduce los costes administrativos.</ul></td>
   <td valign="middle"><ul><li type=round>Elimina costes de hardware. </ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>Continuidad del negocio</b></p></td>
   <td valign="middle"><ul><li type=round>Además de capacidades integradas de infraestructura de tolerancia a fallos, Base de datos SQL de Azure proporciona características como, por ejemplo, Restauración a un momento dado, Restauración geográfica y Replicación geográfica para aumentar la continuidad del negocio. Para obtener más información, consulte [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md).</ul></td>
   <td valign="middle"><ul><li type=round>SQL Server en Máquinas virtuales de Azure permite configurar una solución de recuperación ante desastres y alta disponibilidad para las necesidades específicas de la base de datos. Por consiguiente, podrá tener un sistema altamente optimizado para la aplicación. Podrá probar y ejecutar conmutaciones por error cuando sea necesario. Para obtener información, consulte [Alta disponibilidad y recuperación ante desastres para SQL Server en Máquinas virtuales de Azure](../virtual-machines/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).</ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>Nube híbrida</b></p></td>
   <td valign="middle"><ul><li type=round>La aplicación local puede obtener acceso a datos de Base de datos de SQL de Azure.</ul></td>
   <td valign="middle"><ul>
      <li type=round>Con SQL Server en Máquinas virtuales de Azure, se pueden tener aplicaciones que se ejecuten parcialmente en la nube y parcialmente en la instalación local. Por ejemplo, se puede ampliar la red local y el Dominio de Active Directory a la nube mediante [Redes virtuales de Azure](../virtual-network/virtual-networks-overview.md). Además, se pueden almacenar archivos de datos locales en Almacenamiento de Azure mediante [Archivos de datos de SQL Server en Azure](http://msdn.microsoft.com/library/dn385720.aspx). Para obtener más información, consulte [Introducción a la nube híbrida de SQL Server 2014](http://msdn.microsoft.com/library/dn606154.aspx).
      <li type=round>Admite recuperación ante desastres para aplicaciones de SQL Server locales mediante [Copia de seguridad y restauración de SQL Server con el servicio de Almacenamiento de blobs de Azure](http://msdn.microsoft.com/library/jj919148.aspx) o [réplicas AlwaysOn en Máquinas virtuales de Azure](../virtual-machines/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).
      </ul></td>

</tr>
</table>

## Motivaciones empresariales al elegir Base de datos SQL de Azure o SQL Server en Máquinas virtuales de Azure

### Coste

Si se encuentra en una empresa incipiente con falta de medios o en un equipo de una compañía bien establecida que opera con restricciones presupuestarias, los fondos limitados suelen ser el factor principal a la hora de decidir cómo hospedar la base de datos. En esta sección, obtendremos en primer lugar información relativa a los conceptos básicos sobre facturación y licencias en Azure con respecto a estas dos opciones de base de datos relacional: Base de datos SQL y SQL Server en Máquinas virtuales de Azure. Seguidamente, veremos cómo debemos calcular el coste total de la aplicación.

#### Conceptos básicos sobre facturación y licencias

**Base de datos SQL** se vende a los clientes como servicio, no con licencia, mientras que SQL Server en Máquinas virtuales de Azure requiere licencias tradicionales de SQL Server.

Actualmente, **Base de datos SQL** está disponible en varios niveles de servicio, que se facturan por hora a una tarifa fija en función del nivel de servicio y el nivel de rendimiento que se elija. Además, se le facturará el tráfico saliente de Internet. Los niveles de servicio Basic, Standard y Premium se han concebido para proporcionar un rendimiento predecible con varios niveles de rendimiento para igualar los requisitos máximos de la aplicación. Se puede cambiar de un nivel de servicio a otro y de un nivel de rendimiento a otro para igualar las necesidades variables de capacidad de proceso de la aplicación. Si la base de datos tiene un alto volumen de transacciones y debe admitir muchos usuarios simultáneos, recomendamos usar el nivel de servicio Premium. Para obtener la información más reciente sobre los niveles de servicio que se admiten actualmente, consulte [Opciones y rendimiento de Base de datos SQL: comprender lo que está disponible en cada nivel de servicio](sql-database-service-tiers.md).

Con **Base de datos SQL**, Microsoft configura, revisa y actualiza automáticamente el software de base de datos, lo que reduce los costos de administración. Además, sus capacidades de [copia de seguridad integrada](sql-database-business-continuity.md) ayudan a obtener un ahorro significativo, sobre todo, cuando se tiene gran cantidad de base de datos.

Con **SQL Server en Máquinas virtuales de Azure**, se emplean licencias tradicionales de SQL Server. También puede usar la imagen de SQL Server que proporciona la plataforma (que incluye una licencia) o incorporar su licencia de SQL Server. Cuando use las imágenes suministradas por Azure, el costo operativo depende del tamaño de la máquina virtual, así como de la edición de SQL Server que elija. Independientemente del tamaño de la máquina virtual o la edición de SQL Server, se paga el costo de licencia por minuto de SQL Server y Windows Server, junto con el costo de Almacenamiento de Azure para los discos de la máquina virtual. La opción de facturación por minuto permite utilizar SQL Server durante el tiempo que sea necesario sin comprar licencias adicionales de SQL Server. Si incorpora su propia licencia de SQL Server a Azure, solo se cobran los costos de Windows Server y de almacenamiento. Para obtener más información sobre la incorporación de licencias propias, consulte [Movilidad de Licencias a través de Software Assurance en Azure](https://azure.microsoft.com/pricing/license-mobility/).

#### Cálculo del coste total de la aplicación

Cuando se comienza a usar una plataforma en la nube, el costo de ejecución de la aplicación incluye principalmente los costos de administración y desarrollo, junto con los costos de servicio que la plataforma en la nube pública requiere.

A continuación, se ofrece el cálculo del costo pormenorizado para la aplicación en ejecución en Base de datos SQL y en SQL Server en Máquinas virtuales de Azure:

**Cuándo se usa Base de datos SQL de Azure:**

*Costo total de la aplicación = costos de administración muy minimizados + costos de desarrollo de software + costos de servicio de Base de datos SQL*

**Cuándo se usa SQL Server en Máquinas virtuales de Azure:**

*Costo total de la aplicación = costos minimizados de desarrollo/modificación de software + costos de administración + costos de licencias de SQL Server y Windows Server + costos de Almacenamiento de Azure*

Para obtener más información sobre precios, consulte los siguientes recursos:

- [Precios de base de datos SQL](https://azure.microsoft.com/pricing/details/sql-database/)
- [Precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/) para [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) y [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/)

> [AZURE.NOTE] Hay unas pocas características de SQL Server que no se pueden aplicar o no están disponibles en Base de datos SQL. Consulte [Instrucciones y limitaciones generales de Base de datos SQL de Azure](sql-database-general-limitations.md) e [Información sobre Transact-SQL de Base de datos SQL de Azure](sql-database-transact-sql-information.md) para obtener más información. Si va a mover una solución existente de SQL Server a la nube, consulte [Migrar una base de datos a la Base de datos SQL de Azure](sql-database-cloud-migrate.md). Al migrar una aplicación existente de SQL Server local a Base de datos SQL, considere la posibilidad de actualizar la aplicación para aprovechar las capacidades de la oferta de servicios en la nube. Por ejemplo, puede considerar la posibilidad de usar [Aplicaciones web](https://azure.microsoft.com/services/app-service/web/) o [Servicios en la nube](https://azure.microsoft.com/services/cloud-services/) en el nivel de aplicación para aumentar la relación costo-beneficios.

### Administración

En muchas empresas, la decisión de pasar a un servicio en la nube está tan relacionada con la posibilidad de reducir la carga de complejidad de administración, como con el costo. Con **Base de datos SQL**, Microsoft administra el hardware subyacente, replica automáticamente todos los datos para proporcionar alta disponibilidad, configura y actualiza el software de base de datos, administra el equilibrio de carga y realiza una conmutación por error transparente en caso de error del servidor. Puede seguir administrando la base de datos pero ya no necesita administrar el motor de la base de datos, el sistema operativo del servidor ni el hardware. Las bases de datos y los inicios de sesión, el ajuste de índices y consultas, así como la auditoría y la seguridad, son ejemplos de elementos que puede seguir administrando.

Por otra parte, es posible que cuente con conocimientos internos y desee mantener el control desde la ubicación de la base de datos hasta la del disco. Con **Ejecución de SQL Server en Máquinas virtuales de Azure**, tiene un control completo sobre la configuración del sistema operativo y de la instancia de SQL Server. Con una máquina virtual, usted decide cuándo actualizar el software del sistema operativo y de la base de datos, y cuándo instalar cualquier otro software adicional, como, por ejemplo, herramientas antivirus y de copia de seguridad. Además, se puede controlar el tamaño de la máquina virtual, el número de discos y sus configuraciones de almacenamiento. Por ejemplo, Azure permite cambiar el tamaño de una máquina virtual cuando sea necesario. Para obtener más información, consulte [Tamaños de máquinas virtuales](../virtual-machines/virtual-machines-size-specs.md).

### Contrato de nivel de servicio (SLA)

Para algunos departamentos de TI, cumplir las obligaciones de tiempo de actividad de un contrato de nivel de servicio (SLA) es una prioridad máxima. En esta sección, analizaremos a qué se aplica un contrato de nivel de servicio para cada opción de hospedaje de base de datos.

Para los niveles de servicio Basic, Standard y Premium de **Base de datos SQL**, Microsoft proporciona un contrato de nivel de servicio de disponibilidad del 99,99 %. Para obtener la información más reciente, consulte [Contrato de nivel de servicio para Base de datos SQL](https://azure.microsoft.com/support/legal/sla/sql-database/). Para obtener la información más reciente sobre niveles de servicio de Base de datos SQL y planes de continuidad del negocio admitidos, consulte [Opciones y rendimiento de Base de datos SQL: comprender lo que está disponible en cada nivel de servicio](sql-database-service-tiers.md).

Para **Ejecución de SQL Server en Máquinas virtuales de Azure**, Microsoft proporciona un contrato de nivel de servicio de disponibilidad del 99,95 % que cubre solo la máquina virtual. Este contrato no cubre los procesos (como SQL Server) que se ejecutan en la máquina virtual y requieren que se hospeden como mínimo dos instancias de máquina virtual en un conjunto de disponibilidad. Para obtener la información más reciente, consulte [Contrato de nivel de servicio para Máquinas virtuales](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Para alta disponibilidad (HA) de base de datos en las máquinas virtuales, se debe configurar una de las opciones de alta disponibilidad admitidas en SQL Server, como [AlwaysOn Availability Groups](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).

### <a name="market"></a>Plazo de comercialización

**Base de datos SQL** es la solución correcta para aplicaciones diseñadas en la nube cuando la productividad del desarrollador y un plazo de comercialización rápido son factores críticos. Con una funcionalidad de tipo DBA mediante programación, resulta perfecto para arquitectos y desarrolladores en la nube puesto que reduce la necesidad de administrar el sistema operativo y la base de datos subyacentes. Por ejemplo, se puede usar la [API de REST](http://msdn.microsoft.com/library/azure/dn505719.aspx) y los [cmdlets de PowerShell](http://msdn.microsoft.com/library/azure/dn546726.aspx) para automatizar y administrar operaciones administrativas para cientos de bases de datos. Características como [bases de datos elásticas](sql-database-elastic-pool.md) permiten centrarse en el nivel de aplicación y comercializar la solución con mayor rapidez.

**Ejecución de SQL Server en Máquinas virtuales de Azure** es perfecto si las aplicaciones nuevas o existentes requieren tener acceso y control de todas las características de una instancia de SQL Server. También es una buena opción cuando desea migrar aplicaciones y bases de datos locales existentes a Azure tal cual. Dado que no es necesario cambiar los niveles de presentación, aplicación y datos, se ahorra tiempo y presupuesto en renovar la arquitectura de la solución existente. En su lugar, puede centrarse en migrar todas las soluciones a Azure y realizar algunas optimizaciones del rendimiento que requiere la plataforma de Azure. Para obtener más información, consulte [Procedimientos recomendadas para mejorar el rendimiento para SQL Server en máquinas virtuales de Azure](../virtual-machines/virtual-machines-sql-server-performance-best-practices.md).

## Resumen

Este artículo explora Base de datos SQL y SQL Server en Máquinas virtuales de Azure y describe los factores de motivación empresariales comunes que pueden afectar a su decisión. A continuación, se ofrece un resumen de las sugerencias que debe tener en cuenta:

Elija **Base de datos SQL de Azure**, si:

- Está creando nuevas aplicaciones en la nube o desea migrar la solución existente de SQL Server para aprovechar los ahorros de costos y la optimización del rendimiento que proporciona Servicios en la nube. Este enfoque ofrece las ventajas de un servicio en la nube totalmente administrado, ayuda a reducir el tiempo de comercialización inicial y puede proporcionar una optimización de los costos duradera.

- Desea que Microsoft realice operaciones de administración comunes en las bases de datos y requiere contratos de nivel de servicio de disponibilidad más seguros para las bases de datos.

Consulte [Tutorial de Base de datos SQL: creación de una Base de datos SQL en cuestión de minutos con datos de ejemplo y el Portal de Azure](sql-database-get-started.md) para comenzar.

Elija **SQL Server en Máquinas virtuales de Azure** si:

- Tiene aplicaciones locales existentes y desea dejar de realizar el mantenimiento de su propio hardware o contempla la posibilidad de usar soluciones híbridas. Este enfoque permite acceder más rápidamente a una gran capacidad de bases de datos, a la vez que facilita la conexión a las aplicaciones locales a través de un túnel seguro.

- Tiene recursos de TI existentes, requiere derechos administrativos completos sobre SQL Server y precisa de plena compatibilidad con SQL Server local. Este enfoque permite minimizar costes de desarrollo o modificaciones de aplicaciones existentes con la flexibilidad de ejecutar la mayoría de las aplicaciones. Además, ofrece control total de la configuración de la máquina virtual, el sistema operativo y la base de datos.

Consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure](virtual-machines-provision-sql-server.md) para comenzar.

> [AZURE.NOTE] ¿Desea probar SQL Server 2016 CTP2? Suscríbase a Microsoft Azure y, a continuación, acceda [aquí](http://aka.ms/sql2016vm "aquí") para crear una máquina virtual con SQL Server 2016 CTP2 ya instalado.

<!---HONumber=AcomDC_0302_2016-->