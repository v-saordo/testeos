## Descargar y comprender la plantilla ARM

Puede descargar la plantilla ARM existente para crear una red virtual y dos subredes desde github, puede realizar los cambios que desee y volver a utilizarla. Para ello, siga estos pasos.

1. Navegue a [la página de la plantilla de ejemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Haga clic en **azuredeploy.json** y luego en **RAW**.
3. Guarde el archivo en un una carpeta local en su equipo.
4. Si está familiarizado con las plantillas ARM, salte al paso 7.
5. Abra el archivo que acaba de guardar y vea el contenido de la línea 5 en **parameters**. Los parámetros de plantilla ARM proporcionan un marcador de posición para los valores que se pueden rellenar durante la implementación.

	| Parámetro | Descripción |
	|---|---|
	| **ubicación** | Región de Azure donde se creará la red virtual |
	| **vnetName** | Nombre de la red virtual nueva |
	| **addressPrefix** | Espacio de direcciones de la red virtual, en formato CIDR |
	| **subnet1Name** | Nombre de la primera red virtual |
	| **subnet1Prefix** | Bloque CIDR de la primera subred |
	| **subnet2Name** | Nombre de la segunda red virtual |
	| **subnet2Prefix** | Bloque CIDR de la segunda subred |

	>[AZURE.IMPORTANT] Las plantillas ARM que se mantienen en github pueden cambiar con el tiempo. Asegúrese de comprobar la plantilla antes de usarla.
	
6. Compruebe el contenido en **resources** y observe lo siguiente:

	- **type**. Tipo de recurso que creó la plantilla. En este caso, **Microsoft.Network/virtualNetworks**, que representan una red virtual.
	- **nombre**. Nombre del recurso. Observe el uso de **[parameters('vnetName')]**, lo que significa que será el usuario quien proporcione el nombre como entrada o como archivo de parámetros durante la implementación.
	- **propiedades**. Lista de propiedades para el recurso. Esta plantilla usa las propiedades de espacio de direcciones y subred durante la creación de la red virtual.

7. Vuelva a [la página de la plantilla de ejemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Haga clic en **azuredeploy-paremeters.json**, y, a continuación, haga clic en **RAW**.
9. Guarde el archivo en un una carpeta local en su equipo.
10. Abra el archivo que acaba de guardar y edite los valores de los parámetros. Utilice los siguientes valores para implementar la red virtual que se describe en este escenario.

		{
		  "location": {
		    "value": "Central US"
		  },
		  "vnetName": {
		      "value": "TestVNet"
		  },
		  "addressPrefix": {
		      "value": "192.168.0.0/16"
		  },
		  "subnet1Name": {
		      "value": "FrontEnd"
		  },
		  "subnet1Prefix": {
		    "value": "192.168.1.0/24"
		  },
		  "subnet2Name": {
		      "value": "BackEnd"
		  },
		  "subnet2Prefix": {
		      "value": "192.168.2.0/24"
		  }
		}

11. Guarde el archivo .
  

<!---HONumber=AcomDC_0211_2016-->