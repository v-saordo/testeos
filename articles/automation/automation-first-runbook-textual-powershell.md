<properties
    pageTitle="Mi primer runbook de PowerShell en Automatización de Azure | Microsoft Azure"
    description="Tutorial que le guiará a través de la creación, prueba y publicación de un runbook de PowerShell."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="stevenka"
    editor="tysonn"/>

<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="02/23/2016"
    ms.author="magoedte;sngun"/>

# Mi primer runbook de PowerShell

> [AZURE.SELECTOR] - [Graphical](automation-first-runbook-graphical.md) - [PowerShell Workflow](automation-first-runbook-textual.md) - [PowerShell](automation-first-runbook-textual-PowerShell.md)

Este tutorial le guiará por la creación de un [runbook de PowerShell](automation-runbook-types.md#powershell-runbooks) en Automatización de Azure. Comenzaremos con un runbook simple que probaremos y publicaremos, y explicaremos cómo hacer un seguimiento del estado del trabajo del runbook. A continuación, modificaremos el runbook para administrar recursos de Azure, en este caso, iniciar una máquina virtual de Azure. A continuación, haremos un runbook más sólido agregando parámetros de runbook.

## Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

-	suscripción de Azure. Si aún no tiene ninguna, puede [activar los beneficios de la suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), o bien <a href="/pricing/free-trial/" target="_blank">[registrarse para obtener una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
-	[Cuenta de automatización](automation-configuring.md) para contener el runbook.
-	Una máquina virtual de Azure. Detendremos e iniciaremos esta máquina, por lo que no debería ser de producción.
-	[Usuario de Azure Active Directory y recurso de credencial de automatización](automation-configuring.md) para autenticarse en recursos de Azure. Este usuario debe tener permiso para iniciar y detener la máquina virtual.

## Paso 1: crear nuevo runbook

Empezaremos creando un runbook simple que genere el texto *Hello World*.

1.	En el Portal de Azure, abra su cuenta de Automatización. La página de la cuenta de Automatización proporciona una vista rápida de los recursos que hay en esa cuenta. Ya debería tener algunos recursos. Muchas de ellos son los módulos que se incluyen automáticamente en una cuenta nueva de Automatización. También debe tener el recurso de credencial que se menciona en los [requisitos previos](#prerequisites).
2.	Haga clic en el icono **Runbooks** para abrir la lista de runbooks. ![RunbooksControl](media/automation-first-runbook-textual-powershell/automation-runbooks-control.png)  
3.	Para crear un nuevo runbook, haga clic en el botón **Agregar un runbook** y luego en **Crear un nuevo runbook**.
4.	Asigne al runbook el nombre *MyFirstRunbook-PowerShell*.
5.	En este caso, vamos a crear un [runbook de PowerShell](automation-runbook-types.md#powershell-runbooks), así que seleccione **Powershell** en **Tipo de runbook**.![Tipo de runbook](media/automation-first-runbook-textual-powershell/automation-runbook-type.png)  
6.	Haga clic en **Crear** para crear el runbook y abra el editor de texto.

## Paso 2: Incorporación de código al runbook

Puede escribir el código directamente en el runbook o seleccionar los cmdlets, runbooks y recursos desde el control Biblioteca y agregarlos al runbook con los parámetros relacionados. En este tutorial, escribiremos directamente en el runbook.

1.	Nuestro runbook ahora está vacío, escriba *Write-Output "Hello World."*. ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2.	Para guardar el runbook, haga clic en **Guardar**.![Botón Guardar](media/automation-first-runbook-textual-powershell/automation-save-button.png)  

## Paso 3: probar el runbook

Antes de que publiquemos el runbook para que esté disponible en producción, queremos probarlo para asegurarnos de que funciona correctamente. Cuando se prueba un runbook, se ejecuta su versión **Borrador** y se visualizan sus resultados de forma interactiva.

1.	Haga clic en **Panel Prueba** para abrir el panel Prueba. ![Panel Prueba](media/automation-first-runbook-textual-powershell/automation-testpane.png)  
2.	Haga clic en **Iniciar** para iniciar la prueba. Esta debe ser la única opción habilitada.
3.	Se crea un [trabajo de runbook](automation-runbook-execution.md) y se muestra su estado. El estado del trabajo se iniciará como *En cola*, lo que indica que está esperando que haya disponible un trabajo de runbook en la nube. A continuación, pasará a *Iniciando* cuando un trabajador de runbook solicite el trabajo y, a continuación, a *En ejecución* cuando el runbook empiece a ejecutarse realmente.  
4.	Cuando se complete el trabajo del runbook, se mostrará su resultado. En nuestro caso, deberíamos ver *Hello World*.![Salida del panel de prueba](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
5.	Cierre el panel Prueba para volver al lienzo.

## Paso 4: publicar e iniciar el runbook

El runbook que acabamos de crear aún está en modo borrador. Tenemos que publicarlo antes de que podamos ejecutarlo en producción. Al publicar un runbook, se sobrescribe la versión publicada existente con la versión de borrador. En nuestro caso, no tenemos una versión publicada aún porque acabamos de crear el runbook.

1.	Haga clic en **Publicar** para publicar el runbook y, después, en **Sí** cuando se le solicite.![Botón Publicar](media/automation-first-runbook-textual-powershell/automation-publish-button.png)  
2.	Si se desplaza ahora a la izquierda para ver el runbook en el panel **Runbooks**, mostrará el **Estado de creación** **Publicado**.
3.	Desplácese de nuevo a la derecha para ver el panel de **MyFirstRunbook-PowerShell**. Las opciones en la parte superior nos permiten iniciar el runbook, verlo, programarlo para que se inicie en algún momento en el futuro o crear un [webhook](automation-webhooks.md) para que se inicie a través de una llamada HTTP.
4.	Como solo queremos iniciar el runbook, haga clic en **Iniciar** y, después, en **Aceptar** cuando se abra la hoja Iniciar runbook. ![Botón Iniciar](media/automation-first-runbook-textual-powershell/automation-start-button.png)  
5.	Se abre un panel de trabajo para el trabajo de runbook que acabamos de crear. Podemos cerrar este panel, pero en este caso lo dejaremos abierto para que podamos ver el progreso del trabajo.
6.	El estado del trabajo se muestra en **Resumen de trabajos** y coincide con los estados que vimos cuando probamos el runbook.![Resumen del trabajo](media/automation-first-runbook-textual-powershell/automation-job-summary.png)  
7.	Cuando el estado del runbook aparezca como *Completado*, haga clic en **Salida**. Se abre el panel Salida y podemos ver *Hello World*. ![Salida de trabajo](media/automation-first-runbook-textual-powershell/automation-job-output.png)
8.	Cierre el panel Salida.
9.	Haga clic en **Todos los registros** para abrir el panel Secuencias para el trabajo de runbook. Solo deberíamos ver *Hello World* en el flujo de salida, pero se pueden mostrar otras secuencias de un trabajo de runbook, como Detallado y Error, si el runbook escribe en ellas. ![Todos los registros](media/automation-first-runbook-textual-powershell/automation-alllogs.png)  
10.	Cierre el panel Secuencias y el panel de trabajo para volver al panel MyFirstRunbook.
11.	Haga clic en **Trabajos** para abrir el panel Trabajos de este runbook. Enumera todos los trabajos creados por este runbook. Solo deberíamos ver un trabajo en la lista, ya que solo ejecutamos el trabajo una vez. ![Lista de trabajos](media/automation-first-runbook-textual-powershell/automation-job-list.png)  
12.	Puede hacer clic en esta tarea para abrir el mismo panel Trabajo que vimos cuando se inició el runbook. Esto permite volver atrás en el tiempo y ver los detalles de cualquier trabajo que se creó para un runbook determinado.

## Paso 5: agregar autenticación para administrar recursos de Azure

Hemos probado y publicado nuestro runbook, pero hasta ahora no hace nada útil. Queremos que administre recursos de Azure. Sin embargo, no podrá hacerlo aunque a menos que lo autentiquemos con las credenciales que se mencionan en los [requisitos previos](#prerequisites). Esto se hace con el cmdlet **Add-AzureAccount**.

1.	Abra el editor de texto haciendo clic en **Editar** en el panel MyFirstRunbook-PowerShell. ![Editar runbook](media/automation-first-runbook-textual-powershell/automation-edit-runbook.png)  
2.	Ya no necesitamos la línea **Write-Output**, así que elimínela.
3.	En el control Biblioteca, expanda **Activos** y, luego, **Credenciales**.
4.	Haga clic con el botón derecho en la credencial y haga clic en **Agregar a lienzo**. Esto agrega una actividad **Get-AutomationPSCredential** para la credencial.
5.	Delante de **Get-AutomationPSCredential**, escriba *$Credential =* para asignar la credencial a una variable.
6.	En la línea siguiente, escriba *Add-AzureAccount -Credential $Credential*. ![Credential:](media/automation-first-runbook-textual-powershell/automation-get-credential.png)
7.	Haga clic en el **panel Prueba** para poder probar el runbook.
8.	Haga clic en **Iniciar** para iniciar la prueba. Una vez se completa, obtendrá un resultado que contiene el identificador de suscripción, el tipo y los inquilinos de su cuenta. Esto confirma que la credencial es válida.

## Paso 6: Incorporación de una actividad para iniciar una máquina virtual

Ahora que nuestro runbook se autentica en nuestra suscripción de Azure, podemos administrar los recursos. Vamos a agregar un comando para iniciar una máquina virtual. Puede seleccionar cualquier máquina virtual en su suscripción de Azure. Por ahora vamos a codificar ese nombre en el cmdlet.

1.	Después de *Add-AzureAccount*, escriba *Start-AzureVM -Name 'nombreDeVM' -ServiceName 'nombreDeServicioVM'* proporcionando el nombre y el nombre del servicio de la máquina virtual que se iniciará. ![StartVM](media/automation-first-runbook-textual-powershell/automation-startvm.png)  
2.	Guarde el runbook y haga clic en el **panel Prueba** para poder probarlo.
3.	Haga clic en **Iniciar** para iniciar la prueba. Cuando haya terminado, compruebe que la máquina virtual se ha iniciado.

## Paso 7: agregar un parámetro de entrada al runbook

Actualmente, nuestro runbook inicia la máquina virtual que codificamos en el runbook, pero nuestro runbook sería más útil si pudiéramos especificar la máquina virtual cuando se inicia el runbook. Ahora agregaremos parámetros de entrada para que el runbook proporcione esa funcionalidad.

1.	Agregue parámetros para *VMName* y *VMServiceName* al runbook y use estas variables con el cmdlet **Start-AzureVM**, tal como se muestra en la siguiente imagen. ![Agregar parámetro](media/automation-first-runbook-textual-powershell/automation-add-parameter.png)  
2.	Guarde el runbook y abra el panel Prueba. Tenga en cuenta que ahora puede proporcionar valores para las dos variables de entrada que se usarán en la prueba.
3.	Cierre el panel Prueba.
4.	Haga clic en **Publicar** para publicar la nueva versión del runbook.
5.	Detenga la máquina virtual que inició en el paso anterior.
6.	Haga clic en **Iniciar** para iniciar el runbook. Escriba el valor de **VMName** y de **VMServiceName** para la máquina virtual que va a iniciar. ![Pasar parámetro](media/automation-first-runbook-textual-powershell/automation-pass-parameter.png)  
7.	Cuando se complete el runbook, compruebe que la máquina virtual se ha iniciado.

## Diferencias del flujo de trabajo de PowerShell

Los runbooks de PowerShell tienen el mismo ciclo de vida, las mismas funcionalidades y la misma administración que los runbooks de flujo de trabajo de PowerShell, pero existen algunas diferencias y limitaciones:

1.	Los runbooks de PowerShell se ejecutan más rápido en comparación con los runbooks de flujo de trabajo de PowerShell, pues estos no tienen paso de compilación.
2.	Los runbooks de flujo de trabajo de PowerShell admiten los puntos de control y su uso. Los runbooks de flujo de trabajo de PowerShell pueden reanudarse desde cualquier punto en el runbook mientras que los runbooks de PowerShell solo puede reanudarse desde el principio.
3.	Los runbooks de flujo de trabajo de PowerShell admiten la ejecución en serie y en paralelo, mientras que los runbooks de PowerShell solo puede ejecutar comandos en serie.
4.	En un runbook de flujo de trabajo de PowerShell, una actividad, un comando o un bloque de scripts pueden tener su propio espacio de ejecución, mientras que en un runbook de PowerShell todo lo que hay en el script se ejecuta en un único espacio de ejecución. También se observan algunas [diferencias sintácticas](https://technet.microsoft.com/magazine/dn151046.aspx) entre un runbook de PowerShell nativo y un runbook de flujo de trabajo de PowerShell.

## Pasos siguientes

-	Para empezar a trabajar con runbooks gráficos, consulte [Mi primer runbook gráfico](automation-first-runbook-graphical.md)
-	Para empezar a trabajar con runbooks de flujo de trabajo de PowerShell, consulte [Mi primer runbook de flujo de trabajo de PowerShell](automation-first-runbook-textual.md)
-	Para más información acerca de los tipos de runbook, sus ventajas y limitaciones, consulte [Tipos de runbooks de Automatización de Azure](automation-runbook-types.md)
-	Para más información sobre la característica de compatibilidad con scripts de PowerShell, consulte [Announcing Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) (Anuncio de la compatibilidad nativa con scripts de PowerShell en Automatización de Azure).

<!---HONumber=AcomDC_0302_2016-->