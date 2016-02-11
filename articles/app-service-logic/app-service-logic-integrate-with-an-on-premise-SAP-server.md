<properties
	pageTitle="Integración con un servidor SAP local en el Servicio de aplicaciones de Azure | Microsoft Azure"
	description="Aprenda a integrar con un servidor SAP local"
	authors="rajeshramabathiran"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="sameerch"/>


# Integración con un servidor SAP local
Mediante el conector SAP, puede conectar aplicaciones web, móviles y lógicas de Servicios de aplicaciones de Azure al servidor SAP existente. Esto permite invocar RFC, BAPI y tRFC, así como enviar IDocs al servidor SAP, incluso si se sitúa detrás del firewall local.

Si tiene un servidor de SAP local, use un agente de escucha híbrido para establecer la conexión con el conector de SAP como se muestra:

![Flujo de conectividad híbrida][1]

Mientras que un conector de SAP en la nube no se puede conectar directamente a un servidor de SAP detrás de un firewall local, puede utilizar el agente de escucha híbrido para salvar la distancia. Esto se configura hospedando un extremo de retransmisión que permite al conector establecer conectividad con el servidor de SAP de manera segura.


## Diferentes formas de integrar con SAP
Se admiten las siguientes acciones:

- Llamar a RFC
- Llamar a tRFC
- Llamar a BAPI
- Enviar IDoc

## Requisitos previos
Las bibliotecas de cliente específicas de SAP son necesarias en el equipo cliente donde el agente de escucha híbrida está instalado y en ejecución. En la [Guía de instalación del módulo del adaptador BizTalk][9] de la sección titulada **Para el adaptador SAP**.


## Creación de un nuevo conector de SAP
1. Inicie sesión en el Portal de Azure.
2. Seleccione **Nuevo**.
3. En la hoja Crear, seleccione **Equipo** > **Azure Marketplace**.
4. En la hoja Marketplace, seleccione **Aplicaciones de API** y busque SAP en la barra de búsqueda: ![Aplicación de API del conector de SAP][2]
5. Seleccione el **conector SAP** publicado por Microsoft.
6. En la hoja del conector SAP, seleccione **Crear**.
7. En la nueva hoja que se abre, escriba lo siguiente:  
	1. **Ubicación**: elija la ubicación geográfica donde desea implementar el conector.
	2. **Suscripción**: elija una suscripción en la que desee crear este conector.
	3. **Grupo de recursos**: seleccione o cree un grupo de recursos en el que vaya a estar el conector.
	4. **Plan de hospedaje web**: seleccione o cree un plan de hospedaje web.
	5. **Nivel de precios**: elija un nivel de precios para el conector.
	6. **Nombre**: escriba un nombre para el conector SAP
	7. **Configuración del paquete**
		- **Nombre del servidor**: especifique el nombre del servidor SAP. Ejemplo: "SAPserver" o "SAPserver.mydomain.com".
		- **Nombre de usuario**: especifique un nombre de usuario válido para conectarse al servidor SAP.
		- **Contraseña**: especifique una contraseña válida para conectarse al servidor SAP.
		- **Número de sistema**: especifique el número de sistema del servidor de aplicaciones SAP.
		- **Idioma**: especifique el idioma de inicio de sesión, como "ES". Si no escribe ningún valor, se considera "EN".
		- **Local**: especifique si el servidor SAP es local y se encuentra detrás de un firewall o no. Si se establece en TRUE, deberá instalar un agente de escucha en un servidor que puede tener acceso al servidor SAP. Puede ir a la página de resumen de la aplicación de API y seleccione 'Conexión híbrida' para instalar el agente.
		- **Cadena de conexión del Bus de servicio**: especifique este parámetro si el servidor SAP es local. Debe ser una cadena de conexión del espacio de nombres del bus de servicio que sea válida.
		- **RFC**: escriba las RFC de SAP a las que el conector puede llamar.
		- **TRFC**: escriba las TRFC de SAP a las que el conector puede llamar.
		- **BAPI**: escriba las BAPI de SAP a las que el conector puede llamar.
		- **IDOC**: escriba las IDOC de SAP que el conector puede enviar.
	8. Elija Seleccionar. Dentro de unos minutos, se crea el conector SAP.


## Instalación del agente de escucha híbrido
Busque el conector SAP creado: **Examinar** > **Aplicaciones de API** > *nombre del conector*.

En la hoja del conector, observe que el estado de la conexión híbrida es pendiente. Seleccione Conexión híbrida. Se abrirá la hoja Conexión híbrida.

![Hoja de conexión híbrida][3]

Copie la cadena de configuración de puerta de enlace principal. Se utilizará más adelante como parte de la configuración del agente de escucha híbrido.

Seleccione el vínculo **Descargar y configurar**. Se abre el instalador ClickOnce.

![Instalador de clic único de conexión híbrida][4]

Seleccione **Instalar** y, a continuación, escriba el valor de configuración de la puerta de enlace principal que copió anteriormente:

![Cadena de conexión de escucha de retransmisión][5]

Seleccione **Instalar** para completar la configuración del Administrador de conexiones híbridas:

![Instalación del administrador de conexión híbrida en curso][6]

![Instalación del administrador de conexión híbrida completada][7]

## Validación de la conexión híbrida
Busque el conector SAP creado: **Examinar** > **Aplicaciones de API** > *nombre del conector*.

En la hoja del conector, observe que el estado de la conexión híbrida es *Conectado*:

![Conexión híbrida conectada][8]


## Uso del conector SAP en aplicaciones lógicas
Una vez creado el conector SAP, se puede usar dentro del flujo de trabajo de aplicaciones lógicas. Para ello, cree una nueva aplicación lógica a través de **Nuevo** > **Aplicaciones lógicas** > **Crear**. Escriba los metadatos para la aplicación lógica, incluido el grupo de recursos.

Seleccione **Desencadenadores y acciones**. Se abrirá el diseñador de flujo de trabajo de aplicaciones lógicas.

Seleccione el conector SAP en el panel de la derecha y seleccione una acción en la pestaña Acciones.

> [AZURE.NOTE]La lista de acciones se basa en la configuración que especificó cuando creó el conector SAP.

Para la acción seleccionada, consulte los parámetros de entrada y salida. Puede especificar las entradas de la acción y usar la salida de la acción actual en otras aplicaciones de API, posiblemente para tomar más decisiones en el flujo de trabajo.

<!--Image references-->
[1]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectivityFlow.PNG
[2]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/SAPConnector.APIApp.PNG
[3]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.PNG
[4]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.ClickOnceInstaller.PNG
[5]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnection.ClickOnceInstaller.RelayInformation.PNG
[6]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectionManager.Install.InProgress.PNG
[7]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/HybridConnectionManager.Install.Completed.PNG
[8]: ./media/app-service-logic-integrate-with-an-on-premise-SAP-server/SAPConnector.HybridConnection.Connected.PNG
[9]: http://www.microsoft.com/download/details.aspx?id=35552

<!---HONumber=AcomDC_1223_2015-->