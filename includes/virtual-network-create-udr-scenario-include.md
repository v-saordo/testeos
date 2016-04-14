## Escenario

Para ilustrar mejor cómo crear enrutamientos definidos por el usuario, en este documento se usará el siguiente escenario.

![DESCRIPCIÓN DE LA IMAGEN](./media/virtual-network-create-udr-scenario-include/figure1.png)

En este escenario creará un enrutamiento definido por el usuario para la *subred front-end* y otro enrutamiento definido por el usuario para la *subred back-end* , tal como se describe a continuación:

- **UDR-FrontEnd**. El enrutamiento definido por el usuario front-end se aplicará a la subred *FrontEnd* y contiene una ruta:	
	- **RouteToBackend**. Esta ruta enviará todo el tráfico a la subred de back-end a la máquina virtual **FW1**.
- **UDR-BackEnd**. El UDR de back-end se aplicará a la subred *BackEnd* y contiene una ruta:	
	- **RouteToFrontend**. Esta ruta enviará todo el tráfico de la subred de front-end a la máquina virtual **FW1**.

La combinación de estas rutas garantizará de que todo el tráfico destinado de una subred a otra se enrutará a la máquina virtual **FW1**, que se utiliza como aplicación virtual. También debe activar el reenvío IP para esa VM, para asegurarse de que esta puede recibir el tráfico destinado a otras VM.

<!---HONumber=Oct15_HO3-->