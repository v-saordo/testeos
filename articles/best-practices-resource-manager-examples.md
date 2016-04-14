<properties
	pageTitle="Ejemplos contextuales de procedimientos recomendados para la implementación de plantillas"
	description="Muestra ejemplos de plantillas del Administrador de recursos de Azure que aclaran los procedimientos recomendados."
	services="azure-resource-manager"
	documentationCenter=""
	authors="mmercuri"
	manager="georgem"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="mmercuri"/>

# Ejemplos contextuales de procedimientos recomendados para la implementación de plantillas

En este tema se ofrecen siete ejemplos contextuales de cómo implementar las plantillas del Administrador de recursos de Azure. Para obtener información general sobre los principios que se ilustran en estos ejemplos, vea [Procedimientos recomendados para diseñar plantillas del Administrador de recursos de Azure](best-practices-resource-manager-design-templates.md).

Este tema forma parte de un artículo más extenso. Si desea leer el artículo completo, descargue [World Class ARM Templates Considerations and Proven Practices] (Consideraciones y prácticas comprobadas sobre plantillas ARM de clase mundial) (http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).

## Migración de una plantilla con ámbito de funcionalidad a una plantilla con ámbito de solución completa

El patrón para desarrollar una plantilla con ámbito de funcionalidad se indicó anteriormente. Una duda que puede plantearse es si debe usar esta plantilla con ámbito de funcionalidad sola o como parte de una plantilla con ámbito de solución completa.

Por ejemplo, si hubiera una plantilla centrada en la tecnología que implementase SQL Server como funcionalidad, cuáles serían, de existir, las consideraciones sobre su uso de forma independiente o como parte de una plantilla más amplia con ámbito de solución completa que use SQL Server para admitir una aplicación web.

Al observar este escenario, conviene fijarse en el número de recursos probablemente implicados. Para una implementación sólida, la plantilla con ámbito de funcionalidad no será únicamente una cuenta de almacenamiento y una sola máquina virtual con una instalación de SQL Server. Una sólida plantilla con ámbito de funcionalidad va a implementar varias máquinas virtuales con implementación de SQL Server con alta disponibilidad. En el caso de algunas capacidades, como Analysis Services, es probable que la topología también implemente Active Directory con ella.

Dos importantes consideraciones en este escenario incluyen el ciclo de vida del uso de SQL Server y el RBAC que quiera aplicarle. En concreto, el servidor SQL Server se actualiza y se elimina con el resto de la solución o su ciclo de vida variará con respecto la solución o a otras partes de la solución. Si el ciclo de vida varía, convendrá considerar la posibilidad de colocarlo en otro grupo de recursos.

Otro aspecto que debe contemplarse es cómo le gustaría aplicar un RBAC a la plantilla de solución con ámbito de funcionalidad SQL Server. Según cómo quiera aplicar el RBAC dentro de la topología, puede optar por distintos grupos de recursos que se basen en la alineación con esos detalles. Puede aplicar el RBAC en el nivel de recursos, pero dado el número de recursos de la plantilla de solución con ámbito de funcionalidad SQL Server, debería considerar la posibilidad de tener un grupo de recursos diferenciado al que se aplique el RBAC.

Otro aspecto que debe contemplarse es una evaluación de la plantilla de solución con ámbito de funcionalidad SQL Server para identificar si actualmente crea determinados recursos por sí misma o le permite que "traiga sus propios recursos". En un modelo de tipo "traiga sus propios recursos" (BYOR), la plantilla de solución con ámbito de funcionalidad permitiría que su plantilla volviera a usar recursos existentes previamente, de los que una cuenta de almacenamiento, una red virtual o un conjunto de disponibilidad serían ejemplos típicos. Si no existe un enfoque BYOR en la plantilla con ámbito de funcionalidad, puede modificarla mediante el enfoque descrito anteriormente en este documento para plantillas de recursos opcionales. En este caso, la plantilla con ámbito de solución completa tendría una plantilla de recursos compartidos con estos recursos comunes, y la plantilla con ámbito de funcionalidad se ampliaría para admitir estos recursos de forma opcional. Así se crea una mejor plantilla de solución con ámbito de funcionalidad ya que ahora puede usarse independientemente o como parte de una composición.

Al evaluar si la cuenta de almacenamiento debe transferirse desde la plantilla con ámbito de solución completa, también debe volver a evaluarse el RBAC. En concreto, ¿tiene que asegurarse de que se aplique RBAC a este recurso en particular? En tal caso, si se espera que el recurso lo tenga aplicado cuando se transfiere, se coloca un nivel de confianza no solo en el bloque de la solución, sino en cualquier usuario que opcionalmente quiera proporcionarlo a la plantilla con ámbito de funcionalidad cuando se usa de forma independiente. Si el RBAC es crítico, debe plantearse si convertirlo en una plantilla opcional que se incluye en la plantilla de solución con ámbito de funcionalidad o exigir su creación con el RBAC necesario desde la plantilla de solución con ámbito de funcionalidad.

Si se decide colocarlos en distintos grupos de recursos, también puede usar vínculos de recursos para definir las relaciones entre los recursos, aunque estos abarquen varios grupos de recursos.

## Creación de una plantilla con ámbito de solución completa que incluya varias plantillas con ámbito de funcionalidad

Se trata en gran medida de un superconjunto del ejemplo anterior. En este escenario, una organización tiene varias plantillas de solución con ámbito de funcionalidad para un conjunto de tecnologías de datos, por ejemplo, Kafka, Hadoop de Apache, Apache Spark y Apache Storm, que quiere reunir en un bloque de una solución. La composición resultante usará esas plantillas de solución con ámbito de funcionalidad, así como un almacenamiento compartido y una red virtual con asignaciones de subred específicas.

Aparte de las plantillas con ámbito de funcionalidad, se necesitarán recursos adicionales para la solución, aunque solo sean scripts para reunir las plantillas con ámbito de funcionalidad y configurarlas.

En este caso, se identifica que hay una red virtual compartida y una cuenta de almacenamiento compartido. Para dar cabida a esto, debe agregarlos a una plantilla de recursos compartidos en la plantilla con ámbito de solución completa y asegurarse de que se admite un enfoque de tipo "traiga sus propios recursos" en las plantillas con ámbito de funcionalidad. Si no es así, puede modificar las plantillas con ámbito de funcionalidad para que le den cabida, tal como se describe en el ejemplo anterior.

En el caso de los recursos adicionales que va a agregar, seguirá un superconjunto del patrón empleado para crear una plantilla con ámbito de funcionalidad individual. En este caso, agregará una plantilla de recursos compartidos, plantillas de recursos opcionales, plantillas de nodo de miembro y la configuración de estado deseado (scripts, Chef, Puppet, DSC de Powershell) para los nuevos recursos. Si hay dependencias, podrá optimizarlas para que se usen referencias implícitas en lugar de dependsOn siempre que sea posible para eliminar la posibilidad de que dependencias extraviadas puedan afectar al paralelismo (y la velocidad) de la implementación. También deberá tener en cuenta el ciclo de vida de estos recursos, las consideraciones de RBAC y las dependencias para determinar si se deben colocar en distintos grupos de recursos.

Al agregar recursos compartidos, como la cuenta de almacenamiento compartido, también debe evaluar si es necesario aplicar un bloqueo de recurso, ya que esto puede ayudar a evitar eliminaciones accidentales.

Cuando se agregan nuevos recursos, debe examinar también si algunos de los recursos que se agregan a la plantilla con ámbito de solución completa podrían aislarse como plantillas con ámbito de funcionalidad independientes. En tal caso, debe tener en cuenta que esto promueve un mayor nivel de descomposición que puede suponer ventajas tanto en caso de reutilización como de pruebas.

En cuanto a la integración en los bloques de la solución, los siguientes aspectos que debe contemplar son, tal como se mencionó en el ejemplo anterior, identificar si los ciclos de vida de las plantillas de solución con ámbito de funcionalidad son diferentes a los de la solución más amplia y si los requisitos de RBAC tendrían que distribuirlos en grupos de recursos independientes.

Por último, conviene que tenga en cuenta si quiere tener la posibilidad de definir y consultar los vínculos entre los recursos. Si lo hace, el empleo de vínculos de recursos le permitirá hacerlo en toda la plantilla con ámbito de solución completa, aunque se abarquen varios grupos de recursos.

## Creación de una plantilla con ámbito de solución completa con activación/desactivación parcial del patrón

Este escenario es una variante del anterior. En este caso, el cliente extrae datos de un sistema local a intervalos fijos en el transcurso de un día. Dispone de una canalización de datos para procesar los datos entrantes y de un almacén de datos relacionales donde los datos siempre están disponibles para las consultas. Como la nube es un modelo de pago por uso, al cliente le gustaría tener la canalización de datos operativa solo durante los intervalos en que los datos se presentan para su procesamiento.

Como parte de la canalización de datos, tiene un servidor SQL Server, que recibe los datos procesados y hace que estén disponibles para las consultas. El cliente ha indicado que aunque le gustaría activar y desactivar partes de la ingesta y el procesamiento de la canalización según una programación fija, le gustaría tener siempre disponible el servidor SQL Server.

En este escenario, hay lo que parecen ser diferencias explícitas en el ciclo de vida y, potencialmente, algunas consideraciones adicionales que el cliente no planteó pero que deben evaluarse.

Tal como se describe, la implementación de SQL Server se mantendrá conectada mientras otros recursos se crean y se eliminan. Inicialmente se van a implementar juntos, pero después otros miembros de la plantilla se destruirán y se crearán en otro ciclo de vida. Estos se pueden aislar en distintos grupos de recursos o dejarse en el mismo grupo aplicando un bloqueo de recurso a los recursos de SQL Server. Dada la naturaleza específica de SQL Server, tal como se describe en los ejemplos anteriores, es probable que se represente como un conjunto mayor de recursos, sería adecuado separarlo en su propio grupo de recursos.

El otro aspecto que hay que plantearse es que, aunque el cliente indicó que quiere que el resto de la canalización de datos se active y desactive según una programación, tal vez no tenga en cuenta el comportamiento variable de los sistemas de generación de informes. La entrega programada de datos procedentes de terceros no siempre es precisa: puede que la conectividad no esté disponible durante un período de tiempo, que los relojes de los servidores locales o basados en la nube varíen, que los cambios de horario no se produzcan según lo previsto, etc. Se debe evaluar si también se debe usar el mecanismo de ingesta en un patrón de activación/desactivación y, si es así, si su ciclo de vida es mayor que el de los componentes de procesamiento.

Si usa un servicio administrado como el Centro de eventos o la Factoría de datos de Azure, esto no importa porque sus modelos operativos y el enfoque de facturación asociado hace que estén fácilmente disponibles para la ingesta de los datos y su colocación en el almacenamiento. Si usa otra tecnología, como Kafka, que implementó en una máquina virtual, puede que quiera consultar el ciclo de vida para saber cómo hacer que dicha tecnología y las cuentas de almacenamiento asociadas necesarias para la ingesta estén disponibles. Esto puede traducirse en que los recursos de ingesta y procesamiento se coloquen en grupos de recursos distintos en función de su ciclo de vida.

## Compatibilidad con entornos diferenciados dentro de una suscripción

Para entregar eficazmente los servicios, muchas organizaciones tienen un conjunto de necesidades de escala, aislamiento de facturación, aislamiento de responsabilidad y aislamiento de zona geográfica que deben cumplirse. Al diseñar servicios en Azure, tradicionalmente habrían usado particiones de suscripción en su enfoque para satisfacer estas necesidades.

El Administrador de recursos suaviza las restricciones sobre el número de recursos de un tipo determinado que se pueden implementar en una suscripción y también introduce grupos de recursos, RBAC y auditoría. La combinación de estos elementos puede permitir que las organizaciones usen grupos de recursos para las particiones, lo que les permite cumplir sus requisitos y reducir la cantidad, si existe, de particiones de suscripción que tienen que hacer.

En esta sección se analizan los requisitos observados en estos tipos de entornos y se ofrecen instrucciones sobre cómo entregar los entornos que les satisfacen con ARM.

### Consideraciones sobre el aislamiento

En esta sección se exploran con más detalle las motivaciones comunes de los clientes para el aislamiento de entorno, de facturación y de zona geográfica.

#### Aislamiento de entorno

Los propietarios de servicio desean aislar sus distintos entornos. Tener cada entorno aislado permite a los equipos la posibilidad de tener un mayor control sobre quién puede tener acceso a los entornos. Mientras que los entornos de desarrollo pueden estar más abiertos en cuanto a quién tiene acceso a ellos, a medida que el ámbito del entorno se acerca a producción, el número de usuarios (ya sean personas o cuentas de sistema empleadas para automatización) se reduce para facilitar el cumplimiento normativo y minimizar el riesgo general.

#### Aislamiento de facturación: desarrollo frente a ejecución de un servicio

Para reflejar con precisión el costo de productos vendidos (COGS) y los gastos de explotación (OpEx), los propietarios de empresas desean poder separar el costo de la investigación y generación del servicio frente a la ejecución de los servicios.

Un superconjunto de aislamiento de entorno mencionado anteriormente, el propósito sería la consolidación de desarrollo y pruebas para facturación individual o agregada en el primer caso mientras que la producción permanecería independiente en el último.

#### Aislamiento de facturación: incorporación de transparencia y responsabilidad a los costos de consumo de servicio

El aislamiento de facturación también sirve para aumentar la transparencia en los costos relacionados con el consumo de la plataforma que hacen equipos determinados e introducir los niveles adecuados de responsabilidad.

Aunque la nube es elástica y permite un modelo de pago por uso, es menos conocida para algunos desarrolladores procedentes de un modelo ajeno a la nube donde el hardware se adquiere y se posee. En el modelo ajeno a la nube no había limitaciones físicas en cuanto al número de "máquinas" que podían activarse y había escasos incentivos para reducir verticalmente o desactivar los recursos que no estuvieran en uso. La adquisición de este hardware dedicado, en muchos casos, no la hacían los desarrolladores que lo usaban.

Al aislar las suscripciones y asignar la responsabilidad de las suscripciones a equipos específicos, los propietarios de servicios encuentran este tipo de particiones de la suscripción beneficioso en el impulso y la aplicación de comportamientos deseados.

#### Aislamiento controlado por zonas geográficas: implementaciones específicas de una zona geográfica y reguladas por sus leyes

En ciertos contextos, habrá requisitos que los servicios destinados a una zona geográfica determinada deben tener en cuenta para saber cómo realizar la implementación con el fin de abordar consideraciones de cumplimiento normativo.

Mientras que un servicio puede ser de carácter global, las implementaciones que residen en ciertas zonas geográficas o prestan servicio en ellas pueden estar reguladas por requisitos de personal operativos. En concreto, tener solo personas que sean ciudadanos de un país o un conjunto de países específico y superen determinados procesos de filtrado en segundo plano para operar dichos servicios.

El aislamiento de zonas geográficas también ofrece ventajas en cuanto al aprovechamiento de los nuevos servicios y capacidades de la plataforma. Es posible que algunas ubicaciones geográficas como, por ejemplo, China, solo puedan tener un subconjunto de los servicios de la plataforma disponibles o se retrase la implementación de servicios de la plataforma.

El aislamiento de zonas geográficas permite a los equipos la posibilidad de desarrollar sus servicios de manera que aprovechen los servicios y capacidades nuevos o mejorados de la plataforma donde están disponibles.

### Consideraciones de cumplimiento normativo

Los servicios se pueden entregar en varias regiones geográficas y varios mercados verticales. Estas audiencias suelen tener datos confidenciales o procesos incluidos en sus aplicaciones y existen normativas de cumplimiento asociadas, diseñadas tanto para protegerlos como para auditar su compromiso con ellas.

#### Separación de roles y tareas

La separación de roles y tareas es un requisito clave para que los servicios internos cumplan con las directivas internas. Muchos servicios comerciales requieren también esta opción para mantener el cumplimiento con los gobiernos y las directrices normativas del sector. Los servicios tienen que limitar el acceso a los servicios y sus recursos subyacentes a los roles autorizados en determinadas circunstancias. Muchos servicios han incorporado scaffolding para ofrecer dos capacidades: RBAC y auditoría.

#### Casos de uso de control de acceso basado en rol (RBAC)

En escenarios de cumplimiento, es importante restringir el acceso a determinados recursos.

Por ejemplo, al examinar datos confidenciales en varios escenarios donde el cumplimiento normativo es relevante (información de estado, datos financieros, registros de impuestos, etc.), es importante limitar el número de personas que pueden tener acceso a los datos, verlos o manipularlos únicamente a quienes requieran acceso para realizar los negocios de la organización principal.

El RBAC proporciona acceso a una persona, un sistema o un grupo diferenciado a recursos específicos en las condiciones mencionadas.

#### Auditoría

Además del acceso restringido que RBAC proporciona, las organizaciones también deben auditar el acceso a los recursos y la interacción con los recursos.

### Implementación con el Administrador de recursos de Azure

Antes, las organizaciones habrían usado particiones de la suscripción para lograr estos objetivos. Aunque era posible, no era lo ideal. Como la creación de una suscripción es en efecto una actividad comercial, la API de administración de servicios no exponía un mecanismo por el cual crear o eliminar automáticamente nuevas suscripciones y las suscripciones tenían que crearse manualmente. El número de suscripciones resultante podía crecer considerablemente, en el caso de servicios muy grandes como los propios servicios comerciales de Microsoft, ese número podía abarcar más de mil suscripciones. Esto se traducía a menudo en la creación de scaffolding personalizado para crear y administrar suscripciones en una organización.

Con el Administrador de recursos, la implementación de varios entornos dentro de una suscripción es mucho más sencilla. Modera los anteriores límites fijos en recursos que había en el modelo anterior, lo que reduce considerablemente la necesidad de crear particiones por restricciones de recursos.

Los entornos se pueden colocar en grupos de recursos, a los que se puede aplicar un RBAC específico, lo que le permite proporcionar aislamiento de entorno. En escenarios donde se requiere aislamiento de zona geográfica, esto también puede lograrse mediante grupos de recursos. Como los grupos de recursos pueden abarcar varias zonas geográficas, se puede conseguir el aislamiento específico de una o más zonas geográficas.

Puede aplicar etiquetas a recursos y grupos de recursos que se pueden usar en resúmenes de facturación y vistas resumidas para proporcionar aislamiento de facturación. Puede usar etiquetas para definir el tipo de entorno (investigación, educación, desarrollo, pruebas, producción), la organización o persona responsable ("RR. HH.", "Finanzas", "Jorge Sainz", "Juana García").

El requisito de auditoría se ofrece como parte del conjunto de capacidades listas para usar del Administrador de recursos de Azure subyacente y puede verse en una ubicación central.

Los clientes finales tendrían cuentas registradas en Azure Active Directory que se usarían para la autenticación y el control de acceso basado en rol en el entorno y los recursos.

#### Optimización de densidad

Aunque los límites de recursos son menos estrictos en el Administrador de recursos de Azure, seguirá habiendo límites. Además de crear los entornos propiamente dichos, también debería tratar de lograr la densidad de los entornos de las suscripciones. Proporcionar un entorno es proporcionar capacidad a una persona u organización, y debe evaluar el tamaño de las "camisetas" que quiere entregar. En concreto, identifique las variantes entre clientes pequeños, medianos, grandes y extra grandes en cuanto a los recursos necesarios.

Puede usar diferentes suscripciones para distintos tamaños de camiseta con el fin de conseguir una mayor densidad. Por ejemplo, podría alojar 1.000 entornos de camiseta de tamaño pequeño, 500 implementaciones de tamaño mediano, 100 implementaciones de tamaño grande y 10 implementaciones de tamaño extra grande en una determinada suscripción. Como no se factura ningún costo por tener varias suscripciones, puede que quiera aislar los distintos tamaños en diferentes suscripciones para proporcionar la máxima densidad. Esto puede realizarse mientras mantiene el número de suscripciones relativamente moderado y fácil de administrar.

Un aspecto clave que debe contemplar es identificar si estaría dispuesto a permitir que los clientes aumenten o cambien el tamaño de su camiseta y cómo le gustaría ajustarlo.

Un enfoque sería permitir que un cliente pueda adquirir capacidad adicional dentro de su grupo de recursos existente. Esto puede resultar fácil de adaptar técnicamente, pero tiene implicaciones de densidad. En lugar de tamaños claramente definidos para todos los clientes, esto introduce un nivel de variabilidad que sobrecarga la optimización de la densidad. Si cada entorno de tamaño pequeño es un tamaño X, puede calcular fácilmente de antemano cuántos entornos de tamaño pequeño se colocan en una suscripción para mantener un densidad óptima. Si permite a los clientes personalizar el entorno, el resultado es un número impredecible de variantes, con cantidades de entornos que podrían ser X, X + 1, X + 2, etc. Con este nivel de variabilidad, conseguiría menos densidad de la que sería necesaria para reservar capacidad dentro de una suscripción para alojar estas variaciones.

Aunque posible, esto es poco adecuado como enfoque general, ya que logra menos densidad y exige más sobrecarga que administrar. Para entornos de mayor tamaño, esto puede ser una opción más viable. Como algunos de estos entornos grandes y extra grandes se colocarían en una suscripción, puede optar por colocar menor cantidad de estos en una suscripción para adaptarse al crecimiento.

Otro enfoque sería eliminar el entorno de tamaño actual de los clientes y crear un nuevo entorno de distinto tamaño. Aunque no es adecuado en algunos escenarios, esto funciona bien con entornos que se usan temporalmente, como los de desarrollo y de pruebas.

El siguiente enfoque más sencillo sería ofrecer al cliente la posibilidad de adquirir un entorno de mayor tamaño y que después él mismo administre la migración a ese entorno. Por ejemplo, un cliente que tuviera una implementación de SQL Server en un entorno pequeño podría adquirir un entorno mediano y se responsabilizaría individualmente de la transferencia de datos y el estado personalizado.

Otro enfoque sería proporcionar un servicio administrado donde se ajuste esta transición de un tamaño a otro. Esto es obviamente más complicado, pero según las cargas de trabajo y los clientes podría ser algo que su organización esté dispuesta a incorporar.

## Entrega de entornos con restricciones de directiva de cliente adicionales

Algunas organizaciones tienen requisitos y directivas adicionales para los entornos que implementan. En concreto, tienen directivas que restrinjan los puertos expuestos externamente y puede que tengan directivas que requieran la supervisión del tráfico entrante y saliente en el entorno. Por razones de compatibilidad y costo, puede que también se impongan restricciones sobre los recursos que un cliente final puede crear, actualizar o eliminar. En cuanto a la organización que ofrece el entorno, normalmente también requiere acceso a la suscripción para obtener soporte técnico.

Un superconjunto del escenario anterior, esto exigiría la incorporación de determinados recursos con restricciones adicionales sobre quién puede y no puede crear recursos de un tipo determinado.

La posibilidad de que un usuario cree, actualice o elimine determinados recursos puede restringirse mediante el control de acceso basado en rol. Entre otros ejemplos, se incluiría una organización que requiera una determinada red virtual y posibles subredes que el cliente final no pueda actualizar ni eliminar de la red.

Se pueden implementar bloqueos de recursos para establecer que los recursos son de solo lectura o no se pueden eliminar. Puede usarse el RBAC para permitir que los usuarios o las entidades de servicio realicen determinadas actividades en un recurso o grupo de recursos.

Si la organización requiere que cierto tráfico, por ejemplo, el tráfico entre capas de la aplicación, vaya a través de un intermediario, como un dispositivo de red virtual, deben usarse rutas definidas por el usuario.

Un dispositivo virtual no es más que una máquina virtual que ejecuta una aplicación utilizada para controlar el tráfico de red de alguna manera, como un firewall o un dispositivo NAT. Varios terceros ofrecen dispositivos de red virtual en Azure y las organizaciones pueden traer los suyos.

Un enfoque de tipo "traiga su propio dispositivo" permite que una organización reutilice el código existente que pueda usarse en sus entornos locales. La máquina virtual de este dispositivo virtual debe ser capaz de recibir el tráfico entrante que no se dirige a sí mismo. Para permitir que una máquina virtual reciba el tráfico dirigido a otros destinos, debe habilitar el reenvío IP en la máquina virtual.

Al igual que con los ejemplos anteriores, debe revisarse el ciclo de vida de los recursos y las restricciones de RBAC y considerarlos parte de la estrategia de grupos de recursos.

## Protección de recursos contra actores internos perjudiciales

Es posible que a una organización le preocupe proteger sus recursos y las plantillas que adquieren contra actores perjudiciales.

Un ejemplo de esto podría ser un banco que quiera asegurarse de que un desarrollador de software o un miembro de su personal de TI deshonesto no realice modificaciones ni extraiga información clave que se traduzca en datos que vayan a un actor perjudicial con fines delictivos.

Un escenario típico de empresa es tener un pequeño grupo de operadores de confianza con acceso a los secretos críticos dentro de las cargas de trabajo implementadas y un grupo más amplio de personal de desarrollo y operaciones que puede crear o actualizar las implementaciones de máquina virtual.

Se usaría el Almacén de claves de Azure con ARM para organizar y almacenar certificados y secretos de máquina virtual.

Un procedimiento recomendado consiste en mantener plantillas ARM independientes para la creación de almacenes (que contendrán el material de claves) y la implementación de las máquinas virtuales (con referencias URI a las claves contenidas en los almacenes).

Los secretos almacenados en el almacén de claves están bajo el control total de RBAC de un operador de confianza. Si el operador de confianza deja la compañía o se transfiere dentro de la compañía a un nuevo grupo, ya no tendrá acceso a las claves que se crean en el almacén.

Las plantillas de implementación de ARM solo contienen referencias URI a los secretos, lo que significa que los secretos reales no están en los repositorios de código, configuración o código fuente. Esto reduce las oportunidades de acceso a los secretos por suplantación de identidad (phishing) y limita la posibilidad de que actores perjudiciales realicen cambios.

Como se indicó anteriormente en el documento, no existen almacenes de claves globales. Dado que los almacenes de claves son siempre regionales, los secretos siempre tienen la localidad (y soberanía) con las máquinas virtuales.

Se ofreció una implementación de ejemplo de este enfoque en la sección sobre secretos y certificados que aparecía anteriormente en este documento.

## Habilitación de un modelo de tipo "traiga su propia suscripción"

Los grupos de TI corporativa, los integradores de sistemas y los proveedores de Servicios en la nube pueden emplear un modelo de tipo "traiga su propia suscripción" con sus clientes. En concreto, la organización ofrece un servicio a un cliente final y de alguna manera usa la suscripción de Azure de ese cliente.

Existen diversas variantes de este enfoque, cada una con requisitos ligeramente distintos, tal como se detalla a continuación.

### Habilitación del acceso de terceros para supervisión de recursos dentro de una cuenta

Es posible que una organización con una aplicación de supervisión necesite acceso de solo lectura a la suscripción de un cliente para recuperar datos que se usan en esa aplicación. Esto requeriría acceso de solo lectura durante un período de tiempo continuo. El acceso tendría que estar en el control del cliente, lo que les brinda la posibilidad de finalizar el acceso si se rompe la relación con el proveedor del servicio de supervisión.

#### Implementación con el Administrador de recursos de Azure

Se ofrecen datos con gran cantidad de detalles sobre cómo implementarlo en la "Guía para desarrolladores para autenticación con el recurso API del Administrador de recursos de Azure" que se encuentra [aquí](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/). En ese documento se ofrecen instrucciones de implementación paso a paso, además de código de ejemplo.

### Habilitación del acceso de terceros para implementación de software por una vez

En otro ejemplo, es posible que una organización implemente y configure una versión de su software en una cuenta del cliente, lo que requiere acceso de escritura durante el período de tiempo de la implementación.

#### Implementación con el Administrador de recursos de Azure

Esta seguiría un enfoque similar al ejemplo anterior.

Según las necesidades concretas de la instalación, el rol específico que se asigne a la entidad de servicio solo debe permitir el nivel mínimo de acceso necesario para lograr la instalación y, tras completarse esta, revocarse inmediatamente dicho acceso.

### Habilitación del acceso de terceros para uso de suscripciones de cliente para almacenamiento de datos

En otro ejemplo, es posible que una organización prefiera ejecutar software en su propio entorno, pero usar la cuenta del cliente para almacenamiento. De esta forma, el cliente tiene el control de sus datos en todo momento y puede aprovechar otras tecnologías de la plataforma, por ejemplo, Aprendizaje automático o HDInsight de Azure, a su propia discreción mientras no agregue sobrecarga de costos y facturación al grupo de TI empresarial, el integrador de sistemas o el proveedor de Servicios en la nube que suministra la funcionalidad. Esto requiere acceso continuo a la cuenta de almacenamiento de la organización, donde el cliente tiene control y acceso a información de auditoría en esos datos.

#### Implementación con el Administrador de recursos de Azure

Esto se implementa con el mismo patrón que los demás ejemplos. Se proporciona a una entidad de servicio acceso a los recursos de almacenamiento. Como este escenario requiere que el rol tenga acceso de lectura y escritura a la cuenta de almacenamiento, se asignaría el rol de colaborador a la entidad de servicio para lograr este nivel de acceso.

Dado que este escenario implica una primera entidad y un tercero con una cuenta de almacenamiento compartido, también se querrá garantizar que la cuenta de almacenamiento no se elimina involuntariamente. Para este aspecto del escenario, se aplicará un bloqueo de recurso a la cuenta de almacenamiento.

### Habilitación de administración de servicios por parte de un tercero

En otro ejemplo, una organización querrá implementar, supervisar y administrar software en la suscripción de los clientes. Puede haber restricciones en el cliente en cuanto a los cambios que puede (o más explícitamente, que no puede) realizar en un entorno donde se implementó software.

#### Implementación con el Administrador de recursos de Azure

Esto sigue un superconjunto del patrón mencionado al principio de esta sección. En concreto, se proporciona acceso completo a una entidad de servicio que usa un tercero a los recursos del grupo de recursos.

Además, dado que hay restricciones en el cliente, se concedería a los usuarios o grupos del cliente los derechos adecuados para usar el entorno. Esto puede hacerse a través de plantillas, tal como se mencionó anteriormente en esta sección.

Por último, puede que se quiera garantizar que determinados recursos no se eliminen involuntariamente. En este caso, también debe considerarse el uso de bloqueos de recurso en los recursos que requieren dicha protección.

## Pasos siguientes

- Para obtener información sobre cómo crear plantillas, consulte [Creación de plantillas](resource-group-authoring-templates.md).
- Para obtener recomendaciones sobre cómo controlar la seguridad en el Administrador de recursos de Azure, consulte [Consideraciones de seguridad para el Administrador de recursos de Azure](best-practices-resource-manager-security.md).
- Para obtener información sobre cómo compartir el estado dentro y fuera de las plantillas, consulte [Uso compartido del estado en las plantillas del Administrador de recursos de Azure](best-practices-resource-manager-state.md).

<!---HONumber=AcomDC_1223_2015-->