<properties
	pageTitle="Galerías de runbooks y módulos para la automatización de Azure | Microsoft Azure"
	description="Dispone de runbooks y módulos de Microsoft y la comunidad para instalarlos y usarlos en su entorno de Automatización de Azure. En este artículo, se describe cómo acceder a estos recursos y contribuir sus runbooks a la galería."
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
	ms.date="03/02/2016"
	ms.author="magoedte;bwren" />


# Galerías de runbooks y módulos para Automatización de Azure

En lugar de crear sus propios runbooks y módulos en Automatización de Azure, dispone de acceso a diversas soluciones ya compiladas por Microsoft y la comunidad. Puede usar estas soluciones sin modificaciones o como punto de partida para modificarlas según sus requisitos específicos.

Puede obtener runbooks en la [Galería de runbooks](#runbooks-in-runbook-gallery) y módulos en la [Galería de PowerShell](#modules-in-powerShell-gallery). También puede contribuir a la comunidad si comparte las soluciones que desarrolle.

## Runbooks en la Galería de runbooks

La [Galería de runbooks](http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=RootCategory&f[0].Value=WindowsAzure&f[1].Type=SubCategory&f[1].Value=WindowsAzure_automation&f[1].Text=Automation) ofrece diversos runbooks de Microsoft y la comunidad que se pueden importar a Automatización de Azure. Puede descargar un Runbook de la galería que se hospeda en el [Centro de scripts de TechNet](http://gallery.technet.microsoft.com/) o puede importar Runbooks directamente desde la galería del Portal de Azure (o desde su versión clásica).

Solo se puede importar directamente desde la Galería de Runbooks mediante el Portal de Azure (o su versión clásica). No se puede llevar a cabo esta función mediante Windows PowerShell.

>[AZURE.NOTE] Debería validar el contenido de los runbooks que obtenga de la Galería de runbooks y extremar el cuidado al instalarlos y ejecutarlos en un entorno de producción.|

### Para importar un Runbook desde la Galería de Runbooks con el Portal de Azure clásico

1. En el Portal de administración de Azure, haga clic en **Nuevo**, **Servicios de aplicaciones**, **Automatización**, **Runbook**, **De la galería**.
2. Seleccione una categoría para ver runbooks relacionados y seleccione uno para ver sus detalles. Cuando seleccione el Runbook que desee, haga clic en el botón de flecha derecha.

    ![Galería de runbooks](media/automation-runbook-gallery/runbook-gallery.png)

3. Revise el contenido del runbook y tenga en cuenta los requisitos de la descripción. Haga clic en el botón de flecha derecha cuando termine.
4. Escriba los detalles del runbook y haga clic en el botón de marca de verificación. El nombre del runbook ya estará rellenado.
5. Aparecerá el runbook en la pestaña **Runbooks** de la cuenta de automatización.

### Para importar un runbook de la Galería de runbooks con el Portal de Azure

1. En el Portal de Azure, abra su cuenta de Automatización.
2. Haga clic en el icono **Runbooks** para abrir la lista de runbooks.
3. Haga clic en el botón **Examinar galería**.

    ![Botón Examinar galería](media/automation-runbook-gallery/browse-gallery-button.png)

4. Busque el elemento de la galería que desee y selecciónelo para ver sus detalles.

    ![Examinar galería](media/automation-runbook-gallery/browse-gallery.png)

4. Haga clic en **Ver proyecto de origen** para ver el elemento en el [Centro de scripts de TechNet](http://gallery.technet.microsoft.com/).
5. Para importar un elemento, haga clic en él para ver sus detalles y después haga clic en el botón **Importar**.

    ![Botón Importar](media/automation-runbook-gallery/gallery-item-detail.png)

6. Si lo desea, cambie el nombre del runbook y después haga clic en **Aceptar** para importarlo.
5. Aparecerá el runbook en la pestaña **Runbooks** de la cuenta de automatización.


### Adición de un runbook a la Galería de runbooks

Microsoft recomienda agregar a la Galería de Runbooks aquellos runbooks que piense que podrían ser útiles para otros clientes. Puede agregar un runbook si [lo carga al Centro de scripts](http://gallery.technet.microsoft.com/site/upload) teniendo en cuenta los siguientes detalles.

- Debe especificar *Windows Azure* como **categoría** y *Automatización* como **subcategoría** para el runbook que se mostrará en el asistente.  

- Se debe cargar un único archivo. ps1 o .graphrunbook. Si el runbook requiere módulos, runbooks secundarios o recursos, debe enumerarlos en la descripción del envío y en la sección de comentarios del runbook. Si tiene una solución que requiere varios runbooks, cargue cada uno por separado e indique los nombres de los runbooks relacionados en cada una de sus descripciones. Asegúrese de usar las mismas etiquetas para que se muestren en la misma categoría. Un usuario tendrá que leer la descripción para saber que se requieren otros runbooks para que funcione la solución.

- Inserte un fragmento de código de Flujo de trabajo de PowerShell o PowerShell en la descripción con el icono **Insertar sección de código**.

- El resumen de la descarga se mostrará en los resultados de la Galería de runbooks, por lo que debería proporcionar información detallada que ayude a un usuario a identificar la funcionalidad del runbook.

- Debería asignar entre una y tres de las etiquetas siguientes a la carga. El runbook se mostrará en el asistente en las categorías que coincidan con sus etiquetas. El asistente pasará por alto las etiquetas que no aparezcan en esta lista. Si no especifica ninguna etiqueta coincidente, el runbook se incluirá en la categoría Otros.

 - Copia de seguridad
 - Administración de la capacidad
 - Control de cambios
 - Cumplimiento normativo
 - Desarrollo y entornos de prueba
 - Recuperación ante desastres
 - Supervisión
 - Aplicación de revisiones
 - Aprovisionamiento
 - Corrección
 - Administración del ciclo de vida de VM


- Automatización actualiza la galería una vez a la hora, por lo que no verá sus contribuciones de inmediato.

## Módulos en la Galería de PowerShell

Los módulos de PowerShell contienen cmdlets que puede usar en sus runbooks; los módulos existentes que se pueden instalar en Automatización de Azure están disponibles en la [Galería de PowerShell](http://www.powershellgallery.com). Puede iniciar esta galería desde el Portal de Azure e instalarlos directamente en Automatización de Azure o puede descargarlos e instalarlos de forma manual. No puede instalar los módulos directamente desde el Portal de Azure clásico, pero puede descargarlos e instalarlos como cualquier otro módulo.

### Para importar un módulo de la Galería de módulos de automatización con el Portal de Azure

1. En el Portal de Azure, abra su cuenta de Automatización.
2. Haga clic en el icono **Recursos** para abrir la lista de recursos.
3. Haga clic en el icono **Módulos** para abrir la lista de módulos.
4. Haga clic en el botón **Examinar galería**. Aparece la hoja Examinar galería.

    ![Galería de módulos](media/automation-runbook-gallery/modules-blade.png) <br>
5. Cuando haya aparecido la hoja Examinar galería, puede buscar por los campos siguientes:

   - Nombre de módulo
   - Etiquetas
   - Autor
   - Nombre de cmdlet/recurso de DSC

6. Busque un módulo que le interese y selecciónelo para ver sus detalles. Cuando explore un módulo específico, podrá ver más información sobre el módulo, incluido un vínculo a la Galería de PowerShell, las dependencias necesarias y todos los cmdlets o recursos de DSC que contiene el módulo.

    ![Detalles del módulo de PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png) <br>

7. Para instalar el módulo directamente en Automatización de Azure, haga clic en el botón **Importar**.

    ![Botón de importación de módulo](media/automation-runbook-gallery/module-import-button.png)

8. Al hacer clic en el botón Importar, verá el nombre del módulo que se va a importar. Si están instaladas todas las dependencias, el botón **Aceptar** estará activo. Si faltan dependencias, debe importarlas antes de importar este módulo.
9. Haga clic en **Aceptar** para importar el módulo y aparecerá la hoja del módulo. Cuando Automatización de Azure importa un módulo en la cuenta, extrae metadatos sobre el módulo y los cmdlets.

    ![Hoja de importación de módulo](media/automation-runbook-gallery/module-import-blade.png)

    Esto puede tardar un par de minutos, ya que cada actividad debe extraerse.
10. Recibirá una notificación de que se va a implementar el módulo y otra cuando se haya completado.
11. Una vez importado el módulo, verá las actividades disponibles y podrá usar sus recursos en los runbooks y en la Configuración de estado deseado.

## Solicitud de un runbook o módulo

Puede enviar solicitudes a [Voz de usuario](https://feedback.azure.com/forums/246290-azure-automation/). Si necesita ayuda para escribir un runbook o se plantea preguntas acerca de PowerShell, publique una pregunta en nuestro [foro](http://social.msdn.microsoft.com/Forums/windowsazure/es-ES/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## Artículos relacionados

- [Creación o importación de un runbook en Automatización de Azure](automation-creating-importing-runbook.md)
- [Aprendizaje del flujo de trabajo de Windows PowerShell](automation-powershell-workflow.md)

<!---HONumber=AcomDC_0302_2016-->