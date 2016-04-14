<properties
   pageTitle="Funciones de la plantilla del Administrador de recursos | Microsoft Azure"
   description="Describe las funciones que se van a usar en una plantilla del Administrador de recursos de Azure para recuperar valores, trabajar con cadenas y valores numéricos y recuperar información de implementación."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/22/2016"
   ms.author="tomfitz"/>

# Funciones de la plantilla del Administrador de recursos de Azure

Este tema describe todas las funciones que puede utilizar en una plantilla del Administrador de recursos de Azure.

Las funciones de plantilla y sus parámetros no distinguen mayúsculas de minúsculas. Por ejemplo, el Administrador de recursos resuelve **variables('var1')** y **VARIABLES('VAR1')** de la misma manera. Cuando se evalúa, a menos que la función modifique expresamente las mayúsculas (como toUpper o toLower), la función conservará el caso. Es posible que determinados tipos de recursos tengan requisitos de mayúsculas independientemente de cómo se evalúen las funciones.

## Funciones numéricas

El Administrador de recursos ofrece las siguientes funciones para trabajar con números enteros:

- [agregar](#add)
- [copyIndex](#copyindex)
- [div](#div)
- [int](#int)
- [length](#length)
- [mod](#mod)
- [mul](#mul)
- [sub](#sub)


<a id="add" />
### agregar

**add(operand1, operand2)**

Devuelve la suma de los dos enteros especificados.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| operand1 | Sí | Primer operando que se va a usar.
| operand2 | Sí | Segundo operando que se va a usar.


<a id="copyindex" />
### copyIndex

**copyIndex(offset)**

Devuelve el índice actual de un bucle de iteración.

Esta función siempre se usa con un objeto **copy**. Para ejemplos de cómo usar **copyIndex**, vea [Creación de varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md).


<a id="div" />
### div

**div(operand1, operand2)**

Devuelve la división de enteros de los dos enteros especificados.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| operand1 | Sí | Número que se va a dividir.
| operand2 | Sí | Número que se usa para dividir, tiene que ser distinto de 0.


<a id="int" />
### int

**int(valueToConvert)**

Convierte el valor especificado en entero.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| valueToConvert | Sí | Valor que se convierte en entero. Solo puede ser de tipo cadena o entero.

En el siguiente ejemplo se convierte el valor del parámetro proporcionado por el usuario en entero.

    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    }


<a id="length" />
### length

**longitud (matriz o cadena)**

Devuelve el número de elementos de una matriz o el número de caracteres de una cadena. Puede usar esta función con una matriz para especificar el número de iteraciones al crear recursos. En el ejemplo siguiente, el parámetro **siteNames** debería hacer referencia a una matriz de nombres que se usará al crear los sitios web.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

Para más información sobre cómo usar esta función con una matriz, vea [Creación de varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md).

O bien, puede usarla con una cadena:

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "nameLength": "[length(parameters('appName'))]"
    }


<a id="mod" />
### mod

**mod(operand1, operand2)**

Devuelve el resto de la división de enteros de los dos enteros especificados.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| operand1 | Sí | Número que se va a dividir.
| operand2 | Sí | Número que se usa para dividir, tiene que ser distinto de 0.



<a id="mul" />
### mul

**mul(operand1, operand2)**

Devuelve la multiplicación de los dos enteros especificados.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| operand1 | Sí | Primer operando que se va a usar.
| operand2 | Sí | Segundo operando que se va a usar.


<a id="sub" />
### sub

**sub(operand1, operand2)**

Devuelve la resta de los dos enteros especificados.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| operand1 | Sí | Número del que se va a restar.
| operand2 | Sí | Número que se va a restar.


## Funciones de cadena

El Administrador de recursos ofrece las siguientes funciones para trabajar con cadenas:

- [base64](#base64)
- [concat](#concat)
- [padLeft](#padleft)
- [replace](#replace)
- [split](#split)
- [cadena](#string)
- [substring](#substring)
- [toLower](#tolower)
- [toUpper](#toupper)
- [trim](#trim)
- [uniqueString](#uniquestring)
- [uri](#uri)

Para el número de caracteres de una cadena o una matriz, vea [longitud](#length).

<a id="base64" />
### base64

**base64 (inputString)**

Devuelve la representación de base64 de la cadena de entrada.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| inputString | Sí | Valor de cadena que se va a devolver como una representación de base64.

En el ejemplo siguiente se muestra cómo utilizar la función de base64.

    "variables": {
      "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

<a id="concat" />
### concat

**concat (arg1, arg2, arg3, ...)**

Combina varios valores y devuelve el resultado concatenado. Esta función puede tomar cualquier número de argumentos y puede aceptar cadenas o matrices para los parámetros.

En el ejemplo siguiente se muestra cómo combinar varios valores de cadena para devolver un valor concatenado.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://', reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }

En el ejemplo siguiente se muestra cómo combinar dos matrices.

    "parameters": {
        "firstarray": {
            type: "array"
        }
        "secondarray": {
            type: "array"
        }
     },
     "variables": {
         "combinedarray": "[concat(parameters('firstarray'), parameters('secondarray'))]
     }
        

<a id="padleft" />
### padLeft

**padLeft(stringToPad, totalLength, paddingCharacter)**

Devuelve una cadena alineada a la derecha agregando caracteres a la izquierda hasta alcanzar la longitud total especificada.
  
| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| stringToPad | Sí | La cadena que se va a alinear a la derecha.
| totalLength | Sí | El número total de caracteres de la cadena devuelta.
| paddingCharacter | Sí | El carácter que se va a usar para el relleno a la izquierda hasta alcanza la longitud total.

En el ejemplo siguiente se muestra cómo rellenar el valor del parámetro proporcionado por el usuario agregando el carácter cero hasta que la cadena llegue a 10 caracteres. Si el valor del parámetro original tiene más de 10 caracteres, no se agrega ningún carácter.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
    }

<a id="replace" />
### replace

**replace(originalString, oldCharacter, newCharacter)**

Devuelve una nueva cadena con todas las instancias de un carácter de la cadena especificada sustituidas por otro carácter.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| originalString | Sí | La cadena que tendrá todas las instancias de un carácter sustituido por otro carácter.
| oldCharacter | Sí | El carácter que se va a quitar de la cadena original.
| newCharacter | Sí | El carácter que se va a agregar en lugar del carácter eliminado.

En el ejemplo siguiente se muestra cómo quitar todos los guiones de la cadena proporcionada por el usuario.

    "parameters": {
        "identifier": { "type": "string" }
    },
    "variables": { 
        "newidentifier": "[replace(parameters('identifier'),'-','')]"
    }

<a id="split" />
### split

**split(inputString, delimiter)** **split(inputString, [delimiters])**

Devuelve una matriz de cadenas que contiene las subcadenas de la cadena de entrada que están delimitadas por los delimitadores enviados.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| inputString | Sí | Cadena que se va a dividir.
| delimiter | Sí | Delimitador que se va a usar, puede ser una cadena o una matriz de cadenas.

En el ejemplo siguiente la cadena de entrada se divide con una coma.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "stringPieces": "[split(parameters('inputString'), ',')]"
    }

<a id="string" />
### cadena

**string(valueToConvert)**

Convierte el valor especificado en cadena.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| valueToConvert | Sí | Valor que se convierte en cadena. Solo puede ser de tipo booleano, entero o cadena.

En el siguiente ejemplo se convierte el valor del parámetro proporcionado por el usuario en cadena.

    "parameters": {
        "appId": { "type": "int" }
    },
    "variables": { 
        "stringValue": "[string(parameters('appId'))]"
    }

<a id="substring" />
### substring

**substring(stringToParse, startIndex, length)**

Devuelve una subcadena que empieza en la posición de carácter especificada y que contiene el número especificado de caracteres.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| stringToParse | Sí | La cadena original desde la que se extrae la subcadena.
| startIndex | No | La posición de carácter inicial basado en cero de la subcadena.
| length | No | El número de caracteres de la subcadena.

En el ejemplo siguiente se extraen los tres primeros caracteres de un parámetro.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "prefix": "[substring(parameters('inputString'), 0, 3)]"
    }

<a id="tolower" />
### toLower

**toLower(stringToChange)**

Convierte la cadena especificada a minúsculas.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| stringToChange | Sí | La cadena que se va a convertir a minúsculas.

En el siguiente ejemplo se convierte el valor del parámetro proporcionado por el usuario a minúsculas.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "lowerCaseAppName": "[toLower(parameters('appName'))]"
    }

<a id="toupper" />
### toUpper

**toUpper(stringToChange)**

Convierte la cadena especificada a mayúsculas.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| stringToChange | Sí | La cadena que se va a convertir a mayúsculas.

En el siguiente ejemplo se convierte el valor del parámetro proporcionado por el usuario a mayúsculas.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "upperCaseAppName": "[toUpper(parameters('appName'))]"
    }

<a id="trim" />
### trim

**trim (stringToTrim)**

Quita todos los caracteres de espacio en blanco iniciales y finales de la cadena especificada.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| stringToTrim | Sí | La cadena que se recortará.

En el ejemplo siguiente se recortan los caracteres de espacio en blanco del valor de parámetro proporcionado por el usuario.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "trimAppName": "[trim(parameters('appName'))]"
    }

<a id="uniquestring" />
### uniqueString

**uniqueString (stringForCreatingUniqueString,...)**

Realiza un hash de 64 bits de las cadenas proporcionadas para crear una cadena única. Esta función es útil cuando se debe crear un nombre único para un recurso. Proporciona valores de parámetros que representan el nivel de unicidad del resultado. Puede especificar si el nombre es único para la suscripción, el grupo de recursos o la implementación.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| stringForCreatingUniqueString | Sí | Cadena base utilizada en la función hash para crear una cadena única.
| parámetros adicionales según sea necesario | No | Puede agregar tantas cadenas como necesite para crear el valor que especifica el nivel de unicidad.

El valor devuelto no es una cadena completamente aleatoria, sino que es el resultado de una función hash. El valor devuelto tiene 13 caracteres. No se garantiza que sea único global. Puede que desee combinar el valor con un prefijo de su convención de nomenclatura para crear un nombre más descriptivo.

En los ejemplos siguientes se muestra cómo utilizar uniqueString para crear un valor único para diferentes niveles de uso común.

Único basado en la suscripción

    "[uniqueString(subscription().subscriptionId)]"

Único basado en el grupo de recursos

    "[uniqueString(resourceGroup().id)]"

Único basado en la implementación de un grupo de recursos

    "[uniqueString(resourceGroup().id, deployment().name)]"
    
En el ejemplo siguiente se muestra cómo crear un nombre único para una cuenta de almacenamiento basada en el grupo de recursos.

    "resources": [{ 
        "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]", 
        "type": "Microsoft.Storage/storageAccounts", 
        ...

<a id="uri" />
### uri

**uri (baseUri, relativeUri)**

Crea un URI absoluto mediante la combinación de la cadena de relativeUri y baseUri.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| baseUri | Sí | La cadena de uri base.
| relativeUri | Sí | La cadena de uri relativo que se agregará a la cadena de uri base.

El valor del parámetro **baseUri** puede incluir un archivo específico, pero al construir el identificador URI, solo se usa la ruta de acceso base. Por ejemplo, al pasar ****http://contoso.com/resources/azuredeploy.json** como parámetro baseUri dará como resultado un identificador URI base de ****http://contoso.com/resources/**.

En el ejemplo siguiente se muestra cómo construir un vínculo a una plantilla anidada en función del valor de la plantilla principal.

    "templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"

## Funciones de matriz

El Administrador de recursos ofrece varias funciones para trabajar con valores de matriz:

Para combinar varias matrices en una sola, use [concat](#concat).

Para obtener el número de elementos de una matriz, use [length](#length).

Para dividir un valor de cadena en una matriz de valores de cadena, use [split](#split).

## Funciones con valores de implementación

El Administrador de recursos ofrece las siguientes funciones para obtener valores de las secciones de la plantilla y valores relacionados con la implementación:

- [deployment](#deployment)
- [parameters](#parameters)
- [variables](#variables)

Para obtener valores de recursos, grupos de recursos o suscripciones, consulte [Funciones de recursos](#resource-functions).

<a id="deployment" />
### deployment

**deployment()**

Devuelve información sobre la operación de implementación actual.

Esta función devuelve el objeto pasado durante la implementación. Las propiedades del objeto devuelto variarán en función de si el objeto de implementación se ha pasado como un vínculo o como un objeto en línea. Cuando se pasa el objeto de implementación en línea, como cuando se usa el parámetro **-TemplateFile** en Azure PowerShell para orientarlo a un archivo local, el objeto devuelto tiene el formato siguiente:

    {
        "name": "",
        "properties": {
            "template": {
                "$schema": "",
                "contentVersion": "",
                "resources": [
                ],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

Cuando el objeto se pasa como un vínculo, como cuando se usa el parámetro **-TemplateUri** para orientarlo a un objeto remoto, se devuelve el objeto en el formato siguiente.

    {
        "name": "",
        "properties": {
            "templateLink": {
                "uri": "",
                "contentVersion": ""
            },
            "mode": "",
            "provisioningState": ""
        }
    }

En el ejemplo siguiente se muestra cómo usar deployment() para establecer un vínculo con otra plantilla basada en el identificador URI de la plantilla principal.

    "variables": {  
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
    }  


<a id="parameters" />
### parameters

**parámetros (parameterName)**

Devuelve un valor de parámetro. El nombre del parámetro especificado debe definirse en la sección de parámetros de la plantilla.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| parameterName | Sí | El nombre del parámetro que se va a devolver.

En el ejemplo siguiente se muestra un uso simplificado de la función de los parámetros.

    "parameters": { 
      "siteName": {
          "type": "string"
      }
    },
    "resources": [
       {
          "apiVersion": "2014-06-01",
          "name": "[parameters('siteName')]",
          "type": "Microsoft.Web/Sites",
          ...
       }
    ]

<a id="variables" />
### variables

**variables (variableName)**

Devuelve el valor de variable. El nombre de la variable especificada debe definirse en la sección de variables de la plantilla.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| variable Name | Sí | El nombre de la variable que se va a devolver.



## Funciones de recursos

El Administrador de recursos ofrece las siguientes funciones para obtener valores de recursos:

- [listkeys](#listkeys)
- [list*](#list)
- [providers](#providers)
- [reference](#reference)
- [resourceGroup](#resourcegroup)
- [resourceId](#resourceid)
- [suscripción](#subscription)

Para obtener valores de parámetros, variables o la implementación actual, consulte [Funciones con valores de implementación](#deployment-value-functions).

<a id="listkeys" />
### listKeys

**listKeys (resourceName o resourceIdentifier, apiVersion)**

Devuelve las claves para cualquier tipo de recurso que admite la operación listKeys. El valor de resourceId puede especificarse mediante la [función resourceId](./#resourceid) o mediante el formato **providerNamespace/resourceType/resourceName**. Puede utilizar la función para obtener los valores de primaryKey y secondaryKey.
  
| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| resourceName o resourceIdentifier | Sí | Identificador único para el recurso.
| apiVersion | Sí | Versión de API de estado en tiempo de ejecución de un recurso.

En el ejemplo siguiente se muestra cómo se devuelven las claves de una cuenta de almacenamiento en la sección de salidas.

    "outputs": { 
      "exampleOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2015-05-01-preview')]", 
        "type" : "object" 
      } 
    } 

<a id="list" />
### list*

**list* (resourceName o resourceIdentifier, apiVersion)**

Cualquier operación que comienza por **list** se puede usar como función en la plantilla. Esto incluye **listKeys**, como se muestra anteriormente, pero también operaciones como **list**, **listAdminKeys** y **listStatus**. Al llamar a la función, use el nombre real de la función, en lugar de list*. Para determinar qué tipos de recursos tienen una operación de lista, use el siguiente comando de PowerShell.

    PS C:\> Get-AzureRmProviderOperation -OperationSearchString *  | where {$_.Operation -like "*list*"} | FT Operation

O bien, recupere la lista con CLI de Azure. En el ejemplo siguiente se recuperan todas las operaciones de **apiapps** y se usa la utilidad JSON [jq](http://stedolan.github.io/jq/download/) para filtrar las operaciones de la lista.

    azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains("list"))"

<a id="providers" />
### providers

**providers (providerNamespace, [resourceType])**

Se devuelve información acerca de un proveedor de recursos y sus tipos de recursos admitidos. Si no se proporciona el tipo, se devuelven todos los tipos admitidos.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| providerNamespace | Sí | Espacio de nombres del proveedor
| resourceType | No | El tipo de recurso en el espacio de nombres especificado.

Se devuelve cada tipo admitido en el formato siguiente:

    {
        "resourceType": "",
        "locations": [ ],
        "apiVersions": [ ]
    }

En el ejemplo siguiente se muestra cómo utilizar la función de proveedor:

    "outputs": {
	    "exampleOutput": {
		    "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
		    "type" : "object"
	    }
    }

<a id="reference" />
### reference

**reference (resourceName or resourceIdentifier, [apiVersion])**

Permite que una expresión derive su valor del estado de tiempo de ejecución de otro recurso.

| Parámetro | Obligatorio | Descripción
| :--------------------------------: | :------: | :----------
| resourceName o resourceIdentifier | Sí | Nombre o identificador único de un recurso.
| apiVersion | No | Versión de la API del recurso especificado. Debe incluir este parámetro cuando el recurso no esté aprovisionado en la misma plantilla.

La función **reference** deriva su valor desde un estado de tiempo de ejecución y, por tanto, no se puede utilizar en la sección de variables. Se puede utilizar en la sección de salidas de una plantilla.

Mediante el uso de la función de referencia, se declara implícitamente que un recurso depende de otro recurso si el recurso al que se hace referencia se aprovisiona en la misma plantilla. No tiene que usar también la propiedad **dependsOn**. La función no se evalúa hasta que el recurso al que se hace referencia complete la implementación.

En el ejemplo siguiente se hace referencia a una cuenta de almacenamiento que se implementa en la misma plantilla.

    "outputs": {
		"NewStorage": {
			"value": "[reference(parameters('storageAccountName'))]",
			"type" : "object"
		}
	}

En el ejemplo siguiente se hace referencia a una cuenta de almacenamiento que no se implementa en esta plantilla, pero que existe dentro del mismo grupo de recursos conforme se implementan los recursos.

    "outputs": {
		"ExistingStorage": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15')]",
			"type" : "object"
		}
	}

Puede recuperar un valor concreto del objeto devuelto, como el URI del punto de conexión del blob, como se muestra a continuación.

    "outputs": {
		"BlobUri": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

Si ahora quiere especificar directamente la versión de la API en su plantilla, puede usar la función [providers](#providers) y recuperar uno de los valores, como la versión más reciente, como se muestra a continuación.

    "outputs": {
		"BlobUri": {
			"value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).primaryEndpoints.blob]",
			"type" : "string"
		}
	}

En el ejemplo siguiente se hace referencia a una cuenta de almacenamiento en otro grupo de recursos.

    "outputs": {
		"BlobUri": {
			"value": "[reference(resourceId(parameters('relatedGroup'), 'Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2015-06-15').primaryEndpoints.blob]",
			"type" : "string"
		}
	}

<a id="resourcegroup" />
### resourceGroup

**resourceGroup()**

Devuelve un objeto estructurado que representa el grupo de recursos actual. El objeto tendrá el siguiente formato:

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
    }

En el ejemplo siguiente se utiliza la ubicación del grupo de recursos para asignar la ubicación de un sitio web.

    "resources": [
       {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          ...
       }
    ]

<a id="resourceid" />
### resourceId

**resourceId ([resourceGroupName], resourceType, resourceName1, [resourceName2]...)**

Devuelve el identificador único de un recurso. Utilice esta función cuando el nombre del recurso sea ambiguo o no esté aprovisionado dentro de la misma plantilla. El identificador se devuelve con el formato siguiente:

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}
      
| Parámetro | Obligatorio | Descripción
| :---------------: | :------: | :----------
| resourceGroupName | No | Nombre del grupo de recursos opcional. El valor predeterminado es el grupo de recursos actual. Especifique este valor cuando se recupere un recurso en otro grupo de recursos.
| resourceType | Sí | Tipo de recurso, incluido el espacio de nombres del proveedor de recursos.
| resourceName1 | Sí | Nombre del recurso.
| resourceName2 | No | Siguiente segmento de nombre de recurso si el recurso está anidado.

En el ejemplo siguiente se muestra cómo recuperar los identificadores de recursos para un sitio web y una base de datos. El sitio web existe en un grupo de recursos denominado **myWebsitesGroup** y la base de datos existe en el grupo de recursos actual para esta plantilla.

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'),parameters('databaseName'))]
    
A menudo, necesitará utilizar esta función cuando se usa una cuenta de almacenamiento o red virtual en un grupo de recursos alternativo. La cuenta de almacenamiento o la red virtual puede utilizarse en varios grupos de recursos; por lo tanto, no es deseable su eliminación cuando se elimina un único grupo de recursos. En el ejemplo siguiente se muestra cómo un recurso de un grupo de recursos externos se puede utilizar fácilmente:

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]
    }

<a id="subscription" />
### subscription

**subscription()**

Devuelve detalles acerca de la suscripción en el formato siguiente.

    {
        "id": "/subscriptions/#####"
        "subscriptionId": "#####"
    }

En el ejemplo siguiente se muestra la función de suscripción a la que se llama en la sección de salidas.

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 


## Pasos siguientes
- Para obtener una descripción de las secciones de una plantilla del Administrador de recursos de Azure, vea [Creación de plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).
- Para combinar varias plantillas, vea [Uso de plantillas vinculadas con el Administrador de recursos de Azure](resource-group-linked-templates.md).
- Para iterar una cantidad de veces determinada al crear un tipo de recurso, vea [Creación de varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md).
- Para saber cómo implementar la plantilla que creó, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md)

<!---HONumber=AcomDC_0224_2016-->