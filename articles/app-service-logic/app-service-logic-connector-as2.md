<properties 
   pageTitle="Uso del conector AS2 en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure" 
   description="Creación y configuración del conector AS2 o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="02/18/2016"
   ms.author="rajram"/>

# Introducción al conector AS2 y su incorporación a una aplicación lógica
>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas.

El conector AS2 se usa para recibir y enviar mensajes a través del protocolo de transporte AS2 (Applicability Statement 2) en comunicaciones de negocio a negocio. Los datos se transportan de forma segura y confiable a través de Internet. La seguridad se consigue mediante el uso de cifrado y certificados digitales.

El conector AS2 se puede agregar a los datos de flujos de trabajo y de procesos empresariales como parte de un flujo de trabajo de negocio a negocio dentro de una aplicación lógica.

## Acciones y desencadenadores
Un desencadenador inicia una nueva instancia en función de un suceso específico, como la llegada de un mensaje AS2 de un socio. Una acción es el resultado, por ejemplo, después de recibir un mensaje AS2 enviarlo mediante AS2.

El conector AS2 puede usarse como un desencadenador o una acción en una aplicación lógica y es compatible con los datos en formato JSON y XML. El conector AS2 dispone de los desencadenadores y las acciones siguientes:

Desencadenadores | Acciones
--- | ---
Recibir y descodificar | Codificar y enviar

## Requisitos de inicio
El usuario debe crear los siguientes elementos para que el conector AS2 pueda utilizarlos:

Requisito | Descripción
--- | ---
Aplicación de API TPM | Antes de crear un conector AS2, debe crear un [conector de administración de socios comerciales de BizTalk][1]. <br/><br/>**Nota** Conozca el nombre de la aplicación de API TPM. 
Base de datos SQL de Azure | Almacena elementos B2B, lo que incluye socios, esquemas, certificados y acuerdos. Cada una de las aplicaciones de API de B2B requiere su propia base de datos de SQL Azure. <br/><br/>**Nota** Copie la cadena de conexión en esta base de datos.<br/><br/>[Creación de una base de datos SQL Azure](../sql-database/sql-database-get-started.md)
Contenedor de Almacenamiento de blobs de Azure | Almacena las propiedades de los mensajes cuando está habilitado el archivado AS2. Si no se necesita el archivado de mensajes de AS2, no hace falta un contenedor de almacenamiento. <br/><br/>**Nota** Si va a habilitar el archivado, copie la cadena de conexión en este almacenamiento de blobs.<br/><br/>[Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md).

## Creación del conector AS2

Un conector puede crearse dentro de una aplicación lógica o directamente desde Azure Marketplace. Para crear un conector desde Marketplace:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. Busque "Conector AS2", selecciónelo y seleccione **Crear**.
3. Escriba el nombre, el plan del Servicio de aplicaciones y otras propiedades.
4. Escriba la siguiente configuración del paquete:

	Propiedad | Descripción
--- | --- 
Cadena de conexión de base de datos | Escriba la cadena de conexión de ADO.NET para la Base de datos SQL de Azure que ha creado. Cuando copie la cadena de conexión, la contraseña no se agregará a esta. Asegúrese de escribir la contraseña en la cadena de conexión antes de pegarla.
Habilitación del archivado para los mensajes entrantes | Opcional. Habilite esta propiedad para almacenar las propiedades de mensaje de un mensaje AS2 entrante recibido de un socio. 
Cadena de conexión de Almacenamiento de blobs de Azure | Escriba la cadena de conexión al contenedor de Almacenamiento de blobs de Azure que ha creado. Cuando el archivado está habilitado, los mensajes codificados y descodificados se almacenan en este contenedor de almacenamiento.
Nombre de la instancia de TPM | Escriba el nombre de la aplicación de API de **Administración de socios comerciales de BizTalk** que ha creado anteriormente. Cuando crea el conector AS2, este conector ejecuta solo los acuerdos de AS2 que contiene esta instancia específica de TPM.

5. Seleccione **Crear**.

Los socios comerciales son las entidades implicadas en comunicaciones B2B (negocio a negocio). Cuando dos asociados establecen una relación, esto se conoce como contrato. El contrato definido se basa en la comunicación que los dos socios desean lograr y es específico del protocolo o el transporte.

Vea los pasos para [crear un contrato de socio comercial][2].

## Uso del conector como desencadenador

1. Al crear o editar una aplicación lógica, seleccione en el panel derecho el conector AS2 que creó: ![Configuración del desencadenador][3]

2. Haga clic en la flecha derecha →: ![Opciones del desencadenador][4]

3. El conector AS2 expone un solo desencadenador. Seleccione *Recibir y descodificar*: ![Entrada de Recibir y descodificar][5]

4. Este desencadenador no tiene ninguna entrada. Haga clic en la flecha derecha →: ![Configuración de Recibir y descodificar][6]

Como parte del resultado, el conector devuelve la carga de AS2, así como los metadatos específicos de AS2.

El desencadenador se activa cuando una carga de AS2 es un COMENTARIO a https://{Host URL}/decode. Puede encontrar la dirección URL de Host en la configuración de la aplicación de API. También deberá cambiar el nivel de acceso de la aplicación de API en Configuración de la aplicación a Público (autenticado o anónimo).

## Uso del conector como acción
1. Después del desencadenador (o de elegir "Ejecutar esta lógica manualmente"), agregue en el panel derecho el conector AS2 que creó: ![Configuración de la acción][7]

2. Haga clic en la flecha derecha →: ![Lista de acciones][8]

3. El conector AS2 solo admite una acción. Seleccione *Codificar y enviar*: ![Entrada de Codificar y enviar][9]

4. Especifique las entradas de la acción y configúrela: ![Codificar y enviar configurados][10]

	Los parámetros son:

	Parámetro | Tipo | Descripción
--- | --- | ---
Carga | objeto| El contenido de la carga para codificar y registrar en el extremo configurado. La carga debe suministrarse como un objeto JSON.
AS2 desde | cadena | La identidad de AS2 del remitente del mensaje AS2. Este parámetro se utiliza para buscar el acuerdo para enviar el mensaje.
AS2 hasta | cadena | La identidad de AS2 del destinatario del mensaje AS2. Este parámetro se utiliza para buscar el acuerdo para enviar el mensaje.
URL de asociado | cadena | El extremo del socio al que debe enviarse el mensaje.
Habilitar archivado | boolean | Determina si se debe archivar el mensaje saliente.

La acción devuelve un código de respuesta HTTP 200 de finalización satisfactoria.

## Aplicaciones adicionales del conector
También puede [archivar los mensajes AS2](app-service-logic-archive-as2-messages.md).

Encontrará más información sobre las aplicaciones lógicas en [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Si desea empezar a usar Aplicaciones lógicas de Azure antes de suscribirse para obtener una cuenta de Azure, [pruebe Aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic). Podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-as2/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-as2/TriggerOptions.PNG
[5]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeInput.PNG
[6]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeConfigured.PNG
[7]: ./media/app-service-logic-connector-as2/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-as2/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-as2/EncodeAndSendInput.PNG
[10]: ./media/app-service-logic-connector-as2/EncodeAndSendConfigured.PNG

<!---HONumber=AcomDC_0224_2016-->