<properties
   pageTitle="Sugerencias para la solución de problemas para errores comunes de Automatización de Azure| Microsoft Azure"
   description="Este artículo proporciona pasos básicos de solución de problemas para corregir errores comunes que puede encontrar al trabajar con Automatización de Azure."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="stevenka"
   editor="tysonn"
   tags="top-support-issue"/>
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/02/2016"
   ms.author="sngun; v-reagie"/>

# Sugerencias para la solución de problemas para errores comunes de Automatización de Azure

Este artículo explica algunos de los errores comunes que se pueden producir al trabajar con Automatización de Azure y sugiere los posibles pasos para corregirlos.

## Solución de problemas de errores de autenticación al trabajar con runbooks de Automatización de Azure  

### Escenario: Fallo del inicio de sesión en la cuenta de Azure

**Error:** recibe el error "Unknown\_user\_type: Tipo de usuario desconocido" mientras trabaja con los cmdlets Add-AzureAccount o Login-AzureRmAccount.

**Motivo del error:** este error se produce si el nombre de activo de credencial no es válido o si el nombre de usuario y la contraseña que usó para configurar el activo de credencial de automatización no son válidos.

**Sugerencias para solucionar el problema:** para determinar cuál es el problema, siga estos pasos:

1. Asegúrese de que el nombre de activo de la credencial de Automatización que use para conectarse a Azure no contenga caracteres especiales, incluido el carácter **@**.  

2. Compruebe que puede utilizar el nombre de usuario y la contraseña que se almacenan en la credencial de Automatización de Azure en su editor local ISE de PowerShell. Puede hacerlo ejecutando los siguientes cmdlets en el ISE de PowerShell:

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount –Credential $Cred

3. Si la autenticación falla localmente, esto significa que no ha configurado correctamente las credenciales de Azure Active Directory. Consulte la entrada de blog [Authenticating to Azure using Azure Active Directory (Autenticación en Azure mediante Azure Active Directory)](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) para conseguir configurar correctamente la cuenta de Active Directory.


### Escenario: No se encuentra la suscripción de Azure

**Error:** Recibe el error "No se ha encontrado la suscripción de nombre ``<subscription name>``" mientras está trabajando con los cmdlets Select-AzureSubscription o Select-AzureRmSubscription.

**Motivo del error:** este error se produce si el nombre de la suscripción no es válido o si el usuario de Azure Active Directory que está intentando obtener los detalles de suscripción no está configurado como un administrador de la suscripción.

**Sugerencias para solucionar el problema:** para determinar si se ha autenticado correctamente en Azure y tener acceso a la suscripción que intenta seleccionar, siga estos pasos:

1. Asegúrese de que ejecuta **Add-AzureAccount** antes de ejecutar el cmdlet **Select-AzureSubscription**.  

2. Si continúa recibiendo este mensaje de error, modifique el código agregando el cmdlet **Get-AzureSubscription** a continuación del cmdlet **Add-AzureAccount** y luego ejecute el código. Ahora compruebe si la salida de Get-AzureSubscription contiene los detalles de su suscripción.
    * Si no ve los detalles de la suscripción en la salida, esto significa que la suscripción no se ha inicializado todavía.  
    * Si ve los detalles de suscripción en la salida, confirme que está utilizando el nombre o el identificador correctos de la suscripción con el cmdlet **Select-AzureSubscription**.   


### Escenario: Error de autenticación en Azure porque está habilitada la autenticación multifactor

**Error:** recibe el error "Add-AzureAccount: AADSTS50079: Es necesaria una inscripción de autenticación fuerte (proofup)" al autenticarse en Azure con el nombre de usuario y la contraseña de Azure.

**Motivo del error:** si dispone de autenticación multifactor en su cuenta de Azure, no puede usar un usuario de Azure Active Directory para autenticarse en Azure. En su lugar, tiene que utilizar un certificado o una entidad de servicio para autenticarse en Azure.

**Sugerencias para solucionar el problema:** para usar un certificado con los cmdlets de Administración de servicios de Azure, consulte [Managing Azure Services with the Microsoft Azure Automation Preview Service](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) (Administración de servicios de Azure con el servicio de vista previa de Automatización de Microsoft Azure) Para usar una entidad de servicio con los cmdlets de Azure Resource Manager, consulte [Creación de aplicación de Active Directory y entidad de servicio mediante el portal](./resource-group-create-service-principal-portal.md) y [Autenticación de una entidad de servicio con Azure Resource Manager.](./resource-group-authenticate-service-principal.md)


## Solucionar errores comunes al trabajar con runbooks

### Escenario: error en runbook debido a un objeto deserializado

**Error:** El runbook falla y recibe el error "No se puede enlazar el parámetro ``<ParameterName>``. No se puede convertir el valor ``<ParameterType>`` de tipo deserializado ``<ParameterType>`` a tipo ``<ParameterType>``".

**Motivo del error:** Si su runbook es un flujo de trabajo de PowerShell almacena objetos complejos en un formato deserializado, para conservar el estado del runbook si se suspende el flujo de trabajo.

**Sugerencias para solucionar el problema:**

Cualquiera de las siguientes tres alternativas solucionará este problema:

1. Si canaliza objetos complejos de un cmdlet a otro, encapsule estos cmdlets en un InlineScript.  
2. En lugar de pasar el objeto complejo entero, pase solamente el nombre o valor del mismo que necesite.  

3. Use un runbook de PowerShell en lugar de un runbook de flujo de trabajo de PowerShell.


### Escenario: Error de trabajo de Runbook porque superó la cuota asignada

**Error:** se produce un error en el runbook con el mensaje "Se ha alcanzado la cuota para el tiempo de ejecución de trabajo mensual para esta suscripción".

**Motivo del error:** este error se produce cuando la ejecución del trabajo supera la cuota gratuita de 500 minutos para su cuenta. Esta cuota se aplica a todos los tipos de tareas de ejecución de trabajo como realizar pruebas de un trabajo, iniciar un trabajo desde el portal, ejecutar un trabajo usando Webhook y programar un trabajo para ejecutar mediante el Portal de Azure o en su centro de datos. Para más información sobre precios para Automatización, consulte [Precios de Automatización](https://azure.microsoft.com/pricing/details/automation/).

**Sugerencias para solucionar el problema:** si desea usar más de 500 minutos de procesamiento por mes, tiene que cambiar la suscripción del nivel Gratis al nivel Básico. Puede actualizar al nivel Básico realizando los pasos siguientes:

1. Inicie sesión en la suscripción de Azure  
2. Seleccione la cuenta de Automatización que desee actualizar  
3. Haga clic en **Configuración** > **Plan de tarifa y uso** > **Plan de tarifa**  
4. En la hoja **Elija su nivel de precios**, seleccione **Básico**    


### Escenario: No se reconoce el Cmdlet cuando se ejecuta un runbook

**Error:** se produce un error en su trabajo de runbook con el mensaje "``<cmdlet name>``: El término ``<cmdlet name>`` no se reconoce como nombre de un cmdlet, una función, un archivo de script, o un programa ejecutable".

**Motivo del error:** este error se produce cuando el motor de PowerShell no puede encontrar el cmdlet que está utilizando en su runbook. Esto podría deberse a que el módulo que contiene el cmdlet no está presente en la cuenta, a que haya un conflicto de nombres con un nombre de runbook o a que el cmdlet también existe en otro módulo y Automatización no puede resolver el nombre.

**Sugerencias para solucionar el problema:** cualquiera de las siguientes alternativas solucionará este problema:

- Compruebe que ha escrito correctamente el nombre del cmdlet y verifique que la ruta de acceso al cmdlet es correcta.  

- Asegúrese de que el cmdlet existe en su cuenta de Automatización y de que no hay ningún conflicto. Para comprobar si está presente el cmdlet, abra un runbook en modo de edición y busque el cmdlet que desea encontrar en la biblioteca o ejecute **Get-Command ``<CommandName>``**. Una vez que haya comprobado que el cmdlet está disponible para la cuenta y que no hay conflictos de nombres con otros cmdlets o runbooks, agréguelo al lienzo y asegúrese de que está utilizando un parámetro válido establecido en su runbook.

- Si tiene un conflicto de nombres y el cmdlet está disponible en dos módulos diferentes, puede resolver este problema mediante el nombre completo del cmdlet. Por ejemplo, puede usar **NombreDeMódulo\\NombredeCmdlet**.

- Si está ejecutando el runbook local en un grupo de trabajo híbrido, asegúrese de que el cmdlet o módulo está instalado en el equipo que hospeda el trabajo híbrido.


### Escenario: un runbook de larga duración presenta errores constantemente con la excepción: "El trabajo no se puede seguir ejecutando porque se expulsó repetidamente desde el mismo punto de control".

**Motivo del error:** esto es así por diseño debido a la supervisión de "reparto justo" de los procesos de Automatización de Azure, que suspende automáticamente un runbook si se ejecuta más de tres horas. Sin embargo, el mensaje de error devuelto no proporciona opciones para qué hacer a continuación. Un runbook se puede suspender por varios motivos. Las suspensiones se producen principalmente debido a errores. Por ejemplo, una excepción no detectada en un runbook, un error de red o un bloqueo en el servicio Runbook Worker que ejecuta el runbook, harán que el runbook se suspenda y se inicie desde su último punto de control cuando se reanude.

**Sugerencias para solucionar el problema:** la solución documentada para evitar este problema consiste en utilizar puntos de control en un flujo de trabajo. Para aprender más, consulte [Aprendizaje del flujo de trabajo de Windows PowerShell](automation-powershell-workflow.md#Checkpoints). Encontrará una explicación más completa del "reparto equitativo" y los puntos de control en este artículo de blog [Azure Automation: Reliable, Fault-Tolerant Runbook Execution Using Checkpoints](https://azure.microsoft.com/es-ES/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/) (Automatización de Azure: uso de puntos de control en runbooks).


## Solución de problemas de errores comunes al importar módulos

### Escenario: No se puede importar el módulo o no se puede ejecutar cmdlets después de la importación

**Error:** un módulo no se puede importar o se importa correctamente, pero no se extrae ningún cmdlet.

**Motivo del error:** algunas razones comunes por las que un módulo no se importa correctamente a Automatización de Azure son:

- La estructura no coincide con la estructura que Automatización necesita.  

- El módulo depende de otro módulo que no se ha implementado en su cuenta de Automatización.

- Al módulo le faltan sus dependencias en la carpeta.

- El cmdlet **New-AzureRmAutomationModule** se está usando para cargar el módulo y no se ha proporcionado la ruta de acceso de almacenamiento completa o no se ha cargado el módulo usando una URL de acceso público.

**Sugerencias para solucionar el problema:**

cualquiera de las siguientes alternativas solucionará este problema:

- Asegúrese de que el módulo sigue el formato siguiente: NombreMódulo.zip **->** NombreMódulo o número de versión **->** (NombreMódulo.psm1, NombreMódulo.psd1)

- Abra el archivo. psd1 y compruebe si el módulo tiene dependencias. Si es así, cargue estos módulos en la cuenta de Automatización.

- Asegúrese de que todos los archivos .dll a los que se hace referencia están presentes en la carpeta del módulo.


## Solucionar errores comunes al trabajar con la Configuración de estado deseado (DSC)  

### Escenario: el nodo se encuentra en estado de error con el error "No encontrado"

**Error:** el nodo tiene un informe con estado **Error** y que contiene el error "Error al intentar obtener la acción de servidor https://``<url>``//accounts/``<account-id>``/Nodes(AgentId=``<agent-id>``)/GetDscAction failed because a valid configuration ``<guid>".

**Motivo del error:** este error suele ocurrir cuando se asigna al nodo un nombre de configuración (por ejemplo, ABC), en lugar de un nombre de configuración de nodo (por ejemplo, ABC.WebServer).

**Sugerencias de solución de problemas:**

- Asegúrese de estar asignando al nodo un "nombre de configuración de nodo" y no el "nombre de configuración".  

- Puede asignar una configuración de nodo a un nodo mediante el Portal de Azure o con un cmdlet de PowerShell.
    - Para asignar una configuración de nodo a un nodo mediante el Portal de Azure, abra la hoja **Nodos de DSC**, seleccione un nodo y haga clic en el botón **Asignar configuración de nodo**.  
    - Para asignar una configuración de nodo a un nodo mediante un cmdlet de PowerShell, use el cmdlet **AzureRmAutomationDscNode Set**.


### Escenario: no se produjeron configuraciones de nodo (archivos mof) al compilarse una configuración

**Error:** el trabajo de compilación de DSC se suspendió con el error "La compilación finalizó correctamente, pero no se generaron archivos .mof de configuración de nodo".

**Motivo del error:** cuando la expresión que aparece junto a la palabra clave **Node** en la configuración de DSC se evalúa como $null, no se produce ninguna configuración de nodo.

**Sugerencias para solucionar el problema:** cualquiera de las siguientes alternativas solucionará este problema:

- Asegúrese de que la expresión junto a la palabra clave **Node** en la definición de configuración no se está evaluando como $null.  
- Si se pasan datos de configuración al compilar la configuración, asegúrese de que se pasan los valores esperados que la configuración necesita de [configurationData](automation-dsc-compile.md#configurationdata).


### Escenario: el informe de nodo de DSC se queda bloqueado en el estado "en curso"

**Error:** el agente DSC genera el mensaje "No se encontró ninguna instancia con los valores de propiedad especificados".

**Motivo del error:** ha actualizado la versión de WMF y ha dañado WMI.

**Sugerencias para solucionar el problema:** siga las instrucciones que se indican en la entrada del blog [DSC known issues and limitations](https://msdn.microsoft.com/powershell/wmf/limitation_dsc) (Limitaciones y problemas conocidos de DSC) para corregir el problema.


### Escenario: no se puede usar una credencial en una configuración de DSC

**Error:** el trabajo de compilación de DSC se suspendió con el error "Error System.InvalidOperationException al procesar la propiedad 'Credential' de tipo '``<some resource name>``': se permite convertir y almacenar una contraseña cifrada como texto no cifrado solo si PSDscAllowPlainTextPassword se establece en true".

**Motivo del error:** ha usado una credencial en la configuración pero no ha proporcionado el valor adecuado de **ConfigurationData** para establecer **PSDscAllowPlainTextPassword** como true para cada configuración de nodo.

**Sugerencias de solución de problemas:**
- Asegúrese de que pasa el valor adecuado de **ConfigurationData** para establecer **PSAllowPlainTextPassword** como true para cada configuración de nodo mencionada en la configuración. Para más información, consulte los [recursos en DSC de Automatización de Azure](automation-dsc-compile.md#assets).


## Pasos siguientes

Si ha seguido los pasos de la solución de problemas anteriores y necesita ayuda adicional en cualquier punto de este artículo, puede:

- Obtener ayuda de expertos de Azure. Envíe su problema a los [foros de MSDN Azure o Stack Overflow](https://azure.microsoft.com/support/forums/).

- Registrar un incidente de soporte técnico de Azure. Vaya al [Sitio del soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte técnico** en **Soporte técnico y facturación**.

- Si está buscando una solución de runbook o un módulo de integración de Automatización de Azure, publique una solicitud de script en el [Centro de scripts](https://azure.microsoft.com/documentation/scripts/).

- Si tiene comentarios o solicitudes de características para Automatización de Azure, publíquelos en [User Voice](https://feedback.azure.com/forums/34192--general-feedback).

<!------HONumber=AcomDC_0302_2016-->