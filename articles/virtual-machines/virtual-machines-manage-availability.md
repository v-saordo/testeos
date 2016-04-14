<properties
	pageTitle="Administración de la disponibilidad de las máquinas virtuales | Microsoft Azure"
	description="Aprenda cómo utilizar varias máquinas virtuales para garantizar una alta disponibilidad para su aplicación de Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="kenazk"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/23/2015"
	ms.author="kenazk"/>

# Administración de la disponibilidad de las máquinas virtuales

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Descripción del mantenimiento planeado frente al no planeado
Existen dos tipos de eventos de la plataforma Microsoft Azure que pueden afectar a la disponibilidad de sus máquinas virtuales: el mantenimiento planeado y el mantenimiento no planeado.

- Los **eventos de mantenimiento planeado** son actualizaciones periódicas realizadas por Microsoft en la plataforma Azure subyacente para mejorar en general la fiabilidad, el rendimiento y la seguridad de la infraestructura de la plataforma sobre las que se funcionan sus máquinas virtuales. La mayoría de estas actualizaciones se realizan sin afectar a las máquinas virtuales ni a los servicios en la nube. Sin embargo, en ocasiones estas actualizaciones requieren reiniciar la máquina virtual para aplicar las actualizaciones necesarias a la infraestructura de las plataformas.

- Los **eventos de mantenimiento no planeado** se producen cuando el hardware o la infraestructura física subyacente a su máquina virtual presentan algún tipo de error. Entre estos podemos encontrar errores de la red local, errores de los discos locales u otras errores a nivel de bastidor. Cuando se detecta un error de este tipo, la plataforma Azure automáticamente migrará su máquina virtual desde la máquina física averiada que aloja la máquina virtual hasta una máquina física sin problemas. Estos eventos no son habituales, pero pueden hacer que su máquina virtual se reinicie.

## Siga estas mejores prácticas para diseñar su aplicación para la alta disponibilidad.
Para reducir el impacto del tiempo de parada debido a uno o más de estos eventos, recomendamos las siguientes mejores prácticas de alta disponibilidad para las máquinas virtuales:

* [Configure varias máquinas virtuales en un conjunto de disponibilidad para la redundancia]
* [Configure cada nivel de aplicación en conjuntos separados de disponibilidad]
* [Combine el equilibrador de carga con los conjuntos de disponibilidad]
* [Evite las máquinas virtuales de instancia única en los conjuntos de disponibilidad]

### Configure varias máquinas virtuales en un conjunto de disponibilidad para la redundancia
Para proporcionar redundancia a la aplicación, recomendamos que agrupe dos máquinas virtuales o más en un conjunto de disponibilidad. Esta configuración garantiza que durante un evento de mantenimiento planeado o no planeado, al menos una máquina virtual estará disponible y cumplirá el 99,95% de los niveles de servicio contratados de Azure. Para obtener más información sobre los contratos de nivel de servicio, vea la sección "Servicios en la nube, máquinas virtuales y red virtual" en [Contratos de nivel de servicio](../../../support/legal/sla/).

La plataforma Azure subyacente asigna a cada máquina virtual del conjunto de disponibilidad un dominio de actualización (UD) y un dominio de error (FD). Para un conjunto de disponibilidad determinado, se asignan cinco UD, que el usuario no puede configurar, para indicar grupos de máquinas virtuales y hardware físico subyacente que se pueden reiniciar simultáneamente. Cuando se configuran más de cinco máquinas virtuales dentro de un único conjunto de disponibilidad, la sexta máquina virtual se colocará en el mismo UD que la primera, la séptima en el mismo que la segunda y así sucesivamente. El orden en que los UD se reiniciarán tal vez no siga una secuencia durante un mantenimiento planeado, pero solo se reiniciarán uno por uno.

FD definen un grupo de máquinas virtuales que comparten un origen de alimentación y un interruptor de red comunes. De manera predeterminada, las máquinas virtuales configuradas dentro de su conjunto de disponibilidad se separan en dos FD. Aunque colocar las máquinas virtuales en un conjunto de disponibilidad no protege su aplicación contra errores del sistema operativo ni específicos de aplicaciones, limita el impacto de posibles errores de hardware físico, interrupciones de red o cortes de alimentación.

<!--Image reference-->
   ![Configuración de UD y FD](./media/virtual-machines-manage-availability/ud-fd-configuration.png)

>[AZURE.NOTE] Para obtener instrucciones, vea [Configuración de un conjunto de disponibilidad para máquinas virtuales][].

### Configure cada nivel de aplicación en conjuntos separados de disponibilidad
Si las máquinas virtuales en su conjunto de disponibilidad son casi idénticas y tienen la misma función en su aplicación, recomendamos que configure un conjunto de disponibilidad para cada nivel de la aplicación. Si coloca dos niveles diferentes en el mismo conjunto de disponibilidad, todas las máquinas virtuales en un mismo nivel de aplicación se podrían reiniciar simultáneamente. Al configurar al menos dos máquinas virtuales para cada nivel, se garantiza que al menos una máquina virtual en cada nivel estará disponible.

Por ejemplo, podría poner todas las máquinas virtuales en el front-end de la aplicación que usa IIS, Apache, Nginx, etc., en un solo conjunto de disponibilidad. Asegúrese de que solo las máquinas virtuales de front-end se colocan en el mismo conjunto de disponibilidad. De la misma manera, asegúrese de que solo las máquinas virtuales de niveles de datos se colocan en su propio conjunto de disponibilidad, como por ejemplo, las máquinas virtuales replicadas de SQL Server o las de MySQL.

<!--Image reference-->
   ![Niveles de aplicación](./media/virtual-machines-manage-availability/application-tiers.png)


### Combine el equilibrador de carga con los conjuntos de disponibilidad
Combine el equilibrador de carga con un conjunto de disponibilidad para aprovechar al máximo la resistencia de la aplicación. El equilibrador de carga de Azure distribuye el tráfico entre varias máquinas virtuales. El equilibrador de carga de Azure está incluido en nuestras máquinas virtuales de niveles estándar. Tenga en cuenta que no todos los niveles de las máquinas virtuales incluyen el equilibrador de carga de Azure. Para obtener más información sobre el equilibrio de carga en máquinas virtuales, lea [Equilibrio de carga de máquinas virtuales](../load-balance-virtual-machines.md).

Si el equilibrador de carga no está configurado para equilibrar el tráfico entre varias máquinas virtuales, entonces cualquier evento de mantenimiento planeado afectará a la única máquina virtual dedicada al tráfico y provocará una interrupción del nivel de la aplicación. Si se colocan varias máquinas virtuales en el mismo nivel en el mismo equilibrador de carga y conjunto de disponibilidad, se permitirá tener un tráfico continuamente disponible asistido por al menos una instancia.

### Evite las máquinas virtuales de instancia única en los conjuntos de disponibilidad
Evite dejar una máquina virtual de instancia única sola en un conjunto de disponibilidad. Las máquinas virtuales en esta configuración no tienen derecho a la garantía de los contratos de nivel de servicio y sufrirán un tiempo de inactividad durante los eventos de Azure mantenimiento planeado. Tenga en cuenta que la instancia de máquina virtual única dentro de un conjunto de disponibilidad también recibirá una notificación de correo electrónico por adelantado en la notificación de mantenimiento de máquinas virtuales planeadas de varias instancias.

<!-- Link references -->
[Configure varias máquinas virtuales en un conjunto de disponibilidad para la redundancia]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Configure cada nivel de aplicación en conjuntos separados de disponibilidad]: #configure-each-application-tier-into-separate-availability-sets
[Combine el equilibrador de carga con los conjuntos de disponibilidad]: #combine-the-load-balancer-with-availability-sets
[Evite las máquinas virtuales de instancia única en los conjuntos de disponibilidad]: #avoid-single-instance-virtual-machines-in-availability-sets
[Configuración de un conjunto de disponibilidad para máquinas virtuales]: virtual-machines-how-to-configure-availability.md

<!---HONumber=Oct15_HO4-->