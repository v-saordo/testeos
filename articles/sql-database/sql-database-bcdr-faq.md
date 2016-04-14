<properties 
   pageTitle="Preguntas frecuentes de continuidad de negocio de base de datos SQL" 
   description="Preguntas comunes y respuestas de clientes acerca de las características integradas y opcionales de continuidad de negocio y recuperación ante desastres de base de datos SQL de Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="02/09/2016"
   ms.author="elfish"/>

# Preguntas frecuentes sobre la continuidad del negocio

## 1\. ¿Qué le ocurre a mi período de retención del punto de restauración al cambiar a una versión anterior/actualizar por nivel de servicio?
Después de cambiar a un nivel de rendimiento inferior, el período de retención del punto de restauración cambiará inmediatamente al período de retención del nivel de rendimiento de la base de datos actual.

Si el nivel de servicio de la base de datos se actualiza, el período de retención se ampliará solo cuando se haya actualizado la base de datos.

Por ejemplo, si la base de datos se degrada de la versión P1 a S3, el período de retención cambiará inmediatamente de 35 días a 14 días, por lo que todos los puntos de restauración anteriores a 14 días dejarán de estar disponibles. Posteriormente, si se actualiza la base de datos de nuevo a la versión P1, el período de retención comenzará a partir de 14 días y hasta 35 días.

## 2\. ¿Cuánto dura el período de retención para una base de datos que se ha quitado? 
El período de retención se determina según el nivel de servicio de la base de datos mientras esta existe o según el número de días que exista la base de datos, el menor de estos dos valores.

## 3\. ¿Cómo puedo restaurar un servidor eliminado?

En estos momentos no es posible restaurar un servidor eliminado.

## 4\. ¿Cuánto se tarda en restaurar una base de datos?

El tiempo necesario para restaurar una base de datos depende de varios factores como, por ejemplo, el tamaño de la base de datos, el número de registro de transacciones, el ancho de banda de red, etc. La mayoría de las restauraciones de bases de datos dura unas 12 horas.

## 5\. ¿Puedo cambiar el período de retención de puntos de restauración de la base de datos?

Nº

## 6\. ¿Cómo puedo averiguar cuál es el punto de restauración disponible para mi base de datos?

Para la recuperación a causa de un error de usuario: la hora actual es el punto de restauración más reciente disponible. Para averiguar el punto de restauración disponible más antiguo, use la opción [Obtener base de datos](https://msdn.microsoft.com/library/dn505708.aspx) (*RecoveryPeriodStartDate*) para obtener el punto de restauración más antiguo (punto de restauración no replicado geográficamente).

Para la recuperación tras una interrupción: use la opción [Obtener base de datos recuperable](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) para obtener el último punto de restauración de replicación geográfica.

## 7\. ¿Cómo puedo restaurar de forma masiva restaurar las bases de datos de mi servidor?

No existe ninguna funcionalidad integrada para restaurar de forma masiva. El script denominado [Base de datos SQL de Azure: recuperación completa del servidor](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666), es un ejemplo de uno de los diferentes modos de realizar esta tarea.

## 8\. ¿Cuál es la diferencia entre la replicación geográfica estándar y la replicación geográfica activa?

En la replicación geográfica estándar, la base de datos secundaria no es legible. Solo está disponible para la conmutación por error durante las interrupciones.

En la replicación geográfica activa, todas las bases de datos secundarias son legibles (hasta 4 secundarias).

## 9\. ¿Cuál es el retraso de replicación cuando se utiliza la replicación geográfica estándar o la replicación geográfica activa?

Use la vista de administración dinámica (DMV) [sys.dm\_geo\_replication\_link\_status](https://msdnstage.redmond.corp.microsoft.com/library/mt575504.aspx) para obtener la última hora de la replicación, el retraso de la replicación y otro tipo de información acerca del vínculo de la replicación.

<!---HONumber=AcomDC_0211_2016-->