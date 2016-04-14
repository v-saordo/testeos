<properties
   pageTitle="Administración de registros de auditoría y seguridad de Microsoft Azure | Microsoft Azure"
   description="El artículo brinda una presentación para generar, recopilar y analizar registros de seguridad provenientes de los servicios hospedados en Azure. Está pensado para profesionales de TI y analistas de seguridad que se enfrentan diariamente con la administración de activos de información, incluidos quienes son responsables de los esfuerzos de seguridad y cumplimiento de su organización."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="nayak-mahesh"
   manager="msStevenPo"
   editor=""/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/10/2015"
   ms.author="mnayak;tomsh;terrylan"/>

# Administración de registros de auditoría y seguridad de Microsoft Azure

Azure permite que los clientes realicen la generación y recopilación de eventos de seguridad desde los roles de Infraestructura como servicio (IaaS) y Plataforma como servicio (PaaS) de Azure hasta el almacenamiento central en sus suscripciones. Los clientes entonces pueden utilizar [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) para agregar y analizar los eventos recopilados. Además, estos eventos recopilados se pueden exportar a sistemas de administración de eventos e información de seguridad (SIEM) locales para una supervisión continua.

El ciclo de vida de registro, análisis y supervisión de seguridad de Azure incluye:

- **Generación**: instrumentar aplicaciones y la infraestructura para generar eventos.
- **Recopilación**: configurar Azure para recopilar los diversos registros de seguridad en una cuenta de almacenamiento.
- **Análisis**: usar herramientas de Azure, como HDInsight y sistemas de SIEM locales, para analizar los registros y generar información detallada sobre la seguridad.
- **Supervisión e informes**: Azure ofrece sistemas centralizados de supervisión y análisis que proporcionan visibilidad continua y alertas oportunas.

Este artículo se centra en las fases de generación y recopilación del ciclo de vida.

## Generación de registros
Los eventos de seguridad se generan en el registro de eventos de Windows para los canales de **Sistema**, **Seguridad** y **Aplicación** en las máquinas virtuales. Para garantizar que los eventos se registren sin una posible pérdida de datos, es importante configurar adecuadamente el tamaño del registro de eventos. El tamaño del registro de eventos se debe basar en la cantidad de eventos que genera la configuración de la directiva de auditoría y en las directivas de recopilación de eventos. Si desea obtener más información, consulte [Planeación para la administración y supervisión de auditoría de seguridad](http://technet.microsoft.com/library/ee513968.aspx#BKMK_4).

>[AZURE.NOTE] Cuando se utilice Reenvío de eventos de Windows (WEF) o Diagnósticos de Azure (que se explican en la sección [Recopilación de registros](#log-collection)) para extraer registros desde Servicios en la nube o máquinas virtuales, considere el potencial impacto de interrupciones en el sistema. Por ejemplo, si el entorno de WEF deja de funcionar durante un tiempo, deberá asegurarse de que el tamaño del registro es suficientemente grande para considerar una duración más prolongada, o bien estar preparado para una posible pérdida de datos del registro.

En el caso de las aplicaciones de Servicios en la nube implementadas en Azure y máquinas virtuales creadas desde el [Catálogo de máquinas virtuales de Azure](https://azure.microsoft.com/marketplace/virtual-machines/#microsoft), se habilita un conjunto de eventos de seguridad del sistema operativo de manera predeterminada. Los clientes pueden agregar, quitar o modificar los eventos que se auditarán a través de la personalización de la directiva de auditoría del sistema operativo. Para obtener más información, consulte [Security Policy Settings Reference](http://technet.microsoft.com/library/jj852210.aspx) (Referencia de la configuración de la directiva de seguridad).

Puede utilizar los siguientes métodos para generar registros adicionales desde el sistema operativo (como cambios en la directiva de auditoría) y componentes de Windows (como IIS):

- Directiva de grupo para implementar la configuración de directivas correspondiente a las máquinas virtual de Azure que están unidas a un dominio.
- Configuración de estado deseado (DSC) para insertar y administrar la configuración de directivas. Para obtener más información, consulte [Azure PowerShell DSC](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx).
- Código de inicio del rol de Implementación de servicio para implementar la configuración correspondiente a Servicios en la nube (escenario PaaS).

Configurar las tareas de inicio del rol de Azure permite que el código se ejecute antes de que se inicie un rol. Puede definir una tarea de inicio para un rol; para ello, agregue el elemento **Startup** a la definición del rol en el archivo de definición de servicio, tal como se muestra en el ejemplo siguiente. Para más información, consulte [Ejecutar tareas de inicio en Azure](http://msdn.microsoft.com/library/azure/hh180155.aspx).

El archivo de tarea que se debe ejecutar como una tarea de inicio (EnableLogOnAudit.cmd en el ejemplo siguiente) debe estar incluido en el paquete de compilación. Si utiliza Visual Studio, agregue el archivo al proyecto en la nube, haga clic con el botón derecho en el nombre del archivo, haga clic en **Propiedades** y luego establezca **Copiar en el directorio de salida** en **Copiar siempre**.

    <Startup>
        <Task commandLine="EnableLogOnAudit.cmd" executionContext="elevated" taskType="simple" />
    </Startup>

Contenido de EnableLogOnAudit.cmd:

    @echo off
    auditpol.exe /set /category:"Logon/Logoff" /success:enable /failure:enable
    Exit /B 0

El archivo [Auditpol.exe](https://technet.microsoft.com/library/cc731451.aspx) que se usó en el ejemplo anterior es una herramienta de la línea de comandos incluida en el sistema operativo de Windows Server que le permite administrar la configuración de las directivas de auditoría.

Además de generar los registros de eventos de Windows, varios componentes del sistema operativo de Windows se pueden configurar para generar registros importantes para la supervisión y el análisis de seguridad. Por ejemplo, se generan automáticamente registros de Internet Information Services (IIS) y registros de http.er para roles web y se pueden configurar para la recopilación. Estos registros proporcionan información valiosa que se puede usar para identificar el acceso no autorizado o ataques contra el rol web. Para obtener más información, consulte [Configurar el registro en IIS](http://technet.microsoft.com/library/hh831775.aspx) y [Advanced Logging for IIS – Custom Logging](http://www.iis.net/learn/extensions/advanced-logging-module/advanced-logging-for-iis-custom-logging) (Registro avanzado para IIS: registro personalizado).

Para cambiar el registro IIS en un rol web, los clientes pueden agregar una tarea de inicio en el archivo de definición de servicio de rol web. El ejemplo siguiente habilita el registro HTTP de un sitio web llamado Contoso y especifica que IIS debe registrar todas las solicitudes correspondientes al sitio web de Contoso.

La tarea que actualiza la configuración de IIS se debe incluir en el archivo de definición de servicio del rol web. Los cambios siguientes en el archivo de definición de servicio ejecutan una tarea de inicio que configura el registro IIS a través de la ejecución de un script llamado ConfigureIISLogging.cmd.

    <Startup>
        <Task commandLine="ConfigureIISLogging.cmd" executionContext="elevated" taskType="simple" />
    </Startup>

Contenido de ConfigureIISLogging.cmd

    @echo off
    appcmd.exe set config "Contoso" -section:system.webServer/httpLogging /dontLog:"True" /commit:apphost
    appcmd.exe set config "Contoso" -section:system.webServer/httpLogging /selectiveLogging:"LogAll" /commit
    Exit /B 0


## <a name="log-collection"></a>Recopilación de registros
La recopilación de registros y eventos de seguridad desde Servicios en la nube o máquinas virtuales de Azure se realiza a través de dos métodos principales:

- Diagnósticos de Azure, que recopila eventos en una cuenta de almacenamiento de Azure del cliente.
- Reenvío de eventos de Windows (WEF), una tecnología existente en equipos que ejecutan Windows.

La tabla que aparece a continuación incluye algunas diferencias clave entre estas dos tecnologías. Debe elegir el método adecuado para implementar la recopilación de registros según sus requisitos y estas diferencias clave.

| Diagnóstico de Azure | Reenvío de eventos de Windows |
|-----|-----|
|Admite Máquinas virtuales de Azure y Servicios en la nube de Azure | Admite solo Máquinas virtuales de Azure unidas a un dominio |
|Admite una variedad de formatos de registros, como registros de eventos de Windows, seguimientos de[Seguimiento de eventos para Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) y registros IIS. Para obtener más información, consulte [Orígenes de datos compatibles con Diagnósticos de Azure](#diagnostics) |Admite solo los registros de eventos de Windows |
|Inserta los datos recopilados en Almacenamiento de Azure |Mueve los datos recopilados a los servidores de recopiladores centrales |

##	Recopilación de datos de eventos de seguridad con Reenvío de eventos de Windows
En el caso de Máquinas virtuales de Azure unidas a un dominio, puede configurar WEF con la configuración de directiva de grupo del mismo modo que para los equipos locales unidos a un dominio. Para obtener más información, consulte [Nube híbrida](http://www.microsoft.com/server-cloud/solutions/hybrid-cloud.aspx).

Con este enfoque, una organización podría comprar una suscripción a IaaS, conectarla a su red corporativa mediante [ExpressRoute](https://azure.microsoft.com/services/expressroute/) o una VPN de sitio a sitio y luego unir las máquinas virtuales que tiene en Azure al dominio corporativo. Después, puede configurar WEF desde las máquinas unidas a un dominio.

El reenvío de eventos se divide en dos partes: el origen y el recopilador. El origen es el equipo donde se generan los registros de seguridad. El recopilador es el servidor centralizado que recopila y consolida los registros de eventos. Los administradores de TI se pueden suscribir a eventos para poder recibir y almacenar los eventos que se reenvían desde equipos remotos (el origen del evento). Para obtener más información, consulte [Configurar equipos para reenviar y recopilar eventos](http://technet.microsoft.com/library/cc748890.aspx).

Los eventos recopilados de Windows se pueden enviar a herramientas de análisis locales, como SIEM, para su posterior análisis.

## Recopilación de datos de seguridad con Diagnósticos de Azure
Diagnósticos de Azure le permite recopilar datos de diagnóstico desde un rol web o rol de trabajo de un servicio en la nube, o bien desde máquinas virtuales que se ejecutan en Azure. Es una extensión de agente invitado que se debe habilitar y configurar para la recopilación de datos. La suscripción de un cliente puede incluir insertar los datos en Almacenamiento de Azure.

Los datos se cifran en tránsito (mediante HTTPS). Los ejemplos proporcionados en este documento utilizan Diagnósticos de Azure 1.2. Se recomienda actualizar a la versión 1.2 o posterior para realizar la recopilación de datos de seguridad. Para obtener más información, consulte [Recopilar datos de registro mediante Diagnósticos de Azure](http://msdn.microsoft.com/library/gg433048.aspx).

El diagrama siguiente muestra un flujo de datos de alto nivel para la recopilación de datos de seguridad que utiliza Diagnósticos de Azure y un posterior análisis y supervisión

![][1]

Diagnósticos de Azure mueve registros desde las aplicaciones de Servicios en la nube del cliente y [Máquinas virtuales de Azure](virtual-machines-about.md) a Almacenamiento de Azure. En función de un formato de registro, algunos datos se almacenan en tablas de Azure y otros en blobs. Los datos que se recopilan en [Almacenamiento de Azure](storage-introduction.md) se descargarán a sistemas de SIEM locales mediante el uso de la biblioteca de clientes de Almacenamiento de Azure para supervisión y análisis.

Además, se puede utilizar HDInsight para realizar un posterior análisis de los datos en la nube. A continuación, aparecen algunos ejemplos de la recopilación de datos de seguridad que utilizan Diagnósticos de Azure.

### Recopilación de datos de seguridad desde Máquinas virtuales de Azure con Diagnósticos de Azure

Los ejemplos siguientes utilizan cmdlets de Azure PowerShell y Diagnósticos de Azure 1.2 para habilitar la recopilación de datos de seguridad desde máquinas virtuales. Los datos se recopilan desde máquinas virtuales según un intervalo programado (que se puede configurar) y se insertan en Almacenamiento de Azure dentro de la suscripción de un cliente. En esta sección, analizaremos dos escenarios de recopilación de registros con Diagnósticos de Azure:

1. Configuración de una instancia nueva de una canalización de recopilación de registros de seguridad en una máquina virtual.
2. Actualización de una canalización de recopilación de registros de seguridad existente con una configuración nueva en una máquina virtual.

#### Configuración de una instancia nueva de una canalización de recopilación de registros de seguridad en una máquina virtual
En este ejemplo, configuramos una instancia nueva de una canalización de recopilación de registros de seguridad que utiliza Diagnósticos de Azure y detectamos eventos de error de inicio de sesión (identificadores de evento 4624 y 4625) desde las máquinas virtuales. Puede implementar los pasos siguientes desde el entorno de desarrollo, o bien puede utilizar una sesión de Escritorio remoto a través del Protocolo de escritorio remoto (RDP) al nodo en la nube.

##### Paso 1: Instalar el SDK de Azure PowerShell
El SDK de Azure PowerShell proporciona cmdlets para configurar Diagnósticos de Azure en Máquinas virtuales de Azure. Los cmdlets necesarios se encuentran disponibles en la versión 0.8.7 o posterior de Azure PowerShell. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](powershell-install-configure.md).

##### Paso 2: Preparar el archivo de configuración
Prepare el archivo de configuración según los eventos que desea recopilar. A continuación, aparece un ejemplo de un archivo de configuración de Diagnósticos de Azure para recopilar eventos de Windows desde el canal **Seguridad**, con filtros agregados para recopilar únicamente eventos de error y eventos satisfactorios de inicio de sesión. Para obtener más información, consulte [Esquema de configuración de Diagnósticos de Azure 1.2](http://msdn.microsoft.com/library/azure/dn782207.aspx).

La cuenta de almacenamiento se puede especificar en el archivo de configuración, o bien se puede especificar como parámetro cuando ejecuta los cmdlets de Azure PowerShell para configurar Diagnósticos de Azure.

    <?xml version="1.0" encoding="utf-8" ?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
            <DiagnosticMonitorConfiguration overallQuotaInMB="10000">
                <WindowsEventLog scheduledTransferPeriod="PT1M">
                    <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
                </WindowsEventLog>
            </DiagnosticMonitorConfiguration>
        </WadCfg>
    </PublicConfig>

Cuando guarde el contenido anterior como archivo XML, establezca la codificación en **UTF-8**. Si utiliza Bloc de notas, verá la opción de codificación disponible en el cuadro de diálogo "Guardar como". La tabla siguiente enumera algunos atributos clave para anotar en el archivo de configuración.

| Atributo | Descripción |
|----- |-----|
| overallQuotaInMB | La cantidad máxima de espacio en el disco local que Diagnósticos de Azure puede consumir (el valor se puede configurar). |
| scheduledTransferPeriod | El intervalo existente entre las transferencias programadas a Almacenamiento de Azure, redondeado al minuto más cercano. |
| Nombre | En WindowsEventLog, este atributo es la consulta de XPath que describe los eventos de Windows que se recopilarán. Puede filtrar la recopilación de datos si agrega un filtro como Id. de evento, Nombre del proveedor o Canal. |

Todos los datos del registro de eventos de Windows se mueven a una tabla denominada **WADWindowsEventLogsTable**. Actualmente, Diagnósticos de Azure no permite cambiar el nombre de la tabla.

##### <a name="step3"></a> Paso 3: Validar el archivo XML de configuración
Utilice el procedimiento siguiente para validar que no existe error en el archivo XML de configuración y que es compatible con el esquema de Diagnósticos de Azure:

1. Para descargar el archivo de esquema, ejecute el comando siguiente y luego guarde el archivo.

    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfigSchema.xsd'

2. Una vez que descargue el archivo de esquema, podrá validar el XML de configuración contra el esquema. Para validar el archivo con Visual Studio:
  - Abra el archivo XML en Visual Studio
  - Presione F4 para abrir **Propiedades**
  - Haga clic en **Esquema**, **Agregar**, seleccione el archivo de esquema que descargó (WadConfigSchema.XSD) y luego haga clic en **Aceptar**

3.	En el menú **Ver**, haga clic en **Lista de errores** para ver si existe algún error de validación.

##### <a name="step4"></a> Paso 4: Configurar Diagnósticos de Azure
 Utilice los pasos siguientes para habilitar Diagnósticos de Azure y comenzar la recopilación de datos:

 1.	Para abrir Azure PowerShell, escriba **Add-AzureAccount** y presione ENTRAR.
 2.	Inicie sesión con su cuenta de Azure.
 3.	Ejecute el siguiente script de PowerShell. Asegúrese de actualizar los valores storage\_name, key, config\_path, service\_name y vm\_name.

 ```PowerShell
$storage_name ="<Storage Name>"
$key = "<Storage Key>"
$config_path="<Path Of WAD Config XML>"
$service_name="<Service Name. Usually it is same as VM Name>"
$vm_name="<VM Name>"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
$VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name
$VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext
$VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM
 ```

##### Paso 5: Generar eventos
Para fines de demostración, crearemos algunos eventos de inicio de sesión y comprobaremos que los datos fluyan a Almacenamiento de Azure. Tal como anteriormente se mostró en el paso 2, el archivo XML está configurado para recopilar el id. de evento 4624 (inicio de sesión correcto) y el id. de evento 4625 (error de inicio de sesión) desde el canal **Seguridad**.

 Para generar estos eventos:

1.	Abra una sesión de RDP a su máquina virtual.
2.	Escriba credenciales incorrectas para generar algunos eventos de inicio de sesión con error (Id. de evento 4625).
3.	Después de algunos intentos de inicio de sesión con error, escriba las credenciales correctas para generar un evento de inicio de sesión correcto (EventID 4624).

##### Paso 6: Ver datos
Aproximadamente 5 minutos después de completar los pasos anteriores, los datos debieran comenzar a fluir a la cuenta de almacenamiento de cliente en función de la configuración existente en el archivo XML. Hay muchas herramientas disponibles para ver datos desde Almacenamiento de Azure. Para más información, consulte:

- [Explorar recursos de almacenamiento con el Explorador de servidores](http://msdn.microsoft.com/library/azure/ff683677.aspx)
- [Azure Storage Explorer 6 Preview 3 (Vista previa 3 del Explorador de Almacenamiento de Azure 6) (agosto de 2014)](http://azurestorageexplorer.codeplex.com/)

Para ver los datos:

1.	En Visual Studio (2013, 2012 y 2010 con SP1), haga clic en **Ver** y luego en **Explorador de servidores**.
2.	Navegue a la cuenta de almacenamiento.
3.	Haga clic en **Tablas** y luego haga doble clic en las tablas adecuadas para consultar los registros de seguridad recopilados desde las máquinas virtuales. ![][2]

4.	Haga clic con el botón derecho en la tabla llamada WADWindowsEventLogsTable y luego haga clic en **Ver datos** para abrir la vista de la tabla como se muestra a continuación:

![][3]

En la tabla de almacenamiento anterior, **PartitionKey**, **RowKey** y **Timestamp** son propiedades del sistema.

- **PartitionKey** es una marca de tiempo en segundos y es un identificador único de la partición dentro de la tabla.
- **RowKey** es un identificador único de una identidad dentro de una partición.

Tanto **PartitionKey** como **RowKey** identifican de forma exclusiva todas las entidades de una tabla.

- Una marca de tiempo es un valor de fecha/hora que se mantiene en el servidor para hacer un seguimiento de la última modificación de una entidad.

>[AZURE.NOTE] El tamaño máximo de fila en una tabla de Almacenamiento de Azure está limitado a 1 MB. Una cuenta de almacenamiento puede contener hasta 200 TB de datos provenientes de blobs, colas y tablas si la cuenta se creó después de junio de 2012. Por lo tanto, el tamaño de la tabla puede llegar a 200 TB si los blobs y las colas no ocupan espacio de almacenamiento. Las cuentas creadas antes de junio de 2012 tienen un límite de 100 TB.

Explorador de almacenamiento también le ofrece la opción de editar los datos de la tabla. Haga doble clic en una fila determinada en la vista Tabla para abrir la ventana Editar entidad, como se muestra a continuación:

![][4]

#### Actualización de una canalización de recopilación de registros de seguridad existente con una configuración nueva en una máquina virtual
En esta sección, actualizamos una canalización de recopilación de registros de seguridad de Diagnósticos de Azure existente en una máquina virtual y detectamos errores de registros de eventos de la aplicación de Windows.

##### Paso 1: Actualizar archivo de configuración para incluir eventos de interés
El archivo de Diagnósticos de Azure creado en el ejemplo anterior se debe actualizar para incluir los tipos de errores de registros de eventos de la aplicación de Windows.

>[AZURE.NOTE] Las opciones de configuración de Diagnósticos de Azure existentes se deben combinar con el nuevo archivo de configuración. La configuración definida en el archivo nuevo sobrescribe las configuraciones existentes.

Si desea recuperar la opción de configuración existente, utilice el cmdlet **Get-AzureVMDiagnosticsExtension**. El siguiente es un script de Azure PowerShell de ejemplo para recuperar la configuración existente:

    $service_name="<VM Name>"
    $VM1 = Get-AzureVM -ServiceName $service_name
    $config = Get-AzureVMDiagnosticsExtension -VM $VM1 | Select -Expand PublicConfiguration | % {$_.substring($_.IndexOf(':"')+2,$_.LastIndexOf('",')-$_.IndexOf(':"')-2)}
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($config))
Actualice la configuración de Diagnósticos de Azure para recopilar eventos críticos y errores de registros de eventos de la aplicación de Windows, tal como se muestra a continuación:

    <?xml version="1.0" encoding="utf-8" ?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="10000">
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level =2)]]" />
                <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
            </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

Valide el archivo de configuración; para ello, utilice los mismos pasos que se mostraron anteriormente en el [Paso 3: Validar el archivo XML de configuración](#step3).

##### Paso 2: Actualizar Diagnósticos de Azure para utilizar el archivo de configuración nuevo
Utilice los cmdlets **Set-AzureVMDiagnosticsExtension** y **Update-AzureVM** para actualizar la configuración, tal como se mostró anteriormente en el [Paso 4: Configurar Diagnósticos de Azure](#step4).

##### Paso 3: Comprobar las opciones de configuración
Ejecute el comando siguiente para comprobar que se actualizaron las opciones de configuración:

    $service_name="<VM Name>"
    $VM1 = Get-AzureVM -ServiceName $service_name
    Get-AzureVMDiagnosticsExtension -VM $VM1

##### Paso 4: Generar eventos
En este ejemplo, ejecute el comando siguiente para generar un registro de eventos de la aplicación del tipo **Error**:

    eventcreate /t error /id 100 /l application /d "Create event in application log for Demo Purpose"

![][5]

Abra el Visor de eventos para comprobar que se creó el evento.

![][6]

##### Paso 5: Ver datos
Abra Explorador de servidores en Visual Studio para ver los datos del registro. Debería ver un **EventID 100** creado en **ContosoDesktop**, tal como se muestra a continuación:

![][7]

## Recopilación de datos de seguridad desde Servicios en la nube de Azure con Diagnósticos de Azure

Ahora utilizaremos Diagnósticos de Azure para explorar los mismos dos escenarios de recopilación de registros desde Servicios en la nube de Azure de la sección de Máquinas virtuales (IaaS) anterior:

1.	Configure una instancia nueva de canalización de registros de seguridad en un servicio en la nube.
2.	Actualice una canalización de recopilación de registros existente con una configuración nueva en un servicio en la nube.

El tutorial paso a paso de esta sección incluye:

1.	Crear un servicio en la nube.
2.	Configurar el servicio en la nube para la recopilación de registros de seguridad con Diagnósticos de Azure.
3.	Ilustrar la generación y recopilación de eventos de seguridad en Servicio en la nube:

    - Agregar un administrador a un grupo local con una elevación de privilegios
    - Creación de un proceso nuevo
4.	Actualizar una canalización de recopilación de registros existente en un servicio en la nube:

    - Habilitar la auditoría de eventos de firewall de host (como ejemplo de los eventos de seguridad de red) con Auditpol
    - Configurar los datos de auditoría de firewall que se recopilarán y mostrar los eventos recopilados en la cuenta de almacenamiento del cliente
5.	Mostrar la distribución de eventos de seguridad de Windows y la detección de alzas.
6.	Configurar la recopilación de registros de IIS y compruebe los datos.

Todos los eventos y registros se recopilan en una cuenta de almacenamiento de cliente en Azure. El cliente puede ver y exportar los eventos a sistemas de SIEM locales. También se pueden agregar y analizar con HDInsight.

### Configuración de una instancia nueva de una canalización de recopilación de registros en un servicio en la nube
En este ejemplo, configuramos una instancia nueva de una canalización de recopilación de registros de seguridad que utiliza Diagnósticos de Azure y podemos detectar la incorporación de un usuario a un grupo local y eventos de creación de procesos nuevos en una instancia del servicio en la nube.

#### Paso 1: Crear un servicio en la nube (rol web) e implementarlo

1.	En el equipo del desarrollador, inicie Visual Studio 2013.
2.	Cree un proyecto de servicio en la nube nuevo (nuestro ejemplo utiliza ContosoWebRole).
3.	Seleccione el rol web **ASP.NET**.
4.	Seleccione el proyecto **MVC**.
5.	En el Explorador de soluciones, haga clic en **Roles** y luego haga doble clic en el rol web (WebRole1) para abrir la ventana **Propiedades**.
6.	En la pestaña **Configuración**, elimine la selección de la casilla **Habilitar Diagnósticos** para deshabilitar la versión de Diagnósticos de Azure que se incluye con Visual Studio 2013. ![][8]

7.	Compile la solución para comprobar que no hay errores.
8.	Abra el archivo WebRole1/Controllers/HomeController.cs.
9.	Agregue el método siguiente para habilitar la aplicación de ejemplo para registrar el código de estado HTTP 500 como evento de registro de IIS de ejemplo (se usará más adelante en el ejemplo de IIS):

    ```
    public ActionResult StatusCode500()
        {
            throw new InvalidOperationException("Response.StatusCode is 500");
        }
    ```

10.	 Haga clic con el botón derecho en el nombre del proyecto de servicio en la nube y luego haga clic en **Publicar**.

#### Paso 2: Preparar el archivo de configuración
Ahora prepararemos el archivo de configuración de Diagnósticos de Azure para agregar los eventos que pueden ayudar a registrar las situaciones siguientes:

- Incorporación de un usuario nuevo a un grupo local
- Creación de un proceso nuevo

```
<?xml version="1.0" encoding="UTF-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
</PublicConfig>
```

#### Paso 3: Validar el archivo XML de configuración
Valide el archivo de configuración; para ello, utilice los mismos pasos que se mostraron anteriormente en el [Paso 3: Validar el archivo XML de configuración](#step3).
#### Paso 4: Configurar Diagnósticos de Azure
Ejecute el siguiente script de Azure PowerShell para habilitar Diagnósticos de Azure (asegúrese de actualizar los valores storage\_name, key, config\_path y service\_name).

    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

Para comprobar que el servicio tiene la configuración de diagnóstico más reciente, ejecute el siguiente comando de Azure PowerShell:

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### Paso 5: Generar eventos
Para generar eventos:

1.	Para iniciar una sesión de Escritorio remoto en la instancia de servicio en la nube, en Visual Studio abra Explorador de servidores, haga clic con el botón derecho en la instancia de rol y haga clic en Conectar con Escritorio remoto.
2.	Abra un símbolo del sistema con privilegios elevados y ejecute los comandos siguientes para crear una cuenta de administrador local en la máquina virtual:


    net user contosoadmin <enterpassword> /add net localgroup administrators contosoadmin /add

3.	Abra el Visor de eventos, abra el canal **Seguridad** y observe que se creó un evento 4732, como se muestra a continuación:

![][9]

#### Paso 6: Ver datos
Espere unos 5 minutos para que el agente de Diagnósticos de Azure inserte eventos en la tabla de almacenamiento.

![][10]

Para validar el evento Creación del proceso, abra el Bloc de notas. Tal como se muestra aquí, se registró un evento Creación del proceso en el canal Seguridad.

![][11]

Ahora puede ver el mismo evento en la cuenta de almacenamiento, tal como se muestra aquí:

![][12]

### Actualice una canalización de recopilación de registros existente en un servicio en la nube con una nueva configuración
En esta sección, actualizamos una canalización de recopilación de registros de seguridad existente de Diagnósticos de Azure y detectamos eventos de cambio del firewall de Windows en una instancia de Servicio en la nube.

Para detectar cambios en el firewall, actualizaremos la configuración existente para que incluya los eventos de cambio de firewall.

#### Paso 1: Obtener la configuración existente
>[AZURE.NOTE] Las opciones de configuración nuevas sobrescribirán la configuración existente. Por lo tanto, es importante que todas las opciones de configuración de Diagnósticos de Azure existentes se combinen con el nuevo archivo de configuración.

Para recuperar las opciones de configuración existentes, puede utilizar el cmdlet **Get-AzureServiceDiagnosticsExtension**:

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### Paso 2: Actualizar el archivo XML de configuración para incluir eventos de firewall

    <?xml version="1.0" encoding="UTF-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
        <WindowsEventLog scheduledTransferPeriod="PT1M">
            <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            <DataSource name="Security!*[System[(EventID &gt;= 4944 and EventID &lt;= 4958)]]" />
        </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

Valide el contenido del archivo XML; para ello, utilice el mismo proceso de validación anteriormente descrito en el [Paso 3: Validar el archivo XML de configuración](#step3).

#### Paso 3: Actualizar Diagnósticos de Azure para utilizar la configuración nueva

Ejecute el siguiente script de Azure PowerShell para actualizar Diagnósticos de Azure para utilizar la configuración nueva (asegúrese de actualizar los valores storage\_name, key, config\_path y service\_name con la información del servicio en la nube).

    Remove-AzureServiceDiagnosticsExtension -ServiceName <ServiceName> -Role <RoleName>
    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

Para comprobar que el servicio tiene la configuración de diagnóstico más reciente, ejecute el siguiente comando de Azure PowerShell:

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### Paso 4: Habilitar eventos de firewall

1.	Abra una sesión de Escritorio remoto en la instancia de servicio en la nube.
2.	Abra un símbolo del sistema con privilegios elevados y ejecute el comando siguiente:

    ```
    auditpol.exe /set /category:"Policy Change" /subcategory:"MPSSVC rule-level Policy Change" /success:enable /failure:enable
    ```

#### Paso 5: Generar eventos

1.	Abra Firewall de Windows y haga clic en **Reglas de entrada**.
2.	Haga clic en **Agregar nueva regla** y luego en **Puerto**.
3.	En el campo **Puertos locales**, escriba **5000** y luego haga clic en **Siguiente** tres veces.
4.	En el campo **Nombre**, escriba **Test5000** y haga clic en **Finalizar**.
5.	Abra el Visor de eventos, abra el canal **Seguridad** y observe que se creó un id. de evento 4946, como se muestra a continuación:

![][13]

#### Paso 6: Ver datos
Espere unos 5 minutos para que el agente de Diagnósticos de Azure inserte los datos de eventos en la tabla de almacenamiento.

![][14]

### Distribución de eventos de seguridad y detección de alzas
Una vez que los eventos se encuentran en la cuenta de almacenamiento del cliente, las aplicaciones pueden usar las bibliotecas de clientes de almacenamiento para tener acceso a la agregación de eventos y realizarla. Si desea obtener el código de ejemplo para tener acceso a los datos de la tabla, consulte [Recuperación de los datos de la tabla](storage-dotnet-how-to-use-tables.md).

El siguiente es un ejemplo de la agregación de eventos. Toda alza en la distribución de eventos se puede investigar en más profundidad para ver si existe actividad anómala.

![][15]

### Recopilación de registros de IIS y procesamiento con HDInsight
En esta sección, recopilaremos registros de IIS desde la instancia de rol web y moveremos los registros a un blob de Azure en la cuenta de almacenamiento del cliente.

#### Paso 1: Actualizar el archivo de configuración para incluir la recopilación de registros de IIS

    <?xml version="1.0" encoding="UTF-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
        <Directories scheduledTransferPeriod="PT5M">
        <IISLogs containerName="iislogs" />
        </Directories>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
            <DataSource name="Security!*[System[(EventID=4732 or EventID=4733 or EventID=4688)]]" />
            <DataSource name="Security!*[System[(EventID &gt;= 4944 and EventID &lt;= 4958)]]" />
        </WindowsEventLog>
        </DiagnosticMonitorConfiguration>
    </WadCfg>
    </PublicConfig>

En la configuración de Diagnósticos de Azure anterior, **containerName** es el nombre del contenedor de blobs al que se moverán los registros dentro de la cuenta de almacenamiento del cliente.

Valide el archivo de configuración; para ello, utilice los mismos pasos que se mostraron anteriormente en el [Paso 3: Validar el archivo XML de configuración](#step3).


#### Paso 2: Actualizar Diagnósticos de Azure para utilizar la configuración nueva
Ejecute el siguiente script de Azure PowerShell para actualizar Diagnósticos de Azure para utilizar la configuración nueva (asegúrese de actualizar los valores storage\_name, key, config\_path y service\_name con la información del servicio en la nube).

    Remove-AzureServiceDiagnosticsExtension -ServiceName <ServiceName> -Role <RoleName>
    $storage_name = "<storage account name>"
    $key = " <storage key>"
    $config_path="<path to configuration XML file>"
    $service_name="<Cloud Service Name>"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name

Para comprobar que el servicio tiene la configuración de diagnóstico más reciente, ejecute el siguiente comando de Azure PowerShell:

    Get-AzureServiceDiagnosticsExtension -ServiceName <ServiceName>

#### Paso 3: Generar registros de IIS

1.	Abra un explorador web y navegue al rol web del servicio en la nube (por ejemplo, http://contosowebrole.cloudapp.net/).
2.	Navegue a las páginas **Acerca de** y **Contacto** para crear algunos eventos de registros.
3.	Navegue a una página que genere un código de estado 500 (por ejemplo, http://contosowebrole.cloudapp.net/Home/StatusCode500). Debería ver un error como el siguiente. Recuerde que agregamos el código para **StatusCode500** en el paso 1 de la sección titulada Configuración de una instancia nueva de una canalización de recopilación de registros en un ServiceName en la nube. ![][16]
4.	Abra una sesión de Escritorio remoto en la instancia de servicio en la nube.
5.	Abra Administrador de IIS.
6.	Registro IIS está habilitado de manera predeterminada y está establecido para generar, cada una hora, archivos que contienen todos los campos en formato W3C. Haga clic en **Examinar**; aparecerá, al menos, un archivo de registro, como se muestra aquí: ![][17]

7.	Espere unos 5 minutos para que el agente de Diagnósticos de Azure inserte el archivo de registro en el contenedor de blobs. Para validar los datos, abra **Explorador de servidores** > **Almacenamiento** > **Cuenta de almacenamiento** > **Blobs**. Tal como se muestra aquí, se crea el blob **iislogs**: ![][18]

8.	Haga clic con el botón derecho y seleccione **Ver contenedor de blobs** para mostrar el archivo de registros de IIS almacenados en el blob: ![][19]
9.	Una vez que los eventos de IIS se encuentran en la cuenta de almacenamiento del cliente, las aplicaciones que utilizan el análisis de HDInsight se pueden utilizar para realizar la agregación de eventos. El siguiente gráfico de líneas es un ejemplo de una tarea de agregación de eventos que muestra el código de estado HTTP 500: ![][20]

## Recomendaciones para la recopilación de registros de seguridad
Cuando recopile registros de seguridad, le recomendamos:

- Recopilar sus propios eventos de registro de auditoría específicos para una aplicación de servicio.
- Configurar solo los datos que necesita para realizar análisis y supervisión. Capturar demasiados datos pueden dificultar la solución de problemas y también puede afectar los costos de servicio o almacenamiento.
- Combinar las opciones de configuración de Diagnósticos de Azure existentes con los cambios que realice. El archivo de configuración nuevo sobrescribe las opciones de configuración existentes.
- Elija el intervalo **Período de transferencia programado** con cuidado. Los tiempos de transferencia más breves aumentarán la relevancia de los datos, pero eso puede aumentar los costos de almacenamiento y la carga de procesamiento.

>[AZURE.NOTE] La otra variable que afectará considerablemente la cantidad de datos recopilados es el nivel de registro. El siguiente es un ejemplo de cómo filtrar los registros por nivel de registro:

    System!*[System[(Level =2)]]

El nivel de registro es acumulativo. Si el filtro está definido en **Advertencia**, también se recopilarán los eventos **Error** y **Críticos**.

- Borre periódicamente los datos de diagnóstico de Almacenamiento de Azure si ya no son necesarios.

>[AZURE.NOTE] Si desea obtener más información sobre los datos de diagnóstico, consulte [Guardar y ver datos de diagnóstico en Almacenamiento de Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx). Los contenedores y las tablas donde se almacenan datos de diagnóstico son iguales a otros contenedores y tablas; es posible eliminar blobs y entidades de ellos del mismo modo que lo haría con otros datos. Puede eliminar los datos de diagnóstico de manera programática, a través de una de las bibliotecas de clientes de almacenamiento, o de manera visual, a través de un [cliente del explorador de almacenamiento](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx).

- Almacenar datos de servicio y datos de registro de seguridad en cuentas de almacenamiento independientes es un procedimiento recomendado. Este aislamiento asegura que guardar los datos de registro de seguridad no afecta el rendimiento del almacenamiento para los datos de servicio de producción.
- Elija la duración de la retención de registros según la directiva de cumplimiento y los requisitos de análisis y supervisión de datos de su organización.

## Exportación de registros de seguridad a otro sistema
Puede descargar datos de blobs mediante el uso de la Biblioteca de cliente de Almacenamiento de Azure y luego exportarlos al sistema local para su procesamiento. Para obtener un código de ejemplo para administrar datos de blobs, consulte [Uso del almacenamiento de blobs en .NET](storage-dotnet-how-to-use-blobs.md).

De manera similar, puede descargar los datos de seguridad almacenados en tablas de Azure con la Biblioteca de cliente de Almacenamiento de Azure. Para obtener más información sobre cómo obtener acceso a los datos almacenados en tablas, consulte [Uso del almacenamiento de tablas en .NET](storage-dotnet-how-to-use-tables.md).

## Informes de Azure Active Directory
Azure Active Directory (Azure AD) incluye un conjunto de informes de registros de seguridad, uso y auditoría que brindan una visibilidad sobre la integridad y la seguridad del inquilino de Azure AD. Por ejemplo, Azure AD tiene la capacidad de analizar automáticamente la actividad del usuario y exponer el acceso anómalo y luego ponerlos a disposición a través de informes visibles para el cliente.

Estos informes están disponibles a través del [Portal de administración de Azure](https://manage.windowsazure.com/) en **Active Directory** > **Directorio**. Algunos de estos informes son gratis y otros se ofrecen como parte de una edición de Azure AD Premium. Para obtener más información sobre los informes de Azure AD, consulte [Visualización de los informes de acceso y uso](http://msdn.microsoft.com/library/azure/dn283934.aspx).

## Registros de operaciones de Azure
Los registros de operaciones que se relacionan con los recursos de la suscripción a Azure también se encuentran disponibles mediante la característica **Registros de operaciones** en el Portal de administración.

Para ver los **Registros de operaciones**, abra el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios de administración** y luego en **Registros de operaciones**.

## <a name="diagnostics"></a> Orígenes de datos compatibles con Diagnósticos de Azure

| Origen de datos | Descripción |
|----- | ----- |
| Registros IIS | Información sobre los sitios web de IIS |
| Registros de infraestructura de diagnóstico de Azure | Información sobre Diagnósticos de Azure |
| Registros de solicitudes con error de IIS | Información sobre las solicitudes erróneas a un sitio web o aplicación de IIS |
| Registros de eventos de Windows | Información enviada al sistema de registro de eventos de Windows |
| Contadores de rendimiento | Sistema operativo y contadores de rendimiento personalizados |
| Volcados de memoria | Información sobre el estado del proceso en caso de bloqueo de una aplicación |
| Registros de errores personalizados | Registros creados por su aplicación o servicio |
| .NET EventSource | Eventos generados por su código mediante la clase EventSource de .NET |
| ETW basado en manifiesto | Eventos de Seguimiento de eventos para Windows generados por cualquier proceso |

## Recursos adicionales
Los recursos siguientes proporcionan información general sobre Microsoft Azure y servicios relacionados de Microsoft:

- [Centro de confianza de Microsoft Azure](https://azure.microsoft.com/support/trust-center/)

    Información sobre cómo la seguridad y la privacidad se incrustan en el desarrollo de Azure y sobre cómo Azure cumple con un amplio conjunto de estándares de cumplimiento internacionales y específicos del sector.

- [Página principal de Microsoft Azure](http://www.microsoft.com/windowsazure/)

    Información general y vínculos sobre Microsoft Azure

- [Centro de documentación de Microsoft Azure](http://msdn.microsoft.com/windowsazure/default.aspx)

    Guía para los scripts de automatización y servicios de Azure

- [Centro de respuestas de seguridad de Microsoft (MSRC)](http://www.microsoft.com/security/msrc/default.aspx)

    MSRC trabaja en conjunto con asociados e investigadores de seguridad de todo el mundo para ayudar a prevenir incidentes de seguridad y para aumentar la seguridad de los productos de Microsoft

- [Correo electrónico del Centro de respuestas de seguridad de Microsoft](mailto:secure@microsoft.com)

    Envíe un correo electrónico para informar sobre vulnerabilidades en la seguridad de Microsoft, incluido Microsoft Azure

<!--Image references-->
[1]: ./media/azure-security-audit-log-management/sec-security-data-collection-flow.png
[2]: ./media/azure-security-audit-log-management/sec-storage-table-security-log.PNG
[3]: ./media/azure-security-audit-log-management/sec-wad-windows-event-logs-table.png
[4]: ./media/azure-security-audit-log-management/sec-edit-entity.png
[5]: ./media/azure-security-audit-log-management/sec-app-event-log-cmd.png
[6]: ./media/azure-security-audit-log-management/sec-event-viewer.png
[7]: ./media/azure-security-audit-log-management/sec-event-id100.png
[8]: ./media/azure-security-audit-log-management/sec-diagnostics.png
[9]: ./media/azure-security-audit-log-management/sec-event4732.png
[10]: ./media/azure-security-audit-log-management/sec-step6.png
[11]: ./media/azure-security-audit-log-management/sec-process-creation-event.png
[12]: ./media/azure-security-audit-log-management/sec-process-creation-event-storage.png
[13]: ./media/azure-security-audit-log-management/sec-event4946.png
[14]: ./media/azure-security-audit-log-management/sec-event4946-storage.png
[15]: ./media/azure-security-audit-log-management/sec-event-aggregation.png
[16]: ./media/azure-security-audit-log-management/sec-status-code500.png
[17]: ./media/azure-security-audit-log-management/sec-w3c-format.png
[18]: ./media/azure-security-audit-log-management/sec-blob-iis-logs.png
[19]: ./media/azure-security-audit-log-management/sec-view-blob-container.png
[20]: ./media/azure-security-audit-log-management/sec-hdinsight-analysis.png

<!---HONumber=AcomDC_0128_2016-->