## Cómo crear una red virtual con un archivo de configuración de red en el portal de Azure

Azure utiliza un archivo xml para definir todas las redes virtuales disponibles para una suscripción. Puede descargar este archivo y editarlo para modificar o eliminar las redes virtuales existentes y crear otras nuevas. En este documento, obtendrá información sobre cómo descargar este archivo, denominado archivo de configuración de red (o netcgf) y editarlo para crear una red virtual nueva. Compruebe el [esquema de configuración de red virtual de Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) para obtener más información sobre el archivo de configuración de red.

Para crear una red virtual mediante un archivo netcfg a través del portal de Azure, siga estos pasos.

1. Desde un explorador, vaya a http://manage.windowsazure.com y, si es necesario, inicie sesión con su cuenta de Azure.
2. Desplácese hacia abajo en la lista de servicios y haga clic en **REDES** tal como se muestra a continuación.

	![Redes virtuales de Azure](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure1.gif)

3. En la parte inferior de la página, haga clic en el botón **EXPORTAR**, tal como se muestra a continuación.

	![Botón Exportar](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure2.png)

4. En la página **Exportar la configuración de red**, seleccione la suscripción desde la cual desea exportar la configuración de red y, a continuación, haga clic en el botón de marca de verificación en la esquina inferior izquierda de la página.
5. Siga las instrucciones del explorador para guardar el archivo **NetworkConfig.xml**. Asegúrese de observar dónde guarda el archivo.
6. Abra el archivo que guardó en el paso 5 anterior con cualquier aplicación de edición de texto o XML y busque el elemento **<VirtualNetworkSites>**. Si ya tiene otras redes creadas, cada una de las redes se mostrará como su propio elemento **<VirtualNetworkSite>**.
7. Para crear la red virtual que se describe en este escenario, agregue el siguiente código XML justo debajo del elemento **<VirtualNetworkSites>**:

		<VirtualNetworkSite name="TestVNet" Location="Central US">
		  <AddressSpace>
		    <AddressPrefix>192.168.0.0/16</AddressPrefix>
		  </AddressSpace>
		  <Subnets>
		    <Subnet name="FrontEnd">
		      <AddressPrefix>192.168.1.0/24</AddressPrefix>
		    </Subnet>
		    <Subnet name="BackEnd">
		      <AddressPrefix>192.168.2.0/24</AddressPrefix>
		    </Subnet>
		  </Subnets>
		</VirtualNetworkSite>

8.  Guarde el archivo de configuración de red.
9.  En el portal de Azure, en la esquina inferior izquierda de la página, haga clic en **NUEVO**, a continuación, haga clic en **SERVICIOS DE RED**, después, haga clic en **RED VIRTUAL**, y, luego haga clic en **IMPORTAR CONFIGURACIÓN** tal como se muestra en la figura siguiente.

	![Importar configuración](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure3.gif)

10.  En la página **Importar el archivo de configuración de red**, haga clic en **EXAMINAR ARCHIVO...**, a continuación, vaya a la carpeta en la que guardó el archivo del paso 8, seleccione el archivo y, a continuación, haga clic en **Abrir**. La página web debe tener una apariencia similar a la de la siguiente figura. En la esquina inferior derecha de la página, haga clic en el botón de flecha para ir al paso siguiente.

	![Página Importar archivo de configuración de red](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure4.png)

11.   En la página **Construcción de la red**, observe la entrada de la nueva red virtual, como se muestra en la siguiente figura.

	![Página Construcción de la red](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure5.png)

12.   Haga clic en el botón de marca de verificación en la esquina inferior derecha de la página para crear la red virtual. Tras unos segundos la red virtual se mostrará en la lista de redes virtuales disponibles, como se muestra en la siguiente figura.

	![Nueva red virtual](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure6.png)

<!---HONumber=Oct15_HO3-->