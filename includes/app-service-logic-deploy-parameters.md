Con el Administrador de recursos de Azure, se definen los parámetros de los valores que desea especificar al implementar la plantilla. La plantilla incluye una sección denominada Parámetros que contiene todos los valores de los parámetros. Debe definir un parámetro para los valores que variarán según el proyecto que vaya a implementar o según el entorno en el que vaya a realizar la implementación. No defina parámetros para valores que vayan a permanecer igual. Cada valor de parámetro se usa en la plantilla para definir los recursos que se implementan.

Al definir parámetros, use el campo **allowedValues** para especificar los valores que un usuario puede proporcionar durante la implementación. Use el campo **defaultValue** para asignar un valor al parámetro, si no se proporciona ningún valor durante la implementación.

Vamos a describir cada parámetro de la plantilla.

### logicAppName

El nombre de la aplicación lógica que se va a crear.

    "logicAppName": {
        "type": "string"
    }

### svcPlanName

El nombre del plan del Servicio de aplicaciones que se va a crear para hospedar la aplicación lógica.
    
    "svcPlanName": {
        "type": "string"
    }

### sku

El nivel de precios de la aplicación lógica.

    "sku": {
        "type": "string",
        "defaultValue": "Standard",
        "allowedValues": [
            "Free",
            "Basic",
            "Standard",
            "Premium"
        ]
    }

La plantilla define los valores permitidos para este parámetro (Gratis, Básico, Estándar o Premium) y asigna un valor predeterminado (Estándar) si no se especifica ningún valor.

### svcPlanSize

El tamaño de instancia de la aplicación.

    "svcPlanSize": {
        "defaultValue": "0",
        "type": "string",
        "allowedValues": [
            "0",
            "1",
            "2"
        ]
    }

La plantilla define los valores que se permiten para este parámetro (0, 1 o 2) y asigna un valor predeterminado (0) si no se especifica ningún valor. Los valores corresponden a pequeño, mediano y grande.

<!---HONumber=Oct15_HO3-->