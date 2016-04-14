
El Equilibrio de carga interno (ILB) de Azure proporciona un equilibrio de carga de red entre las máquinas virtuales que residen dentro de un servicio en la nube, una red virtual con un ámbito regional. Un equilibrador de carga interno actúa como límite de seguridad, no permitiendo el acceso directo a las máquinas virtuales detrás de este tipo de equilibrador de carga desde Internet.

Para obtener información sobre el uso y la configuración de redes virtuales con un ámbito regional, consulte [Redes virtuales regionales](../articles/virtual-networks-migrate-to-regional-vnet.md). Las redes virtuales existentes que se han configurado para un grupo de afinidad no pueden usar ILB.

<!---HONumber=AcomDC_0224_2016-->