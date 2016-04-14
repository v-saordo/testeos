<properties
   pageTitle="Deploy Anywhere con Service Fabric de Azure en Windows Server y Linux | Microsoft Azure"
   description="Los clústeres de Service Fabric se ejecutarán en Windows Server y Linux, lo que significa que podrá implementar y hospedar aplicaciones de Service Fabric en cualquier lugar donde sea posible ejecutar Windows Server y Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="Chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/10/2016"
   ms.author="chackdan"/>

# Deploy Anywhere en Windows Server o Linux con Service Fabric
Con la incorporación de "Deploy Anywhere", Service Fabric de Azure permitirá la creación de clústeres de Service Fabric en cualquier máquina virtual o en equipos que ejecuten Windows Server o Linux. Esto significa que podrá implementar y ejecutar aplicaciones de Service Fabric en cualquier entorno donde haya un conjunto de equipos de Windows Server o Linux que estén conectados entre sí, ya sea de manera local o con algún proveedor de nube.

 Service Fabric ofrece un paquete de instalación para crear clústeres de Service Fabric. Un beneficio clave de la característica Deploy Anywhere es que no se producen bloqueos de proveedor cuando se compila una aplicación con Service Fabric, ya que se puede elegir dónde se ejecutan esas aplicaciones. Esta característica también aumenta su potencial para llegar a una base de clientes más amplia, dado que los clientes pueden tener diferentes requisitos para los entornos en los que desean ejecutar las aplicaciones. Por ejemplo, los clientes del sector sanitario o financiero pueden tener diferentes necesidades que los clientes del sector automovilístico o de viajes.

Se espera que la vista previa técnica de esta característica se lance en el primer trimestre de 2016.

## Sistemas operativos compatibles
Podrá crear clústeres en máquinas virtuales o equipos que ejecuten estos sistemas operativos: * Windows Server 2012 * Windows Server 2012 R2 * Windows Server 2016 * Linux

## Lenguajes de programación admitidos
Podrá escribir aplicaciones de Service Fabric en cualquiera de estos lenguajes de programación: * C# * Java

## Configuración y creación de clústeres
Service Fabric proporcionará un paquete de instalación que se puede descargar. Una vez que haya descargado este paquete, deberá realizar cambios en un archivo de configuración para especificar la configuración para el clúster. Una vez que se haya modificado la configuración del clúster, se ejecutará un script de instalación que creará el clúster abarcando las máquinas que especificó en la configuración del clúster.

Cuando lancemos la versión preliminar de esta funcionalidad en el primer trimestre de 2016, proporcionaremos los detalles exactos del proceso de instalación.

## Implementaciones en la nube frente a implementaciones locales
El proceso de creación de un clúster local de Service Fabric será similar al proceso de creación de un clúster en cualquier nube que elija donde cuente con un conjunto de máquinas virtuales. Los pasos iniciales para aprovisionar las máquinas virtuales se regirán por el entorno de nube o local que esté utilizando. Cuando tenga un conjunto de máquinas virtuales con la conectividad de red habilitada entre ellas, los pasos para configurar el paquete de Service Fabric, editar la configuración del clúster y ejecutar la creación del clúster y los scripts de administración serán similares. Esto garantiza que sus conocimientos y su experiencia con respecto al uso y la administración de los clústeres de Service Fabric se puedan aplicar si decide cambiar a un nuevo entorno de hospedaje.

## Ventajas de la característica Deploy Anywhere
* Dado que ningún proveedor está bloqueado, puede elegir dónde se ejecutan sus aplicaciones.
* Las aplicaciones de Service Fabric, una vez escritas, se pueden ejecutar en varios entornos de hospedaje con pocos cambios (o ninguno).
* El proceso de creación de aplicaciones de Service Fabric se aplica de una plataforma de hospedaje a otras.
* La experiencia operativa que se adquiere al ejecutar clústeres de Service Fabric se puede aplicar de un entorno de implementación a otros.
* Existe una amplia cobertura de clientes sin limitaciones derivadas de las restricciones del entorno de hospedaje.
* Hay un nivel adicional de confiabilidad y protección contra interrupciones generalizadas al poder mover los servicios a otro entorno de implementación en caso de que un centro de datos o un proveedor de nube sufran una interrupción.

## Ventajas de clústeres administrados de Service Fabric en Azure frente a clústeres de Service Fabric creados y administrados mediante Deploy Anywhere
La ejecución de clústeres de Service Fabric en Azure proporciona algunas ventajas frente al uso de la opción Deploy Anywhere; por tanto, si no tiene necesidades específicas en cuanto al lugar donde se ejecutan los clústeres, le sugerimos que los ejecute en Azure. En Azure se proporciona integración con otras características y servicios de Azure, lo que facilita las operaciones y la administración del clúster.

* **Portal de Azure:** el Portal de Azure facilita la creación y la administración de clústeres.
* **Administrador de recursos de Azure:** el uso del Administrador de recursos de Azure permite una administración sencilla de todos los recursos que usa el clúster como una unidad y simplifica el costo del seguimiento y la facturación.
* El clúster de Service Fabric de los **recursos de Azure** es un recurso ARM, por tanto, puede ajustarlo como hace con otros recursos ARM en Azure.
* **Diagnósticos:** en Azure se proporciona la integración con Diagnósticos y Visión operativa de Azure.
* **Escalado automático:** para los clústeres hospedados en Azure, proporcionamos una funcionalidad integrada de escalado automático. En otros entornos en los que se usa la característica Deploy Anywhere, hay que crear una característica propia de escalado automático o realizar el escalado manualmente mediante las API que ofrece Service Fabric para escalar los clústeres.

<!---HONumber=AcomDC_0224_2016-->