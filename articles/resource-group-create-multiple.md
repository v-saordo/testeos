<properties
   pageTitle="Implementación de varias instancias de recursos | Microsoft Azure"
   description="Use la operación de copia y matrices en una plantilla del Administrador de recursos de Azure para iterar varias veces al implementar recursos."
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
   ms.date="01/15/2016"
   ms.author="tomfitz"/>

# Creación de varias instancias de recursos en el Administrador de recursos de Azure

En este tema se muestra cómo iterar en la plantilla del Administrador de recursos de Azure para crear varias instancias de un recurso.

## copy, copyIndex y length

En el recurso que se va a crear varias veces, puede definir un objeto **copy** que especifica el número de veces que se va a iterar. La copia tiene el formato siguiente:

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

Se puede obtener acceso al valor de iteración actual con la función **copyIndex()**, como se muestra a continuación dentro de la función concat.

    [concat('examplecopy-', copyIndex())]

Al crear varios recursos desde una matriz de valores, puede usar la función **length** para especificar el recuento. Especifique la matriz como parámetro para la función length.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

## Uso del valor de índice en el nombre

Puede usar la operación de copia para crear varias instancias de un recurso que se nombra exclusivamente según el índice incremental. Por ejemplo, puede que desee agregar un número exclusivo al final de cada nombre de recurso que se implementa. Para implementar tres sitios web con los nombres:

- examplecopy-0
- examplecopy-1
- examplecopy-2.

Use la siguiente plantilla:

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          }
      } 
    ]

## Valor de índice de desplazamiento

Observará en el ejemplo anterior que el valor de índice va de cero a 2. Para desplazar el valor de índice, puede pasar un valor en la función **copyIndex()**, como por ejemplo **copyIndex(1)**. El número de iteraciones que se deben realizar todavía se especifica en el elemento copy, pero el valor de copyIndex se desplaza el valor especificado. Por lo tanto, con la misma plantilla que en el ejemplo anterior, pero especificando **copyIndex(1)**, implementaría tres sitios web con los nombres:

- examplecopy-1
- examplecopy-2
- examplecopy-3

## Uso con la matriz
   
La operación de copia es especialmente útil al trabajar con matrices, ya que puede iterar a través de cada elemento de la matriz. Para implementar tres sitios web con los nombres:

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

Use la siguiente plantilla:

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      }
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          } 
      } 
    ]

Por supuesto, establezca el número de copias en un valor distinto de la longitud de la matriz. Por ejemplo, podría crear una matriz con muchos valores y, a continuación, pasar un valor de parámetro que especifique el número de elementos de la matriz a implementar. En ese caso, establezca el número de copias como se muestra en el primer ejemplo.

## Dependencia de los recursos de un bucle

Puede especificar que un recurso se implemente después de otro recurso mediante el elemento **dependsOn**. Cuando necesite implementar un recurso que dependa de la colección de recursos de un bucle, puede proporcionar el nombre del bucle copy en el elemento **dependsOn**. En el ejemplo siguiente se muestra cómo implementar 3 cuentas de almacenamiento antes de implementar la máquina virtual. La definición de la máquina virtual no se muestra. Tenga en cuenta que el elemento copy tiene **name** establecido en **storagecopy** y el elemento **dependsOn** para las máquinas virtuales también se establece en **storagecopy**.

    {
	    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "contentVersion": "1.0.0.0",
	    "parameters": {},
	    "resources": [
	        {
		        "apiVersion": "2015-06-15",
		        "type": "Microsoft.Storage/storageAccounts",
		        "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
		        "location": "[resourceGroup().location]",
		        "properties": {
                    "accountType": "Standard_LRS"
            	 },
		        "copy": { 
         	        "name": "storagecopy", 
         	        "count": 3 
      		    }
	        },
           {
               "apiVersion": "2015-06-15", 
               "type": "Microsoft.Compute/virtualMachines", 
               "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
               "dependsOn": ["storagecopy"],
               ...
           }
	    ],
	    "outputs": {}
    }

## Recurso anidado de bucle

No puede usar un bucle de copia de un recurso anidado. Si necesita crear varias instancias de un recurso que se define normalmente como anidado dentro de otro recurso, debe en su lugar, crear el recurso como un recurso de nivel superior y definir la relación con el recurso primario a través del **tipo** y las propiedades del **nombre**.

Por ejemplo, supongamos que suele definir un conjunto de datos como un recurso anidado dentro de una factoría de datos.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "string"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
        "resources": [
        {
            "type": "datasets",
            "name": "[parameters('dataSetName')]",
            "dependsOn": [
                "[parameters('dataFactoryName')]"
            ],
            ...
        }
    }]
    
Para crear varias instancias de conjuntos de datos, necesitará cambiar la plantilla, tal como se muestra a continuación. Observe que el tipo cualificado completo y el nombre incluyen el nombre de la factoría de datos.

    "parameters": {
        "dataFactoryName": {
            "type": "string"
         },
         "dataSetName": {
            "type": "array"
         }
    },
    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[parameters('dataFactoryName')]",
        ...
    },
    {
        "type": "Microsoft.DataFactory/datafactories/datasets",
        "name": "[concat(parameters('dataFactoryName'), '/', parameters('dataSetName')[copyIndex()])]",
        "dependsOn": [
            "[parameters('dataFactoryName')]"
        ],
        "copy": { 
            "name": "datasetcopy", 
            "count": "[length(parameters('dataSetName'))]" 
        } 
        ...
    }]

## Pasos siguientes
- Para obtener información sobre las secciones de una plantilla, consulte [Creación de plantillas del Administrador de recursos de Azure](./resource-group-authoring-templates.md).
- Para obtener todas las funciones que puede usar en una plantilla, consulte [Funciones de la plantilla del Administrador de recursos de Azure](./resource-group-template-functions.md).
- Para obtener información sobre cómo implementar la plantilla, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0121_2016-->