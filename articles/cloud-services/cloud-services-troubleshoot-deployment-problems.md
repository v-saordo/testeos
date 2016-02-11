<properties
 pageTitle="Solución de problemas de implementación del servicio en la nube | Microsoft Azure"
 description="Hay algunos problemas comunes que pueden surgir al implementar un servicio en la nube en Azure. Este artículo proporciona soluciones a algunos de ellos."
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

# Cómo solucionar los problemas de implementación del servicio en la nube que pueda encontrarse

Al implementar un paquete de aplicación del servicio en la nube en Azure, puede obtener información sobre la implementación en el panel **Propiedades** del Portal de Azure. Puede usar los detalles de este panel para ayudarle a solucionar problemas con el servicio en la nube y proporcionar esta información al soporte técnico de Azure al abrir una nueva solicitud de soporte técnico.

Puede encontrar el panel **Propiedades** panel de la siguiente manera:

* En el Portal de Azure: haga clic en la implementación del servicio en la nube, haga clic en **Toda la configuración** y, por último, haga clic en **Propiedades**.
* En el Portal de Azure clásico: haga clic en la implementación del servicio en la nube y después en **PANEL**, que se encuentra en la esquina inferior derecha de la página (bajo **vista rápida**). Tenga en cuenta que no hay ningún texto "Propiedades" para este panel.

> [AZURE.NOTE] Puede copiar el contenido del panel Propiedades en el Portapapeles haciendo clic en el icono de la esquina superior derecha del panel.

## Póngase en contacto con el servicio de atención al cliente de Azure

Si necesita más ayuda en cualquier momento con este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure o de desbordamiento de pila](https://azure.microsoft.com/support/forums/).

Como alternativa, también puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte técnico**. Para obtener información sobre el uso del soporte técnico de Azure, lea las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](https://azure.microsoft.com/support/faq/).



## Problema: no puedo acceder a mi sitio web aunque la implementación haya iniciado y todas las instancias de rol están listas

El vínculo de la dirección URL del sitio web que se muestra en el portal no incluye el puerto. El puerto predeterminado para los sitios web es 80. Sin embargo, si la aplicación está configurada para ejecutarse en un otro puerto, debe agregar el puerto correcto a la dirección URL cuando acceda al sitio web.

1. En el Portal de Azure, haga clic en la implementación del servicio en la nube.
2. En el panel **Propiedades** del Portal de Azure, compruebe los puertos de las instancias de rol (en puntos de conexión de entrada).
3. Si el puerto no es *80*, agregue el valor de puerto correcto para la dirección URL cuando acceda a la aplicación. Para especificar un puerto no predeterminado, escriba la dirección URL, seguida de dos puntos (:) y seguidos del número de puerto sin espacios.

## Problema: mis instancias de rol se reinician sin que hiciera nada

La recuperación del servicio se produce automáticamente cuando Azure detecta nodos problemáticos y mueve las instancias de rol a nodos nuevos. Cuando esto ocurre, puede que vea que las instancias de rol se reinician automáticamente. Para averiguar si se produjo la recuperación del servicio:

1. En el Portal de Azure, haga clic en la implementación del servicio en la nube.
2. En el panel **Propiedades** del Portal de Azure, revise la información y determine si la recuperación del servicio se produjo durante el reinicio de los nodos.

Los roles también se reiniciarán aproximadamente una vez al mes durante las actualizaciones del sistema operativo de host y del sistema operativo invitado. Para obtener más información, consulte la entrada del blog [Role Instance Restarts Due to OS Upgrades](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx) (La instancia de rol se reinicia debido a actualizaciones del sistema operativo).

## Problema: No puedo realizar un intercambio de VIP y recibo un error

No se permite un intercambio de VIP si una actualización de implementación está en curso. Las actualizaciones de implementación pueden ocurrir automáticamente cuando:

* Está disponible un nuevo sistema operativo invitado y se han configurado las actualizaciones automáticas
* Se produce una recuperación de servicio

Para averiguar si la actualización automática impide la realización de un intercambio de VIP:

1. En el Portal de Azure, haga clic en la implementación del servicio en la nube.
2. En el panel **Propiedades** del Portal de Azure, busque el valor de **Estado**. Si es **Listo**, compruebe **Última operación** para ver si hubo recientemente alguna que pudo impedir el intercambio de VIP.
3. Repita los pasos 1 y 2 para la implementación de producción.
4. Si una actualización automática está en curso, espere a que finalice antes de intentar realizar el intercambio de VIP.

## Problema: Una instancia de rol entra en un bucle entre Iniciado, Inicializando, Ocupado y Detenido

Esta condición puede indicar un problema con el código de la aplicación, el paquete o el archivo de configuración. Si es true, podrá ver que el estado cambia cada pocos minutos y el Portal de Azure puede indicar algo como **Reciclando**, **Ocupado** o **Inicializando**. Esto indica que hay algún problema con la aplicación que impide que la instancia de rol se ejecute.

Para más información acerca de cómo solucionar este problema, consulte la entrada de blog [Azure PaaS Compute Diagnostics Data] (Datos de diagnóstico de proceso de PaaS de Azure) y [Problemas comunes que causan el reciclaje de los roles](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## Problema: Mi aplicación dejó de funcionar

1. En el Portal de Azure, haga clic en la instancia de rol.
2. En el panel **Propiedades** del Portal de Azure, tenga en cuenta las condiciones siguientes para resolver el problema:
   * Si la instancia de rol se detuvo recientemente (puede comprobar el valor de **Recuento de anulados**), la implementación puede estar actualizándose. Espere para ver si la instancia de rol reanuda el funcionamiento por sí misma.
   * Si la instancia de rol está ocupada, compruebe el código de aplicación para ver si se controla el evento [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck). Debe agregar o corregir el código que controla este evento.
   * Revise los datos de diagnóstico y los escenarios de solución de problemas en la entrada del blog [Azure PaaS Compute Diagnostics Data] (Datos de diagnóstico de proceso de PaaS de Azure).

>[AZURE.WARNING] Si reinicia el servicio en la nube, se restablecen las propiedades de la implementación, y se elimina eficazmente la información del problema original.

## Pasos siguientes

Vea más [artículos de solución de problemas](..\?tag=top-support-issue&service=cloud-services) para servicios en la nube.

Para más información acerca de cómo solucionar el problema de los roles de servicio en la nube mediante el uso de datos de diagnóstico de equipos de PaaS de Azure, consulte la [serie de blogs de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

<!---HONumber=AcomDC_0128_2016-->