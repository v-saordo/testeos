<properties
	pageTitle="Información general sobre las conexiones híbridas | Microsoft Azure"
	description="Obtenga información acerca de las conexiones híbridas, la seguridad, los puertos TCP y las configuraciones admitidas. MABS, WABS."
	services="biztalk-services"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="erikre"
	editor=""/>

<tags
	ms.service="biztalk-services"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/29/2016"
	ms.author="mandia"/>


# Introducción a las conexiones híbridas
En la introducción a las conexiones híbridas, se muestran las configuraciones admitidas y se indican los puertos TCP requeridos.


## ¿Qué es una conexión híbrida?

Las conexiones híbridas son una característica de los servicios de BizTalk de Azure: Las conexiones híbridas proporcionan una manera fácil y cómoda de conectar la característica de Aplicaciones web en el Servicio de aplicaciones de Azure (denominado anteriormente sitios web) y la característica de Aplicaciones móviles en el Servicio de aplicaciones de Azure (denominado anteriormente Servicios móviles) con recursos locales protegidos por un firewall.

![conexiones híbridas][HCImage]

Las ventajas de las conexiones híbridas incluyen:

- Las Aplicaciones web y las Aplicaciones móviles pueden acceder a los datos y servicios locales existentes de forma segura.
- Varias Aplicaciones web y Aplicaciones móviles pueden compartir una conexión híbrida para acceder a un recurso local.
- Se requieren puertos TCP mínimos para acceder a la red.
- Las aplicaciones que usan conexiones híbridas solo acceden al recurso local específico que se publica a través de la conexión híbrida.
- Pueden conectarse a cualquier recurso local que use un puerto TCP estático, como SQL Server, MySQL, API Web HTTP y la mayoría de los servicios web personalizados.

	> [AZURE.NOTE] Los servicios basados en TCP que usan puertos dinámicos (como modo pasivo FTP o modo pasivo extendido) no se admiten actualmente.

- Se pueden usar con todos los marcos compatibles con Aplicaciones web (.NET, PHP, Java, Python, Node.js) y Aplicaciones móviles (Node.js, .NET).
- Las Aplicaciones web y las Aplicaciones móviles pueden acceder a los recursos locales igual que si la aplicación web o móvil se encontrara en la red local. Por ejemplo, la misma cadena de conexión usada en el entorno local se puede utilizar en Azure.


Las conexiones híbridas también ofrecen a los administradores de empresa control y visibilidad sobre los recursos corporativos a los que acceden las aplicaciones híbridas, por ejemplo:

- Mediante el uso de la configuración de directiva de grupo, los administradores pueden permitir conexiones híbridas en la red y designar también recursos a los que pueden acceder las aplicaciones híbridas.
- Los registros de eventos y de auditoría de la red corporativa proporcionan visibilidad sobre los recursos a los que acceden las conexiones híbridas.


## Configuraciones admitidas

Las conexiones híbridas admiten las siguientes combinaciones de aplicaciones y marcos de trabajo:

- Acceso del marco de trabajo .NET a SQL Server
- Acceso del marco de trabajo .NET a los servicios HTTP/HTTPS con WebClient
- Acceso de PHP a SQL Server, MySQL
- Acceso de Java a SQL Server, MySQL y Oracle
- Acceso de Java a los servicios HTTP/HTTPS

A la hora de usar conexiones híbridas para acceder a SQL Server local, tenga en cuenta los siguientes aspectos:

- Las instancias con nombre de SQL Express se deben configurar para usar puertos estáticos. De forma predeterminada, las instancias con nombre de SQL Express usan puertos dinámicos.
- Las instancias predeterminadas de SQL Express usan un puerto estático, pero TCP debe estar habilitado. De forma predeterminada, TCP no está habilitado.
- Si se usan la agrupación en clústeres o los grupos de disponibilidad, el modo `MultiSubnetFailover=true` no se admite.
- Actualmente, el modo `ApplicationIntent=ReadOnly` no se admite.
- Puede que se requiera la autenticación de SQL como método de autorización completo que se admite en la aplicación de Azure y en el servidor SQL local.


## Seguridad y puertos

Las conexiones híbridas emplean la autorización de firma de acceso compartido (SAS) para proteger las conexiones entre las aplicaciones de Azure y el administrador de conexiones híbridas local y la conexión híbrida. Se crean claves de conexión diferentes para la aplicación y el Administrador de conexiones híbridas local. Estas claves de conexión se pueden sustituir y revocar de manera independiente.

Las conexiones híbridas proporcionan una distribución adecuada y segura de las claves a las aplicaciones y al administrador de conexiones híbridas local.

Consulte [Creación y administración de conexiones híbridas](integration-hybrid-connection-create-manage.md).

*La autorización de la aplicación es independiente de la conexión híbrida*. Se puede usar cualquier método de autorización adecuado. El método de autorización depende de los métodos de autorización completos que se admitan en la nube de Azure y de los componentes locales. Por ejemplo, su aplicación de Azure accede a un servidor SQL local. En este escenario, la autorización de SQL puede ser el método de autorización que se admita completamente.

#### Puertos TCP
Las conexiones híbridas requieren únicamente conectividad TCP o HTTP saliente de su red privada. No es necesario abrir los puertos de firewall ni cambiar la configuración del perímetro de red para permitir la conectividad entrante a la red.

Los siguientes puertos TCP se usan en las conexiones híbridas:

Port | Por qué es necesario
--- | ---
9350 - 9354 | Estos puertos se usan para la transmisión de datos. El administrador de retransmisiones de bus de servicio sondea el puerto 9350 para determinar si la conectividad TCP está disponible. Si lo está, asume que el puerto 9352 está también disponible. El tráfico de datos pasa por el puerto 9352. <br/><br/>Permitir conexiones salientes a estos puertos.
5671 | Cuando se usa el puerto 9352 para el tráfico de datos, el puerto 5671 se usa como canal de control. <br/><br/>Permitir conexiones salientes a este puerto.
80, 443 | Estos puertos se usan para algunas solicitudes de datos en Azure. Si los puertos 9352 y 5671 no se pueden usar, los *puertos* 80 y 443 son los puertos de reserva que se usan para la transmisión de datos y el canal de control.<br/><br/>Permitir conexiones salientes a estos puertos. <br/><br/>**Nota** No es aconsejable usar estos puertos de reserva en lugar de los restantes puertos TCP. HTTP/WebSocket se utiliza como protocolo, en lugar del TCP nativo, para los canales de datos. Podría provocar un rendimiento menor.



## Pasos siguientes

[Creación y administración de conexiones híbridas](integration-hybrid-connection-create-manage.md)<br/> [Conexión de un sitio web de Azure a un recurso local](../app-service-web/web-sites-hybrid-connection-get-started.md)<br/> [Conexión a SQL Server local desde una aplicación web de Azure](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)<br/> [Servicios móviles de Azure y conexiones híbridas](../mobile-services/mobile-services-dotnet-backend-hybrid-connections-get-started.md)


## Otras referencias

[API de REST para administrar servicios de BizTalk en Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx) [Servicios de BizTalk: gráfico de ediciones](biztalk-editions-feature-chart.md)<br/> [Creación un servicio de BizTalk mediante el Portal de Azure](biztalk-provision-services.md)<br/> [Servicios de BizTalk: pestañas Panel, Monitor y Escala](biztalk-dashboard-monitor-scale-tabs.md)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png

<!---HONumber=AcomDC_0302_2016-->