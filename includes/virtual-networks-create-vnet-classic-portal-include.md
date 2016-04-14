## Cómo crear una red virtual en el portal de Azure

Para crear una red virtual basada en el escenario anterior, siga estos pasos.

1. Desde un explorador, vaya a http://manage.windowsazure.com y, si es necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **NUEVO** > **SERVICIOS DE RED** > **RED VIRTUAL** > **CREACIÓN PERSONALIZADA** tal como se muestra en la figura siguiente.

	![Crear red virtual en el portal](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure1.gif)

3. En la página **Detalles de red virtual**, escriba el **NOMBRE** de la red virtual, seleccione su **UBICACIÓN**, y, a continuación, haga clic en la flecha situada en la esquina inferior derecha de la página para avanzar al paso 2. En la siguiente figura se muestra la configuración para este escenario.

	![Página de detalles de la red virtual](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure2.png)

4. En la página **Servidores DNS y conectividad VPN**, especifique el nombre y la dirección IP de hasta 9 servidores DNS. Si no especifica un servidor DNS, la red virtual usará la resolución interna de nomenclatura proporcionada por Azure. En este escenario, no se configurarán servidores DNS.
5. Si desea proporcionar acceso de VPN de punto a sitio a la red virtual, habilite la casilla de verificación **Configurar una VPN de punto a sitio**. Si no configura una VPN de punto a sitio, puede agregarla a la red virtual en cualquier momento después de su creación. En este escenario, no se configurará ninguna VPN de punto a sitio.
6. Si desea proporcionar conectividad VPN de sitio a sitio entre la red virtual y otra red virtual o a la red local, habilite la casilla de verificación **Configurar una VPN de sitio a sitio** y especifique si desea usar **ExpressRoute** o no, y el nombre de la red con la que va a conectarse. Si no configura una VPN de sitio a sitio, puede agregarla a la red virtual en cualquier momento después de su creación. En este escenario, no se configurará ninguna VPN de sitio a sitio.
7. Haga clic en la flecha situada en la esquina inferior derecha de la página para avanzar al paso 3. En la figura siguiente se muestra la configuración para este escenario.

	![Página de servidores DNS y conectividad VPN](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure3.png)

8. En la página **Espacios de direcciones de red virtual**, en **IP INICIAL**, haga clic en *10.0.0.0* para cambiar el espacio de direcciones de red virtual y, a continuación, escriba el espacio de direcciones de inicio que desee utilizar. En este escenario, escriba *192.168.0.0*.
9. En **CIDR (RECUENTO DE DIRECCIONES)** seleccione el número de bits de la máscara de subred. En nuestro escenario, seleccione *16 (65536)*.
10. En **SUBREDES**, haga clic en *Subred 1* y cambie el nombre de la subred si es necesario. En este escenario, cambie el nombre a *FrontEnd*.

	>[AZURE.NOTE]Si hace clic fuera del cuadro de texto del nombre de una subred no podrá volver a editar el nombre de la subred. Para corregirlo, deberá quitar la subred haciendo clic en el botón X situado en la parte derecha, a continuación, agregar una nueva subred, tal como se describe más adelante en el paso 13.

11. En **IP INICIAL** de la primera subred, especifique la dirección IP inicial de la subred. En este escenario, escriba *192.168.1.0*.
12. En **CIDR (RECUENTO DE DIRECCIONES)** seleccione el número de bits de la máscara de subred de la primera subred. En nuestro escenario, seleccione *24 (256)*.
13. Haga clic en **Agregar subred** para agregar una nueva subred, si es necesario. En este escenario, agregue una subred y repita los pasos del 10 al 12 para configurar la red virtual como se muestra en la siguiente figura.

	![Página de espacios de direcciones de la red virtual](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure4.png)

14. Haga clic en el botón de marca de verificación en la esquina inferior derecha de la página para crear la red virtual. Tras unos segundos la red virtual se mostrará en la lista de redes virtuales disponibles, como se muestra en la siguiente figura.

	![Nueva red virtual](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure5.png)

<!---HONumber=Oct15_HO3-->