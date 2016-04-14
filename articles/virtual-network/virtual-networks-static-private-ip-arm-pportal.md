<properties 
   pageTitle="Establecimiento de una IP privada estática en el modo ARM con el Portal de Azure | Microsoft Azure"
   description="Descripción de las IP privadas (DIP) y su administración en el modo ARM con el Portal de Azure"
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

# Establecimiento de una dirección IP privada estática en el Portal de Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artículo trata sobre el modelo de implementación del Administrador de recursos. También puedes [Administrar la dirección IP privada estática en el modelo de implementación clásica](virtual-networks-static-private-ip-classic-pportal.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

En los siguientes pasos de ejemplo se presupone que ya se ha creado un entorno simple. Si desea ejecutar los pasos que aparecen en este documento, cree primero el entorno de prueba descrito en [creación de una red virtual](virtual-networks-create-vnet-arm-pportal.md).

## Creación de una VM para probar las direcciones IP privadas estáticas

No se puede establecer una dirección IP privada estática durante la creación de una VM en el modo de implementación del Administrador de recursos mediante el Portal de Azure. En primer lugar debe crear la VM, tehn establece la IP privada como estática.

Para crear una VM denominada *DNS01* en la subred de *front-end* de una red virtual denominada *TestVNet*, siga estos pasos.

1. Desde un explorador, vaya a http://portal.azure.com y, si es necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **NUEVO** > **Proceso** > **Windows Server 2012 R2 Datacenter**. Tenga en cuenta que la lista **Seleccionar un modelo de implementación** ya muestra **Administrador de recursos** y, a continuación, haga clic en **Crear**, como se muestra en la figura siguiente.

	![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-arm-pportal/figure01.png)

3. En la hoja **Básico**, especifique el nombre de la VM que se va a crear (*DNS01* en nuestro escenario), la cuenta de administrador local y la contraseña, como se muestra en la figura siguiente.

	![Hoja Básico](./media/virtual-networks-static-ip-arm-pportal/figure02.png)

4. Asegúrese de que la **Ubicación** seleccionada sea *Centro de EE. UU.* y, a continuación, haga clic en **Seleccionar existente** en **Grupo de recursos** y, a continuación, haga clic de nuevo en **Grupo de recursos** y después haga clic en *TestRG* y en **Aceptar**.

	![Hoja Básico](./media/virtual-networks-static-ip-arm-pportal/figure03.png)

5. En la hoja **Elegir un tamaño**, seleccione **A1 Estándar** y, a continuación, haga clic en **Seleccionar**.

	![Selección de una hoja de tamaño](./media/virtual-networks-static-ip-arm-pportal/figure04.png)

6. En la hoja **Configuración** de teh, asegúrese de que se establecen las siguientes propiedades con los siguientes valores y, a continuación, haga clic en **Aceptar**.

	-**Cuenta de almacenamiento**: *vnetstorage*
	- **Red**: *TestVNet*
	- **Subred**: *front-end*

	![Selección de una hoja de tamaño](./media/virtual-networks-static-ip-arm-pportal/figure05.png)

7. En la hoja **Resumen**, haga clic en **Aceptar**. Observe el icono siguiente que aparece en el panel.

	![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## Recuperación de la información de la dirección IP privada estática para una VM

Para ver la información de la dirección IP privada estática para la VM que creó con los pasos anteriores, ejecute los siguientes pasos.

1. En el Portal de Azure, haga clic en **EXAMINAR TODO** > **Máquinas virtuales** > **DNS01** > **Toda la configuración** > **Interfaces de red** y, a continuación, haga clic en la única interfaz de red que aparece.

	![Implementación del icono de máquina virtual](./media/virtual-networks-static-ip-arm-pportal/figure07.png)

2. En la hoja **Interfaz de red**, haga clic en **Toda la configuración** > **Direcciones IP** y vea los valores **Asignación** y **Dirección IP**.

	![Implementación del icono de máquina virtual](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## Adición de una dirección IP privada estática a una VM existente
Para agregar una dirección IP privada estática a la VM creada con los pasos anteriores, siga los siguientes pasos:

1. En la hoja **Direcciones IP** que se muestra a continuación, haga clic en **Estático** en **Asignación**.
2. Escriba *192.168.1.101* en **Dirección IP** y, a continuación, haga clic en **Guardar**.

	![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

>[AZURE.NOTE] Si después de hacer clic en **Guardar** se da cuenta de que la asignación sigue establecida en **Dinámica**, significa que la dirección IP que escribió sigue estando en uso. Pruebe una dirección IP distinta.

## Eliminación de una dirección IP privada estática de una VM
Para quitar la dirección IP privada estática de la VM creada anteriormente, siga el siguiente paso.
	
1. En la hoja **Direcciones IP** que se muestra a continuación, haga clic en **Dinámico** en **Asignación** y, a continuación, haga clic en **Guardar**.

## Pasos siguientes

- Obtenga más información acerca de las [direcciones IP públicas reservadas](../virtual-networks-reserved-public-ip).
- Obtenga información sobre las [direcciones IP públicas a nivel de instancia (ILPIP)](../virtual-networks-instance-level-public-ip).
- Consulte las [API de REST de IP reservada](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=AcomDC_0211_2016-->