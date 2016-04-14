<properties
	pageTitle="Distribuciones aprobadas de Linux | Microsoft Azure"
	description="Obtenga información acerca de Linux en distribuciones aprobadas de Azure, incluyendo instrucciones para Ubuntu, OpenLogic, Oracle y SUSE."
	services="virtual-machines"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management,azure-resource-manager"
	/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/08/2015"
	ms.author="szark"/>



#Linux en distribuciones aprobadas por Azure

Varios asociados ofrecen las imágenes de Linux del Marketplace o de la Galería de Azure; además, trabajamos con distintas comunidades de Linux para agregar aún más tipos a la lista de distribuciones aprobadas. Mientras tanto, para distribuciones no disponibles desde la galería, siempre puede ofrecer su propio Linux siguiendo las directrices de [esta página](virtual-machines-linux-create-upload-vhd.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## Distribuciones y versiones admitidas ##

En la tabla siguiente se enumeran las distribuciones y versiones de Linux que se admiten en Azure.

Los controladores de los Servicios de integración de Linux (LIS) para Hyper-V y Azure son módulos del kernel que Microsoft aporta directamente al kernel de Linux del canal de subida. Los controladores de LIS están integrados en el núcleo de distribución de forma predeterminada o, para distribuciones anteriores basadas en RHEL/CentOS, están disponibles [aquí](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) en forma de descarga independiente. Consulte [este artículo](virtual-machines-linux-create-upload-vhd-generic.md#linux-kernel-requirements) para obtener más información acerca de los controladores de LIS.

El Agente de Linux de Azure ya está preinstalado en las imágenes de la Galería de imágenes de Azure y generalmente está disponible en el repositorio de paquetes de distribución. El código fuente se puede encontrar en [GitHub](https://github.com/azure/walinuxagent).

Distribución|Versión|Controladores|Agente
---|---|---|---
CentOS de OpenLogic |CentOS 6.3+, 7.0+| CentOS 6.3: [Descarga de LIS](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>CentOS 6.4+: en kernel|Paquete: en el repositorio <a href="http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/">OpenLogic, en "WALinuxAgent"<p><p>Código fuente: [GitHub](https://github.com/Azure/WALinuxAgent)
[CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/)|494\.4.0+ |En kernel|Código fuente: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent)
Debian por Credativ |Debian 7.9+, 8.2+|En kernel|Paquete: en el repositorio, en "waagent" <p><p>Código fuente: [GitHub](https://github.com/Azure/WALinuxAgent)
Oracle Linux| 6\.4+, 7.0+|En kernel|Paquete: en el repositorio, en "WALinuxAgent" <p><p>Código fuente: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
Red Hat Enterprise Linux |RHEL 6.7+, 7.1+|En kernel|Paquete: en el repositorio, en "WALinuxAgent" <p><p>Código fuente: [GitHub](https://github.com/Azure/WALinuxAgent)
SUSE Linux Enterprise |SLES 11 SP3+, SLES 12+ y <p><p> SLES para SAP 11.3+ |En kernel|Paquete: en el repositorio [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools), en "WALinuxAgent" <p><p>Código fuente: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
openSUSE |openSUSE 13.1+|En kernel|Paquete: en el repositorio [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools), en "WALinuxAgent" <p><p>Código fuente: [GitHub](https://github.com/Azure/WALinuxAgent)
Ubuntu|Ubuntu 12.04, 14.04, 15.04 y 15.10|En kernel|Paquete: en el repositorio, en "walinuxagent" <p><p>Código fuente: [GitHub](https://github.com/Azure/WALinuxAgent)
## Asociados

### OpenLogic
[http://www.openlogic.com/azure](http://www.openlogic.com/azure)

OpenLogic es un proveedor líder de soluciones empresariales de código abierto para la nube y el centro de datos. OpenLogic ayuda a cientos de empresas líderes en una amplia variedad de sectores para adquirir, respaldar y controlar de manera segura el software de código abierto. OpenLogic ofrece soporte técnico de calidad comercial e indemnización para 600 paquetes de código abierto con el respaldo de la comunidad de expertos de OpenLogic, incluido soporte técnico a nivel empresarial para CentOS, además de ser el asociado de lanzamiento para brindar imágenes basadas en CentOS en Azure.

### CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

En el sitio web de CoreOS:

*CoreOS está diseñado para la seguridad, coherencia y confiabilidad. En lugar de instalar paquetes a través de yum o apt, CoreOS utiliza contenedores de Linux para administrar los servicios en un nivel superior de abstracción. Un código y todas las dependencias de un servicio único se empaquetan dentro de un contenedor que se puede ejecutar en uno o varios equipos de CoreOS.*


### Credativ
[http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ es una empresa de servicios y consultoría independiente, especializada en el desarrollo y la implementación de soluciones profesionales mediante el uso de software gratuito. Como especialistas en código abierto líderes en el sector, Credative goza de reconocimiento en el ámbito internacional y numerosos departamentos de TI recurren a su soporte técnico. Actualmente, Credativ está preparando con Microsoft imágenes Debian correspondientes para Debian 8 (Jessie) y Debian antes de 7 (Wheezy), que están diseñadas especialmente para ejecutarse en Azure y pueden administrarse fácilmente a través de la plataforma. Credativ también será compatible con el mantenimiento y la actualización a largo plazo de las imágenes de Debian para Azure a través de sus centros de soporte técnico de código abierto.

### Oracle
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

La estrategia de Oracle es ofrecer una amplia cartera de soluciones para nubes públicas y privadas, mientras se brinda a los clientes la posibilidad de elegir y flexibilidad en la manera de implementar el software de Oracle en nubes de Oracle, así como también otras nubes. La asociación de Oracle y Microsoft permite que los clientes implementen software de Oracle en nubes públicas y privadas de Microsoft con la confianza que brinda la certificación y el respaldo de Oracle. El compromiso y la inversión de Oracle en las soluciones de nubes públicas y privadas de Oracle siguen intactos.

### Red Hat
[http://www.redhat.com/en/partners/strategic-alliance/microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

Red Hat, proveedor líder mundial de soluciones de código abierto, ayuda a más del 90 % de las empresas Fortune 500 a resolver desafíos empresariales, a alinear sus estrategias empresariales y de TI, y a prepararse para el futuro de la tecnología. Red Hat hace esto ofreciendo soluciones seguras a través de un modelo de negocio abierto y un modelo de suscripción asequible y predecible.

### SUSE
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server en Azure es una plataforma probada que brinda confiabilidad y seguridad de nivel superior para la informática en nube. La versátil plataforma Linux de SUSE se integra de manera fluida con los servicios en la nube de Azure para brindar un entorno de nube fácilmente administrable. Y con más de 9.200 aplicaciones certificadas de más de 1.800 fabricantes de software independiente para SUSE Linux Enterprise Server, SUSE garantiza que las cargas de trabajo que se ejecutan de manera compatible en el centro de datos pueden implementarse con confianza en Azure.

### Canonical
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

La ingeniería de Canonical y la gobernanza de comunidad abierta impulsan el éxito de Ubuntu en la informática en nube, servidor y cliente, incluidos servicios personales en la nube para consumidores. La visión de Canonical es una plataforma gratuita unificada en Ubuntu, desde el teléfono a la nube, con una familia de interfaces consistentes para el teléfono, tabletas, televisores y equipos de escritorio, que hace de Ubuntu la primera opción para distintas instituciones, desde proveedores de nube pública a fabricantes de aparatos electrónicos de consumo, y una de las favoritas de los tecnólogos.

Con desarrolladores y centros de ingeniería alrededor de todo el mundo, Canonical ocupa una posición exclusiva para asociarse con fabricantes de software, proveedores de contenido y desarrolladores de software para poner en el mercado las soluciones de Ubuntu, desde equipos a servidores y dispositivos portátiles.

<!---HONumber=AcomDC_1210_2015-->