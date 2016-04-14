<properties
   pageTitle="Uso de SAP en máquinas virtuales de Azure | Microsoft Azure"
   description="Uso de SAP en máquinas virtuales de Azure"
   services="virtual-machines,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="juergent"
   editor=""
   tags="azure-service-management"
   keywords=""/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="na"
   ms.date="02/12/2016"
   ms.author="sedusch"/>
   
# Uso de SAP en máquinas virtuales de Azure

La informática en la nube es un término ampliamente usado que está cobrando cada vez más importancia en la industria de TI, desde pequeñas empresas hasta las grandes corporaciones y multinacionales. Microsoft Azure es la plataforma de servicios en la nube de Microsoft que ofrece una amplia gama de nuevas posibilidades. Ahora los clientes pueden aprovisionar y desaprovisionar rápidamente aplicaciones como servicios en la nube, por lo que no se verán limitados por restricciones técnicas o presupuestarias. En lugar de invertir tiempo y presupuesto en infraestructura de hardware, las empresas pueden centrarse en la aplicación, en los procesos empresariales y en sus ventajas para clientes y usuarios.

Con los servicios de máquinas virtuales de Microsoft Azure, Microsoft ofrece una completa plataforma de infraestructura como servicio (IaaS). Las aplicaciones de basadas en SAP NetWeaver son compatibles en las máquinas virtuales de Azure (IaaS). Las notas del producto siguientes describen cómo planear e implementar aplicaciones basadas en SAP NetWeaver en Microsoft Azure como plataforma preferida.

## Planeamiento e implementación

Título: SAP NetWeaver en máquinas virtuales de Azure: guía de planeación e implementación

Resumen: Este es el documento por donde debe empezar si está pensando en ejecutar SAP NetWeaver en máquinas virtuales de Azure. Esta guía de planeación e implementación le ayudará a evaluar si un sistema basado SAP NetWeaver planeado o existente puede implementarse en un entorno de máquinas virtuales de Azure. Cubre varios escenarios de implementación de SAP NetWeaver e incluye configuraciones SAP específicas para Azure. Este documento enumera y describe toda la información de configuración necesaria que deberá tener al lado de SAP/Azure para ejecutar un entorno SAP híbrido. También cubre las medidas que se pueden tomar para garantizar una alta disponibilidad de los sistemas basados en SAP NetWeaver en IaaS.

Actualizado: agosto de 2015

[Descargar esta guía ahora](http://go.microsoft.com/fwlink/?LinkId=397963)
## Implementación
Título: SAP NetWeaver en máquinas virtuales de Azure: Guía de implementación

Resumen: Este documento proporciona una guía de los procedimientos necesarios para implementar el software SAP NetWeaver en máquinas virtuales de Azure Este documento se centra en tres escenarios de implementación específicos, con énfasis en la habilitación de las Extensiones de supervisión de Azure para SAP, incluidas las recomendaciones para la solución de problemas de las Extensiones de supervisión de Azure para SAP. Este documento supone que ha leído la guía de planeación e implementación.

Actualizado: septiembre de 2015

[Descargar esta guía ahora](http://go.microsoft.com/fwlink/?LinkId=397964)

## SAP DBMS en Azure
Título: SAP DBMS en la guía de implementación de Azure

Resumen: Este documento cubre las consideraciones de planeación e implementación para los sistemas DBMS que deben ejecutarse con SAP. En la primera parte se presentan y se enumeran consideraciones generales. Las siguientes partes del documento están relacionadas con las implementaciones de diferentes DBMS en Azure que admite SAP. Diferentes DBMS que se presentan son SQL Server, ASE de SAP, Oracle, SAP MaxDB e IBM DB2 para Linux, Unix y Windows. En estas partes concretas, se tratan las consideraciones que debe tener en cuenta al ejecutar sistemas SAP en Azure junto con los DBMS. Se incluyen temas como los métodos de copia de seguridad y de alta disponibilidad que admiten los distintos DBMS en Azure para el uso con aplicaciones SAP.

Actualización: diciembre de 2015

[Descargar esta guía ahora](http://go.microsoft.com/fwlink/?LinkId=397965)

## SAP NetWeaver en Azure

Título: SAP NetWeaver: creación de una solución de recuperación ante desastres basada en Azure

Resumen: Este documento proporciona una guía paso a paso para crear una solución de recuperación ante desastres basada en Azure para SAP NetWeaver. La solución descrita asume que el entorno SAP se ejecuta en instalaciones locales virtualizadas basadas en Hyper-V. En la primera parte del documento, los servicios de Azure Site Recovery (ASR) se presentan en sus componentes. La segunda parte del documento describe características específicas para entornos basados en SAP NetWeaver. Se explican y describen las posibilidades de uso de ASR con instancias de aplicación de SAP NetWeaver y los servicios centrales de SAP. La segunda parte se centra en cómo usar ASR para los servicios centrales de SAP protegidos con configuraciones del clúster de conmutación por error de Windows Server.

Actualizado: septiembre de 2015

[Descargar esta guía ahora](http://go.microsoft.com/fwlink/?LinkID=521971)

## SAP NetWeaver en Azure - Alta disponibilidad

Título: SAP NetWeaver en Azure - Instancias de SAP ASCS/SCS de agrupación de clústeres mediante el clúster de conmutación por error de Windows Server en Azure con SIOS DataKeeper

Resumen: En este documento se describe cómo usar SIOS DataKeeper para establecer una configuración de SAP ASCS/SCS de alta disponibilidad en Azure. SAP protege su punto único de componentes de error como SAP ASCS/SCS o Enqueue Replication Services con las configuraciones de clúster de conmutación por error de Windows Server que requieren los discos compartidos. Estos componentes SAP son esenciales para la funcionalidad de un sistema SAP. Por lo tanto, es necesario implementar funcionalidad de alta disponibilidad para asegurarse de que dichos componentes pueden soportar un error de un servidor o una máquina virtual tal como se hizo con las configuraciones de Windows Cluster para entornos Hyper-V y de reconstrucción completa. Desde agosto de 2015, Azure en sí mismo no puede proporcionar los discos compartidos que serían necesarios para las configuraciones de alta disponibilidad basadas en Windows necesarias para estos componentes críticos de SAP. Sin embargo con la ayuda del producto DataKeeper de SIOS, las configuraciones de clúster de conmutación por error de Windows Server que sean necesarias para SAP ASCS/SCS pueden crearse en la plataforma de IaaS de Azure. En este documento se describe paso a paso cómo instalar una configuración de clúster de conmutación por error de Windows Server con el disco compartido que SIOS Datakeeper proporciona en Azure. En el documento se explican detalles de las configuraciones del lado de Azure, Windows y SAP que hacen que la configuración de alta disponibilidad funcione de manera óptima. El documento complementa la Documentación de instalación de SAP y las Notas de SAP, que representan los principales recursos para las instalaciones e implementaciones de software de SAP en las plataformas proporcionadas.

Actualizado: agosto de 2015

[Descargar esta guía ahora](http://go.microsoft.com/fwlink/?LinkId=613056)

## SAP NetWeaver en máquinas virtuales Linux de SUSE de Azure

Título: Pruebas de SAP NetWeaver en máquinas virtuales de SUSE Linux de Microsoft Azure

Resumen: no hay ninguna compatibilidad oficial con SAP para la ejecución de SAP NetWeaver en máquinas virtuales con Linux de Azure en este momento. Sin embargo, es posible que los clientes quieran hacer algunas pruebas o que consideren la posibilidad de ejecutar sistemas de aprendizaje o demostración SAP en máquinas virtuales con Linux en Azure siempre que no sea necesario para ponerse en contacto con el soporte de SAP. Este artículo debe ayudar a configurar las máquinas virtuales con Linux de Azure SUSE para la ejecución de SAP y ofrece algunas sugerencias básicas con el fin de evitar posibles problemas comunes.

Actualización: diciembre de 2015

[Este artículo se puede encontrar aquí](virtual-machines-sap-on-linux-suse-quickstart.md)

<!---HONumber=AcomDC_0218_2016-->