<properties
   pageTitle="Planificación de continuidad del negocio en Almacenamiento de datos SQL | Microsoft Azure"
   description="Información general sobre la continuidad del negocio en Almacenamiento de datos SQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="02/17/2016"
   ms.author="sahajs;barbkess;sonyama"/>


# Planificación de continuidad del negocio en Almacenamiento de datos SQL

En este artículo se describen los conceptos básicos de planificación de continuidad del negocio y recuperación ante desastres con el Almacenamiento de datos SQL. Los almacenamientos de datos constituyen el repositorio central de información para empresas y desempeñan un papel fundamental en las operaciones diarias de análisis e inteligencia empresarial en todos los niveles de la organización. Por lo tanto, es esencial que el almacenamiento de datos sea confiable y permita la recuperación y la operación continua. En concreto, este artículo le mostrará cómo Almacenamiento de datos SQL le permite recuperarse ante desastres y errores del usuario con Restaurar base de datos y Restauración geográfica.

## Conceptos

Es necesario optimizar la continuidad del negocio y los planes de recuperación ante desastres para lo siguiente:

**Objetivo de tiempo de recuperación (RTO)**: máximo tiempo aceptable antes de que la base de datos se recupere por completo tras una interrupción; es decir, máxima pérdida de disponibilidad durante los errores.

**Objetivo de punto de recuperación (RPO)**: máxima ventana de tiempo aceptable de actualizaciones perdidas cuando la base de datos se recupera tras una interrupción; es decir, máxima pérdida de datos durante los errores.

**Tiempo estimado de recuperación (ERT):** duración estimada para que la base de datos esté completamente operativa tras una solicitud de restauración.

## Escenarios de continuidad del negocio

**Recuperación de errores de infraestructura:** este escenario se refiere a la recuperación de problemas de infraestructura tales como, errores de disco, etc. Un cliente quiere garantizar la continuidad del negocio mediante una infraestructura de alta disponibilidad con tolerancia a errores.

**Recuperación de errores de usuario:** este escenario se refiere a la recuperación de datos dañados o eliminados de manera fortuita o involuntaria. Por si un usuario modificara o eliminara datos de manera fortuita o involuntaria, un cliente desea tener la opción de garantizar la continuidad del negocio mediante la restauración de la base de datos a un momento anterior en el tiempo.

**Recuperación ante desastres (DR):** este escenario se refiere a la recuperación de una catástrofe grave. Por si se diera el caso de que una interrupción, como un desastre natural o un apagón regional, hiciera que la base de datos dejase de estar disponible, un cliente desea tener la opción de recuperar la base de datos en otra región para continuar con las operaciones del negocio.


## Características de la continuidad del negocio

Veamos cómo Almacenamiento de datos SQL mejora la confiabilidad de la base de datos y permite la recuperación y la operación continua en los escenarios anteriormente mencionados.


### Redundancia de datos

Puesto que Almacenamiento de datos SQL separa el proceso y el almacenamiento, todos los datos se escriben directamente en el Almacenamiento de Azure con redundancia geográfica (RA-GRS). El almacenamiento con redundancia geográfica replica sus datos a una región secundaria que se encuentra a cientos de kilómetros de distancia de la región principal. Los datos se replican tres veces en cada región primaria y secundaria, en dominios de error y en dominios de actualización independientes. Esto garantiza que los datos perduran incluso en el caso de un apagón regional completo o un desastre que haga que una de las regiones deje de estar disponible. Para obtener más información sobre almacenamiento con redundancia geográfica con acceso de lectura, lea [Opciones de redundancia de Almacenamiento de Azure][].

### Restauración de base de datos

La restauración de base de datos está diseñada para restablecer la base de datos a un punto anterior en el tiempo. El servicio Almacenamiento de datos SQL de Azure protege todas las bases de datos con instantáneas de almacenamiento automáticas cada 8 horas como mínimo y las conserva durante 7 días para ofrecerle un conjunto discreto de puntos de restauración. Estas copias de seguridad se almacenan en el Almacenamiento de Azure RA-GRS y, por tanto, tienen redundancia geográfica de forma predeterminada. Las características de copia de seguridad y restauración automáticas se incluyen sin cargos adicionales y permiten proteger las bases de datos de daños o eliminaciones accidentales sin costos ni esfuerzos de administración. Para obtener más información sobre la restauración de bases de datos, consulte [Recuperación de errores de usuario][].

### Restauración geográfica

La restauración geográfica se ha diseñado para recuperar la base de datos en caso de que deje de estar disponible por una interrupción. Póngase en contacto con el soporte técnico para restaurar una base de datos desde una copia de seguridad con redundancia geográfica para crear una nueva base de datos en cualquier región de Azure. Dado que la copia de seguridad tiene redundancia geográfica, puede usarse para recuperar una base de datos aunque la base de datos sea inaccesible debido a una interrupción. La característica de restauración geográfica no incluye ningún cargo adicional.


## Pasos siguientes
Para obtener información sobre las características de continuidad del negocio de otras ediciones de Base de datos SQL, lea la sección [Información general sobre la continuidad del negocio de Base de datos SQL][].

<!--Image references-->

<!--Article references-->
[business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Finalize a recovered database]: ../sql-database/sql-database-recovered-finalize.md
[Opciones de redundancia de Almacenamiento de Azure]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[Información general sobre la continuidad del negocio de Base de datos SQL]: ../sql-database/sql-database-business-continuity.md
[Recuperación de errores de usuario]: sql-data-warehouse-business-continuity-recover-from-user-error.md

<!--MSDN references-->
[Create database restore request]: http://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: http://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: http://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: http://msdn.microsoft.com/library/azure/dn509562.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0218_2016-->