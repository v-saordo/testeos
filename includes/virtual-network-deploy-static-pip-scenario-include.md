## Escenario

Este documento le guiará por una implementación que usa una dirección IP pública estática asignada a una máquina virtual (VM). En este escenario, tiene una sola máquina virtual con su propia dirección IP pública estática. La máquina virtual forma parte de una subred denominada **FrontEnd** y también tiene una dirección IP privada estática (**192.168.1.101**) en esa subred.

Es posible que necesite una dirección IP estática para los servidores web que requieren conexiones SSL en las que el certificado SSL está vinculado a una dirección IP.

![DESCRIPCIÓN DE LA IMAGEN](./media/virtual-network-deploy-static-pip-scenario-include/figure1.png)

Puede seguir los pasos siguientes para implementar el entorno que se muestra en la ilustración anterior.

<!---HONumber=AcomDC_0114_2016-->