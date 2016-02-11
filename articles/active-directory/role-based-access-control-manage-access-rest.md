<properties
	pageTitle="Administración del control de acceso basado en rol con la API de REST"
	description="Administración del control de acceso basado en rol con la API de REST"
	services="active-directory"
	documentationCenter="na"
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="multiple"
	ms.tgt_pltfrm="rest-api"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/25/2016"
	ms.author="kgremban"/>

# Administración del control de acceso basado en rol con la API de REST

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

## Lista de todas las asignaciones de roles

Proporciona una lista todas las asignaciones de roles en el ámbito y los ámbitos secundarios especificados.

Para obtener una lista de las asignaciones de roles, debe tener acceso a la operación `Microsoft.Authorization/roleAssignments/read` en el ámbito. Se concede acceso a esta operación a todos los roles integrados. Para más información sobre las asignaciones de roles y cómo administrar el acceso a los recursos de Azure, consulte [Control de acceso basado en rol de Azure](role-based-access-control-configure.md).

### Solicitud

Use el método **GET** con el identificador URI siguiente:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&filter={filter}

Dentro del URI, realice las sustituciones siguientes para personalizar la solicitud:

Reemplace *{scope}* por el ámbito para el que desee obtener una lista de asignaciones de roles. En los ejemplos siguientes, se muestra cómo especificar el ámbito para los distintos niveles:

| Level | *{Scope}* |
|-------|-----------|
| La suscripción | /subscriptions/{id. de suscripción} |
| El grupos de recursos | /subscriptions/{id. de suscripción}/resourceGroups/myresourcegroup1 |
| Recurso | /subscriptions/{id. de suscripción}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Reemplace *{api-version}* por 2015-07-01.

Reemplace *{filter}* por la condición que desea aplicar para filtrar la lista de asignaciones de roles. Se admiten las condiciones siguientes.


| Condición | *{Filter}* | Sustituya |
|-----------|------------|---------|
| Para obtener una lista de las asignaciones de roles para únicamente el ámbito especificado, sin incluir las asignaciones de roles en ámbitos secundarios | `atScope()` | |
| Para obtener una lista de las asignaciones de roles para solo un usuario, un grupo o una aplicación determinados | `principalId%20eq%20'{objectId}'` | Reemplace *{objectId}* por el valor objectId de Azure AD del usuario, grupo o entidad de servicio. Por ejemplo, `&filter=principalId%20eq%20'3a477f6a-6739-4b93-84aa-3be3f8c8e7c2'` |
| Para obtener una lista de las asignaciones de roles solo para un usuario específico, incluidos los asignados a grupos de los que el usuario forme parte | `assignedTo('{objectId}')` | Reemplace *{objectId}* por el valor objectId de Azure AD del usuario. Por ejemplo, `&filter=assignedTo('3a477f6a-6739-4b93-84aa-3be3f8c8e7c2')` |



### Response

Código de estado: 200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## Obtención de información sobre una asignación de roles

Obtiene información sobre una única asignación de roles especificada por el identificador de asignación de roles.

Para obtener información sobre una asignación de roles, debe tener acceso a la operación `Microsoft.Authorization/roleAssignments/read`. Se concede acceso a esta operación a todos los roles integrados. Para más información sobre las asignaciones de roles y cómo administrar el acceso a los recursos de Azure, consulte [Control de acceso basado en rol de Azure](role-based-access-control-configure.md).

### Solicitud

Use el método **GET** con el identificador URI siguiente:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Dentro del URI, realice las sustituciones siguientes para personalizar la solicitud:

Reemplace *{scope}* por el ámbito para el que desee obtener una lista de asignaciones de roles. En los ejemplos siguientes, se muestra cómo especificar el ámbito para los distintos niveles:

| Level | *{Scope}* |
|-------|-----------|
| La suscripción | /subscriptions/{id. de suscripción} |
| El grupos de recursos | /subscriptions/{id. de suscripción}/resourceGroups/myresourcegroup1 |
| Recurso | /subscriptions/{id. de suscripción}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Reemplace *{role-assignment-id}* por el identificador GUID de la asignación de roles.

Reemplace *{api-version}* por 2015-07-01.

### Response

Código de estado: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## Creación de una asignación de roles

Cree una asignación de roles en el ámbito especificado para la entidad de seguridad especificada que concede el rol especificado.

Para crear una asignación de roles, debe tener acceso a la operación `Microsoft.Authorization/roleAssignments/write`. Entre los roles integrados, solo se concede acceso a esta operación a *Propietario* y *Administrador de acceso de usuarios*. Para más información sobre las asignaciones de roles y cómo administrar el acceso a los recursos de Azure, consulte [Control de acceso basado en rol de Azure](role-based-access-control-configure.md).

### Solicitud

Use el método **PUT** con el identificador URI siguiente:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Dentro del URI, realice las sustituciones siguientes para personalizar la solicitud:

Reemplace *{scope}* por el ámbito en el que desee crear las asignaciones de roles. Cuando se crea una asignación de roles en un ámbito primario, todos los ámbitos secundarios heredan la misma asignación de roles. En los ejemplos siguientes, se muestra cómo especificar el ámbito para los distintos niveles:

| Level | *{Scope}* |
|-------|-----------|
| La suscripción | /subscriptions/{id. de suscripción} |
| El grupos de recursos | /subscriptions/{id. de suscripción}/resourceGroups/myresourcegroup1 |
| Recurso | /subscriptions/{id. de suscripción}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Reemplace *{role-assignment-id}* por un nuevo GUID. Se usará como identificador GUID de la nueva asignación de roles.

Reemplace *{api-version}* por 2015-07-01.

Para el cuerpo de la solicitud, proporcione los valores en el formato siguiente:

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| Nombre del elemento | Obligatorio | Tipo | Descripción |
|------------------|----------|--------|-------------|
| roleDefinitionId | Sí | String | Identificador del rol que se va a asignar. El formato del identificador es: `{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId | Sí | String | objectId de la entidad de seguridad de Azure AD (usuario, grupo o entidad de servicio) a la que se va a asignar el rol. |

### Response

Código de estado: 201

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## Eliminación de una asignación de roles

Elimine una asignación de roles en el ámbito especificado.

Para eliminar una asignación de roles, debe tener acceso a la operación `Microsoft.Authorization/roleAssignments/delete`. Entre los roles integrados, solo se concede acceso a esta operación a *Propietario* y *Administrador de acceso de usuarios*. Para más información sobre las asignaciones de roles y cómo administrar el acceso a los recursos de Azure, consulte [Control de acceso basado en rol de Azure](role-based-access-control-configure.md).

### Solicitud

Use el método **DELETE** con el identificador URI siguiente:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

Dentro del URI, realice las sustituciones siguientes para personalizar la solicitud:

Reemplace *{scope}* por el ámbito en el que desee crear las asignaciones de roles. En los ejemplos siguientes, se muestra cómo especificar el ámbito para los distintos niveles:

| Level | *{Scope}* |
|-------|-----------|
| La suscripción | /subscriptions/{id. de suscripción} |
| El grupos de recursos | /subscriptions/{id. de suscripción}/resourceGroups/myresourcegroup1 |
| Recurso | /subscriptions/{id. de suscripción}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Reemplace *{role-assignment-id}* por el identificador GUID de la asignación de roles.

Reemplace *{api-version}* por 2015-07-01.

### Response

Código de estado: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## Lista de todos los roles

Proporciona una lista de todos los roles disponibles para asignarse en el ámbito especificado.

Para obtener una lista de roles, debe tener acceso a la operación `Microsoft.Authorization/roleDefinitions/read` en el ámbito. Se concede acceso a esta operación a todos los roles integrados. Para más información sobre las asignaciones de roles y cómo administrar el acceso a los recursos de Azure, consulte [Control de acceso basado en rol de Azure](role-based-access-control-configure.md).

### Solicitud

Use el método **GET** con el identificador URI siguiente:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&filter={filter}

Dentro del URI, realice las sustituciones siguientes para personalizar la solicitud:

Reemplace *{scope}* por el ámbito para el que desee obtener una lista de roles. En los ejemplos siguientes, se muestra cómo especificar el ámbito para los distintos niveles:

| Level | *{Scope}* |
|-------|-----------|
| La suscripción | /subscriptions/{id. de suscripción} |
| El grupos de recursos | /subscriptions/{id. de suscripción}/resourceGroups/myresourcegroup1 |
| Recurso | /subscriptions/{id. de suscripción}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Reemplace *{api-version}* por 2015-07-01.

Reemplace *{filter}* por la condición que desea aplicar para filtrar la lista de roles. Se admiten las condiciones siguientes.

| Condición | *{Filter}* | Sustituya |
|-----------|------------|---------|
| Para obtener una lista de los roles disponibles para asignarse en el ámbito especificado y cualquiera de sus ámbitos secundarios | `atScopeAndBelow()` | |
| Para buscar un rol con un nombre para mostrar exacto | `roleName%20eq%20'{role-display-name}'` | Reemplace *{rol-display-name}* por la forma con codificación URL del nombre para mostrar exacto del rol. Por ejemplo, `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |



### Response

Código de estado: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## Obtención de información sobre un rol

Obtiene información sobre un único rol especificado por el identificador de la definición de roles. Para obtener información sobre un solo rol utilizando su nombre para mostrar, consulte Lista de todos los roles y el filtro roleName.

Para obtener información sobre un rol, debe tener acceso a la operación `Microsoft.Authorization/roleDefinitions/read`. Se concede acceso a esta operación a todos los roles integrados. Para más información sobre las asignaciones de roles y cómo administrar el acceso a los recursos de Azure, consulte [Control de acceso basado en rol de Azure](role-based-access-control-configure.md).

### Solicitud

Use el método **GET** con el identificador URI siguiente:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dentro del URI, realice las sustituciones siguientes para personalizar la solicitud:

Reemplace *{scope}* por el ámbito para el que desee obtener una lista de asignaciones de roles. En los ejemplos siguientes, se muestra cómo especificar el ámbito para los distintos niveles:

| Level | *{Scope}* |
|-------|-----------|
| La suscripción | /subscriptions/{id. de suscripción} |
| El grupos de recursos | /subscriptions/{id. de suscripción}/resourceGroups/myresourcegroup1 |
| Recurso | /subscriptions/{id. de suscripción}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Reemplace *{role-definition-id}* por el identificador GUID de la definición de roles. Reemplace *{api-version}* por 2015-07-01.

### Response

Código de estado: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## Creación de un rol personalizado
Cree un rol personalizado.

Para crear un rol personalizado, debe tener acceso a la operación `Microsoft.Authorization/roleDefinitions/write` en todos sus `AssignableScopes`. Entre los roles integrados, solo se concede acceso a esta operación a *Propietario* y *Administrador de acceso de usuarios*. Para más información sobre las asignaciones de roles y cómo administrar el acceso a los recursos de Azure, consulte [Control de acceso basado en rol de Azure](role-based-access-control-configure.md).

### Solicitud

Use el método **PUT** con el identificador URI siguiente:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dentro del URI, realice las sustituciones siguientes para personalizar la solicitud:

Reemplace *{scope}* por el primer elemento *AssignableScope* del rol personalizado. En los ejemplos siguientes, se muestra cómo especificar el ámbito para los distintos niveles.

| Level | *{Scope}* |
|-------|-----------|
| La suscripción | /subscriptions/{id. de suscripción} |
| El grupos de recursos | /subscriptions/{id. de suscripción}/resourceGroups/myresourcegroup1 |
| Recurso | /subscriptions/{id. de suscripción}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Reemplace *{role-definition-id}* por un nuevo GUID. Se usará como identificador GUID del nuevo rol personalizado.

Reemplace *{api-version}* por 2015-07-01.

Para el cuerpo de la solicitud, proporcione los valores en el formato siguiente:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nombre del elemento | Obligatorio | Tipo | Descripción |
|--------------|----------|------|-------------|
| name | Sí | String | Identificador GUID del rol personalizado. |
| properties.roleName | Sí | String | Nombre para mostrar del rol personalizado. Tamaño máximo: 128 caracteres. |
| properties.description | No | String | Descripción del rol personalizado. Tamaño máximo: 1024 caracteres. |
| properties.type | Sí | String | Establézcalo en "CustomRole". |
| properties.permissions.actions | Sí | String | Matriz de cadenas de acción que especifica las operaciones a las que el rol personalizado concede acceso. |
| properties.permissions.notActions | No | String | Matriz de cadenas de acción que especifica las operaciones que se excluirán de aquellas a las que el rol personalizado concede acceso. |
| properties.assignableScopes | Sí | String | Matriz de ámbitos en los que se puede usar el rol personalizado para administrar el acceso. |

### Response

Código de estado: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## Actualización de un rol personalizado

Modifique un rol personalizado.

Para modificar un rol personalizado, debe tener acceso a la operación `Microsoft.Authorization/roleDefinitions/write` en todos sus `AssignableScopes`. Entre los roles integrados, solo se concede acceso a esta operación a *Propietario* y *Administrador de acceso de usuarios*. Para más información sobre las asignaciones de roles y cómo administrar el acceso a los recursos de Azure, consulte [Control de acceso basado en rol de Azure](role-based-access-control-configure.md).

### Solicitud

Use el método **PUT** con el identificador URI siguiente:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dentro del URI, realice las sustituciones siguientes para personalizar la solicitud:

Reemplace *{scope}* por el primer elemento *AssignableScope* del rol personalizado. En los ejemplos siguientes, se muestra cómo especificar el ámbito para los distintos niveles:

| Level | *{Scope}* |
|-------|-----------|
| La suscripción | /subscriptions/{id. de suscripción} |
| El grupos de recursos | /subscriptions/{id. de suscripción}/resourceGroups/myresourcegroup1 |
| Recurso | /subscriptions/{id. de suscripción}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Reemplace *{role-definition-id}* por el identificador GUID del rol personalizado que se va a actualizar.

Reemplace *{api-version}* por 2015-07-01.

Para el cuerpo de la solicitud, proporcione los valores en el formato siguiente:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Nombre del elemento | Obligatorio | Tipo | Descripción |
|--------------|----------|------|-------------|
| name | Sí | String | Identificador GUID del rol personalizado que se va a actualizar. |
| properties.roleName | Sí | String | Nombre para mostrar del rol personalizado actualizado. |
| properties.description | No | String | Descripción del rol personalizado actualizado. |
| properties.type | Sí | String | Establézcalo en "CustomRole". |
| properties.permissions.actions | Sí | String | Matriz de cadenas de acción que especifica las operaciones a las que el rol personalizado actualizado concede acceso. |
| properties.permissions.notActions | No | String | Matriz de cadenas de acción que especifica las operaciones que se excluirán de aquellas a las que el rol personalizado actualizado concede acceso. |
| properties.assignableScopes | Sí | String | Matriz de ámbitos en los que se puede usar el rol personalizado actualizado para administrar el acceso. |

### Response

Código de estado: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## Eliminación de un rol personalizado

Elimine un rol personalizado.

Para eliminar un rol personalizado, debe tener acceso a la operación `Microsoft.Authorization/roleDefinitions/delete` en todos sus `AssignableScopes`. Entre los roles integrados, solo se concede acceso a esta operación a *Propietario* y *Administrador de acceso de usuarios*. Para más información sobre las asignaciones de roles y cómo administrar el acceso a los recursos de Azure, consulte [Control de acceso basado en rol de Azure](role-based-access-control-configure.md).

### Solicitud

Use el método **DELETE** con el identificador URI siguiente:

	https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

Dentro del URI, realice las sustituciones siguientes para personalizar la solicitud:

Reemplace *{scope}* por el ámbito en el que desee eliminar la definición de roles. En los ejemplos siguientes, se muestra cómo especificar el ámbito para los distintos niveles:

| Level | *{Scope}* |
|-------|-----------|
| La suscripción | /subscriptions/{id. de suscripción} |
| El grupos de recursos | /subscriptions/{id. de suscripción}/resourceGroups/myresourcegroup1 |
| Recurso | /subscriptions/{id. de suscripción}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

Reemplace *{role-definition-id}* por el identificador GUID de definición de roles del rol personalizado que se va a eliminar.

Reemplace *{api-version}* por 2015-07-01.

### Response

Código de estado: 200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```

<!---HONumber=AcomDC_0128_2016-->