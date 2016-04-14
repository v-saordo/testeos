<properties
    pageTitle="Mi primer runbook gráfico en Automatización de Azure | Microsoft Azure"
    description="Tutorial que le guiará a través de la creación, prueba y publicación de un runbook gráfico simple."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="stevenka"
    editor=""/>

<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="02/23/2016"
    ms.author="magoedte;bwren"/>

# Mi primer runbook gráfico

> [AZURE.SELECTOR] - [Graphical](automation-first-runbook-graphical.md) - [PowerShell Workflow](automation-first-runbook-textual.md) - [PowerShell](automation-first-runbook-textual-PowerShell.md)

Este tutorial le guiará por la creación de un [runbook gráfico](automation-runbook-types.md#graphical-runbooks) en Automatización de Azure. Comenzaremos con un runbook simple que probaremos y publicaremos, y explicaremos cómo hacer un seguimiento del estado del trabajo del runbook. A continuación, modificaremos el runbook para administrar recursos de Azure, en este caso, iniciar una máquina virtual de Azure. A continuación, haremos un runbook más sólido agregando parámetros de runbook y un vínculo condicional.

## Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

-	suscripción de Azure. Si aún no tiene ninguna, puede [activar los beneficios de la suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), o bien <a href="/pricing/free-trial/" target="_blank">[registrarse para obtener una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
-	[Cuenta de automatización](automation-configuring.md) para contener el runbook.
-	Una máquina virtual de Azure. Detendremos e iniciaremos esta máquina, por lo que no debería ser de producción.
-	[Usuario de Azure Active Directory y recurso de credencial de automatización](automation-configuring.md) para autenticarse en recursos de Azure. Este usuario debe tener permiso para iniciar y detener la máquina virtual.

## Paso 1: crear nuevo runbook

Empezaremos creando un runbook simple que genere el texto *Hello World*.

1.	En el Portal de Azure, abra su cuenta de Automatización. La página de la cuenta de Automatización proporciona una vista rápida de los recursos que hay en esa cuenta. Ya debería tener algunos recursos. Muchas de ellos son los módulos que se incluyen automáticamente en una cuenta nueva de Automatización. También debe tener el recurso de credencial que se menciona en los [requisitos previos](#prerequisites).
2.	Haga clic en el icono **Runbooks** para abrir la lista de runbooks.<br> ![Control de runbooks](media/automation-first-runbook-graphical/runbooks-control.png)
3.	Para crear un nuevo runbook, haga clic en el botón **Agregar un runbook** y luego en **Crear un nuevo runbook**.
4.	Asigne al runbook el nombre *MyFirstRunbook-Graphical*.
5.	En este caso, vamos a crear un [runbook gráfico](automation-graphical-authoring-intro.md), por lo que seleccionaremos **Gráfico** para **Tipo de runbook**.<br>![Nuevo runbook](media/automation-first-runbook-graphical/new-runbook.png)
6.	Haga clic en **Crear** para crear el runbook y abrir el editor gráfico.

## Paso 2: agregar actividades al runbook

El control Biblioteca en el lado izquierdo del editor le permite seleccionar actividades para agregarlas al runbook. Vamos a agregar un cmdlet **Write-Output** para generar el texto desde el runbook.

1.	En el control Biblioteca, expanda el nodo **Cmdlets** y **Microsoft.PowerShell.Utility**.<br>![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/cmdlets-powershell-utility.png)
2.	Desplácese hasta la parte inferior de la lista. Haga clic en **Write-Output** y en **Agregar al lienzo**.
3.	Haga clic en la actividad **Write-Output** en el lienzo. Se abrirá el control Configuración, que le permite configurar la actividad.
4.	El campo **Etiqueta** tiene como valor predeterminado el nombre del cmdlet, pero se puede cambiar a algo más descriptivo. Cámbielo a *Escribir Hello World para la salida*.
5.	Haga clic en **Parámetros** para especificar los valores de los parámetros del cmdlet. Algunos cmdlets tienen varios conjuntos de parámetros y debe seleccionar los que usará. En este caso, **Write-Output** solo tiene un conjunto de parámetros, por lo que no es necesario seleccionar uno. <br>![Propiedades Write-Output](media/automation-first-runbook-graphical/write-output-properties.png)
6.	Seleccione el parámetro **InputObject**. Este es el parámetro donde especificaremos el texto que se va a enviar a la secuencia de salida.
7.	En la lista desplegable **Origen de datos**, seleccione **Expresión de PowerShell**. La lista desplegable **Origen de datos** proporciona diferentes orígenes que se usan para rellenar un valor de parámetro. Puede usar la salida de esos orígenes como otra actividad, un recurso de Automatización o una expresión de PowerShell. En este caso, solo deseamos generar el texto *Hello World*. Podemos usar una expresión de PowerShell y especificar una cadena.
8.	En el cuadro **Expresión**, escriba*"Hello World"* y haga clic en **Aceptar** dos veces para volver al lienzo.<br>![Expresión de PowerShell](media/automation-first-runbook-graphical/expression-hello-world.png)
9.	Para guardar el runbook, haga clic en **Guardar**.<br> ![Guardar runbook](media/automation-first-runbook-graphical/runbook-edit-toolbar-save.png)

## Paso 3: probar el runbook

Antes de que publiquemos el runbook para que esté disponible en producción, queremos probarlo para asegurarnos de que funciona correctamente. Cuando se prueba un runbook, se ejecuta su versión **Borrador** y se visualizan sus resultados de forma interactiva.

1.	Haga clic en **Panel Prueba** para abrir el panel Prueba.<br> ![Panel Prueba](media/automation-first-runbook-graphical/runbook-edit-toolbar-test-pane.png)
2.	Haga clic en **Iniciar** para iniciar la prueba. Esta debe ser la única opción habilitada.
3.	Se crea un [trabajo de runbook](automation-runbook-execution.md) y su estado se muestra en el panel. El estado del trabajo se iniciará como *En cola*, lo que indica que está esperando que haya disponible un trabajo de runbook en la nube. A continuación, pasará a *Iniciando* cuando un trabajador de runbook solicite el trabajo y, a continuación, a *En ejecución* cuando el runbook empiece a ejecutarse realmente.  
4.	Cuando se complete el trabajo del runbook, se mostrará su resultado. En nuestro caso, deberíamos ver *Hello World*.<br> ![Hello World](media/automation-first-runbook-graphical/test-output-hello-world.png)
5.	Cierre el panel Prueba para volver al lienzo.

## Paso 4: publicar e iniciar el runbook

El runbook que acabamos de crear aún está en modo borrador. Tenemos que publicarlo antes de que podamos ejecutarlo en producción. Al publicar un runbook, se sobrescribe la versión publicada existente con la versión de borrador. En nuestro caso, no tenemos una versión publicada aún porque acabamos de crear el runbook.

1.	Haga clic en **Publicar** para publicar el runbook y, a continuación, en **Sí** cuando se le solicite.<br> ![Publicar](media/automation-first-runbook-graphical/runbook-edit-toolbar-publish.png)
2.	Si se desplaza ahora a la izquierda para ver el runbook en el panel **Runbooks**, mostrará el **Estado de creación** **Publicado**.
3.	Desplácese hacia la derecha para ver el panel de **MyFirstRunbook**. Las opciones en la parte superior nos permiten iniciar el runbook, programarlo para que se inicie en algún momento en el futuro o crear un [webhook](automation-webhooks.md) para que se inicie a través de una llamada HTTP.
4.	Queremos iniciar el runbook, por lo que debe hacer clic en **Iniciar** y, a continuación, en **Sí** cuando se le solicite.<br> ![Iniciar runbook](media/automation-first-runbook-graphical/runbook-toolbar-start.png)
5.	Se abre un panel de trabajo para el trabajo de runbook que acabamos de crear. Podemos cerrar este panel, pero en este caso lo dejaremos abierto para que podamos ver el progreso del trabajo.
6.	El estado del trabajo se muestra en **Resumen de trabajos** y coincide con los estados que vimos cuando probamos el runbook.<br>![Resumen del trabajo](media/automation-first-runbook-graphical/job-pane-summary.png)
7.	Cuando el estado del runbook aparezca como *Completado*, haga clic en **Salida**. El panel **Salida** se abre y podemos ver *Hello World*.<br> ![Resumen del trabajo](media/automation-first-runbook-graphical/job-pane-output.png)  
8.	Cierre el panel Salida.
9.	Haga clic en **Secuencias** para abrir el panel Secuencias para el trabajo de runbook. Solo deberíamos ver *Hello World* en el flujo de salida, pero se pueden mostrar otras secuencias de un trabajo de runbook como Detallado y Error si el runbook escribe en ellas.<br> ![Resumen del trabajo](media/automation-first-runbook-graphical/job-pane-streams.png)
10.	Cierre el panel de secuencias y el panel de trabajo para volver al panel de MyFirstRunbook.
11.	Haga clic en **Trabajos** para abrir el panel Trabajos de este runbook. Enumera todos los trabajos creados por este runbook. Solo deberíamos ver un trabajo en la lista ya que solo ejecutamos el trabajo una vez.<br> ![Trabajos](media/automation-first-runbook-graphical/runbook-control-jobs.png)
12.	Puede hacer clic en esta tarea para abrir el mismo panel Trabajo que vimos cuando se inició el runbook. Esto permite volver atrás en el tiempo y ver los detalles de cualquier trabajo que se creó para un runbook determinado.

## Paso 5: agregar autenticación para administrar recursos de Azure

Hemos probado y publicado nuestro runbook, pero hasta ahora no hace nada útil. Queremos que administre recursos de Azure. Sin embargo, no podrá hacerlo aunque a menos que lo autentiquemos con las credenciales que se mencionan en los [requisitos previos](#prerequisites). Esto se hace con el cmdlet **Set-AzureAccount**.

1.	Abra el editor gráfico haciendo clic en **Editar** en el panel MyFirstRunbook.<br> ![Editar runbook](media/automation-first-runbook-graphical/runbook-toolbar-edit.png)
2.	Ya no necesitamos **Escribir Hello World en la salida**, así que haga clic en él y seleccione **Eliminar**.
3.	En el control Biblioteca, expanda **Cmdlets** y, a continuación, **Azure**.
4.	Agregue **Add-AzureAccount** al lienzo.<br> ![Add-AzureAccount](media/automation-first-runbook-graphical/add-azureaccount.png)
5.	Seleccione **Add-AzureAccount** y, a continuación, haga clic en **Parámetros** en el panel Configuración.
6.	**Add-AzureAccount** tiene varios conjuntos de parámetros, por lo que necesitamos seleccionar uno antes de poder proporcionar valores de los parámetros. Haga clic en **Conjunto de parámetros** y seleccione el conjunto de parámetros **Usuario**.
7.	Una vez seleccionado el conjunto de parámetros, los parámetros se muestran en el panel Configuración de parámetros de actividad. Haga clic en **Credencial**.<br> ![Agregar parámetros de la cuenta de Azure](media/automation-first-runbook-graphical/add-azureaccount-parameters.png)
8.	Queremos que este cmdlet use un recurso de credencial en nuestra cuenta de Automatización, por lo que seleccionaremos **Recurso de credencial** en **Origen de datos**.
9.	Seleccione el recurso de credencial que tiene acceso para iniciar y detener una máquina virtual en el entorno de Azure.<br> ![Agregar origen de datos de la cuenta de Azure](media/automation-first-runbook-graphical/credential-data-source.png)
10.	Haga clic en **Aceptar** dos veces para volver al lienzo.

## Paso 6: agregar una actividad para iniciar una máquina virtual

Ahora agregaremos una actividad **Start-AzureVM** para iniciar una máquina virtual. Puede seleccionar cualquier máquina virtual en su suscripción de Azure. Por ahora vamos a codificar ese nombre en el cmdlet.

1.	Expanda **Cmdlets** y, a continuación, **Azure**.
2.	Agregue **Start-AzureVM** al lienzo y haga clic y arrástrelo debajo de **Add-AzureAccount**.
3.	Mantenga el puntero sobre **Add-AzureAccount** hasta que aparezca un círculo en la parte inferior de la forma. Haga clic en el círculo y arrastre la flecha hasta **Start-AzureVM**. La flecha que acaba de crear es un *vínculo*. Se iniciará el runbook con **Add-AzureAccount** y, a continuación, deberá ejecutar **Start-AzureVM**.<br> ![Start-AzureVM](media/automation-first-runbook-graphical/start-azurevm.png)
4.	Seleccione **Start-AzureVM**. Haga clic en **Parámetros** y, a continuación, en **Conjunto de parámetros** para ver los conjuntos de **Start-AzureVM**. Seleccione el conjunto de parámetros **ByName**. Tenga en cuenta que **Name** y **ServiceName** tienen signos de exclamación junto a ellos. Esto indica que son parámetros necesarios.
5.	Seleccione **Name**. Use **Valor constante** para el **Origen de datos** y escriba el nombre de la máquina virtual que se iniciará. Haga clic en **OK**.
6.	Seleccione **ServiceName**. Use **Valor constante** para el **Origen de datos** y escriba el nombre de la máquina virtual que se iniciará. Haga clic en **Aceptar**.<br> ![Parámetros Start-AzureVM](media/automation-first-runbook-graphical/start-azurevm-params.png)
7.	Haga clic en el panel Prueba para que podemos probar el runbook.
8.	Haga clic en **Iniciar** para iniciar la prueba. Cuando haya terminado, compruebe que la máquina virtual se ha iniciado.

## Paso 7: agregar un parámetro de entrada al runbook

Actualmente, nuestro runbook inicia la máquina virtual que especificamos en el cmdlet **Start-AzureVM**, pero nuestro runbook sería más útil si pudiéramos especificar la máquina virtual cuando se inicia el runbook. Ahora agregaremos un parámetro de entrada para que el runbook proporcione esa funcionalidad.

1.	Abra el editor gráfico haciendo clic en **Editar** en el panel **MyFirstRunbook**.
2.	Haga clic en **Entrada y salida** y, a continuación, en **Agregar entrada** para abrir el panel Parámetros de entrada de runbook.<br> ![Entrada y salida de runbook](media/automation-first-runbook-graphical/runbook-edit-toolbar-inputoutput.png)
3.	Especifique *VMName* para el campo **Name**. Mantenga *string* en **Type** y cambie **Mandatory** a *Yes*. Haga clic en **OK**.
4.	Cree un segundo parámetro de entrada obligatorio denominado *VMServiceName* y, a continuación, haga clic en **Aceptar** para cerrar el panel **Entrada y salida**.<br> ![Parámetros de entrada de runbook](media/automation-first-runbook-graphical/input-parameters.png)
5.	Seleccione la actividad **Start-AzureVM** y, a continuación, haga clic en **Parámetros**.
6.	Cambie el **Origen de datos** para **Name** a **Entrada de runbook** y, a continuación, seleccione **VMName**.<br> ![Parámetro de nombre Start-AzureVM](media/automation-first-runbook-graphical/vmname-property.png)
7.	Cambie el **Origen de datos** para **ServiceName** a **Entrada de runbook** y, a continuación, seleccione **VMServiceName**.<br> ![Parámetros Start-AzureVM](media/automation-first-runbook-graphical/start-azurevm-params-inputs.png)
8.	Guarde el runbook y abra el panel Prueba. Tenga en cuenta que ahora puede proporcionar valores para las dos variables de entrada que se usarán en la prueba.
9.	Cierre el panel Prueba.
10.	Haga clic en **Publicar** para publicar la nueva versión del runbook.
11.	Detenga la máquina virtual que inició en el paso anterior.
12.	Haga clic en **Iniciar** para iniciar el runbook. Escriba el valor de **VMName** y **VMServiceName** para la máquina virtual que va a iniciar.<br>![Inicio del runbook](media/automation-first-runbook-graphical/start-runbook-input-params.png)
13.	Cuando se complete el runbook, compruebe que la máquina virtual se ha iniciado.

## Paso 8: crear un vínculo condicional

Ahora modificaremos el runbook para que solo intente iniciar el runbook si no se ha iniciado ya. Haremos esto agregando un cmdlet **Get-AzureVM** al runbook que incluirá el estado actual de la máquina virtual. A continuación, agregaremos un vínculo condicional que solo ejecutará **Start-AzureVM** si se detiene el estado de ejecución actual. Si no se detiene el runbook, genere un mensaje.

1.	Abra **MyFirstRunbook**en el editor gráfico.
2.	Quite el vínculo entre **Add-AzureAccount** y **Start-AzureVM** haciendo clic en él y, a continuación, presione la tecla *Eliminar*.
3.	En el control Biblioteca, expanda **Cmdlets** y, a continuación, **Azure**.
4.	Agregue **Get-AzureVM** al lienzo.
5.	Cree un vínculo desde **Add-AzureAccount** a **Get-AzureVM**. Cree otro vínculo desde **Get-AzureVM** a **Start-AzureVM**.<br> ![Runbook con Get-AzureVM](media/automation-first-runbook-graphical/get-azurevm.png)  
6.	Seleccione **Get-AzureVM** y haga clic en **Parámetros**. Seleccione el conjunto de parámetros *GetVMByServiceAndVMName*.
7.	Para **Name**, establezca el **Origen de datos** en **Entrada de runbook** y, a continuación, seleccione **VMName**.  
8.	Para **ServiceName**, establezca el **Origen de datos** en **Entrada de runbook**y, a continuación, seleccione **VMServiceName**.  
9.	Seleccione el vínculo entre **Get-AzureVM** y **Start-AzureVM**.
10.	En el panel Configuración, cambie **Aplicar condición** a **True**. Tenga en cuenta que el vínculo se convierte en una línea discontinua que indica que la actividad de destino solo se ejecutará si la condición se resuelve como true.
11.	En **Expresión de condición**, escriba *$ActivityOutput['Get-AzureVM'].PowerState -eq "Stopped"*. **Start-AzureVM** ahora solo se ejecutará si la máquina virtual se ha detenido.<br> ![Condición de vínculo](media/automation-first-runbook-graphical/link-condition.png)
12.	En el control Biblioteca, expanda **Cmdlets** y, a continuación, **Microsoft.PowerShell.Utility**.
13.	Agregue **Write-Output** al lienzo.
14.	Cree un vínculo desde **Get-AzureVM** a **Write-Output**.
15.	Seleccione el vínculo y cambie **Aplicar condición** a **True**.
16.	Para **Expresión de condición**, escriba *$ActivityOutput['Get-AzureVM'].PowerState -ne "Stopped"*. **Write-Output** ahora solo se ejecutará si no se detiene la máquina virtual.<br> ![Runbook con Write-Output](media/automation-first-runbook-graphical/write-output-link.png)
17.	Seleccione **Write-Output** y haga clic en **Parámetros**.
18.	Para **InputObject**, cambie **Origen de datos** a **Expresión de PowerShell** y escriba la expresión *"$VMName.Name ya iniciado."*.
19.	Guarde el runbook y abra el panel Prueba.
20.	Inicie el runbook con la máquina virtual detenida- Se debería iniciar.
21.	Inicie el runbook con la máquina virtual iniciada. Deberá obtener el resultado de que ya se ha iniciado.

## Artículos relacionados

-	[Creación gráfica en Automatización de Azure](automation-graphical-authoring-intro.md)
-	[Mi primer runbook de flujo de trabajo de PowerShell](automation-first-runbook-textual.md)
-	[Mi primer runbook de PowerShell](automation-first-runbook-textual-PowerShell.md)

<!---HONumber=AcomDC_0302_2016-->