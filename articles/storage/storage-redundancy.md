
<properties 
  pageTitle="Replicación de almacenamiento de Azure | Microsoft Azure" 
  description="Los datos de su cuenta de almacenamiento de Microsoft Azure se replican para garantizar la durabilidad y la alta disponibilidad. Entre las opciones de replicación se incluyen el almacenamiento con redundancia local (LRS), el almacenamiento con redundancia de zona (ZRS), el almacenamiento con redundancia geográfica (GRS) y el almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS)." 
  services="storage" 
  documentationCenter="" 
  authors="tamram" 
  manager="carmonm" 
  editor="tysonn"/>

<tags 
  ms.service="storage" 
  ms.workload="storage" 
  ms.tgt_pltfrm="na" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.date="02/17/2016" 
  ms.author="tamram"/>

# Replicación de almacenamiento de Azure

Los datos de la cuenta de almacenamiento de Microsoft Azure siempre se replican para garantizar la durabilidad y la alta disponibilidad, y cumplen el [contrato de nivel de servicio de Almacenamiento de Azure](https://azure.microsoft.com/support/legal/sla/storage) incluso en caso de errores de hardware transitorios.

Cuando cree una cuenta de almacenamiento, debe seleccionar una de las siguientes opciones de replicación:

- [Almacenamiento con redundancia local (LRS)](#locally-redundant-storage)
- [Almacenamiento con redundancia de zona (ZRS)](#zone-redundant-storage)
- [Almacenamiento con redundancia geográfica (GRS)](#geo-redundant-storage)
- [Almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS).](#read-access-geo-redundant-storage)

La siguiente tabla proporciona una breve descripción de las diferencias entre LRS, ZRS, GRS y RA-GRS, mientras que secciones subsiguientes abordan con más detalle cada tipo de replicación.


| Estrategia de replicación | LRS | ZRS | GRS | RA-GRS |
|:-----------------------------------------------------------------------------------|:----|:----|:----|:-------|
| Los datos se replican entre varias instalaciones | No | Sí | Sí | Sí |
| Los datos se pueden leer desde la ubicación secundaria al igual que desde la ubicación principal | No | No | No | Sí |
| Cantidad de copias de datos mantenidas en nodos independientes | 3 | 3 | 6 | 6 |


## Almacenamiento con redundancia local

El almacenamiento con redundancia local (LRS) replica los datos dentro de la región en la que creó su cuenta de almacenamiento. Para maximizar la durabilidad, cada solicitud hecha respecto de los datos en la cuenta de almacenamiento se replica tres veces. Cada una de estas tres réplicas reside en dominios de error y dominios de actualización independientes. Un dominio de error (FD) es un grupo de nodos que representan una unidad física de error y se pueden considerar como nodos pertenecientes a la misma estantería. Un dominio de actualización (UD) es un grupo de nodos que se actualizan en conjunto durante el proceso de actualización de un servicio (implementación). Las tres réplicas se distribuyen entre dominios UD y FD para garantizar que los datos se encuentran disponibles incluso si un error de hardware afecta a una sola estantería y cuando los nodos se actualizan durante una implementación. Una solicitud se devuelve correctamente solo una vez que se ha escrito a todas las tres réplicas.

A pesar que el almacenamiento con redundancia geográfica (GRS) se recomienda para la mayoría de las aplicaciones, el almacenamiento con redundancia local puede ser deseable en ciertos escenarios:

- LRS es menos costoso que GRS y también ofrece un mayor rendimiento. Si su aplicación almacena datos que se pueden reconstruir fácilmente, puede optar por LRS.

- Algunas aplicaciones están restringidas a la replicación de datos solo dentro de una región debido a requisitos de gobernanza de datos.

- Si la aplicación tiene su propia estrategia de replicación geográfica, es posible que no requiera GRS.


## Almacenamiento con redundancia de zona

El almacenamiento con redundancia de zona (ZRS) replica sus datos entre dos o tres instalaciones, ya sea dentro de una sola región o entre dos regiones, lo que proporciona una mayor durabilidad que LRS. Si su cuenta de almacenamiento tiene habilitado ZRS, sus datos se conservan incluso ante un error en una de las instalaciones.


>[AZURE.NOTE]  Actualmente, el ZRS solo está disponible para los blobs en bloques y únicamente se admite en las versiones del 14/02/2014 y posteriores. Tenga en cuenta que una vez que haya creado la cuenta de almacenamiento y haya seleccionado la replicación con redundancia de zona, no podrá convertirla para usar cualquier otro tipo de aplicación (o viceversa).


## Almacenamiento con redundancia geográfica

El almacenamiento con redundancia geográfica (GRS) replica sus datos a una región secundaria que se encuentra a cientos de kilómetros de distancia de la región primaria. Si la cuenta de almacenamiento tiene habilitado GRS, sus datos se mantienen incluso ante un apagón regional completo o un desastre del cual la región principal no se puede recuperar.

En el caso de una cuenta de almacenamiento con GRS habilitado, una actualización primero se envía a la región principal, donde se replica tres veces. Luego la actualización se replica a la región secundaria, donde también se replica tres veces, entre dominios de error y dominios de actualización independientes.


> [AZURE.NOTE] Con GRS, las solicitudes para escribir datos se replican de manera asincrónica a la región secundaria. Es importante tener en cuenta que optar por GRS no afecta la latencia de las solicitudes realizadas respecto de la región principal. Sin embargo, como la replicación asincrónica implica un retraso, ante la eventualidad de un desastre regional es posible que los cambios que todavía no se hayan replicado a la región secundaria se pierdan si no es posible recuperar los datos desde la región principal.
 
Cuando crea una cuenta de almacenamiento, selecciona la región principal de la cuenta. La región secundaria se determina según la región principal y no es posible cambiarla. La siguiente tabla muestra los emparejamientos de la región principal y la secundaria.
 
| Principal | Secundario |
|---------------------|---------------------|
| Centro-Norte de EE. UU | Centro-Sur de EE. UU |
| Centro-Sur de EE. UU | Centro-Norte de EE. UU |
| Este de EE. UU. | Oeste de EE. UU. |
| Oeste de EE. UU. | Este de EE. UU. |
| Este de EE. UU. - 2 | Central EE. UU.: |
| Central EE. UU.: | Este de EE. UU. - 2 |
| Europa del Norte | Europa occidental |
| Europa occidental | Europa del Norte |
| Sudeste de Asia | Asia oriental |
| Asia oriental | Sudeste de Asia |
| Este de China | Norte de China |
| Norte de China | Este de China |
| Este de Japón | Oeste de Japón |
| Oeste de Japón | Este de Japón |
| Sur de Brasil | Centro-Sur de EE. UU |
| Australia Oriental | Sudeste de Australia |
| Sudeste de Australia | Australia Oriental |
| Sur de India | India central |
| India central | Sur de India |


## Almacenamiento con redundancia geográfica con acceso de lectura

El almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) maximiza la disponibilidad para la cuenta de almacenamiento al proporcionar acceso de solo lectura a los datos de la ubicación secundaria, además de la replicación entre dos regiones que proporciona GRS. Ante la eventualidad de que los datos dejen de estar disponibles en la región principal, la aplicación puede leer datos desde la región secundaria.

Cuando habilita el acceso de solo lectura a los datos en la región secundaria, los datos quedan disponibles en un extremo secundario, además del extremo principal para la cuenta de almacenamiento. El punto de conexión secundario es similar al punto de conexión principal, pero anexa el sufijo `–secondary` al nombre de la cuenta. Por ejemplo, si el punto de conexión principal del servicio BLOB es `myaccount.blob.core.windows.net`, el punto de conexión secundario es `myaccount-secondary.blob.core.windows.net`. Las claves de acceso de la cuenta de almacenamiento son iguales para los extremos principal y secundario.

## Pasos siguientes

- [Acerca de las cuentas de almacenamiento de Azure](storage-create-storage-account.md)
- [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure](storage-scalability-targets.md)
- [Opciones de redundancia de Almacenamiento de Microsoft Azure y Almacenamiento con redundancia geográfica con acceso de lectura](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)  
- [Emulador de almacenamiento de Microsoft Azure 3.1 con RA-GRS ](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/08/microsoft-azure-storage-emulator-3-1-with-ra-grs.aspx)
- [Documento de SOSP: Almacenamiento de Azure: un servicio de almacenamiento en la nube altamente disponible con gran coherencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)  

<!---HONumber=AcomDC_0218_2016-->