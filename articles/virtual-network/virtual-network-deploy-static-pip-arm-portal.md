<properties 
   pageTitle="Implementación de una VM con una dirección IP pública estática a través del Portal de Azure en el Administrador de recursos | Microsoft Azure"
   description="Obtenga más información acerca de la implementación de VM con una dirección IP pública estática a través del Portal de Azure en el Administrador de recursos"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="telmos" />

# Implementar una máquina virtual con una dirección IP pública estática mediante el portal de Azure

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## Creación de una máquina virtual con una IP pública estática 

Para crear una máquina virtual con una dirección IP pública estática en el portal de Azure, siga estos pasos.

1. Desde un explorador, vaya al [Portal de Azure](https://portal.azure.com) y, si fuera necesario, inicie sesión con su cuenta de Azure.
2. En la esquina superior izquierda del portal, haga clic en **Nuevo**>>**Proceso**>**Windows Server 2012 R2 Datacenter**.
3. En la lista **Seleccionar un modelo de implementación**, seleccione **Administrador de recursos** y haga clic en **Crear**.
4. En la hoja **Fundamentos**, escriba la información de la máquina virtual, tal como se muestra a continuación, y haga clic en **Aceptar**.

	![Portal de Azure: conceptos básicos](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)

5. En la hoja **Elegir un tamaño**, haga clic en **A1 estándar**, tal como se muestra a continuación, y haga clic en **Seleccionar**.

	![Portal de Azure: elegir un tamaño](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)

6. En la hoja **Configuración**, haga clic en **Dirección IP pública** luego en la hoja **Crear la dirección IP pública**, en **Asignación**, haga clic en **Estático**, tal como se muestra a continuación. A continuación, haga clic en **Aceptar**.

	![Portal de Azure: crear la dirección IP pública](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)

7. En la hoja **Configuración**, haga clic en **Aceptar**.
8. Revise la hoja **Resumen**, tal como se muestra a continuación, y haga clic en **Aceptar**.

	![Portal de Azure: crear la dirección IP pública](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)

9. Observe el nuevo icono en el panel.

	![Portal de Azure: crear la dirección IP pública](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)

10. Una vez creada la máquina virtual, la hoja **Configuración** se muestra, tal como se indica a continuación.

	![Portal de Azure: crear la dirección IP pública](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

<!---HONumber=AcomDC_0211_2016-->