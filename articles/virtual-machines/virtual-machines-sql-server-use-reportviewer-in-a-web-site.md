<properties 
	pageTitle="Uso de ReportViewer en un sitio web | Microsoft Azure"
	description="En este tema se describe cómo crear un sitio web de Microsoft Azure con el control ReportViewer de Visual Studio que muestre un informe guardado en una máquina virtual de Microsoft Azure."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" 
	tags="azure-service-management" />
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="12/11/2015"
	ms.author="jroth" />

# Usar ReportViewer en un sitio web hospedado en Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


Puede crear un sitio web de Microsoft Azure con el control ReportViewer de Visual Studio que muestre un informe guardado en una máquina virtual de Microsoft Azure. El control ReportViewer se encuentra en una aplicación web que se crea con la plantilla de aplicación web ASP.NET.

>[AZURE.IMPORTANT]Las plantillas de aplicación web de MVC de ASP.NET no admiten el control ReportViewer.

Para incorporar ReportViewer en su sitio web de Microsoft Azure, debe completar las siguientes tareas.

- **Agregar** ensamblados al paquete de implementación

- **Configurar** autenticación y autorización

- **Publicar** la aplicación web ASP.NET en Azure

## Requisitos previos

Revise la sección "Recomendación general y procedimientos recomendados" de [SQL Server Business Intelligence en máquinas virtuales de Azure](virtual-machines-sql-server-business-intelligence.md).

>[AZURE.NOTE]Los controles ReportViewer se incluyen con Visual Studio, Standard Edition o superior. Si usa Web Developer Express Edition, debe instalar el [MICROSOFT REPORT VIEWER 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747) para usar las características de tiempo de ejecución de ReportViewer.
>
>No se admite el ReportViewer configurado en modo de procesamiento local en Microsoft Azure.

Revise las notas del producto [Control Visor de informes de Reporting Services y servidores de informes basados en máquinas virtuales de Microsoft Azure](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx).

## Adición de ensamblados al paquete de implementación

Cuando se hospeda la aplicación ASP.NET en local, los ensamblados de ReportViewer normalmente se instalan directamente en la memoria caché de ensamblados global (GAC) del servidor IIS durante la instalación de Visual Studio y la aplicación puede obtener acceso a ellos directamente. Sin embargo, cuando hospeda su aplicación ASP.NET en la nube, Microsoft Azure no permite que se instale nada en la GAC, por lo que debe asegurarse de que los ensamblados de ReportViewer están disponibles localmente para la aplicación. Puede ello, agregue referencias a los ensamblados en el proyecto y configúrelos para que se copien localmente.

En el modo de procesamiento remoto, el control ReportViewer usa los siguientes ensamblados:

- **Microsoft.ReportViewer.WebForms.dll**: contiene el código de ReportViewer, que se necesita para usar el ReportViewer en la página. Al colocar un control ReportViewer en una página ASP.NET del proyecto, se agrega una referencia para este ensamblado al proyecto.

- **Microsoft.ReportViewer.Common.dll**: contiene las clases usadas por el control ReportViewer en tiempo de ejecución. No se agrega automáticamente al proyecto.

### Para agregar una referencia a Microsoft.ReportViewer.Common

- Haga clic con el botón secundario en el nodo **Referencias** del proyecto y seleccione **Agregar referencia**, el ensamblado en la pestaña de .NET y haga clic en **Aceptar**.

### Para hacer que los ensamblados estén localmente disponibles para su aplicación ASP.NET

1. En la carpeta **Referencias**, haga clic en el ensamblado Microsoft.ReportViewer.Common, para que sus propiedades aparezcan en el panel Propiedades.

1. En el panel Propiedades, establezca **Copia local** en verdadero.

1. Repita los pasos 1 y 2 para Microsoft.ReportViewer.WebForms.

### Para obtener el paquete de idioma de ReportViewer

1. Instale el paquete redistribuible de Microsoft Report Viewer 2012 Runtime adecuado desde el [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=317386).

1. Seleccione el idioma en la lista desplegable y la página le dirigirá a la página del centro de descarga correspondiente.

1. Haga clic en **Descargar** para iniciar la descarga de ReportViewerLP.exe.

1. Después de descargar ReportViewerLP.exe, haga clic en **Ejecutar** para instalar inmediatamente o haga clic en **Guardar** para guardarlo en el equipo. Si hace clic en **Guardar**, recuerde el nombre de la carpeta donde se guarda el archivo.

1. Encuentre la carpeta donde guardó el archivo. Haga clic con el botón secundario en ReportViewerLP.exe, haga clic en **Ejecutar como administrador**, y luego en **Sí**.

1. Después de ejecutar ReportViewerLP.exe, verá que en la ruta c:\\windows\\assembly se encuentran los archivos de recursos **Microsoft.ReportViewer.Webforms.Resources** y **Microsoft.ReportViewer.Common.Resources**.

### Para configurar para el control ReportViewer localizado

1. Descargue e instale el paquete redistribuible de Microsoft Report Viewer 2012 Runtime siguiendo las instrucciones especificadas anteriormente.

1. Cree la carpeta <language> en el proyecto y copie allí los archivos de ensamblados de recursos asociados. Los archivos de ensamblados de recursos que se va a copiar son: **Microsoft.ReportViewer.Webforms.Resources.dll** y **Microsoft.ReportViewer.Common.Resources.dll**. Seleccione los archivos de ensamblados de recursos y, en el panel Propiedades, establezca **Copiar en el directorio de resultados** en "** Copiar siempre **".

1. Establezca la referencia cultural y la referencia cultural de la interfaz de usuario para el proyecto web. Para obtener más información sobre cómo establecer la referencia cultural y la referencia cultural de la interfaz de usuario para una página web ASP.NET, vea [Cómo establecer la referencia cultural y la referencia cultural de la interfaz de usuario para la globalización de páginas web de ASP.NET](http://go.microsoft.com/fwlink/?LinkId=237461).

## Configuración de autenticación y autorización

El control ReportViewer debe usar las credenciales adecuadas para autenticarse con el servidor de informes y las credenciales deben estar autorizadas por el servidor de informes para obtener acceso a los informes que quiere. Para obtener información sobre la autenticación, vea las notas del producto [Control Visor de informes de Reporting Services y servidores de informes basados en máquinas virtuales de Microsoft Azure](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## Publicar la aplicación web ASP.NET en Azure

Para instrucciones sobre cómo publicar una aplicación web ASP.NET en Azure, consulte [Cómo migrar y publicar una aplicación web en Azure desde Visual Studio](../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) e [Introducción a aplicaciones web y ASP.NET](../app-service-web/web-sites-dotnet-get-started.md).

>[AZURE.IMPORTANT]Si el comando Agregar proyecto de implementación de Azure o Agregar proyecto de servicio de nube de Azure no aparece en el menú contextual del Explorador de soluciones, puede que necesite cambiar el marco de trabajo de destino del proyecto a .NET Framework 4.
>
>Los dos comandos ofrecen esencialmente la misma funcionalidad. Uno u otro comando aparecerán en el menú contextual en función de qué versión del SDK de Microsoft Azure haya instalado.

## Recursos

[Informes de Microsoft](http://go.microsoft.com/fwlink/?LinkId=205399)

[Business Intelligence de SQL Server en Máquinas virtuales de Azure](virtual-machines-sql-server-business-intelligence.md)

[Usar PowerShell para crear una máquina virtual de Azure con un servidor de informes en modo nativo](virtual-machines-sql-server-create-native-mode-report-server-powershell.md)

[Control Visor de informes de Reporting Services y servidores de informes basados en máquinas virtuales de Microsoft Azure](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx)

<!---HONumber=AcomDC_1217_2015-->