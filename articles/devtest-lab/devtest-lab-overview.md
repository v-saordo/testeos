<properties 
	pageTitle="¿Qué es el servicio Laboratorio de desarrollo y pruebas? | Microsoft Azure"
	description="Aprenda cómo el Laboratorio de desarrollo y pruebas puede facilitar la creación, la administración y la supervisión de máquinas virtuales de Azure"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/30/2016"
	ms.author="tarcher"/>

#¿Qué es el Laboratorio de desarrollo y pruebas?

Los desarrolladores y evaluadores están buscando la manera de resolver los retrasos en la creación y administración de sus entornos trasladándolos a la nube. Azure soluciona el problema de los retrasos en el entorno y hace posible el autoservicio dentro de una estructura nueva y rentable. Sin embargo, los desarrolladores y evaluadores necesitan aún dedicar mucho tiempo a la configuración de los entornos que se administran automáticamente. Además, los encargados de la toma de decisiones no tienen claro cómo aprovechar la nube para maximizar sus ahorros sin agregar demasiada sobrecarga al proceso.

El Laboratorio de desarrollo y pruebas de Azure es un servicio que ayuda a los desarrolladores y evaluadores a crear rápidamente entornos de Azure al tiempo que se optimizan los recursos y se controlan los costos. Puede probar la versión más reciente de la aplicación aprovisionando rápidamente entornos de Windows y Linux mediante plantillas y artefactos reutilizables. Integre fácilmente la canalización de la implementación con el Laboratorio de desarrollo y pruebas para proporcionar entornos a petición. Escale verticalmente las pruebas de carga mediante el aprovisionamiento de varios agentes de prueba y cree entornos previamente aprovisionados con fines de capacitación y demostración.

##¿Por qué usar el Laboratorio de desarrollo y pruebas?

El Laboratorio de desarrollo y pruebas proporciona las siguientes ventajas en la creación, la configuración y la administración de entornos de desarrollador y prueba en la nube:

###Autoservicio libre de preocupaciones

El Laboratorio de desarrollo y pruebas facilita el control de costos al permitirle definir directivas en el laboratorio, como el número de máquinas virtuales (VM) por usuario y el número de máquinas virtuales por laboratorio. El Laboratorio de desarrollo y pruebas también permite crear directivas para apagar e iniciar automáticamente las máquinas virtuales.

###Obtenga acceso rápidamente al modo "Listo para probar"

El Laboratorio de desarrollo y pruebas le permite crear entornos ya aprovisionados con todo lo que su equipo necesita para comenzar a desarrollar y probar aplicaciones. Basta con notificar los entornos en los que se instaló la última compilación en buen estado de la aplicación y podrá empezar a trabajar de inmediato. O bien, utilice contenedores para agilizar y simplificar aún más la creación de entornos.

###Se crean una vez y se utilizan en todas partes

Capture y comparta plantillas de entornos y artefactos dentro de su equipo u organización, todo con control de código fuente, para crear entornos de desarrollo y pruebas con facilidad.

###Se integra con la cadena de herramientas existente

Aproveche complementos ya creados o nuestra API para aprovisionar entornos de desarrollo y pruebas directamente desde la herramienta de integración continua (CI), el entorno de desarrollo integrado (IDE) o la canalización de entrega de versiones que prefiera. También puede utilizar nuestra completa herramienta de línea de comandos.

##Conceptos del Laboratorio de desarrollo y pruebas

La lista siguiente contiene las definiciones y los conceptos clave del Laboratorio de desarrollo y pruebas:

Los **artefactos** se utilizan para implementar y configurar la aplicación después de aprovisionar una máquina virtual. Los artefactos pueden ser:

- Herramientas que desea instalar en la máquina virtual, como agentes, Fiddler y Visual Studio.
- Acciones que desea ejecutar en la máquina virtual, como la clonación de un repositorio.
- Aplicaciones que desea probar.

Los artefactos son archivos JSON basados en el Administrador de recursos de Azure que contienen instrucciones para realizar la implementación y aplicar la configuración. Puede leer más acerca del Administrador de recursos de Azure en [Información general del Administrador de recursos de Azure](/resource-group-overview.md).

Los **repositorios de artefactos** son repositorios de Git donde se insertan los artefactos. Es posible agregar los mismos repositorios de artefactos a varios laboratorios de su organización a fin de reutilizarlos y compartirlos.

**Base** es una imagen de máquina virtual con todas las herramientas y los valores de configuración preinstalados y configurados para crear rápidamente una máquina virtual. Puede aprovisionar una máquina virtual seleccionando una base existente y agregando un artefacto para instalar su agente de prueba. Luego puede guardar la máquina virtual aprovisionada como base para que esta pueda utilizarse sin que sea necesario volver a instalar al agente de prueba para cada aprovisionamiento de la máquina virtual.

Los **límites** son un mecanismo que sirve para minimizar la pérdida en el laboratorio. Por ejemplo, puede establecer un límite para restringir el número de máquinas virtuales que se pueden crear por usuario o en un laboratorio.

Las**directivas** ayudan a controlar los costos en su laboratorio. Por ejemplo, puede crear una directiva para apagar automáticamente las máquinas virtuales según una programación definida.

##Pasos siguientes

Para obtener una introducción al Laboratorio de desarrollo y pruebas, siga el tutorial paso a paso [Creación de un laboratorio de desarrollo y pruebas de Azure](devtest-lab-create-lab.md).

<!---HONumber=AcomDC_0204_2016-->