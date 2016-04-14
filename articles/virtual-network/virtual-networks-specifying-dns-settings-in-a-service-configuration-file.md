<properties 
   pageTitle="Especificación de la configuración DNS en un archivo de configuración de servicio | Microsoft Azure"
   description="Especificación de la configuración de DNS personalizada mediante un archivo de configuración de servicio para una red virtual"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/24/2016"
   ms.author="joaoma" />

# Especificar la configuración DNS en un archivo de configuración de servicio

## Elementos DNS

Un archivo de configuración de servicio puede contener un elemento DnsServers con una lista de direcciones IPv4 de los servidores de sistema de nombres de dominio (DNS) que el servicio usará. La configuración en el archivo de configuración de servicio sobrescribirá la del archivo de configuración de red. Para obtener más información, consulte [Esquema de configuración del servicio de Azure (archivo .cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**Elemento NetworkConfiguration**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

>[AZURE.WARNING] El atributo **nombre** en el elemento **DnsServer** solo se usa como nombre de referencia. No representa el nombre de host del servidor DNS. Cada valor del atributo **DnsServer** debe ser único en toda la suscripción de Microsoft Azure.

## Otras referencias

[Esquema de configuración del servicio de Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Esquema de configuración de red virtual de Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Configuración de una red virtual mediante un archivo de configuración de red](http://go.microsoft.com/fwlink/?LinkId=248094)

[Información acerca de la configuración de red virtual en el Portal de administración](http://go.microsoft.com/fwlink/?LinkId=248092)

<!---HONumber=AcomDC_0302_2016-->