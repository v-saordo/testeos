<properties
   pageTitle="Adición de un firewall de aplicaciones web en el Centro de seguridad de Azure | Microsoft Azure"
   description="En este documento, se muestra cómo implementar la recomendación **Agregar un firewall de aplicaciones web** del Centro de seguridad de Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/23/2016"
   ms.author="terrylan"/>

# Adición de un firewall de aplicaciones web en el Centro de seguridad de Azure

El Centro de seguridad de Azure puede recomendarle agregar Firewall de aplicaciones web (WAF) de un partner de Microsoft para proteger las aplicaciones web. Este documento le ofrece un ejemplo de cómo hacerlo:

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure. En este documento se presenta el servicio mediante una implementación de ejemplo. No se trata de una guía paso a paso.

## Implementación de la recomendación

1. En la hoja **Recomendaciones**, seleccione **Proteger la aplicación web con Firewall de aplicaciones web**. ![][1].

2. En la hoja **Proteger la aplicación web con Firewall de aplicaciones web**, seleccione una aplicación web. Se abre la hoja **Agregar Firewall de aplicaciones web**. ![][2]
3. Puede elegir usar un firewall de aplicaciones web existentes, si está disponible, o puede crear uno nuevo. En este ejemplo no hay ningún WAF existente, por lo que vamos a crear uno nuevo.

4. Para crear un nuevo WAF, seleccione una solución de la lista de partners integrados. En ese ejemplo, seleccionaremos **Firewall de aplicaciones web de Barracuda**.
5. Se abrirá la hoja **Firewall de aplicaciones web de Barracuda**, que le proporcionará información sobre la solución del partner. Seleccione **Crear** en la hoja de información. ![][3]

6. Se abrirá la hoja **Nuevo Firewall de aplicaciones web** donde puede realizar los pasos para la **Configuración de VM** y brindar **Información sobre el WAF**. Seleccione **Configuración de VM**.

7. En la hoja **Configuración de VM**, se ingresa la información requerida para poner en marcha la máquina virtual que ejecutará el WAF. ![][4]
8. Vuelva a la hoja **Nuevo Firewall de aplicaciones web** y seleccione **Información de WAF**. En la hoja **Información de WAF**, configure el WAF. El paso 7 le permite configurar la máquina virtual en que se ejecutará el WAF y el paso 8 le permite aprovisionar el WAF.

9. Vuelva a la hoja **Recomendaciones**. Después de crear el WAF se generó una entrada nueva, llamada **Finalizar la configuración del Firewall de aplicaciones web**. Dicha le permite saber qué se necesita para completar el proceso de conectar el WAF dentro de la Red virtual de Azure, con el fin de que pueda proteger la aplicación. ![][5]

10. Seleccione **Finalizar la configuración del Firewall de aplicaciones web**. Se abre una nueva hoja. En ella puede ver que hay una aplicación web que necesita que su tráfico se vuelva a enrutar.
11. Seleccione la aplicación web. Se abre una hoja en la que encontrará los pasos necesarios para finalizar la configuración del Firewall de aplicaciones web. Complete los pasos y, a continuación, seleccione **Restringir tráfico**. Luego, el Centro de seguridad realizará las conexiones automáticamente. ![][6]

> [AZURE.NOTE] El proceso de aprovisionamiento automático se basa en paquetes WAF (creados con el modelo de implementación de Resource Manager) que se implementan en una red virtual independiente. El acceso a las aplicaciones web protegidas en máquinas virtuales (clásicas) está restringido a los dispositivos WAF solo mediante NSG. Esta compatibilidad se extenderá a una implementación completamente personalizada de paquetes WAF (clásicos) en el futuro. Obtenga más información sobre los [modelos de implementación clásico y de Resource Manager](../azure-classic-rm.md) para los recursos de Azure.

Los registros de ese WAF ahora están totalmente integrados. El Centro de seguridad puede comenzar a recopilar y analizar automáticamente los registros, con el fin de poder exponer las alertas de seguridad importantes.

## Pasos siguientes

En este documento, mostramos cómo implementar la recomendación "Adición de una aplicación web" del Centro de seguridad. Para obtener más información sobre cómo configurar un firewall de aplicaciones web, vea lo siguiente:

- [Configuración de un firewall de aplicaciones web (WAF) para entornos del Servicio de aplicaciones](../app-service-web/app-service-app-service-environment-web-application-firewall.md)

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

- [Establecimiento de directivas de seguridad en el Centro de seguridad de Azure](security-center-policies.md): aprenda a configurar directivas de seguridad.
- [Supervisión del estado de seguridad en el Centro de seguridad de Azure](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administración de alertas de seguridad y respuesta a estas en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): Obtenga información sobre cómo administrar alertas de seguridad y responder a estas.
- [Administración de recomendaciones de seguridad en el Centro de seguridad de Azure](security-center-recommendations.md): obtenga información sobre cómo las recomendaciones lo ayudan a proteger sus recursos de Azure.
- [Preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md): Encuentre las preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]: ./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png

<!---HONumber=AcomDC_0224_2016-->