<properties
	pageTitle="¿Qué cargas de trabajo se pueden proteger con Azure Site Recovery?" 
	description="Azure Site Recovery protege sus cargas de trabajo y aplicaciones mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales locales y servidores físicos en Azure o en un sitio local secundario." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="12/01/2015" 
	ms.author="raynew"/>

# ¿Qué cargas de trabajo se pueden proteger con Azure Site Recovery?

Azure Site Recovery permite a los clientes a implementar soluciones de disponibilidad con reconocimiento de aplicaciones que contribuyen a la estrategia de recuperación ante desastres y continuidad del negocio (BCDR) de su organización. Ya sea en aplicaciones basadas en Windows Server o en Linux, las aplicaciones empresariales de Microsoft u ofertas de otros proveedores, puede usar Azure Site Recovery para habilitar la recuperación ante desastres, entornos de desarrollo/pruebas a petición y migración a la nube.

Site Recovery se integra con aplicaciones de Microsoft, incluidas SharePoint, Exchange, Dynamics, SQL Server y Active Directory. Microsoft también trabaja en estrecha colaboración con proveedores líderes, como Oracle, SAP, IBM y Red Hat para asegurarse de que sus aplicaciones y servicios funcionan bien en plataformas de Microsoft, como Hyper-V y Azure. Puede personalizar su solución de recuperación ante desastres para cada aplicación específica.


##Principales características
Las características de Azure Site Recovery que contribuyen a su estrategia de protección y recuperación de nivel de aplicación incluyen:

- La replicación casi sincrónica con RPO de tan solo 30 segundos, que satisface las necesidades de las aplicaciones más críticas.
- Instantáneas coherentes con la aplicación para aplicaciones simples o de N niveles
- Integración con SQL Server AlwaysOn y asociación con otras tecnologías de replicación de nivel de aplicación, incluida la replicación de AD, SQL AlwaysOn, Grupos de disponibilidad de base de datos (DAG) de Exchange y Oracle Data Guard.
- Los planes de recuperación flexibles permiten la recuperación de toda la pila de aplicaciones con un solo clic e incluyen los scripts externos o las acciones manuales. 
- La administración de red avanzada en Site Recovery y Azure simplifican los requisitos de red para una aplicación, incluida la reserva de direcciones IP, la configuración de equilibradores de carga o la integración del Administrador de tráfico de Azure para conmutaciones de red de RTO.
-  Una biblioteca de automatización enriquecida que proporciona scripts específicos de la aplicación y preparados para la producción que pueden descargarse e integrarse con Site Recovery.



##Resumen de cargas de trabajo

Las tecnologías de replicación de Site Recovery son compatibles con cualquier aplicación que se ejecuta en una máquina virtual. Además, hemos llevado a cabo pruebas adicionales en colaboración con los equipos de producto de las aplicaciones para mejorar aún más cada aplicación.

**Carga de trabajo** | <p>**Replicación de máquinas virtuales de Hyper-V**</p> <p>**(para sitio secundario)**</p> | <p>**Replicación de máquinas de Hyper-V virtual**</p> <p>**(para Azure)**</p> | <p>**Replicación de máquinas virtuales de VMware**</p> <p>**(en sitio secundario)**</p> | <p>**Replicación de máquinas virtuales de VMware**</p><p>**(para Azure)****</p>
---|---|---|---|---
Active Directory, DNS | Y | Y | Y | Y
Aplicaciones web (IIS, SQL) | Y | Y | Y | Y
SCOM | Y | Y | Y | Y
Sharepoint | Y | Y | Y |Y
<p>SAP</p><p>Replicación del sitio de SAP en Azure para sin clúster</p> | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft) 
Exchange (no DAG) | Y | Próximamente | Y | Y
Escritorio remoto/VDI | Y | Y | Y | N/A
<p>Linux</p> <p>(aplicaciones y sistema operativo)</p> | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft)
Dynamics AX | Y | Y | Y | Y
Dynamics CRM | Y | Próximamente | Y | Próximamente
Oracle | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft)
Servidor de archivos de Windows | Y | Y | Y | Y

##Protección de Active Directory y DNS

Todas las aplicaciones empresariales, como SharePoint, Dynamics AX y SAP, dependen de la infraestructura de Active Directory y DNS. Como parte de la solución BCDR, debe proteger y recuperar estos componentes de la infraestructura antes de recuperar las aplicaciones y cargas de trabajo.

Con Site Recovery, puede crear un plan de recuperación ante desastres automatizada completa para Active Directory y DNS. Por ejemplo, si está usando Active Directory para varias aplicaciones, como SAP y SharePoint en el sitio principal y desea conmutar por error el sitio completo, puede conmutar por error Active Directive primero mediante un plan de recuperación y luego conmutar por error las aplicaciones que dependen de Active Directory con los planes de recuperación específicos de la aplicación.

[Más información](http://aka.ms/asr-ad)

##Protección de SQL Server

SQL Server proporciona una base para los servicios de datos de muchas aplicaciones empresariales en un centro de datos local. Las tecnologías de alta disponibilidad y recuperación ante desastres para SQL Server y Site Recovery son complementarias y pueden usarse juntas para proporcionar protección de extremo a extremo para las aplicaciones empresariales de varios niveles. Site Recovery ofrece los siguientes beneficios para los entornos de SQL Server:

- Protección y replicación sencillas de servidores SQL independientes o agrupados en Azure o en un sitio secundario. Proporciona una solución de recuperación ante desastres simple y rentable para muchas ediciones y versiones de SQL Server.
- Integración con grupos de disponibilidad AlwaysOn de SQL, administración de procesos de conmutación por error y conmutación por recuperación con planes de recuperación en Azure Site Recovery.
- Planes de recuperación de extremo a extremo para toda la pila de aplicaciones, incluidas las bases de datos de SQL Server.
- Escalación vertical de SQL Server para cargas máximas con Azure Site Recovery "protegiéndolos" en tamaños de máquina virtual de IaaS más grandes en Azure.
- Prueba de SQL Server en Azure o un sitio secundario con Azure Site Recovery. Las conmutaciones por error de pruebas pueden usarse para comprobaciones de cumplimento de datos de análisis sin afectar al entorno de producción.

[Más información](http://aka.ms/asr-sql)

##Protección de SharePoint

Azure Site Recovery ayuda a proteger la implementación de SharePoint. Con Site Recovery, puede:

- Eliminar la necesidad y el costo de la infraestructura asociada para una granja de servidores de espera para la recuperación ante desastres. Con Site Recovery, puede habilitar la protección de toda la granja de servidores (web, aplicaciones y niveles de base de datos) en Azure o en un sitio secundario.
- Simplificar la administración e implementación de las aplicaciones. Las actualizaciones implementadas en el sitio principal se replican automáticamente y estarán disponibles cuando se recupera la granja de servidores en el sitio secundario. Esto elimina la complejidad de la administración para mantener actualizada una granja de servidores de reserva y reducir los costos.
- Simplificar el desarrollo de aplicaciones de SharePoint y pruebas mediante la creación de un entorno de réplica de copia de producción a petición para realizar la prueba y la depuración.
- Simplifique la transición a la nube mediante Site Recovery para migrar las implementaciones de SharePoint a Azure.

[Más información](http://aka.ms/asr-sharepoint)


## Protección de Dynamics AX

Azure Site Recovery le ayuda a proteger la solución ERP de Dynamics AX:

- Habilite la protección de todo el entorno de Dynamics AX (niveles web y AOS, niveles de base de datos y SharePoint) en Azure o en un sitio secundario con Site Recovery.
- Simplifique la migración a la nube mediante Site Recovery para migrar las implementaciones de Dynamics AX a Azure.
- Simplifique el desarrollo de aplicaciones de Dynamics AX y pruebas mediante la creación de una copia de producción a petición para probar y depurar.

[Más información](http://aka.ms/asr-dynamics)

## Protección de RDS 
Servicios de Escritorio remoto habilita la infraestructura de escritorio virtual (VDI), escritorios basados en sesión y aplicaciones, lo que permite a los usuarios trabajar desde cualquier lugar. Con Site Recovery, puede habilitar la protección de escritorios virtuales agrupados administrados o no administrados en un sitio secundario y aplicaciones remotas y sesiones en un sitio secundario o Azure.

[Más información](http://aka.ms/asr-rds)


## Protección de Exchange

Microsoft Exchange incluye compatibilidad integrada para alta disponibilidad y recuperación ante desastres. Los DAG de Exchange y Azure Site Recovery pueden trabajar conjuntamente.

- Los DAG de Exchange son la solución recomendada para la recuperación ante desastres de Exchange en una empresa. Los planes de recuperación en Site Recovery puede incluir DAG para organizar la conmutación por error de DAG a través de los sitios.
- Para implementaciones pequeñas, como servidores únicos o no agrupados, Site Recovery puede proteger y realizar una conmutación por error en Azure o en un sitio secundario.

[Más información](http://aka.ms/asr-exchange)

## Protección de SAP

Use Site Recovery para proteger la implementación de SAP:

- Habilite la protección de toda la implementación de SAP con diferentes niveles en Azure o en un sitio secundario.
- Simplifique la migración a la nube con Site Recovery para migrar la implementación de SAP en Azure.
- Simplifique el desarrollo de SAP y pruebas mediante la creación de una copia de producción a petición para probar y depurar aplicaciones.

[Más información](http://aka.ms/asr-sap)

<!---HONumber=AcomDC_1203_2015-->