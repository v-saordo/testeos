<properties
	pageTitle="Incorporación de Microsoft Translator a PowerApps Enterprise o aplicaciones lógicas | Microsoft Azure"
	description="Información general de la API de Microsoft Translator con los parámetros de la API de REST"
	services=""
    suite=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/25/2016"
   ms.author="mandia"/>

# Introducción a la API de Microsoft Translator
Conéctese a Microsoft Translator para traducir el texto, detectar un idioma, etc. La API de Microsoft Translator pueden usarse desde:

- PowerApps 
- Aplicaciones lógicas 

Con Microsoft Translator, puede:

- Compilar el flujo de negocio en función de los datos que obtiene de Microsoft Translator. 
- Usar acciones para traducir texto, detectar un idioma, etc. Estas acciones obtienen una respuesta y luego dejan el resultado a disposición de otras acciones. Por ejemplo, cuando se crea un nuevo archivo en Dropbox, puede convertir el texto del archivo a otro idioma mediante Microsoft Translator.
- Agregue la API de Microsoft Translator a PowerApps Enterprise. Así los usuarios pueden usar esta API en sus aplicaciones. 

Para obtener información acerca de cómo agregar una API en PowerApps Enterprise, vaya a [Registro de una API administrada por Microsoft o una API administrada por TI](../power-apps/powerapps-register-from-available-apis.md).

Para agregar una operación a las aplicaciones lógicas, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Desencadenadores y acciones
Microsoft Translator incluye las siguientes acciones. No hay desencadenadores.

Desencadenadores | Acciones
--- | ---
None | <ul><li>Detectar idioma</li><li>Texto a voz</li><li>Traducir texto</li><li>Obtener idiomas</li><li>Obtener idiomas de voz</li></ul>

Todas las API admiten datos en formato JSON y XML.

## Creación de la conexión a Microsoft Translator

### Incorporación de una configuración adicional en PowerApps
Al agregar Microsoft Translator a PowerApps Enterprise, escriba los valores de **Id. de cliente** y **Secreto de cliente** de la aplicación de Microsoft Translator. Si no tiene una aplicación de traducción, puede crear una:

1. Vaya a la [página del programador de Azure Data Market][5] e inicie sesión con su Cuenta de Microsoft. 

2. Seleccione **Registrar su aplicación**:

	1. Escriba un valor en **Id. de cliente**.
	2. Escriba el **nombre** de la aplicación.
	3. Escriba un valor ficticio en la **URL de redireccionamiento**. Por ejemplo, escriba: *https://contosoredirecturl*.
	4. Escriba una **descripción**.
	5. Seleccione **Crear**.  

	![Registrar su aplicación][6]

Ahora, copie y pegue los valores de **Id. de cliente** y **Secreto de cliente** en la configuración de Translator en el Portal de Azure.


## Referencia de la API de REST de Swagger
Se aplica a la versión: 1.0.

### Detectar idioma    
Detecta el idioma de origen de un texto dado. ```GET: /Detect```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|query|cadena|yes|query|Ninguna |Texto cuyo idioma se identificará|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Texto a voz    
Convierte un texto dado en voz en forma de secuencia de audio en formato de onda. ```GET: /Speak```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|query|cadena|yes|query|Ninguna |Texto que desea convertir|
|language|cadena|yes|query|Ninguna |Código de idioma para generar voz (ejemplo: ' es-es')|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Traducir texto    
Traduce texto a un idioma especificado mediante Microsoft Translator. ```GET: /Translate```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|query|cadena|yes|query|Ninguna |Texto para traducir|
|languageTo|cadena|yes|query| Ninguna|Código de idioma de destino (ejemplo: 'fr')|
|languageFrom|cadena|no|query|Ninguna |Idioma de origen; si no se proporciona, Microsoft Translator intentará detectarlo automáticamente. (Ejemplo: es)|
|categoría|cadena|no|query|general |Categoría de traducción (predeterminada: 'general')|

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener idiomas    
Recupera todos los idiomas que admite Microsoft Translator. ```GET: /TranslatableLanguages```

No hay parámetros para esta llamada.

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|


### Obtener idiomas de voz    
Recupera los idiomas disponibles para la síntesis de voz. ```GET: /SpeakLanguages```

No hay parámetros para esta llamada.

#### Respuesta
|Nombre|Descripción|
|---|---|
|200|OK|
|default|Error en la operación.|

## Definiciones de objeto

#### Idioma: modelo de idioma para idiomas de Microsoft Translator traducibles

| Nombre | Tipo de datos | Obligatorio|
|---|---|---|
|Código|cadena|no|
|Nombre|cadena|no|


## Pasos siguientes
Después de agregar la API de Microsoft Translator a PowerApps Enterprise, [conceda a los usuarios los permisos necesarios](../power-apps/powerapps-manage-api-connection-user-access.md) para usar dicha API en sus aplicaciones.

[Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).


<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/create-api-microsofttranslator/register-your-application.png

<!---HONumber=AcomDC_0302_2016-->

