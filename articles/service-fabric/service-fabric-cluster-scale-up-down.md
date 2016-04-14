<properties
   pageTitle="Escalado o reducción vertical de un clúster de Service Fabric | Microsoft Azure"
   description="Escale o reduzca verticalmente un clúster de Service Fabric para satisfacer la demanda mediante la incorporación o eliminación de máquinas virtuales."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/12/2016"
   ms.author="chackdan"/>


# Escalado o reducción vertical de un clúster de Service Fabric agregando o quitando máquinas virtuales

Puede escalar o reducir verticalmente clústeres de Azure Service Fabric para satisfacer la demanda mediante la incorporación o eliminación de máquinas virtuales.

>[AZURE.NOTE] La suscripción debe contar con núcleos suficientes para agregar las nuevas máquinas virtuales que compondrán este clúster.

## Escalado manual de un clúster de Service Fabric

### Elección del tipo de nodo que se escalará

Si el clúster tiene varios tipos de nodo, tendrá que agregar o quitar máquinas virtuales de determinados tipos de nodo. Este es el procedimiento:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. Vaya a **Clústeres de Service Fabric**. ![Página Clústeres de Service Fabric en el Portal de Azure.][BrowseServiceFabricClusterResource]

3. Seleccione el clúster que quiere escalar o reducir verticalmente.

4. Navegue hasta la hoja **Configuración** del panel del clúster. Si no ve la hoja **Configuración**, haga clic en **Toda la configuración** en la parte esencial del panel del clúster.

5. Haga clic en **NodeTypes**, que abrirá la hoja de la **lista NodeTypes**.

6. Haga clic en el tipo de nodo que quiere escalar o reducir verticalmente, que abrirá la hoja de **detalles de NodeType**.

### Escalado vertical mediante la incorporación de nodos

Ajuste el número de máquinas virtuales hasta donde desee y luego guárdelo. Se agregarán los nodos y las máquinas virtuales una vez completada la implementación.

### Reducción vertical mediante la eliminación de nodos

La eliminación de nodos es un proceso de dos pasos:

1. Ajuste el número de máquinas virtuales hasta donde desee y guárdelo. El extremo inferior del control deslizante indica el número mínimo de máquinas virtuales necesarias para ese tipo de nodo.

    >[AZURE.NOTE] Debe mantener un mínimo de cinco máquinas virtuales para el tipo de nodo principal.

2. Una vez completada la implementación, se le notificarán los nombres de máquina virtual que ahora se pueden eliminar. Luego debe navegar a los recursos de máquina virtual y eliminarlos:

    a. Vuelva al panel del clúster y haga clic en **Grupo de recursos**. Se abrirá la hoja **Grupo de recursos**.

    b. Busque en **Resumen** o abra la lista de recursos haciendo clic en "**...**".

    c. Haga clic en el nombre de la máquina virtual que el sistema indicó que se podía eliminar.

    d. Haga clic en el icono **Eliminar** para eliminar la máquina virtual.

>[AZURE.NOTE] Los clústeres de Service Fabric requieren que un cierto número de nodos estén activos en todo momento con el fin de mantener la disponibilidad y conservar el estado (esto se conoce como "mantenimiento del cuórum"). Por lo tanto, normalmente no es seguro apagar todas las máquinas del clúster a menos que antes haya realizado una [copia de seguridad completa del estado](service-fabric-reliable-services-backup-restore.md).

## Escalado automático de clústeres de Service Fabric

En este momento, los clústeres de Service Fabric no admiten el escalado automático. En un futuro cercano, los clústeres se integrarán en conjuntos de escala de máquina virtual y, en ese momento, el escalado automático será posible y se comportará de manera similar al comportamiento de escala automática disponible en los Servicios en la nube.

## Escalado de clústeres mediante el uso de PowerShell y CLI

En este artículo se trata el escalado de clústeres mediante el portal. Sin embargo, puede realizar las mismas acciones desde la línea de comandos con comandos del Administrador de recursos de Azure en el recurso de clúster. La respuesta GET del recurso de clúster ofrecerá la lista de nodos que se han deshabilitado.

## Pasos siguientes

- [Obtener información sobre actualizaciones de clúster](service-fabric-cluster-upgrade.md)
- [Obtener información sobre los servicios con estado de creación de particiones para una escala máxima](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png

<!---HONumber=AcomDC_0218_2016-->