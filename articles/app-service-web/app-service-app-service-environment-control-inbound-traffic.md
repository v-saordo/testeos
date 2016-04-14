<properties 
	pageTitle="Cómo controlar el tráfico de entrada a un entorno del Servicio de aplicaciones " 
	description="Obtenga información acerca de cómo configurar las reglas de seguridad de red para controlar el tráfico entrante a un entorno del Servicio de aplicaciones." 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2016" 
	ms.author="stefsch"/>

# Cómo controlar el tráfico de entrada a un entorno del Servicio de aplicaciones

## Información general ##
Siempre se crea un entorno del Servicio de aplicaciones en una subred de una [red virtual][virtualnetwork] "v1" regional clásica. Una nueva red virtual "v1" regional clásica y una nueva subred pueden definirse en el momento en que se crea un entorno del Servicio de aplicaciones. También puede crearse un entorno del Servicio de aplicaciones en una red virtual regional clásica "v1" y una subred preexistentes. Para obtener más detalles sobre la creación de un entorno del Servicio de aplicaciones, consulte [Creación de un entorno del Servicio de aplicaciones][HowToCreateAnAppServiceEnvironment].

**Nota:** No se puede crear un Entorno del Servicio de aplicaciones en una red virtual "v2" administrada por ARM.

Siempre debe crearse un entorno del Servicio de aplicaciones dentro de una subred al proporcionar esta un límite de red que puede utilizarse para bloquear el tráfico entrante tras dispositivos y servicios ascendentes de forma que solo se acepta el tráfico HTTP y HTTPS de determinadas direcciones IP ascendentes.

El tráfico de red entrante y saliente de una subred se controla mediante un [grupo de seguridad de red][NetworkSecurityGroups]. Para controlar el tráfico entrante, se requiere la creación de reglas de seguridad de red en un grupo de seguridad de red y, a continuación, la asignación a este de la subred que contiene el entorno del Servicio de aplicaciones.

Una vez que un grupo de seguridad de red se asigna a una subred, el tráfico entrante a aplicaciones en el entorno del Servicio de aplicaciones se permite o bloquea según las reglas de permiso y denegación definidas en el grupo de seguridad de red.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Puertos de red usados en un entorno del Servicio de aplicaciones ##
Antes de bloquear el tráfico de red entrante con un grupo de seguridad de red, es importante conocer el conjunto de puertos de red necesarios y opcionales utilizados por un entorno del Servicio de aplicaciones. El bloqueo accidental del tráfico a algunos puertos puede producir la pérdida de funcionalidad en un entorno del Servicio de aplicaciones.

A continuación se muestra una lista de puertos utilizados por un entorno del Servicio de aplicaciones:

- 454: **puerto obligatorio** utilizado por la infraestructura de Azure para administrar y mantener entornos del Servicio de aplicaciones. No bloquee el tráfico a este puerto.
- 455: **puerto obligatorio** utilizado por la infraestructura de Azure para administrar y mantener entornos del Servicio de aplicaciones. No bloquee el tráfico a este puerto.
- 80: puerto predeterminado para el tráfico HTTP entrante a aplicaciones que se ejecutan en planes del Servicio de aplicaciones en un entorno del Servicio de aplicaciones
- 443: puerto predeterminado para el tráfico SSL entrante a aplicaciones que se ejecutan en planes del Servicio de aplicaciones en un entorno del Servicio de aplicaciones
- 21: canal de control para FTP. Este puerto se puede bloquear de forma segura si no se utiliza FTP.
- 10001-10020: canales de datos para FTP. Al igual que ocurre con el canal de control, estos puertos se pueden bloquear de forma segura si no se utiliza FTP (**Nota:** Los canales de datos FTP pueden cambiar durante la vista previa).
- 4016: usado para la depuración remota con Visual Studio 2012. Este puerto se puede bloquear de forma segura si no se utiliza la característica.
- 4018: usado para la depuración remota con Visual Studio 2013. Este puerto se puede bloquear de forma segura si no se utiliza la característica.
- 4020: usado para la depuración remota con Visual Studio 2015. Este puerto se puede bloquear de forma segura si no se utiliza la característica.

## Requisitos de DNS y conectividad saliente ##
Para que un Entorno del Servicio de aplicaciones funcione correctamente, necesita acceso de salida al Almacenamiento de Azure en todo el mundo, así como a la Base de datos SQL en la misma región de Azure. Si se bloquea el acceso de salida a Internet en la red virtual, los entornos del Servicio de aplicaciones no podrán tener acceso a estos extremos de Azure.

Los Entornos del Servicio de aplicaciones también requieren una infraestructura DNS válida configurada para la red virtual. Si por algún motivo se cambia la configuración de DNS después de haber creado un entorno del Servicio de aplicaciones, los desarrolladores pueden forzar a un entorno del Servicio de aplicaciones para recoger la nueva configuración de DNS. Si se desencadena un reinicio gradual del entorno mediante el icono "Reiniciar", ubicado en la parte superior de la hoja de administración del entorno del Servicio de aplicaciones en el [Portal de Azure][NewPortal], el entorno seleccionará la nueva configuración de DNS.

La siguiente lista detalla los requisitos de conectividad y DNS para un Entorno del Servicio de aplicaciones:

-  Conectividad de red saliente a los puntos de conexión de Almacenamiento de Azure en todo el mundo. Esto incluye los puntos de conexión situados en la misma región que el Entorno del Servicio de aplicaciones, así como los puntos de conexión de almacenamiento ubicados en **otras** regiones de Azure. Los puntos de conexión de Almacenamiento de Azure se resuelven en los dominios DNS siguientes: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* y *file.core.windows.net*.  
-  Conectividad de red saliente a los puntos de conexión de Base de datos SQL ubicados en la misma región que el entorno del Servicio de aplicaciones. Los puntos de conexión de Base de datos SQL se resuelven en el dominio siguiente: *database.windows.net*.
-  Conectividad de la red saliente a los puntos de conexión del plano de administración de Azure (puntos de conexión ASM y ARM). Incluye conectividad saliente tanto a *management.core.windows.net* como a *management.azure.com*. 
-  Conectividad de red saliente a *ocsp.msocsp.com*. Es necesario para admitir la funcionalidad SSL.
-  La configuración de DNS para la red virtual debe ser capaz de resolver todos los puntos de conexión y dominios mencionados en los puntos anteriores. Si estos puntos de conexión no se pueden resolver, se producirá un error en los intentos de creación del entorno del Servicio de aplicaciones y los entornos del Servicio de aplicaciones existentes se marcarán como incorrectos.
-  Si existe un servidor DNS personalizado en el otro punto de conexión de una puerta de enlace de VPN, el servidor DNS debe estar accesible desde la subred que contiene el entorno de Servicio de aplicaciones. 
-  La ruta de acceso de la red saliente no puede atravesar los servidores proxy corporativos internos, ni puede forzar la tunelización a local. Si lo hace, se cambiará la dirección NAT en vigor del tráfico de red saliente del entorno del Servicio de aplicaciones. Al cambiar la dirección NAT del tráfico de red de salida de un entorno del Servicio de aplicaciones causará errores de conectividad a muchos de los puntos de conexión enumerados anteriormente. Lo que da como resultado un error al intentar la creación del entorno del Servicio de aplicaciones, y además, los entornos del Servicio de aplicaciones previamente correctos estarán marcados como incorrectos.  
-  El acceso de red entrante a los puertos necesarios para los Entornos del Servicio de aplicaciones debe estar permitido, como se describe en este [artículo](app-service-app-service-environment-control-inbound-traffic.md).

También se recomienda configurar de antemano los servidores DNS personalizados de la red virtual antes de crear un entorno del Servicio de aplicaciones. Si se cambia la configuración de DNS de una red virtual al crear un entorno del Servicio de aplicaciones, se generará un error en el proceso de creación de dicho entorno. De manera similar, si existe un servidor DNS personalizado en el otro extremo de una puerta de enlace de VPN y el servidor DNS es inaccesible o no está disponible, el proceso de creación del entorno de servicio de aplicaciones también producirá un error.

## Creación de un grupo de seguridad de red ##
Para obtener los detalles completos de cómo funcionan los grupos de seguridad de red, consulte la siguiente [información][NetworkSecurityGroups]. Los detalles siguientes hacen referencia a los puntos destacados de los grupos de seguridad de red, centrándose en la configuración y aplicación de un grupo de seguridad de red a una subred que contiene un entorno del Servicio de aplicaciones.

**Nota:** Solo se pueden configurar grupos de seguridad de red con los cmdlets de Powershell descritos a continuación. Los grupos de seguridad de red no se pueden configurar gráficamente mediante el [Portal de Azure](portal.azure.com) porque este solo permite la configuración gráfica de grupos de seguridad de red asociados a las redes virtuales "v2". Sin embargo, los entornos de servicio de aplicaciones solo funcionan actualmente con las redes virtuales clásicas "v1". Como resultado, solo se pueden utilizar los cmdlets de Powershell para configurar los grupos de seguridad de red asociados a las redes virtuales "v1".

Los grupos de seguridad de red se crean por primera vez como una entidad independiente asociada a una suscripción. Puesto que los grupos de seguridad de red se crean en una región de Azure, asegúrese de que el grupo de seguridad de red se crea en la misma región que el entorno del Servicio de aplicaciones.

Lo siguiente muestra la creación de un grupo de seguridad de red:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Una vez creado un grupo de seguridad de red, una o varias reglas de seguridad de red se agregan a él. Dado que el conjunto de reglas puede cambiar con el tiempo, se recomienda espaciar el esquema de numeración usado para las prioridades de reglas para facilitar la inserción de reglas adicionales con el tiempo.

En el ejemplo siguiente se muestra una regla que concede acceso de forma explícita a los puertos de administración necesarios para que la infraestructura de Azure administre y mantenga un entorno del Servicio de aplicaciones. Tenga en cuenta que todo el tráfico de administración fluye a través de SSL y se protege mediante certificados de cliente, por lo que aunque estén abiertos los puertos, son inaccesibles para cualquier entidad que no sea la infraestructura de administración de Azure.


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    

Al bloquear el acceso a los puertos 80 y 443 para "ocultar" un entorno del Servicio de aplicaciones tras dispositivos o servicios ascendentes, tendrá que conocer la dirección IP ascendente. Por ejemplo, si usa un firewall de aplicaciones web (WAF), este tendrá su propia dirección (o direcciones) IP, que usa al transmitir el tráfico a un entorno del Servicio de aplicaciones de nivel auxiliar. Deberá usar esta dirección IP en el parámetro *SourceAddressPrefix* de una regla de seguridad de red.

En el ejemplo siguiente, se permite explícitamente el tráfico entrante de una dirección IP ascendente específica. La dirección *1.2.3.4* se usa como marcador de posición para la dirección IP de un WAF ascendente. Cambie el valor para que coincida con la dirección usada por su dispositivo o servicio ascendente.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
Si se desea compatibilidad con FTP, las reglas siguientes pueden utilizarse como plantilla para conceder acceso al puerto de control FTP y los puertos del canal de datos. Puesto que FTP es un protocolo con estado, es posible que no pueda enrutar el tráfico FTP a través de un dispositivo de firewall o proxy HTTP/HTTPS. En este caso deberá establecer *SourceAddressPrefix* en un valor diferente: por ejemplo, el intervalo de direcciones IP del desarrollador o los equipos de implementación en los que se ejecutan los clientes FTP.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Nota:** puede cambiar el intervalo de puertos del canal de datos durante el período de vista previa).

Si se usa la depuración remota con Visual Studio, las reglas siguientes muestran cómo conceder acceso. Puesto que cada versión usa un puerto diferente para la depuración remota, hay una regla distinta para cada versión compatible de Visual Studio. Al igual que ocurre con el acceso FTP, es posible que el tráfico de depuración remoto no fluya correctamente a través de un dispositivo de proxy o WAF tradicional. En su lugar, *SourceAddressPrefix* se puede establecer en el intervalo de direcciones IP de los equipos del desarrollador que ejecutan Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## Asignación de un grupo de seguridad de red a una subred ##
Un grupo de seguridad de red tiene una regla de seguridad predeterminada que deniega el acceso a todo el tráfico externo. El resultado de la combinación de las reglas de seguridad de red descritas anteriormente y la regla de seguridad predeterminada que bloquea el tráfico entrante es que solo el tráfico de intervalos de direcciones de origen asociado a una acción *Permitir* podrá enviar tráfico a aplicaciones que se ejecutan en un entorno del Servicio de aplicaciones.

Después de rellenarse un grupo de seguridad de red con las reglas de seguridad, debe asignarse a la subred que contiene el entorno del Servicio de aplicaciones. El comando de asignación hace referencia tanto al nombre de la red virtual donde reside el entorno del Servicio de aplicaciones como al nombre de la subred donde se creó este.

En el ejemplo siguiente se muestra la asignación de un grupo de seguridad de red a una subred y una red virtual:


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Cuando se realice correctamente la asignación del grupo de seguridad de red (la asignación es una operación de ejecución prolongada y puede tardar unos minutos en completarse), solo el tráfico entrante que coincida con las reglas de *permisión* llegará correctamente a las aplicaciones del entorno del Servicio de aplicaciones.

Por razones de integridad, en el ejemplo siguiente se muestra cómo quitar y, por tanto, desasociar el grupo de seguridad de red de la subred:


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## Consideraciones especiales para IP-SSL explícito ##
Si una aplicación está configurada con una dirección IP explícita en lugar de la dirección IP predeterminada del entorno de Servicio de aplicaciones, tanto el tráfico HTTP como el tráfico HTTPS fluyen a la subred a través de un conjunto de puertos distintos de los puertos 80 y 443.

El par individual de puertos utilizados para cada dirección IP-SSL se encuentra haciendo clic en "Todas las configuraciones" --> "Direcciones IP" de la hoja de la interfaz de usuario del entorno del Servicio de aplicaciones. La hoja "Direcciones IP" muestra una tabla de todas las direcciones IP-SSL configuradas explícitamente para el entorno del Servicio de aplicaciones, junto con el par de puertos especiales que se utilizan para enrutar el tráfico HTTP y HTTPS asociado con cada dirección IP-SSL. Es este par de puertos el que debe utilizarse para los parámetros de DestinationPortRange al configurar las reglas de un grupo de seguridad de red.

## Introducción

Para empezar a trabajar con los entornos del Servicio de aplicaciones, consulte [Introducción al entorno del Servicio de aplicaciones][IntroToAppServiceEnvironment]

Para obtener detalles en torno a las aplicaciones que se conectan de forma segura al recurso de back-end desde un entorno del Servicio de aplicaciones, consulte [Conexión segura a los recursos de back-end desde un entorno del Servicio de aplicaciones][SecurelyConnecttoBackend]

Para obtener más información acerca de la plataforma de Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[SecurelyConnecttoBackend]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NewPortal]: https://portal.azure.com

<!-- IMAGES -->
 

<!---HONumber=AcomDC_0302_2016-->