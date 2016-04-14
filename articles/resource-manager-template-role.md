<properties
   pageTitle="Plantilla del Administrador de recursos para las asignaciones de roles | Microsoft Azure"
   description="Muestra el esquema del Administrador de recursos para implementar una asignación de rol mediante una plantilla."
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
   ms.date="01/04/2016"
   ms.author="tomfitz"/>

# Asignaciones de roles: esquema de plantilla

Asigna un usuario, grupo o entidad de servicio a un rol en un ámbito especificado.

## Formato de esquema

Para crear una asignación de roles, agregue el siguiente esquema a la sección de recursos de la plantilla.
    
    {
        "type": "Microsoft.Authorization/roleAssignments",
        "apiVersion": "2015-07-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "roleDefinitionId": string,
            "principalId": string,
            "scope": string
        }
    }

## Valores

Las tablas siguientes describen los valores que debe establecer en el esquema.

| Nombre | Tipo | Obligatorio | Valores permitidos | Descripción |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Sí | **Microsoft.Authorization/roleAssignments** | El tipo de recurso que se creará. |
| apiVersion | enum | Sí | **2015-07-01** | La versión de la API que se usará para crear el recurso. |  
| name | cadena | Sí | Identificador único global | Un identificador para la nueva asignación de roles. |
| dependsOn | array | No | Una lista separada por comas de nombres de recursos o identificadores de recursos únicos. | La colección de recursos de la que depende esta asignación de roles. Si asigna un rol que se centra en un recurso y ese recurso se implementa en la misma plantilla, incluya el nombre de ese recurso en este elemento para asegurarse de que el recurso se implementa en primer lugar. | 
| propiedades | objeto | Sí | (se muestra a continuación) | Un objeto que identifica la definición de rol, la entidad y el ámbito. |  

### properties object

| Nombre | Tipo | Obligatorio | Valores permitidos | Descripción |
| ------- | ---- | ---------------- | -------- | ----------- |
| roleDefinitionId | cadena | Sí | **/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}** | El identificador de una definición de rol existente que se usará en la asignación de roles. |
| principalId | cadena | Sí | Identificador único global | El identificador de una entidad existente. Se asigna al identificador dentro del directorio y puede señalar a un usuario, una entidad de servicio o un grupo de seguridad. |
| ámbito | cadena | Sí | Para el grupo de recursos:<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}**<br /><br />Para el recurso:<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** | El ámbito en el que se aplica esta asignación de roles. |


## Cómo usar el recurso de asignación de rol

Una asignación de roles se agrega a la plantilla cuando se necesita agregar un usuario, un grupo o una entidad de servicio a un rol durante la implementación. Las asignaciones de roles se heredan de los niveles de ámbito más elevados; por tanto, si ya ha agregado una entidad a un rol en el nivel de suscripción, no es necesario volver a asignarla para el grupo de recursos o el recurso.

Puede generar un nuevo identificador para **name** con:

    PS C:\> $name = [System.Guid]::NewGuid().toString()

Puede recuperar el identificador único global para la definición de roles con:

    PS C:\> $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

Puede recuperar el identificador de la entidad con uno de los siguientes comandos.

Para un grupo denominado **Auditors**:

    PS C:\> $principal = (Get-AzureRmADGroup -SearchString Auditors).id

Para un usuario denominado **exampleperson**:

    PS C:\> $principal = (Get-AzureRmADUser -SearchString exampleperson).id

Para una entidad de servicio denominada **exampleapp**:

    PS C:\> $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 
 

## Ejemplos

En el ejemplo siguiente se asigna un grupo a un rol para el grupo de recursos.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "roleDefinitionId": {
                "type": "string"
            },
            "roleAssignmentId": {
                "type": "string"
            },
            "principalId": {
                "type": "string"
            }
        },
        "resources": [
            {
              "type": "Microsoft.Authorization/roleAssignments",
              "apiVersion": "2015-07-01",
              "name": "[parameters('roleAssignmentId')]",
              "properties":
              {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]",
                "scope": "[concat(subscription().id, '/resourceGroups/', resourceGroup().name)]"
              }
            }
        ],
        "outputs": {}
    }

## Plantillas de inicio rápido

Las plantillas siguientes muestran cómo usar el recurso de asignación de rol:

- [Assign built-in role to resource group](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-resourcegroup) (Asignar rol integrado al grupo de recursos)
- [Assign built-in role to existing VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-virtualmachine)
- [Assign built-in role to multiple existing VMs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-rbac-builtinrole-multipleVMs)

## Pasos siguientes

- Para obtener más información sobre la estructura de la plantilla, consulte [Crear plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).
- Para obtener más información acerca del control de acceso basado en roles, vea [Control de acceso basado en roles de Azure Active Directory](active-directory/role-based-access-control-configure.md).

<!---HONumber=AcomDC_0107_2016-->