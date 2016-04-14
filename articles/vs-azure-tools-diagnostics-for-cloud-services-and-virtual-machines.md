<properties
   pageTitle="Configuración de Diagnósticos en Servicios en la nube y Máquinas virtuales de Azure | Microsoft Azure"
   description="Describe cómo configurar la información de diagnóstico para depurar los servicios en la nube de Azure y máquinas virtuales (VM) en Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="01/30/2016"
   ms.author="tarcher" />

# Configuración de Diagnósticos en Servicios en la nube y Máquinas virtuales de Azure

Cuando tenga que solucionar problemas de un servicio en la nube o una máquina virtual de Azure, puede configurar diagnósticos de Azure con mayor facilidad mediante Visual Studio. El diagnóstico de Azure captura los datos del sistema y los datos de registro en las máquinas virtuales y las instancias de máquina virtual que ejecutan el servicio en la nube y transfiere datos a la cuenta de almacenamiento que elija. Vea [Habilitación del registro de diagnóstico para aplicaciones web en el Servicio de aplicaciones de Azure](/app-service-web/web-sites-enable-diagnostic-log.md) para obtener más información sobre el registro de diagnóstico de Azure.

En este tema se muestra cómo habilitar y configurar el diagnóstico de Azure en Visual Studio, tanto antes como después de la implementación, así como en máquinas virtuales de Azure. También muestra cómo seleccionar los tipos de información de diagnóstico que sé recopilarán y cómo ver la información que se recopila.

Puede configurar Diagnósticos de Azure de las maneras siguientes:

- Puede cambiar la configuración de diagnóstico a través del cuadro de diálogo **Configuración de diagnósticos** en Visual Studio. La configuración se guarda en un archivo denominado diagnostics.wadcfgx (diagnostics.wadcfg en Azure SDK 2.4 o versiones anteriores). También puede modificar directamente el archivo de configuración. Si actualiza manualmente el archivo, los cambios de configuración surtirán efecto la próxima vez que implemente el servicio en la nube en Azure o ejecute el servicio en el emulador.

- Use **Cloud Explorer** o el **Explorador de servidores** en Visual Studio para cambiar la configuración de diagnóstico de una máquina virtual o un servicio en la nube en ejecución.

## Cambios de diagnóstico de Azure 2.6

Para los proyectos de Azure SDK 2.6 en Visual Studio, se realizaron los siguientes cambios. (Estos cambios también se aplican a las versiones posteriores del SDK de Azure.)

- El emulador local ahora es compatible con diagnósticos. Esto significa que puede recopilar datos de diagnóstico y asegurarse de que su aplicación crea los seguimientos correctos mientras desarrolla y realiza pruebas en Visual Studio. La cadena de conexión `UseDevelopmentStorage=true` habilita la colección de datos de diagnóstico mientras ejecuta el proyecto de servicio en la nube en Visual Studio mediante el emulador de almacenamiento de Azure. Todos los datos de diagnóstico se recopilan en la cuenta de almacenamiento (Almacenamiento de desarrollo).

- La cadena de conexión de la cuenta de almacenamiento de diagnóstico (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) se almacena una vez más en el archivo de configuración de servicio (.cscfg). En Azure SDK 2.5 se especificó la cuenta de almacenamiento de diagnóstico en el archivo diagnostics.wadcfgx.

Existen algunas diferencias importantes entre el funcionamiento de la cadena de conexión en Azure SDK 2.4 y Azure SDK 2.6 y versiones posteriores.

- En Azure SDK 2.4 y versiones anteriores, la cadena de conexión se usaba como tiempo de ejecución mediante el complemento de diagnóstico para obtener la información de la cuenta de almacenamiento para la transferencia de registros de diagnóstico.

- En Azure SDK 2.6 y versiones posteriores, Visual Studio usa la cadena de conexión de diagnóstico para configurar la extensión de diagnóstico con la información de la cuenta de almacenamiento adecuada durante la publicación. La cadena de conexión le permite definir las cuentas de almacenamiento diferentes para diferentes configuraciones de servicio que Visual Studio usará al publicar. Sin embargo, porque ya no está disponible el complemento de diagnóstico (después de Azure SDK 2.5), el archivo .cscfg por sí solo no puede habilitar la extensión de diagnóstico. Debe habilitar la extensión por separado a través de herramientas como Visual Studio o PowerShell.

- Para simplificar el proceso de configuración de la extensión de diagnósticos con PowerShell, la salida del paquete de Visual Studio también contiene el XML de configuración pública para la extensión de diagnósticos para cada rol. Visual Studio usa la cadena de conexión de diagnósticos para rellenar la información de la cuenta de almacenamiento presente en la configuración pública. Los archivos de configuración públicas se crean en la carpeta Extensiones y siguen el patrón PaaSDiagnostics.<RoleName>.PubConfig.xml. Cualquier implementación basada en PowerShell puede usar este patrón para asignar cada configuración a un rol.

- El portal de Azure también usa la cadena de conexión del archivo .cscfg para obtener acceso a los datos de diagnóstico, por lo que puede aparecer en la pestaña **Supervisión**. La cadena de conexión es necesaria para configurar el servicio para que muestre los datos de supervisión detallada en el portal.

## Migración de proyectos a Azure SDK 2.6 y versiones posteriores

Al migrar de Azure SDK 2.5 a Azure SDK 2.6 o versiones posteriores, si tenía una cuenta de almacenamiento de diagnósticos especificada en el archivo .wadcfgx, permanecerá allí. Para aprovechar la flexibilidad de usar diferentes cuentas de almacenamiento para distintas configuraciones de almacenamiento, tendrá que agregar manualmente la cadena de conexión a su proyecto. Si está migrando un proyecto de Azure SDK 2.4 o versiones anteriores a Azure SDK 2.6, se conservan las cadenas de conexión de diagnóstico. Sin embargo, tenga en cuenta los cambios en cómo se tratan las cadenas de conexión en Azure SDK 2.6 tal como se especifica en la sección anterior.

### Cómo determina Visual Studio la cuenta de almacenamiento de diagnósticos

- Si se especifica una cadena de conexión de diagnóstico en el archivo .cscfg, Visual Studio lo usa para configurar la extensión de diagnóstico al publicar y al generar los archivos xml de configuración pública durante el empaquetado.

- Si no se especifica ninguna cadena de conexión de diagnóstico en el archivo .cscfg, Visual Studio recurre a usar la cuenta de almacenamiento especificada en el archivo .wadcfgx para configurar la extensión de diagnóstico al publicar, y a generar los archivos xml de configuración pública durante el empaquetado.

- La cadena de conexión de diagnóstico del archivo .cscfg tiene prioridad sobre la cuenta de almacenamiento del archivo .wadcfgx. Si se especifica una cadena de conexión de diagnóstico en el archivo .cscfg, Visual Studio la usa y omite la cuenta de almacenamiento en .wadcfgx.

### ¿Qué hace la casilla "Actualizar las cadenas de conexión de almacenamiento de desarrollo..."?

La casilla **Actualizar las cadenas de conexión de almacenamiento de desarrollo para el diagnóstico y el almacenamiento en caché con las credenciales de la cuenta de almacenamiento de Microsoft Azure al publicar en Microsoft Azure** le ofrece una manera cómoda de actualizar cualquier cadena de conexión de cuenta de almacenamiento de desarrollo con la cuenta de almacenamiento de Azure especificada durante la publicación.

Por ejemplo, supongamos que activa esta casilla y la cadena de conexión de diagnóstico especifica `UseDevelopmentStorage=true`. Al publicar el proyecto en Azure, Visual Studio actualizará automáticamente la cadena de conexión de diagnóstico con la cuenta de almacenamiento que especificó en el Asistente para publicación. Sin embargo, si se especificó una cuenta de almacenamiento real como la cadena de conexión de diagnóstico, se usará dicha cuenta en su lugar.

## Diferencias de funcionalidad de diagnóstico entre Azure SDK 2.4 y versiones anteriores y Azure SDK 2.5 y versiones posteriores

Si está actualizando su proyecto de Azure SDK 2.4 a Azure SDK 2.5 o versiones posteriores, debe tener en cuenta las siguientes diferencias de funcionalidad de diagnóstico.

- **Las API de configuración están desusadas**: la configuración mediante programación del diagnóstico está disponible en Azure SDK 2.4 o versiones anteriores, pero está en desuso en Azure SDK 2.5 y versiones posteriores. Si la configuración de diagnóstico está definida actualmente en el código, deberá volver a definir esa configuración desde el principio en el proyecto migrado para que el diagnóstico siga funcionando. El archivo de configuración de diagnóstico de Azure SDK 2.4 es diagnostics.wadcfg y diagnostics.wadcfgx, para Azure SDK 2.5 y versiones posteriores.

- **El diagnóstico para aplicaciones de servicio en la nube solo se puede configurar en el nivel de rol, no en el nivel de instancia.**

- **Cada vez que implementa la aplicación, se actualiza la configuración de diagnóstico**: esto puede provocar problemas de paridad si cambia la configuración de diagnóstico del Explorador de servidores y luego vuelve a implementar la aplicación.

- **En Azure SDK 2.5 y versiones posteriores, los volcados de memoria se configuran en el archivo de configuración de diagnóstico, no en el código**: si tiene volcados de memoria configurados en el código, tendrá que transferir manualmente la configuración del código al archivo de configuración, porque los volcados de memoria no se transfieren durante la migración a Azure SDK 2.6.

## Habilitar diagnósticos en los proyectos de servicios en la nube antes de implementarlos

En Visual Studio, puede elegir recopilar datos de diagnóstico para roles que se ejecutan en Azure, al ejecutar el servicio en el emulador antes de implementarlo. Todos los cambios a la configuración de diagnóstico de Visual Studio se guardan en el archivo de configuración diagnostics.wadcfgx. Estos valores de configuración especifican la cuenta de almacenamiento donde se guardan los datos de diagnóstico cuando implementa su servicio en la nube.

### Habilitar los diagnósticos en Visual Studio antes de la implementación

1. En el menú contextual para el rol que le interesa, elija **Propiedades** y luego elija la pestaña **Configuración** en la ventana **Propiedades** del rol.

1. En la sección **Diagnóstico**, asegúrese de que la casilla **Habilitar diagnósticos** está activada.

    ![Acceso a la opción de habilitar diagnósticos](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)

1. Elija el botón de puntos suspensivos (...) para especificar la cuenta de almacenamiento en la que quiera guardar los datos de diagnóstico. La cuenta de almacenamiento que elija será la ubicación en la que se guarden los datos de diagnóstico.

    ![Especificar la cuenta de almacenamiento que se va a usar](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)

1. En el cuadro de diálogo **Crear cadena de conexión de almacenamiento**, especifique si quiere conectarse mediante el Emulador de almacenamiento de Azure, una suscripción de Azure o credenciales escritas manualmente.

    ![Cuadro de diálogo Cuenta de almacenamiento](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)

  - Si elige la opción Emulador de almacenamiento de Microsoft Azure, la cadena de conexión se establece en UseDevelopmentStorage=true.

  - Si elige la opción Su suscripción, puede elegir la suscripción de Azure que quiere usar y el nombre de cuenta. Puede elegir el botón Administrar cuentas para administrar sus suscripciones de Azure.

  - Si elige la opción de credenciales especificadas manualmente, se le pedirá que escriba el nombre y la clave de la cuenta de Azure que quiere usar.

1. Elija el botón **Configurar** para ver el cuadro de diálogo **Configuración de diagnósticos**. Cada pestaña (excepto **General** y **Directorios de registro**) representa un origen de datos de diagnóstico que puede recopilar. La pestaña predeterminada, **General**, le ofrece las siguientes opciones de colección de datos de diagnóstico: **Solo errores**, **Toda la información** y **Plan personalizado**. La opción predeterminada **Solo errores** toma la cantidad mínima de almacenamiento porque no transfiere advertencias o mensajes de seguimiento. La opción Toda la información transfiere la mayoría de la información y, por tanto, es la opción más cara en términos de almacenamiento.

    ![Habilitar configuración y diagnóstico de Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)

1. En este ejemplo, seleccione la opción **Plan personalizado** para que pueda personalizar los datos recopilados.

1. El cuadro **Cuota de disco en MB** especifica la cantidad de espacio que quiere asignar en su cuenta de almacenamiento de datos de diagnóstico. Puede cambiar el valor predeterminado si lo desea.

1. En cada pestaña de datos de diagnóstico que quiere recopilar, seleccione su casilla **Habilitar la transferencia de<log type>**. Por ejemplo, si quiere recopilar registros de aplicación, seleccione la casilla **Habilitar la transferencia de registros de aplicación** en la pestaña **Registros de aplicación**. Además, especifique cualquier otra información requerida por cada tipo de datos de diagnóstico. Vea la sección **Configurar orígenes de datos de diagnósticos** más adelante en este tema para consultar la información de configuración de cada pestaña.

1. Después de habilitar la colección de todos los datos de diagnóstico que quiera, elija el botón **Aceptar**.

1. Abra el proyecto del servicio en la nube de Azure en Visual Studio de la manera habitual. Conforme usa la aplicación, la información de registro que habilitó se guarda en la cuenta de almacenamiento de Azure que especificó.

## Habilitar diagnósticos en máquinas virtuales de Azure

En Visual Studio, puede elegir recopilar datos de diagnóstico para máquinas virtuales de Azure.

### Para habilitar diagnósticos en máquinas virtuales de Azure

1. En el **Explorador de servidores**, elija el nodo de Azure y luego conéctese a su suscripción de Azure, si aún no está conectado.

1. Expanda el nodo **Máquinas virtuales**. Puede crear una nueva máquina virtual o seleccionar uno que ya exista.

1. En el menú contextual para la máquina virtual que le interese, elija **Configurar**. Esto muestra el cuadro de diálogo de configuración de máquina virtual.

    ![Configuración de una máquina virtual de Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)

1. Si aún no está instalado, agregue la extensión Diagnósticos de Microsoft Monitoring Agent. Esta extensión le permite recopilar datos de diagnóstico para la máquina virtual de Azure. En la lista Extensiones instaladas, elija el menú desplegable Seleccionar una extensión disponible y luego elija Diagnósticos de Microsoft Monitoring Agent.

    ![Instalación de una extensión de máquina virtual de Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)

    >[AZURE.NOTE] Hay otras extensiones de diagnóstico disponibles para las máquinas virtuales. Para obtener más información, vea Características y extensiones de máquina virtual de Azure.

1. Elija el botón **Agregar** para agregar la extensión y ver el cuadro de diálogo **Configuración de diagnóstico**.

1. Elija el botón **Configurar** para especificar una cuenta de almacenamiento y luego elija el botón **Aceptar**.

    Cada pestaña (excepto para **General** y **Directorios de registro**) representa un origen de datos de diagnóstico que puede recopilar.

    ![Habilitar configuración y diagnóstico de Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)

    La pestaña predeterminada, **General**, le ofrece las siguientes opciones de colección de datos de diagnóstico: **Solo errores**, **Toda la información** y **Plan personalizado**. La opción predeterminada **Solo errores** toma la cantidad mínima de almacenamiento porque no transfiere advertencias o mensajes de seguimiento. La opción **Toda la información** transfiere la mayoría de la información y, por tanto, es la opción más cara en términos de almacenamiento.

1. En este ejemplo, seleccione la opción **Plan personalizado** para que pueda personalizar los datos recopilados.

1. El cuadro **Cuota de disco en MB** especifica la cantidad de espacio que quiere asignar en su cuenta de almacenamiento de datos de diagnóstico. Puede cambiar el valor predeterminado si lo desea.

1. En cada pestaña de datos de diagnóstico que quiera recopilar, seleccione su casilla **Habilitar la transferencia de <log type>**.

    Por ejemplo, si quiere recopilar registros de aplicación, seleccione la casilla **Habilitar la transferencia de registros de aplicación** en la pestaña **Registros de aplicación**. Además, especifique cualquier otra información requerida por cada tipo de datos de diagnóstico. Vea la sección **Configurar orígenes de datos de diagnósticos** más adelante en este tema para consultar la información de configuración de cada pestaña.

1. Después de habilitar la colección de todos los datos de diagnóstico que quiera, elija el botón **Aceptar**.

1. Guarde el proyecto actualizado.

    Verá un mensaje en la ventana **Registro de actividad de Microsoft Azure** de que la máquina virtual se ha actualizado.

## Configurar orígenes de datos de diagnósticos

Cuando habilite la colección de datos de diagnóstico, puede elegir exactamente qué orígenes de datos quiere recopilar y qué información se recopila. La siguiente es una lista de las pestañas del cuadro de diálogo **Configuración de diagnósticos** y de qué significa cada opción de configuración.

### Registros de aplicación

Los **Registros de aplicación** contienen información de diagnóstico generada por una aplicación web. Si quiere capturar registros de aplicación, active la casilla **Habilitar la transferencia de registros de aplicación**. Puede aumentar o disminuir el número de minutos durante los cuales se transfieren los registros de aplicación a la cuenta de almacenamiento cambiando el valor **Período de transferencia (min)**. También puede cambiar la cantidad de información que se captura en el registro estableciendo el valor del nivel de registro. Por ejemplo, puede elegir **Detallado** para obtener más información o **Crítico** para capturar solo los errores críticos. Si tiene un proveedor de diagnósticos específico que emite registros de aplicación, puede capturarlos agregando el GUID del proveedor para el cuadro **GUID de proveedor**.

  ![Registros de aplicación](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

  Vea [Habilitación del registro de diagnóstico para aplicaciones web en el Servicio de aplicaciones de Azure](web-sites-enable-diagnostic-log.md) para obtener más información sobre los registros de aplicación.

### Registros de eventos de Windows

Si quiere capturar registros de aplicación de Windows, active la casilla **Habilitar la transferencia de registros de eventos de Windows**. Puede aumentar o disminuir el número de minutos durante los cuales se transfieren los registros de eventos a la cuenta de almacenamiento cambiando el valor **Período de transferencia (min)**. Active las casillas para los tipos de eventos de los que quiera realizar un seguimiento.

  ![Registros de eventos](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

Si usa Azure SDK 2.6 o versiones posteriores y quiere especificar un origen de datos personalizado, escríbalo de nuevo en el cuadro de texto **<Data source name>** y luego elija el botón **Agregar** que se encuentra junto a él. El origen de datos se agrega al archivo diagnostics.cfcfg.

Si usa Azure SDK 2.5 y quiere especificar un origen de datos personalizado, puede agregarlo a la sección `WindowsEventLog` del archivo diagnostics.wadcfgx , como en el ejemplo siguiente.

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### Contadores de rendimiento

La información del contador de rendimiento puede ayudarle a buscar cuellos de botella del sistema y a optimizar el rendimiento del sistema y de la aplicación. Vea [Crear y usar contadores de rendimiento en una aplicación de Azure](https://msdn.microsoft.com/library/azure/hh411542.aspx) para obtener más información. Si quiere capturar los contadores de rendimiento, active la casilla **Habilitar la transferencia de contadores de rendimiento**. Puede aumentar o disminuir el número de minutos durante los cuales se transfieren los registros de eventos a la cuenta de almacenamiento cambiando el valor **Período de transferencia (min)**. Active las casillas para los contadores de rendimiento de los que quiera realizar un seguimiento.

  ![Contadores de rendimiento](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

Para realizar el seguimiento de un contador de rendimiento que no aparece en la lista, especifíquelo mediante la sintaxis sugerida y luego elija el botón **Agregar**. El sistema operativo de la máquina virtual determina a qué contadores de rendimiento puede realizar el seguimiento. Para obtener más información sobre la sintaxis, vea [Especificación de una ruta de contador](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx).

### Registros de infraestructura

Si quiere capturar registros de infraestructura, que contienen información sobre la infraestructura del diagnóstico de Azure, el módulo RemoteAccess y el módulo RemoteForwarder, active la casilla **Habilitar la transferencia de registros de infraestructura**. Puede aumentar o disminuir el número de minutos durante los cuales se transfieren los registros a su cuenta de almacenamiento cambiando el valor Período de transferencia (min).

  ![Registros de infraestructura de diagnóstico](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

  Consulte [Recopilar datos de registro mediante Diagnósticos de Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx) para obtener más información.

### Directorios de registro

Si quiere capturar los directorios de registro, que contienen los datos recopilados de los directorios de registro para las solicitudes de Internet Information Services (IIS), las solicitudes con error o las carpetas que elija, active la casilla **Habilitar la transferencia de directorios de registro**. Puede aumentar o disminuir el número de minutos durante los cuales se transfieren los registros a su cuenta de almacenamiento cambiando el valor **Período de transferencia (min)**.

Puede activar las casillas de los registros que quiera recopilar, como **Registros de IIS** y los registros de **Solicitud con error**. Se proporcionan nombres de contenedor de almacenamiento predeterminados, pero puede cambiar los nombres si lo desea.

Además, puede capturar registros desde cualquier carpeta. Solo tiene que especifique la ruta de acceso en la sección **Registro del directorio absoluto** y elegir el botón **Agregar directorio**. Los registros se capturarán en los contenedores especificados.

  ![Directorios de registro](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### Registros de ETW

Si usa [Seguimiento de eventos para Windows] (https://msdn.microsoft.com/library/windows/desktop/bb968803(v=vs.85).aspx) (ETW) y quiere capturar registros de ETW, active la casilla **Habilitar la transferencia de registros de ETW**. Puede aumentar o disminuir el número de minutos durante los cuales se transfieren los registros a su cuenta de almacenamiento cambiando el valor **Período de transferencia (min)**.

Los eventos se capturan de los orígenes de eventos y manifiestos de eventos que especifique. Para especificar un origen de eventos, escriba un nombre en la sección **Orígenes de eventos** y luego elija el botón **Agregar origen de evento**. De forma similar, puede especificar un manifiesto de evento en la sección **Manifiestos de eventos** y luego elegir el botón **Agregar manifiesto de evento**.

  ![Registros de ETW](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

  El marco de trabajo de ETW se admite en ASP.NET a través de clases en el espacio de nombres [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110). El espacio de nombres de Microsoft.WindowsAzure.Diagnostics, que se hereda de las clases [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110) estándar y las extiende , habilita el uso de [System.Diagnostics.aspx] (https://msdn.microsoft.com/library/system.diagnostics(v=vs.110) como un marco de registro en el entorno de Azure. Para obtener más información, vea [Tomar el control del registro y el seguimiento en Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) y [Habilitación de diagnósticos en Servicios en la nube y Máquinas virtuales de Azure](/cloud-services/cloud-services-dotnet-diagnostics.md).

### Volcados de memoria

Si quiere capturar información sobre cuándo se bloquea una instancia de rol, active la casilla **Habilitar la transferencia de volcados de memoria**. (Puesto que ASP.NET controla la mayoría de las excepciones, normalmente solo es útil para roles de trabajo.) Puede aumentar o disminuir el porcentaje del espacio de almacenamiento dedicado a los volcados de memoria cambiando el valor **Cuota de directorio (%)**. Puede cambiar el contenedor de almacenamiento donde se almacenan los volcados de memoria y puede seleccionar si quiere capturar un volcado **Completo** o **Mini**.

Se muestran los procesos de los que se está realizando un seguimiento actualmente. Active las casillas para los procesos que quiera capturar. Para agregar otro proceso a la lista, escriba el nombre del proceso y luego elija el botón **Agregar proceso**.

  ![Volcados de memoria](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

  Vea [Tomar el control del registro y el seguimiento en Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) y [Diagnósticos de Microsoft Azure Parte 4: Componentes de registro personalizados y cambios de Diagnósticos de Azure 1.3](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/) para obtener más información.

## Ver los datos de diagnóstico

Cuando haya recopilado los datos de diagnóstico para un servicio en la nube o una máquina virtual, podrá verlos.

### Para ver los datos de diagnóstico del servicio en la nube

1. Implemente su servicio en nube como de costumbre y luego ejecútelo.

1. Puede ver los datos de diagnóstico en un informe que Visual Studio genera o en tablas de su cuenta de almacenamiento. Para ver los datos en un informe, abra **Cloud Explorer** o el **Explorador de servidores**, abra el menú contextual del nodo para el rol que le interesa y luego elija **Ver datos de diagnóstico**.

    ![Ver los datos de diagnóstico](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)

    Aparece un informe que muestra los datos disponibles.

    ![Informe de diagnósticos de Microsoft Azure en Visual Studio](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)

    Si no aparecen los datos más recientes, puede que tenga que esperar a que transcurra el período de transferencia.

    Elija el vínculo **Actualizar** para actualizar los datos inmediatamente o elija un intervalo en el cuadro de lista desplegable **Actualización automática** para que los datos se actualizan automáticamente. Para exportar los datos de error, elija el botón **Exportar a CSV** para crear un archivo de valores separados por comas que se puede abrir en una hoja de cálculo.

    En **Cloud Explorer** o el **Explorador de servidores**, abra la cuenta de almacenamiento asociada a la implementación.

1. Abra las tablas de diagnósticos en el visor de tablas y luego revise los datos que ha recopilado. Para los registros de IIS y los registros personalizados, puede abrir un contenedor de blobs. Si revisa la siguiente tabla, encontrará el contenedor de blobs o tablas con los datos que le interesan. Además de los datos para ese archivo de registro, las entradas de tabla contienen EventTickCount, DeploymentId, Role y RoleInstance para ayudarle a identificar qué máquina virtual y rol generó los datos y cuándo.

    |Datos de diagnóstico|Descripción|La ubicación|
    |---|---|---|
    |Registros de aplicación|Registros que su código genera llamando a métodos de la clase System.Diagnostics.Trace.|WADLogsTable|
    |Registros de eventos|Estos datos proceden de los registros de eventos de Windows en las máquinas virtuales. Windows almacena información en estos registros, pero las aplicaciones y los servicios también los usan para informar de errores o información de registro.|WADWindowsEventLogsTable|
    |Contadores de rendimiento|Puede recopilar datos sobre cualquier contador de rendimiento que esté disponible en la máquina virtual. El sistema operativo ofrece contadores de rendimiento, que incluyen muchas estadísticas como el tiempo del procesador y el uso de la memoria.|WADPerformanceCountersTable|
    |Registros de infraestructura|Estos registros se generan desde la propia infraestructura de diagnóstico.|WADDiagnosticInfrastructureLogsTable|
    |Registros IIS|Estos registros registran solicitudes web. Si su servicio en la nube obtiene una cantidad importante de tráfico, estos registros pueden ser bastante largos, por lo que debe recopilar y almacenar estos datos solo cuando los necesite.|Puede encontrar registros de solicitudes con error en el contenedor de blobs debajo de wad-iis-failedreqlogs en una ruta de acceso para esa implementación, rol e instancia. Puede encontrar registros completos en wad-iis-logfiles. Se crean entradas para cada archivo en la tabla WADDirectories.|
    |Volcados de memoria|Esta información ofrece imágenes binarias del proceso del servicio en la nube (normalmente un rol de trabajo).|contenedor de blobs de wad-crush-dumps|
    |Archivos de registro personalizados|Registros de datos que ha predefinido.|Puede especificar en código la ubicación de archivos de registro personalizados en su cuenta de almacenamiento. Por ejemplo, puede especificar un contenedor de blobs personalizado.|

1. Si se truncan los datos de cualquier tipo, puede intentar aumentar el búfer para este tipo de datos o acortar el intervalo entre las transferencias de datos desde la máquina virtual a su cuenta de almacenamiento.

1. (opcional) Purgar los datos de la cuenta de almacenamiento ocasionalmente para reducir los costos de almacenamiento generales.

1. Al realizar una implementación completa, se actualizará el archivo diagnostics.cscfg (.wadcfgx para Azure SDK 2.5) en Azure y su servicio en la nube recogerá cualquier cambio en la configuración de diagnóstico. Si, en su lugar, actualiza una implementación existente, no se actualizará el archivo .cscfg en Azure. No obstante, todavía puede cambiar la configuración de diagnóstico, siguiendo los pasos descritos en la siguiente sección. Para obtener más información sobre cómo realizar una implementación completa y actualizar una implementación existente, vea [Asistente Publicar aplicaciones de Azure](vs-azure-tools-publish-azure-application-wizard.md).

### Para ver los datos de diagnóstico de máquina virtual

1. En el menú contextual para la máquina virtual, elija **Ver datos de diagnóstico**.

    ![Ver datos de diagnóstico en la máquina virtual de Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)

    Se abrirá la ventana **Resumen de diagnóstico**.

    ![Resumen de diagnóstico de máquina virtual de Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)

    Si no aparecen los datos más recientes, puede que tenga que esperar a que transcurra el período de transferencia.

    Elija el vínculo **Actualizar** para actualizar los datos inmediatamente o elija un intervalo en el cuadro de lista desplegable **Actualización automática** para que los datos se actualizan automáticamente. Para exportar los datos de error, elija el botón **Exportar a CSV** para crear un archivo de valores separados por comas que se puede abrir en una hoja de cálculo.

## Configurar el diagnóstico de servicio en la nube después de la implementación

Si está investigando un problema con un servicio en la nube que ya se está ejecutando, es posible que quiera recopilar datos que no ha especificado antes de implementar originalmente el rol. En este caso, puede comenzar a recopilar esos datos usando la configuración en el Explorador de servidores. Puede configurar diagnóstico para una sola instancia o para todas las instancias de un rol, en función de si abre el cuadro de diálogo Configuración de diagnóstico en el menú contextual para la instancia o el rol. Si configura el nodo de role, los cambios se aplicarán a todas las instancias. Si configura el nodo de rol, los cambios solo se aplicarán a esa instancia.

### Para configurar diagnósticos para un servicio en la nube en ejecución

1. En el Explorador de servidores, expanda el nodo **Servicios en la nube** y luego expanda los nodos para busca el rol o instancia que quiere investigar o ambos.

    ![Configuración de diagnósticos](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)

1. En el menú contextual para un nodo de instancia o un nodo de rol, elija **Actualizar valores de diagnóstico** y luego elija la configuración de diagnóstico que quiere recopilar.

    Para obtener información acerca de las opciones de configuración, consulte **Configurar orígenes de datos de diagnósticos** en este tema. Para obtener información sobre cómo ver los datos de diagnósticos, vea **Ver los datos de diagnóstico** en este tema.

    Si cambia la colección de datos en el **Explorador de servidores**, estos cambios permanecerán en vigor hasta que vuelva a implementar totalmente su servicio en la nube. Si usa la configuración de publicación predeterminada, no se sobrescriben los cambios, ya que la configuración de publicación predeterminada es actualizar la implementación existente, en lugar de hacer una nueva implementación completa. Para asegurarse de que la configuración está borrada durante la implementación, vaya a la casilla **Configuración avanzada** del Asistente para publicación y desactive la casilla **Actualización de implementación**. Cuando vuelva a implementar con esa casilla desactivada, la configuración se revertirá a la del archivo .wadcfgx (o .wadcfg) según se establece a través del editor de Propiedades para el rol. Si actualiza su implementación, Azure conserva la configuración anterior.

## Solucionar problemas del servicio en la nube de Azure

Si experimenta problemas con sus proyectos de servicio en la nube, como un rol que se atasca en un estado "ocupado", recicla de manera repetida o genera un error de servidor interno, existen herramientas y técnicas que puede usar para diagnosticar y corregir estos problemas. Para ver ejemplos específicos de problemas comunes y soluciones, así como para obtener información general sobre los conceptos y herramientas que se usan para diagnosticar y corregir estos errores, vea [Datos de diagnóstico de proceso de PaaS de Azure](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

## Preguntas y respuestas

**¿Cuál es el tamaño del búfer y cuál debería ser?**

En cada instancia de máquina virtual, las cuotas limitan cuántos datos de diagnósticos se pueden almacenar en el sistema de archivos local. Además, especifique un tamaño de búfer para cada tipo de datos de diagnóstico que está disponible. Este tamaño de búfer actúa como una cuota individual para ese tipo de datos. Compruebe la parte inferior del cuadro de diálogo para determinar la cuota global y la cantidad de memoria que permanece. Si especifica búferes mayores o más tipos de datos, alcanzará la cuota global. Puede cambiar la cuota global modificando el archivo de configuración diagnostics.wadcfg/.wadcfgx. Los datos de diagnóstico se almacenan en el mismo sistema de archivos que los datos de la aplicación, por lo que si la aplicación usa una gran cantidad de espacio en disco, no debería aumentar la cuota de diagnóstico global.

**¿Cuál es el período de transferencia y cuál debería ser?**

El período de transferencia es el tiempo que transcurre entre las capturas de datos. Tras cada período de transferencia, los datos se mueven desde el sistema de archivos local de una máquina virtual a las tablas de su cuenta de almacenamiento. Si la cantidad de datos que se recopilan supera la cuota antes del final de un período de transferencia, se descartarán los datos más antiguos. Puede que quiera reducir el período de transferencia si pierde datos porque los datos superan el tamaño del búfer o la cuota global.

**¿En qué zona horaria están las marcas de tiempo?**

Las marcas de tiempo se encuentran en la zona horaria local del centro de datos que hospeda el servicio en la nube. Se usan las siguientes tres columnas de marca de tiempo en las tablas de registro.

  - **PreciseTimeStamp** es la marca de tiempo de ETW del evento. Es decir, el tiempo en que se registra el evento del cliente.

  - **TIMESTAMP** es PreciseTimeStamp redondeado hacia abajo hasta el límite de la frecuencia de carga. Por tanto, si su frecuencia de carga es de 5 minutos y la hora del evento es 00:17:12, TIMESTAMP será 00:15:00.

  - **Timestamp** es la marca de tiempo en que se creó la entidad en la tabla de Azure.

**¿Cómo administrar los costos al recopilar información de diagnóstico?**

La configuración predeterminada (**Nivel de registro** establecido en **Error** y **Período de transferencia** establecido en **1 minuto**) está diseñada para minimizar los costos. Los costos de proceso aumentarán si recopila más datos de diagnóstico o disminuye el período de transferencia. No recopile más datos de los que necesite y no olvide deshabilitar la colección de datos cuando ya no la necesite. Siempre puede habilitarla de nuevo, incluso en tiempo de ejecución, como se muestra en la sección anterior.

**¿Cómo recopilo registros de solicitudes con error de IIS?**

De forma predeterminada, IIS no recopila registros de solicitud con error. Puede configurar IIS para recopilarlos si edita el archivo web.config para su rol web.

**No estoy obteniendo información de seguimiento desde métodos RoleEntryPoint como OnStart. ¿Cuál es el problema?**

A los métodos de RoleEntryPoint se les llama en el contexto de WAIISHost.exe, no IIS. Por tanto, no se aplica la información de configuración de archivo web.config que normalmente habilita el seguimiento. Para resolver este problema, agregue un archivo .config a su proyecto de rol web y asigne un nombre al archivo que coincida con el ensamblado de salida que contiene el código RoleEntryPoint. En el proyecto de rol web predeterminado, el nombre del archivo .config sería WAIISHost.exe.config. Luego agregue las líneas siguientes a este archivo:

```
<system.diagnostics>
  <trace>
      <listeners>
          <add name “AzureDiagnostics” type=”Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener”>
              <filter type=”” />
          </add>
      </listeners>
  </trace>
</system.diagnostics>
```

En la ventana **Propiedades**, establezca la propiedad **Copiar en el directorio de salida** en **Copiar siempre**.

## Pasos siguientes

Para obtener más información sobre el registro de diagnósticos de Azure, consulte [Habilitación de diagnósticos en Servicios en la nube y Máquinas virtuales de Azure](cloud-services-dotnet-diagnostics.md) y [Habilitación del registro de diagnóstico para aplicaciones web en el Servicio de aplicaciones de Azure](web-sites-enable-diagnostic-log.md).

<!---HONumber=AcomDC_0204_2016-->