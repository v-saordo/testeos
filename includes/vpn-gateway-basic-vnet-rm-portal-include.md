Para crear una red virtual basada en el escenario anterior por medio del Portal de Azure, siga estos pasos.

1. Desde un explorador, vaya a http://portal.azure.com y, si es necesario, inicie sesión con su cuenta de Azure.

2. Haga clic en **NUEVO** > **Redes** > **Red virtual** y, luego, haga clic en **Administrador de recursos** de la lista **Seleccionar un modelo de implementación** y, finalmente, haga clic en **Crear**.

3. En la hoja **Crear red virtual**, configure los valores de la red virtual, como se muestra en la figura siguiente.

	![Hoja Crear red virtual](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)

4. Haga clic en **Grupo de recursos** y seleccione un grupo de recursos al que va a agregar la red virtual, o haga clic en **Crear nuevo** para agregar la red virtual a un nuevo grupo de recursos. En la siguiente figura se muestra la configuración de grupo de recursos de un nuevo grupo de recursos denominado **TestRG**. Para obtener más información sobre los grupos de recursos, visite [Información general del Administrador de recursos de Azure](resource-group-overview.md/#resource-groups).

	![Grupos de recursos](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)

5. Si es necesario, cambie la configuración de la **Suscripción** y la **Ubicación** de la red virtual.

6. Haga clic en **Crear** y observe el icono denominado **Creación de red virtual** tal como se muestra en la figura siguiente.

	![Icono de Crear red virtual](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)

7. Espere a que se cree la red virtual y, luego, en la hoja **Red virtual**, haga clic en **Toda la configuración** > **Subredes** > **Agregar**.

8. Especifique la configuración de subred para la subred *BackEnd*, tal como se muestra a continuación y haga clic en **Aceptar**.

	![Configuración de subred](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)

9. Consulte la lista de subredes.

	![Lista de subredes en la red virtual](./media/vpn-gateway-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)

<!---HONumber=AcomDC_0107_2016-->