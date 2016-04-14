<properties 
   pageTitle="Establecimiento de una IP privada estática en el modo clásico con el Portal de Azure | Microsoft Azure"
   description="Descripción de las IP privadas estáticas y su administración en el modo clásico con el Portal de Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="telmos" />

# Configuración de una dirección IP privada estática (clásica) en el Portal de Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artículo trata sobre el modelo de implementación clásico. También puede [administrar la dirección IP privada estática en el modelo de implementación del Administrador de recursos](virtual-networks-static-private-ip-arm-pportal.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

En los siguientes pasos de ejemplo se presupone que ya se ha creado un entorno simple. Si desea ejecutar los pasos que aparecen en este documento, cree primero el entorno de prueba descrito en [creación de una red virtual](virtual-networks-create-vnet-classic-pportal.md).

## Especificación de una dirección IP privada estática al crear una VM
Para crear una VM denominada *DNS01* en la subred de *front-end* de una red virtual denominada *TestVNet* con una IP privada estática de *192.168.1.101*, siga estos pasos.

1. Desde un explorador, vaya a http://portal.azure.com y, si es necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **NUEVO** > **Proceso** > **Windows Server 2012 R2 Datacenter**. Tenga en cuenta que la lista **Seleccionar un modelo de implementación** ya muestra **Clásico** y, a continuación, haga clic en **Crear**.

	![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-classic-pportal/figure01.png)

3. En la hoja **Crear VM**, especifique el nombre de la VM que se va a crear (*DNS01* en nuestro escenario), la cuenta de administrador local y la contraseña.

	![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-classic-pportal/figure02.png)

4. Haga clic en **Configuración opcional** > **Red** > **Red virtual** y, a continuación, haga clic en **TestVNet**. Si **TestVNet** no está disponible, asegúrese de que usa la ubicación *Centro de EE. UU.* y que ha creado el entorno de prueba descrito al principio de este artículo.

	![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-classic-pportal/figure03.png)

5. En la hoja **Red**, asegúrese de que la subred seleccionada actualmente sea *front-End* y, a continuación, haga clic en **Direcciones IP**, en **Asignación de dirección IP**, en **Estático** y, a continuación, especifique *192.168.1.101* para **Dirección IP** como se muestra a continuación.

	![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-classic-pportal/figure04.png)

6. Haga clic en **Aceptar** en la hoja **Direcciones IP** y, a continuación, haga clic en **Aceptar** en la hoja **Red** y, a continuación, haga clic en **Aceptar** en la hoja **Configuración opcional**.
7. En la hoja **Crear VM**, haga clic en **Crear**. Observe el icono siguiente que aparece en el panel.

	![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## Recuperación de la información de la dirección IP privada estática para una VM

Para ver la información de la dirección IP privada estática para la VM que creó con los pasos anteriores, ejecute los siguientes pasos.

1. En el Portal de Azure, haga clic en **EXAMINAR TODO** > **Máquinas virtuales (clásico)** > **DNS01** > **Toda la configuración** > **Direcciones IP** y observe la asignación de direcciones IP y la dirección IP que se muestran a continuación.

	![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## Eliminación de una dirección IP privada estática de una VM
Para quitar la dirección IP privada estática de la VM creada anteriormente, siga los siguientes pasos.
	
1. En la hoja **Direcciones IP** que aparecen a continuación, haga clic en **Dinámico** a la derecha de **Asignación de la dirección IP** y, a continuación, haga clic en **Guardar** y después en **Sí**.

	![Creación de una VM en el Portal de Azure](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## Adición de una dirección IP privada estática a una VM existente
Para agregar una dirección IP privada estática a la VM creada con los pasos anteriores, siga los siguientes pasos:

1. En la hoja **Direcciones IP** que se muestra a continuación, haga clic en **Estático** a la derecha de **Asignación de dirección IP**.
2. Escriba *192.168.1.101* en **Dirección IP** y, a continuación, haga clic en **Guardar** y en **Sí**.

## Pasos siguientes

- Obtenga más información acerca de las [direcciones IP públicas reservadas](../virtual-networks-reserved-public-ip).
- Obtenga información sobre las [direcciones IP públicas a nivel de instancia (ILPIP)](../virtual-networks-instance-level-public-ip).
- Consulte las [API de REST de IP reservada](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=AcomDC_0211_2016-->