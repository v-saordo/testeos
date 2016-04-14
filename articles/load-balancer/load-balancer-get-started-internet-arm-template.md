<properties 
   pageTitle="Crear un equilibrador de carga orientado a Internet en el administrador de recursos mediante una plantilla | Microsoft Azure"
   description="Obtenga información acerca de cómo crear un equilibrador de carga orientado a Internet en el Administrador de recursos mediante una plantilla ARM"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="joaoma" />

# Introducción a la creación de un equilibrador de carga orientado a Internet con una plantilla de ARM

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artículo trata sobre el modelo de implementación del Administrador de recursos. También puede [obtener información sobre cómo crear un equilibrador de carga orientado a Internet mediante el modelo de implementación clásica](load-balancer-get-started-internet-classic-portal.md)


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## Implementar la plantilla ARM por medio de un solo clic para implementar

La plantilla de ejemplo disponible en el repositorio público usa un archivo de parámetros que contiene los valores predeterminados utilizados para generar el escenario descrito anteriormente. Para implementar esta plantilla mediante el método de hacer clic para implementar, siga [esta plantilla](http://go.microsoft.com/fwlink/?LinkId=544801), haga clic en **Implementar en Azure**, reemplace los valores de parámetro predeterminados si es necesario y siga las instrucciones del portal.

## Implementar la plantilla ARM mediante PowerShell

Para implementar la plantilla ARM que descargó con PowerShell, siga estos pasos.

1. Si es la primera vez que usa Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](powershell-install-configure.md) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccionar su suscripción.

2. Ejecute el cmdlet **New-AzureRmResourceGroupDeployment** para crear un grupo de recursos mediante esta plantilla.

		New-AzureRmResourceGroupDeployment -Name TestRG -Location uswest `
		    -TemplateFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' `
		    -TemplateParameterFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json'	

## Implementar la plantilla ARM mediante la CLI de Azure

Para implementar la plantilla ARM mediante la CLI de Azure, siga estos pasos.

1. Si nunca ha usado la CLI de Azure, consulte [Instalación y configuración de la CLI de Azure](xplat-cli.md) y siga las instrucciones hasta el punto donde deba seleccionar su cuenta y suscripción de Azure.
2. Ejecute el comando **azure config mode** para cambiar al modo de Administrador de recursos, como se muestra a continuación.

		azure config mode arm

	Este es el resultado esperado del comando anterior:

		info:    New mode is arm

3. Desde el explorador, vaya a ****https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json**, copie el contenido del archivo json y péguelo en un archivo nuevo en el equipo. En este escenario, deberá copiar los valores siguientes en un archivo denominado **c:\\lb\\azuredeploy.parameters.json**.
4. Ejecute el cmdlet **azure group deployment create** para implementar el nuevo equilibrador de carga mediante la plantilla y los archivos de parámetros que ha descargado y modificado anteriormente. En la lista que se muestra en la salida se explican los parámetros utilizados.

		azure group create -n TestRG -l westus -f 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' -e 'c:\lb\azuredeploy.parameters.json'

## Pasos siguientes

[Introducción a la configuración de un equilibrador de carga interno](load-balancer-internal-getstarted.md)

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0218_2016-->