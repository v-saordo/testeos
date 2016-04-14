<properties
	pageTitle="Introducción a la auditoría de bases de datos SQL | Microsoft Azure"
	description="Introducción a la auditoría de bases de datos SQL"
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="jeffreyg; ronitr"/>
 
# Introducción a la auditoría de bases de datos SQL
La auditoría de Base de datos SQL de Azure realiza un seguimiento de los eventos de base de datos y escribe eventos auditados en un registro de auditoría en la cuenta de Almacenamiento de Azure. La auditoría está generalmente disponible para los niveles de servicio Básico, Estándar y Premium.

La auditoría puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas.

Las herramientas de auditoría posibilitan y facilitan la observancia de estándares reguladores pero no garantizan el cumplimiento. Para obtener más información acerca de los programas de Azure compatibles con la observancia de estándares, consulte el [Centro de confianza de Azure](https://azure.microsoft.com/support/trust-center/compliance/).

+ [Conceptos básicos de la auditoría de Base de datos SQL de Azure]
+ [Configuración de la auditoría para su base de datos]
+ [Análisis de registros e informes de auditoría]

##<a id="subheading-1"></a>Conceptos básicos de la auditoría de Base de datos SQL de Azure

Las secciones siguientes describen la configuración de auditoría mediante el Portal de Azure. También puede [configurar la auditoría para su base de datos con el Portal de Azure clásico].

La auditoría de Base de datos SQL le permite:

- **Conservar** una traza de auditoría de eventos seleccionados. Puede definir categorías de acciones de base de datos para auditar.
- **Informar** sobre la actividad de la base de datos. Puede usar informes preconfigurados y un panel para empezar rápidamente con el informe de actividades y eventos.
- **Analizar** informes. Puede buscar eventos sospechosos, actividades inusuales y tendencias.

> [AZURE.NOTE] Ahora puede recibir alertas proactivas en actividades anómalas de la base de datos que pueden indicar posibles amenazas de seguridad con la nueva característica de **detección de amenazas**, ahora en vista previa. La detección de amenazas puede activarse y configurarse en la hoja de configuración de auditoría. Vea [Introducción a la detección de amenazas](sql-database-threat-detection-get-started.md) para obtener más detalles.

Puede configurar la auditoría para las categorías de eventos siguientes:

**SQL sin formato** y **SQL parametrizado** para los que los registros de auditoría recopilados se clasifican como

- **Acceso a datos**
- **Cambios de esquema (DDL)**
- **Cambios de datos (DML)**
- **Cuentas, roles y permisos (DCL)**
- **Procedimiento almacenado**, **Inicio de sesión** y **Administración de transacciones**.

Para cada categoría de eventos, las operaciones de **aciertos** y **errores** se configuran por separado.

Para obtener más detalles acerca de las actividades y eventos auditados, consulte [Referencia de formato del registro de auditoría (descarga de archivo .doc)](http://go.microsoft.com/fwlink/?LinkId=506733).

Los registros de auditoría se almacenan en su cuenta de almacenamiento de Azure. Puede definir un período de conservación para el registro de auditoría, después del cual se eliminarán los registros antiguos.

Puede definirse una directiva de auditoría para una base de datos específica o como directiva de servidor predeterminada. La directiva de auditoría de servidor predeterminada se aplicará a todas las bases de datos de un servidor que no tengan una directiva de auditoría de base de datos de reemplazo específica definida.

Antes de configurar la auditoría, compruebe si usa un [Cliente de nivel inferior](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).


##<a id="subheading-2"></a>Configuración de la auditoría para su base de datos

1. Inicie el [Portal de Azure](https://portal.azure.com) en https://portal.azure.com. También puede iniciar el [Portal de Azure clásico](https://manage.windowsazure.com/) en https://manage.windowsazure.com/. Consulte los detalles que aparecen a continuación.

2. Vaya a la hoja de configuración de la base de datos SQL o el servidor SQL Server que desea auditar. En la hoja Configuración, seleccione **Auditoría y detección de amenazas**.

	![Panel de navegación][1]

3. En la hoja de configuración de auditoría, **active** la auditoría.

4. Seleccione **Detalles de almacenamiento** para abrir la hoja de almacenamiento de registros de auditoría. Seleccione la cuenta de almacenamiento de Azure donde se guardarán los registros y el período de conservación. **Sugerencia:** use la misma cuenta de almacenamiento para todas las bases de datos auditadas con el fin de obtener el máximo rendimiento de las plantillas de informes de auditorías.

	![Panel de navegación][2]

5. Haga clic en **Eventos auditados** para personalizar los eventos que se van a auditar. En la hoja **Registro por evento** haga clic en **Acierto** y **Error** para registrar todos los eventos, o elija categorías individuales de eventos.


6. Puede activar la casilla **Heredar la configuración de auditoría del servidor** para indicar que esta base de datos se auditará según la configuración de su servidor. Una vez que active esta opción, verá un vínculo que permite ver o modificar la configuración de la auditoría de servidor de este contexto.

	![Panel de navegación][3]

7. Una vez haya establecido la configuración de la auditoría, puede **activar** la detección de amenazas y configurar los mensajes de correo electrónico para recibir alertas de seguridad. Consulte la página [Introducción a la detección de amenazas](sql-database-threat-detection-get-started.md) para obtener más detalles.

8. Haga clic en **Guardar**.



##<a id="subheading-3"></a>Análisis de registros e informes de auditoría

Los registros de auditoría se agregan en una recopilación de tablas de Almacenamiento con el prefijo **SQLDBAuditLogs** en la cuenta de almacenamiento de Azure que eligió durante la configuración. Puede ver archivos de registro usando una herramienta como el [Explorador de almacenamiento de Azure](http://azurestorageexplorer.codeplex.com/).

Hay una plantilla de informe preconfigurada disponible como [hoja de cálculo de Excel descargable](http://go.microsoft.com/fwlink/?LinkId=403540) para ayudarle a analizar datos de registro rápidamente. Para utilizar la plantilla en los registros de auditoría, necesita Excel 2013 o posterior y Power Query, que puede descargar [aquí](http://www.microsoft.com/download/details.aspx?id=39379).

Puede importar los registros de auditoría en la plantilla de Excel directamente desde su cuenta de almacenamiento de Azure con Power Query. A continuación, puede explorar los registros de auditoría y crear paneles e informes sobre los datos del registro.


![Panel de navegación][4]


##<a id="subheading-4"></a>Configuración de la auditoría para su base de datos con el Portal de Azure clásico

1. Inicie el [Portal de Azure clásico](https://manage.windowsazure.com/) en https://manage.windowsazure.com/.

2. Haga clic en la base de datos o el servidor de SQL Server que desea auditar y, a continuación, haga clic en la pestaña **Auditoría y seguridad**.

3. Establezca Auditoría en **HABILITADO**.

	![Panel de navegación][5]

4. Editar la **REGISTRO POR EVENTO** según sea necesario, para personalizar los eventos que se auditarán.

	![Panel de navegación][6]

5. Seleccione una **CUENTA DE ALMACENAMIENTO** y configure la **RETENCIÓN**.

	![Panel de navegación][7]

6. Haga clic en **GUARDAR**.




##<a id="subheading-5">Prácticas para el uso en producción</a>
La descripción de esta sección se refiere a las capturas de pantalla anteriores. Se puede usar tanto el [Portal de Azure](https://portal.azure.com) como el [Portal de Azure clásico](https://manage.windowsazure.com/).


##<a id="subheading-6"></a>Regeneración de clave de almacenamiento

En el entorno de producción, es probable que actualice periódicamente las claves de almacenamiento. Al actualizar las claves se debe volver a guardar la directiva de auditoría. El proceso es el siguiente:


1. Vuelva a la hoja de configuración de auditoría, cambie la **Clave de acceso de almacenamiento** de *Principal* a *Secundaria* y haga clic en **GUARDAR**.

	![][8]

2. Vaya a la hoja de configuración de almacenamiento y **regenere** la *Clave de acceso primaria*.

3. Vuelva a la hoja de configuración de auditoría, cambie la **Clave de acceso de almacenamiento** de *Secundaria* a *Principal* y haga clic en **GUARDAR**.

4. Vuelva a la interfaz de usuario de almacenamiento y **vuelva a generar** la *Clave de acceso secundaria* (como preparación para el siguiente ciclo de actualización de las claves).
  
##<a id="subheading-7"></a>Automatización
Existen varios cmdlets de PowerShell que puede usar para configurar la auditoría en la base de datos de SQL de Azure.

- [Get-AzureRMSqlDatabaseAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603731.aspx)
- [Get-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt619329.aspx)
- [Remove-AzureRMSqlDatabaseAuditing](https://msdn.microsoft.com/library/azure/mt603796.aspx)
- [Remove-AzureRMSqlServerAuditing](https://msdn.microsoft.com/library/azure/mt603574.aspx)
- [Set-AzureRMSqlDatabaseAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603531.aspx)
- [Set-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603794.aspx)
- [Use-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt619353.aspx)




<!--Anchors-->
[Conceptos básicos de la auditoría de Base de datos SQL de Azure]: #subheading-1
[Configuración de la auditoría para su base de datos]: #subheading-2
[Análisis de registros e informes de auditoría]: #subheading-3
[configurar la auditoría para su base de datos con el Portal de Azure clásico]: #subheading-4
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation]: #subheading-7


<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_storage_account.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_inherit_from_server.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_report_template.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_classic_portal_enable.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_classic_portal_events.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_classic_portal_storage.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_storage_key_rotation.png


 

<!---HONumber=AcomDC_0204_2016-->