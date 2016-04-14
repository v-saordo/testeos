## Implementación con la CLI de Azure

1. Inicie sesión en su cuenta de Azure.

        azure login

  Después de proporcionar las credenciales, el comando devuelve el resultado del inicio de sesión.

        ...
        info:    login command OK

2. Si tiene varias suscripciones, proporcione el identificador de suscripción que desea usar para la implementación.

        azure account set <YourSubscriptionNameOrId>

3. Cambie al módulo de Administrador de recursos de Azure.

        azure config mode arm

   Recibirá una confirmación del modo nuevo.

        info:     New mode is arm

4. Si no tiene un grupo de recursos existente, cree uno nuevo. Proporcione el nombre del grupo de recursos y la ubicación que necesita para la solución.

        azure group create -n ExampleResourceGroup -l "West US"

   Se devuelve un resumen del grupo de recursos nuevo.

        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Para crear una implementación nueva para el grupo de recursos, ejecute el siguiente comando y proporcione los parámetros necesarios. Los parámetros incluirán un nombre para la implementación, el nombre del grupo de recursos, la ruta de acceso o dirección URL a la plantilla que creó y cualquier otro parámetro necesario para el escenario.

   Tiene las opciones siguientes para proporcionar valores de parámetro:

   - Use parámetros en línea y una plantilla local.

             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - Use parámetros en línea y un vínculo a una plantilla.

             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - Use un archivo de parámetro.

             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

  Una vez implementado el grupo de recursos, verá un resumen de la implementación.

         info:    Executing command group deployment create
         + Initializing template configurations and parameters
         + Creating a deployment
         ...
         info:    group deployment create command OK


6. Para obtener información acerca de la implementación más reciente.

         azure group log show -l ExampleResourceGroup

7. Para obtener información detallada acerca de los errores de la implementación.

         azure group log show -l -v ExampleResourceGroup

<!---HONumber=Oct15_HO3-->