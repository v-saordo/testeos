<properties 
	pageTitle="Uso del Administrador de conexiones híbridas | Servicio de aplicaciones de Microsoft Azure" 
	description="Instale y configure el Administrador de conexiones híbridas y conéctese a los conectores locales en Servicio de aplicaciones de Azure" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2016" 
	ms.author="mandia"/>

# Conexión a conectores locales en Servicio de aplicaciones de Azure mediante el Administrador de conexiones híbridas

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas.

Para usar un sistema local, el Servicio de aplicaciones de Azure emplea el Administrador de conexiones híbridas. Algunos conectores pueden conectar a un sistema local, como SQL Server, SAP, SharePoint, etc.

El Administrador de conexiones híbridas (HCM) es un instalador de un solo clic que se instala en un servidor IIS de la red, detrás del firewall. Mediante una Retransmisión de bus de servicio de Azure, HCM autentica el sistema local con el conector de Azure.

> [AZURE.NOTE] El Administrador de conexiones híbridas solo es necesario si va a conectarse a un recurso local detrás del firewall. En caso contrario, no lo necesita.

Para empezar, necesitará lo siguiente:

- Una cadena de conexión SAS para el espacio de nombres de la Retransmisión de bus de servicio. Consulte [Precios de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) para determinar qué nivel incluye retransmisiones.
- Información de inicio de sesión del sistema local, que incluye nombre de usuario y contraseña. Por ejemplo, si se conecta a un servidor SQL Server local, necesitará la cuenta de inicio de sesión y la contraseña de SQL Server.
- Información del servidor, lo que incluye el número de puerto y el nombre del servidor. Por ejemplo, si se va a conectar a un servidor SQL Server local, necesitará el nombre del servidor SQL Server y el número de puerto TCP.

## Para obtener la cadena de conexión del Bus de servicio

En el portal de Azure, copie la cadena de conexión SAS raíz de Bus de servicio. Esta cadena de conexión conecta el conector de Azure con el sistema local.

1. En el [Portal de Azure clásico](http://go.microsoft.com/fwlink/p/?LinkID=213885), seleccione el espacio de nombres del Bus de servicio y seleccione **Información de conexión**:

	![][SB_ConnectInfo]

2. Copie la cadena de conexión SAS.

	![][SB_SAS]

## Instalación del Administrador de conexiones híbridas

1. En el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), seleccione el conector que ha creado. Para abrirlo, puede seleccionar **Examinar**, **Aplicaciones de la API** y luego el conector o la aplicación de API. <br/><br/> En **Conexión híbrida**, la instalación está **incompleta**: <br/> ![][2] 

2. Seleccione **Conexión híbrida**. Se muestra la cadena de conexión del Bus de servicio que escribió anteriormente.
3. Copie la **Cadena de configuración principal**: <br/> ![][PrimaryConfigString]

4. En **Administrador de conexiones híbridas local**, puede descargar el Administrador de conexiones híbridas o instalarlo directamente desde el portal. <br/><br/> Para instalar directamente desde el portal, vaya a su servidor IIS local, vaya al portal y seleccione **Descargar y configurar**. <br/><br/> Para descargar el Administrador de conexiones híbridas, vaya al servidor IIS local y vaya a la **aplicación ClickOnce** (http://hybridclickonce.azurewebsites.net/install/Microsoft.Azure.BizTalk.Hybrid.ClickOnce.application). La instalación se inicia automáticamente para que pueda ejecutarla.

5. En la ventana **Configuración del proceso de escucha**, escriba la **Cadena de configuración principal** que pegó anteriormente (en el paso 3) y seleccione **Instalar**.

Cuando el programa de instalación haya finalizado, se mostrará lo siguiente: <br/> ![][3]

Ahora, cuando busque de nuevo el conector, el estado de la conexión híbrida será **Conectada**. Puede que tenga que cerrar el conector y volver a abrirlo: <br/> ![][4]

> [AZURE.NOTE] Para cambiar a la cadena de conexión secundaria, vuelva a ejecutar el programa de instalación de conexiones híbridas y escriba la **Cadena de configuración secundaria**.


## Puertos TCP y seguridad

Cuando se crea una conexión híbrida, se crea un sitio web en el servidor IIS local. El servidor IIS puede estar en una red perimetral. Los requisitos del puerto TCP en el servidor IIS incluyen:

Puerto TCP | Porqué
--- | ---
 | No se requiere ningún puerto TCP entrante.
9350 - 9354 | Estos puertos se usan para la transmisión de datos. El administrador de retransmisiones de bus de servicio sondea el puerto 9350 para determinar si la conectividad TCP está disponible. Si lo está, asume que el puerto 9352 está también disponible. El tráfico de datos pasa por el puerto 9352. <br/><br/>Permitir conexiones salientes a estos puertos.
5671 | Cuando se usa el puerto 9352 para el tráfico de datos, el puerto 5671 se utiliza como canal de control. <br/><br/>Permitir conexiones salientes a este puerto. 
80, 443 | Si los puertos 9352 y 5671 no se pueden usar, *entonces* los puertos 80 y 443 son los puertos de reserva usados para la transmisión de datos y el canal de control.<br/><br/>Permitir conexiones salientes a estos puertos.
Puerto del sistema local | En el sistema local, abra el puerto usado por el sistema. Por ejemplo, SQL Server usa normalmente el puerto 1433. Abra este puerto TCP.

[Hospedaje detrás de un firewall con el Bus de servicio](http://msdn.microsoft.com/library/azure/ee706729.aspx)

## Solución de problemas

![][HCMFlow]

### Solución de problemas en locales

1. En el servidor IIS, confirme que el rol web de IIS está instalado y que se inician todos los servicios IIS.
2. En el servidor IIS, confirme que el Administrador de conexiones híbridas está instalado y ejecutándose:
 - En el Administrador de IIS (inetmgr), debe aparecer el sitio web ***MicrosoftAzureBizTalkHybridListener*** y estar en ejecución. 
 - Este sitio web usa el ***HybridListenerAppPool*** que se ejecuta como cuenta de usuario local integrada de *NetworkService*. Este grupo de aplicaciones también debería iniciarse.
3. En el servidor IIS, confirme que el conector está instalado y ejecutándose: 
 - Se crea un sitio web para el conector del Servicio de aplicaciones. Por ejemplo, si ha creado un conector SQL, hay un sitio web ***MicrosoftSqlConnector\_nnn***. En el Administrador de IIS (inetmgr), confirme que este sitio web se muestra y se inicia. 
 - Este sitio web usa su propio grupo de aplicaciones de IIS denominado ***HybridAppPoolnnn***. Este grupo de aplicaciones se ejecuta como la cuenta de usuario local integrada de *NetworkService*. Tanto este sitio web como el conjunto de aplicaciones se deben haber iniciado. 
 - Busque el conector local. Por ejemplo, si el sitio web de su conector usa el puerto 6569, vaya a http://localhost:6569. Un documento predeterminado no está configurado así que se espera un `HTTP Error 403.14 - Forbidden error`.
4. En el firewall, confirme que los puertos TCP que se muestran en este tema están abiertos.
5. Mire el sistema de origen o de destino:
 - Algunos sistemas locales requieren archivos de dependencia adicionales. Por ejemplo, si se conecta a SAP local, algunos archivos de SAP adicionales se deben instalar en el servidor IIS.
 - Compruebe la conectividad con el sistema con la cuenta de inicio de sesión. Por ejemplo, el puerto TCP usado por el sistema debe estar abierto, como el puerto 1433 para SQL Server. La cuenta de inicio de sesión que escribió en el Portal de Azure debe tener acceso al sistema.
6. En el servidor IIS, compruebe los registros de eventos para ver si hay errores. 
7. Limpie y vuelva a instalar el Administrador de conexiones híbridas: 
 - En IIS, elimine manualmente el sitio web del conector y su grupo de aplicaciones. 
 - Vuelva a ejecutar el Administrador de conexiones híbridas y confirme que especifica la **Cadena de configuración principal** correcta para el conector.



### En el Portal de Azure clásico

1. Confirme que el espacio de nombres del Bus de servicio tiene un estado **Activo**.
2. Al crear el conector, escriba la cadena de conexión de SAS del Bus de servicio. No escriba la cadena de conexión de ACS.


## P+F

**Pregunta**: hay dos administradores de conexiones híbridas. ¿Cuál es la diferencia?<br/> **Respuesta**: está la tecnología de [conexiones híbridas](../biztalk-services/integration-hybrid-connection-overview.md) que usan principalmente las aplicaciones web (antes sitios web) y las aplicaciones móviles (antes servicios móviles) para conectarse al entorno local. Este Administrador de conexiones híbridas tiene su propio [programa de instalación](../biztalk-services/integration-hybrid-connection-create-manage.md) y usa un servicio de BizTalk de Azure (en segundo plano). Admite únicamente los protocolos TCP y HTTP.

Con los conectores del Servicio de aplicaciones de Azure, también contamos con un Administrador de conexiones híbridas. Este Administrador de conexiones híbridas *no* usa un servicio de BizTalk de Azure (en segundo plano) y admite más protocolos además de TCP y HTTP. Consulte la [Lista de conectores y aplicaciones de API](app-service-logic-connectors-list.md).

Ambos usan el Bus de servicio de Azure para conectarse al sistema local.

¿**Pregunta**: al crear una aplicación de API personalizada, ¿puedo usar el Administrador de conexiones híbridas del Servicio de aplicaciones para conectar con el entorno local? <br/> **Respuesta**: no, en el sentido tradicional. Puede usar un conector integrado y configurar el Administrador de conexiones híbridas del Servicio de aplicaciones para conectarse al sistema local. Luego, use este conector con su aplicación de API personalizada, mediante una aplicación lógica posiblemente. Actualmente, no puede desarrollar o crear su propia aplicación de API híbrida (como el conector de SQL o el conector de archivos).

Si la API personalizada usa un puerto TCP o HTTP, puede usar [Conexiones híbridas](../biztalk-services/integration-hybrid-connection-overview.md) y su Administrador de conexiones híbridas. En este escenario, se usa un servicio de BizTalk de Azure. [Conectarse a un servidor SQL Server local desde una aplicación web](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md) puede servir de ayuda.


## Más información.

[Supervisión de las aplicaciones de lógica](app-service-logic-monitor-your-logic-apps.md)<br/> [Precios de Service Bus](https://azure.microsoft.com/pricing/details/service-bus/)



[SB_ConnectInfo]: ./media/app-service-logic-hybrid-connection-manager/SB_ConnectInfo.png
[SB_SAS]: ./media/app-service-logic-hybrid-connection-manager/SB_SAS.png
[PrimaryConfigString]: ./media/app-service-logic-hybrid-connection-manager/PrimaryConfigString.png
[HCMFlow]: ./media/app-service-logic-hybrid-connection-manager/HCMFlow.png
[2]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-hybrid-connection-manager/HybridSetup.jpg
[4]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupComplete.jpg

 

<!---HONumber=AcomDC_0224_2016-->