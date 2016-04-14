Puede usar un equilibrador de carga para proporcionar alta disponibilidad para las cargas de trabajo en Azure. Un equilibrador de carga de Azure es de tipo Capa 4 (TCP, UDP) que distribuye el tráfico entrante entre las instancias de servicio correctas de los servicios en la nube o las máquinas virtuales definidas en un conjunto de equilibrador de carga.
 
Puede configurar un equilibrador de carga para.

- Equilibrar la carga del tráfico entrante de Internet a las máquinas virtuales (VM). En este escenario hacemos referencia a un equilibrador de carga como un [equilibrador de carga orientado a Internet](../articles/load-balancer/load-balancer-internet-overview.md).
- El tráfico de equilibrio de carga entre máquinas virtuales de una red virtual (VNet), las máquinas virtuales de los servicios en la nube o entre equipos locales y máquinas virtuales en una red virtual entre entornos. En este escenario hacemos referencia a un equilibrador de carga como un [equilibrador de carga interno (ILB)](../articles/load-balancer/load-balancer-internal-overview.md).
- 	Enrutar el tráfico externo a una instancia específica de máquina virtual.

<!---HONumber=AcomDC_0224_2016-->