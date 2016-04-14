<properties
	pageTitle="Desarrollar API para PowerApps Enterprise | Microsoft Azure"
	description="Compilar o crear API personalizadas para PowerApps"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajram"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="rajram"/>

# Desarrollar una API para PowerApps

Puede crear o desarrollar su propia API que puede usarse dentro de PowerApps. Entre los pasos se incluyen:

- Compilación e implementación de la API
- Definición de la API de [Swagger 2.0](http://swagger.io/) de autor para la API

En este tema se muestran estos pasos.

## Paso 1: Compilación e implementación de la API

La compilación e implementación de una API para PowerApps es igual que crear cualquier API. Puede elegir su lenguaje de programación y marco de trabajo favorito. También puede hospedar su API en cualquier lugar. Es recomendable hospedarla en el mismo entorno del Servicio de aplicaciones de PowerApps que la [aplicación de API](https://azure.microsoft.com/services/app-service/api/).

Los artículos siguientes muestran cómo compilar e implementar una API de .Net, Java o Node.js en el entorno del Servicio de aplicaciones:

- [Compilación e implementación de una .NET en el Servicio de aplicaciones de Azure](../app-service-api-dotnet-get-started.md)
- [Compilación e implementación de una aplicación de API de Java en el Servicio de aplicaciones de Azure](../app-service-api-java-api-app.md)
- [Compilación e implementación de una aplicación de API de Node.js en el Servicio de aplicaciones de Azure](../app-service-api-nodejs-api-app.md)


## Paso 2: Definición de la API de Swagger 2.0 de autor para la API

Si se sigue uno de los artículos mencionados en el *Paso 1*, se generará automáticamente una definición estándar de la API de Swagger 2.0 estándar automáticamente para la API. Para optimizarla para PowerApps, puede personalizar la definición de la API de Swagger 2.0 generada mediante las siguientes extensiones de esquema.

Para obtener información sobre cómo personalizar la definición de la API Swagger 2.0 en general, consulte [Personalización de definiciones de API generadas por Swashbuckle](../app-service-api-dotnet-swashbuckle-customize.md).

### Extensiones de esquema
Además de la swagger generada automáticamente por Swashbuckle, existen algunas extensiones swagger adicionales disponibles al crear una API para PowerApps. En esta sección se enumeran y describen estas extensiones.

##### x-ms-summary
Cadena que describe los nombres para mostrar de las entidades que no tienen un campo de resumen definido en la especificación Swagger. **Nombres de parámetro** es un ejemplo.

##### x-ms-visibility
Este valor describe si la entidad se muestra en el diseñador de flujo de lógica. Están disponibles los siguientes valores:

- "none" (valor predeterminado)
- "advanced"
- "internal": el diseñador de flujo de lógica no muestra estas operaciones

Si una operación está marcada como "importante", se espera que el cliente de flujo de lógica resalte estas operaciones.

##### x-ms-trigger
Define si esta operación se puede usar como un desencadenador en el flujo de lógica. Las opciones incluyen:
	
- ninguno (predeterminado): la operación no se puede usar como desencadenador.
- single: esta operación también puede usarse como un desencadenador.
- por lotes: esta operación puede usarse como un desencadenador. Además, esta operación responde con una "matriz" de objetos JSON y el flujo de lógica activa un desencadenador para cada elemento de la matriz.


##### x-ms-dynamic-values
Esta es un indicio para el diseñador del flujo de lógica de que la API proporciona una lista de valores permitidos de forma dinámica para este parámetro. El diseñador del flujo de lógica puede invocar una operación definida por el valor de este campo y extraer los valores posibles del resultado. El diseñador del flujo de lógica puede mostrar estos valores como opciones al usuario final.

El valor es un objeto que contiene las siguientes propiedades:
	
- operationId: una cadena que coincide con el operationId que se invoca
- parameters: un objeto cuyas propiedades definen los parámetros necesarios para la operación
- value-collection: una cadena de ruta de acceso que se evalúa en una matriz de objetos en la carga de respuesta
- value-path: una cadena de ruta de acceso en el objeto situado dentro de "value-collection" que hace referencia al valor del parámetro.
- value-title: una cadena de ruta de acceso en el objeto situado dentro de "value-collection" que hace referencia a una descripción del valor.


Ejemplo:

```javascript
"/api/tables/{table}/items": {
  "post": {
    "operationId": "TableData_CreateItem",
    "summary": "Create an object in {Salesforce}",
    "parameters": [
      {
        "name": "table",
        "x-ms-summary": "Object Type",
        "x-ms-dynamic-values": {
          "operationId": "TableMetadata_ListTables",      // operation that needs to be invoked
          "parameters": { },                              // parameters for the above operation, if any
          "value-collection": "values",                   // field that contains the collection
          "value-path": "Name",                           // field that contains the value
          "value-title": "DisplayName"                    // field that contains a display name for the value
      }
      // ...
    ]
    // ...
  }
  // ...
}
```

En el ejemplo anterior, el swagger define una operación denominada _TableData\_CreateItem_ que crea un nuevo objeto en Salesforce.

Salesforce dispone de una gran cantidad de objetos integrados. _x-ms-dynamic-values_ se usa aquí para ayudar al diseñador a descifrar la lista de los objetos de Salesforce integrados. La obtiene llamando a _TableMetadata\_ListTables_.

##### x-ms-dynamic-schema
Este es un indicio para el diseñador de flujo de lógica que el esquema de este parámetro (o respuesta) es de naturaleza dinámica. Puede invocar una operación definida por el valor de este campo y detectar el esquema de forma dinámica. A continuación, puede mostrar una interfaz de usuario adecuada para tomar entradas del usuario o mostrar campos disponibles.

Ejemplo:

```javascript
{
  "name": "item",
  "in": "body",
  "required": true,
  "x-ms-dynamic-schema": {
    "operationId": "Metadata_GetTableSchema",
    "parameters": {
      "tablename": "{table}"              // the value that the user has selected from the above parameter
    },
    "value-path": "Schema"                // the field that contains the JSON schema
  }
},
```

Esto es útil en escenarios en los que las entradas para una operación son dinámicas. Por ejemplo, considere el caso de SQL. El esquema de cada tabla es diferente. Por tanto, cuando un usuario selecciona una tabla determinada, el diseñador de flujo de lógica debe comprender la estructura de la tabla para que pueda mostrar los nombres de las columnas. En este contexto, si la definición de swagger dispone de _x-ms-dynamic-schema_, llama a la operación correspondiente para capturar el esquema.

<!---HONumber=AcomDC_1203_2015-->