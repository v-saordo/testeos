<properties 
   pageTitle="Creación de grupos de seguridad de red en el modo ARM mediante el Portal de Azure | Microsoft Azure"
   description="Aprenda a crear e implementar grupos de seguridad de red en ARM mediante el Portal de Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
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

# Administración de grupos de seguridad de red mediante el portal de vista previa

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artículo trata sobre el modelo de implementación del Administrador de recursos. También puede [crear grupos de seguridad de red en el modelo de implementación clásica](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

## Implementar la plantilla ARM por medio de un solo clic para implementar

En este momento, no se puede crear un grupo de seguridad de red desde la vista previa. Sin embargo, puede administrar grupos de seguridad de red existentes. Para administrar grupos de seguridad de red, utilice la plantilla de ejemplo disponible en el repositorio público para crear los recursos descritos en el escenario anterior. Implemente [esta plantilla](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG), haga clic en **Implementar en Azure**, reemplace los valores de parámetro predeterminados si es necesario y siga las instrucciones del portal.

## Creación de reglas en un grupo de seguridad de red existente

Para crear reglas en un grupo de seguridad de red existente desde el portal de vista previa, siga estos pasos.

1. Desde un explorador, vaya a http://portal.azure.com y, si es necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **Examinar >** > **Grupos de seguridad de red**.

![Portal de vista previa - Grupos de seguridad de red](./media/virtual-networks-create-nsg-arm-pportal/figure1.png)

3. En la lista de grupos de seguridad de red, haga clic en **NSG-FrontEnd** > **Reglas de seguridad de entrada**

![Portal de vista previa - NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)

4. En la lista de **Reglas de seguridad de entrada**, haga clic en **Agregar**.

![Portal de vista previa - Agregar regla](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)

5. En la hoja **Agregar regla de seguridad de entrada**, cree una regla denominada *web-rule* con prioridad de *200* que permita el acceso a través de *TCP* al puerto *80* y a cualquier máquina virtual desde cualquier origen y luego haga clic en **Aceptar**. Observe que la mayoría de estas configuraciones son ya valores predeterminados.

![Portal de vista previa - Configuración de la regla](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)

6. Después de unos segundos, verá la nueva regla en el grupo de seguridad de red.

![Portal de vista previa - Nueva regla](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)

<!---HONumber=AcomDC_0211_2016-->