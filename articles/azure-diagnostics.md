<properties
	pageTitle="Información general de Diagnósticos de Azure"
	description="Use Diagnósticos de Azure para realizar tareas de depuración, medición de rendimiento, supervisión y análisis de tráfico en servicios en la nube, en máquinas virtuales y en Service Fabric"
	services="multiple"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="robb"/>


# ¿Qué es Diagnósticos de Microsoft Azure?


Diagnósticos de Azure es la funcionalidad de Azure que habilita la recopilación de datos de diagnóstico en una aplicación implementada. Puede utilizar la extensión de diagnóstico desde un número de orígenes diferentes. Actualmente se admiten los roles web y de trabajo del Servicio en la nube de Azure, las máquinas virtuales de Azure que ejecutan Microsoft Windows y Service Fabric. Otros servicios de Azure tienen su propio diagnóstico independiente.

## Datos que puede recopilar

Diagnósticos de Azure puede recopilar los tipos de datos siguientes:

Origen de datos|Descripción
---|---
Contadores de rendimiento | Sistema operativo y contadores de rendimiento personalizados
Registros de aplicación | Seguimiento de mensajes escritos por la aplicación
Registros de eventos de Windows | Información enviada al sistema de registro de eventos de Windows
Origen de eventos de .NET | Eventos de escritura de código mediante la clase [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) de .NET
Registros IIS | Información sobre los sitios web de IIS
ETW basado en manifiesto | Eventos de Seguimiento de eventos para Windows generados por cualquier proceso
Volcados de memoria | Información sobre el estado del proceso en caso de bloqueo de una aplicación
Registros de errores personalizados | Registros creados por su aplicación o servicio
Registros de infraestructura de diagnóstico de Azure|Información sobre Diagnósticos

La extensión de diagnósticos de Azure puede transferir estos datos a una cuenta de almacenamiento de Azure o enviarlos a servicios como [Application Insights](./application-insights/app-insights-cloudservices.md). Estos datos se pueden usar para depurar y solucionar problemas, medir el rendimiento, supervisar el uso de los recursos, analizar el tráfico, planear la capacidad y realizar auditorías.


## Control de versiones
Consulte el [historial de versiones de Diagnósticos de Azure](azure-diagnostics-versioning-history.md).

## Pasos siguientes
Seleccione el servicio sobre el cual intenta recopilar diagnósticos y utilice los siguientes artículos para comenzar. Utilice los vínculos de diagnósticos generales de Azure como referencia para tareas específicas.

## Aplicaciones web
Tenga en cuenta que las aplicaciones web no utilizan Diagnósticos de Azure. Puede encontrar información equivalente en [Aplicaciones web](./app-service-web/web-sites-enable-diagnostic-log.md).

## Servicios en la nube que usan Diagnósticos de Azure
- Si utiliza Visual Studio, consulte [Depuración de una máquina virtual o un servicio en la nube de Azure en Visual Studio](./vs-azure-tools-debug-cloud-services-virtual-machines.md) para comenzar. De lo contrario, consulte
- [Supervisión de servicios en la nube](./cloud-services/cloud-services-how-to-monitor.md)
- [Habilitación de diagnósticos en Servicios en la nube y Máquinas virtuales de Azure](./cloud-services/cloud-services-dotnet-diagnostics.md)

Para temas más avanzados, consulte
- [Application Insights para los Servicios en la nube de Azure](./application-insights/app-insights-cloudservices.md)
- [Seguimiento del flujo en una aplicación de servicios en la nube con Diagnósticos de Azure](./cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
- [Uso de PowerShell para habilitar Diagnósticos de Azure en una máquina virtual con Windows](./virtual-machines/virtual-machines-extensions-diagnostics-windows-powershell.md)


## Máquinas virtuales que usan Diagnósticos de Azure
- Si utiliza Visual Studio, consulte [Depuración de una máquina virtual o un servicio en la nube de Azure en Visual Studio](./vs-azure-tools-debug-cloud-services-virtual-machines.md) para comenzar. De lo contrario, consulte
- [Set up Azure Diagnostics on an Azure Virtual Machine](./virtual-machines-dotnet-diagnostics.md) (Configuración de Diagnósticos de Azure en una máquina virtual de Azure)

Para temas más avanzados, consulte
- [Uso de PowerShell para habilitar Diagnósticos de Azure en una máquina virtual con Windows](./virtual-machines/virtual-machines-extensions-diagnostics-windows-powershell.md)
- [Creación de una máquina virtual de Windows con supervisión y diagnóstico mediante una plantilla del Administrador de recursos de Azure](./virtual-machines/virtual-machines-extensions-diagnostics-windows-template.md)

## Service Fabric con Diagnósticos de Azure
Empiece por el artículo [Supervisión y diagnóstico de servicios en una configuración de desarrollo de máquina local](./service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Cuando acceda a este artículo, verá muchos otros artículos de diagnósticos de Service Fabric disponibles en el árbol de navegación de la izquierda.

## Artículos generales de Diagnósticos de Azure
- [Configuración del esquema de Diagnósticos de Azure](https://msdn.microsoft.com/library/azure/mt634524.aspx): obtenga información sobre cómo cambiar el archivo de esquema para recopilar y redirigir los datos de diagnóstico. Tenga en cuenta que también puede utilizar Visual Studio para cambiar el archivo del esquema.
- [Almacenamiento y visualización de los datos de diagnóstico en Almacenamiento de Azure](./cloud-services/cloud-services-dotnet-diagnostics-storage.md): conozca los nombres de las tablas y los blobs donde se escriben los datos de diagnóstico.
- Aprenda a [utilizar los contadores de rendimiento en Diagnósticos de Azure](./cloud-services/cloud-services-dotnet-diagnostics-performance-counters.md).
- Aprenda a [redirigir la información de diagnóstico de Azure a Application Insights](./azure-diagnostics-configure-applicationinsights.md).
- Si tiene problemas al iniciar los diagnósticos o al buscar datos en las tablas de Almacenamiento de Azure, consulte [Azure Diagnostics Troubleshooting](./azure-diagnostics-troubleshooting.md) (Solución de problemas de Diagnósticos de Azure).

<!---HONumber=AcomDC_0302_2016-->