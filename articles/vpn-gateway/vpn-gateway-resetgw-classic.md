<properties
   pageTitle="Restablecer una Puerta de enlace de VPN de Azure | Microsoft Azure"
   description="Este artículo te guía a través del restablecimiento de la Puerta de enlace de VPN de Azure. Ten en cuenta que este artículo se aplica a las Puertas de enlace de VPN creadas con el modelo de implementación clásico."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="cherylmc"/>

# Restablecer una Puerta de enlace de VPN de Azure con PowerShell


Este artículo te guiará a través del restablecimiento de la Puerta de enlace de VPN de Azure mediante cmdlets de PowerShell. Estas instrucciones se aplican al modelo de implementación clásica. Todavía no tenemos un cmdlet o una API de REST para restablecer la Puerta de enlace de VPN para las redes virtuales creadas mediante el modelo de implementación del Administrador de recursos. Se encuentran en proceso. Puede indicar si la puerta de enlace de VPN se creó usando el modelo de implementación clásico mediante la visualización de la red virtual en el Portal de Azure. Las redes virtuales creadas mediante el modelo clásico de implementación se muestran en la parte de Red virtual (clásica) del Portal de Azure.

El restablecer la Puerta de enlace de VPN de Azure es útil si se pierde la conectividad VPN entre locales en uno o varios túneles VPN de S2S. En esta situación, todos tus dispositivos VPN locales funcionan correctamente, pero no pueden establecer túneles IPsec con las Puertas de enlace de VPN de Azure. Cuando usas el cmdlet *Reset-AzureVNetGateway*, este reiniciará la puerta de enlace y después le volverá a aplicar las configuraciones entre locales. La puerta de enlace mantendrá la dirección IP pública que ya tiene. Esto significa que no tendrás que actualizar la configuración del enrutador VPN con una nueva dirección IP pública de Puerta de enlace de VPN de Azure.


Antes de restablecer la puerta de enlace, comprueba los elementos clave que se enumeran a continuación para todos los túneles VPN de sitio a sitio (S2S) de IPsec. Cualquier incoherencia que haya en los elementos provocará la desconexión de los túneles VPN de S2S. El comprobar y corregir la configuración de las puertas de enlace locales y VPN de Azure te evitará reinicios e interrupciones innecesarios para las demás conexiones de trabajo de las puertas de enlace.

Comprueba los elementos siguientes antes de restablecer la puerta de enlace.

- Las direcciones IP de Internet (VIP) tanto de la puerta de enlace VPN de Azure como de la puerta de enlace VPN local están configuradas correctamente en Azure así como en las directivas VPN locales.
- La clave previamente compartida tiene que ser la misma en las Puertas de enlace VPN de Azure y en las locales.
- Si aplicas la configuración específica de IPsec/IKE, como el cifrado, algoritmos hash y PFS (confidencialidad directa perfecta), asegúrate de que tanto las Puertas de enlace de VPN de Azure como las locales tienen la misma configuración.


## Restablecer una Puerta de enlace de VPN con PowerShell

El cmdlet de PowerShell para restablecer la Puerta de enlace de VPN de Azure es *Reset-AzureVNetGateway*. Todas las Puertas de enlace de VPN de Azure se componen de dos instancias de VM que se ejecutan en una configuración activa o en modo de espera. Una vez que se emite el comando, se reiniciará inmediatamente la instancia activa actual de la Puerta de enlace de VPN de Azure. Habrá un breve intervalo durante la conmutación por error de la instancia activa (que se va a reiniciar) hasta la instancia en modo de espera. El intervalo debe ser inferior a un minuto.

En el ejemplo siguiente se restablece la Puerta de enlace de VPN de Azure para la red virtual denominada «ContosoVNet».
 
			D:\PS> Reset-AzureVNetGateway –VnetName “ContosoVNet” 

	 		Error          :
	 		HttpStatusCode : OK
	 		Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
	 		Status         : Successful
			RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
			StatusCode     : OK


Si la conexión no se restaura después del primer reinicio, vuelve a ejecutar el mismo comando para reiniciar la segunda instancia de VM (la nueva puerta de enlace activa). Si se solicitan los dos reinicios consecutivamente, habrá un período un poco más largo donde se estén reiniciando ambas instancias de VM (activa y en espera). En este caso, ocurrirá una interrupción mayor en la conectividad de VPN, de 2 a 4 minutos, para que las máquinas virtuales completen los reinicios.

Después de dos reinicios, si sigue teniendo problemas de conectividad entre locales, abra una incidencia de soporte técnico en el Portal de Azure clásico para ponerse en contacto con el soporte técnico de Microsoft Azure.

## Pasos siguientes
	
Para obtener más información sobre este cmdlet consulta la [Referencia de PowerShell](https://msdn.microsoft.com/library/azure/mt270366.aspx).

<!---HONumber=AcomDC_0211_2016-->