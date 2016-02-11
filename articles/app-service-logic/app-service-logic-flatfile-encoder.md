<properties 
   pageTitle="Uso del Codificador de archivos sin formato de BizTalk en una aplicación lógica | Microsoft Azure" 
   description="Conector o aplicación de API del Codificador de archivos sin formato de BizTalk" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic" 	
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="01/19/2016"
   ms.author="rajram"/>

# Codificador de archivos sin formato de BizTalk

Use el conector Codificador de archivos sin formato de BizTalk para interoperar entre datos de archivos sin formato (por ejemplo, un archivo Excel o CSV) y datos XML. En este sentido, puede convertir una instancia de archivo sin formato a XML y viceversa.

##Uso del Codificador de archivos sin formato de BizTalk
Para usar el Codificador de archivos sin formato de BizTalk, primero debe crear una instancia de la aplicación de API del Codificador de archivos sin formato de BizTalk. Esta operación puede realizarse en línea, mediante la creación de una aplicación lógica, o bien seleccionando la aplicación de API del Codificador de archivos sin formato de BizTalk en Azure Marketplace. Estos son los pasos para crear una aplicación de API de Codificador de archivos sin formato de BizTalk desde Azure Marketplace: 1. Inicie sesión en el Portal de Azure (http://portal.azure.com). 2. Seleccione Nuevo > Marketplace > Todo. 3. Busque "Codificador de archivos sin formato de BizTalk" en el cuadro de búsqueda. 4. Seleccione el Codificador de archivos sin formato de BizTalk de la lista de resultados. 5. Seleccione "Crear" y proporcione un nombre y los demás detalles necesarios. 6. Seleccione "Crear". Se le redirigirá a la página de inicio, donde puede ver el progreso de creación. Esta operación puede tardar varios minutos en completarse. Recibirá una notificación cuando lo haga.

###Configuración del Codificador de archivos sin formato de BizTalk
Después de crear la aplicación de API, puede ejecutarla directamente desde la página de inicio del Portal de Azure o desde la superficie del diseñador al crear una aplicación lógica.

Para iniciarla desde la página de inicio de Azure puede buscarla si escribe el nombre que asignó al Codificador de archivos sin formato de BizTalk cuando se creó. Para ello, siga estos pasos: 1. Escriba el nombre del Codificador de archivos sin formato de BizTalk en el cuadro de búsqueda en el Portal de Azure y haga clic en Buscar. 2. Luego, seleccione su Codificador de archivos sin formato de BizTalk de la lista. Se abre la hoja Aplicación de API donde puede configurar la aplicación de API del Codificador de archivos sin formato de BizTalk. Para iniciar la configuración, puede agregar un esquema; para ello, siga estos pasos. 1. Seleccione el componente "Esquemas". ![Elemento Esquemas del Codificador de archivos sin formato de BizTalk][2] 2. Luego, seleccione "Agregar nuevo" en la hoja Esquemas que se abre ![Lista de acciones del Codificador de archivos sin formato de BizTalk][7] 3. Seleccione una de las tres opciones para proporcionar el esquema. Las opciones son CARGAR NUEVO ESQUEMA, GENERAR DESDE JSON Y GENERAR DESDE ARCHIVO SIN FORMATO. ![Lista de acciones del Codificador de archivos sin formato de BizTalk][8] 4. Siga los pasos para proporcionar el esquema, según la selección realizada en el paso anterior. Verá que el esquema se ha cargado: ![Lista de acciones del Codificador de archivos sin formato de BizTalk][9]

###Uso del Codificador de archivos sin formato de BizTalk en la superficie de diseño
Ahora que ha configurado el Codificador de archivos sin formato de Biztalk, es hora de usarlo en una aplicación lógica. Para empezar, cree una nueva aplicación lógica (nosotros iniciamos una existente que ha creado con anterioridad y luego seguimos estos pasos): 1. En la ficha "Iniciar lógica", haga clic en "Ejecutar esta lógica manualmente". 2. Seleccione la aplicación de API del Codificador de archivos sin formato de BizTalk que creó anteriormente en la galería (encontrará el Codificador de archivos sin formato de BizTalk que creó en la lista de aplicaciones de API de la parte derecha de la pantalla.). 3. Seleccione la flecha derecha negra. Se presentan las dos acciones disponibles (Archivo sin formato a XML y XML a archivo sin formato): ![Lista de acciones del Codificador de archivos sin formato de BizTalk][1] ![Lista de acciones del Codificador de archivos sin formato de BizTalk][4]

Siga los pasos que se describen a continuación en función de la acción seleccionada.

####Archivo sin formato a XML

![Lista de acciones del Codificador de archivos sin formato de BizTalk][5]

Parámetro|Tipo|Descripción del parámetro
---|---|---
Archivos planos|cadena|Contenido del archivo sin formato de entrada
Nombre del esquema|cadena|Nombre del esquema que representa el archivo sin formato de entrada
Nombre de raíz|cadena|Nombre del nodo raíz del esquema de archivo sin formato


La acción devuelve la salida como una cadena: XML de salida. El XML de salida contiene la representación XML del contenido del archivo sin formato de entrada.

####XML a archivo sin formato

![Lista de acciones del Codificador de archivos sin formato de BizTalk][6]

Parámetro|Tipo|Descripción del parámetro
---|---|---
XML de entrada|cadena|Contenido de XML de entrada

La acción devuelve la salida como una cadena: archivo sin formato. La salida contiene la representación del archivo sin formato del contenido XML de entrada.

<!-- References -->
[1]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ClickToConfigure.PNG
[2]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemasPart.PNG
[3]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemaUpload.PNG
[4]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ListOfActions.PNG
[5]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.FlatFileToXml.PNG
[6]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.XmlToFlatFile.PNG
[7]: ./media/app-service-logic-flatfile-encoder/flatfileencoder.addschema.PNG
[8]: ./media/app-service-logic-flatfile-encoder/flatfileencoder.selectschemauploadoption.PNG
[9]: ./media/app-service-logic-flatfile-encoder/flatfileencoder.shemauploaded.PNG

 

<!---HONumber=AcomDC_0121_2016-->