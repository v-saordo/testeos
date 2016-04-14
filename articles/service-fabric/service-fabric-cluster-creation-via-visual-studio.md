<properties
   pageTitle="Configuración de un clúster de Service Fabric mediante Visual Studio | Microsoft Azure"
   description="Describe cómo configurar un clúster de Service Fabric con una plantilla del Administrador de recursos de Azure (ARM) creada por un proyecto de grupo de recursos de Azure en Visual Studio."
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/12/2016"
   ms.author="karolz@microsoft.com"/>

# Configuración de un clúster de Service Fabric mediante Visual Studio
Este artículo describe cómo configurar un clúster de Service Fabric con Visual Studio y una plantilla del Administrador de recursos de Azure (ARM). Usaremos el proyecto Grupo de recursos de Azure de Visual Studio para crear la plantilla. Una vez creada una plantilla, se puede implementar directamente en Azure desde Visual Studio, pero también puede usarse desde un script o como parte de una utilidad de integración continua (CI).

## Creación de una plantilla de clúster de Service Fabric con un proyecto de grupo de recursos de Azure
Para empezar, abra Visual Studio y cree un proyecto de grupo de recursos de Azure (está disponible en la carpeta **Nube**):

![Cuadro de diálogo Nuevo proyecto con el proyecto del Grupo de recursos de Azure seleccionado][1]

Puede crear una nueva solución de Visual Studio para este proyecto o agregarlo a una solución existente.

>[AZURE.NOTE] Si no ve el proyecto de grupo de recursos de Azure en el nodo Nube, no tiene instalado SDK de Azure. Inicie el Instalador de plataforma web ([instalar desde aquí](http://www.microsoft.com/web/downloads/platform.aspx) si no lo ha hecho todavía) y a continuación busque "SDK de Azure para .NET" e instale la versión que sea compatible con su versión de Visual Studio.

Una vez alcanzado el botón Aceptar, Visual Studio le pedirá que seleccione la plantilla del Administrador de recursos que quiere crear:

![Seleccione el cuadro de diálogo Plantilla de Azure con la plantilla del clúster de Service Fabric seleccionado][2]

Seleccione la plantilla de Clúster de Service Fabric y luego vuelva a presionar el botón Aceptar. Se creará el proyecto y la plantilla del Administrador de recursos.

## Preparación de la plantilla para la implementación
Antes de implementar la plantilla para crear el clúster, debe proporcionar los valores para los parámetros necesarios de la plantilla. Estos valores de parámetro se leen desde el archivo `ServiceFabricCluster.param.dev.json`, que se encuentra en la carpeta `Templates` del proyecto de grupo de recursos. Abra el archivo y proporcione los valores siguientes:

|Nombre de parámetro |Descripción|
|-----------------------  |--------------------------|
|clusterLocation |El nombre de la **región de Azure** donde se ubicará el clúster de Service Fabric. Por ejemplo, "este de EE. UU.".|
|clusterName |El nombre DNS (Sistema de nombres de dominio) del clúster de Service Fabric que la plantilla va a crear. <br /><br /> Por ejemplo, si establece este parámetro para `myBigCluster` y el parámetro `clusterLocation` se establece como "este de EE. UU.", el nombre del clúster será `myBigCluster.eastus.cloudapp.azure.com`.|
|certificateThumbprint |La huella digital del certificado que protegerá al clúster.|
|sourceVaultValue |El *Identificador de recurso* del almacén de claves donde se guarda el certificado que protege al clúster.|
|certificateUrlValue |La dirección URL del certificado de seguridad del clúster.|

La plantilla del Administrador de recursos de Service Fabric de Visual Studio crea un clúster seguro que está protegido por un certificado. Este certificado se identifica mediante los tres últimos parámetros de la plantilla (`certificateThumbprint`, `sourceVaultValue` y `certificateUrlValue`) y tiene que existir en un **Almacén de claves de Azure**. Para obtener más información sobre cómo crear el certificado de seguridad del clúster, consulte el artículo [Protección de un clúster de Service Fabric mediante certificados](service-fabric-cluster-security.md#secure-a-service-fabric-cluster-by-using-certificates).

## Opcional: Incorporación de puertos de aplicación pública
Otro aspecto de la plantilla que quizá quiera cambiar antes de su implementación son los puertos de aplicación pública para el clúster. De forma predeterminada, la plantilla abre sólo dos puertos TCP públicos (80 y 8081); si necesita más para sus aplicaciones, tendrá que modificar la definición del equilibrador de carga de Azure en la plantilla. La definición se almacena en el archivo de plantilla principal (`SecureFabricCluster.json`). Abrir el archivo y busque `loadBalancedAppPort`. Observará que cada puerto está asociado con tres artefactos:

1. Un parámetro de plantilla que define el valor de puerto TCP para el puerto: ```json
	"loadBalancedAppPort1": {
	    "type": "int",
	    "defaultValue": 80
	}
	```

2. Un *sondeo* que define con qué frecuencia y durante cuánto tiempo el equilibrador de carga de Azure intentará usar un nodo específico de Service Fabric antes de conmutar por error a otro. Los sondeos forman parte del recurso de equilibrador de carga. Esta es la definición de sondeo para el primer puerto de la aplicación predeterminada: ```json
	{
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[parameters('loadBalancedAppPort1')]",
            "protocol": "tcp"
        }
    }
	```

3. Una *regla de equilibrio de carga* que enlaza el puerto y el sondeo y habilita el equilibrio de carga en un conjunto de nodos de clúster de Service Fabric: ```json
	{
	    "name": "AppPortLBRule1",
	    "properties": {
	        "backendAddressPool": {
	            "id": "[variables('lbPoolID0')]"
	        },
	        "backendPort": "[parameters('loadBalancedAppPort1')]",
	        "enableFloatingIP": false,
	        "frontendIPConfiguration": {
	            "id": "[variables('lbIPConfig0')]"
	        },
	        "frontendPort": "[parameters('loadBalancedAppPort1')]",
	        "idleTimeoutInMinutes": 5,
	        "probe": {
	            "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
	        },
	        "protocol": "tcp"
	    }
	}
    ``` Si las aplicaciones que piensa implementar en el clúster necesitan más puertos, tendrá que agregarlos mediante la creación de sondeos y reglas de equilibrio de carga adicionales. Para obtener más información sobre cómo trabajar con el equilibrador de carga de Azure a través de plantillas del Administrador de recursos, consulte el artículo [Introducción a la configuración de un equilibrador de carga interno con el Administrador de recursos de Azure](../load-balancer/load-balancer-internal-arm-powershell.md).

## Implementación de la plantilla con Visual Studio.
Una vez que guarde todos los valores de parámetro necesarios en el archivo `ServiceFabricCluster.param.dev.json`, ya está listo para implementar la plantilla y crear el clúster de Service Fabric. Haga clic con el botón derecho en el proyecto de grupo de recursos en el Explorador de soluciones de Visual Studio y elija **Implementar**. Visual Studio mostrará el cuadro de diálogo **Implementar en grupo de recursos**, que le pedirá autenticarse en Azure si es necesario:

![Cuadro de diálogo Implementar en grupo de recursos][3]

El cuadro de diálogo le permite elegir un grupo de recursos existente del Administrador de recursos para el clúster y le proporciona una opción para crear uno nuevo. Generalmente, tiene sentido usar un grupo de recursos independiente para un clúster de Service Fabric.

Una vez que haga clic en el botón Implementar, Visual Studio le pedirá que confirme los valores de parámetro de plantilla. Haga clic en el botón **Guardar**. Hay un parámetro que no tiene un valor almacenado, se trata de la contraseña de la cuenta administrativa para el clúster. Proporcione un valor de contraseña cuando Visual Studio le solicite uno.

>[AZURE.NOTE] Si nunca usó PowerShell para administrar Azure desde el equipo que está empleando ahora, es necesario realizar un pequeño mantenimiento. 1. Habilite los scripts de PowerShell con el comando [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx). Para los equipos de desarrollo es normalmente aceptable una directiva "sin restricciones". 2. Decida si quiere permitir la recopilación de datos de diagnóstico de los comandos de Azure PowerShell y ejecute [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) o [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx) según sea necesario. Si usa Azure PowerShell versión 0.9.8 o anterior, estos comandos se denominan `Enable-AzureDataCollection` y `Discable-AzureDataCollection`, respectivamente. Esto evita solicitudes innecesarias durante la implementación de la plantilla.

Puede supervisar el progreso del proceso de implementación en la ventana Resultados de Visual Studio. Una vez completada la implementación de plantilla, el nuevo clúster está listo para usarse.

Si hay errores, vaya al [Portal de Azure](https://portal.azure.com/) y compruebe las **Notificaciones**. Una implementación de grupo de recursos con errores dejará allí una información de diagnóstico detallada.

>[AZURE.NOTE] Los clústeres de Service Fabric requieren que un cierto número de nodos estén activos en todo momento con el fin de mantener la disponibilidad y conservar el estado (esto se conoce como "mantenimiento del cuórum"). Por lo tanto, normalmente no es seguro apagar todas las máquinas del clúster a menos que antes haya realizado una [copia de seguridad completa del estado](service-fabric-reliable-services-backup-restore.md).

## Pasos siguientes
- [Obtener información acerca de la configuración de un clúster de Service Fabric en el Portal de Azure](service-fabric-cluster-creation-via-portal.md)
- [Obtener información sobre cómo administrar e implementar aplicaciones de Service Fabric con Visual Studio](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png

<!---HONumber=AcomDC_0218_2016-->