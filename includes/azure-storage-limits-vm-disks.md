Una máquina virtual de Azure admite la conexión de varios discos de datos. Si desea obtener un rendimiento óptimo, conviene que limite el número de discos muy usados que se conectan a la máquina virtual para evitar una posible limitación de solicitudes. Si no todos los discos se usan mucho al mismo tiempo, la cuenta de almacenamiento admite un mayor número de discos.

- **En el caso de cuentas de almacenamiento estándar:** una cuenta de almacenamiento estándar tiene una tasa total máxima de solicitudes de 20 000 E/S por segundo. El número total de E/S por segundo en todos los discos de máquina virtual de una cuenta de almacenamiento estándar no debe superar este límite.

	Puede calcular aproximadamente el número de discos muy usados que admite una sola cuenta de almacenamiento estándar en función del límite de tasa de solicitudes. Por ejemplo, en el caso de una máquina virtual de nivel básico, el número máximo de discos muy usados está alrededor de 66 (20 000/300 E/S por segundo por disco) y, en el caso de una máquina virtual de nivel estándar, aproximadamente de 40 (20 000/500 E/S por segundo por disco), tal y como se muestra en la tabla siguiente.
 
- **En el caso de cuentas de almacenamiento premium:** una cuenta de almacenamiento premium tiene una capacidad total máxima de proceso de 50 Gbps. La capacidad total de proceso en todos los discos de la máquina virtual no debe superar este límite.

<!---HONumber=AcomDC_1125_2015-->