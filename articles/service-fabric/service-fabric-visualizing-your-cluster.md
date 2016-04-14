<properties
   pageTitle="Visualización del clúster mediante el Explorador de Service Fabric | Microsoft Azure"
   description="El Explorador de Service Fabric es una herramienta basada en web para inspeccionar y administrar aplicaciones y nodos en la nube en un clúster de Service Fabric de Microsoft Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/13/2016"
   ms.author="seanmck"/>

# Visualización del clúster mediante el Explorador de Service Fabric

El Explorador de Service Fabric es una herramienta web para inspeccionar y administrar aplicaciones y nodos en un clúster de Azure Service Fabric. El Explorador de Service Fabric se hospeda directamente en el clúster para que siempre esté disponible, independientemente de dónde se ejecuta el clúster.

## Conexión al Explorador de Service Fabric

Si ha seguido las instrucciones para [preparar el entorno de desarrollo](service-fabric-get-started.md), puede iniciar el explorador de Service Fabric en el clúster local en http://localhost:19080/Explorer.

>[AZURE.NOTE]Si usa Internet Explorer con el Explorador de Service Fabric para administrar un clúster remoto, deberá configurar algunas opciones de Internet Explorer. Vaya a **Herramientas** > **Configuración de Vista de compatibilidad** y desactive la casilla **Mostrar sitios de la intranet en Vista de compatibilidad** para asegurarse de que toda la información se cargue correctamente.

## Información sobre el diseño del Explorador de Service Fabric

Puede desplazarse por el Explorador de Service Fabric desde el árbol situado a la izquierda. En la raíz del árbol, el panel del clúster proporciona información general del clúster, incluido un resumen de la aplicación y del estado del nodo.

![Panel de clúster del Explorador de Service Fabric][sfx-cluster-dashboard]

### Visualización del diseño del clúster

Los nodos de un clúster de Service Fabric se colocan en una cuadrícula bidimensional de dominios de error y dominios de actualización. Esta colocación garantiza que las aplicaciones permanecen disponibles en presencia de errores de hardware y actualizaciones de aplicaciones. Puede ver cómo se dispone el clúster actual mediante el mapa del clúster.

![Mapa de clúster del Explorador de Service Fabric][sfx-cluster-map]

### Visualización de aplicaciones y servicios

El clúster contiene dos subárboles: uno para las aplicaciones y otro para los nodos.

Puede usar la vista de aplicaciones para navegar por la jerarquía lógica de Service Fabric: aplicaciones, servicios, particiones y réplicas.

En el ejemplo siguiente, la aplicación **MyApp** se compone de dos servicios: **MyStatefulService** y **WebService**. Como **MyStatefulService** es un servicio con estado, incluye una partición con una réplica principal y dos réplicas secundarias. Por el contrario, el servicio WebSvcService no tiene estado y contiene una única instancia.

![Vista de aplicación del explorador de Service Fabric][sfx-application-tree]

En cada nivel del árbol, el panel principal muestra la información pertinente sobre el elemento. Por ejemplo, puede ver el estado de mantenimiento y la versión para un servicio determinado.

![Panel Essentials del explorador de Service Fabric][sfx-service-essentials]

### Visualización de los nodos del clúster

En la vista de nodos se muestra el diseño físico del clúster. Para un nodo determinado, puede comprobar qué aplicaciones tienen el código implementado en ese nodo. Más específicamente, puede ver qué réplicas están en ejecución.

## Acciones

El explorador de Service Fabric ofrece una forma rápida de invocar acciones en nodos, aplicaciones y servicios dentro del clúster.

Por ejemplo, para eliminar una instancia de la aplicación, solo hay que elegir la aplicación en el árbol de la izquierda y luego elegir **Acciones** > **Eliminar aplicación**.

![Eliminación de una aplicación en el explorador de Service Fabric][sfx-delete-application]

En la tabla siguiente se enumeran las acciones disponibles para cada entidad:

| **Entidad** | **Acción** | **Descripción** |
| ------ | ------ | ----------- |
| Tipo de aplicación | Tipo de anulación de aprovisionamiento | Quita el paquete de aplicación del almacén de imágenes del clúster. Requiere que todas las aplicaciones de ese tipo se quiten en primer lugar. |
| Application | Eliminar aplicación | Elimine la aplicación, incluidos todos sus servicios y su estado (si hubiera). |
| Servicio | Eliminar servicio | Elimine el servicio y su estado (si hubiera). |
| Nodo | Activar | Active el nodo. |
|| Desactivar (pausa) | Pause el nodo en su estado actual. Los servicios seguirán ejecutándose, pero Service Fabric no introducirá ni sacará nada proactivamente, a menos que se requiera para prevenir una interrupción o una incoherencia de datos. Esta acción se utiliza normalmente para habilitar los servicios de depuración en un nodo específico para asegurarse de que no se mueven durante la inspección. |
|| Desactivar (reiniciar) | Saque todos los servicios de la memoria de un nodo y cierre los servicios persistentes de forma segura. Suele usarse cuando es necesario reiniciar los procesos de host o el equipo. |
|| Desactivar (quitar datos) | Cierre todos los servicios que se ejecutan en el nodo después de la creación de suficientes réplicas de reserva con seguridad. Se utiliza normalmente cuando un nodo (o al menos su almacenamiento) se está sacando permanentemente de circulación. |
|| Quitar el estado del nodo | Quite información de las réplicas de un nodo del clúster. Se utiliza normalmente cuando un nodo con error ya se considera irrecuperable. |

Como muchas acciones son destructivas, le pediremos que confirme su intención antes de que finalice la acción.

>[AZURE.TIP]Todas las acciones que pueden realizarse a través del Explorador de Service Fabric también pueden realizarse a través de PowerShell o una API de REST, para habilitar la automatización.



## Conexión a un clúster de Service Fabric remoto

Como el explorador de Service Fabric está basado en web y se ejecuta dentro del clúster, es accesible desde cualquier explorador, siempre que conozca el punto de conexión del clúster y tenga permisos suficientes para tener acceso a él.

### Detección del punto de conexión del Explorador de Service Fabric para un clúster remoto

Para acceder al explorador de Service Fabric para un clúster determinado, simplemente dirija el explorador a:

http://&lt;your-cluster-endpoint&gt;:19080/Explorer

La dirección URL completa también está disponible en el panel de elementos esenciales del clúster del portal de Azure.

### Conexión a un clúster seguro

Puede controlar el acceso al clúster de Service Fabric al requerir a los clientes que presenten un certificado para conectarse a él.

Si intenta conectarse al Explorador de Service Fabric en un clúster seguro, el explorador le pedirá que presente un certificado para poder tener acceso.

## Pasos siguientes

- [Información general sobre Testability](service-fabric-testability-overview.md)
- [Administración de aplicaciones de Service Fabric en Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Implementación de aplicaciones de Service Fabric con PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png

<!---HONumber=AcomDC_0121_2016-->