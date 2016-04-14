<properties 
   pageTitle="Uso de las direcciones IP públicas en una red virtual"
   description="Obtenga información acerca de la configuración de una red virtual para usar direcciones IP públicas"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# Espacio de direcciones IP públicas en una red virtual (VNet)

Ahora puede agregar un espacio de direcciones IP públicas a sus redes virtuales. Anteriormente, solo podía agregar bloques de direcciones RFC 1918 (espacio privado) a sus redes virtuales. Cuando se agrega un intervalo de direcciones IP público, se tratará como parte del espacio de direcciones IP de redes virtuales privadas que solo es accesible dentro de la red virtual, redes virtuales interconectadas y desde su ubicación local.

La incorporación de un espacio de direcciones IP públicas funciona conceptualmente de la siguiente forma:

![Dirección IP pública conceptual](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## Adición de un intervalo de direcciones IP públicas

Agregue un intervalo de direcciones IP públicas del mismo modo que agregaría un intervalo de direcciones IP privadas; para ello, utilice un archivo *netcfg* o realice la configuración en el portal. Puede agregar un intervalo de direcciones IP públicas al crear la red virtual, o bien puede volver atrás y agregarlo más adelante. El ejemplo siguiente muestra espacios de direcciones IP públicas y privadas configurados en la misma red virtual.

![Dirección IP pública en portal](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## ¿Hay alguna limitación de uso?

Hay algunos intervalos de direcciones IP que no están permitidos:

- 224\.0.0.0/4 (multidifusión)

- 255\.255.255.255/32 (difusión)

- 127\.0.0.0/8 (bucle invertido)

- 169\.254.0.0/16 (local de vínculo)

- 68\.63.129.16/32 (DNS interno)

## Pasos siguientes

[Administración de las propiedades de la red virtual](../virtual-networks-settings)

[Administración de servidores DNS usados por una red virtual](../virtual-networks-manage-dns-in-vnet)

[Eliminación de una red virtual](../virtual-networks-delete-vnet)

<!---HONumber=AcomDC_1217_2015-->