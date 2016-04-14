<properties 
	pageTitle="Nueva versión de esquema 2015-08-01-preview" 
	description="Aprenda a escribir la definición de JSON de la versión más reciente de Aplicaciones lógicas" 
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
	ms.date="02/17/2016"
	ms.author="stepsic"/>
	
# Nueva versión de esquema 2015-08-01-preview

La nueva versión de esquema y API de Aplicaciones lógicas presenta varias mejoras que optimizan la confiabilidad y la facilidad de uso de estas aplicaciones. Hay cuatro diferencias principales:

1. El tipo de acción **APIApp** se ha actualizado al nuevo tipo de acción **APIConnection**.
2. La instrucción **Repeat** se llama ahora **Foreach**.
3. La aplicación de API **Agente de escucha HTTP** ya no es necesaria.
4. En la llamada a los flujos de trabajo secundarios se usa un nuevo esquema.

## 1\. Paso a las conexiones de API

El cambio más importante es que ya no es necesario implementar aplicaciones de API en la suscripción de Azure para usar las API. Hay dos maneras de usar las API: *API administradas y *API web personalizadas.

Cada una de ellas se controla de forma algo diferente ya que sus modelos de administración y hospedaje son diferentes. Una ventaja de este modelo es que ya no está limitado a los recursos que se implementan en el grupo de recursos.

### API administradas

Hay varias API que administra Microsoft en su nombre, como Office 365, Salesforce, Twitter, FTP etc. Algunas de estas API administrada pueden usarse tal y como están, como en el caso de Bing Translate, mientras que otras requieren configuración. Esta configuración se conoce como *conexión*.

Por ejemplo, cuando utilice Office 365, deberá crear una conexión que contenga el token de inicio de sesión de Office 365. Este token se almacenará y actualizará de forma segura para que la aplicación lógica pueda llamar siempre a la API de Office 365. Como alternativa, si desea conectarse a su servidor SQL o FTP, debe crear una conexión que tenga la cadena de conexión.

Dentro de la definición estas acciones se denominan `APIConnection`. A continuación se muestra un ejemplo de una conexión que llama a Office 365 para enviar un correo electrónico:

```
{
    "actions": {
        "Send_Email": {
            "type": "ApiConnection",
            "inputs": {
                "host": {
                    "api": {
                        "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['shared_office365']['connectionId']"
                    }
                },
                "method": "post",
                "body": {
                    "Subject": "Reminder",
                    "Body": "Don't forget!",
                    "To": "me@contoso.com"
                },
                "path": "/Mail"
            }
        }
    }
}
```

La parte de las entradas que es específica de las conexiones de API es el objeto `host`. Este contiene dos partes: `api` y `connection`.

La parte `api` contiene la URL de tiempo de ejecución del lugar donde se hospeda esa API administrada. Puede ver todas las API administradas disponibles para usted mediante la llamada a `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

Cuando use una API, puede tener o no **parámetros de conexión** definidos. Si no tiene, no se requiere ninguna **conexión**. Si tiene, tendrá que crear una conexión. Cuando cree esa conexión, tendrá el nombre que elija y hará referencia a ella en el objeto `connection` dentro del objeto `host`. Para crear una conexión en un grupo de recursos, llame a:

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

Con el siguiente cuerpo:


```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
	"parameterValues" : {
		"accountName" : "{The name of the storage account -- the set of parameters is different for each API}"
	}
  },
  "location" : "{Logic app's location}"
}
```

### Implementación de las API administradas en una plantilla de Azure Resource Manager

Puede crear una aplicación completa en una plantilla de ARM siempre que no requiera inicio de sesión interactivo. Si requiere inicio de sesión, puede configurar todo con la plantilla de ARM, pero aún así tendrá que visitar el portal para autorizar las conexiones.

```
	"resources": [{
		"apiVersion": "2015-08-01-preview",
		"name": "azureblob",
		"type": "Microsoft.Web/connections",
		"location": "[resourceGroup().location]",
		"properties": {
			"api": {
				"id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
			},
			"parameterValues": {
				"accountName": "[parameters('storageAccountName')]",
				"accessKey": "[parameters('storageAccountKey')]"
			}
		}
	}, {
		"type": "Microsoft.Logic/workflows",
		"apiVersion": "2015-08-01-preview",
		"name": "[parameters('logicAppName')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[resourceId('Microsoft.Web/connections', 'azureblob')]"
		],
		"properties": {
			"sku": {
				"name": "[parameters('sku')]",
				"plan": {
					"id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
				}
			},
			"definition": {
				"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
				"actions": {
					"Create_file": {
						"type": "apiconnection",
						"inputs": {
							"host": {
								"api": {
									"runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
								},
								"connection": {
									"name": "@parameters('$connections')['azureblob']['connectionId']"
								}
							},
							"method": "post",
							"queries": {
								"folderPath": "[concat('/',parameters('containerName'))]",
								"name": "helloworld.txt"
							},
							"body": "@decodeDataUri('data:,Hello+world!')",
							"path": "/datasets/default/files"
						},
						"conditions": []
					}
				},
				"contentVersion": "1.0.0.0",
				"outputs": {},
				"parameters": {
					"$connections": {
						"defaultValue": {},
						"type": "Object"
					}
				},
				"triggers": {
					"recurrence": {
						"type": "Recurrence",
						"recurrence": {
							"frequency": "Day",
							"interval": 1
						}
					}
				}
			},
			"parameters": {
				"$connections": {
					"value": {
						"azureblob": {
							"connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
							"connectionName": "azureblob",
							"id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
						}

					}
				}
			}
		}
	}]
```

En este ejemplo puede ver que las conexiones son recursos normales que residen en su grupo de recursos. Hacen referencia a las API administradas disponibles en su suscripción.

### API web personalizadas

Si utiliza sus propias API (en concreto, las que no están administradas por Microsoft), debe emplear la acción **HTTP** integrada para llamarlas. Para tener una buena experiencia, debe exponer un punto de conexión de Swagger para la API. De esta forma, el diseñador de aplicaciones lógicas podrá representar las entradas y salidas de la API. Sin un punto de conexión de Swagger, el diseñador solo podrá mostrar las entradas y salidas como objetos JSON opacos.

Este es un ejemplo que muestra la nueva propiedad `metadata.apiDefinitionUrl`: ```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata" : {
              "apiDefinitionUrl" : "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method" : "GET"
            }
        }
    }
}
```

Si hospeda su API web en el **Servicio de aplicaciones**, se mostrará automáticamente en la lista de acciones disponibles en el diseñador. De lo contrario, tendrá que pegar la dirección URL directamente. El punto de conexión de Swagger debe estar sin autenticar para que se pueda usar dentro del diseñador de Aplicaciones lógicas (aunque puede proteger la API propiamente dicha con cualquier método que se admita en Swagger).

### Uso de las aplicaciones de API ya implementadas con 2015-08-01-preview

Si anteriormente implementó una aplicación de API, puede llamarla mediante la acción **HTTP**.

Por ejemplo, si utiliza Dropbox para mostrar archivos, tendrá algo parecido a esto en su definición de versión de esquema **2014-12-01-preview**: ```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
            "defaultValue": "eyJ0eX...wCn90",
            "type": "String",
            "metadata": {
                "token": {
                    "name": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
                }
            }
        }
    },
    "actions": {
        "dropboxconnector": {
            "type": "ApiApp",
            "inputs": {
                "apiVersion": "2015-01-14",
                "host": {
                    "id": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                    "gateway": "https://avdemo.azurewebsites.net"
                },
                "operation": "ListFiles",
                "parameters": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Puede construir la acción HTTP equivalente como se indica a continuación (la sección de parámetros de la definición de aplicación lógica permanece sin cambios):

```
{
    "actions": {
        "dropboxconnector": {
            "type": "Http",
            "metadata" : {
              "apiDefinitionUrl" : "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
            },
            "inputs": {
                "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
                "method" : "POST",
                "body": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Vamos a recorrer estas propiedades una a una:

| Propiedad de acción | Descripción |
| --------------- | -----------  |
| `type` | `Http` en lugar de `APIapp` |
| `metadata.apiDefinitionUrl` | Si quiere usar esta acción en el diseñador de Aplicaciones lógicas, incluirá el punto de conexión de metadatos. Esto se construye a partir de: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Esto se construye a partir de: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Siempre `POST` |
| `inputs.body` | Idéntico a los parámetros de la aplicación de API | 
| `inputs.authentication` | Idéntico a la autenticación de la aplicación de API |

Este enfoque debería funcionar para todas las acciones de aplicación de API. Sin embargo, tenga en cuenta que estas aplicaciones de API anteriores ya no se admiten, y debe cambiar a una de las otras dos opciones anteriores (una API administrada u hospedar la API web personalizada).

## 2\. Cambio de nombre de Repeat a Foreach

En la versión de esquema anterior, recibimos numerosos comentarios de los clientes diciendo que la instrucción **Repeat** era confusa y no captaba adecuadamente el significado de "para cada bucle". Por eso, le hemos cambiado el nombre por **Foreach**. Por ejemplo:

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "repeat": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{repeatItem()}"
            }
        }
    }
}
```

Ahora se escribiría como:

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "foreach": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{item()}"
            }
        }
    }
}
```

Anteriormente, la función `@repeatItem()` se utilizó para hacer referencia al elemento actual que se repite. Esto se ha simplificado a solo `@item()`.

### Referencia a las salidas de Foreach
Para simplificarlo aún más, las salidas de las acciones de **Foreach** no se incluirán en un objeto llamado **repeatItems**. Esto significa que, mientras que las salidas de la instrucción Repeat anterior eran:

```
{
    "repeatItems": [
        {
            "name": "pingBing",
            "inputs": {
                "uri": "https://www.bing.com/search?q=0",
                "method": "GET"
            },
            "outputs": {
                "headers": { },
                "body": "<!DOCTYPE html><html lang="en" xml:lang="en" xmlns="http://www.w3.org/1999/xhtml" xmlns:Web="http://schemas.live.com/Web/">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

Ahora serán:

```
[
    {
        "name": "pingBing",
        "inputs": {
            "uri": "https://www.bing.com/search?q=0",
            "method": "GET"
        },
        "outputs": {
            "headers": { },
            "body": "<!DOCTYPE html><html lang="en" xml:lang="en" xmlns="http://www.w3.org/1999/xhtml" xmlns:Web="http://schemas.live.com/Web/">...</html>"
        }
        "status": "Succeeded"
    }
]
```

Al hacer referencia a estas salidas, para llegar al cuerpo de la acción, tenía que hacer:

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "repeat" : "@outputs('pingBing').repeatItems",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@repeatItem().outputs.body"
            }
        }
    }
}
```

Ahora puede hacer esto en su lugar:

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "foreach" : "@outputs('pingBing')",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@item().outputs.body"
            }
        }
    }
}
```

Con estos cambios, las funciones `@repeatItem()`, `@repeatBody()` y `@repeatOutputs()` se eliminan.

## 3\. Agente de escucha HTTP nativo 
Las funcionalidades de agente de escucha HTTP ahora están integradas, así que ya no necesita implementar una aplicación de API de agente de escucha HTTP. Consulte aquí [los detalles completos de cómo crear puntos de conexión de aplicación lógica que se pueden llamar](app-service-logic-http-endpoint.md).

Con estos cambios, la función `@accessKeys()` se elimina y se ha sustituido por la función `@listCallbackURL()` a efectos de obtener el punto de conexión (cuando es necesario). Además, ahora debe definir al menos un desencadenador en la aplicación lógica. Si quiere ejecutar el flujo de trabajo con `/run`, deberá tener un desencadenador `manual`, `apiConnectionWebhook` o `httpWebhook`.

## 4\. Llamada a los flujos de trabajo secundarios

Anteriormente, para llamar a los flujos de trabajo secundarios era necesario ir a ese flujo de trabajo, obtener el token de acceso y luego pegarlo en la definición de la aplicación lógica deseada. Con la nueva versión de esquema, el motor de Aplicaciones lógicas generará automáticamente una firma SAS en tiempo de ejecución del flujo de trabajo secundario, lo que significa que no tendrá que pegar ningún secreto en la definición. Aquí tiene un ejemplo:

```
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName" : "myendpointtrigger"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "conditions" : []
}
```

Una segunda mejora es proporcionaremos a los flujos de trabajo secundarios acceso completo a la solicitud entrante. Esto significa que puede pasar parámetros en la sección *queries* y en el objeto *headers* y que puede definir completamente todo el cuerpo.

Por último, hay cambios necesarios en el flujo de trabajo secundario. Mientras que antes podía llamar a un flujo de trabajo secundario directamente, ahora deberá definir un punto de conexión de desencadenamiento en el flujo de trabajo para que el elemento primario realice la llamada. Esto significa, por lo general, agregar un desencadenador de tipo **manual** y luego usarlo en la definición del elemento primario. Tenga en cuenta que, en concreto, la propiedad `host` tiene un valor `triggerName` porque siempre debe especificar qué desencadenador va a invocar.

## Otros cambios

### Nueva propiedad queries
Todos los tipos de acción admiten ahora una nueva entrada llamada **queries**. Esta puede ser un objeto estructurado en lugar de tener que ensamblar la cadena a mano.

### Cambio de nombre de la función parse()
Como pronto agregaremos más tipos de contenido, el nombre de la función `parse()` a cambiado a `json()`.

## Próximamente: API de Enterprise Integration
En este momento, no disponemos aún de versiones administradas de API de Enterprise Integration (como AS2). Pronto llegarán, como se explica en el [mapa de ruta](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). Mientras tanto, puede usar sus API de BizTalk implementadas existentes mediante la acción HTTP, como se ha explicado anteriormente en "Uso de las aplicaciones de API ya implementadas".

<!---HONumber=AcomDC_0224_2016-->