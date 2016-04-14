<properties 
   pageTitle="Introducción a la auditoría de bases de datos de Almacenamiento de datos SQL | Microsoft Azure" 
   description="Introducción a la auditoría de bases de datos de Almacenamiento de datos SQL" 
   services="sql-data-warehouse" 
   documentationCenter="" 
   authors="twounder" 
   manager="barbkess" 
   editor=""/>

<tags 
   ms.service="sql-data-warehouse" 
   ms.workload="data-management" 
   ms.tgt_pltfrm="na" 
   ms.devlang="na" 
   ms.topic="article" 
   ms.date="01/07/2016" 
   ms.author="mausher;barbkess;sonyama"/>
 
# Introducción a la auditoría de bases de datos de Almacenamiento de datos SQL 
La auditoría de Almacenamiento de datos SQL de Azure realiza el seguimiento de los eventos de base de datos y escribe los eventos auditados en un registro de auditoría en la cuenta de Almacenamiento de Azure.

La auditoría puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas.

Las herramientas de auditoría posibilitan y facilitan la observancia de estándares reguladores pero no garantizan el cumplimiento. Para obtener más información acerca de los programas de Azure compatibles con el cumplimiento de estándares, consulte el <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Centro de confianza de Azure</a>.

+ [Conceptos básicos de auditoría de Base de datos] 
+ [Configuración de la auditoría para su base de datos]
+ [Análisis de registros e informes de auditoría]

##<a id="subheading-1"></a>Conceptos básicos de auditoría de Almacenamiento de datos SQL de Azure


La auditoría de Almacenamiento de datos SQL la permite que:

- **Conservar** una traza de auditoría de eventos seleccionados. Puede definir categorías de acciones de base de datos para auditar.
- **Informar** sobre la actividad de la base de datos. Puede usar informes preconfigurados y un panel para empezar rápidamente con el informe de actividades y eventos.
- **Analizar** informes. Puede buscar eventos sospechosos, actividades inusuales y tendencias.

Puede configurar la auditoría para las categorías de eventos siguientes:

**SQL sin formato** y **SQL parametrizado** para los que los registros de auditoría recopilados se clasifican como

- **Acceso a datos**
- **Cambios de esquema (DDL)**
- **Cambios de datos (DML)**
- **Cuentas, roles y permisos (DCL)**
- **Procedimiento almacenado**, **Inicio de sesión** y **Administración de transacciones**.

Para cada categoría de eventos, las operaciones de **aciertos** y **errores** se configuran por separado.

Para obtener más detalles acerca de las actividades y eventos auditados, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">Referencia de formato del registro de auditoría (descarga de archivo .doc)</a>.

Los registros de auditoría se almacenan en su cuenta de almacenamiento de Azure. Puede definir un período de retención de registro de auditoría.

Puede definirse una directiva de auditoría para una base de datos específica o como directiva de servidor predeterminada. La directiva de auditoría de servidor predeterminada se aplicará a todas las bases de datos de un servidor que no tengan una directiva de auditoría de base de datos de reemplazo específica definida.

Antes de configurar la auditoría, compruebe si usa un ["Cliente de nivel inferior"](sql-data-warehouse-auditing-downlevel-clients.md).


##<a id="subheading-2"></a>Configuración de la auditoría para su base de datos

1. Inicie el <a href="https://portal.azure.com" target="_blank">Portal de Azure</a>.

2. Navegue a la hoja de configuración de la base de datos de Almacenamiento de datos SQL o el servidor de SQL Server que quiera auditar. Haga clic en el botón **Configuración** situado en la parte superior y, a continuación, en la hoja Configuración, y seleccione **Auditoría**.

	![][1]

3. En la hoja de configuración de auditoría, desactive primero la casilla **Heredar la configuración de auditoría del servidor**. Esto permite especificar la configuración de una base de datos determinada.
	
	![][2]

4. A continuación, para habilitar la auditoría, haga clic en el botón **ACTIVADO**.

	![][3]

5. En la hoja de configuración de auditoría, seleccione **DETALLES DE ALMACENAMIENTO** para abrir la hoja Almacenamiento de registros de auditoría. Seleccione la cuenta de almacenamiento de Azure donde se guardarán los registros y el período de retención. **Sugerencia**: use la misma cuenta de almacenamiento para todas las bases de datos auditadas con el fin de obtener el máximo rendimiento de las plantillas de informes preconfiguradas.

	![][4]

6. Haga clic en el botón **Aceptar** para guardar los detalles de configuración de almacenamiento.


7. En **REGISTRO POR EVENTO**, haga clic en **CORRECTO** y **ERROR** para registrar todos los eventos, o elija categorías individuales de eventos.


8. Si va a configurar la auditoría para una base de datos, puede que tenga que modificar la cadena de conexión del cliente para asegurarse de que los datos de auditoría se capturan correctamente. Consulte el tema [Modificación del FDQN de servidor en la cadena de conexión](sql-data-warehouse-auditing-downlevel-clients.md) sobre conexiones de cliente de nivel inferior.

9. Haga clic en **Aceptar**.


##<a id="subheading-3">Análisis de registros e informes de auditoría</a>

Los registros de auditoría se agregan en una recopilación de tablas de Almacenamiento con el prefijo **SQLDBAuditLogs** en la cuenta de almacenamiento de Azure que eligió durante la configuración. Puede ver los archivos de registro usando una herramienta como el <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Explorador de almacenamiento de Azure</a>.

Hay una plantilla de informe de panel preconfigurada disponible como <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">hoja de cálculo de Excel descargable</a> para ayudarle a analizar datos de registro rápidamente. Para utilizar la plantilla en los registros de auditoría, necesita Excel 2013 o posterior y Power Query, que puede descargar <a href="http://www.microsoft.com/download/details.aspx?id=39379">aquí</a>.

La plantilla contiene datos de ejemplo ficticios y puede configurar Power Query para importar el registro de auditoría directamente desde la cuenta de almacenamiento de Azure.

Para obtener instrucciones más detalladas sobre el trabajo con la plantilla de informe, lea el <a href="http://go.microsoft.com/fwlink/?LinkId=506731">documento de procedimiento (.doc descargable)</a>.

![][5]


##<a id="subheading-4">Prácticas para el uso en producción</a>
La descripción de esta sección se refiere a las capturas de pantalla anteriores. Se puede usar tanto el <a href="https://portal.azure.com" target="_blank">Portal de Azure</a> como el <a href= "https://manage.windowsazure.com/" target="_bank">Portal de Azure clásico</a>.
 

##<a id="subheading-5"></a>Regeneración de clave de almacenamiento

En el entorno de producción, es probable que actualice periódicamente las claves de almacenamiento. Cuando actualice las claves deberá volver a guardar la directiva. El proceso es el siguiente:


1. En la hoja de configuración de auditoría (descrita anteriormente en la sección de configuración de auditoría), cambie la **Clave de acceso de almacenamiento** de *Principal* a *Secundaria* y presione **GUARDAR**. ![][4]
2. Vaya a la hoja de configuración de almacenamiento y **regenere** la *Clave de acceso primaria*.

3. Vuelva a la hoja de configuración de auditoría, cambie la **Clave de acceso de almacenamiento** de *Secundaria* a *Principal* y presione **GUARDAR**.

4. Vuelva a la interfaz de usuario de almacenamiento y **regenere** la *Clave de acceso secundaria* (como preparación para el siguiente ciclo de actualización de las claves).
  
##<a id="subheading-6"></a>Automatización
Hay varios cmdlets de PowerShell que puede usar para configurar la auditoría en la base de datos de SQL de Azure. Para acceder a los cmdlets de auditoría debe ejecutar PowerShell en el modo del Administrador de recursos de Azure.

> [AZURE.NOTE]El módulo [Administrador de recursos de Azure](https://msdn.microsoft.com/library/dn654592.aspx) se encuentra disponible actualmente en modo de vista previa. Puede que no ofrezca las mismas capacidades de administración que el módulo de Azure.

Cuando esté en el modo Administrador de recursos de Azure, ejecute `Get-Command *AzureSql*` para enumerar los cmdlets disponibles.


<!--Anchors-->
[Conceptos básicos de auditoría de Base de datos]: #subheading-1
[Configuración de la auditoría para su base de datos]: #subheading-2
[Análisis de registros e informes de auditoría]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-get-started/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-get-started/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-get-started/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-get-started/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-get-started/sql-data-warehouse-auditing-dashboard.png


<!--Link references-->

<!---HONumber=AcomDC_0114_2016-->