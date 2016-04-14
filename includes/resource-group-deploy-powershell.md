## Implementación con PowerShell

1. Inicie sesión en su cuenta de Azure.

          Add-AzureAccount

   Después de proporcionar sus credenciales, el comando devuelve información acerca de su cuenta.

          Id                             Type       ...
          --                             ----    
          example@contoso.com            User       ...   

2. Si tiene varias suscripciones, proporcione el identificador de suscripción que desea usar para la implementación. 

          Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Cambie al módulo Administrador de recursos de Azure.

          Switch-AzureMode AzureResourceManager

4. Si no tiene un grupo de recursos existente, cree uno nuevo. Proporcione el nombre del grupo de recursos y la ubicación que necesita para la solución.

        New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"

   Se devuelve un resumen del grupo de recursos nuevo.

        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. Para crear una implementación nueva para el grupo de recursos, ejecute el comando **New-AzureResourceGroupDeployment** y proporcione los parámetros necesarios. Los parámetros incluirán un nombre para la implementación, el nombre del grupo de recursos, la ruta de acceso o dirección URL a la plantilla que creó y cualquier otro parámetro necesario para el escenario. 
   
   Tiene las opciones siguientes para proporcionar valores de parámetro:
   
   - Use parámetros en línea.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

   - Use un objeto de parámetro.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

   - Uso de un archivo de parámetro.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

  Una vez implementado el grupo de recursos, verá un resumen de la implementación.

             DeploymentName    : ExampleDeployment
             ResourceGroupName : ExampleResourceGroup
             ProvisioningState : Succeeded
             Timestamp         : 4/14/2015 7:00:27 PM
             Mode              : Incremental
             ...

6. Para obtener información acerca de los errores de la implementación.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. Para obtener información detallada acerca de los errores de la implementación.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput

<!---HONumber=Oct15_HO3-->