<properties 
	pageTitle="Creación de definiciones de aplicación lógica | Microsoft Azure" 
	description="Obtenga información sobre cómo escribir la definición de JSON para aplicaciones lógicas." 
	authors="stepsic-microsoft-com" 
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
	ms.date="12/07/2015"
	ms.author="stepsic"/>
	
# Creación de definiciones de aplicación lógica
En este tema se explica cómo usar definiciones de [aplicaciones lógicas de servicios de aplicaciones](app-service-logic-what-are-logic-apps.md), que es un lenguaje JSON declarativo simple. Si no lo ha hecho todavía, consulte primero [Creación de una aplicación lógica nueva](../app-service-create-a-logic-app.md). También puede leer el [material de referencia completo del lenguaje de definición en MSDN](https://msdn.microsoft.com/library/azure/dn948512.aspx).

## Varios pasos que se repiten en una lista

Un patrón común es tener un paso que obtiene una lista de elementos y, a continuación, disponer de una serie de dos o más acciones que desea realizar en la lista:

![Repetir sobre listas](./media/app-service-logic-author-definitions/repeatoverlists.png)

En este ejemplo hay 3 acciones:

1. Obtener una lista de artículos. Esto devuelve un objeto que contiene una matriz.

2. Una acción que va a una propiedad de vínculo en cada artículo, que devuelve la ubicación real del artículo.

3. Una acción que itera todos los resultados de la segunda acción para descargar los artículos reales.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            }
        },
        "readLinks": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem().link"
            },
            "repeat": "@body('getArticles').responseData.feed.entries"
        },
        "downloadLinks": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatOutputs().headers.location"
            },
            "conditions": [
                {
                    "expression": "@not(equals(actions('readLinks').status, 'Skipped'))"
                }
            ],
            "repeat": "@actions('readLinks').outputs.repeatItems"
        }
    },
    "outputs": {}
}
```

Como se explica en [Uso de las características de aplicaciones lógicas](app-service-logic-use-logic-app-features.md), itere por la primera lista usando la propiedad `repeat:` en la segunda acción. Sin embargo, para la tercera acción, debe seleccionar la propiedad `@actions('readLinks').outputs.repeatItems`, dado que la segunda se ejecuta para cada artículo.

Dentro de la acción se pueden usar las funciones siguientes: [`repeatItem()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatItem), [`repeatOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatOutputs) o [`repeatBody()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatBody). En este ejemplo, quería obtener el encabezado `location`, por lo que usé la función [`repeatOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatOutputs) para obtener los resultados de la ejecución de la acción de la segunda acción que ahora por la que estamos iterando actualmente.

## Asignación de elementos de una lista a una configuración diferente

A continuación, supongamos que queremos obtener contenido completamente diferente según un valor de una propiedad. Podemos crear una asignación de valores a destinos como un parámetro:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "specialCategories": {
            "defaultValue": [
                "science",
                "google",
                "microsoft",
                "robots",
                "NSA"
            ],
            "type": "Array"
        },
        "destinationMap": {
            "defaultValue": {
                "science": "http://www.nasa.gov",
                "microsoft": "https://www.microsoft.com/es-ES/default.aspx",
                "google": "https://www.google.com",
                "robots": "https://en.wikipedia.org/wiki/Robot",
                "NSA": "https://www.nsa.gov/"
            },
            "type": "Object"
        }
    },
    "triggers": {},
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            },
            "conditions": []
        },
        "getSpecialPage": {
            "repeat": "@body('getArticles').responseData.feed.entries",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(repeatItem().categories, parameters('specialCategories')))]"
            },
            "conditions": [
                {
                    "expression": "@greater(length(intersection(repeatItem().categories, parameters('specialCategories'))), 0)"
                }
            ]
        }
    }
}
```

En este caso, en primer lugar, obtenemos una lista de artículos y, a continuación, el segundo paso busca en una asignación, según la categoría que se ha definido como un parámetro, la dirección URL en la que obtener el contenido.

Prestemos atención aquí a dos elementos: la función [`intersection()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#intersection) se utiliza para comprobar si la categoría coincide con una de las categorías conocidas definidas. En segundo lugar, una vez que obtenemos la categoría, podemos extraer el elemento de la asignación mediante corchetes: `parameters[...]`.

## Encadenado/anidado de aplicaciones web repitiendo en una lista

Suele ser más fácil administrar las aplicaciones lógicas cuando son más discretas. Para ello, factorice la lógica en varias definiciones y llámelas desde la misma definición principal. En este ejemplo, habrá una aplicación lógica principal que recibe pedidos y una aplicación lógica secundaria que ejecuta algunos pasos para cada pedido.

En la aplicación lógica principal:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "orders": {
            "defaultValue": [
                {
                    "quantity": 10,
                    "id": "myorder1"
                },
                {
                    "quantity": 200,
                    "id": "specialOrder"
                },
                {
                    "quantity": 5,
                    "id": "myOtherOrder"
                }
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "iterateOverOrders": {
            "repeat": "@parameters('orders')",
            "type": "Workflow",
            "inputs": {
                "uri": "https://westus.logic.azure.com/subscriptions/xxxxxx-xxxxx-xxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Logic/workflows/xxxxxxx",
                "apiVersion": "2015-02-01-preview",
                "trigger": {
                    "name": "submitOrder",
                    "outputs": {
                        "body": "@repeatItem()"
                    }
                },
                "authentication": {
                    "type": "Basic",
                    "username": "default",
                    "password": "xxxxxxxxxxxxxx"
                }
            }
        },
        "sendInvoices": {
            "repeat": "@outputs('iterateOverOrders').repeatItems",
            "type": "Http",
            "inputs": {
                "uri": "http://www.example.com/?invoiceID=@{repeatOutputs().run.outputs.deliverTime.value}",
                "method": "GET"
            }
        }
    },
    "outputs": {}
}
```

A continuación, en la aplicación lógica secundaria usará la función [`triggerBody()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#triggerBody) para obtener los valores que se pasaron al flujo de trabajo secundario. A continuación, deberá rellenar las salidas con los datos que desea devolver al flujo principal.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "calulatePrice": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?action=calcPrice&id=@{triggerBody().id}&qty=@{triggerBody().quantity}"
            }
        },
        "calculateDeliveryTime": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?action=calcTime&id=@{triggerBody().id}&qty=@{triggerBody().quantity}"
            }
        }
    },
    "outputs": {
        "deliverTime": {
            "type": "String",
            "value": "@outputs('calculateDeliveryTime').headers.etag"
        }
    }
}
```

Puede leer sobre la [acción del tipo de aplicación lógica en MSDN](https://msdn.microsoft.com/library/azure/dn948511.aspx).

>[AZURE.NOTE]El Diseñador de aplicaciones lógicas no admite acciones de tipo de aplicación lógica, por lo que deberá editar manualmente la definición.


## Un paso de control de errores si algo va mal

Normalmente desea poder escribir un *paso de corrección*; cierta lógica que se ejecuta, si, **y solo si**, una o varias llamadas no se han podido realizar correctamente. En este ejemplo, obtenemos datos desde diversos lugares, pero si se produce un error en la llamada, es necesario PUBLICAR un mensaje en alguna parte para poder localizar ese error más adelante:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "dataFeeds": {
            "defaultValue": [
                "https://www.microsoft.com/es-ES/default.aspx",
                "https://gibberish.gibberish/"
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "repeat": "@parameters('dataFeeds')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem()"
            }
        },
        "postToErrorMessageQueue": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?noteAnErrorFor=@{repeatItem().inputs.uri}"
            },
            "conditions": [
                {
                    "expression": "@equals(actions('readData').status, 'Failed')"
                },
                {
                    "expression": "@equals(repeatItem().status, 'Failed')"
                }
            ]
        }
    },
    "outputs": {}
}
```

Estoy utilizando dos condiciones porque en el primer paso estoy repetición de una lista. Si tiene una sola acción, sólo necesitaría una condición (la primera). Tenga en cuenta también que puede usar las *entradas* a la acción incorrecta en el paso de solución (aquí se pasa la URL con error al segundo paso):

![Corrección](./media/app-service-logic-author-definitions/remediation.png)

Por último, dado que ahora se ha controlado el error, la ejecución ya no genera ningún **error**. Como puede ver aquí, esta ejecución es **correcta** a pesar de que antes generara errores, porque escribí este paso para solucionar este error.

## Dos (o más) pasos que se ejecutan en paralelo

Para que varias acciones se ejecuten en paralelo, y no en secuencias, necesita quitar la condición `dependsOn` que vincula a ambas acciones. Cuando se quita la dependencia, se ejecutarán automáticamente las acciones en paralelo, a menos que necesiten datos entre sí.

![Bifurcaciones](./media/app-service-logic-author-definitions/branches.png)

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "dataFeeds": {
            "defaultValue": [
                "https://www.microsoft.com/es-ES/default.aspx",
                "https://office.live.com/start/default.aspx"
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "repeat": "@parameters('dataFeeds')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem()"
            }
        },
        "branch1": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?branch1Logic=@{repeatItem().inputs.uri}"
            }
        },
        "branch2": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?branch2Logic=@{repeatItem().inputs.uri}"
            }
        }
    },
    "outputs": {}
}
```

Como puede ver en el ejemplo anterior, branch1 y branch2 sólo dependen en el contenido de readData. Como resultado, ambas ramas se ejecutarán en paralelo:

![Paralelo](./media/app-service-logic-author-definitions/parallel.png)

Puede ver que la marca de tiempo para ambas bifurcaciones es idéntica.

## Combinación de dos bifurcaciones condicionales de lógica

Puede combinar dos flujos condicionales de lógica (que puede o no puede haber ejecutado) al tener una única acción que obtiene los datos de ambas bifurcaciones.

La estrategia en esta caso varía en función de si controla un elemento o una colección de elementos. Si se trata de un único elemento, deberá usar la función [`coalesce()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#coalesce):

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1"
            },
            "type": "Object"
        }
    },
    "triggers": {},
    "actions": {
        "handleNormalOrders": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderNormally=@{parameters('order').id}"
            },
            "conditions": [
                {
                    "expression": "@lessOrEquals(parameters('order').quantity, 100)"
                }
            ]
        },
        "handleSpecialOrders": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderSpecially=@{parameters('order').id}"
            },
            "conditions": [
                {
                    "expression": "@greater(parameters('order').quantity, 100)"
                }
            ]
        },
        "submitInvoice": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?invoice=@{coalesce(outputs('handleNormalOrders')?.headers?.etag,outputs('handleSpecialOrders')?.headers?.etag )}"
            },
            "conditions": [
                {
                    "expression": "@or(equals(actions('handleNormalOrders').status, 'Succeeded'), equals(actions('handleSpecialOrders').status, 'Succeeded'))"
                }
            ]
        }
    },
    "outputs": {}
}
```
 
O bien, cuando las dos primeras bifurcaciones funcionan sobre una lista de pedidos, por ejemplo, deberá usar la función [`union()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#union) para combinar los datos de ambas bifurcaciones.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "orders": {
            "defaultValue": [
                {
                    "quantity": 10,
                    "id": "myorder1"
                },
                {
                    "quantity": 200,
                    "id": "specialOrder"
                },
                {
                    "quantity": 5,
                    "id": "myOtherOrder"
                }
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "handleNormalOrders": {
            "repeat": "@parameters('orders')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderNormally=@{repeatItem().id}"
            },
            "conditions": [
                {
                    "expression": "@lessOrEquals(repeatItem().quantity, 100)"
                }
            ]
        },
        "handleSpecialOrders": {
            "repeat": "@parameters('orders')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderSpecially=@{repeatItem().id}"
            },
            "conditions": [
                {
                    "expression": "@greater(repeatItem().quantity, 100)"
                }
            ]
        },
        "submitInvoice": {
            "repeat": "@union(actions('handleNormalOrders').outputs.repeatItems, actions('handleSpecialOrders').outputs.repeatItems)",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?invoice=@{repeatOutputs().headers.etag}"
            },
            "conditions": [
                {
                    "expression": "@equals(repeatItem().status, 'Succeeded')"
                }
            ]
        }
    },
    "outputs": {}
}
```
## Trabajo con cadenas

Existen diversas funciones que pueden utilizarse para la cadena maniplate. Veamos un ejemplo donde tenemos una cadena que se va a pasar a un sistema, pero no estamos seguros de que la codificación de caracteres se controlará correctamente. Una opción consiste en codificar esta cadena con base64. Sin embargo, para evitar escapes en una dirección URL, vamos a reemplazar algunos caracteres.

También queremos una subcadena del nombre del pedido, porque los cinco primeros caracteres no se usan.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1",
                "orderer": "NAME=Stèphén__Šīçiłianö"
            },
            "type": "Object"
        }
    },
    "triggers": {},
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
            }
        }
    },
    "outputs": {}
}
```

Trabajar desde dentro hacia fuera:

1. Obtener la [`length()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#length) del nombre del solicitante; esto devuelve el número total de caracteres.

2. Restar 5 (porque desearemos una cadena más corta).

3. Obtener realmente la [`substring()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#substring). Comenzamos con el índice `5` y pasamos al resto de la cadena.

4. Convertir esta subcadena en una cadena [`base64()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#base64).

5. [`replace()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#replace) todos los caracteres `+` por `-`.

6. [`replace()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#replace) todos los caracteres `/` por `_`.

## Trabajo con fechas

Las fechas puede ser útiles, especialmente cuando intenta extraer datos de un origen de datos que no admite de forma natural **desencadenadores**. También puede utilizar las fechas para averiguar cuánto duran los distintos pasos.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1"
            },
            "type": "Object"
        }
    },
    "triggers": {},
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{parameters('order').id}"
            }
        },
        "timingWarning": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
        	},
            "conditions": [
                {
                    "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
                }
            ]
        }
    },
    "outputs": {}
}
```

En este ejemplo, vamos a extraer el valor `startTime` del paso anterior. A continuación, vamos a obtener la hora actual y le vamos a restar un segundo:[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/dn948512.aspx#addseconds) (podría utilizar otras unidades de tiempo como `minutes` o `hours`). Por último, podemos comparar estos dos valores. Si el primero es menor que el segundo, significa que ha transcurrido más de un segundo desde la primera vez que se realizó el pedido.

Observe también que podemos utilizar formateadores de cadena para dar formato a fechas: en la cadena de consulta utilizo [`utcnow('r')`](https://msdn.microsoft.com/library/azure/dn948512.aspx#utcnow) para obtener el RFC1123. Todos los formatos de fecha [están documentados en MSDN](https://msdn.microsoft.com/library/azure/dn948512.aspx#utcnow).

## Transferencia de valores en tiempo de ejecución para variar el comportamiento

Supongamos que tiene comportamientos diferentes que desea ejecutar en función de algún valor que usa para iniciar la aplicación lógica. Puede utilizar la función [`triggerOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#triggerOutputs) para obtener estos valores aparte de los que ha transferido:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "triggers": {},
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@triggerOutputs().uriToGet"
            }
        },
        "extraStep": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/extraStep"
            },
            "conditions": [
                {
                    "expression": "@triggerOutputs().doMoreLogic"
                }
            ]
        },  
    },
    "outputs": {}
}
```

Para realizar este trabajo, al iniciar la ejecución es necesario transferir las propiedades que desee (en el ejemplo anterior, `uriToGet` y `doMoreLogic`). En esta llamada puede [utilizar la autenticación básica para](https://msdn.microsoft.com/library/azure/dn948513.aspx#basicAuth):

```
POST https://<<Logic app endpoint from the Essentials>>/run?api-version=2015-02-01-preview
Authorization: Basic <<Based 64 encoded username (default) : password (from the Settings blade)>>
Content-type: application/json
```

Con la siguiente carga. Tenga en cuenta que se ha proporcionado la aplicación lógica con los valores que se van a usar ahora:

```
{
    "outputs": {
        "uriToGet" : "http://my.uri.I.want/",
        "doMoreLogic" : true
    }
}
``` 

Al ejecutar esta aplicación lógica, llamará al URI que ha transferido y ejecutará dicho pasos adicional porque he transferido `true`. Si desea variar solo los parámetros del tiempo de implementación (no para *ejecución*), entonces debe usar `parameters` como se menciona a continuación.

## Uso de parámetros de tiempo de implementación para entornos diferentes

Es habitual tener un ciclo de vida de implementación donde tiene un entorno de desarrollo, un entorno de ensayo y, a continuación, un entorno de producción. En todos ellos puede querer la misma definición, pero usar bases de datos distintas, por ejemplo. Del mismo modo, es posible que desee utilizar la misma definición en muchas regiones diferentes para lograr una alta disponibilidad, pero que cada instancia de aplicación lógica se comunique con la base de datos de dicha región.

Tenga en cuenta que esto es diferente del uso de parámetros distintos en el *tiempo de ejecución*, para lo que debe usar la función `trigger()` según se ha descrito anteriormente.

Puede empezar con una definición muy sencilla, como esta:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "connection": {
            "type": "string"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('connection')"
            }
        }        
    },
    "outputs": {}
}
```

A continuación, en la solicitud `PUT` real para la aplicación lógica, puede proporcionar el parámetro `connection`. Tenga en cuenta que, como ya no hay ningún valor predeterminado, este parámetro es necesario en la carga de la aplicación lógica:

```
{
    "properties": {
        "sku": {
            "name": "Premium",
            "plan": {
                "id": "/subscriptions/xxxxx/resourceGroups/xxxxxx/providers/Microsoft.Web/serverFarms/xxxxxx"
            }
        },
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

En cada entorno puede proporcionar un valor diferente para el parámetro `connection`.

## Ejecución de un paso hasta que se cumpla una condición

Es posible que tenga una API a la que está llamando y quiera esperar una respuesta determinada antes de continuar. Por ejemplo, imagine que quiere esperar a que alguien cargue un archivo en un directorio antes de procesar el archivo. Puede hacerlo con *do-until*:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "http0": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://mydomain/listfiles"
            },
            "until": {
                "limit": {
                    "timeout": "PT10M"
                },
                "conditions": [
                    {
                        "expression": "@greater(length(action().outputs.body),0)"
                    }
                ]
            }
        }
    },
    "outputs": {}
}
```

Consulte la [documentación sobre la API de REST](https://msdn.microsoft.com/library/azure/dn948513.aspx) para conocer todas las opciones disponibles para crear y administrar aplicaciones lógicas.

<!---HONumber=AcomDC_1210_2015-->