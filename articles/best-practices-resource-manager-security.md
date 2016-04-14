<properties
	pageTitle="Consideraciones de seguridad para el Administrador de recursos de Azure"
	description="Muestra enfoques recomendados en el Administrador de recursos de Azure para proteger recursos con claves y secretos, el control de acceso basado en roles y los grupos de seguridad de red."
	services="azure-resource-manager"
	documentationCenter=""
	authors="george-moore"
	manager="georgem"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="georgem;tomfitz"/>


# Consideraciones de seguridad para el Administrador de recursos de Azure

Al mirar los aspectos de seguridad de las plantillas del Administrador de recursos de Azure, hay varias áreas a tener en cuenta: claves y secretos, control de acceso basado en roles y grupos de seguridad de red.

En este tema se supone que está familiarizado con el control de acceso basado en rol (RBAC) del Administrador de recursos de Azure. Para más información, vea [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).

Este tema forma parte de un artículo más extenso. Si quiere leer el artículo completo, descargue [World Class ARM Templates Considerations and Proven Practices] (Consideraciones y prácticas comprobadas sobre plantillas ARM de clase mundial) (http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).

## Secretos y certificados

Máquinas virtuales de Azure, Administrador de recursos de Azure y Almacén de claves de Azure están totalmente integrados para ofrecer compatibilidad para el control seguro de certificados que se deben implementar en la máquina virtual. La utilización de Almacén de claves de Azure con el Administrador de recursos para organizar y almacenar certificados y secretos de máquina virtual es una práctica recomendada y proporciona las siguientes ventajas:

- Las plantillas solo contienen referencias URI a los secretos, lo que significa que los secretos reales no están en los repositorios de código, configuración o código fuente. Esto impide ataques de suplantación de identidad de claves en repositorios internos o externos, como los robots de recolección de GitHub.
- Los secretos almacenados en el almacén de claves están bajo el control total de RBAC de un operador de confianza. Si el operador de confianza deja la compañía o pasa a un nuevo grupo dentro de la compañía, ya no tendrán acceso a las claves que crearon en el almacén.
- Compartimentación completa de todos los activos:
      - las plantillas para implementar las claves 
      - las plantillas para implementar una máquina virtual con referencias a las claves 
      - los materiales de clave reales en el almacén. Cada plantilla (y acción) pueden estar bajo roles RBAC diferentes para una separación completa de responsabilidades.
- La carga de secretos en una máquina virtual durante la implementación se produce a través de un canal directo entre el tejido de Azure y el Almacén de claves dentro de los confines del centro de datos de Microsoft. Una vez que las claves se encuentran en el Almacén de claves, nunca salen a la luz través de un canal que no sea de confianza fuera del centro de datos.  
- Los almacenes de claves son siempre regionales, por lo que los secretos siempre tienen la localidad (y soberanía) con las máquinas virtuales. No hay almacenes de claves globales.

### Separación de claves de las implementaciones

Un procedimiento recomendado consiste en mantener plantillas independientes para:

1.	Creación de almacenes (que contendrán el material de claves)
2.	Implementación de las máquinas virtuales (con referencias URI a las claves contenidas en los almacenes)

Un escenario típico de empresa es tener un pequeño grupo de operadores de confianza con acceso a los secretos críticos dentro de las cargas de trabajo implementadas y un grupo más amplio de personal de desarrollo y operaciones que puede crear o actualizar las implementaciones de máquina virtual. A continuación se muestra un ejemplo de plantilla ARM que crea y configura un nuevo almacén en el contexto de la identidad del usuario actualmente autenticado en Azure Active Directory. Este usuario tendría el permiso predeterminado para crear, eliminar, enumerar, actualizar, hacer copias de seguridad, restaurar y obtener la mitad pública de claves en este nuevo almacén de claves.

Aunque la mayoría de los campos de esta plantilla son autoexplicativos, el valor **enableVaultForDeployment** merece más atención: los almacenes no tienen acceso permanente predeterminado por ningún otro componente de la infraestructura de Azure. Al establecer este valor, se permite que los componentes de infraestructura de Proceso de Azure tengan acceso de solo lectura a este almacén con nombre específico. Por lo tanto, un procedimiento muy recomendable es no combinar datos confidenciales corporativos en el mismo almacén que los secretos de máquina virtual.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the Vault"
                }
            },
            "location": {
                "type": "string",
                "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
                "metadata": {
                    "description": "Location of the Vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                    "description": "Tenant Id of the subscription. Get using Get-AzureSubscription cmdlet or Get Subscription API"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object Id of the AD user. Get using Get-AzureADUser cmdlet"
                }
            },
            "skuName": {
                "type": "string",
                "allowedValues": ["Standard", "Premium"],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enableVaultForDeployment": {
                "type": "bool",
                "allowedValues": [true, false],
                "metadata": {
                    "description": "Specifies if the vault is enabled for a VM deployment"
                }
            }
        },
        "resources": [{
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2014-12-19-preview",
            "location": "[parameters('location')]",
            "properties": {
                "enabledForDeployment": "[parameters('enableVaultForDeployment')]",
                "tenantid": "[parameters('tenantId')]",
                "accessPolicies": [{
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "secrets": ["all"],
                        "keys": ["all"]
                    }
                }],
                "sku": {
                    "name": "[parameters('skuName')]",
                    "family": "A"
                }
            }
        }]
    }

Una vez creado el almacén, el siguiente paso es hacer referencia a ese almacén en la plantilla de implementación de una nueva máquina virtual. Como se mencionó anteriormente, un procedimiento recomendado es tener un grupo de desarrollo y operadores diferentes que administre implementaciones de máquina virtual, de forma que ese grupo no tenga acceso directo a las claves almacenadas en el almacén.

El fragmento de plantilla siguiente estaría compuesto de construcciones de implementación de orden superior, cada una de ellas haciendo referencia de forma segura a secretos confidenciales que no están bajo el control directo del operador.

    "vaultName": {
        "type": "string",
        "metadata": {
            "description": "Name of Key Vault that has a secret"
        }
    },
    {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[parameters('vmName')]",
        "location": "[parameters('location')]",
        "properties": {
            "osProfile": {
                "secrets": [{
                    "sourceVault": {
                        "id": "[resourceId('vaultrg', 'Microsoft.KeyVault/vaults', 'kayvault')]"
                    },
                    "vaultCertificates": [{
                        "certificateUrl": "[parameters('secretUrlWithVersion')]",
                        "certificateStore": "My"
                    }]
                }]
            }
        }
    }

Para pasar un valor de un almacén de claves como parámetro durante la implementación de una plantilla, consulte [Paso de valores seguros durante la implementación](resource-manager-keyvault-parameter.md).

## Entidades de servicio para interacciones entre suscripciones

Las identidades de servicio se representan por entidades de servicio en Active Directory. Las entidades de servicio estarán en el centro de la habilitación de escenarios clave para organizaciones de TI empresarial, integradores de sistemas (SI) y proveedores de servicios en la nube (CSV). En concreto, habrá casos de uso donde una de estas organizaciones necesite interactuar con la suscripción de uno de sus clientes.

Su organización podría proporcionar una oferta que supervise una solución implementada en el entorno y la suscripción de sus clientes. En este caso, necesitará obtener acceso a los registros y a otros datos dentro de una cuenta de los clientes para que pueda utilizarla en la solución de supervisión. Si es una organización de TI corporativa o un integrador de sistemas, es posible que haga una oferta a un cliente en la que implemente y administre una funcionalidad para ellos, por ejemplo, una plataforma de análisis de datos, donde la oferta reside en la propia suscripción de los clientes.

En estos casos de uso, su organización requeriría una identidad a la que se podría dar acceso para realizar estas acciones dentro del contexto de una suscripción de cliente.

Estos escenarios implican una serie de consideraciones para el cliente:

-	Por motivos de seguridad, puede que el acceso necesite centrarse en ciertos tipos de acciones, por ejemplo, acceso de solo lectura.
-	Dado que los recursos implementados se proporcionan a un costo, puede haber restricciones similares en el acceso requerido por motivos financieros.
-	Por motivos de seguridad, puede que el acceso necesite centrarse en uno (cuentas de almacenamiento) o varios (grupo de recursos que contiene un entorno o una solución) recursos específicos.
-	Dado que una relación con un proveedor puede cambiar, el cliente querrá tener la posibilidad de habilitar o deshabilitar el acceso a SI o CSV.
-	Dado que las acciones en esta cuenta tienen implicaciones de facturación, el cliente desea compatibilidad con auditorías y contabilidad para facturación.
-	Desde una perspectiva de cumplimiento, el cliente deseará poder auditar su comportamiento dentro de su entorno.

Una combinación de una entidad de servicio y RBAC puede utilizarse para satisfacer estos requisitos.

## Grupos de seguridad de red

Muchos escenarios tendrán requisitos que especifican cómo se controla el tráfico a una o más instancias de máquina virtual en la red virtual. Puede usar un grupo de seguridad de red (NSG) para hacer esto como parte de una implementación de la plantilla ARM.

Un grupo de seguridad de red es un objeto de nivel superior que está asociado a su suscripción. Un grupo de seguridad de red contiene reglas de control de acceso que permiten o deniegan el tráfico a instancias de máquina virtual. Las reglas de un grupo de seguridad de red pueden cambiarse en cualquier momento; los cambios se aplican a todas las instancias asociadas. Para usar un grupo de seguridad de red, debe tener una red virtual asociada a una región (ubicación). Los grupos de seguridad de red no son compatibles con redes virtuales asociadas a un grupo de afinidad. Si no tiene una red virtual regional y desea controlar el tráfico a los extremos, consulte [Listas de control de acceso (ACL) de red?](../virtual-network/virtual-networks-acl.md).

Puede asociar un grupo de seguridad de red a una máquina virtual o a una subred dentro de una red virtual. Cuando se asocia a una máquina virtual, el grupo de seguridad de red se aplica a todo el tráfico enviado y recibido por la instancia de máquina virtual. Cuando se aplica a una subred dentro de la red virtual, se aplica a todo el tráfico enviado y recibido por todas las instancias de máquina virtual de la subred. Una máquina virtual o una subred solo puede asociarse a 1 grupo de seguridad de red, pero cada grupo de seguridad de red puede contener hasta 200 reglas. Puede tener 100 grupos de seguridad de red por suscripción.

>[AZURE.NOTE]  No se admiten ACL basadas en el extremo y grupos de seguridad de red en la misma instancia de máquina virtual. Si desea usar un grupo de seguridad de red y ya tiene un extremo del ACL, quite primero el extremo del ACL. Para obtener información acerca de cómo hacerlo, consulte [Administración de listas de control de acceso (ACL) para extremos mediante PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

### Funcionamiento de los grupos de seguridad de red

Los grupos de seguridad de red son diferentes de las ACL basadas en extremos. Las ACL de extremo solo funcionan en el puerto público que se expone a través del extremo de entrada. Un grupo de seguridad de red funciona en una o más instancias de máquina virtual y controla todo el tráfico es entrante y saliente de la máquina virtual.

Un grupo de seguridad de red tiene un *nombre*, está asociado a una *región* (una de las ubicaciones de Azure compatibles) y tiene una etiqueta descriptiva. Contiene dos tipos de reglas, Entrante y Saliente. Se aplican las reglas de entrada en los paquetes entrantes a una máquina virtual y las reglas de salida se aplican a los paquetes salientes de la máquina virtual. Las reglas se aplican a la máquina del servidor donde se encuentra la máquina virtual. Un paquete entrante o saliente debe coincidir con una regla Permitir para admitirlo; de lo contrario, se quitará.

Las reglas se procesan en el orden de prioridad. Por ejemplo, una regla con un número de prioridad más bajo (por ejemplo 100) se procesa antes que las reglas con números de prioridad más alto (por ejemplo 200). Una vez que se encuentra una coincidencia, no se procesan más reglas.

Una regla especifica lo siguiente:

-	Nombre: identificador único para la regla
-	Tipo: Entrante o Saliente
-	Prioridad: número entero comprendido entre 100 y 4096 (las reglas se procesan de baja a alta)
-	Dirección IP de origen: CIDR de intervalo de direcciones IP de origen
-	Intervalo de puertos de origen: entero o intervalo comprendido entre 0 y 65536
-	Intervalo de direcciones IP de destino: CIDR del intervalo de direcciones IP de destino
-	Intervalo de puertos de destino: entero o intervalo comprendido entre 0 y 65536
-	Protocolo: TCP, UDP o ‘*’
-	Acceso: Permitir o Denegar

### Reglas predeterminadas

Un grupo de seguridad de red contiene las reglas predeterminadas. Las reglas predeterminadas no se pueden eliminar, pero dado que tienen asignada la mínima prioridad, pueden reemplazarse por las reglas que cree. Las reglas predeterminadas describen la configuración predeterminada recomendada por la plataforma. Como se muestra en las siguientes reglas predeterminadas, el tráfico que se origina y termina en una red virtual se permite en las direcciones tanto de entrada como de salida.

Aunque la conectividad a Internet está permitida para la dirección de salida, está bloqueada para la dirección de entrada de manera predeterminada. Una regla predeterminada permite que el equilibrador de carga de Azure sondee el estado de una máquina virtual. Puede invalidar esta regla si la máquina virtual o el conjunto de máquinas virtuales en el grupo de seguridad de red no participa en el conjunto de carga equilibrada.

Las reglas predeterminadas se muestran en las tablas siguientes.

**Reglas predeterminadas de entrada**

Nombre |	Prioridad |	IP de origen |	Puerto de origen |	IP de destino |	Puerto de destino |	Protocolo |	Access
--- | --- | --- | --- | --- | --- | --- | ---
ALLOW VNET INBOUND (PERMITIR ENTRANTE DE RED VIRTUAL) | 65000 | VIRTUAL\_NETWORK |	* |	VIRTUAL\_NETWORK | * |	* | PERMITIR
ALLOW AZURE LOAD BALANCER INBOUND (PERMITIR ENTRANTE DEL EQUILIBRADOR DE CARGA DE AZURE) | 65001 | AZURE\_LOADBALANCER | * | * | * | * | PERMITIR
DENY ALL INBOUND (DENEGAR TODO EL TRÁFICO ENTRANTE) | 65500 | * | * | * | * | * | DENEGAR

**Reglas predeterminadas de salida**

Nombre |	Prioridad |	IP de origen |	Puerto de origen |	IP de destino |	Puerto de destino |	Protocolo |	Access
--- | --- | --- | --- | --- | --- | --- | ---
ALLOW VNET OUTBOUND (PERMITIR SALIENTE DE RED VIRTUAL) | 65000 | VIRTUAL\_NETWORK | * | VIRTUAL\_NETWORK | * | * | PERMITIR
ALLOW INTERNET OUTBOUND (PERMITIR SALIENTE DE INTERNET) | 65001 | * | * | INTERNET | * | * | PERMITIR
DENY ALL OUTBOUND (DENEGAR TODO EL TRÁFICO SALIENTE) | 65500 | * | * | * | * | * | DENEGAR

### Reglas especiales de infraestructura

Las reglas de los grupos de seguridad de red son explícitas. No se permite ningún tráfico o se deniega más allá de lo especificado en las reglas de los grupos de seguridad de red. Sin embargo, dos tipos de tráfico siempre se permiten, independientemente de la especificación del grupo de seguridad de red. Estos aprovisionamientos se establecen para admitir la infraestructura.

- **IP virtual del nodo de host:** servicios de infraestructura básica, como DHCP, DNS y seguimiento de estado, se proporcionan a través de la dirección IP 168.63.129.16 del host virtualizado. Esta dirección IP pública pertenece a Microsoft y será la única dirección IP virtualizada que se usará en todas las regiones con este fin. Esta dirección IP se asigna a la dirección IP física del equipo del servidor (nodo de host) que hospeda la máquina virtual. El nodo de host actúa como la retransmisión DHCP, la resolución recursiva de DNS y el origen de sonda del sondeo de mantenimiento del equilibrador de carga y el sondeo de mantenimiento del equipo. La comunicación con esta dirección IP no se debe considerar como un ataque.
- **Licencias (servicio de administración de claves):** las imágenes de Windows que se ejecutan en las máquinas virtuales deben contar con licencia. Para ello, se debe enviar una solicitud de licencia a los servidores host del servicio de administración de claves que administran dichas consultas. Esto siempre se hará en el puerto de salida 1688.

### Etiquetas predeterminadas

Las etiquetas predeterminadas son identificadores proporcionados por el sistema para tratar una categoría de direcciones IP. Las etiquetas predeterminadas se pueden especificar en reglas definidas por el usuario.

**Etiquetas predeterminadas para grupos de seguridad de red**

Etiqueta |	Descripción
--- | ---
VIRTUAL\_NETWORK |	Denota todo el espacio de direcciones de red. Incluye el espacio de direcciones de red virtual (CIDR de IP en Azure), así como todos los espacios de direcciones locales conectados (redes locales). Esto también incluye los espacios de direcciones de red virtual a red a virtual.
AZURE\_LOADBALANCER | Denota el equilibrador de carga de la infraestructura de Azure y se convertirá en una dirección IP de centro de datos de Azure donde se originarán las comprobaciones del estado de Azure. Esto solo es necesario si la máquina virtual o un conjunto de máquinas virtuales asociado al grupo de seguridad de red participa en un conjunto de carga equilibrada.
INTERNET | Denota el espacio de direcciones IP que está fuera de la red virtual al que puede conectarse la red Internet pública. Este intervalo incluye además un espacio de direcciones IP públicas propiedad de Azure.

### Puertos e intervalos de puertos

Las reglas del grupo de seguridad de red se pueden especificar en un único origen o puerto de destino, o en un intervalo de puertos. Este enfoque resulta especialmente útil en casos en los que desea abrir un amplio intervalo de puertos para una aplicación, como FTP. El intervalo debe ser secuencial y no se puede combinar con especificaciones de puerto individuales. Para especificar un intervalo de puertos, use el carácter de guión (–). Por ejemplo, **100-500**.

### Tráfico ICMP

Con las reglas actuales del grupo de seguridad de red, puede especificar TCP o UDP como protocolos, pero no ICMP. Sin embargo, el tráfico ICMP se permite dentro de una red virtual de manera predeterminada gracias a las reglas entrantes que permiten el tráfico desde y hacia cualquier puerto y protocolo (*) dentro de la red virtual.

### Asociación de un grupo de seguridad de red a una máquina virtual

Cuando un grupo de seguridad de red está directamente asociado a una máquina virtual, las reglas de acceso de red del grupo de seguridad de red se aplican directamente a todo el tráfico destinado a la máquina virtual. Siempre que se actualiza el grupo de seguridad de red para los cambios de regla, el control de tráfico refleja las actualizaciones en minutos. Cuando el grupo de seguridad de red se desasocia de la máquina virtual, el estado vuelve a su condición anterior al grupo de seguridad personal, es decir, a los valores predeterminados del sistema que había antes de introducir el grupo de seguridad de red.

### Asociación de un grupo de seguridad de red a una subred

Cuando un grupo de seguridad de red está asociado a una subred, se aplican las reglas de acceso a la red en el grupo de seguridad de red a todas las máquinas virtuales de la subred. Cada vez que se actualizan las reglas de acceso en el grupo de seguridad de red, los cambios se aplican a todas las máquinas virtuales de la subred en unos minutos.

### Asociación de un grupo de seguridad de red a una subred y a una máquina virtual

Puede asociar un grupo de seguridad de red a una máquina virtual y otro grupo de seguridad de red a la subred donde reside la máquina virtual. Este escenario se admite para proporcionar la máquina virtual con dos niveles de protección. En el tráfico entrante, el paquete sigue las reglas de acceso especificadas en la subred, seguida de las reglas de la máquina virtual. Cuando es saliente, el paquete sigue las reglas especificadas en la máquina virtual en primer lugar, y a continuación, sigue las reglas especificadas en la subred, tal como se muestra a continuación.

![Asociación de grupo de seguridad de red a una subred y una máquina virtual](./media/best-practices-resource-manager-security/nsg-subnet-vm.png)

Cuando un grupo de seguridad de red está asociado a una máquina virtual o una subred, las reglas de control de acceso de la red pasan a ser muy explícitas. La plataforma no insertará ninguna regla implícita para permitir el tráfico a un puerto determinado. En este caso, si crea un extremo en la máquina virtual, también debe crear una regla para permitir el tráfico de Internet. Si no es así, no se puede tener acceso a *VIP:{Puerto}* desde el exterior.

Por ejemplo, puede crear una nueva máquina virtual y un nuevo grupo de seguridad de red. Asociar el grupo de seguridad de red a la máquina virtual. La máquina virtual puede comunicarse con otras máquinas virtuales de la red virtual a través de la regla ALLOW VNET INBOUND (PERMITIR ENTRANTE DE RED VIRTUAL). La máquina virtual puede realizar conexiones salientes a Internet mediante la regla ALLOW INTERNET OUTBOUND (PERMITIR SALIENTE DE INTERNET). Posteriormente, cree un extremo en el puerto 80 para recibir tráfico en su sitio web que se ejecuta en la máquina virtual. Los paquetes destinados al puerto 80 en la dirección IP virtual pública (VIP) desde Internet no llegarán a la máquina virtual hasta que agregue una regla similar a la siguiente tabla al grupo de seguridad de red.

**Regla explícita que permite el tráfico a un puerto determinado**

Nombre |	Prioridad |	IP de origen |	Puerto de origen |	IP de destino |	Puerto de destino |	Protocolo |	Access
--- | --- | --- | --- | --- | --- | --- | ---
WEB | 100 | INTERNET | * | * | 80 | TCP | PERMITIR

## Rutas definidas por el usuario

Azure utiliza una tabla de enrutamiento para decidir cómo reenviar el tráfico IP basándose en el destino de cada paquete. Aunque Azure proporciona una tabla de enrutamiento predeterminada en función de la configuración de red virtual, es posible que tenga que agregar rutas personalizadas a esa tabla.

La necesidad más común de una entrada personalizada en la tabla de enrutamiento es el uso de un dispositivo virtual en el entorno de Azure. Tenga en cuenta el escenario mostrado en la ilustración siguiente. Suponga que desea asegurarse de que todo el tráfico dirigido a la capa intermedia y a las subredes con copia de seguridad que se inicia desde la subred front-end a través de un dispositivo virtual de firewall. Si únicamente se agrega el dispositivo a la red virtual y se conecta a las diferentes subredes no proporcionará esta funcionalidad. También debe cambiar la tabla de enrutamiento que se aplica a la subred para asegurarse de que los paquetes se reenvían al dispositivo virtual del firewall.

Se aplica lo mismo si implementa un dispositivo NAT virtual para controlar el tráfico entre Internet y de la red virtual de Azure. Para asegurarse de que se usa el dispositivo virtual, debe crear una ruta que especifique que todo el tráfico destinado a Internet debe reenviarse al dispositivo virtual.

### Enrutamiento

Los paquetes se enrutan sobre una red TCP/IP basada en una tabla de enrutamiento definida en cada nodo de la red física. Una tabla de enrutamiento es una colección de rutas individuales que se utiliza para decidir dónde reenviar los paquetes según la dirección IP de destino. Una ruta consta de lo siguiente:

- Prefijo de dirección El CIDR de destino al que se aplica la ruta, por ejemplo, 10.1.0.0/16.
- Tipo de próximo salto El tipo de salto de Azure al que debe enviarse el paquete. Los valores posibles son:
  - Local. Representa la red virtual local. Por ejemplo, si tiene dos subredes, 10.1.0.0/16 y 10.2.0.0/16 en la misma red virtual, la ruta de cada subred de la tabla de rutas tendrá un valor de próximo salto de Local.
  - Puerta de enlace de VPN. Representa una puerta de enlace de VPN S2S de Azure.
  - Internet. Representa la puerta de enlace de Internet predeterminada proporcionada por la infraestructura de Azure.
  - Dispositivo virtual. Representa un dispositivo virtual agregado a la red virtual de Azure.
  - NULL. Representa un agujero negro. Los paquetes enviados a un agujero negro no se reenviarán de ninguna manera.
-	Valor del próximo salto. El valor del próximo salto contiene los paquetes de la dirección IP a la que se deben reenviar. Solo se permiten valores de próximo salto en las rutas donde el tipo de próximo salto es *Dispositivo virtual*. El próximo salto debe estar en la subred (la interfaz local del dispositivo virtual según el identificador de red), no en una subred remota. 

![Enrutamiento](./media/best-practices-resource-manager-security/routing.png)

### Rutas predeterminadas

Cada subred que se creó en una red virtual se asocia automáticamente a una tabla de enrutamiento que contiene las siguientes reglas de ruta predeterminadas:

- Regla de red virtual local: esta regla se crea automáticamente para cada subred de una red virtual. Especifica que hay un vínculo directo entre las máquinas virtuales en la red virtual y que no hay ningún salto intermedio. Esto permite que las máquinas virtuales de la misma subred, sin tener en cuenta el identificador de red en que se encuentran las máquinas virtuales, se comuniquen entre sí sin necesidad de una dirección de puerta de enlace predeterminada.
- Regla local: esta regla se aplica a todo el tráfico destinado al intervalo de direcciones locales y usa la puerta de enlace de VPN como el próximo destino del salto.
- Regla de Internet: esta regla controla todo el tráfico destinado a la red pública y usa la puerta de enlace de Internet de infraestructura como el próximo salto para todo el tráfico destinado a Internet.

### Rutas BGP

En el momento de redactar este artículo, todavía no se admite [ExpressRoute](expressroute/expressroute-introduction.md) en el [Proveedor de recursos de red](virtual-network/resource-groups-networking.md) del Administrador de recursos de Azure. Si tiene una conexión de ExpressRoute entre la red local y Azure, puede habilitar BGP para propagar las rutas desde la red local a Azure una vez que ExpressRoute se admita en NRP. Estas rutas BGP se usan en la misma forma que las rutas predeterminadas y las rutas definidas por el usuario en cada subred de Azure. Para obtener más información, consulte [Introducción a ExpressRoute](expressroute/expressroute-introduction.md).

>[AZURE.NOTE] Cuando ExpressRoute se admita en NRP, podrá configurar el entorno de Azure para forzar la tunelización a través de la red local creando una ruta definida por el usuario para la subred 0.0.0.0/0 que use la puerta de enlace de VPN como próximo salto. Sin embargo, esto solo funciona si se utiliza una puerta de enlace de VPN, no ExpressRoute. Para ExpressRoute, la tunelización forzada se configura a través de BGP.

### Rutas definidas por el usuario

No puede ver las rutas predeterminadas especificadas anteriormente en el entorno de Azure y, en la mayoría de los entornos, son las únicas rutas que va a necesitar. Sin embargo, puede que necesite crear una tabla de enrutamiento y agregar una o varias rutas en casos concretos, como:

-	Tunelización forzada a Internet a través de la red local.
-	Uso de dispositivos virtuales en el entorno de Azure.

En los escenarios anteriores, tendrá que crear una tabla de enrutamiento y agregarle las rutas definidas por el usuario. Puede tener varias tablas de enrutamiento y la misma tabla de enrutamiento puede asociarse a una o varias subredes. Y cada subred solo puede estar asociada a una tabla de enrutamiento única. Todas las máquinas virtuales y servicios de nube de una subred utilizan la tabla de enrutamiento asociada a esa subred.

Las subredes dependen de rutas predeterminadas hasta que una tabla de enrutamiento está asociada a la subred. Una vez creada una asociación, el enrutamiento se realiza en función de la [Coincidencia más larga de prefijo (LPM)](https://en.wikipedia.org/wiki/Longest_prefix_match) entre las rutas definidas por el usuario y las rutas predeterminadas. Si hay más de una ruta con la misma coincidencia LPM, se selecciona una ruta en función de su origen en el orden siguiente:

1.	Ruta definida por el usuario
2.	Ruta BGP (cuando se utiliza ExpressRoute)
3.	Ruta predeterminada

>[AZURE.NOTE] Las rutas definidas por el usuario solo se aplican a las máquinas virtuales de Azure y servicios de nube. Por ejemplo, si quiere agregar un dispositivo virtual de firewall entre la red local y Azure, tendrá que crear una ruta definida por el usuario para las tablas de rutas de Azure que reenvíe todo el tráfico que va al espacio de direcciones local al dispositivo virtual. Sin embargo, el tráfico entrante desde el espacio de direcciones local se propagará a través de la puerta de enlace de VPN o circuito ExpressRoute directamente en el entorno de Azure, omitiendo el dispositivo virtual.

### Reenvío IP

Como se describió anteriormente, una de las razones principales para crear una ruta definida por el usuario es reenviar el tráfico a un dispositivo virtual. Un dispositivo virtual no es más que una máquina virtual que ejecuta una aplicación utilizada para controlar el tráfico de red de alguna manera, como un firewall o un dispositivo NAT.

La máquina virtual de este dispositivo virtual debe ser capaz de recibir el tráfico entrante que no se dirige a sí mismo. Para permitir que una máquina virtual reciba el tráfico dirigido a otros destinos, debe habilitar el reenvío IP en la máquina virtual.

## Pasos siguientes
- Para comprender cómo configurar las entidades de seguridad con el acceso correcto para trabajar con recursos en su organización, vea [Autenticación de una entidad de servicio con el Administrador de recursos de Azure](resource-group-authenticate-service-principal.md).
- Si desea bloquear el acceso a un recurso, puede usar bloqueos de administración. Consulte [Bloqueo de recursos con el Administrador de recursos de Azure](resource-group-lock-resources.md).
- Para configurar el enrutamiento y el reenvío IP, vea [Creación de rutas y habilitación del reenvío IP en Azure](virtual-network/virtual-networks-udr-how-to.md). 
- Para obtener información general sobre el control de acceso basado en roles, vea [Control de acceso basado en roles en el portal de Microsoft Azure](role-based-access-control-configure.md).

<!---HONumber=AcomDC_0211_2016-->