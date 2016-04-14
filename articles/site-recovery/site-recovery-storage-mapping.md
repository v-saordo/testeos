<properties
	pageTitle="Asignación de almacenamiento en Azure Site Recovery para la replicación de máquinas virtuales de Hyper-V entre centros de datos locales | Microsoft Azure"
	description="Preparación de la asignación de almacenamiento para la replicación de máquina virtual de Hyper-V entre dos centros de datos locales con Azure Site Recovery"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="02/22/2016"
	ms.author="raynew"/>


# Preparación de la asignación de almacenamiento para la replicación de máquina virtual de Hyper-V entre dos centros de datos locales con Azure Site Recovery


Azure Site Recovery contribuye a su estrategia de continuidad de negocio y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores virtuales. Este artículo describe la asignación de almacenamiento, que le ayuda a realizar un uso óptimo del almacenamiento cuando use Site Recovery para replicar máquinas virtuales de Hyper-V entre dos centros de datos locales de VMM.

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Información general

La asignación de almacenamiento solo es pertinente cuando replica máquinas virtuales de Hyper-V que se encuentran en las nubes VMM desde un centro de datos principal a un centro de datos secundario mediante la replicación SAN o réplica de Hyper-V de la siguiente manera:


- **Replicación entre sitios locales con réplica de Hyper-V**: configure la asignación de almacenamiento asignando clasificaciones de almacenamiento en servidores VMM de origen y destino para realizar las siguientes acciones:

	- **Identificación del almacenamiento de destino para máquinas virtuales de réplica**: las máquinas virtuales se replicará a un destino de almacenamiento (recurso compartido de SMB o volúmenes compartidos de clúster (CSV)) que elija.
	- **Colocación de máquinas virtuales de réplica**: la asignación de almacenamiento se usa para colocar máquinas virtuales de réplica en los servidores host de Hyper-V de forma óptima. Las máquinas virtuales de réplica se colocarán en hosts que pueden tener acceso a las redes de almacenamiento asignadas.
	- **Sin asignación de redes**: si no configura la asignación de almacenamiento, las máquinas virtuales se replicarán en la ubicación de almacenamiento predeterminada especificada en el servidor host de Hyper-V asociado a la máquina virtual de réplica.

- **Replicación entre sitios locales con SAN**: configure la asignación de almacenamiento, para lo que es preciso que asigne grupos de matrices de almacenamiento en los servidores VMM de origen y destino.
	- **Especificación de grupos**: especifica las matrices para especificar qué grupo de almacenamiento secundario recibe datos de replicación desde el grupo principal.
	- **Identificación de los grupos de almacenamiento de destino**: garantiza que las LUN de un grupo de replicación de origen se replican en el grupo de almacenamiento de destino asignado que elija.

## Configuración de las clasificaciones de almacenamiento para la replicación de Hyper-V

Cuando use la réplica de Hyper-V para replicar con Site Recovery, asigne entre las clasificaciones de almacenamiento en servidores VMM de origen y destino, o bien en un único servidor VMM si el mismo servidor VMM administra dos sitios. Observe lo siguiente:

- Cuando se configura correctamente la asignación y la replicación está habilitada, un disco duro virtual de una máquina virtual en la ubicación principal se replicará el en almacenamiento de la ubicación de destino asignada.
- Las clasificaciones de almacenamiento deben estar disponibles para los grupos host ubicados en nubes de origen y de destino.
- Las clasificaciones no necesitan tener el mismo tipo de almacenamiento. Por ejemplo, puede asignar una clasificación de origen que contenga recursos compartidos de SMB a una clasificación de destino que contenga CSV.
- Obtenga más información en [Creación de clasificaciones de almacenamiento en VMM](https://technet.microsoft.com/library/gg610685.aspx).

## Ejemplo

Si las clasificaciones están configuradas correctamente en VMM, al seleccionar el servidor VMM de origen y de destino durante la asignación de almacenamiento, se mostrarán las clasificaciones de origen y de destino. Este es un ejemplo de recursos compartidos de archivos de almacenamiento y clasificaciones para una organización con dos ubicaciones en Nueva York y Chicago.

**Ubicación** | **Servidor VMM** | **Recurso compartido de archivos (origen)** | **Clasificación (origen)** | **Asignado a** | **Recurso compartido de archivos (destino)**
---|---|--- |---|---|---
Nueva York | VMM\_Source| SourceShare1 | GOLD | GOLD\_TARGET | TargetShare1
 | | SourceShare2 | SILVER | SILVER\_TARGET | TargetShare2
 | | SourceShare3 | BRONZE | BRONZE\_TARGET | TargetShare3
Chicago | VMM\_Target | | GOLD\_TARGET | No asignado |
| | | SILVER\_TARGET | No asignado |
 | | | BRONZE\_TARGET | No asignado

Se configuran en la pestaña **Almacenamiento del servidor** en la página **Recursos** del portal de Site Recovery.

![Configurar la asignación de almacenamiento](./media/site-recovery-storage-mapping/storage-mapping1.png)

En este ejemplo: - si una máquina virtual de réplica se crea para cualquier máquina virtual en almacenamiento GOLD (SourceShare1), se replicará en un almacenamiento de GOLD\_TARGET (TargetShare1). - Cuando se crea una máquina virtual de réplica para cualquier máquina virtual en almacenamiento SILVER (SourceShare2), se replicarán en un almacenamiento SILVER\_TARGET (TargetShare2) y así sucesivamente.

Los recursos compartidos de archivo reales y sus clasificaciones asignadas en VMM aparecen en la siguiente captura de pantalla.

![Clasificaciones de almacenamiento en VMM](./media/site-recovery-storage-mapping/storage-mapping2.png)

## Múltiples cuentas de almacenamiento

Si la clasificación de destino se asigna a varios recursos compartidos de SMB o CSV, se selecciona automáticamente la ubicación de almacenamiento óptima cuando la máquina virtual está protegida. Si no hay disponible ningún almacenamiento de destino adecuado con la clasificación especificada, la ubicación de almacenamiento predeterminada especificada en el host de Hyper-V se utiliza para colocar los discos duros virtuales de réplica.

En la tabla siguiente se muestra cómo están configurados la clasificación de almacenamiento y los volúmenes compartidos de clúster en nuestro ejemplo.

**Ubicación** | **Clasificación** | **Almacenamiento asociada**
---|---|---
Nueva York | GOLD | <p>C:\\ClusterStorage\\SourceVolume1</p><p>\\FileServer\\SourceShare1</p>
 | SILVER | <p>C:\\ClusterStorage\\SourceVolume2</p><p>\\FileServer\\SourceShare2</p>
Chicago | GOLD\_TARGET | <p>C:\\ClusterStorage\\TargetVolume1</p><p>\\FileServer\\TargetShare1</p>
 | SILVER\_TARGET| <p>C:\\ClusterStorage\\TargetVolume2</p><p>\\FileServer\\TargetShare2</p>

Esta tabla resume el comportamiento cuando se habilita la protección para las máquinas virtuales (VM1 - VM5) en este entorno de ejemplo.

**Máquina virtual** | **Almacenamiento de origen** | **Clasificación de origen** | **Almacenamiento de destino asignado**
---|---|---|---
VM1 | C:\\ClusterStorage\\SourceVolume1 | GOLD | <p>C:\\ClusterStorage\\SourceVolume1</p><p>\\\FileServer\\SourceShare1</p><p>Both GOLD\_TARGET</p>
VM2 | \\FileServer\\SourceShare1 | GOLD | <p>C:\\ClusterStorage\\SourceVolume1</p><p>\\FileServer\\SourceShare1</p> <p>Both GOLD\_TARGET</p>
VM3 | C:\\ClusterStorage\\SourceVolume2 | SILVER | <p>C:\\ClusterStorage\\SourceVolume2</p><p>\\FileServer\\SourceShare2</p>
VM4 | \\FileServer\\SourceShare2 | SILVER |<p>C:\\ClusterStorage\\SourceVolume2</p><p>\\FileServer\\SourceShare2</p><p>Los dos SILVER\_TARGET</p>
VM5 | C:\\ClusterStorage\\SourceVolume3 | N/D | No hay ninguna asignación, por lo que se usa la ubicación de almacenamiento predeterminada del host de Hyper-V.

## Pasos siguientes

Ahora que dispone de más información sobre la asignación de almacenamiento, [prepárese para la implementación de Azure Site Recovery](site-recovery-best-practices.md).

<!---HONumber=AcomDC_0224_2016-->