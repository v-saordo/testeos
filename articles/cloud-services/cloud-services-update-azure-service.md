<properties
pageTitle="Actualización de un servicio en la nube | Microsoft Azure"
description="Aprenda a actualizar servicios en la nube en Azure. Obtenga información acerca de cómo se realiza una actualización en un servicio en la nube para garantizar la disponibilidad."
services="cloud-services"
documentationCenter=""
authors="kenazk"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="10/26/2015"
ms.author="kenazk"/>

# Actualización de un servicio en la nube

## Información general
A vista de pájaro, actualizar un servicio en la nube, incluidos sus roles y el sistema operativo invitado, es un proceso de tres pasos. En primer lugar, se deben cargar los archivos binarios y archivos de configuración para el nuevo servicio en la nube, o la versión del sistema operativo. A continuación, Azure reserva recursos informáticos y de red para el servicio en la nube según los requisitos de la nueva versión de dicho servicio. Por último, Azure lleva a cabo una actualización gradual para actualizar de forma incremental el inquilino a la nueva versión o sistema operativo invitado, conservando su disponibilidad. En este artículo se tratan los detalles de este último paso: la actualización gradual.

## Actualización de un servicio de Azure

Azure organiza las instancias de rol en agrupaciones lógicas denominadas dominios de actualización (UD). Los dominios de actualización (UD) son conjuntos lógicos de instancias de rol que se actualizan como un grupo. Azure actualiza un servicio en la nube con los UD de uno en uno, lo que permite a las instancias de otros UD continuar atendiendo el tráfico.

El número predeterminado de dominios de actualización es 5. Puede especificar un número diferente de dominios de actualización incluyendo el atributo upgradeDomainCount en el archivo de definición de servicio (.csdef). Para obtener más información sobre el atributo upgradeDomainCount, consulte [Esquema WebRole](https://msdn.microsoft.com/library/azure/gg557553.aspx) o [Esquema WorkerRole](https://msdn.microsoft.com/library/azure/gg557552.aspx).

Al realizar una actualización local de uno o varios roles en el servicio, Azure actualiza los conjuntos de instancias de rol según el dominio de actualización al que pertenecen. Azure actualiza todas las instancias de un dominio de actualización dado (deteniéndolas, actualizándolas, conectándolas de nuevo), y luego pasa al dominio siguiente. Al detener solo las instancias que se ejecutan en el dominio de actualización en ese moment, Azure se asegura de que una actualización se realiza con el menor impacto posible en el servicio que está en ejecución. Para obtener más información, consulte [Cómo se realiza una actualización](https://msdn.microsoft.com/library/azure/Hh472157.aspx#proceed) más adelante en este artículo.

> [AZURE.NOTE]Aunque en el contexto de Azure hay pequeñas diferencias entre los distintos tipos de actualización, en este documento el término "actualización" se usa de forma general en las explicaciones de los procesos y las descripciones de las características.

El servicio tiene que definir al menos dos instancias de un rol para que ese rol se actualice localmente sin tiempo de inactividad. Si consta de una única instancia de un rol, el servicio no estará disponible hasta que haya finalizado la actualización local.

Este tema trata la siguiente información sobre las actualizaciones de Azure:

-   [Cambios de servicio permitidos durante una actualización](#AllowedChanges)
-   [Reversión de una actualización](#RollbackofanUpdate)
-   [Iniciación de varias operaciones mutantes en una implementación en curso](#multiplemutatingoperations)
-   [Distribución de roles entre dominios de actualización](#distributiondfroles)
-   [Cómo se realiza una actualización](#howanupgradeproceeds)

## Cambios de servicio permitidos durante una actualización
La siguiente tabla muestra los cambios permitidos en un servicio durante una actualización:

|Cambios permitidos en el hospedaje, servicios y roles|Actualización local|Por etapas (intercambio de VIP)|Eliminar y volver a implementar|
|---|---|---|---|
|Versión del sistema operativo|Sí|Sí|Sí
|Nivel de confianza de .NET|Sí|Sí|Sí|
|Tamaño de la máquina virtual|Sí*|Sí|Sí|
|Configuración de almacenamiento local|Solo aumentar *|Sí|Sí|
|Agregar o quitar roles en un servicio|Sí|Sí|Sí|
|Número de instancias de un rol concreto|Sí|Sí|Sí|
|Número o tipo de puntos de conexión de un servicio|Sí*|No|Sí|
|Nombres y valores de configuración|Sí|Sí|Sí|
|Valores (pero no nombres) de configuración|Sí|Sí|Sí|
|Incorporación de nuevos certificados|Sí|Sí|Sí|
|Cambio de certificados existentes|Sí|Sí|Sí|
|Implementación de nuevo código|Sí|Sí|Sí|
\*Requiere Azure SDK 1.5 o versiones posteriores.

> [AZURE.WARNING]Cambiar el tamaño de la máquina virtual destruirá los datos locales.


Los elementos siguientes no se admiten durante una actualización:

-   Cambio del nombre de un rol. Quitar y, a continuación, agregar el rol con el nuevo nombre.
-   Cambio del número de dominios de actualización.
-   Disminución del tamaño de los recursos locales.

Si está realizando otras actualizaciones a la definición del servicio, como reducir el tamaño del recurso local, debe realizar una actualización de intercambio de VIP en su lugar. Para obtener más información, consulte [Intercambiar implementaciones](https://msdn.microsoft.com/library/azure/ee460814.aspx).

## Cómo se realiza una actualización
Puede decidir si desea actualizar todos los roles en el servicio o un único rol. En cualquier caso, todas las instancias de cada rol que se está actualizando y que pertenecen al primer dominio de actualización se detienen, se actualizan y se vuelven a conectar. Una vez que vuelvan a conectarse, las instancias en el segundo dominio de actualización se detienen, se actualizan y se vuelven a conectar. Un servicio en la nube puede tener como mucho una actualización activa a la vez. La actualización se realiza siempre con la versión más reciente del servicio en la nube.

El siguiente diagrama ilustra cómo funciona la actualización si está actualizando todos los roles en el servicio:

![Actualización de servicio](media/cloud-services-update-azure-service/IC345879.png "Actualización de servicio")

El diagrama a continuación ilustra cómo tiene lugar la actualización si actualiza un único rol:

![Actualización de rol](media/cloud-services-update-azure-service/IC345880.png "Actualización de rol")

> [AZURE.NOTE]Al actualizar un servicio desde una instancia única a varias instancias, el servicio se desactiva mientras se realiza la actualización debido a la forma en la que Azure actualiza los servicios. La disponibilidad de servicio garantizada por el contrato de nivel de servicio solo se aplica a los servicios que se implementan con más de una instancia. En la lista siguiente se describe cómo afectará a los datos de cada unidad cada escenario de actualización de servicio de Azure:
>
>Reinicio de máquina virtual:
>
-   C: conservados
-   D: conservados
-   E: conservados
>
>Reinicio del Portal:
>
-   C: conservados
-   D: conservados
-   E: destruidos
>
>Restablecimiento de la imagen inicial del Portal:
>
-   C: conservados
-   D: destruidos
-   E: destruidos

>Actualización local:
>
-   C: conservados
-   D: conservados
-   E: destruidos
>
>Migración de nodos:
>
-   C: destruidos
-   D: destruidos
-   E: destruidos

>Tenga en cuenta que, en la lista anterior, la unidad E: representa la unidad raíz de rol y no debe ser de tipo no modificable. En su lugar, use la variable de entorno % RoleRoot % para representar la unidad.

>Para minimizar el tiempo de inactividad al actualizar un servicio de instancia única, implemente un nuevo servicio de varias instancias en el servidor de ensayo y realice un intercambio de VIP.

Durante una actualización automática, el controlador de tejido de Azure evalúa periódicamente el estado del servicio en la nube para determinar cuándo es seguro introducir el siguiente UD. Esta evaluación de estado se realiza por rol y tiene en cuenta únicamente las instancias de la versión más reciente (es decir, instancias de las UD que ya se han introducido). Comprueba que un número mínimo de instancias de rol, para cada rol, hayan alcanzado un estado terminal satisfactorio.

### Tiempo de espera de inicio de la instancia de rol 
Fabric Controller esperará 30 minutos a que cada instancia de rol llegue a un estado Iniciado. Si ha transcurrido la duración del tiempo de espera, Fabric Controller seguirá avanzando hasta la siguiente instancia de rol.

## Reversión de una actualización
Azure proporciona flexibilidad en la administración de servicios durante una actualización, permitiéndole iniciar operaciones adicionales en un servicio, una vez que el controlador de tejido de Azure acepta la solicitud de actualización inicial. Solo puede realizar una reversión cuando una actualización se encuentra en el estado de implementación **en curso**. Se considera que una actualización está en curso, siempre que haya por lo menos una instancia del servicio que aún no se ha actualizado a la nueva versión. Para ver si se permite una reversión, compruebe que el valor de la marca RollbackAllowed, devuelto por las operaciones [Obtener implementación](https://msdn.microsoft.com/library/azure/ee460804.aspx) y [Obtener propiedades de servicio en la nube](https://msdn.microsoft.com/library/azure/ee460806.aspx), está establecido en true.

> [AZURE.NOTE]Solo tiene sentido realizar una reversión en una actualización **local**, porque las actualizaciones de intercambio de VIP implican reemplazar una instancia en ejecución completa del servicio con otra.

La reversión de una actualización en curso tiene los efectos siguientes en la implementación:

-   Las instancias de rol que aún no se hayan actualizado a la nueva versión no se actualizan, porque esas instancias ya están ejecutando la versión de destino del servicio.
-   Las instancias de rol que ya se hayan actualizado a la nueva versión del archivo de paquete de servicio (\*.cspkg) o del archivo de configuración (\*.cscfg) (o ambos archivos), se revierten a la versión previa a la actualización de estos archivos.

Esta funcionalidad la proporcionan las características siguientes:

-   La operación [Actualización o reversión de la actualización](https://msdn.microsoft.com/library/azure/hh403977.aspx), que puede llamarse en una actualización de configuración (desencadenada al llamar a [Cambiar configuración de implementación](https://msdn.microsoft.com/library/azure/ee460809.aspx)) o una actualización (desencadenada al llamar a [Actualizar implementación](https://msdn.microsoft.com/library/azure/ee460793.aspx)) siempre que haya como mínimo una instancia en el servicio que aún no se haya actualizado a la nueva versión.
-   El elemento bloqueado y el elemento RollbackAllowed, que se devuelven como parte del cuerpo de respuesta de las operaciones [Obtener implementación](https://msdn.microsoft.com/library/azure/ee460804.aspx) y [Obtener propiedades de servicio en la nube](https://msdn.microsoft.com/library/azure/ee460806.aspx):
    1.  El elemento bloqueado permite detectar cuándo se puede invocar una operación de mutación en una implementación determinada.
    2.  El elemento RollbackAllowed permite detectar cuándo la operación de [Actualización o reversión de la actualización](https://msdn.microsoft.com/library/azure/hh403977.aspx) puede llamarse en una implementación determinada.

    Para realizar una reversión, no es necesario comprobar los dos elementos Locked y RollbackAllowed. Es suficiente con confirmar que RollbackAllowed está establecido en true. Estos elementos solo se devuelven si estos métodos se invocan usando el encabezado de solicitud establecido en "x-ms-version: 2011-10-01" o una versión posterior. Para obtener más información acerca de los encabezados de control de versiones, consulte [Control de versiones de la administración del servicio](https://msdn.microsoft.com/library/azure/gg592580.aspx).

Hay algunas situaciones en las que no se admite la reversión de una actualización, estas son las siguientes:

-   Reducción de recursos locales: si la actualización aumenta los recursos locales para un rol, la plataforma Azure no permite la reversión. Para obtener más información acerca de cómo configurar los recursos locales para un rol, consulte [Configuración de recursos de almacenamiento local](https://msdn.microsoft.com/library/azure/ee758708.aspx).
-   Limitaciones de cuota: si la actualización fue una operación de reducción vertical, es posible que no tenga una cuota de proceso suficiente para completar la operación de reversión. Cada suscripción a Azure tiene una cuota asociada que especifica el número máximo de núcleos que todos los servicios hospedados que pertenecen a dicha suscripción pueden usar. Si la reversión de una determinada actualización hace que su suscripción supere la cuota asignada, no se habilitará dicha reversión.
-   Condición de carrera: si se completó la actualización inicial, no es posible una reversión.

Un ejemplo de cuando la reversión de una actualización podría resultar útil es si está usando la operación [Actualizar implementación](https://msdn.microsoft.com/library/azure/ee460793.aspx) en el modo manual para controlar la velocidad a la que se realiza una actualización local importante en su servicio hospedado de Azure.

Durante la ejecución de la actualización se llama a [Actualizar implementación](https://msdn.microsoft.com/library/azure/ee460793.aspx) en modo manual y se comienzan a recorrer los dominios de actualización. Si en algún momento, mientras supervisa la actualización, se da cuenta de que algunas instancias de rol en los primeros dominios de actualización examinados han dejado de responder, puede llamar a la operación [Actualización o reversión de la actualización](https://msdn.microsoft.com/library/azure/hh403977.aspx) en la implementación, lo que dejará intactas las instancias que aún no se hayan actualizado y revertirá las instancias que se hayan actualizado al paquete de servicio y configuración anteriores.

## Iniciación de varias operaciones mutantes en una implementación en curso
En algunos casos puede que desee iniciar varias operaciones simultáneas de mutación en una implementación en curso. Por ejemplo, puede realizar una actualización del servicio y, mientras la actualización se está distribuyendo a través de su servicio, quiere realizar algún cambio, por ejemplo, para revertir la actualización, aplicar otra actualización o incluso eliminar la implementación. Un caso en el que esto podría ser necesario es si una actualización de servicio contiene código con errores que hace que una instancia de rol actualizada se bloquee repetidamente. En este caso, el controlador de tejido de Azure no puede progresar en la aplicación de esa actualización, ya que el número de instancias en el dominio actualizado que están en buen estado es insuficiente. Este estado se conoce como una *implementación bloqueada*. Puede desbloquear la implementación revirtiendo la actualización o aplicando una nueva actualización encima de la que tiene los errores.

Una vez que el controlador de tejido de Azure ha recibido la solicitud inicial para actualizar el servicio, puede iniciar las operaciones de mutación subsiguientes. Es decir, no es necesario esperar a que la operación inicial se complete antes de iniciar una operación de mutación.

Iniciar una segunda operación de actualización mientras se está realizando la primera actualización, funcionará de una forma similar a la operación de reversión. Si la segunda actualización está en modo automático, el primer dominio de actualización se actualizará inmediatamente, lo que posiblemente ocasionará que las instancias de varios dominios de actualización estén sin conexión al mismo tiempo.

Las operaciones de mutación son las siguientes: [Cambiar configuración de implementación](https://msdn.microsoft.com/library/azure/ee460809.aspx), [Actualizar implementación](https://msdn.microsoft.com/library/azure/ee460793.aspx), [Actualizar estado de la implementación ](https://msdn.microsoft.com/library/azure/ee460808.aspx), [Eliminar implementación](https://msdn.microsoft.com/library/azure/ee460815.aspx), y [Actualización o reversión de la actualización](https://msdn.microsoft.com/library/azure/hh403977.aspx).

Dos operaciones [Obtener implementación](https://msdn.microsoft.com/library/azure/ee460804.aspx) y [Obtener propiedades de servicio en la nube](https://msdn.microsoft.com/library/azure/ee460806.aspx), devuelven la marca de bloqueado que se puede examinar para determinar si una operación de mutación se puede llamar en una implementación determinada.

Para llamar a la versión de estos métodos que devuelve la marca de bloqueado, debe establecer el encabezado de solicitud a "x-ms-version: 2011-10-01" o una versión posterior. Para obtener más información acerca de los encabezados de control de versiones, consulte [Control de versiones de la administración del servicio](https://msdn.microsoft.com/library/azure/gg592580.aspx).

## Distribución de roles entre dominios de actualización
Azure distribuye las instancias de un rol uniformemente en un número determinado de dominios de actualización, que pueden configurarse como parte del archivo de definición (.csdef). El número máximo de dominios de actualización es 20 y el número predeterminado 5. Para obtener más información sobre cómo modificar el archivo de definición de servicio, consulte [Esquema de definición del servicio de Azure (archivo .csdef)](https://msdn.microsoft.com/library/azure/ee758711.aspx).

Por ejemplo, si su rol tiene diez instancias, de forma predeterminada cada dominio de actualización contiene dos instancias. Si su rol tiene 14 instancias, entonces cuatro de los dominios de actualización contienen tres instancias y un quinto dominio contiene dos.

Los dominios de actualización se identifican con un índice basado en cero: el primer dominio de actualización tiene un identificador de 0 y el segundo un identificador de 1 y así sucesivamente.

El siguiente diagrama ilustra cómo se distribuyen los dos roles que contiene un servicio, cuando el servicio define dos dominios de actualización. El servicio está ejecutando ocho instancias del rol web y nueve instancias del rol de trabajo.

![Distribución de dominios de actualización](media/cloud-services-update-azure-service/IC345533.png "Distribución de dominios de actualización")

> [AZURE.NOTE]Tenga en cuenta que Azure controla cómo se asignan las instancias en los dominios de actualización. No es posible especificar las instancias que se asignan a un dominio determinado.

## Pasos siguientes
[Administración de servicios en la nube](cloud-services-how-to-manage.md)<br>
[Supervisión de servicios en la nube](cloud-services-how-to-monitor.md)<br>
[Configuración de servicios en la nube](cloud-services-how-to-cofigure.md)<br>

<!---HONumber=Nov15_HO4-->
