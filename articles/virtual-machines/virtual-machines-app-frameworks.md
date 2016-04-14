<properties
   pageTitle="Implementación de marcos de aplicaciones populares mediante plantillas | Microsoft Azure"
   description="Cree marcos de aplicaciones populares con las plantillas del Administrador de recursos de Azure para instalar Active Directory, Docker y mucho más."
   services="virtual-machines"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager" />

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="infrastructure"
   ms.date="02/03/2016"
   ms.author="rasquill"/>

# Creación de marcos de aplicaciones populares mediante el uso de plantillas del Administrador de recursos de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

Las cargas de trabajo suelen necesitar muchos recursos para funcionar acorde al diseño. Las plantillas del Administrador de recursos de Azure hacen que esto sea posible no solo para definir cómo se configuran las aplicaciones, sino también para saber cómo se implementan los recursos para admitir las aplicaciones configuradas. En este artículo se presentan las plantillas más populares de la galería y se proporciona información para usar el Portal de Azure, Azure PowerShell o la CLI de Azure para implementarlas.

## Aplicaciones

En esta tabla se puede encontrar más información sobre los parámetros que se usan en la plantilla, puede inspeccionar la plantilla antes de implementarla o puede implementar la plantilla directamente desde el Portal de Azure.

| Application | Más información | Ver la plantilla | Implementarla ahora |
|:---|:---:|:---:|:---:|
| Active Directory | [Galería](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Factive-directory-new-domain-ha-2-dc%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Apache | [Galería](https://azure.microsoft.com/documentation/templates/apache2-on-ubuntu-vm/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/apache2-on-ubuntu-vm) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapache2-on-ubuntu-vm%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
| Couchbase | [Galería](https://azure.microsoft.com/documentation/templates/couchbase-on-ubuntu/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/couchbase-on-ubuntu) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcouchbase-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| DataStax | [Galería](https://azure.microsoft.com/documentation/templates/datastax-on-ubuntu/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/datastax-on-ubuntu) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fdatastax-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Django | [Galería](https://azure.microsoft.com/documentation/templates/django-app/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/django-app) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fdjango-app%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Docker | [Galería](https://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fdocker-simple-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Elasticsearch | [Galería](https://azure.microsoft.com/documentation/templates/elasticsearch/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Felasticsearch%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Jenkins | [Galería](https://azure.microsoft.com/documentation/templates/jenkins-on-ubuntu/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/jenkins-on-ubuntu) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fjenkins-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Kafka | [Galería](https://azure.microsoft.com/documentation/templates/kafka-ubuntu-multidisks/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/kafka-on-ubuntu) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%kafka-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| LAMP | [Galería](https://azure.microsoft.com/es-ES/documentation/templates/lamp-app/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Flamp-app%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| MongoDB | [Galería](https://azure.microsoft.com/documentation/templates/mongodb-on-ubuntu/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-ubuntu) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fmongodb-on-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Redis | [Galería](https://azure.microsoft.com/documentation/templates/redis-high-availability/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/redis-high-availability) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fredis-high-availability%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| SharePoint | [Galería](https://azure.microsoft.com/documentation/templates/sharepoint-three-vm/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/sharepoint-three-vm) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsharepoint-three-vm%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Spark | [Galería](https://azure.microsoft.com/documentation/templates/spark-ubuntu-multidisks/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/spark-ubuntu-multidisks) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fspark-ubuntu-multidisks%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| Tomcat | [Galería](https://azure.microsoft.com/documentation/templates/openjdk-tomcat-ubuntu-vm/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/openjdk-tomcat-ubuntu-vm) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fopenjdk-tomcat-ubuntu-vm%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| WordPress | [Galería](https://azure.microsoft.com/documentation/templates/wordpress-single-vm-ubuntu/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/wordpress-single-vm-ubuntu) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fwordpress-single-vm-ubuntu%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |
| ZooKeeper | [Galería](https://azure.microsoft.com/documentation/templates/zookeeper-cluster-ubuntu-vm/) | [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/zookeeper-cluster-ubuntu-vm) | <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fzookeeper-cluster-ubuntu-vm%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a> |

Además de estas plantillas, puede buscar a través de las [plantillas de la galería](https://azure.microsoft.com/documentation/templates/).

## Portal de Azure

Es fácil implementar una plantilla con el Portal de Azure; solo tiene que enviarle una dirección URL. Necesita el nombre del archivo de plantilla para implementarla. Puede encontrar el nombre examinando las páginas de la galería de plantillas o mediante una búsqueda en el repositorio de GitHub. Cambie {nombre de la plantilla} en esta dirección URL por el nombre de la plantilla que desea implementar y luego escríbala en el explorador:

    https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F{template name}%2Fazuredeploy.json

Debe aparecer la hoja de implementación personalizada:

![](./media/virtual-machines-workload-template-ad-domain/azure-portal-template.png)

1.	Para el panel **Plantilla**, haga clic en **Guardar**.
2.	Haga clic en **Parámetros**. En el panel **Parámetros**, escriba nuevos valores, seleccione de valores permitidos o acepte los valores predeterminados y, a continuación, haga clic en **Aceptar**.
3.	Si es necesario, haga clic en **Suscripción** y seleccione la suscripción de Azure correcta.
4.	Haga clic en **Grupo de recursos** y seleccione un grupo de recursos existente. Como alternativa, haga clic en **O crear nuevo** para crear uno nuevo para esta implementación.
5.	Si es necesario, haga clic en **Ubicación** y seleccione la ubicación correcta de Azure.
6.	Si es necesario, haga clic en **Condiciones legales** para revisar las condiciones y el contrato para usar la plantilla.
7.	Haga clic en **Crear**.

Dependiendo de la plantilla, es posible que Azure tarde algún tiempo en implementar los recursos.

## Azure PowerShell

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Ejecute estos comandos para crear el grupo de recursos y la implementación después de reemplazar el texto entre corchetes con el nombre del grupo de recursos, la ubicación, el nombre de la implementación y el nombre de plantilla:

	New-AzureRmResourceGroup -Name {resource-group-name} -Location {location}
	New-AzureRmResourceGroupDeployment -Name {deployment-name} -ResourceGroupName {resource-group-name} -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/{template-name}/azuredeploy.json"

Al ejecutar el comando **New-AzureRmResourceGroupDeployment**, se le pedirá que escriba los valores de los parámetros en la plantilla. Dependiendo de la plantilla, es posible que Azure tarde algún tiempo en implementar los recursos.

## CLI de Azure

[Instale la CLI de Azure](../xplat-cli-install.md), inicie sesión y asegúrese de habilitar los comandos del Administrador de recursos. Para obtener información cómo hacerlo, vea [Uso de la CLI de Azure para Mac, Linux y Windows con el Administrador de recursos de Azure](../xplat-cli-azure-resource-manager.md).

Ejecute estos comandos para crear el grupo de recursos y la implementación después de reemplazar el texto entre corchetes con el nombre del grupo de recursos, la ubicación, el nombre de la implementación y el nombre de plantilla:

	azure group create {resource-group-name} {location}
	azure group deployment create --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/{template-name}/azuredeploy.json {resource-group-name} {deployment-name}

Al ejecutar el comando **azure group deployment create**, se le pedirá que escriba los valores de los parámetros en la plantilla. Dependiendo de la plantilla, es posible que Azure tarde algún tiempo en implementar los recursos.

## Pasos siguientes

Descubra todas las plantillas que están a su disposición en [GitHub](https://github.com/Azure/azure-quickstart-templates).

Obtenga más información sobre [Administrador de recursos de Azure](../resource-group-template-deploy.md).

<!---HONumber=AcomDC_0204_2016-->