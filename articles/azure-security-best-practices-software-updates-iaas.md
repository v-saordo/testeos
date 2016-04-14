<properties
   pageTitle="Prácticas recomendadas para actualizaciones de software en IaaS de Microsoft Azure | Microsoft Azure"
   description="El artículo proporciona una recopilación de prácticas recomendadas para actualizaciones de software en un entorno de IaaS de Microsoft Azure. Está pensado para profesionales de TI y analistas de seguridad que se enfrentan diariamente con la administración de control de cambios, de actualizaciones de software y de activos, incluidos quiénes son responsables de los esfuerzos de seguridad y cumplimiento de su organización."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriD"
   manager="swadhwa"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/10/2015"
   ms.author="yurid"/>

#Prácticas recomendadas para actualizaciones de software en IaaS de Microsoft Azure

Antes de profundizar en cualquier tipo de discusión acerca de los procedimientos recomendados para un entorno de IaaS de Azure, es importante comprender cuáles son los escenarios en los que tendrá que administrar actualizaciones de software. El diagrama siguiente debería ayudarle con esto:

![Modelos de nube y responsabilidades](./media/azure-security-best-practices-software-updates-iaas/sec-cloudstack.png)

En el modelo de centro de datos tradicional donde toda la infraestructura se encuentra localmente, tiene toda la responsabilidad de la administración de actualizaciones de sistemas operativos, aplicaciones, dispositivos de red (enrutadores, conmutadores, etc.) y hardware (firmware). En un escenario de IaaS, aún tendrá que administrar las actualizaciones de sistemas operativos y aplicaciones; no obstante, toda la infraestructura bajo los sistemas operativos y aplicaciones está administrada por Microsoft. En todos estos modelos, los clientes siguen siendo los propietarios de los datos y los responsables de su protección en el nivel de punto final.

En un escenario de PaaS, tendrá menos responsabilidad por las actualizaciones de software, ya que la administración de actualizaciones para el sistema operativo es responsabilidad de Microsoft. En un escenario de SaaS, la responsabilidad de las actualizaciones de software para toda la pila es de Microsoft.

Estos mismos principios se aplican en un escenario híbrido, en el que su empresa usa máquinas virtuales de IaaS de Azure que se comunican con los recursos locales tal como se muestra en el diagrama siguiente.

![Escenario típico híbrido con Microsoft Azure](./media/azure-security-best-practices-software-updates-iaas/sec-azconnectonpre.png)

## Evaluación inicial

Incluso si su empresa ya usa un sistema de administración de actualizaciones y ya cuenta con directivas de actualización de software, es importante revisar con frecuencia las evaluaciones de directivas anteriores y actualizarlas según los requisitos actuales. Esto significa que deberá estar familiarizado con el estado actual de los recursos de su empresa. Para llegar a este estado, debe conocer:

-   Los equipos físicos y virtuales de su empresa.

-   Los sistemas operativos y las versiones que se ejecutan en cada uno de estos equipos físicos y virtuales.

-   Las actualizaciones de software actualmente instaladas en cada equipo (versiones de Service Pack, actualizaciones de software y otras modificaciones).

-   La función que realiza cada equipo en su empresa.

-   Las aplicaciones y programas que se ejecutan en cada equipo.

-   La propiedad y la información de contacto de cada equipo.

-   Los activos presentes en su entorno y su valor relativo para determinar las áreas que necesitan más atención y protección.

-   Los problemas de seguridad conocidos y los procesos existentes en su empresa para identificar los problemas de seguridad nuevos o los cambios en el nivel de seguridad.

-   Las medidas implementadas para proteger su entorno.

Debe actualizar esta información con regularidad y que esté disponible para las personas implicadas en el proceso de administración de actualizaciones de software.

## Establecimiento de una línea base

Una parte importante del proceso de administración de actualizaciones de software es crear instalaciones iniciales estándar de las versiones del sistema operativo, aplicaciones y hardware para los equipos de la empresa; estas se denominan líneas base. Una línea base es la configuración de un producto o sistema establecida en un momento concreto en el tiempo. Una línea base de aplicación o sistema operativo, por ejemplo, proporciona la capacidad de recompilar un equipo o un servicio en un estado específico.

Las líneas base proporcionan la base para encontrar y corregir posibles problemas y simplificar el proceso de administración de actualizaciones de software al reducir el número de actualizaciones de software que se deben implementar en su empresa y aumentar la capacidad de supervisar el cumplimiento.

Después de realizar la auditoría inicial de la empresa, tiene que usar la información que se obtiene de la auditoría a fin de definir una línea base operativa para los componentes de TI dentro de su entorno de producción. Puede ser necesario tener cierto número de líneas base, en función de los distintos tipos de hardware y software implementados en producción.

Por ejemplo, algunos servidores requieren una actualización de software para impedir que se bloqueen al entrar en el proceso de cierre cuando ejecutan Windows Server 2012. Una línea base para estos servidores debe incluir esta actualización de software.

En organizaciones grandes, a menudo resulta útil dividir los equipos de la empresa en categorías de activos y mantener cada categoría en una línea base estándar mediante el uso de las mismas versiones de software y actualizaciones de software. Puede usar luego estas categorías de activos a la hora de priorizar una distribución de actualización de software.

## Suscripción a los servicios adecuados de notificaciones de actualización de software

Después de realizar una auditoría inicial del software en uso en su empresa, debe determinar el mejor método para recibir notificaciones de nuevas actualizaciones de software para cada producto y versión de software. Según el producto de software, el mejor método de notificaciones podría ser notificaciones de correo electrónico, sitios web o publicaciones del equipo.

Por ejemplo, Microsoft Security Response Center (MSRC) responde a todos los aspectos relacionados con la seguridad acerca de los productos de Microsoft y proporciona el servicio de boletines de seguridad de Microsoft, una notificación de correo electrónico gratuita sobre vulnerabilidades recién identificadas y actualizaciones de software que se publican para corregir estas vulnerabilidades. Puede suscribirse a este servicio en http://www.microsoft.com/technet/security/bulletin/notify.mspx

## Consideraciones sobre actualización de software

Después de realizar una auditoría inicial del software en uso en su empresa, debe determinar los requisitos para configurar el sistema de administración de actualizaciones de software, que depende del sistema de administración de actualizaciones de software que esté usando. Para WSUS, lea [Prácticas recomendadas con Windows Server Update Services](https://technet.microsoft.com/library/Cc708536) y para System Center, lea [Planeación de actualizaciones de software en Configuration Manager](https://technet.microsoft.com/library/gg712696).

Sin embargo, hay algunas consideraciones generales y prácticas recomendadas que se pueden aplicar sin tener en cuenta la solución que esté usando tal como se muestra en las secciones siguientes.

### Configuración del entorno

Tenga en cuenta las siguientes prácticas al planear la instalación del entorno de administración de actualizaciones de software:

-   **Crear recopilaciones de actualizaciones de software en función de criterios estables de producción**: en general, el uso de criterios estables para crear recopilaciones de inventario y distribución de actualizaciones de software le ayudará a simplificar todas las fases del proceso de administración de actualizaciones de software. Los criterios estables pueden incluir la versión del sistema operativo cliente instalado y el nivel de Service Pack, el rol del sistema o la organización de destino.

-   **Crear recopilaciones de preproducción que incluyan equipos de referencia**: la recopilación de preproducción debe incluir configuraciones representativas de las versiones del sistema operativo, el software relacionado con una determinada línea de negocio y otros tipos de software en ejecución en la empresa.

También debe tener en cuenta dónde se ubicará el servidor de actualizaciones de software, si va a estar en la infraestructura de IaaS de Azure en la nube o si va estar de forma local. Se trata de una decisión importante, ya que necesita evaluar la cantidad de tráfico entre los recursos locales y la infraestructura de Azure. Lea [Conectar una red local con una red virtual de Microsoft Azure](https://technet.microsoft.com/library/Dn786406.aspx) para más información sobre cómo conectar la infraestructura local a Azure.

Las opciones de diseño que determinarán dónde estará ubicado el servidor de actualizaciones también varían en función de la infraestructura actual y del sistema de actualizaciones de software que está usando actualmente. Para WSUS, lea [Implementación de Windows Server Update Services en la organización](https://technet.microsoft.com/library/hh852340.aspx) y para System Center Configuration Manager, lea [Planeación de sitios y jerarquías en Configuration Manager](https://technet.microsoft.com/library/Gg712681.aspx).

### Copia de seguridad

Las copias de seguridad periódicas son importantes no solo para la plataforma de administración de actualizaciones de software en sí, sino también para los servidores que se van a actualizar. Las organizaciones que tienen un [proceso de administración de cambios](https://technet.microsoft.com/library/cc543216.aspx) existente requerirán TI para justificar las razones de por qué el servidor necesita actualizarse, el tiempo de inactividad estimado y el posible impacto. Para asegurarse de que cuenta con una configuración de reversión en caso de que se produzca un error en una actualización, asegúrese de hacer una copia de seguridad del sistema con regularidad.

Algunas opciones de copia de seguridad de IaaS de Azure incluyen:

-   [Protección de cargas de trabajo de IaaS de Azure con Data Protection Manager](https://azure.microsoft.com/blog/2014/09/08/azure-iaas-workload-protection-using-data-protection-manager/)

-   [Copia de seguridad de máquinas virtuales de Azure](../backup/backup-azure-vms.md)

### Supervisión

Debe ejecutar informes periódicos para supervisar el número de actualizaciones instaladas o que faltan, o actualizaciones con el estado de incompleto, para cada actualización de software que esté autorizada. De igual forma, los informes para actualizaciones de software aún no autorizadas pueden facilitar las decisiones sobre implementación

También debería tener en cuenta las siguientes tareas:

-   Realizar una auditoría de actualizaciones de seguridad aplicables e instaladas para todos los equipos de la empresa.

-   Autorizar e implementar las actualizaciones en los equipos apropiados.

-   Realizar un seguimiento del inventario y del estado y progreso de la instalación de actualizaciones para todos los equipos de la empresa.

Además de las consideraciones generales explicadas en este artículo, también debe tener en cuenta las prácticas recomendadas de cada producto, por ejemplo: si tiene una máquina virtual en Azure con SQL Server, asegúrese de que está siguiendo la recomendación sobre actualizaciones de software para ese producto.

## Pasos siguientes

Use las instrucciones descritas en este artículo para determinar las mejores opciones sobre actualizaciones de software para máquinas virtuales dentro de IaaS de Azure. Hay muchas similitudes entre las prácticas recomendadas de actualización de software en un centro de datos tradicional y en IaaS de Azure, por lo tanto, se recomienda evaluar las directivas de actualización de software actuales para incluir máquinas virtuales de Azure y aplicar las prácticas recomendadas relevantes de este artículo en el proceso de actualización de software global.

<!---HONumber=AcomDC_1217_2015-->