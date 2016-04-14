<properties
   pageTitle="Crear un FQDN para una máquina virtual en el Portal de Azure | Microsoft Azure"
   description="Aprenda a crear un nombre de dominio completo o FQDN para una máquina virtual basada en el administrador de recursos en el Portal de Azure."
   services="virtual-machines"
   documentationCenter=""
   authors="dsk-2015"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/19/2016"
   ms.author="dkshir"/>

# Crear un nombre de dominio completo en el Portal de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.


Cuando crea una máquina virtual en el [Portal de Azure](https://portal.azure.com) mediante el modelo de implementación del **Administrador de recursos**, el portal crea un recurso de IP pública para la máquina virtual. Puede usar esta dirección IP para obtener acceso remoto a la máquina virtual. Sin embargo, el portal no crea un [nombre de dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) o FQDN de forma predeterminada, es más sencillo hacerlo una creada la máquina virtual. Este artículo muestra los pasos para crear un nombre DNS o FQDN.

En el artículo se supone que ha iniciado la sesión en su suscripción en el portal y que ha creado una máquina virtual con las imágenes disponibles con el **Administrador de recursos**. Cuando comience a ejecutarse la máquina virtual, siga estos pasos.

1.  Vea la configuración de la máquina virtual en el portal y haga clic en la dirección IP pública.

    ![buscar recurso de ip](media/virtual-machines-create-fqdn-on-portal/locatePublicIP.PNG)

2.  Tenga en cuenta que el nombre DNS de la dirección IP pública está en blanco. Haga clic en **Toda la configuración** en la hoja de la dirección IP pública.

    ![configuración de ip](media/virtual-machines-create-fqdn-on-portal/settingsIP.PNG)

3.  Abra la ficha de **Configuración** de la dirección IP pública. Escriba la etiqueta de nombre DNS deseada y haga clic en **Guardar**.

    ![escribir etiqueta de nombre dns](media/virtual-machines-create-fqdn-on-portal/dnsNameLabel.PNG)

    El recurso de dirección IP pública mostrará ahora esta nueva etiqueta DNS en su hoja.

4.  Cierre las hojas de la dirección IP pública y vuelva a la hoja de la máquina virtual del portal. Compruebe que el nombre DNS o FQDN aparece junto a la dirección IP para el recurso de dirección IP pública.

    ![Se crea el FQDN](media/virtual-machines-create-fqdn-on-portal/fqdnCreated.PNG)


    Ahora puede conectarse de manera remota a la máquina virtual con este nombre DNS. Por ejemplo, use `SSH adminuser@testdnslabel.centralus.cloudapp.azure.com` al conectarse a una máquina virtual de Linux que tiene el nombre de dominio completo nombre de `testdnslabel.centralus.cloudapp.azure.com` y el nombre de usuario de `adminuser`.

<!---HONumber=AcomDC_0128_2016-->