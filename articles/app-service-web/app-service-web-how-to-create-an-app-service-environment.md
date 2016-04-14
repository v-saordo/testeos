<properties 
	pageTitle="Creación de un entorno del Servicio de aplicaciones" 
	description="Descripción de flujo de creación de entornos de servicio de aplicación" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/14/2016" 
	ms.author="ccompy"/>

# Creación de un entorno del Servicio de aplicaciones #

Entornos del Servicio de aplicaciones es una opción del nivel Premium del Servicio de aplicaciones de Azure que ofrece una mejor funcionalidad de configuración que no está disponible en las marcas de varios inquilinos. La característica de ASE esencialmente implementa el Servicio de aplicaciones de Azure en la red virtual de un cliente. Para comprender mejor las capacidades ofrecidas por los entornos del Servicio de aplicaciones lea la [¿Qué es un entorno del Servicio de aplicaciones][WhatisASE].

### Información general ###

La creación de un ASE requiere que los clientes proporcionen la siguiente información:

- Nombre del ASE
- Suscripción que se usará para el ASE  
- resource group
- Selección de la red virtual de Azure junto con una subred
- Definición del grupo de recursos de ASE

Existen algunos detalles importantes para cada uno de esos elementos.
- El nombre del ASE se utilizará en el subdominio para cualquier aplicación realizada en ese ASE.
- Todas las aplicaciones que se creen en un ASE estarán en la misma suscripción en la que se encuentre el ASE.
- Si no tiene acceso a la suscripción utilizada para crear el ASE, no podrá utilizarlo para crear aplicaciones.
- Las redes virtuales que se utilizan para hospedar un ASE deben ser redes virtuales clásicas y regionales del tipo "v1". 
- La subred utilizada para hospedar el ASE no debe contener otros recursos informáticos.
- En una subred solo puede haber un ASE.

Cada implementación de ASE es un servicio hospedado que Azure administra y mantiene. Los recursos de proceso que hospedan los roles del sistema ASE no son accesibles para el cliente aunque este administre la cantidad de instancias y sus tamaños.

Hay dos maneras de obtener acceso a la IU de creación de ASE. Puede encontrarlo buscando ***entorno del Servicio de aplicaciones*** en Azure Marketplace o a través de Nuevo -> Web + Móvil.

Si desea que la red virtual tenga un grupo de recursos independiente del ASE, primero debe crear la red virtual por separado y luego seleccionarla durante la creación del ASE. Además, si desea crear una subred en una red virtual existente durante la creación dell ASE, el ASE debe estar entonces en el mismo grupo de recursos que la red virtual.

### Creación rápida ###
La experiencia de creación de un ASE tiene un conjunto de valores predeterminados para permitir una experiencia de creación rápida. Puede crear rápidamente un ASE con solo introducir el nombre de la implementación. Esto a su vez crea un ASE en la región más cercana a la suya con una:

- Red virtual con 512 direcciones 
- Subnet con 256 direcciones
- Grupo de servidores front-end con recursos de proceso 2 P2
- Grupo de trabajo con recursos de proceso 2 P1
- Una sola dirección IP que se usará para SSL de IP

Los grupos de servidores front-end requieren P2 o superior. Asegúrese de seleccionar la suscripción en la que desea que esté el ASE. Las únicas cuentas que puede usar el ASE para hospedar contenido deben encontrarse en la suscripción utilizada para crearlo.

![][1]

El nombre especificado para el ASE se usará en las aplicaciones creadas en él. Si el nombre del ASE es appsvcenvdemo, el nombre de dominio será .*appsvcenvdemo.p.azurewebsites.net*. Por tanto, si creó una aplicación llamada *mytestapp*, se podría obtener acceso a ella en *mytestapp.appsvcenvdemo.p.azurewebsites.net*. No puede usar espacios en blanco en el nombre del ASE. Si utiliza caracteres en mayúsculas en el nombre, el nombre de dominio será la versión en minúsculas total de ese nombre.

Contar con los valores predeterminados resulta muy útil en varias situaciones, pero con frecuencia necesitará algunos ajustes. En las siguientes secciones se examina cada una de las configuraciones relativas al ASE.

### Red virtual ###
Aunque existe la capacidad de creación rápida que creará automáticamente una nueva red virtual, la característica también admite la selección de una red virtual existente y la creación manual de una red virtual. Puede seleccionar una red virtual existente (por el momento solo se admiten redes virtuales "v1") si es lo suficientemente grande como para admitir la implementación de un entorno del Servicio de aplicaciones. La red virtual debe tener 8 direcciones o más.

Si selecciona una red virtual que ya existía también tendrá que especificar una subred para usar o crear una nuevo. La subred debe tener 8 direcciones o más y no puede contener ningún otro recurso. La creación de ASE producirá un error si intenta usar una subred que ya tiene asignadas máquinas virtuales.

Si usa la IU de creación de red virtual, tiene que proporcionar lo siguiente:

- Nombre de red virtual
- Intervalo de direcciones de red virtual en la notación CIDR
- Ubicación

La ubicación de la red virtual es la ubicación del ASE porque este se implementa en esa red virtual.

Después de especificar o seleccionar la red virtual, debe crear o seleccionar una subred, según corresponda. Aquí debe proporcionar los detalles siguientes:
- Nombre de subred
- Intervalo de subred en la notación CIDR

Si no está familiarizado con CIDR (Enrutamiento de interdominios sin clases), adopta la forma de una dirección IP que está separada del valor de CIDR por una barra diagonal. Es como esta *10.0.0.0/22*. El valor CIDR indica el número de bits de referencia que se enmascaran en la dirección IP mostrada. Una manera más fácil de expresar el concepto aquí es que los valores CIDR proporcionan un intervalo de IP. En este ejemplo, /10.0.0.0/22 significa un intervalo de 1024 direcciones o entre 10.0.0.0 y 10.0.3.255. /23 significa 512 direcciones, y así sucesivamente.

No olvide que si desea crear una subred en una red virtual existente, el ASE estará en el mismo grupo de recursos que la red virtual. Para mantener el ASE en un grupo de recursos independiente de la red virtual, simplemente cree la red virtual y la subred por separado y con anterioridad a la creación del ASE.

![][2]


### Grupos de recursos de proceso ###

Durante la creación del ASE, puede establecer el número de recursos de cada grupo de recursos junto con su tamaño. Aunque los tamaños de los grupos de recursos se pueden establecer durante la creación del ASE, también puede ajustarlos después con las opciones de escalado manual o automática.

Como se ha indicado anteriormente, un ASE consta de servidores front-end y trabajos. Los servidores front-end manejan la carga de conexiones de la aplicación y los trabajos ejecutan el código de aplicación. Los servidores front-end se administran en un grupo de recursos de proceso dedicado. A su vez, los trabajos se administran en 3 grupos de recursos de proceso independientes llamados

- Grupo de trabajo 1
- Grupo de trabajo 2
- Grupo de trabajo 3

Si tiene un gran número de solicitudes de aplicaciones web sencillas, lo más probable es que escalara verticalmente sus servidores front-end y quizás tuviera menos trabajos. Si tuviera aplicaciones web de uso intensivo de CPU o de memoria con tráfico ligero, entonces no necesitaría muchos servidores front-end, pero es probable que necesitara más trabajos o trabajos más grandes.

![][3]

Independientemente del tamaño de los recursos de proceso, el espacio mínimo tiene 2 servidores front-end y 2 trabajos. Un ASE se puede configurar para usar hasta 55 recursos de proceso en total. De esos 55 recursos de proceso, solamente 50 se pueden usar para hospedar cargas de trabajo. El motivo de esto es doble. Hay un mínimo de 2 recursos de proceso front-end. Esto deja hasta 53 para admitir asignación de grupos de trabajo. Para proporcionar tolerancia a errores, necesita tener un recurso de proceso adicional asignado según las reglas siguientes:

- cada grupo de trabajo necesita al menos un recurso de proceso adicional que no se pueden asignar a cargas de trabajo
- cuando la cantidad de recursos de proceso de un grupo supera un determinado valor, se necesita otro recurso de proceso

Dentro de cualquier grupo de trabajo los requisitos de tolerancia a errores son que para un determinado valor de X recursos asignados a un grupo de trabajo:

- Si el valor de X está comprendido entre 2 y 20, la cantidad de recursos de proceso utilizables que puede usar para cargas de trabajo es X-1
- Si el valor de X está comprendido entre 21 y 40, la cantidad de recursos de proceso utilizables que puede usar para cargas de trabajo es X-2
- Si el valor de X está comprendido entre 41 y 53, la cantidad de recursos de proceso utilizables que puede usar para cargas de trabajo es X-3

Además de ser capaz de administrar la cantidad de recursos de proceso que se pueden asignar a un grupo determinado, también tiene control sobre el tamaño. Con entornos del Servicio de aplicaciones puede elegir 4 tamaños diferentes (etiqueta P1 a P4). Para obtener detalles sobre los tamaños y sus precios, vea [Precios de Servicio de aplicaciones][AppServicePricing]. Los tamaños de los recursos de proceso P1 a P3 son los mismos que los que están disponibles en los entornos multiempresa. El recurso de proceso P4 proporciona 8 núcleos con 14 GB de RAM y solamente está disponible en un entorno del Servicio de aplicaciones.

El precio de los entornos del Servicio de aplicaciones está en contra de los recursos de proceso asignados. Se paga por los recursos de proceso asignados a su entorno del Servicio de aplicaciones independientemente de si hospedan cargas de trabajo o no.

De forma predeterminada, un ASE viene con 1 dirección IP que está disponible para SSL de IP. Si sabe que va a necesitar más puede especificarlo aquí o administrarlo después de la creación.
  
### Después de la creación del entorno del Servicio de aplicaciones ###

Después de la creación de ASE puede ajustar:

- La cantidad de servidores front-end (mínimo: 2)
- La cantidad de trabajos (mínimo: 2)
- Cantidad de direcciones IP disponibles para SSL de IP
- Los tamaños de recursos de proceso usados por los servidores front-end o los trabajos (el tamaño mínimo de front-end es P2)

No puede cambiar:

- La ubicación
- La suscripción
- El grupos de recursos
- La red virtual usada
- La subred usada

Aquí encontrará más detalles sobre el escalado manual, la administración y la supervisión de entornos del Servicio de aplicaciones: [Configuración de un entorno del Servicio de aplicaciones][ASEConfig].

Para obtener información sobre el escalado automático, puede encontrar aquí una guía: [Configuración del escalado automático para un entorno del Servicio de aplicaciones][ASEAutoscale].

Existen dependencias adicionales que no están disponibles para personalización, como la base de datos y el almacenamiento. Estas son controlados por Azure y se incluyen con el sistema. El almacenamiento del sistema admite hasta 500 GB para todo el entorno del Servicio de aplicaciones, y la base de datos se ajusta con Azure según sea necesario por medio de la escala del sistema.


## Introducción

Para empezar a trabajar con entornos del Servicio de aplicaciones, vea [Introducción a los entornos del Servicio de aplicaciones][WhatisASE].

Para obtener más información acerca de la plataforma de Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png
[3]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-resources.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/

<!---HONumber=AcomDC_0302_2016-->