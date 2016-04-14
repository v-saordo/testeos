<properties
   pageTitle="Definición de dependencias en plantillas del Administrador de recursos de Azure"
   description="Describe cómo establecer un recurso como dependiente de otro recurso durante la implementación."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="mmercuri"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/07/2015"
   ms.author="mmercuri"/>

# Definición de dependencias en plantillas del Administrador de recursos de Azure

Para un recurso determinado, puede haber varias dependencias ascendentes y secundarias que son críticas para el éxito de la topología. Puede definir las dependencias de otros recursos mediante **dependsOn** y la propiedad **resources** de un recurso. También se puede especificar una dependencia mediante la función **reference**.

    {
        "name": "<name-of-the-resource>",
        "type": "<resource-provider-namespace/resource-type-name>",
        "apiVersion": "<supported-api-version-of-resource>",
        "location": "<location-of-resource>",
        "tags": { <name-value-pairs-for-resource-tagging> },
        "dependsOn": [ <array-of-related-resource-names> ],
        "properties": { <settings-for-the-resource> },
        "resources": { <dependent-resources> }
    }

 También hay vínculos de recursos que pueden definir relaciones entre recursos y respaldar la definición de estas relaciones entre los grupos de recursos.

## dependsOn

Para una máquina virtual determinada, puede que dependa de tener un recurso de base de datos bien aprovisionado. En otro caso, puede que dependa de la instalación de varios nodos del clúster para poder implementar una máquina virtual con la herramienta de administración de clúster.

Dentro de la plantilla, la propiedad dependsOn ofrece la posibilidad de definir esta dependencia para un recurso. Su valor puede ser una lista de nombres de recursos separados por coma. Las dependencias entre los recursos se evalúan y los recursos se implementan en su orden dependiente. Cuando no hay recursos dependientes entre sí, se intenta implementarlos en paralelo.

Si bien puede que se sienta tentado a usar dependsOn para asignar dependencias entre los recursos, es importante comprender por qué lo hace ya que puede afectar al rendimiento de la implementación. Por ejemplo, si lo hace porque desea documentar cómo están interconectados los recursos, dependsOn no es el enfoque correcto. El ciclo de vida de dependsOn es solo para la implementación y no está disponible para después de esta. Después de la implementación, no hay ninguna manera de consultar estas dependencias. Con el uso de dependsOn corre el riesgo de que el rendimiento sea vea afectado e impedir sin darse cuenta que el motor de implementación use los paralelismos que podrían haber. Para documentar y proporcionar capacidad de consulta sobre las relaciones entre los recursos, debe usar la [vinculación de recursos](resource-group-link-resources.md).

Este elemento no es necesario si se usa la función reference para obtener una representación de un recurso, ya que un objeto de referencia implica una dependencia del recurso. De hecho, si hay una opción para usar reference frente a dependsOn, lo aconsejable es usar la función reference y tener referencias implícitas. De nuevo, la razón fundamental es el rendimiento. Las referencias definen dependencias implícitas que se sabe que son necesarias dado que se hace referencia a ellas dentro de la plantilla. Por su presencia, son relevantes, para evitar nuevamente la optimización del rendimiento y el riesgo potencial de que el motor de implementación no evite paralelismos innecesariamente.

Si necesita definir una dependencia entre un recurso y recursos que se crean a través de un bucle copy, puede establecer el elemento dependsOn en el nombre del bucle. Para ver un ejemplo, consulte [Creación de varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md)

## resources

La propiedad resources permite especificar los recursos secundarios que están relacionados con el recurso que se está definiendo. Los recursos secundarios solo se pueden definir en cinco niveles de profundidad. Es importante tener en cuenta que no se crea una dependencia implícita entre un recurso secundario y el recurso primario. Si necesita que el recurso secundario se implemente después del recurso primario, debe declarar explícitamente esa dependencia con la propiedad dependsOn.

Cada recurso primario solo acepta determinados tipos de recursos como recursos secundarios. Los tipos de recursos que se aceptan se especifican en el [esquema de plantilla](https://github.com/Azure/azure-resource-manager-schemas) del recurso principal. El nombre del tipo de recurso secundario incluye el nombre del tipo de recurso primario, por ejemplo, **Microsoft.Web/sites/config** y **Microsoft.Web/sites/extensions** son ambos recursos secundarios de **Microsoft.Web/Sites**.

## función reference

La función reference permite que una expresión derive su valor de otros pares de valor y nombre JSON o de recursos en tiempo de ejecución. Las expresiones de referencia declaran implícitamente que un recurso depende de otro. La propiedad representada por **propertyPath** a continuación es opcional, si no se especifica, la referencia se hace al recurso.

    reference('resourceName').propertyPath

Puede usar este elemento o el elemento dependsOn para especificar las dependencias, pero no es necesario usar ambos para el mismo recurso dependiente. Lo aconsejable es usar la referencia implícita para evitar el riesgo de tener por accidente un elemento dependsOn innecesario que impida que el motor de implementación realice aspectos de la implementación en paralelo.

Para más información, consulte [función reference](../resource-group-template-functions/#reference).

## Pasos siguientes

- Para más información sobre la creación de plantillas del Administrador de recursos de Azure, consulte [Creación de plantillas](resource-group-authoring-templates.md). 
- Para obtener una lista de las funciones disponibles en una plantilla, consulte [Funciones de plantilla](resource-group-template-functions.md).

<!---HONumber=AcomDC_1210_2015-->