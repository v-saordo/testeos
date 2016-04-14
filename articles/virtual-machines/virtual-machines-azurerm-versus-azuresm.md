<properties
   pageTitle="Proveedores de proceso, red y almacenamiento | Microsoft Azure"
   description="Información general conceptual de proveedores de recursos de procesos, redes y almacenamiento (PRC, NRP y SRP) en el Administrador de recursos de Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="mahthi"
   manager="timlt"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure-services"
   ms.date="04/29/2015"
   ms.author="mahthi"/>

# Proceso, red y proveedores de almacenamiento de Azure en el Administrador de recursos de Azure

La inclusión de capacidades de procesos, redes y almacenamiento con el Administrador de recursos de Azure fundamentalmente simplificará la implementación y administración de aplicaciones complejas que se ejecuta en IaaS. Muchas aplicaciones requieren una combinación de recursos, como una red virtual, una cuenta de almacenamiento, una máquina virtual y una interfaz de red. El Administrador de recursos de Azure ofrece la capacidad de crear una plantilla JSON para implementar y administrar todos estos recursos como una sola aplicación.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## Ventajas de la integración de procesos, redes y almacenamiento en el Administrador de recursos de Azure

El Administrador de recursos de Azure ofrece la capacidad de aprovechar fácilmente las plantillas de aplicación pregenerada o crear una plantilla de aplicación para implementar y administrar los recursos de procesos, redes y almacenamiento de Azure. En esta sección, analizaremos las ventajas de implementar recursos a través del Administrador de recursos de Azure.

-	La complejidad simplificada: cree, integre y colabore en aplicaciones complicadas que pueden incluir toda la gama de recursos de Azure (por ejemplo, sitios web, bases de datos SQL, máquinas virtuales o redes virtuales) desde un archivo de plantilla que se puede compartir
-	Flexibilidad para tener implementaciones repetibles para administradores de sistemas, devOps y desarrollo cuando se utiliza el mismo archivo de plantilla
-	La integración profunda de extensiones de máquina virtual (scripts personalizados, DSC, Chef, Puppet, etc.) con el Administrador de recursos de Azure en un archivo de plantilla permite una fácil orquestación de la configuración en la VM
-	Definición de etiquetas y la propagación de facturación de las etiquetas de los recursos de procesos, redes y almacenamiento
-	Administración de acceso a recursos de la organización sencilla y precisa con el Control de acceso basado en roles (RBAC) de Azure
-	Historial de actualización simplificado mediante la modificación de la plantilla original y, a continuación, una nueva implementación


## Avances de las API de procesos, redes y almacenamiento en el Administrador de recursos de Azure

Además de las ventajas que se han mencionado anteriormente, existen algunos avances de rendimiento significativos en las API publicadas.

-	Habilitación de la implementación masiva y paralela de las máquinas virtuales
-	Compatibilidad con tres dominios de error en conjuntos de disponibilidad
-	Extensión de script personalizada mejorada que permite la especificación de scripts desde cualquier dirección URL personalizada accesible públicamente
- Integración de máquinas virtuales con el almacén de claves de Azure para un almacenamiento muy seguro e implementación privada de secretos desde los [módulos de seguridad de hardware](http://wikipedia.org/wiki/Hardware_security_module) [validados por FIPS](http://wikipedia.org/wiki/FIPS_140-2)
-	Proporciona los bloques de creación básicos de las redes a través de API que permiten a los clientes construir aplicaciones complicadas que incluyen interfaces de red, equilibradores de carga y redes virtuales
-	Las interfaces de red como un nuevo objeto permiten mantener y volver a usar configuraciones de redes complicadas para las máquinas virtuales
-	Los equilibradores de carga como un recurso de primera clase permiten la asignación de direcciones IP
-	Las API de red virtual pormenorizadas permiten simplificar la administración de redes virtuales individuales

## Diferencias conceptuales con la introducción de nuevas API

En esta sección, examinaremos algunas de las diferencias conceptuales más importantes entre las API basadas en XML disponibles hoy en día y las API basadas en JSON disponibles a través del Administrador de recursos de Azure para procesos, redes y almacenamiento.

 Elemento | Administración de servicios de Azure (basado en XML) | Proveedores de procesos, redes y almacenamiento (basados en JSON)
 ---|---|---
| Servicio en la nube para máquinas virtuales |	El servicio en la nube es un contenedor para las máquinas virtuales que exige la disponibilidad de la plataforma y el equilibrio de carga. | El servicio en la nube ya no es un objeto necesario para crear una máquina virtual usando el nuevo modelo. |
| Conjuntos de disponibilidad | La disponibilidad en la plataforma se ha indicado mediante la configuración del mismo "AvailabilitySetName" en las máquinas virtuales. El número máximo de dominios con error era de 2. | Un conjunto de disponibilidad es un recurso expuesto por el proveedor de Microsoft.Compute. Las máquinas virtuales que requieren alta disponibilidad deben incluirse en el conjunto de disponibilidad. Ahora, el recuento máximo de dominios con error es de 3. |
| Grupos de afinidad |	Los grupos de afinidad eran necesarios para crear redes virtuales. Sin embargo, con la introducción de las redes virtuales regionales, ya no era necesario. |Para simplificar, no existe el concepto de grupos de afinidad en las API expuestas a través del Administrador de recursos de Azure. |
| Equilibrio de carga | La creación de un servicio en la nube proporciona un equilibrador de carga implícito para las máquinas virtuales implementadas. | El equilibrador de carga es un recurso expuesto por el proveedor de Microsoft.Network. La interfaz de red principal de las máquinas virtuales cuya carga se debe equilibrar debe hacer referencia al equilibrador de carga. Los equilibradores de carga pueden ser internos o externos. [Más información.](resource-groups-networking.md) |
|Dirección IP virtual | Los servicios en la nube obtendrán a una VIP (dirección IP virtual) predeterminada cuando se agrega una máquina virtual a un servicio en la nube. La dirección IP virtual es la dirección asociada al equilibrador de carga implícito. | La dirección IP pública es un recurso expuesto por el proveedor de Microsoft.Network. La dirección IP pública puede ser estática (reservada) o dinámica. Las direcciones IP públicas dinámicas pueden asignarse a un equilibrador de carga. Las direcciones IP públicas se pueden proteger mediante grupos de seguridad. |
|Dirección IP reservada|	Puede reservar una dirección IP en Azure y asociarlo a un servicio en la nube para asegurarse de que la dirección IP es permanente. | La dirección IP pública puede crearse en modo "Estático" y ofrece la misma capacidad que una "dirección IP reservada". Las direcciones IP públicas estáticas solo puede asignarse a un equilibrador de carga ahora. |
|Dirección IP pública (PIP) por máquina virtual | Las direcciones IP públicas también se pueden asociar directamente a una máquina virtual. | La dirección IP pública es un recurso expuesto por el proveedor de Microsoft.Network. La dirección IP pública puede ser estática (reservada) o dinámica. Sin embargo, solo se pueden asignar direcciones IP públicas dinámica a una interfaz de red para obtener una dirección IP pública por máquina virtual ahora. |
|Extremos| Es necesario configurar los extremos de entrada en una máquina virtual para abrir la conectividad para determinados puertos. Uno de los modos comunes de conectarse a las máquinas virtuales es mediante la configuración de extremos de entrada. | Las reglas de entrada NAT se puede configurar en equilibradores de carga para lograr la misma capacidad de habilitar los extremos en puertos específicos para conectarse a las máquinas virtuales. |
|Nombre DNS| Un servicio en la nube obtendría un nombre de DNS único global implícito. Por ejemplo: `mycoffeeshop.cloudapp.net`. | Los nombres DNS son parámetros opcionales que se pueden especificar en un recurso de dirección IP pública. El nombre de dominio completo tendrá el siguiente formato: `<domainlabel>.<region>.cloudapp.azure.com`. |
|Interfaces de red | La interfaz de red principal y secundaria y sus propiedades se definieron como configuración de red de una máquina virtual. | La interfaz de red es un recurso expuesto por el proveedor de Microsoft.Network. El ciclo de vida de la interfaz de red no está asociado a una máquina virtual. |

## Introducción a las plantillas de Azure para máquinas virtuales

Puede empezar con las plantillas de Azure aprovechando las distintas herramientas que tenemos para desarrollar e implementar la plataforma.

### Portal de Azure

El Portal de Azure seguirá teniendo la opción de implementar máquinas virtuales con el modelo de implementación clásica y máquinas virtuales con el modelo de implementación del Administrador de recursos simultáneamente. El Portal de Azure también permitirá las implementaciones de plantillas personalizadas.

### Azure PowerShell

Azure PowerShell tendrá dos modos de implementación: el modo **AzureServiceManagement** y el modo **AzureResourceManager**. El modo AzureResourceManager contendrá también los cmdlets para administrar máquinas virtuales, redes virtuales y cuentas de almacenamiento. Puede leer más sobre este tema [aquí](../powershell-azure-resource-manager.md).

### CLI de Azure

La interfaz de línea de comandos (CLI de Azure) de Azure tendrá dos modos de implementación: el modo **AzureServiceManagement** y el modo **AzureResourceManager**. El modo AzureResourceManager ahora también contiene comandos para administrar máquinas virtuales, redes virtuales y cuentas de almacenamiento. Puede leer más sobre este tema [aquí](xplat-cli-azure-resource-manager.md).

### Visual Studio

Con la última versión del SDK de Azure para Visual Studio, puede crear e implementar máquinas virtuales y aplicaciones complejas desde Visual Studio. Visual Studio ofrece la capacidad de implementar desde una lista de plantillas preconfigurada o iniciar desde una plantilla vacía.

### API de REST

Puede encontrar la documentación detallada de la API de REST para los proveedores de recursos de procesos, redes y almacenamiento [aquí](https://msdn.microsoft.com/library/azure/dn790568.aspx).

## Preguntas más frecuentes

**¿Puedo crear una máquina virtual mediante el nuevo Administrador de recursos de Azure para implementar en una red virtual o en una cuenta de almacenamiento creada mediante las API de Administración de servicios de Azure?**

Esto no se admite en este momento. No se puede implementar mediante las nuevas API del Administrador de recursos de Azure para implementar una máquina virtual en una red virtual que creó con las API de administración de servicios.

**¿Puedo crear una máquina virtual mediante las nuevas API de Administrador de recursos de Azure desde una imagen de usuario que se creó utilizando las API de administración de servicios de Azure?**

Esto no se admite en este momento. Sin embargo, puede copiar los archivos VHD de una cuenta de almacenamiento que se creó mediante las API de administración de servicios y copiarlos en una cuenta nueva creada mediante el uso de las nuevas API del Administrador de recursos de Azure.

**¿Cuál es el impacto en la cuota de mi suscripción?**

Las cuotas de las máquinas virtuales, redes virtuales y cuentas de almacenamiento creadas mediante las nuevas API del Administrador de recursos de Azure son independientes de las cuotas que tiene actualmente. Cada suscripción obtiene nuevas cuotas para crear los recursos mediante las nuevas API. Puede leer más acerca de las cuotas adicionales [aquí](../azure-subscription-service-limits.md).

**¿Puedo continuar y usar mis scripts automatizados para aprovisionar máquinas virtuales, redes virtuales, cuentas de almacenamiento, etc. a través de las nuevas API de administrador de recursos de Azure?**

Toda la automatización y los scripts que ha creado continuarán funcionando para las máquinas virtuales existentes, las redes virtuales se crean en el modo de administración de servicios de Azure. Sin embargo, los scripts deben actualizarse para utilizar el nuevo esquema para crear los mismos recursos a través del nuevo modo Administrador de recursos de Azure. Obtenga más información acerca de cómo modificar los [scripts de CLI de Azure](xplat-cli-azure-manage-vm-asm-arm.md).

**¿Las redes virtuales creadas mediante las nuevas API del Administrador de recursos de Azure se pueden conectar a mi circuito de Express Route?**

Esto no se admite en este momento. No se pueden conectar las redes virtuales creadas mediante las nuevas API del Administrador de recursos de Azure con un circuito Route Express. Se admitirán en el futuro.

**¿Dónde puedo encontrar ejemplos de plantillas del Administrador de recursos de Azure?**

Puede encontrar un conjunto completo de plantillas de inicio en [Plantillas de inicio rápido del Administrador de recursos de Azure](https://azure.microsoft.com/documentation/templates/).

<!---HONumber=AcomDC_0128_2016-->