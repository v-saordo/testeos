<properties
	pageTitle="Planeación del mantenimiento de máquinas virtuales de Azure | Microsoft Azure"
	description="Entienda qué es el mantenimiento planeado de Azure y cómo afecta a sus máquinas virtuales que se ejecutan en Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="drewm"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="drewm"/>


# Planeación del mantenimiento de máquinas virtuales de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Por qué Azure realiza mantenimiento planeado

Microsoft Azure realiza periódicamente actualizaciones en todo el planeta para mejorar la fiabilidad, el rendimiento y la seguridad de infraestructuras host que subyacen bajo las máquinas virtuales. Muchas de esas actualizaciones se realizan sin que afecten a las máquinas virtuales o a los servicios en la nube, incluidas las actualizaciones de conservación de memoria.

Sin embargo, algunas de estas actualizaciones requieren que las máquinas virtuales se reinicien para aplicar a la infraestructura las actualizaciones requeridas. Las máquinas virtuales se cierran mientras arreglamos la infraestructura y después las máquinas virtuales se reinician.

Tenga en cuenta que hay dos tipos de mantenimiento que pueden afectar a la disponibilidad de las máquinas virtuales: planeado y no planeado. En esta página se describe cómo Microsoft Azure realiza el mantenimiento planeado. Para obtener más información acerca del mantenimiento planeado, consulte [Mantenimiento planeado frente a mantenimiento no planeado](virtual-machines-manage-availability.md).

## Actualizaciones de conservación de memoria

En el caso de una clase de actualizaciones en Microsoft Azure, los clientes no verán afectadas sus máquinas virtuales en ejecución. Muchas de estas actualizaciones son componentes o servicios que se pueden actualizar sin interferir con la instancia en ejecución. Algunas de estas actualizaciones son actualizaciones de la infraestructura de la plataforma en el sistema operativo host, que se pueden aplicar sin necesidad de un reinicio completo de las máquinas virtuales.

Estas actualizaciones se realizan con la tecnología que permite la migración en vivo (una actualización de "conservación de memoria"). Al actualizar, la máquina virtual se pone en un estado de "pausa" y conserva la memoria RAM, mientras que el sistema operativo host subyacente recibe las actualizaciones y revisiones necesarias. La máquina virtual se reanudará en menos de 30 segundos después de haberse puesto en pausa. Una vez reanudada, se sincronizará automáticamente el reloj de la máquina virtual.

No todas las actualizaciones pueden implementarse con este mecanismo, pero gracias a su breve período de pausa, implementar las actualizaciones de este modo reduce en gran medida el impacto en las máquinas virtuales.

Se aplican actualizaciones de instancias múltiples (parar máquinas virtuales en un conjunto de disponibilidad) a un dominio de actualización a la vez.

## Configuraciones de máquinas virtuales

Hay dos tipos de configuraciones de máquinas virtuales: instancias múltiples y una sola instancia. En una configuración de instancias múltiples, las máquinas virtuales similares se colocan en un conjunto de disponibilidad.

La configuración de instancias múltiples proporciona redundancia entre equipos físicos, potencia y red, y se recomienda para garantizar la disponibilidad de la aplicación. Todas las máquinas virtuales del conjunto de disponibilidad deben prestar el mismo servicio a la aplicación.

Para obtener más información sobre la configuración de las máquinas virtuales para ofrecer alta disponibilidad, vea [Administración de la disponibilidad de las máquinas virtuales](virtual-machines-manage-availability.md).

Por el contrario, se usa una configuración de una sola instancia se usa para máquinas virtuales independientes no colocadas en un conjunto de disponibilidad. Por sí mismas, estas máquinas virtuales no tienen derecho al contrato de nivel de servicio (SLA) que requiere la implementación de dos o más máquinas virtuales en el mismo conjunto de disponibilidad.

Para obtener más información sobre los contratos de nivel de servicio, vea la sección "Servicios en la nube, máquinas virtuales y red virtual" de [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).


## Actualizaciones de una configuración de instancias múltiples

Durante el mantenimiento planeado, la plataforma Azure actualizará primero el conjunto de máquinas virtuales que se hospedan en una configuración de instancias múltiples. Esto hace que estas máquinas virtuales se reinicien con aproximadamente 15 minutos de inactividad.

En una configuración de instancias múltiples, las máquinas virtuales se actualizan de forma que conservan la disponibilidad durante todo el proceso, suponiendo que cada máquina realiza una función similar a las otras que forman parte del conjunto.

La plataforma Azure subyacente asigna a cada máquina virtual del conjunto de disponibilidad un dominio de actualización y un dominio de error. Cada dominio de actualización es un grupo de máquinas virtuales que se reiniciará en la misma ventana de tiempo. Cada dominio de error es un grupo de máquinas virtuales que comparten una fuente de alimentación común y un conmutador de red.

Para obtener más información sobre dominios de actualización y dominios de error, vea [Configuración de varias máquinas virtuales en un conjunto de disponibilidad para redundancia](virtual-machines-manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

Para evitar que los dominios de actualización se queden sin conexión al mismo tiempo, al realizar el mantenimiento, se cierra cada máquina virtual en un dominio de actualización, se aplica la actualización a los equipos host, se reinician las máquinas virtuales y se pasa al siguiente dominio de actualización. El evento de mantenimiento planeado termina cuando todos los dominios de actualización se han actualizado.

El orden en el que los dominios de actualización se reinician no puede llevarse a cabo secuencialmente durante un mantenimiento planeado, sino que solamente se reiniciará un dominio de actualización simultáneamente. Actualmente, Azure ofrece una notificación de mantenimiento planeado para máquinas virtuales con una semana de antelación en la configuración de instancias múltiples.

Después de restaurar una máquina virtual, aquí se muestra un ejemplo de lo que puede mostrar el Visor de eventos de Windows:

<!--Image reference-->
![][image2]

Use el visor para determinar qué máquinas virtuales están configuradas en una configuración de instancias múltiples mediante el portal de Azure, Azure PowerShell o la CLI de Azure. Por ejemplo, para determinar qué máquinas virtuales hay en una configuración de instancias múltiples, puede examinar la lista de máquinas virtuales con la columna del conjunto de disponibilidad agregada al cuadro de diálogo de exploración de las máquinas virtuales. En el siguiente ejemplo, las máquinas virtuales Example-VM1 y Example-VM2 están en una configuración de instancias múltiples:

<!--Image reference-->
![][image4]

## Actualizaciones de una configuración de una sola instancia

Una vez completadas las actualizaciones de instancias múltiples, Azure realizará la actualización de las máquinas virtuales de una sola instancia. Esta actualización también provoca el reinicio de las máquinas virtuales que no se ejecutan en conjuntos de disponibilidad.

Tenga en cuenta que, aunque solamente tenga una instancia ejecutándose en un conjunto de disponibilidad, la plataforma Azure seguirá tratándola como una actualización de instancias múltiples.

Las máquinas virtuales con una configuración de una sola instancia se actualizan cerrándolas, aplicando la actualización a la máquina host y reiniciando las máquinas virtuales, con 15 minutos de inactividad aproximadamente. Estas actualizaciones se ejecutan en todas las máquinas virtuales de una región en una sola ventana de mantenimiento.

Este evento de mantenimiento planeado afectará a la disponibilidad de la aplicación para este tipo de configuración de máquina virtual. Azure ofrece una notificación de mantenimiento planeado para máquinas virtuales con 1 semana de antelación en la configuración de una instancia.

### Notificación por correo electrónico

Solo en el caso de las configuraciones de máquinas virtuales de una instancia o de instancias múltiples, Azure envía con antelación una comunicación por correo electrónico para avisarle del próximo mantenimiento planeado (una semana de antelación). Este correo electrónico se enviará a las cuentas de correo electrónico del administrador y coadministrador de cuenta proporcionadas en la suscripción. A continuación se muestra un ejemplo de este tipo de correo electrónico:

<!--Image reference-->
![][image1]

## Pares de región

Al ejecutar el mantenimiento, Azure solo actualizará las instancias de máquina virtual en una sola región de su pareja. Por ejemplo, al actualizar las máquinas virtuales de la zona centro-norte de EE. UU., Azure no actualizará las máquinas virtuales de centro-sur de EE. UU. al mismo tiempo. Se programarán a una hora independiente, lo que permitirá la conmutación por error o el equilibrio de carga entre regiones. Sin embargo, otras regiones como Europa del Norte pueden estar en mantenimiento al mismo tiempo que el Este de EE. UU.

Consulte la tabla siguiente para obtener información sobre los pares de región actuales:

Región 1 | Región 2
:----- | ------:
Centro-Norte de EE. UU | Centro-Sur de EE. UU
Este de EE. UU. | Oeste de EE. UU.
Este de EE. UU. - 2 | Central EE. UU.:
Europa del Norte | Europa occidental
Sudeste de Asia | Asia oriental
Este de China | Norte de China
Este de Japón | Oeste de Japón
Sur de Brasil | Centro-Sur de EE. UU
Sudeste de Australia | Australia Oriental
Gobierno de EE. UU. - Iowa | Gobierno de EE. UU. - Virginia

<!--Anchors-->
[image1]: ./media/virtual-machines-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-planned-maintenance/EventViewerPostReboot.png
[image3]: ./media/virtual-machines-planned-maintenance/RegionPairs.PNG
[image4]: ./media/virtual-machines-planned-maintenance/AvailabilitySetExample.png


<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/

<!---HONumber=AcomDC_0204_2016-->