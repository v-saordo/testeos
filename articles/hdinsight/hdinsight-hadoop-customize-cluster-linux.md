<properties
	pageTitle="Personalizar los clústeres de HDInsight mediante acciones de script | Microsoft Azure"
	description="Obtenga información sobre cómo agregar componentes personalizados en clústeres de HDInsight basados en Linux mediante acciones de script. Las acciones de script son scripts de Bash que se ejecutan durante la creación del clúster y pueden usarse para personalizar la configuración del clúster o para agregar servicios adicionales y utilidades como Hue, Solr o R."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="larryfr"/>

# Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)

HDInsight proporciona una opción de configuración denominada **Acción de script** que invoca scripts personalizados, que definen la personalización que se realizará en el clúster en el proceso de aprovisionamiento. Estos scripts pueden utilizarse para instalar software adicional en un clúster o para cambiar la configuración de las aplicaciones de un clúster.

> [AZURE.NOTE] La información de este artículo es específica de los clústeres de HDInsight basados en Linux. Para obtener una versión de este artículo específica para clústeres basados en Windows, vea [Personalización de clústeres de HDInsight mediante la acción de script (Windows)](hdinsight-hadoop-customize-cluster.md)

## Acción de script en el proceso de aprovisionamiento de clústeres

Acción de script sólo se utiliza mientras un clúster está en proceso de creación. El siguiente diagrama ilustra el momento en que acción de script se ejecuta durante el proceso de aprovisionamiento:

![Fases y personalización de clústeres de HDInsight durante la creación de clústeres][img-hdi-cluster-states]

Se ejecuta el script durante la configuración de HDInsight. En esta fase, el script se ejecuta de forma paralela en todos los nodos especificados en el clúster con privilegios raíz en los nodos.

> [AZURE.NOTE] Puesto que dispone de privilegios raíz en los nodos del clúster cuando se ejecuta el script, puede realizar operaciones como la detención o el inicio de servicios, incluidos los servicios de Hadoop. Si detiene los servicios, tiene que asegurarse de que los servicios de Ambari y los demás servicios de Hadoop estén en funcionamiento antes de que finalice la ejecución del script. Estos servicios son necesarios para determinar correctamente el estado del clúster durante la creación.

Cada clúster puede aceptar varias acciones de script, que se invocan en el orden en que se hayan especificado. Un script se puede ejecutar en los nodos principales, los nodos de trabajo, o en ambos.

> [AZURE.IMPORTANT] Las acciones de script se tienen que completar dentro de un periodo de 60 minutos o superarán el tiempo de espera. Durante el aprovisionamiento del nodo, el script se ejecuta a la vez con otros procesos de instalación y de configuración. La competición por los recursos, como el ancho de banda de red o el tiempo de CPU puede ocasionar que el script tarde más en terminar que en el entorno de desarrollo.
> 
> Para minimizar el tiempo necesario para ejecutar el script, evite tareas tales como descargar y compilar aplicaciones desde el origen. En su lugar, compile previamente la aplicación y almacene la versión binaria en el almacenamiento de blobs de Azure para que se pueda descargar rápidamente en el clúster.


## Ejemplo de scripts de acción de script

Los scripts de acción de script pueden usarse desde el Portal de Azure, Azure PowerShell o el SDK para .NET de HDInsight. En este artículo se muestra cómo usar la acción de script desde el portal. Para obtener información sobre cómo usar PowerShell y el SDK de .NET para usar la acción de script, examine los ejemplos enumerados en la tabla siguiente.

HDInsight proporciona varios scripts para instalar los siguientes componentes en clústeres de HDInsight:

Nombre | Script
----- | -----
**Instalación de Hue** | https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh. Consulte [Instalación y uso de Hue en clústeres de HDInsight](hdinsight-hadoop-hue-linux.md)
**Instalar Spark** | https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh. Vea [Instalación y uso de Spark en clústeres de HDInsight](hdinsight-hadoop-spark-install-linux.md).
**Instalar R** | https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh. Consulte [Instalación y uso de R en clústeres de HDInsight](hdinsight-hadoop-r-scripts-linux.md).
**Instalar Solr** | https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Consulte [Instalación y uso de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install-linux.md).
**Instalación de Giraph** | https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Vea [Instalación y uso de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install-linux.md).
| **Carga previa de las bibliotecas de Hive** | https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh. Consulte [Incorporación de bibliotecas de Hive en clústeres de HDInsight](hdinsight-hadoop-add-hive-libraries.md). |

## Use una acción de script desde el Portal de Azure

1. Comience a crear un clúster, tal como se describe en [Creación de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md#portal).

2. En __Configuración opcional__, de la hoja **Acciones de script**, haga clic en **Agregar acción de script** para proporcionar detalles sobre la acción de script, tal como se muestra a continuación:

	![Uso de la acción de script para personalizar un clúster](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png)

	| Propiedad | Valor |
	| -------- | ----- |
	| Nombre | Especifique un nombre para la acción de script. |
	| URI de script | Especifique el URI al script que se ha invocado para personalizar el clúster. |
	| Head/Worker | Especifique los nodos (**Principal** o **Trabajo** o **Zookeeper**) en los que se ejecuta el script de personalización. |
	| Parámetros | Especifique los parámetros, si lo requiere el script. |

	Presione ENTRAR para agregar más de una acción de script para instalar varios componentes en el clúster.

3. Haga clic en **Seleccionar** para guardar la configuración de la acción de script y continúe con la creación del clúster.

## Use una acción de script de las plantillas de Administrador de recursos de Azure

En esta sección, usamos plantillas del Administrador de recursos de Azure (ARM) para crear un clúster de HDInsight y usar una acción de script para instalar componentes personalizados (en este ejemplo, R) en el clúster. En esta sección se proporciona una plantilla ARM de ejemplo para crear un clúster mediante la acción de script.

### Antes de empezar

* Para obtener información acerca de la configuración de una estación de trabajo para que ejecute cmdlets de HDInsight PowerShell, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).
* Para obtener instrucciones sobre cómo crear plantillas ARM, consulte [Creación de plantillas del Administrador de recursos de Azure](../resource-group-authoring-templates.md).
* Si todavía no ha utilizado Azure PowerShell con el Administrador de recursos, consulte [Uso de Azure PowerShell con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md)

### Creación de clústeres mediante la acción de script

1. Copie la plantilla siguiente en una ubicación en el equipo. Esta plantilla instala R en el nodo principal, así como en los nodos de trabajo en el clúster. También puede comprobar si la plantilla JSON es válida. Pegue la plantilla de contenido en [JSONLint](http://jsonlint.com/), herramienta de validación JSON en línea.

			{
		    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
		    "contentVersion": "1.0.0.0",
		    "parameters": {
		        "clusterLocation": {
		            "type": "string",
		            "defaultValue": "West US",
		            "allowedValues": [ "West US" ]
		        },
		        "clusterName": {
		            "type": "string"
		        },
		        "clusterUserName": {
		            "type": "string",
		            "defaultValue": "admin"
		        },
		        "clusterUserPassword": {
		            "type": "securestring"
		        },
		        "sshUserName": {
		            "type": "string",
		            "defaultValue": "username"
		        },
		        "sshPassword": {
		            "type": "securestring"
		        },
		        "clusterStorageAccountName": {
		            "type": "string"
		        },
		        "clusterStorageAccountResourceGroup": {
		            "type": "string"
		        },
		        "clusterStorageType": {
		            "type": "string",
		            "defaultValue": "Standard_LRS",
		            "allowedValues": [
		                "Standard_LRS",
		                "Standard_GRS",
		                "Standard_ZRS"
		            ]
		        },
		        "clusterStorageAccountContainer": {
		            "type": "string"
		        },
		        "clusterHeadNodeCount": {
		            "type": "int",
		            "defaultValue": 1
		        },
		        "clusterWorkerNodeCount": {
		            "type": "int",
		            "defaultValue": 2
		        }
		    },
		    "variables": {
		    },
		    "resources": [
		        {
		            "name": "[parameters('clusterStorageAccountName')]",
		            "type": "Microsoft.Storage/storageAccounts",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-05-01-preview",
		            "dependsOn": [ ],
		            "tags": { },
		            "properties": {
		                "accountType": "[parameters('clusterStorageType')]"
		            }
		        },
		        {
		            "name": "[parameters('clusterName')]",
		            "type": "Microsoft.HDInsight/clusters",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-03-01-preview",
		            "dependsOn": [
		                "[concat('Microsoft.Storage/storageAccounts/', parameters('clusterStorageAccountName'))]"
		            ],
		            "tags": { },
		            "properties": {
		                "clusterVersion": "3.2",
		                "osType": "Linux",
		                "clusterDefinition": {
		                    "kind": "hadoop",
		                    "configurations": {
		                        "gateway": {
		                            "restAuthCredential.isEnabled": true,
		                            "restAuthCredential.username": "[parameters('clusterUserName')]",
		                            "restAuthCredential.password": "[parameters('clusterUserPassword')]"
		                        }
		                    }
		                },
		                "storageProfile": {
		                    "storageaccounts": [
		                        {
		                            "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
		                            "isDefault": true,
		                            "container": "[parameters('clusterStorageAccountContainer')]",
		                            "key": "[listKeys(resourceId(parameters('clusterStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).key1]"
		                        }
		                    ]
		                },
		                "computeProfile": {
		                    "roles": [
		                        {
		                            "name": "headnode",
		                            "targetInstanceCount": "[parameters('clusterHeadNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        },
		                        {
		                            "name": "workernode",
		                            "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        }
		                    ]
		                }
		            }
		        }
		    ],
		    "outputs": {
		        "cluster":{
		            "type" : "object",
		            "value" : "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
		        }
		    }
		}

2. Inicie Azure PowerShell e inicie sesión en su cuenta de Azure. Después de proporcionar sus credenciales, el comando devuelve información acerca de su cuenta.

		Add-AzureRmAccount

		Id                             Type       ...
		--                             ----
		someone@example.com            User       ...

3. Si tiene varias suscripciones, proporcione el identificador de suscripción que desea usar para la implementación.

		Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] Puede usar `Get-AzureRmSubscription` para obtener una lista de todas las suscripciones asociadas a su cuenta, lo que incluye el identificador de suscripción de cada una.

5. Si no tiene un grupo de recursos existente, cree uno nuevo. Proporcione el nombre del grupo de recursos y la ubicación que necesita para la solución. Se devuelve un resumen del grupo de recursos nuevo.

		New-AzureRmResourceGroup -Name myresourcegroup -Location "West US"

		ResourceGroupName : myresourcegroup
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		            		Actions  NotActions
		            		=======  ==========
		            		*
		ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup


6. Para crear otra implementación del grupo de recursos, ejecute el comando **New-AzureRmResourceGroupDeployment** y especifique los parámetros necesarios. Los parámetros incluirán un nombre para la implementación, el nombre del grupo de recursos y la ruta de acceso o dirección URL a la plantilla que creó. Si la plantilla requiere parámetros, tiene que pasar también esos parámetros. En este caso, la acción de script para instalar R en el clúster no requiere ningún parámetro.


		New-AzureRmResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>


	Se le pedirá que proporcione valores para los parámetros definidos en la plantilla.

7. Una vez implementado el grupo de recursos, verá un resumen de la implementación.

		  DeploymentName    : mydeployment
		  ResourceGroupName : myresourcegroup
		  ProvisioningState : Succeeded
		  Timestamp         : 8/17/2015 7:00:27 PM
		  Mode              : Incremental
		  ...

8. Si se produce un error en la implementación, puede usar los cmdlets siguientes para obtener información sobre los errores.

		Get-AzureRmResourceGroupDeployment -ResourceGroupName myresourcegroup -ProvisioningState Failed

## Use una acción de script de PowerShell de Azure

En esta sección, usamos el cmdlet [Add-AzureRMHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) para invocar scripts mediante la acción de script con el fin de personalizar un clúster. Antes de continuar, asegúrese de que ha instalado y configurado PowerShell de Azure. Para información sobre cómo configurar una estación de trabajo para que ejecute cmdlets de HDInsight PowerShell, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

Lleve a cabo los siguiente pasos:

1. Abra la consola de Azure PowerShell y declare las siguientes variables:

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionId = "<SubscriptionId>"		# ID of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
        $resourceGroupName = "<ResourceGroupName>"      # The resource group that the HDInsight cluster will be created in

2. Especifique los valores de configuración (como nodos en el clúster) y el almacenamiento predeterminado que se va a usar.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureRmSubscription -SubscriptionId $subscriptionId
		$config = New-AzureRmHDInsightClusterConfig
		$config.DefaultStorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccountKey=$storageAccountKey

3. Use el cmdlet **Add-AzureRmHDInsightScriptAction** para invocar el script. En el ejemplo siguiente se usa el script para instalar R en el clúster:

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION FOR HEADNODE AND WORKERNODE
		$config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install R"  -NodeType HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install R"  -NodeType WorkerNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh

	El cmdlet **Add-AzureRmHDInsightScriptAction** toma los siguientes parámetros:

	| Parámetro | Definición |
	| --------- | ---------- |
	| Configuración | Objeto de configuración al que se agrega la información de la acción de script. |
	| Nombre | Nombre de la acción de script. |
	| NodeType | Especifica el nodo en el que se ejecuta el script de personalización. Los valores válidos son **HeadNode** (para instalar en el nodo principal), **WorkerNode** (para instalar en todos los nodos de datos) o **ZookeeperNode** (para instalar en el nodo zookeeper). |
	| Parámetros | Parámetros requeridos por el script. |
	| URI | Especifica el URI para el script que se ejecuta. |

4. Establecimiento del usuario admin/HTTPS para el clúster:

        $httpCreds = get-credential
        
    Cuando se le solicite, escriba el nombre 'admin' e indique una contraseña.

5. Establecimiento de las credenciales SSH:

        $sshCreds = get-credential
    
    Cuando se le solicite, escriba el nombre de usuario SSH y la contraseña. Si desea proteger la cuenta SSH con un certificado en lugar de una contraseña, use una contraseña en blanco y establezca `$sshPublicKey` en el contenido de la clave pública del certificado que quiera usar. Por ejemplo:
    
        $sshPublicKey = Get-Content .\path\to\public.key -Raw
    
4. Por último, cree el clúster:
        
        New-AzureRmHDInsightCluster -config $config -clustername $clusterName -DefaultStorageContainer $containerName -Location $location -ResourceGroupName $resourceGroupName -ClusterSizeInNodes $clusterNodes -HttpCredential $httpCreds -SshCredential $sshCreds -OSType Linux
    
    Si usa una clave pública para proteger la cuenta SSH, debe especificar también `-SshPublicKey $sshPublicKey` como parámetro.

El clúster puede tardar varios minutos en crearse.

## Use una acción de script desde .NET SDK de HDInsight

El .NET SDK de HDInsight proporciona bibliotecas de cliente que facilitan el trabajo con HDInsight desde una aplicación .NET. Para ver un ejemplo de código, consulte [Crear clústeres basados en Linux en HDInsight con el SDK de .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).


## Solución de problemas

Puede usar la interfaz de usuario web de Ambari para ver la información registrada por los scripts durante la creación del clúster. Sin embargo, si se produce un error en la creación del clúster debido a un error en el script, los registros también están disponibles en la cuenta de almacenamiento predeterminada asociada al clúster. Esta sección proporciona información sobre cómo recuperar los registros mediante ambas opciones.

### Uso de la interfaz de usuario web de Ambari

1. Abra el explorador y vaya a https://CLUSTERNAME.azurehdinsight.net. Reemplace CLUSTERNAME por el nombre del clúster de HDInsight.

	Cuando se le solicite, escriba el nombre de cuenta de administrador (admin) y la contraseña de administrador para el clúster. Tendrá que volver a escribir las credenciales de administrador en un formulario web.

2. En los vínculos de la parte superior de la página, seleccione la entrada __ops__. Esto mostrará una lista de las operaciones actuales y anteriores realizadas en el clúster a través de Ambari.

	![Barra de interfaz de usuario web de Ambari con ops seleccionado](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Busque las entradas que tienen __run\_customscriptaction__ en la columna __Operaciones__. Estas se crean al ejecutarse las acciones de script.

	![Captura de pantalla de operaciones](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

	Seleccione esta entrada y profundice a través de los vínculos para ver el resultado STDOUT y STDERR que se genera cuando el script se ejecutó en el clúster.

### Acceso a los registros desde la cuenta de almacenamiento predeterminada

Si se produce un error en la creación del clúster debido a un error en la acción de script, los registros de la acción de script todavía pueden tener acceso directamente desde la cuenta de almacenamiento predeterminado asociada al clúster.

* Los registros de almacenamiento están disponibles en `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`. 

	![Captura de pantalla de operaciones](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

	En este caso, los registros se organizan por separado para el nodo principal, el nodo de trabajo y el nodo de Zookeeper. Algunos ejemplos son:
	* **nodo principal** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`
	* **nodo de trabajo** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`
	* **nodo de Zookeeper** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Todos los stdout y stderr del host correspondiente se cargan en la cuenta de almacenamiento. Hay un archivo **output-*.txt** y **errors-*.txt** para cada acción de script. El archivo de output-*.txt contiene información sobre el URI del script que se ejecuta en el host. Por ejemplo:

		'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Es posible crear repetidamente un clúster de la acción de script con el mismo nombre. En tal caso, puede distinguir los registros relevantes según el nombre de la carpeta de fecha. Por ejemplo, la estructura de carpetas para un clúster (mycluster) creado en diferentes fechas será la siguiente:
	* `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04`
	* `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Si crea un clúster de acción de script con el mismo nombre en el mismo día, puede usar el prefijo único para identificar los archivos de registro correspondientes.

* Si crea un clúster al final del día, es posible que los archivos de registro abarquen dos días. En tales casos, verá dos carpetas de fecha diferentes para el mismo clúster.

* La carga de los archivos de registro en el contenedor predeterminado puede tardar hasta 5 minutos, especialmente para grandes clústeres. Por lo tanto, si desea tener acceso a los registros, no debe eliminar inmediatamente el clúster si se produce un error en una acción de script.


## Soporte técnico para el software de código abierto utilizado en clústeres de HDInsight

El servicio Microsoft Azure HDInsight es una plataforma flexible que permite compilar aplicaciones con grandes volúmenes de datos en la nube mediante el ecosistema de tecnologías de código abierto formadas en torno a Hadoop. Microsoft Azure ofrece un nivel general de soporte técnico para las tecnologías de código abierto, tal como se describe en la sección **Ámbito de soporte técnico** del [sitio web de Preguntas más frecuentes de soporte técnico de Azure](https://azure.microsoft.com/support/faq/). Además, el servicio de HDInsight ofrece un nivel adicional de soporte técnico para algunos de los componentes, como se describe a continuación.

Hay dos tipos de componentes de código abierto que están disponibles en el servicio de HDInsight:

- **Componentes integrados**: estos componentes están instalados previamente en clústeres de HDInsight y proporcionan la funcionalidad básica del clúster. Por ejemplo, el administrador de recursos de YARN, el lenguaje de consulta Hive (HiveQL) y la biblioteca Mahout pertenecen a esta categoría. Una lista completa de los componentes del clúster está disponible en [Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight](hdinsight-component-versioning.md).

- **Componentes personalizados**: el usuario del clúster puede instalar o usar en la carga de trabajo cualquier componente que esté disponible en la comunidad o que haya creado personalmente.

> [AZURE.WARNING] Los componentes ofrecidos con HDInsight son totalmente compatibles. Además, el soporte técnico de Microsoft le ayudará a aislar y resolver problemas relacionados con estos componentes.
>
> Los componentes personalizados reciben soporte técnico comercialmente razonable para ayudarle a solucionar el problema. Esto podría resolver el problema o pedirle que forme parte de los canales disponibles para las tecnologías de código abierto donde se encuentra la más amplia experiencia para esa tecnología. Por ejemplo, hay diversos sitios de la comunidad que se pueden usar, como el [foro de MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/es-ES/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Además, los proyectos de Apache tienen sitios del proyecto en [http://apache.org](http://apache.org), por ejemplo, [Hadoop](http://hadoop.apache.org/) y [Spark](http://spark.apache.org/).

El servicio HDInsight proporciona varias maneras de utilizar los componentes personalizados. Independientemente de cómo se use el componente o cómo se instale en el clúster, se aplica el mismo nivel de soporte técnico. A continuación, se muestra una lista de las maneras más comunes que se pueden usar los componentes personalizados en los clústeres de HDInsight:

1. Envío de trabajos: se pueden enviar al clúster trabajos de Hadoop o de otros tipos que ejecuten o usen componentes personalizados.

2. Personalización del clúster: durante la creación del clúster puede especificar una configuración adicional y componentes personalizados que se instalarán en los nodos del clúster.

3. Muestras: para los componentes personalizados más populares, Microsoft y otros pueden proporcionar muestras de cómo estos componentes se pueden utilizar en los clústeres de HDInsight. Estas muestras se proporcionan sin soporte técnico.

## Pasos siguientes

Consulte la siguiente información y ejemplos sobre la creación y uso de scripts para personalizar un clúster:

- [Desarrollo de la acción de script con HDInsight](hdinsight-hadoop-script-actions-linux.md)
- [Instalación y uso de Spark en clústeres de HDInsight](hdinsight-hadoop-spark-install-linux.md)
- [Instalación y uso de R en clústeres de Hadoop de HDInsight](hdinsight-hadoop-r-scripts-linux.md)
- [Instalación y uso de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install-linux.md)
- [Instalación y uso de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install-linux.md)



[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Fases durante la creación del clúster"

<!---HONumber=AcomDC_0224_2016-->