<properties
   pageTitle="Crear plantillas del Administrador de recursos de Azure"
   description="Cree plantillas del Administrador de recursos de Azure mediante la sintaxis declarativa de JSON para implementar aplicaciones en Azure."
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
   ms.date="02/17/2016"
   ms.author="tomfitz"/>

# Creación de plantillas del Administrador de recursos de Azure

Normalmente, las aplicaciones de Azure requieren una combinación de recursos (por ejemplo, un servidor de base de datos, una base de datos o un sitio web) para cumplir los objetivos deseados. En lugar de implementar y administrar cada recurso por separado, puede crear una plantilla del Administrador de recursos de Azure que implementa y aprovisiona todos los recursos de su aplicación en una operación única y coordinada. En la plantilla, se definen los recursos necesarios para la aplicación y se especifican los parámetros de implementación para especificar valores para diferentes entornos. La plantilla consta de JSON y expresiones que puede usar para generar valores para su implementación. En este tema se describen las secciones de la plantilla.

Visual Studio ofrece las herramientas para ayudarle a crear plantillas. Para más información sobre el uso de Visual Studio con las plantillas, vea [Creación e implementación de grupos de recursos de Azure mediante Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

Debe limitar el tamaño de la plantilla a 1 MB y cada archivo de parámetros a 64 KB. El límite de 1 MB se aplica al estado final de la plantilla una vez se ha ampliado con definiciones de recursos iterativas y los valores de variables y parámetros.

## Planeamiento de la plantilla

Antes de comenzar con la plantilla, debe dedicar algún tiempo a averiguar lo que quiere implementar y cómo usará la plantilla. Específicamente, debe plantearse:

1. Qué tipos de recursos tiene que implementar
2. Dónde van a residir los recursos
3. Qué versión de la API del proveedor de recursos usará al implementar el recurso
4. Si alguno de los recursos debe implementarse después de otros recursos
5. Qué valores quiere pasar durante la implementación y qué valores desea definir directamente en la plantilla
6. Si necesita devolver valores de la implementación

Para ayudarle a descubrir qué tipos de recursos están disponibles para la implementación, qué regiones son compatibles con el tipo y las versiones de API disponibles para cada tipo, vea [Proveedores, regiones, versiones de API y esquemas del Administrador de recursos](resource-manager-supported-services.md). En este tema se ofrecen ejemplos y vínculos que le ayudarán a determinan los valores que debe especificar en la plantilla.

Si un recurso debe implementarse después de otro, puede marcarlo como dependiente del otro recurso. Verá cómo hacerlo en la sección [Recursos](#resources) a continuación.

Puede variar el resultado de la implementación de plantilla especificando valores de parámetros durante la ejecución. Verá cómo hacerlo en la sección [Parámetros](#parameters) a continuación.

Puede devolver valores de la implementación en la sección [Salidas](#outputs).

## Formato de plantilla

En la estructura más simple, una plantilla contiene los siguientes elementos.

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| Nombre del elemento | Obligatorio | Descripción
| :------------: | :------: | :----------
| $schema | Sí | Ubicación del archivo de esquema JSON que describe la versión del idioma de la plantilla. Debe usar la dirección URL mostrada anteriormente.
| contentVersion | Sí | Versión de la plantilla (por ejemplo, 1.0.0.0). Puede especificar cualquier valor para este elemento. Al implementar los recursos con la plantilla, este valor se puede usar para asegurarse de que se está usando la plantilla correcta.
| parameters | No | Valores que se proporcionan cuando se ejecuta la implementación para personalizar la implementación de recursos.
| variables | No | Valores que se usan como fragmentos JSON en la plantilla para simplificar expresiones de idioma de la plantilla.
| resources | Sí | Tipos de servicios que se implementan o actualizan en un grupo de recursos.
| outputs | No | Valores que se devuelven después de la implementación.

Examinaremos las secciones de la plantilla con mayor detenimiento más adelante en este tema. Por ahora, voy a revisar parte de la sintaxis que conforma la plantilla.

## Expresiones y funciones

La sintaxis básica de la plantilla es JSON; sin embargo, las expresiones y funciones amplían el JSON que está disponible en la plantilla y le permiten crear valores que no son valores literales estrictos. Las expresiones se incluyen entre corchetes ([ y ]) y se evalúan cuando se implementa la plantilla. Pueden aparecer expresiones en cualquier lugar de un valor de cadena JSON y devolver siempre otro valor JSON. Si necesita usar una cadena literal que comienza por un corchete [, debe usar dos corchetes [[.

Normalmente, se usan expresiones con funciones para realizar operaciones con el fin de configurar la implementación. Al igual que en JavaScript, las llamadas de función tienen el formato **functionName(arg1,arg2,arg3)**. Se hace referencia a las propiedades mediante los operadores dot e [index] .

En el ejemplo siguiente se muestra cómo utilizar algunas de las funciones para la construcción de valores:
 
    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

Para obtener la lista completa de las funciones de plantilla, consulte [Funciones de la plantilla del Administrador de recursos de Azure](./resource-group-template-functions.md).


## Parámetros

En la sección de parámetros de la plantilla, especifique los valores que el usuario puede introducir al implementar los recursos. Estos valores de parámetros permiten personalizar la implementación al proporcionar valores que son específicos para un entorno concreto (por ejemplo, desarrollo, prueba y producción). No tiene que especificar parámetros en la plantilla, pero sin parámetros la plantilla implementaría siempre los mismos recursos con los mismos nombres, ubicaciones y propiedades.

Puede usar estos valores de parámetros a lo largo de la plantilla para establecer valores para los recursos implementados. Solo los parámetros declarados en la sección de parámetros se pueden usar en otras secciones de la plantilla.

En la sección de parámetros, no puede usar un valor de parámetro para construir otro valor de parámetro. Cree nuevos valores en la sección de variables.

Defina recursos con la estructura siguiente:

    "parameters": {
       "<parameterName>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<optional-default-value-of-parameter>",
         "allowedValues": [ "<optional-array-of-allowed-values>" ],
         "minValue": <optional-minimum-value-for-int-parameters>,
         "maxValue": <optional-maximum-value-for-int-parameters>,
         "minLength": <optional-minimum-length-for-string-secureString-array-parameters>,
         "maxLength": <optional-maximum-length-for-string-secureString-array-parameters>,
         "metadata": {
             "description": "<optional-description-of-the parameter>" 
         }
       }
    }

| Nombre del elemento | Obligatorio | Descripción
| :------------: | :------: | :----------
| parameterName | Sí | Nombre del parámetro. Debe ser un identificador válido de JavaScript.
| type | Sí | Tipo del valor del parámetro. Consulte la lista siguiente de tipos permitidos.
| defaultValue | No | Valor predeterminado del parámetro, si no se proporciona ningún valor.
| allowedValues | No | Matriz de valores permitidos para el parámetro para asegurarse de que se proporciona el valor correcto.
| minValue | No | El valor mínimo de parámetros de tipo int, este valor es inclusivo.
| maxValue | No | El valor máximo de parámetros de tipo int, este valor es inclusivo.
| minLength | No | La longitud mínima de los parámetros de tipo cadena, secureString y matriz, este valor es inclusivo.
| maxLength | No | La longitud máxima de los parámetros de tipo cadena, secureString y matriz, este valor es inclusivo.
| description | No | Descripción del parámetro que se mostrará a los usuarios de la plantilla mediante la interfaz de plantilla personalizada del portal.

Los valores y tipos permitidos son los siguientes:

- string o secureString: cualquier cadena JSON válida
- int: cualquier entero JSON válido
- bool: cualquier booleano JSON válido
- object o secureObject: cualquier objeto JSON válido
- array: cualquier matriz JSON válida

Para especificar un parámetro como opcional, establezca su defaultValue en una cadena vacía.

Si especifica un parámetro con un nombre que coincide con el de uno de los parámetros del comando usado para implementar la plantilla (por ejemplo, incluye un parámetro denominado **ResourceGroupName** en la plantilla y este parámetro es el mismo que el parámetro **ResourceGroupName** del cmdlet [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx)), se le pedirá que proporcione un valor para un parámetro con el sufijo **FromTemplate** (como **ResourceGroupNameFromTemplate**). Por lo general, debe evitar esta confusión no nombrando los parámetros con el mismo nombre que los parámetros utilizados para operaciones de implementación.

>[AZURE.NOTE] Todas las contraseñas, claves y otros secretos deben utilizar el tipo **secureString**. No se pueden leer los parámetros de plantilla con el tipo secureString después de la implementación de recursos.

En el ejemplo siguiente se muestra cómo definir los parámetros.

    "parameters": {
       "siteName": {
          "type": "string",
          "minLength": 2,
          "maxLength": 60
       },
       "siteLocation": {
          "type": "string",
          "minLength": 2
       },
       "hostingPlanName": {
          "type": "string"
       },  
       "hostingPlanSku": {
          "type": "string",
          "allowedValues": [
            "Free",
            "Shared",
            "Basic",
            "Standard",
            "Premium"
          ],
          "defaultValue": "Free"
       },
       "instancesCount": {
          "type": "int",
          "maxValue": 10
       },
       "numberOfWorkers": {
          "type": "int",
          "minValue": 1
       }
    }

Para más información sobre la especificación de valores de parámetros durante la implementación, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](../resource-group-template-deploy/#parameter-file).

## Variables

En la sección de variables, se crean valores que pueden usarse en toda la plantilla. Normalmente, estas variables se basarán en los valores proporcionados de los parámetros. No es necesario definir las variables, pero a menudo simplifican la plantilla reduciendo expresiones complejas.

Defina variables con la siguiente estructura:

    "variables": {
       "<variable-name>": "<variable-value>",
       "<variable-name>": { 
           <variable-complex-type-value> 
       }
    }

En el ejemplo siguiente se muestra cómo definir una variable que se construye a partir de dos valores de parámetro:

    "parameters": {
       "username": {
         "type": "string"
       },
       "password": {
         "type": "secureString"
       }
     },
     "variables": {
       "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
    }

En el ejemplo siguiente se muestra una variable que es un tipo JSON complejo y las variables que se construyen a partir de otras variables:

    "parameters": {
       "environmentName": {
         "type": "string",
         "allowedValues": [
           "test",
           "prod"
         ]
       }
    },
    "variables": {
       "environmentSettings": {
         "test": {
           "instancesSize": "Small",
           "instancesCount": 1
         },
         "prod": {
           "instancesSize": "Large",
           "instancesCount": 4
         }
       },
       "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
       "instancesSize": "[variables('currentEnvironmentSettings').instancesSize",
       "instancesCount": "[variables('currentEnvironmentSettings').instancesCount"
    }

## Recursos

En la sección de recursos, se define que los recursos se implementan o se actualizan. Aquí es donde la plantilla puede ser más complicada porque es preciso entender los tipos que se implementan para proporcionar los valores correctos. Para conocer gran parte de lo que necesita saber sobre proveedores de recursos, consulte [Proveedores, regiones, versiones de API y esquemas del Administrador de recursos](resource-manager-supported-services.md).

Defina recursos con la siguiente estructura:

    "resources": [
       {
         "apiVersion": "<api-version-of-resource>",
         "type": "<resource-provider-namespace/resource-type-name>",
         "name": "<name-of-the-resource>",
         "location": "<location-of-resource>",
         "tags": "<name-value-pairs-for-resource-tagging>",
         "comments": "<your-reference-notes>",
         "dependsOn": [
           "<array-of-related-resource-names>"
         ],
         "properties": "<settings-for-the-resource>",
         "resources": [
           "<array-of-child-resources>"
         ]
       }
    ]

| Nombre del elemento | Obligatorio | Descripción
| :----------------------: | :------: | :----------
| apiVersion | Sí | Versión de la API de REST que debe usar para crear el recurso. Para determinar los números de versión disponibles para un determinado tipo de recurso, consulte [Versiones de API compatibles](../resource-manager-supported-services/#supported-api-versions).
| type | Sí | Tipo de recurso. Este valor es una combinación del espacio de nombres del proveedor de recursos y el tipo de recurso que admite el proveedor de recursos.
| name | Sí | Nombre del recurso. El nombre debe cumplir las restricciones de componente URI definidas en RFC3986.
| location | No | Ubicaciones geográficas compatibles del recurso proporcionado. Para determinar las ubicaciones disponibles, consulte [Regiones admitidas](../resource-manager-supported-services/#supported-regions).
| etiquetas | No | Etiquetas asociadas al recurso.
| comentarios | No | Notas para documentar los recursos de la plantilla
| dependsOn | No | Recursos de los que depende el recurso que se está definiendo. Las dependencias entre los recursos se evalúan y los recursos se implementan en su orden dependiente. Cuando no hay recursos dependientes entre sí, se intenta implementarlos en paralelo. El valor puede ser una lista separada por comas de nombres de recursos o identificadores de recursos únicos.
| propiedades | No | Opciones de configuración específicas de recursos. Los valores de las propiedades son exactamente los mismos valores que se especifican en el cuerpo de la solicitud de la operación de API de REST (método PUT) para crear el recurso. Para obtener vínculos a documentación del esquema de recursos o la API de REST, consulte [Proveedores, regiones, versiones de API y esquemas del Administrador de recursos](resource-manager-supported-services.md).
| resources | No | Recursos secundarios que dependen del recurso que se está definiendo. Solo puede proporcionar los tipos de recursos que permite el esquema del recurso principal. El nombre completo del tipo de recurso secundario incluye el tipo de recurso principal, como **Microsoft.Web/sites/extensions**. La dependencia del recurso primario no está implícita; debe definir explícitamente esa dependencia. 


Si el nombre del recurso no es único, puede usar la función auxiliar **resourceId** (descrita a continuación) para obtener el identificador único para cualquier recurso.

La sección de recursos contiene una matriz de los recursos para implementar. En cada recurso, puede definir también una matriz de recursos secundarios para esos recursos. Por lo tanto, la sección de recursos podría tener una estructura como:

    "resources": [
       {
           "name": "resourceA",
           ...
       },
       {
           "name": "resourceB",
           ...
           "resources": [
               {
                   "name": "firstChildResourceB",
                   ...
               },
               {   
                   "name": "secondChildResourceB",
                   ...
               }
           ]
       },
       {
           "name": "resourceC",
           ...
       }
    ]



En el ejemplo siguiente se muestra un recurso **Microsoft.Web/serverfarms** y un recurso **Microsoft.Web/sites** con un recurso secundario **Extensions**: Observe que el sitio se ha marcado como dependiente de la granja de servidores ya que la granja de servidores debe existir antes para que se pueda implementar el sitio. Observe también que el recurso **Extensions** es un elemento secundario del sitio.

    "resources": [
        {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/serverfarms",
          "name": "[parameters('hostingPlanName')]",
          "location": "[resourceGroup().location]",
          "properties": {
              "name": "[parameters('hostingPlanName')]",
              "sku": "[parameters('hostingPlanSku')]",
              "workerSize": "0",
              "numberOfWorkers": 1
          }
        },
        {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          "tags": {
              "environment": "test",
              "team": "ARM"
          },
          "dependsOn": [
              "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
          ],
          "properties": {
              "name": "[parameters('siteName')]",
              "serverFarm": "[parameters('hostingPlanName')]"
          },
          "resources": [
              {
                  "apiVersion": "2014-06-01",
                  "type": "Extensions",
                  "name": "MSDeploy",
                  "dependsOn": [
                      "[resourceId('Microsoft.Web/sites', parameters('siteName'))]"
                  ],
                  "properties": {
                    "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                      "Application Path": "[parameters('siteName')]"
                    }
                  }
              }
          ]
        }
    ]


## Salidas

En la sección de salidas, especifique valores que se devuelven de la implementación. Por ejemplo, podría devolver el URI para acceder a un recurso implementado.

En el ejemplo siguiente se muestra la estructura de una definición de salida:

    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>",
       }
    }

| Nombre del elemento | Obligatorio | Descripción
| :------------: | :------: | :----------
| outputName | Sí | Nombre del valor de salida. Debe ser un identificador válido de JavaScript.
| type | Sí | Tipo del valor de salida. Los valores de salida admiten los mismos tipos que los parámetros de entrada de plantilla.
| value | Sí | Expresión de lenguaje de plantilla que se evaluará y devolverá como valor de salida.


En el ejemplo siguiente se muestra un valor que se devuelve en la sección de salidas.

    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

## Escenarios más avanzados.
En este tema se ofrece una visión preliminar de la plantilla. Sin embargo, el escenario puede requerir tareas más avanzadas.

Puede que necesite combinar dos plantillas o usar una plantilla secundaria dentro de una plantilla principal. Para más información, consulte [Uso de plantillas vinculadas con el Administrador de recursos de Azure](resource-group-linked-templates.md).

Para iterar una cantidad de veces específica al crear un tipo de recurso, vea [Creación de varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md).

Puede que necesite usar los recursos que existen dentro de un grupo de recursos diferente. Esto es habitual al trabajar con cuentas de almacenamiento o redes virtuales que se comparten entre varios grupos de recursos. Para obtener más información, vea la [función resourceId](../resource-group-template-functions#resourceid).

## Plantilla completa
La siguiente plantilla implementa una aplicación web y aprovisiona con código desde un archivo. zip.

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "siteName": {
           "type": "string"
         },
         "hostingPlanName": {
           "type": "string"
         },
         "hostingPlanSku": {
           "type": "string",
           "allowedValues": [
             "Free",
             "Shared",
             "Basic",
             "Standard",
             "Premium"
           ],
           "defaultValue": "Free"
         }
       },
       "resources": [
         {
           "apiVersion": "2014-06-01",
           "type": "Microsoft.Web/serverfarms",
           "name": "[parameters('hostingPlanName')]",
           "location": "[resourceGroup().location]",
           "properties": {
             "name": "[parameters('hostingPlanName')]",
             "sku": "[parameters('hostingPlanSku')]",
             "workerSize": "0",
             "numberOfWorkers": 1
           }
         },
         {
           "apiVersion": "2014-06-01",
           "type": "Microsoft.Web/sites",
           "name": "[parameters('siteName')]",
           "location": "[resourceGroup().location]",
           "tags": {
             "environment": "test",
             "team": "ARM"
           },
           "dependsOn": [
             "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
           ],
           "properties": {
             "name": "[parameters('siteName')]",
             "serverFarm": "[parameters('hostingPlanName')]"
           },
           "resources": [
             {
               "apiVersion": "2014-06-01",
               "type": "Extensions",
               "name": "MSDeploy",
               "dependsOn": [
                 "[resourceId('Microsoft.Web/sites', parameters('siteName'))]"
               ],
               "properties": {
                 "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
                 "dbType": "None",
                 "connectionString": "",
                 "setParameters": {
                   "Application Path": "[parameters('siteName')]"
                 }
               }
             }
           ]
         }
       ],
       "outputs": {
         "siteUri": {
           "type": "string",
           "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
         }
       }
    }

## Pasos siguientes
- Para información detallada sobre las funciones que puede usar desde una plantilla, consulte [Funciones de la plantilla del Administrador de recursos de Azure](resource-group-template-functions.md).
- Para saber cómo implementar la plantilla que creó, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).
- Para obtener un ejemplo en profundidad de la implementación de una aplicación, consulte [Aprovisionamiento e implementación predecibles de microservicios en Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Para ver los esquemas disponibles, consulte [Esquemas del Administrador de recursos de Azure](https://github.com/Azure/azure-resource-manager-schemas).

<!---HONumber=AcomDC_0224_2016-->