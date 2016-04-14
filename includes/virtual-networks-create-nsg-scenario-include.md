## Escenario

Para ilustrar mejor cómo crear NSG, en este documento se usará el siguiente escenario.

![Escenario de red virtual](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

En este escenario, creará un NSG para cada subred de la red virtual **TestVNet**, tal como se describe a continuación:

- **NSG-FrontEnd**. El NSG frontend se aplicará a la subred *FrontEnd* y contiene dos reglas:	
	- **rdp-rule**. Esta regla permitirá el tráfico RDP a la subred *FrontEnd*.
	- **web-rule**. Esta regla permitirá el tráfico HTTP a la subred *FrontEnd*.
- **NSG-BackEnd**. El NSG backend se aplicará a la subred *BackEnd* y contiene dos reglas:	
	- **sql-rule**. Esta regla permite el tráfico SQL tan solo desde la subred *FrontEnd*.
	- **web-rule**. Esta regla deniega todo el tráfico ligado a Internet de la subred *BackEnd*.

La combinación de estas reglas crea un escenario similar a DMZ, donde la subred de backend solo puede recibir tráfico entrante para el tráfico SQL de la subred frontend y no tiene acceso a Internet, mientras que la subred frontend puede comunicarse con Internet y recibir solicitudes HTTP entrantes solamente.
 

<!---HONumber=Oct15_HO3-->