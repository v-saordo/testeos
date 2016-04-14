<properties
   pageTitle="Selección de un dominio de Internet de la compañía para un dominio del Administrador de tráfico | Microsoft Azure"
   description="Este artículo le ayudará a que el nombre de dominio de la empresa indique un nombre de dominio del Administrador de tráfico."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="joaoma" />

# Orientar un dominio de Internet de la compañía a un dominio del Administrador de tráfico de Azure

Para que el nombre de dominio de la empresa apunte a un nombre de dominio del Administrador de tráfico, modifique el registro de recursos DNS del servidor DNS de Internet para que use el tipo de registro CNAME, que asigna el nombre de dominio de la empresa al nombre de dominio del perfil de Administrador de tráfico. Puede ver el nombre de dominio del Administrador de tráfico en la sección **General** de la página Configuración del perfil del Administrador de tráfico.

Por ejemplo, para que el nombre de dominio de la empresa www.contoso.com apunte al nombre de dominio del Administrador de tráfico contoso.trafficmanager.net, debe actualizar el registro de recursos DNS para que sea el siguiente:

    www.contoso.com IN CNAME contoso.trafficmanager.net

Todas las solicitudes de tráfico de *www.contoso.com* se redirigirán ahora a *contoso.trafficmanager.net*.

>[AZURE.IMPORTANT] No puede hacer que un dominio de segundo nivel como por ejemplo *contoso.com*, indique el dominio del Administrador de tráfico. Se trata de una limitación del protocolo DNS, que no permite registros CNAME para nombres de dominio de segundo nivel.

## Pasos siguientes

[Métodos de enrutamiento del Administrador de tráfico](traffic-manager-routing-methods.md)

[Administrador de tráfico: deshabilitación, habilitación o eliminación de un perfil](disable-enable-or-delete-a-profile.md)

[Administrador de tráfico: deshabilitación o habilitación de un extremo](disable-or-enable-an-endpoint.md)

<!---HONumber=AcomDC_0218_2016-->