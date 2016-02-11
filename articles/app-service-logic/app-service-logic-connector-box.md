<properties
   pageTitle="Uso del conector de Box en Aplicaciones lógicas | Servicio de aplicaciones de Microsoft Azure"
   description="Creación y configuración del conector de Box o la aplicación de API y su uso en una aplicación lógica en Servicio de aplicaciones de Azure"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="11/11/2015"
   ms.author="rajram"/>

# Introducción al conector de Box y su incorporación a su aplicación lógica 
Conéctese a Box para obtener, cargar, eliminar, etc. archivos. Los conectores se usan en Aplicaciones lógicas como parte de un "flujo de trabajo".

Puede tener escenarios donde sea necesario trabajar con Box, que le permite compartir datos de forma segura con cualquier persona, incluso si están ubicados fuera del firewall. Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo.


## Acciones y desencadenadores
La aplicación de la galería Box proporciona acciones en forma de mecanismos para la interacción con Box:

**Acciones**: las acciones permiten realizar acciones predefinidas en la cuenta de Box configurada con la aplicación lógica. A continuación se muestran las acciones que se pueden realizar en la cuenta de Box mediante el conector de Box:

a. *Enumerar archivos:* esta operación devolverá la información de todos los archivos de una carpeta. Lista de los parámetros necesarios para la acción:

Nombre de parámetro | Descripción | Obligatorio
--- | --- | ---
Ruta de acceso a la carpeta | Ruta de acceso de la carpeta para enumerar. | Sí

> [AZURE.NOTE]No devuelve ningún contenido del archivo.

b. *Obtener archivo:* esta operación recupera un archivo que incluye su contenido y propiedades. Lista de los parámetros necesarios para la acción:

Nombre de parámetro | Descripción | Obligatorio
--- | --- | ---
Ruta de acceso de archivo | Ruta de acceso de la carpeta donde reside el archivo. | Sí
Tipo de archivo | Especifica si el archivo es texto o binario. | No

> [AZURE.NOTE]Esta operación no elimina el archivo después de su lectura.


c. *Cargar archivo*: como sugiere su nombre, esta acción carga el archivo en la cuenta de Box. Si ya existe el archivo, no se sobrescribe y se produce un error. Lista de los parámetros necesarios para la acción:

Nombre de parámetro | Descripción | Obligatorio
--- | --- | ---
Ruta de acceso de archivo | La ruta de acceso al archivo. | Sí
Contenido del archivo | El contenido del archivo que se va a cargar. | Sí
Codificación de transferencia de contenido | Tipo de codificación del contenido, que puede ser Base64 o ninguna. | 

d. *Eliminar archivo*: la acción elimina el archivo especificado de una carpeta. Se produce una excepción si la carpeta o el archivo no se encuentra. Lista de los parámetros necesarios para la acción:

Nombre de parámetro | Descripción | Obligatorio
--- | --- | ---
Ruta de acceso de archivo | Ruta de acceso de archivo completa, incluidas las carpetas. | Sí


## Creación de un conector de Box para la aplicación lógica

Un conector puede crearse dentro de una aplicación lógica o directamente desde Azure Marketplace. Para crear un conector desde Marketplace:

1. En el panel de inicio de Azure, seleccione **Marketplace**.
2. Busque "Conector de Box", selecciónelo y seleccione **Crear**.
3. Escriba el nombre, el plan de Servicio de aplicaciones y otras propiedades: 

	![][1]
4. Seleccione **Crear**.


## Uso del conector de Box en la aplicación lógica

Una vez creada la aplicación de API, puede usar el conector de Box como acción en la aplicación lógica. Para ello, siga estos pasos:

1. En la aplicación lógica, abra **Desencadenadores y acciones** para abrir el diseñador de Aplicaciones lógicas y configure el flujo. El conector de Box se muestra en la galería. Selecciónelo para agregarlo automáticamente al diseñador de Aplicaciones lógicas.

	> [AZURE.NOTE]Si se selecciona el conector de Box al principio de la aplicación lógica, actúa como desencadenador. De lo contrario, podrían realizarse acciones en la cuenta de Box mediante el conector. El conector de Box no tiene ningún desencadenador en el momento en que se redacta este artículo.

2. Autentique y autorice las aplicaciones lógicas para realizar operaciones en su nombre. Seleccione **Autorizar** en el conector de Box: 
	![][2]

3. Proporcione los detalles de inicio de sesión de la cuenta de Box en la que desea realizar las operaciones: 
	![][3]

4. Conceda acceso a su cuenta a las aplicaciones lógicas para llevar a cabo la operación en su nombre: 
	![][4]

5. Se muestra la lista de acciones y puede elegir la operación apropiada que desea realizar: 
	![][5]

## Aplicaciones adicionales del conector
Una vez creado el conector, puede agregarlo a un flujo de trabajo empresarial mediante una aplicación lógica. Consulte [¿Qué son las aplicaciones lógicas?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE]Si desea empezar a trabajar con las aplicaciones lógicas de Azure antes de registrarse para obtener una cuenta de Azure, vaya a [Prueba de aplicaciones lógicas](https://tryappservice.azure.com/?appservice=logic), donde podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Consulte la referencia de API de REST de Swagger en [Referencia de conectores y aplicaciones de API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede consultar las estadísticas de rendimiento y la seguridad de control para el conector. Consulte [Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-box/image_0.jpg
[2]: ./media/app-service-logic-connector-box/image_1.jpg
[3]: ./media/app-service-logic-connector-box/image_2.jpg
[4]: ./media/app-service-logic-connector-box/image_3.jpg
[5]: ./media/app-service-logic-connector-box/image_4.jpg

<!---HONumber=Nov15_HO3-->