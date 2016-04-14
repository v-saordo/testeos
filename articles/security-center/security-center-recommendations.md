<properties
   pageTitle="Administración de recomendaciones de seguridad en el Centro de seguridad de Azure | Microsoft Azure"
   description="Este documento explica cómo las recomendaciones del Centro de seguridad de Azure ayudan a proteger los recursos de Azure y a cumplir con las directivas de seguridad."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/23/2016"
   ms.author="terrylan"/>

# Administración de recomendaciones de seguridad en el Centro de seguridad de Azure

En este documento se explica cómo usar las recomendaciones del Centro de seguridad de Azure para proteger los recursos de Azure.

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure. En este documento se presenta el servicio mediante una implementación de ejemplo. No se trata de una guía paso a paso.

## ¿Qué es el Centro de seguridad de Azure?
El Centro de seguridad de Azure ayuda a evitar, detectar y responder a amenazas con más visibilidad y control de la seguridad en sus recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

## ¿Cuáles son las recomendaciones de seguridad?
El Centro de seguridad analiza periódicamente el estado de seguridad de los recursos de Azure. Cuando el Centro de seguridad identifica vulnerabilidades de seguridad potenciales, crea recomendaciones. Las recomendaciones le guían en el proceso de configuración de los controles necesarios.

## Implementación de recomendaciones de seguridad

### Obtención de recomendaciones

En [Establecimiento de directivas de seguridad en el Centro de seguridad de Azure](security-center-policies.md), aprenderá a:

- Configurar directivas de seguridad.
- Activar la recopilación de datos.
- Elegir las recomendaciones que verá como parte de la directiva de seguridad.

Las recomendaciones de directiva actuales se centran en las actualizaciones del sistema, las reglas de línea de base, los programas antimalware, las [ACL para puntos de conexión](../virtual-machines/virtual-machines-set-up-endpoints.md), los [grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md) en subredes y las interfaces de red, la auditoría de bases de datos SQL, el cifrado de datos transparente de bases de datos SQL y los firewalls de aplicaciones web. [Establecimiento de directivas de seguridad](security-center-policies.md) proporciona una descripción de cada opción de recomendación.

### Supervisión de recomendaciones
Después de establecer una directiva de seguridad, el Centro de seguridad analiza el estado de seguridad de los recursos, con el fin de identificar vulnerabilidades potenciales. El icono **Recomendaciones** de la hoja **Centro de seguridad** permite conocer el número total de recomendaciones que identifica el Centro de seguridad.

![][2]

Para ver los detalles de cada recomendación:

1. Haga clic en el **icono Recomendaciones** de la hoja **Centro de seguridad**. Se abrirá la hoja **Recomendaciones**.

Las recomendaciones aparecen en un formato de tabla, donde cada línea representa una recomendación determinada. Las columnas de esta tabla son:

- **DESCRIPCIÓN**: se explica la recomendación y lo que es preciso hacer para abordarla.
- **RECURSO**: enumera los recursos a los que se aplica la recomendación.
- **ESTADO**: describe el estado actual de la recomendación:
    - **Abierta**: la recomendación aún no se ha abordado.
    - **En curso**: la recomendación se aplica actualmente a los recursos y no se requiere ninguna acción por su parte.
    - **Resuelta**: se ha completado la recomendación (la línea aparecerá atenuada).
- **GRAVEDAD**: describe la gravedad de una recomendación concreta:
    - **Alta**: existe una vulnerabilidad en un recurso importante (como una aplicación, una VM o un grupo de seguridad de red) y requiere atención.
    - **Media**: existe una vulnerabilidad y se requieren pasos adicionales o no críticos para eliminarla o completar un proceso.
    - **Baja**: existe una vulnerabilidad que se debe abordar, pero no requiere una atención inmediata. (De manera predeterminada no se muestran las recomendaciones bajas, pero si desea verlas, puede filtrar por ellas).

Use la tabla siguiente como referencia para ayudarle a entender las recomendaciones disponibles y lo que hará cada uno si se aplica.

> [AZURE.NOTE] Querrá entender los [modelos de implementación clásico y del Administrador de recursos](../azure-classic-rm.md) para los recursos de Azure.

|Recomendación|Descripción|
|-----|-----|
|Habilitar la colección de datos para suscripciones/máquinas virtuales|Recomienda activar la recopilación de datos en la directiva de seguridad para cada una de las suscripciones o para las máquinas virtuales seleccionadas.|
|Resolver el error de coincidencia de reglas de línea base|Recomienda alinear las configuraciones del SO con las líneas base recomendadas; por ejemplo, no permitir guardar las contraseñas.|
|Aplicar actualizaciones del sistema|Recomienda implementar las actualizaciones críticas y de seguridad del sistema que falten en las máquinas virtuales (solo máquinas virtuales de Windows).|
|Configurar ACL para puntos de conexión|Recomienda configurar listas de control de acceso para restringir el acceso de entrada a las máquinas virtuales (solo clásicas).|
|[Agregar un firewall de aplicaciones web](security-center-add-web-application-firewall.md)|Recomienda implementar un Firewall de aplicaciones web (WAF) para los puntos de conexión web. El proceso de aprovisionamiento automático se basa en paquetes WAF (creados con el modelo de implementación del Administrador de recursos) que se implementan en una red virtual independiente. El acceso a las aplicaciones web protegidas en máquinas virtuales (clásicas) está restringido a los dispositivos WAF solo mediante NSG. Esta compatibilidad se extenderá a una implementación completamente personalizada de paquetes WAF (clásicos) en el futuro.|
|Finalizar la configuración del firewall de aplicaciones web|Para completar la configuración de un WAF, el tráfico se debe redirigir a la aplicación del WAF. Si se sigue esta recomendación, se completarán los cambios necesarios en la configuración.|
|[Habilitar antimalware](security-center-enable-antimalware.md)|Recomienda aprovisionar programas antimalware a las máquinas virtuales (solo máquinas virtuales de Windows).|
|Habilitar grupos de seguridad de red en interfaces de red/subredes|Recomienda habilitar grupos de seguridad de red (NSG) en subredes e interfaces de red (solo máquinas virtuales del Administrador de recursos).|
|Restringir el acceso a través de puntos de conexión externos públicos|Recomienda configurar reglas de tráfico de entrada para los NSG.|
|Habilitar Auditoría SQL de servidor|Recomienda activar la auditoría de servidores SQL Server de Azure (solo el servicio SQL de Azure; no incluye la instancia SQL que se ejecuta en sus máquinas virtuales).|
|Habilitar Auditoría SQL de base de datos|Recomienda activar la auditoría de bases de datos SQL de Azure (solo el servicio SQL de Azure; no incluye la instancia SQL que se ejecuta en sus máquinas virtuales).|
|Habilitar Cifrado de datos transparente en bases de datos SQL|Recomienda habilitar el cifrado de bases de datos SQL (solo el servicio SQL de Azure).|
|Implementar el Agente de VM|Permite ver las VM que requieren el Agente de VM. El agente de máquina virtual debe estar instalado en las máquinas virtuales para aprovisionar la detección de revisiones, la detección de línea de base y los programas antimalware. De manera predeterminada, el agente de máquina virtual está instalado en las máquinas virtuales que se implementan desde Azure Marketplace. El artículo [VM Agent and Extensions – Part 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) (Agente de VM y extensiones, parte 2) proporciona información sobre cómo instalar el Agente de VM.|
| [Aplicar cifrado de discos](security-center-apply-disk-encryption.md) |Se recomienda cifrar los discos de la máquina virtual mediante Cifrado de discos de Azure (máquinas virtuales Linux y Windows). Se recomienda cifrar tanto los volúmenes de datos como los del sistema operativo en la máquina virtual.|

Puede filtrar y descartar las recomendaciones.

1. Haga clic en **filtro** en la hoja **Recomendaciones**. Se abrirá la hoja **Filtro**, donde podrá seleccionar los valores de gravedad y de estado que desee ver.

    ![][3]

2. Si determina que una recomendación no corresponde, puede descartar la recomendación y, luego, filtrarla para que no aparezca en la vista. Hay dos formas de descartar una recomendación. Una forma es hacer clic con el botón derecho en un elemento y seleccionar **Descartar**. La otra es mover el puntero sobre un elemento, hacer clic en los tres puntos que aparecen a la derecha y seleccionar **Descartar**. Para ver las recomendaciones descartadas, haga clic en **Filtro** y seleccione **Descartadas**.

    ![][4]

### Aplicación de recomendaciones
Después de revisar todas las recomendaciones, decida cuáles son las primeras que debe aplicar. Se recomienda usar la clasificación de gravedad como parámetro principal para evaluar las recomendaciones que se deben aplicar primero. Con la recomendación **Habilitar antimalware**, examinaremos un ejemplo sobre cómo aplicar una recomendación.

1. En la hoja **Recomendaciones**, seleccione **Habilitar antimalware**. ![][5]

2. En la hoja **Instalar antimalware**, seleccione desde la lista de máquinas virtuales sin antimalware habilitado y haga clic en **Instalar antimalware**.
3. Se abre la hoja **Nuevo recurso** que le permite seleccionar la solución antimalware que desea usar. Seleccione **Microsoft Antimalware**.
4. Aparece información adicional sobre la solución antimalware. Seleccione **Crear**.
5. Escriba las opciones de configuración requeridas en la hoja **Agregar extensión** y seleccione **Aceptar**. ![][6]

[Microsoft Antimalware](../azure-security-antimalware.md) ahora está activo en la máquina virtual seleccionada.


## Pasos siguientes
En este documento, se han presentando las recomendaciones de seguridad del Centro de seguridad. Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

- [Establecimiento de directivas de seguridad en el Centro de seguridad de Azure](security-center-policies.md): aprenda a configurar directivas de seguridad.
- [Supervisión del estado de seguridad en el Centro de seguridad de Azure](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administración de alertas de seguridad y respuesta a estas en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar alertas de seguridad y responder a estas.
- [Preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md): encuentre las preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!--Image references-->
[2]: ./media/security-center-recommendations/recommendations-tile.png
[3]: ./media/security-center-recommendations/filter-recommendations.png
[4]: ./media/security-center-recommendations/dismiss-recommendations.png
[5]: ./media/security-center-recommendations/select-enable-antimalware.png
[6]: ./media/security-center-recommendations/install-antimalware.png

<!---HONumber=AcomDC_0224_2016-->