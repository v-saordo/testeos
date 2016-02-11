<properties
	pageTitle="RBAC: Roles integrados | Microsoft Azure"
	description="Este tema describe los roles integrados para el control de acceso basado en rol"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="01/21/2016"
	ms.author="kgremban"/>

#RBAC: Roles integrados

## Roles integrados

El control de acceso basado en roles de Azure (RBAC) dispone de los siguientes roles integrados que se pueden asignar a usuarios, grupos y servicios. La definición de los roles integrados no se puede modificar. En una próxima versión del RBAC de Azure se podrán definir roles personalizados formando un conjunto de acciones elegidas a partir de una lista de acciones disponibles que se pueden realizar en recursos de Azure.

Haga clic en los vínculo a continuación para ver las propiedades **acciones** y **no acciones** de una definición de rol. La propiedad **acciones** especifica las acciones permitidas en los recursos de Azure. Las cadenas de acciones pueden utilizar caracteres comodín. La propiedad **no acciones** de una definición de rol especifica las acciones que se deben excluir de las acciones permitidas.


| Nombre de rol | Descripción |
| --------- | ----------- |
| [Colaborador de servicio de administración de API](#api-management-service-contributor) | Puede administrar servicios de administración de API |
| [Colaborador de componentes de Application Insights](#application-insights-component-contributor) | Puede administrar los componentes de Application Insights |
| [Operador de automatización](#automation-operator) | Puede iniciar, detener, suspender y reanudar trabajos |
| [Colaborador de BizTalk](#biztalk-contributor) | Puede administrar los servicios de BizTalk |
| [Colaborador de ClearDB MySQL DB](#cleardb-mysql-db-contributor) | Puede administrar bases de datos ClearDB MySQL |
| [Colaborador](#contributor) | Puede administrar todo el contenido, excepto el acceso |
| [Colaborador de Factoría de datos](#data-factory-contributor) | Puede administrar las factorías de datos |
| [Usuario del laboratorio de desarrollo y pruebas](#devtest-lab-user) | Puede ver todo el contenido así como conectar, iniciar, reiniciar y apagar las máquinas virtuales |
| [Colaborador de cuenta de base de datos de documento](#document-db-account-contributor) | Puede administrar cuentas de DocumentDB |
| [Colaborador de la cuenta de Sistemas inteligentes](#intelligent-systems-account-contributor) | Puede administrar cuentas de Sistemas inteligentes |
| [Colaborador de la red](#network-contributor) | Puede administrar todos los recursos de red |
| [Colaborador de la cuenta de NewRelic APM](#newrelic-apm-account-contributor) | Puede administrar aplicaciones y cuentas de administración del rendimiento de la aplicación New Relic |
| [Propietario](#owner) | Puede administrar todo el contenido, incluido el acceso |
| [Lector](#reader) | Puede ver todo el contenido, pero no puede realizar cambios |
| [Colaborador de la memoria caché de Redis](#redis-cache-contributor]) | Puede administrar memorias caché en Redis |
| [Colaborador de colecciones de trabajos de Scheduler](#scheduler-job-collections-contributor) | Puede administrar las colecciones de trabajo de Programador |
| [Colaborador del servicio de búsqueda](#search-service-contributor) | Puede administrar los servicios de búsqueda |
| [Administrador de seguridad](#security-manager) | Puede administrar los componentes y las directivas de seguridad, además de las máquinas virtuales |
| [Colaborador de Base de datos de SQL](#sql-db-contributor) | Puede administrar bases de datos SQL, pero no las directivas relacionadas con la seguridad |
| [Administrador de seguridad SQL](#sql-security-manager) | Puede administrar las directivas relacionadas con la seguridad de las bases de datos y los servidores SQL |
| [Colaborador de SQL Server](#sql-server-contributor) | Puede administrar las bases de datos y los servidores SQL, pero no las directivas relacionadas con su seguridad |
| [Colaborador de cuentas de almacenamiento clásico](#classic-storage-account-contributor) | Puede administrar las cuentas de almacenamiento clásico |
| [Colaborador de la cuenta de almacenamiento](#storage-account-contributor) | Puede administrar las cuentas de almacenamiento |
| [Administrador de acceso de usuario](#user-access-administrator) | Puede administrar el acceso de usuarios a los recursos de Azure |
| [Colaborador de la máquina virtual clásica](#classic-virtual-machine-contributor) | Puede administrar máquinas virtuales clásicas, pero no la cuenta de almacenamiento o la red virtual a la que están conectadas |
| [Colaborador de la máquina virtual](#virtual-machine-contributor) | Puede administrar máquinas virtuales, pero no la cuenta de almacenamiento o la red virtual a la que están conectadas |
| [Colaborador de la red clásica](#classic-network-contributor) | Puede administrar IP reservadas y redes virtuales clásicas |
| [Colaborador de plan web](#web-plan-contributor) | Puede administrar planes web |
| [Colaborador de sitio web](#website-contributor) | Puede administrar sitios web, pero no los planes web a los que están conectados |

### Colaborador de servicio de administración de API
Puede administrar servicios de administración de API

| **Acciones** | |
| ------- | ------ |
| Microsoft.ApiManagement/Services/* | Crear y administrar los servicios de administración de API |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer roles y asignaciones de roles |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de componente de Application Insights
Puede administrar los componentes de Application Insights

| **Acciones** | |
| ------- | ------ |
| Microsoft.Insights/components/* | Crear y administrar componentes de Insights |
| Microsoft.Insights/webtests/* | Crear y administrar pruebas web |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer grupos de recursos de suscripción |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Operador de automatización
Puede iniciar, detener, suspender y reanudar trabajos

| **Acciones** ||
| ------- | ------ |
| Microsoft.Automation/automationAccounts/read | Leer cuentas de automatización |
| Microsoft.Automation/automationAccounts/runbooks/read | Leer runbooks de automatización |
| Microsoft.Automation/automationAccounts/schedules/read | Leer esquemas de cuentas de automatización |
| Microsoft.Automation/automationAccounts/schedules/write | Escribir esquemas de cuentas de automatización |
| Microsoft.Automation/automationAccounts/jobs/read | Leer trabajos de cuentas de automatización |
| Microsoft.Automation/automationAccounts/jobs/write | Escribir trabajos de cuentas de automatización |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Detener un trabajo de cuenta de automatización |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Suspender un trabajo de cuenta de automatización |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Reanudar un trabajo de cuenta de automatización |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Leer un programa de trabajos de cuentas de automatización |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Leer un programa de trabajos de cuentas de automatización |

### Colaborador de BizTalk
Puede administrar los servicios de BizTalk

| **Acciones** ||
| ------- | ------ |
| Microsoft.BizTalkServices/BizTalk/* | Crear y administrar los servicios de BizTalk |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer recursos del grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de ClearDB MySQL DB
Puede administrar bases de datos ClearDB MySQL

| **Acciones** ||
| ------- | ------ |
| successbricks.cleardb/databases/* | Crear y administrar bases de datos ClearDB MySQL |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer recursos del grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador
Puede administrar todo el contenido, excepto el acceso

| **Acciones** ||
| ------- | ------ |
| * | Crear y administrar recursos de todos los tipos |

| **No acciones** | |
| ------- | ------ |
| Microsoft.Authorization/*/Write | No puede crear roles ni asignaciones de roles |
| Microsoft.Authorization/*/Delete | No puede eliminar roles ni asignaciones de roles |

### Colaborador de factoría de datos
Puede administrar las factorías de datos

| **Acciones** ||
| ------- | ------ |
| Microsoft.DataFactory/dataFactories/* | Crear y administrar factorías de datos |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer recursos del grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Usuario del laboratorio de desarrollo y pruebas
Puede ver todo el contenido así como conectar, iniciar, reiniciar y apagar las máquinas virtuales

| **Acciones** ||
| ------- | ------ |
| */read | Leer recursos de todos los tipos | | Microsoft.DevTestLab/labs/labStats/action | Leer estadísticas de laboratorio | | Microsoft.DevTestLab/Environments/* | Crear y administrar entornos |
| Microsoft.DevTestLab/labs/createEnvironment/action | Crear un entorno de laboratorio |
| Microsoft.Compute/virtualMachines/start/action | Iniciar máquinas virtuales |
| Microsoft.Compute/virtualMachines/restart/action | Reiniciar máquinas virtuales |
| Microsoft.Compute/virtualMachines/deallocate/action | Desasignar máquinas virtuales |
| Microsoft.Storage/storageAccounts/listKeys/action | Enumerar claves de cuentas de almacenamiento |
| Microsoft.Network/virtualNetworks/join/action | Unirse a redes virtuales |
| Microsoft.Network/loadBalancers/join/action | Unirse a equilibradores de carga |
| Microsoft.Network/publicIPAddresses/link/action | Vincular a direcciones IP públicas |
| Microsoft.Network/networkInterfaces/link/action | Vincular a interfaces de red |
| Microsoft.Network/networkInterfaces/write | Escribir interfaces de red |

### Colaborador de cuenta de base de datos de documento
Puede administrar cuentas de DocumentDB

| **Acciones** ||
| ------- | ------ |
| Microsoft.DocumentDb/databaseAccounts/* | Crear y administrar cuentas de DocumentDB |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer recursos del grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de la cuenta de sistemas inteligentes
Puede administrar cuentas de Sistemas inteligentes

| **Acciones** ||
| ------- | ------ |
| Microsoft.IntelligentSystems/accounts/* | Crear y administrar cuentas de sistemas inteligentes |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer recursos del grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de la red
Puede administrar todos los recursos de red

| **Acciones** ||
| ------- | ------ |
| Microsoft.Network/* | Crear y administrar redes |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer recursos del grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de la cuenta de NewRelic APM
Puede administrar aplicaciones y cuentas de administración del rendimiento de la aplicación New Relic

| **Acciones** ||
| ------- | ------ |
| NewRelic.APM/accounts/* | Crear y administrar cuentas de administración del rendimiento de la aplicación New Relic |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer recursos del grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Propietario
Puede administrar todo el contenido, incluido el acceso

| **Acciones** ||
| ------- | ------ |
| * | Crear y administrar recursos de todos los tipos |

### Lector
Puede ver todo el contenido, pero no puede realizar cambios

| **Acciones** ||
| ------- | ------ |
| **/read | Leer recursos de todos los tipos, excepto secretos. |

### Colaborador de la memoria caché de Redis
Puede administrar memorias caché en Redis

| **Acciones** ||
| ------- | ------ |
| Microsoft.Cache/redis/* | Crear y administrar memorias caché de Redis |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer recursos del grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de colecciones de trabajos de Scheduler
Puede administrar las colecciones de trabajo de Programador

| **Acciones** ||
| ------- | ------ |
| Microsoft.Scheduler/jobcollections/* | Crear y administrar colecciones de trabajos |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer recursos del grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador del servicio de búsqueda
Puede administrar los servicios de búsqueda

| **Acciones** ||
| ------- | ------ |
| Microsoft.Search/searchServices/* | Crear y administrar servicios de búsqueda |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer recursos del grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Administrador de seguridad
Puede administrar los componentes y las directivas, además de las máquinas virtuales

| **Acciones** ||
| ------- | ------ |
| Microsoft.ClassicNetwork/*/read | Leer información de configuración acerca de la red clásica |
| Microsoft.ClassicCompute/*/read | Leer información de configuración para máquinas virtuales de proceso clásico |
| Microsoft.ClassicCompute/virtualMachines/*/write | Escribir configuración para máquinas virtuales |
| Microsoft.Security/* | Crear y administrar las directivas y los componentes de seguridad |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer recursos del grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de la base de datos de SQL
Puede administrar bases de datos SQL, pero no las directivas relacionadas con la seguridad

| **Acciones** ||
| ------- | ------ |
| Microsoft.Sql/servers/read | Leer los servidores SQL Server |
| Microsoft.Sql/servers/databases/* | Crear y administrar bases de datos SQL |
| Microsoft.Authorization/*/read | Leer roles y asignaciones de roles |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer recursos del grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alertas |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

| **No acciones** | |
| ------- | ------ |
| Microsoft.Sql/servers/databases/auditingPolicies/* | No puede editar las directivas de auditoría |
| Microsoft.Sql/servers/databases/connectionPolicies/* | No puede editar las directivas de conexión |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | No puede editar las directivas de enmascaramiento |
| Microsoft.Sql/servers/databases/securityMetrics/* | No puede editar las métricas de seguridad |

### Administrador de seguridad SQL
Puede administrar las directivas relacionadas con la seguridad de las bases de datos y los servidores SQL

| **Acciones** ||
| ------- | ------ |
| Microsoft.Sql/servers/read | Leer los servidores SQL Server |
| Microsoft.Sql/servers/auditingPolicies/* | Crear y administrar directivas de auditoría de SQL Server |
| Microsoft.Sql/servers/databases/read | Leer bases de datos SQL |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Crear y administrar directivas de auditoría de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Crear y administrar directivas de conexión de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Crear y administrar directivas de enmascaramiento de datos de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/securityMetrics/* | Crear y administrar métricas de seguridad de bases de datos de SQL Server |
| Microsoft.Authorization/*/read | Leer autorización de Microsoft |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos de suscripción |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer recursos de grupos de recursos de suscripción |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos de suscripciones |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
| Microsoft.Sql/servers/databases/schemas/read | Leer esquemas de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/schemas/tables/read | Leer tablas de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Leer columnas de tablas de bases de datos de SQL Server |

### Colaborador de SQL Server
Puede administrar las bases de datos y los servidores SQL, pero no las directivas relacionadas con la seguridad

| **Acciones** ||
| ------- | ------ |
| Microsoft.Sql/servers/* | Crear y administrar servidores de SQL Server |
| Microsoft.Authorization/*/read | Leer autorización|
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos de suscripción |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer recursos de grupos de recursos de suscripción |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos de suscripciones |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

| **No acciones** | |
| ------- | ------ |
| Microsoft.Sql/servers/auditingPolicies/* | No puede editar las directivas de auditoría de SQL Server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | No puede editar las directivas de auditoría de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/connectionPolicies/* | No puede editar las directivas de conexión de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | No puede editar las directivas de enmascaramiento de datos de bases de datos de SQL Server |
| Microsoft.Sql/servers/databases/securityMetrics/* | No puede editar las métricas de seguridad de bases de datos de SQL Server |

### Colaborador de cuentas de almacenamiento clásico
Puede administrar las cuentas de almacenamiento clásico

| **Acciones** ||
| ------- | ------ |
| Microsoft.ClassicStorage/storageAccounts/* | Crear y administrar cuentas de almacenamiento |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.Resources/subscriptions/resources/read | Leer recursos de suscripción |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos de suscripción |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer recursos de grupos de recursos de suscripción |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos de suscripción |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de la cuenta de almacenamiento
Puede administrar las cuentas de almacenamiento

| **Acciones** ||
| ------- | ------ |
| Microsoft.Storage/storageAccounts/* | Crear y administrar cuentas de almacenamiento |
| Microsoft.Authorization/*/read | Leer toda la autorización |
| Microsoft.Resources/subscriptions/resources/read | Leer recursos de suscripción |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos de suscripción |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer recursos de grupos de recursos de suscripción |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos de suscripción |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Administrador de acceso de usuario
Puede administrar el acceso de usuarios a los recursos de Azure

| **Acciones** ||
| ------- | ------ |
| */read | Leer recursos de todos los tipos, excepto secretos. | 
| Microsoft.Authorization/* | Leer autorización |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de la máquina virtual clásica
Puede administrar máquinas virtuales clásicas, pero no la cuenta de almacenamiento o la red virtual a la que están conectadas

| **Acciones** ||
| ------- | ------ |
| Microsoft.ClassicStorage/storageAccounts/read | Leer cuentas de almacenamiento clásico |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Enumerar claves de cuentas de almacenamiento |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Leer discos de cuentas de almacenamiento |
| Microsoft.ClassicStorage/storageAccounts/images/read | Leer imágenes de cuentas de almacenamiento |
| Microsoft.ClassicNetwork/virtualNetworks/read | Leer redes virtuales |
| Microsoft.ClassicNetwork/reservedIps/read | Leer direcciones IP reservadas |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Unirse a redes virtuales |
| Microsoft.ClassicNetwork/reservedIps/link/action | Vincular IP reservadas |
| Microsoft.ClassicCompute/domainNames/* | Crear y administrar nombres de dominio de proceso clásico |
| Microsoft.ClassicCompute/virtualMachines/* | Crear y administrar máquinas virtuales |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos de suscripción |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer recursos de grupos de recursos de suscripción |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos de suscripción |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de la máquina virtual
Puede administrar máquinas virtuales, pero no la cuenta de almacenamiento o la red virtual a la que están conectadas

| **Acciones** ||
| ------- | ------ |
| Microsoft.Storage/storageAccounts/read | Leer cuentas de almacenamiento |
| Microsoft.Storage/storageAccounts/listKeys/action | Enumerar claves de cuentas de almacenamiento |
| Microsoft.Network/virtualNetworks/read | Leer redes virtuales |
| Microsoft.Network/virtualNetworks/subnets/join/action | Unirse a subredes de redes virtuales |
| Microsoft.Network/loadBalancers/read | Leer equilibradores de carga |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Unirse a los grupos de direcciones de back-end de equilibrador de carga |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Unir las reglas NAT de entrada del equilibrador de carga |
| Microsoft.Network/publicIPAddresses/read | Leer direcciones IP públicas de red |
| Microsoft.Network/publicIPAddresses/join/action | Unir las direcciones IP públicas de red |
| Microsoft.Network/networkSecurityGroups/read | Leer grupos de seguridad de red |
| Microsoft.Network/networkSecurityGroups/join/action | Unir los grupos de seguridad de red |
| Microsoft.Network/networkInterfaces/* | Crear y administrar interfaces de red |
| Microsoft.Network/locations/* | Crear y administrar ubicaciones de red |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Unir grupos de direcciones de back-end de puerta de enlace de aplicaciones de red |
| Microsoft.Compute/virtualMachines/* | Crear y administrar máquinas virtuales |
| Microsoft.Compute/availabilitySets/* | Crear y administrar conjuntos de disponibilidad de proceso |
| Microsoft.Compute/locations/* | Crear y administrar ubicaciones de proceso |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos de suscripción |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer recursos de grupos de recursos de suscripción |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos de suscripción |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de la red clásica
Puede administrar IP reservadas y redes virtuales clásicas

| **Acciones** ||
| ------- | ------ |
| Microsoft.ClassicNetwork/* | Crear y administrar redes clásicas |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos de suscripción |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer recursos de grupos de recursos de suscripción |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos de suscripción |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de plan web
Puede administrar planes web

| **Acciones** ||
| ------- | ------ |
| Microsoft.Web/serverFarms/* | Crear y administrar granjas de servidores |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos de suscripción |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer recursos de grupos de recursos de suscripción |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos de suscripción |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |

### Colaborador de sitio web
Puede administrar sitios web, pero no los planes web a los que están conectados

| **Acciones** ||
| ------- | ------ |
| Microsoft.Web/serverFarms/read | Leer las granjas de servidores |
| Microsoft.Web/serverFarms/join/action | Unir granjas de servidores |
| Microsoft.Web/sites/* | Crear y administrar sitios web |
| Microsoft.Web/certificates/* | Crear y administrar certificados de sitios web |
| Microsoft.Web/listSitesAssignedToHostName/read | Leer sitios asignados a un nombre de host |
| Microsoft.Authorization/*/read | Leer autorización |
| Microsoft.Resources/subscriptions/resourceGroups/read | Leer grupos de recursos de suscripción |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | Leer recursos de grupos de recursos de suscripción |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | Crear y administrar implementaciones de grupos de recursos de suscripción |
| Microsoft.Insights/alertRules/* | Crear y administrar reglas de alerta de Insights |
| Microsoft.Support/* | Crear y administrar incidencias de soporte técnico |
| Microsoft.Insights/components/* | Crear y administrar componentes de Insights |

## Temas de RBAC
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0128_2016-->