<properties 
   pageTitle="Tipos de runbooks de Automatización de Azure"
   description="Describe los distintos tipos de runbooks que puede usar en la Automatización de Azure y las consideraciones que debe tener en cuenta al determinar qué tipo usar."
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
   ms.date="02/09/2016"
   ms.author="bwren" />

# Tipos de runbooks de Automatización de Azure

Automatización de Azure admite tres tipos de runbooks que se describen brevemente en la tabla siguiente. Las secciones siguientes proporcionan información adicional sobre cada tipo, incluidas consideraciones sobre cuándo usar cada uno.


| Escriba | Descripción |
|:---|:---|
| [Gráfico](#graphical-runbooks) | Basado en el flujo de trabajo de Windows PowerShell y creado y editado completamente en el editor gráfico del Portal de Azure. | 
| [Flujo de trabajo de PowerShell](#powershell-workflow-runbooks) | Runbook de texto basado en el flujo de trabajo de Windows PowerShell. |
| [PowerShell](#powershell-runbooks) | Runbook de texto basado en el script de Windows PowerShell. |

## Runbooks gráficos

Los [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) se crean y modifican con el editor gráfico en el Portal de Azure. Puede exportarlos a un archivo y después importarlos en otra cuenta de automatización, pero no puede crearlos ni editarlos con otra herramienta. Los runbooks gráficos generan código del flujo de trabajo de PowerShell, pero no se puede ver ni modificar el código directamente. Los runbooks gráficos no se pueden convertir a uno de los [formatos de texto](automation-runbook-types.md), ni puede convertirse un runbook de texto a un formato gráfico.

### Ventajas

- Cree runbooks con conocimientos mínimos del [flujo de trabajo de PowerShell](automation-powershell-workflow.md).
- Represente visualmente los procesos de administración.
- Use los [puntos de control](automation-powershell-workflow.md#checkpoints) para reanudar el runbook en caso de error.
- Use el [procesamiento paralelo](automation-powershell-workflow.md#parallel-processing) para realizar varias actividades en paralelo.
- Se pueden incluir otros runbooks gráficos o del flujo de trabajo de PowerShell como runbooks secundarios para crear flujos de trabajo de alto nivel.


### Limitaciones

- No se pueden editar los runbooks fuera del Portal de Azure.
- Puede requerir el [control de un script de flujo de trabajo](automation-powershell-workflow.md#activities) que contenga el código del flujo de trabajo de PowerShell para llevar a cabo la lógica compleja.
- No se puede ver ni modificar directamente el código del flujo de trabajo de PowerShell creado por el flujo de trabajo gráfico. Tenga en cuenta que puede ver el código en cualquier actividad de script de flujo de trabajo.
- El runbook tarda más en iniciarse que los runbooks de PowerShell ya que deben compilarse antes de su ejecución.
- Los runbooks de PowerShell pueden incluirse únicamente como runbooks secundarios mediante el cmdlet Start-AzureAutomationRunbook, que crea un nuevo trabajo.


## Runbooks del flujo de trabajo de PowerShell

Los runbooks del flujo de trabajo de PowerShell son runbooks de texto basados en el [flujo de trabajo de Windows PowerShell](automation-powershell-workflow.md). Puede modificar directamente el código del runbook con el editor de texto en el Portal de Azure. También puede usar cualquier editor de texto sin conexión e [importar el runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) en Automatización de Azure.

### Ventajas

- Implemente toda la lógica compleja con código del flujo de trabajo de PowerShell.
- Use los [puntos de control](automation-powershell-workflow.md#checkpoints) para reanudar el runbook en caso de error.
- Use el [procesamiento paralelo](automation-powershell-workflow.md#parallel-processing) para realizar varias acciones en paralelo.
- Se pueden incluir otros runbooks gráficos o del flujo de trabajo de PowerShell como runbooks secundarios para crear flujos de trabajo de alto nivel.


### Limitaciones

- El autor debe estar familiarizado con el flujo de trabajo de PowerShell.
- El runbook debe tratar la complejidad adicional del flujo de trabajo de PowerShell como [objetos deserializados](automation-powershell-workflow.md#code-changes).
- El runbook tarda más en iniciarse que los runbooks de PowerShell ya que deben compilarse antes de su ejecución.
- Los runbooks de PowerShell pueden incluirse únicamente como runbooks secundarios mediante el cmdlet Start-AzureAutomationRunbook, que crea un nuevo trabajo.


## Runbooks de PowerShell

Los runbooks de PowerShell están basados en Windows PowerShell. Puede modificar directamente el código del runbook con el editor de texto en el Portal de Azure. También puede usar cualquier editor de texto sin conexión e [importar el runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) en Automatización de Azure.

### Ventajas

- Implemente toda la lógica compleja con código de PowerShell sin las complejidades adicionales de flujo de trabajo de PowerShell. 
- El runbook se inicia con más rapidez que los runbooks gráficos o de flujo de trabajo de PowerShell, ya que no es necesario compilarlos antes de la ejecución.

### Limitaciones

- Debe estar familiarizado con el scripting de PowerShell.
- No puede usar el [procesamiento paralelo](automation-powershell-workflow.md#parallel-processing) para realizar varias acciones en paralelo.
- No puede usar los [puntos de control](automation-powershell-workflow.md#checkpoints) para reanudar el runbook en caso de error.
- Los runbooks gráficos y de flujo de trabajo de PowerShell pueden incluirse únicamente como runbooks secundarios mediante el cmdlet Start-AzureAutomationRunbook, que crea un nuevo trabajo.

### Problemas conocidos
A continuación se describen los problemas conocidos actuales con runbooks de PowerShell.

- Los runbooks de PowerShell no pueden recuperar un [activo de variables](automation-variables.md) sin cifrar con un valor null.
- Los runbooks de PowerShell no pueden recuperar un [activo de variables](automation-variables.md) con *~* en el nombre.
- Get-Process en un bucle de un runbook de PowerShell puede bloquearse después de más de 80 iteraciones. 
- Un runbook de PowerShell puede producir un error si se intenta escribir una gran cantidad de datos en el flujo de salida a la vez. Se puede evitar este problema si se genera únicamente la información que se necesita al trabajar con objetos grandes. Por ejemplo, en lugar de generar un elemento *Get-Process*, puede generar simplemente los campos obligatorios con *Get-Process | Select ProcessName, CPU*.

## Consideraciones

Se deben tener en cuenta las siguientes consideraciones adicionales al determinar qué tipo usar para un runbook determinado.

- No se pueden convertir runbooks de un tipo a otro.
- Existen limitaciones al usar runbooks de diferentes tipos como runbooks secundarios. Consulte [Runbooks secundarios en la Automatización de Azure](automation-child-runbooks.md) para obtener más información.



  
## Artículos relacionados

- [Creación gráfica en Automatización de Azure](automation-graphical-authoring-intro.md)
- [Aprendizaje del flujo de trabajo de Windows PowerShell](automation-powershell-workflow.md)
- [Creación o importación de un runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx)

<!---HONumber=AcomDC_0211_2016-->