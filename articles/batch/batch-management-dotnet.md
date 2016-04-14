<properties
	pageTitle="Administración de cuentas con la biblioteca .NET de Administración de Lote | Microsoft Azure"
	description="Cree, elimine y modifique cuentas de Lote de Azure en sus aplicaciones con la biblioteca .NET de Administración de Lote."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="01/28/2016"
	ms.author="marsma"/>

# Administración de cuotas y cuentas de Lote de Azure con la biblioteca .NET de Administración de Lote

> [AZURE.SELECTOR]
- [Azure portal](batch-account-create-portal.md)
- [Batch Management .NET](batch-management-dotnet.md)

Puede reducir la sobrecarga de mantenimiento en las aplicaciones de Lote de Azure con la biblioteca [.NET de Administración de Lote][api_mgmt_net] para automatizar la creación, eliminación, administración de claves y detección de cuota de las cuentas de Lote.

- **Cree y elimine cuentas de Lote** en cualquier región. Por ejemplo, si como proveedor de software independiente (ISV) proporciona un servicio a los clientes en el que a cada uno se le asigna una cuenta de Lote independiente para la facturación, puede agregar capacidades de creación y eliminación de la cuenta al portal del cliente.
- **Recupere y regenere claves de la cuenta** mediante programación para cualquiera de sus cuentas de Lote. Lo que es especialmente útil para mantener el cumplimiento con las directivas de seguridad que podrían exigir la sustitución periódico o la expiración de las claves de la cuenta. Cuando se tiene varias cuentas de Lote en distintas regiones de Azure, la automatización de este proceso de sustitución aumentará la eficacia de la solución.
- **Compruebe las cuotas de la cuenta** y elimine las incertidumbres al determina los límites de cada cuenta de Lote. Al comprobar las cuotas de la cuenta antes de iniciar los trabajos, de crear grupos o de agregar los nodos de proceso, puede ajustar proactivamente dónde o cuándo se crean esos recursos de proceso. Puede determinar qué cuentas requieren un aumento de la cuota antes de asignarles recursos adicionales.
- **Combine las características de otros servicios de Azure** para una experiencia completa de administración, para lo que debe sacar provecho a la vez de la biblioteca .NET de Administración de Lote, [Azure Active Directory][aad_about] y [Administrador de recursos de Azure][resman_overview] en la misma aplicación. Mediante el uso de estas características y sus API puede proporcionar una experiencia de autenticación sin fricciones, la capacidad para crear y eliminar grupos de recursos, y las funcionalidades que se han descrito anteriormente para una solución de administración de un extremo a otro.

> [AZURE.NOTE] Aunque este artículo se centra en la administración de sus cuentas de Lote, claves y cuotas mediante programación, puede realizar muchas de estas actividades con el [Portal de Azure][azure_portal]. Consulte [Creación y administración de una cuenta de Lote de Azure en el Portal de Azure](batch-account-create-portal.md) y [Cuotas y límites del servicio de Lote de Azure](batch-quota-limit.md) para obtener más información.

## Creación y eliminación de cuentas de Lote

Como se mencionó anteriormente, una de las principales características de la API de .Administración de Lote es la creación y eliminación de cuentas de Lote en una región de Azure. Para ello, usará [BatchManagementClient.Accounts.CreateAsync][net_create] y [DeleteAsync][net_delete], o sus contrapartidas sincrónicas.

El fragmento de código siguiente crea una cuenta, obtiene la cuenta recién creada del servicio Lote y la elimina. En este y en otros fragmentos de código de este artículo, `batchManagementClient` es una instancia totalmente inicializada de [BatchManagementClient][net_mgmt_client].

```
// Create a new Batch account
await batchManagementClient.Accounts.CreateAsync("MyResourceGroup",
	"mynewaccount",
	new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
BatchAccountGetResponse getResponse = await batchManagementClient.Accounts.GetAsync("MyResourceGroup", "mynewaccount");
AccountResource account = getResponse.Resource;

// Delete the account
await batchManagementClient.Accounts.DeleteAsync("MyResourceGroup", account.Name);
```

> [AZURE.NOTE] Las aplicaciones que usan la biblioteca .NET de Administración de Lote y su clase BatchManagementClient requieren acceso de **administrador de servicio** o **coadministrador** a la suscripción que posee la cuenta de Lote que se va a administrar. Para más información, consulte la sección "[Azure Active Directory](#aad)" a continuación y el código de ejemplo [AccountManagement][acct_mgmt_sample].

## Recuperación y regeneración de claves de cuenta

Obtenga las claves de las cuentas principal y secundaria de cualquier cuenta de Lote de su suscripción con [ListKeysAsync][net_list_keys]. Dichas claves se pueden regenerar con [RegenerateKeyAsync][net_regenerate_keys].

```
// Get and print the primary and secondary keys
BatchAccountListKeyResponse accountKeys = await batchManagementClient.Accounts.ListKeysAsync("MyResourceGroup", "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.PrimaryKey);
Console.WriteLine("Secondary key: {0}", accountKeys.SecondaryKey);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys = await batchManagementClient.Accounts.RegenerateKeyAsync(
	"MyResourceGroup",
	"mybatchaccount",
	new BatchAccountRegenerateKeyParameters() { KeyName = AccountKeyType.Primary });
```

> [AZURE.TIP] Puede crear un flujo de trabajo de conexión simplificada para las aplicaciones de administración. En primer lugar, obtenga una clave de cuenta para la cuenta de Lote que desea administrar con [ListKeysAsync][net_list_keys]. Después, use esta clave al inicializar la clase [BatchSharedKeyCredentials][net_sharedkeycred] de la biblioteca de .NET de Lote, que se usa al inicializar [BatchClient][net_batch_client].

## Comprobación de la suscripción de Azure y cuotas de la cuenta de Lote

Las suscripciones de Azure y los servicios individuales de Azure, como Lote, tienen cuotas predeterminadas que limitan el número de determinadas entidades que contienen. Para conocer las cuotas predeterminadas de las suscripciones de Azure, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](./../azure-subscription-service-limits.md). Para conocer las cuotas predeterminadas del servicio Lote, consulte [Cuotas y límites del servicio de Lote de Azure](batch-quota-limit.md). Mediante el uso de la biblioteca de .NET de Administración de Lote, puede comprobar dichas cuotas en sus aplicaciones. Esto le permite tomar decisiones sobre la asignación antes de agregar cuentas o recursos de proceso como los grupos y nodos de ejecución.

### Comprobación de una suscripción de Azure para las cuotas de cuentas de Lote

Antes de crear una cuenta de Lote en una región, puede comprobar la suscripción de Azure para ver si se puede agregar una cuenta en esa región.

En el siguiente fragmento de código, primero se usa [BatchManagementClient.Accounts.ListAsync][net_mgmt_listaccounts] para obtener una colección de todas las cuentas de Lote que están en una suscripción. Una vez obtenida esta colección, se determina el número de cuentas que están en la región de destino. Posteriormente, se utiliza [BatchManagementClient.Subscriptions][net_mgmt_subscriptions] para obtener la cuota de la cuenta de Lote y determinar el número de cuentas que se pueden crear en dicha región (si se puede crear alguna).

```
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse = await batchManagementClient.Accounts.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}", creds.SubscriptionId, accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

En el fragmento de código anterior, `creds` es una instancia de [TokenCloudCredentials][azure_tokencreds]. Para ver un ejemplo de creación de este objeto, consulte el ejemplo de código [AccountManagement][acct_mgmt_sample] en GitHub.

### Comprobación de una cuenta de Lote para las cuotas de recursos de proceso

Antes de aumentar los recursos de proceso en la solución de Lote, puede realizar las comprobaciones pertinentes para asegurarse de que los recursos que desea asignar no exceden las cuotas de cuenta actualmente activas. En el siguiente fragmento de código, simplemente se imprime la información de la cuota de la cuenta de Lote denominada `mybatchaccount`. Pero en su propia aplicación puede usar dicha información para determinar si la cuenta puede tratar los recursos adicionales que desea crear.

```
// First obtain the Batch account
BatchAccountGetResponse getResponse = await batchManagementClient.Accounts.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [AZURE.IMPORTANT] Aunque existen cuotas predeterminadas para los servicios y las suscripciones de Azure, muchos de estos límites se pueden aumentar mediante una solicitud en el [Portal de Azure][azure_portal]. Por ejemplo, consulte [Cuotas y límites del servicio de Lote de Azure](batch-quota-limit.md) para obtener instrucciones sobre cómo aumentar las cuotas de la cuenta de Lote.

## Biblioteca .NET de Administración de Lote, Azure AD y Administrador de recursos

Si se trabaja con la biblioteca .NET de Administración de Lote, lo habitual es sacar provecho de las funcionalidades tanto de [Azure Active Directory][aad_about] (Azure AD) como de [Administrador de recursos de Azure][resman_overview]. El proyecto de ejemplo que encontrará a continuación emplea Azure Active Directory y el Administrador de recursos para mostrar la API de .NET de Administración de Lote.

### <a name="aad"></a>Azure Active Directory

El propio Azure usa Azure Active Directory (AAD) para la autenticación de sus clientes, administradores de servicios y usuarios de la organización. En el contexto de .NET de Administración de Lote, se utilizará para autenticar un administrador o coadminstrator de suscripciones. Esto permitirá que, después, la biblioteca de administración consulte el servicio Lote y realice las operaciones que se describen en este artículo.

En el proyecto de ejemplo que encontrará a continuación, la [Biblioteca de autenticación de Active Directory ][aad_adal] (ADAL) de Azure se usa para pedir al usuario sus credenciales de Microsoft. Cuando se suministran las credenciales de administrador o coadministrador de servicios, la aplicación puede consultar en Azure una lista de suscripciones (y crear y eliminar tanto los grupos de recursos como las cuentas de Lote).

### Resource Manager

Al crear cuentas de Lote con la biblioteca .NET de Administración de Lote, lo habitual es que se creen en un [grupo de recursos][resman_overview]. El grupo de recursos se puede crear mediante programación, para lo que se usa la clase [ResourceManagementClient][resman_client] de la biblioteca [.NET de Administrador de recursos][resman_api]. O bien se puede agregar una cuenta a un grupo de recursos existente creado previamente en el [Portal de Azure][azure_portal].

## <a name="sample"></a>Proyecto de ejemplo en GitHub

Consulte el proyecto de ejemplo [AccountManagment][acct_mgmt_sample] en GitHub para ver la biblioteca .NET de Administración de Lote en acción. Esta aplicación de consola muestra la creación y uso de [BatchManagementClient][net_mgmt_client] y [ResourceManagementClient][resman_client]. También muestra el uso de la [Biblioteca de autenticación de Active Directory][aad_adal] (ADAL) de Azure, que requiere ambos clientes.

Para ejecutar correctamente la aplicación de ejemplo, primero debe registrarla en Azure AD desde el Portal de Azure. Consulte "Adición de una aplicación" en [Integración de aplicaciones con Azure Active Directory][aad_integrate]. Luego siga los pasos de este artículo para registrar la aplicación de ejemplo dentro del directorio predeterminado de su propia cuenta. Seleccione "Aplicación de cliente nativo" para el tipo de aplicación, y puede especificar cualquier URI válida (como `http://myaccountmanagementsample`) para el "URI de redirección"(no es necesario que sea un punto de conexión real).

Después de agregar la aplicación, delegue el permiso de "Acceso de Administración de servicios de Azure como organización" a la aplicación *API de administración de servicios de Windows Azure* en los ajustes de configuración de la aplicación en el portal:

![Permisos de la aplicación en el Portal de Azure][2]

Una vez que haya agregado la aplicación como se describe anteriormente, actualice `Program.cs` en el proyecto de ejemplo [AccountManagment][acct_mgmt_sample] con el URI de redireccionamiento de la aplicación y el identificador de cliente. Puede encontrar estos valores en la pestaña "Configuración" de la aplicación:

![Configuración de la aplicación en el Portal de Azure][3]

La aplicación de ejemplo [AccountManagment][acct_mgmt_sample] muestra las siguientes operaciones:

1. Adquiera un token de seguridad en Azure AD mediante [ADAL][aad_adal]. Si el usuario no ha iniciado sesión, se le pedirán sus credenciales de Azure.
2. Con el token de seguridad obtenido en Azure AD, cree un [SubscriptionClient][resman_subclient] para consultar en Azure una lista de las suscripciones asociadas con la cuenta. lo que permite al usuario seleccionar una suscripción si se encuentran varias.
3. Cree un nuevo objeto de credenciales asociado con la suscripción seleccionada.
4. Cree [ResourceManagementClient][resman_client] con las nuevas credenciales.
5. Use [ResourceManagementClient][resman_client] para crear un nuevo grupo de recursos.
6. Use [BatchManagementClient][net_mgmt_client] para realizar varias operaciones de cuenta de Lote:
  - Crear una nueva cuenta de Lote en el grupo de recursos recién creado.
  - Obtener la cuenta recién creada desde el servicio Lote.
  - Imprimir las claves de la nueva cuenta.
  - Regenerar una nueva clave principal para la cuenta.
  - Imprimir la información de cuota de la cuenta.
  - Imprimir la información de cuota de la suscripción.
  - Imprimir todas las cuentas de la suscripción.
  - Eliminar la cuenta recién creada.
7. Elimine el grupo de recursos.

Antes de eliminar el grupo de recursos y la cuenta de Lote recién creados, puede examinarlos en el [Portal de Azure][azure_portal]\:

![Portal de Azure que muestra el grupo de recursos y la cuenta de Lote][1] <br /> *Portal de Azure que muestra el nuevo grupo de recursos y la cuenta de Lote*

[aad_about]: ../active-directory/active-directory-whatis.md "¿Qué es Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Escenarios de autenticación para Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integración de aplicaciones con Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: http://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png

<!---HONumber=AcomDC_0204_2016-->