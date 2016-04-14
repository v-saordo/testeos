<properties
	pageTitle="Directiva del Administrador de recursos de Azure | Microsoft Azure"
	description="Describe cómo usar la directiva del Administrador de recursos de Azure para evitar infracciones en distintos ámbitos como, por ejemplo, la suscripción, los grupos de recursos o los recursos individuales."
	services="azure-resource-manager"
	documentationCenter="na"
	authors="ravbhatnagar"
	manager="ryjones"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="02/26/2016"
	ms.author="gauravbh;tomfitz"/>

# Uso de directivas para administrar los recursos y controlar el acceso

El Administrador de recursos de Azure permite controlar el acceso mediante directivas personalizadas. Con las directivas, puede impedir que los usuarios de su organización dividan las convenciones que se necesitan para administrar los recursos de esta.

Se crean definiciones de directivas que describen las acciones o los recursos que se han denegado específicamente. Esas definiciones de directivas se asignan en el ámbito deseado, como la suscripción, el grupo de recursos o un recurso individual.

En este artículo se explica la estructura básica del lenguaje de definición de directivas que se puede usar para crear directivas. A continuación, describiremos cómo es posible aplicar estas directivas en distintos ámbitos y, finalmente, mostraremos algunos ejemplos de aplicación mediante la API de REST.

La directiva está actualmente disponible como vista previa.

## ¿En qué se diferencia de RBAC?

Existen algunas diferencias importantes entre el control de acceso basado en roles y las directivas, pero lo primero que hay que entender es que las directivas y RBAC funcionan conjuntamente. Para poder usar la directiva, el usuario debe autenticarse a través de RBAC. A diferencia de RBAC, la directiva es un sistema que permite de manera predeterminada y niega explícitamente.

RBAC se centra en las acciones que un **usuario** puede realizar en distintos ámbitos. Por ejemplo, un usuario determinado se agrega al rol de colaborador para un grupo de recursos en el ámbito deseado, por lo que el usuario puede realizar cambios en ese grupo de recursos.

La directiva se centra en acciones de **recursos** en varios ámbitos. Por ejemplo, mediante directivas, puede controlar los tipos de recursos que se pueden aprovisionar o restringir las ubicaciones en las que se pueden aprovisionar los recursos.

## Escenarios comunes

Un escenario común es exigir etiquetas departamentales para contracargos. Es posible que una organización solo quiera permitir operaciones cuando se asocie el centro de coste correcto.; en caso contrario, denegará la solicitud. Esto ayudaría a aplicar el cargo al centro de costes correcto para las operaciones realizadas.

Otro escenario común es que la organización que desee controlar las ubicaciones en las que se crean los recursos. O bien, puede que desee controlar el acceso a los recursos al permitir el aprovisionamiento solo de determinados tipos de recursos.

Del mismo modo, una organización puede controlar el catálogo de servicios o aplicar las convenciones de nombre deseadas para los recursos.

Con las directivas, estos escenarios pueden conseguirse fácilmente tal como se describe a continuación.

## Estructura de definición de directiva

La definición de la directiva se crea mediante JSON. Consta de uno o más operadores lógicos/condicionales que definen las acciones y un efecto que indica lo que ocurre cuando se cumplen las condiciones. El esquema está publicado en [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json).

Básicamente, una directiva contiene lo siguiente:

**Operadores lógicos/condicionales:** contiene un conjunto de condiciones que se pueden manipular mediante un conjunto de operadores lógicos.

**Efecto:** describe cuál será el efecto cuando se cumpla la condición: denegar o auditoría. Un efecto de auditoría emitirá un registro de servicio de eventos de advertencia. Por ejemplo, un administrador puede crear una directiva que provoque una auditoría si alguien crea una máquina virtual grande, y revisar los registros más adelante.

    {
      "if" : {
        <condition> | <logical operator>
      },
      "then" : {
        "effect" : "deny | audit"
      }
    }


## Operadores lógicos

A continuación se muestran los operadores lógicos admitidos junto con la sintaxis:

| Nombre del operador | Sintaxis |
| :------------- | :------------- |
| Not | "not" : {&lt;condition or operator &gt;} |
| Y | "allOf" : [ {&lt;condition or operator &gt;},{&lt;condition or operator &gt;}] |
| O | "anyOf" : [ {&lt;condition or operator &gt;},{&lt;condition or operator &gt;}] |

El Administrador de recursos permite especificar una lógica compleja en su directiva a través de operadores anidados. Por ejemplo, puede denegar la creación de recursos en una ubicación determinada para un tipo de recurso especificado. A continuación se muestra un ejemplo de operadores anidados.

## Condiciones

Una condición evalúa si un **campo** o un **origen** cumple determinados criterios. La sintaxis y los nombres de condición admitidos son los siguientes:

| Nombre de la condición | Sintaxis |
| :------------- | :------------- |
| Equals | "equals" : "&lt;valor&gt;" |
| Like | "like" : "&lt;valor&gt;" |
| Contains | "contains" : "&lt;valor&gt;"|
| En el | "in" : [ "&lt;valor1&gt;","&lt;valor2&gt;" ]|
| ContainsKey | "containsKey" : "&lt;nombre de clave&gt;" |

## Campos y orígenes

Las condiciones se crean mediante el uso de campos y orígenes. Un campo representa las propiedades de la carga de solicitud de recursos que se usa para describir el estado del recurso. Un origen representa las características de la propia solicitud.

Estos son los campos y orígenes admitidos:

Campos: **name**, **kind**, **type**, **location**, **tags**, **tags.*** y **property alias**.

Orígenes: **action**.

El alias de propiedad es un nombre que se puede usar en la definición de directiva para acceder a las propiedades específicas del tipo de recursos, como la configuración y la SKU. Funciona en todas las versiones de API en las que existe la propiedad. Los alias se pueden recuperar mediante la API de REST que se indica a continuación (en el futuro se agregará compatibilidad con Powershell):

    GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
	
La definición de un alias se parece a lo siguiente. Como puede ver, un alias define rutas de acceso en distintas versiones de API, aunque se cambie el nombre de la propiedad.

    "aliases": [
      {
        "name": "Microsoft.Storage/storageAccounts/sku.name",
        "paths": [
          {
            "path": "Properties.AccountType",
            "apiVersions": [ "2015-06-15", "2015-05-01-preview" ]
          }
        ]
      }
    ]

Actualmente, los alias admitidos son:

| Nombre de alias | Descripción |
| ---------- | ----------- |
| {resourceType}/sku.name | Los tipos de recursos admitidos son: Microsoft.Storage/storageAccounts,<br />Microsoft.Scheduler/jobcollections,<br />Microsoft.DocumentDB/databaseAccounts,<br />Microsoft.Cache/Redis,<br />Microsoft..CDN/profiles |
| {resourceType}/sku.family | El tipo de recurso admitido es Microsoft.Cache/Redis |
| {resourceType}/sku.capacity | El tipo de recurso admitido es Microsoft.Cache/Redis |
| Microsoft.Cache/Redis/enableNonSslPort | |
| Microsoft.Cache/Redis/shardCount | |


Para obtener más información acerca de las acciones, vea [RBAC - Roles integrados](active-directory/role-based-access-built-in-roles.md). Actualmente, la directiva solo funciona en las solicitudes PUT.


## Ejemplos de definición de directivas

A continuación veremos cómo se va a definir la directiva para obtener los escenarios mencionados anteriormente.

### Contracargo: requiere etiquetas departamentales

La directiva siguiente rechaza todas las solicitudes que no tengan una etiqueta que contiene la clave "costCenter".

    {
      "if": {
        "not" : {
          "field" : "tags",
          "containsKey" : "costCenter"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }


### Cumplimiento geográfico: garantiza las ubicaciones de los recursos

El ejemplo siguiente muestra una directiva que denegará todas las solicitudes cuya ubicación no sea Europa del Norte o Europa Occidental.

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### Selección de servicio: selecciona el catálogo de servicios

El ejemplo siguiente muestra el uso del origen. Muestra que solo se permiten acciones en los servicios de tipo Microsoft.Resources/*, Microsoft.Compute/*, Microsoft.Storage/* y Microsoft.Network/*. Se denegará todo lo demás.

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "source" : "action",
              "like" : "Microsoft.Resources/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Compute/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Storage/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### Uso de SKU aprobadas

En el ejemplo siguiente se muestra el uso del alias de propiedad para restringir las SKU. En el ejemplo siguiente, solo se aprueba el uso de Standard\_LRS y Standard\_GRS para cuentas de almacenamiento.

    {
      "if": {
        "allOf": [
          {
            "source": "action",
            "like": "Microsoft.Storage/storageAccounts/*"
          },
          {
            "not": {
              "allof": [
                {
                  "field": "Microsoft.Storage/storageAccounts/accountType",
                  "in": ["Standard_LRS", "Standard_GRS"]
                }
              ]
            }
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }
    

### Convención de nomenclatura

El ejemplo siguiente muestra el uso de caracteres comodín compatibles con la condición "like". La condición indica que se denegará la solicitud si el nombre coincide con el patrón mencionado (namePrefix*nameSuffix).

    {
      "if" : {
        "not" : {
          "field" : "name",
          "like" : "namePrefix*nameSuffix"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }
    
### Requisito de etiqueta solo para los recursos de almacenamiento

En el ejemplo siguiente se muestra cómo anidar operadores lógicos para que requieran una etiqueta de aplicación solo para los recursos de almacenamiento.

    {
        "if": {
            "allOf": [
              {
                "not": {
                  "field": "tags",
                  "containsKey": "application"
                }
              },
              {
                "source": "action",
                "like": "Microsoft.Storage/*"
              }
            ]
        },
        "then": {
            "effect": "audit"
        }
    }

## Asignación de directiva

Las directivas se pueden aplicar en distintos ámbitos como, por ejemplo, la suscripción, los grupos de recursos y los recursos individuales. Todos los recursos secundarios heredan las directivas. De este modo, si una directiva se aplica a un grupo de recursos, será aplicable a todos los recursos de dicho grupo de recursos.

## Creación de una directiva

En esta sección se ofrecen detalles sobre cómo se puede crear una directiva usando la API de REST.

### Crear una definición de directiva con la API de REST

Puede crear una directiva con la [API de REST para definiciones de directiva](https://msdn.microsoft.com/library/azure/mt588471.aspx). La API de REST permite crear y eliminar definiciones de directiva, así como recuperar información sobre las definiciones existentes.

Para crear una nueva directiva, ejecute lo siguiente:

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

Con un cuerpo de solicitud similar al siguiente:

    {
      "properties":{
        "policyType":"Custom",
        "description":"Test Policy",
        "policyRule":{
          "if" : {
            "not" : {
              "field" : "tags",
              "containsKey" : "costCenter"
            }
          },
          "then" : {
            "effect" : "deny"
          }
        }
      },
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
      "type":"Microsoft.Authorization/policyDefinitions",
      "name":"testdefinition"
    }


La definición de la directiva puede definirse como uno de los ejemplos anteriores. Para la versión de la API, use *2015-10-01-preview*. Para obtener más ejemplos y más detalles, vea la [API de REST para definiciones de directiva](https://msdn.microsoft.com/library/azure/mt588471.aspx).

### Crear una definición de directiva con PowerShell

Puede crear una nueva definición de directiva mediante el cmdlet New-AzureRmPolicyDefinition tal como se muestra a continuación. En los ejemplos siguientes se crea una directiva para permitir los recursos solo en Europa del Norte y Europa occidental.

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation onlyin certain regions" -Policy '{	"if" : {
    	    			    "not" : {
    	      			    	"field" : "location",
    	      			    		"in" : ["northeurope" , "westeurope"]
    	    			    	}
    	    		          },
    	      		    		"then" : {
    	    			    		"effect" : "deny"
    	      			    		}
    	    		    	}'    		

El resultado de la ejecución se almacena en el objeto $policy para que se pueda usar posteriormente durante la asignación de la directiva. Para el parámetro de directiva, también se puede proporcionar la ruta de acceso a un archivo .json que contenga la directiva en lugar de especificar la directiva en línea tal como se muestra a continuación.

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain 	regions" -Policy "path-to-policy-json-on-disk"


## Aplicación de una directiva

### Asignación de directivas con la API de REST

Puede aplicar la definición de la directiva en el ámbito deseado a través de la [API de REST para asignaciones de directivas](https://msdn.microsoft.com/library/azure/mt588466.aspx). La API de REST permite crear y eliminar asignaciones de directiva, así como recuperar información sobre las asignaciones existentes.

Para crear una nueva asignación de directiva, ejecute:

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

{policy-assignment} es el nombre de la asignación de directiva. Para obtener la versión de la API, use *2015-10-01-preview*.

Con un cuerpo de solicitud similar al siguiente:

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyAssignments/VMPolicyAssignment",
      "type":"Microsoft.Authorization/policyAssignments",
      "name":"VMPolicyAssignment"
    }

Para ejemplos y más detalles, vea la [API de REST para asignaciones de directivas](https://msdn.microsoft.com/library/azure/mt588466.aspx).

### Asignación de directivas con PowerShell

Puede aplicar la directiva creada anteriormente a través de PowerShell en el ámbito deseado mediante el cmdlet New-AzureRmPolicyAssignment tal como se muestra a continuación:

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>
        
Aquí $policy es el objeto de la directiva que se devolvió como resultado de ejecutar el cmdlet New-AzureRmPolicyDefinition tal como se muestra más arriba. En este caso, el ámbito es el nombre del grupo de recursos que especifique.

Si desea quitar la asignación de la directiva anterior, puede hacerlo de la siguiente manera:

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

Puede obtener, cambiar o quitar definiciones de la directiva mediante los cmdlets Get-AzureRmPolicyDefinition, Set-AzureRmPolicyDefinition y Remove-AzureRmPolicyDefinition respectivamente.

De forma similar, puede obtener, cambiar o quitar las asignaciones de directivas mediante los cmdlets Get-AzureRmPolicyAssignment, Set-AzureRmPolicyAssignment y Remove-AzureRmPolicyAssignment respectivamente.

##Eventos de auditoría de directivas

Después de aplicar la directiva, puede empezar a ver los eventos relacionados con ella. Puede ir al portal o usar PowerShell para obtener estos datos.

Para ver todos los eventos relacionados con el efecto de denegación, puede usar el siguiente comando.

    Get-AzureRmLog | where {$_.subStatus -eq "Forbidden"}     

Para ver todos los eventos relacionados con el efecto de auditoría, puede usar el siguiente comando.

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/audit/action"} 
    

<!---HONumber=AcomDC_0302_2016-->