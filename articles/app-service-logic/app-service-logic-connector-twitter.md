<properties
	pageTitle="Uso del conector de Twitter en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
	description="Creación y configuración del conector de Twitter o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
	services="app-service\logic"
	documentationCenter=".net,nodejs,java"
	authors="anuragdalmia"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="12/17/2015"
   ms.author="sameerch"/>


# Introducción al conector de Twitter y su incorporación a su aplicación lógica
Conéctese a la fuente de Twitter para registrar tweets y recibir tweets de su escala de tiempo, de la escala de tiempo de sus amigos y de los seguidores de su cuenta de Twitter. Los conectores pueden utilizarse en aplicaciones lógicas para recuperar, procesar o insertar datos como parte de un “flujo de trabajo”. El uso del conector de Twitter en su flujo de trabajo le permite conseguir distintos resultados. Por ejemplo, puede:

- Recibir nuevos tweets asociados con una palabra clave o un texto determinados. Cuando se recupera un nuevo tweet, se desencadena una nueva instancia del flujo de trabajo y se pasan los datos al siguiente conector del flujo. Por ejemplo, suponga que crea un conector de Twitter y que usa el desencadenador Nuevo tweet a partir de búsqueda para supervisar #peanutbutterandjelly. Siempre que hay un nuevo tweet para #peanutbutterandjelly, el flujo de trabajo (también conocido como aplicación lógica) se inicia automáticamente.
- Con las distintas acciones, como "Buscar tweets", toma la respuesta y la usa en su flujo de trabajo. Por ejemplo, puede buscar tweets por el nombre de su compañía. Cuando los encuentra, puede usar una aplicación lógica para escribir estos datos en una base de datos SQL Server. Luego, puede usar esos datos de SQL Server para determinar los tweets que se están escribiendo sobre su compañía. 
- Utilice todos los operadores en la [Búsqueda de Twitter](https://twitter.com/search). Seleccione el vínculo **Operadores**. El conector de Twitter admite todos los operadores que aparecen en la lista.


## Acciones y desencadenadores
Los *desencadenadores* son eventos que se producen. Por ejemplo, un nuevo tweet puede desencadenar o iniciar un flujo de trabajo o un proceso. Una *acción* es el resultado de algo. Por ejemplo, puede buscar un tweet específico y, cuando lo encuentre, enviar un correo electrónico o actualizar una base de datos.

El conector de Twitter puede usarse como desencadenador o como acción en una aplicación lógica y es compatible con datos en formato JSON y XML.

El conector de SQL dispone de los siguientes desencadenadores y acciones:

Desencadenadores | Acciones
--- | ---
Nuevo Tweet a partir de búsqueda | <ul><li>Obtener escala de tiempo de usuario</li><li>Buscar tweets</li><li>Twittear</li><li>Obtener escala de tiempo de menciones</li><li>Obtener escala de tiempo de inicio</li><li>Obtener seguidores</li><li>Obtener amigos</li><li>Obtener detalles de usuario</li><li>Twittear a un usuario</li><li>Enviar mensaje directo</li></ul>

El desencadenador **Nuevo tweet** se ha archivado. Actualmente, sigue estando disponible como una operación avanzada y se puede usar. La acción **Retwittear** se ha eliminado y ya no se admite. Si usa esta acción, se produce un error en tiempo de ejecución. Como consecuencia, quite la acción de retwitteo de las aplicaciones lógicas.


## Creación del conector de Twitter

> [AZURE.IMPORTANT]Al crear un conector de Twitter, puede elegir registrar su propia aplicación con Twitter y usar las claves de aplicación con el conector de Twitter. Puede registrar una aplicación de forma gratuita en [http://apps.twitter.com](http://apps.twitter.com). Durante el registro, asegúrese de que tiene que proporciona alguna dirección URL de devolución de llamada. Podría cambiar la dirección URL de devolución de llamada una vez creado el conector de Twitter. Necesitará la clave y el secreto de API de Twitter para crear un conector.

Un conector puede crearse dentro de una aplicación lógica o directamente desde Azure Marketplace. Para crear un conector desde Marketplace:

1. [Opcional] Cree una aplicación gratuita de Twitter en [http://apps.twitter.com](http://apps.twitter.com).
    * Al registrarse para la aplicación, puede colocar cualquier dirección URL para el sitio web. Especifique cualquier dirección URL de devolución de llamada (no la deje en blanco); podrá actualizarla más adelante.
2. En el panel de inicio de Azure, seleccione **Marketplace**.
3. Busque "Conector de Twitter", selecciónelo y seleccione **Crear**.
4. [Opcional] Haga clic en 'Configuración del paquete' y pegue la 'clave consumidor' de su aplicación de Twitter en el campo 'clientId'. Pegue el 'secreto de consumidor' de su aplicación de Twitter en el campo 'clientSecret':
	![][10]
5. Especifique otra configuración necesaria en el nombre del conector, el servicio de la aplicación y el grupo de recursos.
6.	Haga clic en **Crear**.

> [AZURE.NOTE]Si quiere proteger aún más su API de Twitter con la dirección URL de redireccionamiento, puede usar la [seguridad de OAUTH](app-service-logic-oauth-security.md).


## Uso del conector Twitter en su aplicación lógica
Una vez creada la aplicación de API, ahora puede usar el conector de Twitter como desencadenador o como acción en las aplicaciones lógicas. Para ello, siga estos pasos:

1.	Cree una nueva aplicación lógica o abra una existente: 
	![][2]
2.	Abra **Desencadenadores y acciones** para abrir el diseñador de aplicaciones lógicas: 
	![][3]
3.	El conector de Twitter aparece en el lado derecho. Selecciónelo para agregarlo automáticamente a su aplicación lógica: 
	![][4]
4.	Seleccione **Autorizar**, escriba sus credenciales de Twitter y seleccione **Autorizar aplicación**: 
	![][5]


Ahora puede configurar el conector de Twitter para compilar el flujo de trabajo. Puede usar los tweets recuperados del desencadenador de Twitter en otras acciones del flujo: 
	![][6]

De igual forma, puede usar las acciones de Twitter en el flujo. Seleccione una acción de Twitter y configure las entradas para esa acción: 
	![][7] 
	![][8]

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE]Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-twitter/img1.png
[2]: ./media/app-service-logic-connector-twitter/img2.png
[3]: ./media/app-service-logic-connector-twitter/img3.png
[4]: ./media/app-service-logic-connector-twitter/img4.png
[5]: ./media/app-service-logic-connector-twitter/img5.png
[6]: ./media/app-service-logic-connector-twitter/triggers.png
[7]: ./media/app-service-logic-connector-twitter/img7.png
[8]: ./media/app-service-logic-connector-twitter/actions.png
[9]: ./media/app-service-logic-connector-twitter/settings.PNG
[10]: ./media/app-service-logic-connector-twitter/TwitterAPISettings.png

<!---HONumber=AcomDC_1223_2015-->