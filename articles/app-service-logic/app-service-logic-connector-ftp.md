<properties
	pageTitle="Uso del conector de FTP en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
	description="Creación y configuración del conector de FTP o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
	authors="rajram"
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
	ms.date="11/30/2015"
	ms.author="rajram"/>

# Introducción al conector de FTP y su incorporación a su aplicación lógica
Conéctese a un servidor FTP para mover datos o archivos. Las características clave del conector de FTP incluyen:

- Extracción de archivos del servidor FTP a petición
- Ejecución de sondeos según una programación configurable
- Sondeo del servidor FTP y desencadenamiento del flujo de lógica según los documentos nuevos en el servidor FTP
- Especificación del servidor FTP como dirección IP, puerto, contraseña y nombre de host
- Capacidad para ejecutar envíos a petición
- Capacidad de eliminar archivos en el servidor FTP a petición

Puede agregar el conector de FTP a sus datos de flujo de trabajo empresarial y datos de proceso como parte de este flujo de trabajo en una aplicación lógica.

## Creación de un nuevo conector de FTP
Para crear un nuevo conector de FTP, siga los pasos que se mencionan a continuación. -Inicie el portal de Azure - Abra Azure Marketplace con +Nuevo (en la parte inferior de la página) -> Web+móvil--> Azure Marketplace: ![Inicio de Azure Marketplace][1]

- Haga clic en Aplicaciones de la API.
- Busque FTP y seleccione el conector de FTP: ![Selección del Conector de FTP][2]

- Haga clic en Crear.
- En la hoja del conector de FTP que se abre, proporcione los siguientes datos: ![Creación del Conector de FTP][3]

- **Ubicación**: elija la ubicación geográfica en la que desea implementar el conector.
- **Suscripción**: elija una suscripción en la que desee crear este conector.
- **Grupo de recursos**: seleccione o cree un grupo de recursos en el que vaya a estar el conector.
- **Plan de hospedaje web**: seleccione o cree un plan de hospedaje web.
- **Nivel de precios**: elija un nivel de precios para el conector.
- **Nombre**: asigne un nombre al conector de FTP.
- **Configuración del paquete**
	- **Dirección del servidor**: especifique el nombre del servidor FTP o la dirección IP.
	- **Nombre de usuario**: especifique el nombre de usuario para conectarse al servidor FTP.
	- **Contraseña**: especifique la contraseña para conectarse al servidor FTP.
	- **Carpeta raíz**: especifique una ruta de acceso a la carpeta raíz.
	- **Usar archivo binario**: especifique true para usar el modo de transferencia de archivos binarios y false para ASCII.
	- **Usar SSL**: especifique true para usar FTP a través del canal SSL/TLS seguro
	- **Puerto del servidor**: especifique el nombre de puerto del servidor FTP.
- Haga clic en Crear. Se creará un nuevo conector de FTP.

## Uso del conector de FTP en la aplicación lógica
Una vez que se haya creado el conector de FTP, se puede consumir desde el flujo.

Cree un nuevo flujo a través de +Nuevo -> Web+Móvil -> LogicApp. Proporcione los metadatos para el flujo, incluido el grupo de recursos: ![Creación de la aplicación lógica][4]

Haga clic en *Desencadenadores y acciones*. Se abrirá el diseñador de flujos: ![Diseñador de flujo vacío de la aplicación lógica][5]

El conector de FTP puede usarse como desencadenador y como acción.

### Desencadenador
En el diseñador de flujos vacío, haga clic en el conector de FTP desde el panel de la galería de la derecha: ![Elección del desencadenador de FTP][6]

El conector FTP tiene un desencadenador - “File Available (Read then Delete)”. Este desencadenador:

- Realiza un sondeo de la ruta de acceso de la carpeta para los archivos nuevos.
- Crea una instancia del flujo de lógica para cada archivo nuevo.
- Elimina el archivo de la ruta de acceso de la carpeta después de que se ha creado una instancia del flujo de lógica.

Haga clic en el desencadenador "File Available (Read then Delete)": ![Desencadenador de FTP de entradas básicas][7]

Las entradas le ayudarán a configurar una ruta de acceso a una carpeta concreta que se sondeará en una frecuencia programada. Las entradas básicas son: Frecuencia: especifica la frecuencia del sondeo de FTP - Intervalo: especifica el intervalo de la frecuencia programada - Ruta de carpeta: especifica la ruta de la carpeta en el servidor FTP - Tipo de archivo: especifica si el tipo de archivo es de texto o binario

Al hacer clic en los puntos suspensivos "...", se mostrarán las entradas avanzadas: ![Desencadenador de FTP de entradas básicas][8]

Las entradas avanzadas son: Máscara de archivo: especifica la máscara de archivo mientras realiza el sondeo - Excluir máscara de archivo: especifica las máscaras de archivo que excluir mientras realiza el sondeo.

Proporcione las entradas y haga clic en la marca de verificación para completar la configuración de la entrada: ![Desencadenador de FTP de entradas básicas][9]

Tenga en cuenta que el desencadenador FTP configurado muestra tanto los parámetros configurados de entrada como de salida.

#### Uso de la salida del desencadenador FTP en acciones posteriores
La salida del conector de FTP puede utilizarse como entrada de algunas otras acciones en el flujo.

Puede hacer clic en “...” en el cuadro de diálogo de entrada de la acción y seleccionar la salida de FTP directamente en el cuadro desplegable.

También puede escribir una expresión directamente en el cuadro de entrada de la acción. A continuación se proporciona la expresión de flujo para hacer referencia a la salida del desencadenador de FTP.

	@triggers('ftpconnector').outputs.body.Content

### Acciones
Haga clic en el conector de FTP en el panel derecho. El conector de FTP enumera las acciones admitidas: ![Lista de acciones de FTP][10]

El conector de FTP admite las siguientes acciones:

- **Obtener archivo**: obtiene el contenido de un archivo específico.
- **Cargar archivo**: carga el archivo en la ruta de acceso de la carpeta FTP.
- **Eliminar archivo**: elimina el archivo de la ruta de acceso de la carpeta FTP.
- **Enumerar archivo**: enumera todos los archivos en la ruta de acceso de la carpeta FTP.

Utilicemos el ejemplo Cargar archivo. Haga clic en Cargar archivo.

Las entradas básicas aparecen primero: ![Entradas básicas de la acción Cargar archivo][11]


- **Contenido**: especifique el contenido del archivo que se va a cargar.
- **Codificación de la transferencia de contenido**: especifique ninguna o Base64.
- **Ruta de archivo**: especifica la ruta de acceso del archivo que se va a cargar.

Haga clic en ... para las entradas avanzadas: ![Entradas básicas de la acción Cargar archivo][12]


- **Anexar si existe**: True o False en “Anexar si existe”. Cuando está habilitado, los datos se anexan al archivo (si existe). Cuando está deshabilitado, se sobrescribe el archivo (si existe).
- **Carpeta temporal**: opcional. Si se proporciona, el adaptador cargará el archivo a la «Ruta a la carpeta temporal» y una vez que se realiza la carga, el archivo se moverá a la «Ruta de la carpeta». La «Ruta a la carpeta temporal» debe estar en el mismo disco físico que la «Ruta de la carpeta» para asegurarse de que la operación de mover es atómica. La carpeta temporal solo puede usarse cuando la propiedad «Anexar si existe» está deshabilitada.

Proporcione las entradas y haga clic en la marca de verificación para completar la configuración de la entrada: ![Acción Cargar archivo configurada][13]

El parámetro "Ruta de archivo" se establece en:

	@concat('/Output/',triggers().outputs.body.FileName)

Tenga en cuenta que la acción Cargar archivo de FTP configurada muestra ambos parámetros de entrada, así como parámetros de salida.

#### Uso de los resultados de las acciones anteriores como entrada para la acción de FTP
Tenga en cuenta que en la captura de pantalla configurada, el valor de Contenido se establece en una expresión.

	@triggers().outputs.body.Content


Puede establecerlo en cualquier valor que desee. Esto es solo un ejemplo. La expresión toma el resultado del desencadenador de aplicación lógica y lo utiliza como el contenido del archivo que se va a cargar. Supongamos que desea utilizar la salida de una acción anterior, por ejemplo, transform (transformar). En ese caso, la expresión será:

	@actions('transformservice').outputs.body.OutputXML

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE]Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-ftp/LaunchAzureMarketplace.PNG
[2]: ./media/app-service-logic-connector-ftp/SelectFTPConnector.PNG
[3]: ./media/app-service-logic-connector-ftp/CreateFTPConnector.PNG
[4]: ./media/app-service-logic-connector-ftp/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-ftp/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-ftp/ChooseFTPTrigger.PNG
[7]: ./media/app-service-logic-connector-ftp/BasicInputsFTPTrigger.PNG
[8]: ./media/app-service-logic-connector-ftp/AdvancedInputsFTPTrigger.PNG
[9]: ./media/app-service-logic-connector-ftp/ConfiguredFTPTrigger.PNG
[10]: ./media/app-service-logic-connector-ftp/ListOfFTPActions.PNG
[11]: ./media/app-service-logic-connector-ftp/BasicInputsUploadFile.PNG
[12]: ./media/app-service-logic-connector-ftp/AdvancedInputsUploadFile.PNG
[13]: ./media/app-service-logic-connector-ftp/ConfiguredUploadFile.PNG
 

<!---HONumber=AcomDC_1203_2015-->