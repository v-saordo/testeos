<properties
 pageTitle="Solución de problemas de implementación de servicios en la nube | Microsoft Azure"
 description="Hay varios problemas comunes que pueden surgir al implementar servicios en la nube en Azure. Este artículo proporciona soluciones a algunos de ellos."
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="01/20/2016"
   ms.author="daleche" />

# Solución de problemas de implementación de servicios en la nube

Al implementar un paquete de aplicación del servicio en la nube en Azure, puede obtener información sobre la implementación en el panel **Propiedades** del Portal de Azure. Puede usar los detalles de este panel para ayudarle a solucionar problemas con el servicio en la nube y proporcionar esta información al soporte técnico de Azure al abrir una nueva solicitud de soporte técnico.

Puede encontrar el panel **Propiedades** panel de la siguiente manera:

* En el Portal de Azure, haga clic en la implementación del servicio en la nube, haga clic en **Toda la configuración** y después en **Propiedades**.
* En el Portal de Azure clásico, haga clic en la implementación del servicio en la nube y después en **PANEL**, que se encuentra en la esquina inferior derecha de la página (debajo de **vista rápida**). Tenga en cuenta que en este panel no hay ninguna etiqueta "Propiedades".

> [AZURE.NOTE] El contenido del panel **Propiedades** se puede copiar en el Portapapeles. Para ello, es preciso hacer clic en el icono de la esquina superior derecha del panel.

## Póngase en contacto con el servicio de atención al cliente de Azure

Si necesita más ayuda en cualquier momento con este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure o de desbordamiento de pila](https://azure.microsoft.com/support/forums/).

Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](http://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte**. Para obtener información sobre el uso del soporte técnico de Azure, lea las [Preguntas más frecuentes del soporte técnico de Microsoft Azure](http://azure.microsoft.com/support/faq/).

## Problema: no puedo acceder a mi sitio web, pero la implementación se ha iniciado y todas las instancias de rol están listas

El vínculo de la dirección URL del sitio web que se muestra en el portal no incluye el puerto. El puerto predeterminado para los sitios web es 80. Si la aplicación está configurada para ejecutarse en otro puerto, debe agregar el puerto correcto a la dirección URL al acceder al sitio web.

1. En el Portal de Azure, haga clic en la implementación del servicio en la nube.
2. En el panel **Propiedades** del Portal de Azure, compruebe los puertos de las instancias de rol (en **Puntos de conexión de entrada**).
3. Si el puerto no es el 80, agregue el valor de puerto correcto para la dirección URL al acceder a la aplicación. Para especificar un puerto no predeterminado, escriba la dirección URL, seguida de dos puntos (:) y del número de puerto, sin espacios.

## Problema: mis instancias de rol se reciclan automáticamente

Se produce automáticamente una recuperación del servicio cuando Azure detecta nodos con problemas y, por consiguiente, mueve instancias de rol a nodos nuevos. Cuando esto ocurre, puede que vea que las instancias de rol se reciclan automáticamente. Para averiguar si se produjo la recuperación del servicio:

1. En el Portal de Azure, haga clic en la implementación del servicio en la nube.
2. En el panel **Propiedades** del Portal de Azure, revise la información y determine si la recuperación del servicio se produjo mientras observaba el reciclaje de roles.

Los roles también se reciclarán aproximadamente una vez al mes durante las actualizaciones del sistema operativo del host y del sistema operativo invitado. Para obtener más información, consulte la entrada del blog [Role Instance Restarts Due to OS Upgrades](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx) (La instancia de rol se reinicia debido a actualizaciones del sistema operativo).

## Problema: No puedo realizar un intercambio de VIP y recibo un error

No se permite un intercambio de VIP si una actualización de implementación está en curso. Las actualizaciones de implementación pueden ocurrir automáticamente cuando:

* Hay un nuevo sistema operativo invitado y ha configurado actualizaciones automáticas
* Se produce una recuperación de servicio.

Para averiguar si alguna actualización automática impide la realización de un intercambio de VIP:

1. En el Portal de Azure, haga clic en la implementación del servicio en la nube.
2. En el panel **Propiedades** del Portal de Azure, examine el valor de **Estado**. Si es **Listo**, compruebe **Última operación** para ver si hubo recientemente alguna que pudo impedir el intercambio de VIP.
3. Repita los pasos 1 y 2 para la implementación de producción.
4. Si una actualización automática está en curso, espere a que finalice antes de intentar realizar el intercambio de VIP.

## Problema: Una instancia de rol entra en un bucle entre Iniciado, Inicializando, Ocupado y Detenido

Esta condición puede indicar un problema con el código de la aplicación, el paquete o el archivo de configuración. En ese caso, podría ver que el estado cambia cada pocos minutos y en el Portal de Azure puede aparecer algo como **Reciclando**, **Ocupado** o **Inicializando**. Esto indica que hay algún problema con la aplicación que impide que la instancia de rol se ejecute.

Para más información acerca de cómo solucionar este problema, consulte la entrada de blog [Azure PaaS Compute Diagnostics Data](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) (Datos de diagnóstico de proceso de PaaS de Azure) y [Problemas comunes que causan el reciclaje de los roles](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## Problema: Mi aplicación dejó de funcionar

1. En el Portal de Azure, haga clic en la instancia de rol.
2. En el panel **Propiedades** del Portal de Azure, tenga en cuenta las condiciones siguientes para resolver el problema:
   * Si la instancia de rol se detuvo recientemente (puede comprobar el valor de **Recuento de anulados**), la implementación puede estar actualizándose. Espere para ver si la instancia de rol reanuda el funcionamiento por sí misma.
   * Si la instancia de rol está en estado [Ocupado](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck), compruebe el código de aplicación para ver si se controla el evento **StatusCheck**. Debe agregar o corregir el código que controla este evento.
   * Revise los datos de diagnóstico y los escenarios de solución de problemas en la entrada del blog [Azure PaaS Compute Diagnostics Data](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) (Datos de diagnóstico de proceso de PaaS de Azure).

>[AZURE.WARNING] Si recicla el servicio en la nube, se restablecen las propiedades de la implementación, con lo que se elimina eficazmente la información del problema original.

## Pasos siguientes

Vea más [artículos de solución de problemas](..\?tag=top-support-issue&service=cloud-services) para servicios en la nube.

Para más información acerca de cómo solucionar los problemas de los roles de los servicios en la nube mediante el uso de datos de diagnóstico de equipos de PaaS de Azure, consulte la [serie de blogs de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

<!---HONumber=AcomDC_0204_2016-->