<properties
   pageTitle="Trabajos híbridos de runbook de Automatización de Azure"
   description="Este artículo brinda información sobre cómo instalar y usar Trabajo híbrido de runbook, una característica de Automatización de Azure que le permite ejecutar runbooks en máquinas de su centro de datos local."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/25/2016"
   ms.author="bwren" />

# Trabajos híbridos de runbook de Automatización de Azure

Los runbooks de Automatización de Azure no pueden tener acceso a los recursos de su centro de datos local, debido a que se ejecutan en la nube de Azure. La característica Trabajo híbrido de runbook de Automatización de Azure permite ejecutar runbooks en máquinas ubicadas en su centro datos, para así poder administrar los recursos locales. Los runbooks se almacenan y administran en Automatización de Azure y después se entregan a una o más máquinas locales.

Esta funcionalidad se ilustra en la imagen siguiente.

![Información general de Trabajo híbrido de runbook](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-overview.png)

Puede designar uno o más equipos del centro de datos para que actúen como un trabajo híbrido de runbook y ejecuten runbooks desde Automatización de Azure. Cada trabajo requiere el agente de administración de Microsoft con una conexión a Microsoft Operations Management Suite y al entorno de runbooks de Automatización de Azure. Operations Management Suite solo se usa para instalar y mantener el agente de administración y para supervisar la funcionalidad del trabajo. Automatización de Azure realiza la entrega de los runbooks y la instrucción para ejecutarlos.

![Componentes de Trabajo híbrido de runbook](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-components.png)

>[AZURE.NOTE] Visión operativa se está integrando en Operations Management Suite y es posible que vea cualquiera de estos nombres usado en el portal y en la documentación.

No hay ningún requisito de firewall de entrada para admitir Trabajos híbridos de runbook. El agente que se encuentra en el equipo local inicia toda la comunicación con Automatización de Azure en la nube. Cuando se inicia un runbook, Automatización de Azure crea una instrucción que el agente recupera. El agente baja el runbook y todos los parámetros antes de ejecutarlo. También recuperará todos los [recursos](http://msdn.microsoft.com/library/dn939988.aspx) que usa el runbook desde Automatización de Azure.

>[AZURE.NOTE] Los trabajo híbridos de runbooks no admiten actualmente [configuraciones de DSC](automation-dsc-overview.md).

## Grupos de Trabajos híbridos de runbook

Cada Trabajo híbrido de runbook es miembro de un grupo de Trabajos híbridos de runbook que especifica cuando instala el agente. Un grupo puede incluir solo un agente, pero puede instalar varios agentes en un grupo para contar con alta disponibilidad.

Cuando se inicia un runbook en un Trabajo híbrido de runbook, se especifica el grupo en el que se ejecutará. Los miembros del grupo determinarán qué trabajo no atiende a la solicitud. No es posible especificar un trabajo determinado.

## Requisitos de Hybrid Runbook Worker

Debe designar al menos un equipo local para ejecutar trabajos de runbook híbrido. Este equipo debe tener lo siguiente:

- Windows Server 2012 o superior
- Windows PowerShell 4.0 o posterior.

Tenga en cuenta las siguientes recomendaciones para los Hybrid Worker:

- Designe varios Hybrid Worker en cada grupo para una alta disponibilidad.  
- Los Hybrid Worker pueden coexistir con servidores de runbooks de Service Management Automation o System Center Orchestrator.
- Considere la posibilidad de usar una máquina ubicada físicamente en la región de su cuenta de automatización, o cerca de ella, puesto que los datos del trabajo se envían a la Automatización de Azure cuando se completa un trabajo.

Requisitos del firewall:

- La máquina local en la que se ejecuta el trabajo de Hybrid Runbook Worker debe tener acceso de salida a *.cloudapp.net en los puertos 443, 9354 y 30000-30199.

## Instalación de Trabajo híbrido de runbook
El siguiente procedimiento describe cómo instalar y configurar Hybrid Runbook Worker. Realice los dos primeros pasos una vez para su entorno de Automatización y después repita los pasos restantes en cada equipo de trabajo.

### 1\. Creación del área de trabajo de Operations Management Suite
Si todavía no tiene un área de trabajo de Operations Management Suite, puede crear una siguiendo las instrucciones que aparecen en [Configuración del área de trabajo de Visión operativa](../operational-insights/operational-insights-onboard-in-minutes.md). Si cuenta con un área de trabajo existente, puede usarla.

### 2\. Adición de soluciones de Automatización de área de trabajo de Operations Management Suite
Las soluciones agregan funcionalidad a Operations Management Suite. La solución de Automatización agrega funcionalidad a Automatización de Azure, incluida la compatibilidad con Hybrid Runbook Worker. Cuando se agrega la solución al área de trabajo, se insertarán automáticamente los componentes de trabajo al equipo del agente que va a instalar en el paso siguiente.

Siga las instrucciones de [Para agregar una solución mediante la Galería de soluciones](../operational-insights/operational-insights-setup-workspace.md#1-add-solutions) para agregar la solución de **Automatización** al área de trabajo de Operations Management Suite.

### 3\. Instalación del agente de Microsoft Management
El agente de Microsoft Management conecta los equipos a Operations Management Suite. Cuando instala el agente en la máquina local y se conecte al área de trabajo, se descargarán automáticamente los componentes necesarios para Hybrid Runbook Worker.

Siga las instrucciones que se encuentran en [Conexión de equipos directamente en Visión operativa](../operational-insights/operational-insights-direct-agent.md) para instalar el agente en la máquina local. Puede repetir este proceso para varios equipos para agregar varios trabajos a su entorno.

Cuando el agente se conecta correctamente a Operations Management Suite, se enumerará en la pestaña **Orígenes conectados** del panel **Configuración** de Operations Management Suite. Puede comprobar que el agente ha descargado correctamente la solución Automatización cuando tiene una carpeta llamada **AzureAutomationFiles** en C:\\Program Files\\Microsoft Monitoring Agent\\Agent.

### 4\. Instalación del entorno de runbook y conexión con Automatización de Azure
Cuando agregue un agente a Operations Management Suite, la solución Automatización inserta el módulo **HybridRegistration** de PowerShell que contiene el cmdlet **Add-HybridRunbookWorker**. Este cmdlet se usa para instalar el entorno de runbook en el equipo y registrarlo con Automatización de Azure.

Abra una sesión de PowerShell en modo de Administrador y ejecute el comando siguientes para importar el módulo.

	cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation<version>\HybridRegistration"
	Import-Module HybridRegistration.psd1


A continuación, ejecute el cmdlet **Add-HybridRunbookWorker** con la siguiente sintaxis:

	Add-HybridRunbookWorker –Name <String> -EndPoint <Url> -Token <String>

Puede obtener la información necesaria para este cmdlet desde la hoja **Administrar claves** del Portal de Azure. Puede abrir esta hoja con un clic en el icono de clave en el panel Elementos de la cuenta de Automatización.

![Información general de Trabajo híbrido de runbook](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

- **Name** es el nombre del grupo de Trabajos híbridos de runbook. Si este grupo ya existe en la cuenta de automatización, se le agregará el equipo actual. Si todavía no existe, se agregará.
- **Extremo** es el campo **URL** campo de la hoja **Administrar claves**.
- **Token** es la **Clave de acceso primaria** en la hoja **Administrar claves**.  

Use el modificador **-Verbose** con **Add-HybridRunbookWorker** para recibir información detallada sobre la instalación.

### 5\. Instalación de módulos PowerShell
Los runbooks pueden usar cualquiera de las actividades y los cmdlets definidos en los módulos instalados en el entorno de Automatización de Azure. Sin embargo, estos módulos no se implementan automáticamente en las máquinas locales, por lo que debe instalarlos de forma manual. La excepción es el módulo de Azure que se instala de manera predeterminada y brinda acceso a los cmdlets para todos los servicios y actividades de Azure para Automatización de Azure.

Debido a que el propósito principal de la característica Trabajo híbrido de runbook es administrar recursos locales, es muy probable que deba instalar los módulos que admiten estos recursos. Puede consultar [Instalación de módulos](http://msdn.microsoft.com/library/dd878350.aspx) para obtener información sobre cómo instalar módulos de Windows PowerShell.

## Eliminación de Hybrid Runbook Worker

Puede quitar Hybrid Runbook Worker de un equipo local con el cmdlet **Remove-HybridRunbookWorker** en esta máquina. Use el modificador **-Verbose** para ver un registro detallado del proceso de eliminación.

## Inicio de runbooks en Trabajo híbrido de runbook

[Inicio de un runbook en Automatización de Azure](automation-starting-a-runbook.md) describe los distintos métodos para iniciar un runbook. Trabajo híbrido de runbook agrega una opción **Ejecutar en** donde puede especificar el nombre de un grupo de Trabajos híbridos de runbook. Si se especifica un grupo, los trabajos de ese grupo recuperan y ejecutan el runbook. Si no se especifica esta opción, se ejecuta en Automatización de Azure de la manera normal.

Cuando inicia un runbook en el Portal de Azure, verá una opción **Ejecutar en** donde puede seleccionar **Azure** o **Hybrid Worker**. Si selecciona **Trabajo híbrido**, puede seleccionar el grupo en una lista desplegable.

Use el parámetro **RunOn**. Podría usar el comando siguiente para iniciar un runbook llamado Test-Runbook en un grupo de Trabajos híbridos de runbook denominado MyHybridGroup con Windows PowerShell.

	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

>[AZURE.NOTE] El parámetro **RunOn** se agregó al cmdlet **Start-AzureAutomationRunbook** en la versión 0.9.1 de PowerShell de Microsoft Azure. Si tiene instalada una versión anterior, deberá [descargar la versión más reciente](https://azure.microsoft.com/downloads/). Solo necesita instalar esta versión en una estación de trabajo donde iniciará el runbook desde Windows PowerShell. No es necesario que la instale en el equipo de trabajo, a menos que tenga la intención de iniciar runbooks desde ese equipo. Actualmente no puede iniciar un runbook en un Trabajo híbrido de runbook desde otro runbook, debido a que esto requeriría que la versión más reciente de PowerShell de Azure esté instalada en su cuenta de Automatización. La versión más reciente se actualizará automáticamente en Automatización de Azure y pronto se insertará de manera automática en los trabajos.

## Permisos de runbooks

Los runbooks que se ejecutan en un Hybrid Runbook Worker no pueden usar el mismo [método que se usa normalmente para los runbooks que realizan autenticación a los recursos de Azure](automation-configuring.md#configuring-authentication-to-azure-resources), debido a que tendrán acceso a recursos fuera de Azure. El runbook puede proporcionar su propia autenticación a los recursos locales, o puede especificar una cuenta RunAs para proporcionar un contexto de usuario para todos los runbooks.

### Autenticación de runbook

De forma predeterminada, los runbooks se ejecutarán en el contexto de la cuenta de sistema local en el equipo local, por lo que deben proporcionar su propia autenticación a los recursos a los que tendrán acceso.

Puede usar los recursos [Credencial](http://msdn.microsoft.com/library/dn940015.aspx) y [Certificado](http://msdn.microsoft.com/library/dn940013.aspx) en el runbook con cmdlets que le permitan especificar las credenciales, para que así pueda realizar la autenticación a distintos recursos. El ejemplo siguiente muestra una porción de un runbook que reinicia un equipo. Recupera las credenciales desde un recurso de Credencial y el nombre del equipo desde un recurso de Variable y, a continuación, usa estos valores con el cmdlet Restart-Computer.

	$Cred = Get-AutomationCredential "MyCredential"
	$Computer = Get-AutomationVariable "ComputerName"

	Restart-Computer -ComputerName $Computer  -Credential $Cred

También puede aprovechar [InlineScript](automation-powershell-workflow.md#inline-script), que le permitirá ejecutar bloques de código en otro equipo con credenciales especificadas por el [parámetro común PSCredential](http://technet.microsoft.com/library/jj129719.aspx).

### Cuenta RunAs

En lugar de tener runbooks que proporcionen su propia autenticación a los recursos locales, puede especificar una cuenta **Ejecutar como** para un grupo de Hybrid Worker. Especifique un [activo de credencial](automation-credentials.md) que tenga acceso a los recursos locales, y todos los runbooks se ejecutarán con estas credenciales cuando se ejecutan en un Hybrid Runbook Worker en el grupo.

El nombre de usuario de la credencial debe tener uno de los siguientes formatos:

- dominio\\nombre de usuario 
- username@domain
- nombre de usuario (para cuentas locales en el equipo local)


Utilice el procedimiento siguiente para especificar una cuenta RunAs de un grupo de Hybrid Worker:

1. Crear un [activo de credencial](automation-credentials.md) con acceso a los recursos locales.
2. Abra la cuenta de Automatización en el Portal de Azure.
2. Seleccione el icono **Grupos de Hybrid Worker** y luego seleccione el grupo.
3. Seleccione **Toda la configuración** y luego **Configuración del grupo de Hybrid Worker**.
4. Cambie **Ejecutar como** de **Predeterminado** a **Personalizado**.
5. Seleccione la credencial y haga clic en **Guardar**.


## Creación de runbooks para Trabajo híbrido de runbook

No hay ninguna diferencia en la estructura de runbooks que se ejecutan en Automatización de Azure y los que se ejecutan en un Trabajo híbrido de runbook. Los runbooks que usa con cada uno de ellos probablemente sean muy distintos entre sí, debido a que los runbooks para Trabajo híbrido de runbook normalmente administrarán los recursos locales de su centro de datos, mientras que los runbooks en Automatización de Azure normalmente administrarán recursos en la nube de Azure.

Puede editar un runbook para Trabajo híbrido de runbook en Automatización de Azure, pero es probable que tenga dificultades si intenta probar el runbook en el editor. Es posible que los módulos de PowerShell que tienen acceso a los recursos locales no estén instalados en el entorno de Automatización de Azure; si este es el caso, la prueba presentará errores. Si instala los módulos necesarios, se ejecutará el runbook, pero no podrá tener acceso a los recursos locales para una prueba completa.

## Solución de problemas de runbooks en Hybrid Runbook Worker

[La salida y los mensajes de runbooks](automation-runbook-output-and-messages.md) se envían a Automatización de Azure desde los trabajos híbridos igual que los trabajos de runbook que se ejecutan en la nube. También puede habilitar los flujos Detallado y Progreso de la misma manera que haría para otros runbooks.

Los registros se almacenan localmente en cada Hybrid Worker en C:\\ProgramData\\Microsoft\\System Center\\Orchestrator\\7.2\\SMA\\Sandboxes.


## Relación con Service Management Automation

[Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx) es un componente de Windows Azure Pack (WAP) que permite ejecutar los mismos runbooks admitidos por Automatización de Azure en su centro de datos local. A diferencia de Automatización de Azure, SMA requiere una instalación local que incluye el Portal de administración de Windows Azure Pack y una base de datos para contener los runbooks y la configuración de SMA. Automatización de Azure proporciona estos servicios en la nube y solo requiere que mantenga los Trabajos híbridos de runbook en su entorno local.

Si es un usuario existente de SMA, puede mover los runbooks a Automatización de Azure para usarlos con Trabajo híbrido de runbook sin cambios, suponiendo que realizan su propia autenticación a recursos, tal como se describe en [Creación de runbooks para Trabajo híbrido de runbook](#creating-runbooks-for-hybrid-runbook-worker). Los runbooks en SMA se ejecutan en el contexto de la cuenta de servicio en el servidor de trabajo, lo que puede proporciona esa autenticación para los runbooks.

Puede usar los criterios siguientes para determinar si Automatización de Azure con Trabajo híbrido de runbook o Service Management Automation es más adecuado para sus requisitos.

- SMA requiere una instalación local de Windows Azure Pack con más recursos locales y costos de mantenimiento más altos que Automatización de Azure, que solo necesita instalar un agente en los trabajos de runbooks locales. Operations Management Suite administra los agentes, con lo que disminuyen los costos de mantenimiento.
- Automatización de Azure almacena sus runbooks en la nube y los entrega a Trabajos híbridos de runbooks locales. Si su directiva de seguridad no permite este comportamiento, deberá usar SMA.
- Windows Azure Pack es una descarga gratuita, mientras que Automatización de Azure podría generar gastos de suscripción.
- Automatización de Azure con Trabajo híbrido de runbook le permiten administrar runbooks para recursos de nube y recursos locales en una ubicación, en lugar de administrar Automatización de Azure y SMA de manera independiente.
- Automatización de Azure tiene características avanzadas, que incluyen la creación gráfica que no se encuentra disponible en SMA.


## Artículos relacionados

- [Inicio de un runbook en Automatización de Azure](automation-starting-a-runbook.md)
- [Edición de un runbook en Automatización de Azure](https://msdn.microsoft.com/library/dn879137.aspx)
 

<!---HONumber=AcomDC_0128_2016-->