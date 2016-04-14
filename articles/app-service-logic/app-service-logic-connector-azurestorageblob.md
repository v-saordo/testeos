<properties 
   pageTitle="Uso del conector de blobs de almacenamiento de Azure en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure" 
   description="Creación y configuración del conector del blob de almacenamiento de Azure o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="02/11/2016"
   ms.author="rajram"/>
   
# Introducción al conector del blog de almacenamiento de Azure y su incorporación a su aplicación lógica 

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas. Para la versión de esquema 2015-08-01-preview, haga clic en [API de blobs de Almacenamiento de Azure](../connectors/create-api-azureblobstorage.md).

Conecte con el blob de almacenamiento de Azure para cargar, descargar y eliminar blobs del contenedor de blobs. Los conectores se usan en Aplicaciones lógicas como parte de un "flujo de trabajo".

## Acciones y desencadenadores
Los *desencadenadores* son eventos que se producen. Por ejemplo, cuando se actualiza un pedido o cuando se agrega un cliente nuevo. Un *acción* es el resultado del desencadenador. Por ejemplo, cuando se actualiza un pedido,se envía una alerta al vendedor. O bien, cuando se agrega un nuevo cliente, a este se le envía un correo electrónico de bienvenida.

El conector de Blob de almacenamiento puede usarse como acción en una aplicación lógica y es compatible con datos en formato JSON y XML. Actualmente, no hay ningún desencadenador para el conector de Blob de almacenamiento.

El conector de Blob de almacenamiento dispone de los siguientes desencadenadores y acciones:

Desencadenadores | Acciones
--- | ---
None | <ul><li>Obtener blob: para obtener un blob específico del contenedor</li><li>Cargar blob: para cargar un nuevo blob o actualizar uno existente</li><li>Eliminar blob: para eliminar un blob de un contenedor</li><li>Mostrar blobs: para mostrar todos los blobs de un directorio</li><li>Blob de instantánea: para crear una instantánea de solo lectura de un blob concreto</li><li>Copiar blob: para crear un nuevo blob a partir de la copia de otro. El blob de origen puede estar en la misma cuenta o en otra cuenta.</li></ul>


## Creación del conector de Blob de almacenamiento de Azure

Un conector puede crearse dentro de una aplicación lógica o directamente desde Azure Marketplace. Para crear un conector desde Marketplace:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. Busque “Blob”: ![Selección del conector del blobs de almacenamiento de Azure][2]

3. Selecciónelo y elija **Crear**.
4. Escriba el nombre, el plan del Servicio de aplicaciones y otras propiedades.
5. Escriba la siguiente configuración del paquete:

	Nombre | Obligatorio | Descripción
--- | --- | ---
URI del contenedor/SAS | Sí | Escriba el URI del contenedor de blobs. El URI también puede incluir también el token SAS. Por ejemplo, escriba http://*storageaccountname*.blob.core.windows.net/containername o http://*storageaccountname*.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah
Clave de acceso | No | Escriba una clave de acceso válida a la cuenta de almacenamiento principal o secundaria. Deje este campo vacío si usa un token de SAS para la autenticación.

	![Creación del conector del blobs de almacenamiento de Azure][3]

6. Haga clic en **Crear**.

## Uso del conector de blobs de almacenamiento de Azure en la aplicación lógica
Una vez que ha creado el conector de Blob de almacenamiento de Azure, puede agregarlo al flujo de trabajo.

1. Cree una nueva aplicación de lógica: Nuevo -> Web y Móvil -> Aplicación lógica. Establezca las propiedades de la aplicación lógica: ![Creación de la aplicación lógica][4]

2. Haga clic en **Desencadenadores y acciones**. Se abre el diseñador del flujo de trabajo: ![Diseñador de flujo vacío de la aplicación lógica][5]

3. En el panel derecho, seleccione el conector de Blob de almacenamiento de Azure. El conector muestra las acciones disponibles: ![Lista de acciones de blobs de almacenamiento de Azure][10]

4. En este escenario, vamos a usar la acción **Cargar blob**: ![Entradas de acción Cargar blob][11]

5. Escriba los valores de entrada y seleccione la marca de verificación para completar la configuración:

	Entrada | Descripción
--- | ---
Ruta del blob | Determina la ruta de acceso del blob que se va a cargar. La ruta de acceso se interpreta en relación con la ruta de acceso del contenedor configurado.
Contenido de escritura de blob | Escriba el contenido y las propiedades del blob que se va a cargar.
Codificación de transferencia de contenido | Especifique ninguno o Base64.
Sobrescribir | Cuando está establecido en true, el blob existente se sobrescribe. Cuando se establece en false, devuelve un error si ya existe un blob en la misma ruta de acceso.

Tenga en cuenta que la acción Cargar blob del blob de almacenamiento de Azure configurada muestra ambos parámetros de entrada, así como parámetros de salida.

#### Uso de las salidas de las acciones anteriores como entrada para las acciones de blobs de almacenamiento de Azure
En la captura de pantalla anterior, el valor de **Contenido** puede ser una expresión:

	@triggers().outputs.body.Content

Puede establecerlo en cualquier valor que desee. La expresión toma el resultado del desencadenador de aplicación lógica y lo utiliza como el contenido del archivo que se va a cargar. Por ejemplo, desea usar el resultado de una transformación. En ese caso, la expresión sería:

	@actions('transformservice').outputs.body.OutputXML

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[2]: ./media/app-service-logic-connector-azurestorageblob/SelectAzureStorageBlobConnector.PNG
[3]: ./media/app-service-logic-connector-azurestorageblob/CreateAzureStorageBlobConnector.PNG
[4]: ./media/app-service-logic-connector-azurestorageblob/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-azurestorageblob/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-azurestorageblob/ChooseBlobAvailableTrigger.PNG
[7]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsBlobAvailableTrigger.PNG
[8]: ./media/app-service-logic-connector-azurestorageblob/AdvancedInputsBlobAvailableTrigger.PNG
[9]: ./media/app-service-logic-connector-azurestorageblob/ConfiguredBlobAvailableTrigger.PNG
[10]: ./media/app-service-logic-connector-azurestorageblob/ListOfAzureStorageBlobActions.PNG
[11]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsUploadBlob.PNG
 

<!---HONumber=AcomDC_0224_2016-->