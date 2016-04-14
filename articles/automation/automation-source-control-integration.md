<properties 
    pageTitle="Integración del control de código fuente en Automatización de Azure | Microsoft Azure"
    description="En este artículo se describe la integración del control de código fuente con GitHub en Automatización de Azure."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="stevenka"
    editor="tysonn" />    
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="02/23/2016"
    ms.author="magoedte;sngun" />

# Integración del control de código fuente en Automatización de Azure

La integración del control de código fuente permite asociar runbooks de su cuenta de Automatización a un repositorio de control de código fuente de GitHub. El control de código fuente le permite colaborar fácilmente con su equipo, realizar el seguimiento de los cambios y revertir a versiones anteriores de los runbooks. Por ejemplo, le permite sincronizar distintas ramas de control de código fuente con sus cuentas de Automatización de desarrollo, prueba o producción, lo que facilita la promoción de código que se ha probado en el entorno de desarrollo a la cuenta de Automatización de producción.

El control de código fuente le permite insertar código de Automatización de Azure en el control de código fuente o extraer sus runbooks del control de código fuente para llevarlos a Automatización de Azure. En este artículo se describe cómo configurar el control de código fuente en su entorno de Automatización de Azure. Para comenzar, vamos a configurar Automatización de Azure para acceder a su repositorio de GitHub y a recorrer diferentes operaciones que pueden realizarse mediante la integración del control de código fuente.


>[AZURE.NOTE] El control de código fuente admite la extracción e inserción de [runbooks del flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) y de [runbooks de PowerShell](automation-runbook-types.md#powershell-runbooks). Los [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) aún no se admiten.<br><br>


Hay dos pasos sencillos necesarios para configurar el control de código fuente para su cuenta de Automatización y solo uno si ya tiene una cuenta GitHub. Son las siguientes:
## Paso 1: Creación de un repositorio de GitHub

Si ya tiene una cuenta de GitHub y un repositorio que desea vincular a Automatización de Azure, inicie sesión en su cuenta existente y comience desde el paso 2. De lo contrario, vaya a [GitHub](https://github.com/), suscríbase a una cuenta nueva y [cree un nuevo repositorio](https://help.github.com/articles/create-a-repo/).


## Paso 2: Configuración del control de código fuente en Automatización de Azure

1. En la hoja Cuenta de Automatización del Portal de Azure, haga clic en **Configurar control de código fuente**. 
 
    ![Configuración del control de código fuente](media/automation-source-control-integration/automation_01_SetUpSourceControl.png)

2. Se abre la hoja **Control de código fuente**, donde puede configurar los detalles de la cuenta de GitHub. A continuación se muestra la lista de parámetros de configuración:

    |**Parámetro** |**Descripción** |
    |:---|:---| 
    |Elegir origen | Seleccione el origen. Actualmente, solo se admite **GitHub**. |
    |Autorización | Haga clic en el botón **Autorizar** para conceder acceso a Automatización de Azure al repositorio de GitHub. Si ya inició sesión con su cuenta de GitHub en una ventana diferente, se usan las credenciales de dicha cuenta. Cuando la autorización sea correcta, la hoja mostrará su nombre de usuario de GitHub en **Propiedad de autorización**. |
    |Selección del repositorio | Seleccione un repositorio de GitHub en la lista de repositorios disponibles. |
    |Elegir rama | Seleccione una rama en la lista de ramas disponibles. Solo se muestra la rama **principal** si no creó ninguna rama. |
    |Ruta de acceso de la carpeta de runbook | La ruta de acceso de la carpeta de runbook especifica la ruta de acceso en el repositorio de GitHub desde el que desea insertar o extraer el código. Debe especificarse en formato **/nombreCarpeta/nombreDeSubcarpeta**. Solo se pueden sincronizar los runbooks de la ruta de acceso de la carpeta de runbook con la cuenta de Automatización. Los runbooks de las subcarpetas de la ruta de acceso de la carpeta de runbook **no** se sincronizarán. Use **/** para sincronizar todos los runbooks en el repositorio. |


3. Por ejemplo, si tiene un repositorio denominado **scriptsDePowerShell** que contiene una carpeta denominada **carpetaRaíz**, que a su vez contiene una carpeta denominada **subCarpeta**. Puede usar las siguientes cadenas para cada nivel de la carpeta de sincronización:

    1. Para sincronizar runbooks desde el **repositorio**, la ruta de acceso de la carpeta de runbook es */*.
    2. Para sincronizar runbooks desde la **carpetaRaíz**, la ruta de acceso de la carpeta de runbook es */carpetaRaíz*.
    3. Para sincronizar runbooks desde la **subCarpeta**, la ruta de acceso de la carpeta de runbook es */carpetaRaíz/subCarpeta*.
  

4. Después de configurarlos, los parámetros se muestran en la hoja **Configurar control de código fuente.**
 
    ![Hoja Configurar](media/automation-source-control-integration/automation_02_SourceControlConfigure.png)


5. Al hacer clic en Aceptar, la integración del control de código fuente estará configurada para la cuenta de Automatización y debe actualizarse con la información de GitHub. Ahora puede hacer clic en este elemento para ver todo el historial de trabajos de sincronización del control de código fuente.

    ![Valores de repositorio](media/automation-source-control-integration/automation_03_RepoValues.png)

6. Una vez configurado el control de código fuente, se crearán los siguientes recursos de Automatización en su cuenta de Automatización: dos [activos de variables](automation-variables.md).
      
    * La variable **Microsoft.Azure.Automation.SourceControl.Connection** contiene los valores de la cadena de conexión, tal como se muestra a continuación.  

    |**Parámetro** |**Valor** |
    |:---|:---|
    | Nombre | Microsoft.Azure.Automation.SourceControl.Connection |
    | Tipo | String |
    | Valor | {"Branch":<*nombreDeRama*>,"RunbookFolderPath":<*rutaDeCarpetaDeRunbook*>,"ProviderType":<*tiene un valor de 1 para GitHub*>,"Repository":<*nombreDelRepositorio*>,"Username":<*nombreDe UsuarioDeGitHub*>} | <br>


    * La variable **Microsoft.Azure.Automation.SourceControl.OAuthToken** contiene el valor cifrado seguro de OAuthToken.  

    |**Parámetro** |**Valor** |
    |:---|:---|
    | Nombre | Microsoft.Azure.Automation.SourceControl.OAuthToken |
    | Tipo | Unknown(Encrypted) |
    | Valor | <*OAuthToken cifrado*> |  

    ![Variables](media/automation-source-control-integration/automation_04_Variables.png)

    * Se agrega el **control de código fuente de Automatización** como una aplicación autorizada a su cuenta de GitHub. Para ver la aplicación: desde la página principal de GitHub, vaya a **perfil** > **Configuración** > **Aplicaciones**. Esta aplicación permite que Automatización de Azure sincronice el repositorio de GitHub con una cuenta de Automatización.  

    ![Aplicación Git](media/automation-source-control-integration/automation_05_GitApplication.png)


## Uso del control de código fuente en Automatización


### Protección de un runbook de Automatización de Azure en el control de código fuente

La protección de runbooks permite insertar los cambios realizados en un runbook en Automatización de Azure en el repositorio de control de código fuente. A continuación se muestran los pasos necesarios para proteger un runbook:

1. Desde su cuenta de Automatización, [cree un nuevo runbook textual](automation-first-runbook-textual.md) o [edite un runbook textual existente](automation-edit-textual-runbook.md). Este runbook puede ser un flujo de trabajo de PowerShell o un runbook de scripts de PowerShell.  

2. Después de editar el runbook, guárdelo y haga clic en **Proteger** en la hoja **Editar**.

    ![Botón Proteger](media/automation-source-control-integration/automation_06_CheckinButton.png)


     >[AZURE.NOTE] La protección de la Automatización de Azure sobrescribirá el código existente en el control de código fuente. La instrucción de línea de comandos de Git equivalente para la protección es **git add + git commit + git push**.

3. Al hacer clic en **Insertar en el repositorio**, se mostrará un mensaje de confirmación. Haga clic en Sí para continuar.

    ![Mensaje de protección](media/automation-source-control-integration/automation_07_CheckinMessage.png)

4. La protección se inicia en el runbook de control de código fuente: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Este runbook se conecta a GitHub y aplica los cambios realizados de Automatización de Azure al repositorio. Para ver el historial de trabajos de protección, vuelva a la pestaña **Integración del control de código fuente** y haga clic para abrir la hoja Sincronización de repositorio. Esta hoja muestra todos los trabajos de control de código fuente. Seleccione el trabajo que quiere ver y haga clic para ver los detalles.

    ![Runbook de protección](media/automation-source-control-integration/automation_08_CheckinRunbook.png)

    >[AZURE.NOTE] Los runbooks de control de código fuente son runbooks de Automatización especiales que no se pueden ver ni editar. Aunque no se muestran en la lista de runbooks, verá que se muestran los trabajos de sincronización en la lista de trabajos.
 
5. El nombre del runbook modificado se envía como un parámetro de entrada al runbook de protección. También puede [ver los detalles del trabajo](automation-runbook-execution.md#viewing-job-status-using-the-azure-management-portal) si expande el runbook en la hoja **Sincronización de repositorio**.

    ![Entrada de protección](media/automation-source-control-integration/automation_09_CheckinInput.png)

6. Actualice el repositorio de GitHub cuando finalice el trabajo para ver los cambios. Debe haber una confirmación en el repositorio con un mensaje de confirmación: ***Nombre de runbook* actualizado en Automatización de Azure.**



### Sincronización de runbooks de control de código fuente a la Automatización de Azure 

El botón de sincronización que se encuentra en la hoja Sincronización de repositorios permite extraer todos los runbooks desde la ruta de acceso de la carpeta de runbooks del repositorio y llevarlos a su cuenta de Automatización. El mismo repositorio puede sincronizarse con más de una cuenta de Automatización. A continuación se muestran los pasos necesarios para sincronizar un runbook:

1. En la cuenta de Automatización donde se configuró el control de código fuente, abra la hoja **Integración del control de código fuente/Sincronización de repositorio** y haga clic en **Sincronizar**. En el mensaje de confirmación que se muestra, haga clic en **Sí** para continuar.  

    ![Botón Sincronizar](media/automation-source-control-integration/automation_10_SyncButtonwithMessage.png)

2. La sincronización inicia el runbook: **Sync-MicrosoftAzureAutomationAccountFromGitHubV1**. Este runbook se conecta a GitHub y aplica los cambios realizados del repositorio a Automatización de Azure. Debería ver un nuevo trabajo en la hoja **Sincronización de repositorio** para esta acción. Para ver detalles sobre el trabajo de sincronización, haga clic para abrir la hoja de detalles del trabajo.
 
    ![Runbook de sincronización](media/automation-source-control-integration/automation_11_SyncRunbook.png)

 
    >[AZURE.NOTE] Una sincronización del control de código fuente sobrescribe la versión de borrador de los runbooks que existen actualmente en la cuenta de Automatización para **TODOS** los runbooks que están actualmente en el control del código fuente. La instrucción de línea de comandos de Git equivalente para realizar la sincronización es **git pull**.


## Solución de problemas de control de código fuente

Si hay errores en el trabajo de protección o de sincronización, el estado del trabajo debe suspenderse. Podrá ver más detalles sobre el error en la hoja del trabajo. En la sección **Todos los registros** se mostrarán todas las transmisiones de PowerShell asociadas a ese trabajo. De esta forma tendrá los detalles necesarios para ayudarle a solucionar los problemas con la protección o la sincronización. También se muestra la secuencia de acciones que se produjeron mientras se sincronizaba o insertaba en el repositorio un runbook.

![Imagen de todos los registros](media/automation-source-control-integration/automation_13_AllLogs.png)

## Desconexión del control de código fuente

Para desconectarse de su cuenta de GitHub, abra la hoja Sincronización de repositorio y haga clic en **Desconectar**. Cuando desconecte el control de código fuente, los runbooks que se sincronizaron seguirán estando en su cuenta de Automatización, pero no se habilitará la hoja Sincronización de repositorio.

  ![Botón Desconectar](media/automation-source-control-integration/automation_12_Disconnect.png)



## Pasos siguientes

Para obtener más información sobre la integración del control de código fuente, consulte los siguientes recursos:
- [Automatización de Azure: integración del control de código fuente en Automatización de Azure](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
- [Vote por su sistema de control de código fuente favorito](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d)  
- [Automatización de Azure: integración del control de código fuente de runbook mediante Visual Studio Team Services](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  

<!---HONumber=AcomDC_0302_2016-->