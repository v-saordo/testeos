<properties
   pageTitle="Implementación de un clúster del servicio Contenedor de Azure | Microsoft Azure"
   description="Implementación de un clúster del servicio Contenedor de Azure mediante el Portal de Azure, CLI de Azure o PowerShell."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contenedores, microservicios, Mesos, Azure"/>
   
<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>
   
# Implementación de un clúster del servicio Contenedor de Azure

El servicio Contenedor de Azure proporciona una rápida implementación de agrupación en clústeres de contenedor de código abierto y soluciones de orquestación populares. Con el uso del servicio Contenedor de Azure, se pueden implementar clústeres de Marathon Mesos y Docker Swarm con plantillas del Administrador de recursos de Azure o con el portal. Estos clústeres se implementan mediante conjuntos de escalado de máquinas virtuales de Azure, y aprovechan las posibilidades de redes y almacenamiento de Azure. Para obtener acceso al servicio Contenedor de Azure necesita una suscripción de Azure. Si no cuenta con una, suscríbase a una [evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935) hoy mismo.

Este documento analiza la implementación de un clúster del servicio Contenedor de Azure usando [Portal de Azure](#creating-a-service-using-the-azure-portal), [CLI de Azure](#creating-a-service-using-the-azure-cli), y [el módulo de Azure PowerShell](#creating-a-service-using-powershell).
   
## Creación de un servicio mediante el Portal de Azure
 
Seleccione una de las siguientes plantillas para implementar un clúster de Mesos o Docker Swarm. **Nota**: Ambas plantillas son iguales, a excepción de la selección predeterminada del orquestador.
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
 
Cada página de la plantilla tiene un botón 'implementar en Azure', al hacer clic en este botón, se iniciará el Portal de Azure con un formulario de aspecto similar al siguiente. <br />

![Creación de una implementación](media/create-mesos-params.png) <br />

Complete el formulario utilizando estas instrucciones y haga clic en Aceptar cuando haya terminado. <br />

Campo | Descripción
----------------|-----------
DNSNAMEPREFIX | Debe ser un valor único a nivel universal. Se utilizará para crear nombres DNS para cada una de las partes claves del servicio. Encontrará más información a continuación.
AGENTCOUNT | Este es el número de máquinas virtuales que se creará en el conjunto de escalado del agente de ACS.
AGENTVMSIZE | Especifica el tamaño de las máquinas virtuales del agente. Tenga cuidado y seleccione un tamaño que proporcione suficientes recursos para alojar los contenedores más grandes.
ADMINUSERNAME | Este es el nombre de usuario que se usará para una cuenta en cada una de las máquinas virtuales y los conjuntos de escalado de máquinas virtuales en el clúster de ACS.
ORCHESTRATORTYPE| Seleccione el orquestador que desea usar en el clúster de ACS.
MASTERCOUNT | Este es el número de máquinas virtuales para configurar como patrones para el clúster. Puede seleccionar 1, pero esto no proporcionará ningún resistencia en el clúster y solo se recomienda para la realización de pruebas. El número recomendado para un clúster de producción sería 3 o 5. 
SSHRSAPUBLICKEY | Es necesario utilizar SSH para la autenticación con las máquinas virtuales. Es aquí donde agrega la clave pública. Es muy importante que tenga cuidado al pegar el valor de la clave en este cuadro. Algunos editores insertarán saltos de línea en el contenido, esto partirá la clave. Compruebe que la clave no tiene saltos de línea y que incluye el prefijo 'ssh-rsa' y el sufijo 'username@domain'. Debería ser algo similar a ' ssh rsa AAAAB3Nz... SNIPPEDCONTENT... UcyupgH azureuser@linuxvm'. Si necesita crear una clave SSH puede encontrar instrucciones para [Windows](../virtual-machines/virtual-machines-windows-use-ssh-key.md) y [Linux](../virtual-machines/virtual-machines-linux-use-ssh-key.md) en el sitio de documentación de Azure.
  
Una vez que haya establecido los valores adecuados para los parámetros, haga clic en Aceptar. A continuación, proporcione un nombre de grupo de recursos, seleccione una región y revise y acepte los términos legales.

> Durante la versión preliminar, no hay ningún cargo por el servicio Contenedor de Azure, solo se aplicarán cargos por proceso estándar como máquinas virtuales, almacenamiento, redes, etc.
 
![Selección de grupo de recursos](media/resourcegroup.png)
 
Por último, haga clic en "Crear". Volverá al panel, y suponiendo que no haya desactivado "Anclar al panel" en la hoja de implementación, verá un icono animado que tendrá un aspecto similar al siguiente:

![implementación](media/deploy.png)
 
Ahora relájese mientras se crea el clúster. Una vez completado, verá algunas hojas que muestran los recursos que componen el clúster de ACS.
 
![Terminado](media/final.png)

## Creación de un servicio con CLI de Azure

Para crear una instancia del servicio Contenedor de Azure mediante la línea de comandos, necesita una suscripción de Azure. Si no tiene una, puede registrarse para obtener una evaluación gratuita. También tiene que haber instalado y configurado CLI de Azure.
 
Seleccione una de las siguientes plantillas para implementar un clúster de Mesos o Docker Swarm. **Nota**: Ambas plantillas son iguales, a excepción de la selección predeterminada del orquestador.
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
 
A continuación, asegúrese de que CLI de Azure se ha conectado a una suscripción de Azure. Esto puede realizarse mediante el comando a continuación.

```bash
Azure account show
```
Si no se devuelve una cuenta de Azure, utilice lo siguiente para iniciar la sesión de CLI en Azure.
 
```bash
azure login -u user@domain.com
```

A continuación, asegúrese de configurar las herramientas de CLI de Azure para usar el Administrador de recursos de Azure.
 
```bash
azure config mode arm
```
 
Si desea crear el clúster en un nuevo grupo de recursos, primero tiene que crear el grupo de recursos. Utilice este comando, donde `GROUP_NAME` es el nombre del grupo de recursos que desea crear, y `REGION` es la región en la que desea crear el grupo de recursos.
 
```bash
azure group create GROUP_NAME REGION
```
 
Una vez que tenga un grupo de recursos puede crear el clúster con este comando, donde:

- **RESOURCE\_GROUP**: es el nombre del grupo de recursos que quiere usar para este servicio.
- **DEPLOYMENT\_NAME** es el nombre de esta implementación.
- **TEMPLATE\_URI** es la ubicación del archivo de implementación. **Nota**: Este tiene que ser el archivo RAW, no un puntero a la interfaz de usuario de GitHub. Para encontrar esta dirección URL seleccione el archivo azuredeploy.json en GitHub y haga clic en el botón RAW:

> Nota: Al ejecutar este comando, el shell solicitará los valores de parámetro de implementación.
 
```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI
```
 
### Suministro de los parámetros de plantilla
 
Esta versión del comando requiere que el usuario defina los parámetros de forma interactiva. Si desea proporcionar parámetros como una cadena con el formato json, puede hacerlo con el conmutador `-p`. Por ejemplo:
 
 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
 ```

También puede proporcionar un archivo de parámetros con el formato json usando el conmutador `-e`:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON'
 ```
 
Puede encontrar un archivo de parámetros de ejemplo denominado `azuredeploy.parameters.json` con las plantillas de ACS en GitHub.
 
## Creación de un servicio con PowerShell

También se puede implementar un clúster de ACS con PowerShell. Este documento se basa en la versión 1.0 y posterior del [módulo de Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/).

Seleccione una de las siguientes plantillas para implementar un clúster de Mesos o Docker Swarm. **Nota**: Ambas plantillas son iguales, a excepción de la selección predeterminada del orquestador.
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Antes de crear un clúster en su suscripción de Azure, compruebe que la sesión de PowerShell se ha iniciado en Azure. Esto se puede completar con el comando `Get-AzureRMSubscription`.

```powershell
Get-AzureRmSubscription
```

Si tiene que iniciar sesión en Azure, use el comando `Login-AzureRMAccount`.

```powershell
Login-AzureRmAccount
```
 
Si desea realizar la implementación en un nuevo grupo de recursos, primero tiene que crear el grupo de recursos. Para crear un nuevo grupo de recursos, utilice el comando `New-AzureRmResourceGroup`, especificando un nombre y una región de destino para el grupo de recursos.

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

Una vez que tenga un grupo de recursos puede crear el clúster con el comando a continuación. El URI de la plantilla que desee se especificará para el parámetro `-TemplateUri`. Al ejecutar este comando, PowerShell solicitará los valores de parámetro de implementación.

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
 ```
 
### Suministro de los parámetros de plantilla
 
Si está familiarizado con PowerShell, sabe que puede recorrer los parámetros disponibles para un cmdlet; basta con que escriba un signo menos (-) y luego presione la tecla TAB. Esta misma funcionalidad también sirve para los parámetros que se definen en la plantilla. En cuanto escribe el nombre de la plantilla, el cmdlet captura la plantilla, analiza los parámetros y agrega los parámetros de la plantilla al comando de manera dinámica. De esta forma, resulta muy fácil especificar los valores de los parámetros de la plantilla. Además, si olvida el valor de algún parámetro necesario, PowerShell le pide dicho valor.
 
A continuación se muestra el comando completo con parámetros incluidos. Puede proporcionar sus propios valores para los nombres de los recursos.

```
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE\_GROUP\_NAME-TemplateURI TEMPLATE\_URI -adminuser value1 -adminpassword value2 ....
```
 
## Pasos siguientes
 
Ahora que tiene un clúster funcionando, consulte los siguientes documentos para obtener información detallada sobre conexión y administración.
 
- [Conexión a un clúster del servicio Contenedor de Azure](./container-service-connect.md)
- [Administración de contenedores con la API de REST](./container-service-mesos-marathon-rest.md)

 

<!----HONumber=AcomDC_0224_2016--->