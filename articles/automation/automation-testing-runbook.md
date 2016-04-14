<properties 
	pageTitle="Prueba de un runbook en Automatización de Azure"
	description="Antes de publicar un runbook en Automatización de Azure, puede probarlo para asegurarse de que funciona según lo previsto. En este artículo se describe cómo probar un runbook y ver su salida."
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
	ms.author="magoedte;bwren" />

# Prueba de un runbook en Automatización de Azure
Cuando se prueba un runbook, se ejecuta la [versión de borrador](automation-creating-importing-runbook.md#publishing-a-runbook) y se completan todas las acciones que realiza. No se crea ningún historial de trabajos, pero los flujos de [salida](automation-runbook-output-and-messages.md#output-stream) así como de [error y advertencia](automation-runbook-output-and-messages.md#message-streams) se muestran en el panel de salida de la prueba. Los mensajes del [flujo detallado](automation-runbook-output-and-messages.md#message-streams) solo se muestran en el panel de salida si la [variable $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) está establecida en Continue.

Aun cuando se ejecuta la versión de borrador, el runbook ejecuta el flujo de trabajo con normalidad y realiza las acciones correspondientes en los recursos del entorno. Por este motivo, solo debe probar runbooks en recursos no pertenecientes a entornos de producción.

El procedimiento para probar cada [tipo de Runbook](automation-runbook-types.md) es el mismo, y no hay diferencias entre realizar las pruebas en el editor de texto o en el editor gráfico del Portal de Azure.


## Para probar un runbook en el portal de Azure

Puede trabajar con cualquier [tipo de Runbook](automation-runbook-types.md) en el Portal de Azure.

1. Abra la versión de borrador del runbook en el [editor de texto](automation-editing-a-runbook#Portal) o el [editor gráfico](automation-graphical-authoring-intro.md).
2. Haga clic en el botón **Probar** para abrir la hoja Prueba.
3. Si el runbook tiene parámetros, se enumerarán en el panel izquierdo, donde puede proporcionar los valores que se usarán para la prueba.
4. Si quiere ejecutar la prueba en un [Hybrid Runbook Worker](automation-hybrid), cambie **Configuración de ejecución** a **Hybrid Worker** y seleccione el nombre del grupo de destino. De lo contrario, mantenga el valor predeterminado **Azure** para ejecutar la prueba en la nube.
5. Haga clic en el botón **Iniciar** para iniciar la prueba.
6. Si el runbook es de tipo [Flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) o [Gráfico](automation-runbook-types.md#graphical-runbooks), puede detenerlo o suspenderlo durante la prueba con los botones situados bajo el panel de salida. Cuando se suspende el runbook, este completa la actividad que está realizando en ese momento antes de suspenderse. Una vez suspendido, puede detener o reiniciar el runbook.
7. Revise la salida del runbook en el panel de salida.



## Para probar un runbook en el portal de Azure

En el portal de Azure solo se puede trabajar con [runbooks de Flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).


1. [Abra la versión de borrador del runbook](automation-edit-textual-runbook.md#to-edit-a-runbook-with-the-azure-portal).
2. Haga clic en el botón **Probar** para iniciar la prueba. Si el runbook tiene parámetros, aparece un cuadro de diálogo para proporcionar los valores que se usarán para la prueba.
6. Puede detener o suspender el runbook durante la prueba con los botones situados bajo el panel de salida. Cuando se suspende el runbook, este completa la actividad que está realizando en ese momento antes de suspenderse. Una vez suspendido, puede detener o reiniciar el runbook.
7. Revise la salida del runbook en el panel de salida.


## Artículos relacionados

- [Creación o importación de un runbook en Automatización de Azure](automation-creating-importing-runbook.md)
- [Runbooks gráficos en Automatización de Azure](automation-graphical-authoring-intro.md)
- [Edición de runbooks de texto en Automatización de Azure](automation-edit-textual-runbook.md)
- [Salidas de runbook y mensajes en la Automatización de Azure](automation-runbook-output-and-messages.md)

<!---HONumber=AcomDC_0302_2016-->