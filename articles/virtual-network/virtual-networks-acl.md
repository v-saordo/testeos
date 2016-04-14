<properties 
   pageTitle="¿Qué es una lista de control de acceso (ACL) de red?"
   description="Más información acerca de las ACL"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# ¿Qué es una lista de control de acceso (ACL) de extremo?

Una lista de control de acceso (ACL) de extremo es una mejora de seguridad disponible para la implementación de Azure. Una ACL proporciona la capacidad de permitir o denegar tráfico a un extremo de la máquina virtual de forma selectiva. Esta capacidad de filtro de paquetes proporciona un nivel adicional de seguridad. Puede especificar ACL de red solo para extremos. No se puede especificar una ACL para una red virtual o para una subred específica contenida en una red virtual.

> [AZURE.IMPORTANT]Siempre que sea posible, se recomienda utilizar grupos de seguridad de red (NSG) en lugar de ACL. Para obtener más información sobre los grupos de seguridad de red, consulte [¿Qué es un grupo de seguridad de red?](../virtual-networks-nsg).

Las listas de control de acceso se pueden configurar mediante PowerShell o el Portal de administración. Para configurar una ACL de red con PowerShell, consulte [Administración de listas de control de acceso (ACL) para extremos usando PowerShell](virtual-networks-acl-powershell.md). Para configurar una ACL de red mediante el Portal de administración, consulte [Configuración de extremos en una máquina virtual](../virtual-machines-set-up-endpoints/).

Con las ACL de red, puede hacer lo siguiente:

- Permitir o denegar selectivamente el tráfico entrante según el intervalo de direcciones IPv4 de subred remota a un extremo de entrada de la máquina virtual

- Incluir en una lista negra direcciones IP

- Crear varias reglas por extremo de máquina virtual

- Especificar hasta 50 reglas de ACL por extremo de máquina virtual

- Usar el orden de reglas para garantizar que se aplica el conjunto correcto de reglas en un extremo de máquina virtual determinado (de menor a mayor)

- Especificar una ACL para una dirección IPv4 de la subred remota específica.

## Funcionamiento de las ACL

Una ACL es un objeto que contiene una lista de reglas. Al crear una ACL y al aplicarla a un extremo de máquina virtual, el filtrado de paquetes tiene lugar en el nodo de host de la máquina virtual. Esto significa que el tráfico de direcciones IP remotas lo filtra el nodo de host para hacer coincidir las reglas de ACL en lugar de en su máquina virtual. De esta forma se evita que la máquina virtual gaste preciosos ciclos de CPU en el filtrado de paquetes.

Cuando se crea una máquina virtual, se coloca una ACL predeterminada para bloquear todo el tráfico entrante. Sin embargo, si se crea un extremo para (puerto 3389), la ACL predeterminada se modifica para permitir todo el tráfico entrante para ese extremo. Después se permite el tráfico entrante desde cualquier subred remota a ese extremo y no se requiere ningún aprovisionamiento de firewall. Todos los demás puertos se bloquean para el tráfico entrante a menos que se creen extremos para esos puertos. De forma predeterminada, se permite el tráfico saliente.

**Ejemplo de tabla de ACL predeterminada**

| **Nº de regla** | **Subred remota** | **Extremo** | **Permitir o denegar** |
|--------|---------------|----------|-------------|
| 100 | 0\.0.0.0/0 | 3389 | Permitir |

## Permitir y denegar

Selectivamente puede permitir o denegar el tráfico de red para un extremo de entrada de la máquina virtual mediante la creación de reglas que especifiquen "permitir" o "denegar". Es importante tener en cuenta que, de forma predeterminada, cuando se crea un extremo, se deniega todo el tráfico al extremo. Por ese motivo, es importante entender cómo se crean reglas de permiso y denegación y colocarlas en el orden de prioridad correcto si desea un control granular sobre el tráfico de red que ha elegido dar permiso para que llegue al extremo de la máquina virtual.

Puntos que se deben tener en cuenta:

1. **Sin ACL**: de forma predeterminada, cuando se crea un extremo, se permite todo en este extremo.

1. **Permitir**: al agregar uno o más intervalos "permitidos", se deniegan todos los demás intervalos de forma predeterminada. Solo los paquetes del intervalo de IP permitidas podrán comunicarse con el extremo de la máquina virtual.

1. **Denegar**: al agregar uno o más intervalos "denegados", se permiten todos los demás intervalos del tráfico de forma predeterminada.

1. **Combinación de permitir y denegar**: puede utilizar una combinación de "permitir" y "denegar" si desea definir un intervalo de direcciones IP específico para permitir o denegar.

## Reglas y precedencia de reglas

Las ACL de red se pueden configurar en los extremos de la máquina virtual específica. Por ejemplo, puede especificar una ACL de red para un extremo RDP creado en una máquina virtual que bloquee el acceso a determinadas direcciones IP. La tabla siguiente muestra una manera de conceder acceso a direcciones IP virtuales públicas (VIP) de un determinado intervalo para permitir el acceso para RDP. Se deniegan todas las direcciones IP remotas. Seguimos un orden de regla de la *inferior tiene prioridad*.

### Varias reglas

En el ejemplo siguiente, si desea permitir el acceso al extremo RDP solo desde dos intervalos de direcciones IPv4 públicas (65.0.0.0/8 y 159.0.0.0/8), puede lograrlo especificando dos reglas *Permitir*. En este caso, como RDP se crea de forma predeterminada para una máquina virtual, puede que desee bloquear el acceso al puerto RDP según una subred remota. El ejemplo siguiente muestra una manera de conceder acceso a direcciones IP virtuales públicas (VIP) de un determinado intervalo para permitir el acceso para RDP. Se deniegan todas las direcciones IP remotas. Esto funciona porque las ACL de red se puede configurar para un extremo de máquina virtual específica y se deniega el acceso de forma predeterminada.

**Ejemplo: varias reglas**

| **Nº de regla** | **Subred remota** | **Extremo** | **Permitir o denegar** |
|--------|---------------|----------|-------------|
| 100 | 65\.0.0.0/8 | 3389 | Permitir |
| 200 | 159\.0.0.0/8 | 3389 | Permitir |

### Orden de reglas

Dado que se pueden especificar varias reglas para un extremo, debe haber una manera de organizar las reglas para determinar qué regla tiene prioridad. El orden de reglas especifica la prioridad. Las ACL de red siguen el orden de regla de la *inferior tiene prioridad*. En el ejemplo siguiente, se le ha concedido acceso de manera selectiva al extremo del puerto 80 a solo determinados intervalos de direcciones IP. Para configurar esto, tenemos una regla de denegación (regla nº 100) para las direcciones del espacio 175.1.0.1/24. A continuación, se especifica una segunda regla con prioridad 200 que permite el acceso a todas las demás direcciones bajo 175.0.0.0/8.

**Ejemplo: precedencia de reglas**

| **Nº de regla** | **Subred remota** | **Extremo** | **Permitir o denegar** |
|--------|---------------|----------|-------------|
| 100 | 175\.1.0.1/24 | 80 | Denegar |
| 200 | 175\.0.0.0/8 | 80 | Permitir |

## ACL de red y conjuntos de carga equilibrada

Las ACL de red pueden especificarse en un extremo del conjunto con equilibrio de carga. Si se especifica una ACL para un conjunto con equilibrio de carga, la ACL de red se aplica a todas las máquinas virtuales de ese conjunto. Por ejemplo, si se ha creado un conjunto con equilibrio de carga con "Puerto 80" y ese conjunto contiene tres máquinas virtuales, la ACL de red creada en el extremo "Puerto 80" de una máquina virtual se aplicará automáticamente a las demás máquinas virtuales.

![ACL de red y conjuntos de carga equilibrada](./media/virtual-networks-acl/IC674733.png)

## Pasos siguientes

[Administración de listas de control de acceso (ACL) para extremos mediante PowerShell](../virtual-networks-acl-powershell)

<!---HONumber=AcomDC_1217_2015-->