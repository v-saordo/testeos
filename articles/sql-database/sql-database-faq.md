<properties 
   pageTitle="Preguntas más frecuentes sobre la Base de datos SQL de Azure" 
   description="Respuestas a las preguntas más comunes que los clientes preguntan sobre las bases de datos en la nube y Base de datos SQL de Azure, el sistema de administración de bases de datos relacionales (RDBMS) de Microsoft y bases de datos como un servicio en la nube." 
   services="sql-database" 
   documentationCenter="" 
   authors="jeffgoll" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="02/25/2016"
   ms.author="sashan"/>

# Preguntas más frecuentes sobre la Base de datos SQL

## ¿Cómo aparece el uso que hago de la Base de datos SQL en la factura? 
Base de datos SQL se factura con tarifas por horas predecibles basadas en el nivel de servicio + nivel de rendimiento para bases de datos únicas o en eDTU por grupo de bases de datos elásticas. El uso real se procesa y se prorratea por horas, por lo que su factura podría mostrar fracciones de una hora. Por ejemplo, si una base de datos existe durante 12 horas al mes, la factura mostrará un uso de 0,5 días. Además, los niveles de servicio y de nivel de rendimiento, así como las eDTU por grupo se desglosan en la factura para que pueda ver más fácilmente el número de días que usó la base de datos para cada elemento, por mes.

## ¿Qué ocurre si una base de datos única está activa durante menos de una hora o usa un nivel de servicio mayor durante menos de una hora?
Se le cobrará por cada hora que una base de datos exista con el mayor nivel de servicio + nivel de rendimiento aplicable durante esa hora, independientemente del uso o de si la base de datos estuvo activa durante menos de una hora. Por ejemplo, si crea una base de datos única y la elimina a los 5 minutos, se le efectuará un cargo de 1 hora por usar la base de datos.

Ejemplos
	
- Si crea una base de datos Básica y la actualiza inmediatamente a Estándar S1, se le cobrará la tarifa Estándar S1 por la primera hora.

- Asimismo, si actualiza una base de datos de Básica a Premium a las 22:00 h y la actualización termina a la 01:35 h del día siguiente, se le cobrará la tarifa Premium a partir de la 01:00 h.

- Si degrada una base de datos de Premium a Básica a las 11:00 h y se completa a las 14:15 h, la base de datos se cargará a la tarifa Premium hasta las 15:00 h y, después, se cargará a la tarifa Básica.

## ¿Cómo se muestra el uso del grupo de bases de datos elásticas en la factura y qué ocurre al cambiar a eDTU por grupo?
Los cargos por grupo de bases de datos elásticas se muestran en la factura como DTU elásticas (eDTU) con los incrementos que se indican en eDTU por grupo en la [página de precios](https://azure.microsoft.com/pricing/details/sql-database/). No se realizan cargos por base de datos para los grupos de bases de datos elásticas. Se le cobrará por cada hora que un grupo exista en la eDTU más alta, independientemente del uso o de si el grupo estuvo activo durante menos de una hora.

Ejemplos

- Si crea un grupo de bases de datos elásticas Estándar con 200 eDTU a las 11:18 horas, al agregar cinco bases de datos al grupo se le cobrarán 200 eDTU por la hora completa, empezando a las 11:00 h, durante el resto del día.
- El día 2, a las 05:05 h, la base de datos 1 empieza a consumir 50 eDTU y se mantiene constante durante todo el día. Las bases de datos 2 a 5 fluctúan entre 0 y 80 eDTU. Durante el día se agregan otras cinco bases de datos con un consumo variable de eDTU durante todo el día. El día 2 es un día completo por el que se facturan 200 eDTU. 
- El día 3, a las 05:00 h, se agregan otras 15 bases de datos. El uso de las bases de datos aumenta a lo largo del día hasta el punto en que decide aumentar las eDTU para el grupo de 200 a 400 a las 20:05 h. Los cargos en el nivel de 200 eDTU estaban en vigor hasta las 20:00 h y aumenta a 400 eDTU para las 4 restantes. 

## ¿Cómo se muestra en mi factura el uso de la replicación geográfica en un grupo de bases de datos elásticas?
A diferencia de las bases de datos únicas, el uso de replicación geográfica con bases de datos elásticas no afecta a la facturación. Solo se cobrarán las eDTU aprovisionadas para cada uno de los grupos (grupo principal y grupo secundario).

## ¿Cómo afectará a mi factura el uso de la característica de auditoría? 
La auditoría está integrada en el servicio de la Base de datos SQL sin coste adicional y está disponible para las bases de datos Básica, Estándar y Premium. Sin embargo, para almacenar los registros de auditoría, la característica de auditoría usa una cuenta de Almacenamiento de Azure y se aplican tarifas por tablas y colas de Almacenamiento de Azure en función del tamaño de su registro de auditoría.

## ¿Cómo puedo encontrar el nivel de rendimiento y de nivel de servicio adecuado para las bases de datos únicas y los grupos de bases de datos elásticas? 
Dispone de algunas herramientas.

- Para las bases de datos locales, use el [Asesor de ajuste de tamaño de DTU](http://dtucalculator.azurewebsites.net/), que recomienda las bases de datos y las DTU necesarias y evalúa varias bases de datos para grupos de bases de datos elásticas.
- Si una base de datos única se beneficiaría de estar en un grupo, el motor inteligente de Azure recomendará un grupo de bases de datos elásticas si ve un patrón de uso histórico que lo garantiza. Para obtener más información acerca de su funcionamiento, consulte los [Grupos de bases de datos elásticas recomendados](sql-database-elastic-pool-portal.md#recommended-elastic-database-pools). Consulte [Consideraciones de precio y rendimiento para un grupo de bases de datos elásticas](sql-database-elastic-pool-guidance.md), y así poder realizar los cálculos matemáticos por su cuenta.
- Para ver si necesita ajustar una base de datos única hacia arriba o hacia abajo, consulte la [guía de rendimiento para bases de datos únicas](sql-database-performance-guidance.md).

## ¿Con qué frecuencia se puede cambiar el nivel de servicio o el nivel de rendimiento de una base de datos única? 
Si usa las bases de datos V12, puede cambiar el nivel de servicio (entre Básica, Estándar y Premium) o el nivel de rendimiento dentro de un nivel de servicio (por ejemplo, de S1 a S2) siempre que quiera. En cuanto a las versiones anteriores de las bases de datos, puede cambiar el nivel de servicio o el nivel de rendimiento un total de cuatro veces en un período de 24 horas.

##¿Con qué frecuencia se pueden ajustar las eDTU por cada grupo? 
Tan a menudo como desee.

## ¿Cuánto tiempo se tarda en cambiar el nivel de nivel de servicio o de rendimiento de una base de datos única o en incorporar o retirar una base de datos de un grupo de bases de datos elásticas? 
Para cambiar el nivel de servicio de una base de datos e incorporarla y retirarla de un grupo es necesario copiar la base de datos en la plataforma como operación en segundo plano. Esta opción puede tardar desde solo unos minutos hasta varias horas, según el tamaño de las bases de datos. En ambos casos, las bases de datos permanecen en línea y disponibles durante el traslado. Para obtener información acerca de cómo cambiar las bases de datos únicas, consulte [Cambiar el nivel de servicio de una base de datos](sql-database-scale-up.md). Para las bases de datos elásticas, consulte [Referencia de grupos elásticos](sql-database-elastic-pool-reference.md#latency-of-elastic-pool-operations).

##¿Cuándo se deben usar bases de datos elásticas y cuándo una base de datos única? 
En general, los grupos de bases de datos elásticas están diseñados para un patrón de aplicación de software como servicio (SaaS) típico, donde hay una base de datos por cliente o inquilino. Adquirir bases de datos individuales y aprovisionarlas en exceso para cubrir todas las variaciones y la demanda máxima no resulta rentable. Con los grupos, el rendimiento colectivo del grupo y el escalado horizontal y reducción vertical de las bases de datos se administra automáticamente.

El motor inteligente de Azure recomendará un grupo para las bases de datos si ve un patrón de uso que lo garantiza. Para obtener más información, consulte los [Grupos de bases de datos elásticas recomendados](sql-database-elastic-pool-portal.md#recommended-elastic-database-pools). Para obtener instrucciones detalladas acerca de cómo elegir entre bases de datos únicas y elásticas, consulte [Consideraciones de precio y rendimiento para grupos de bases de datos elásticas](sql-database-elastic-pool-guidance.md).

## ¿Qué significa tener hasta un 200 % del almacenamiento de base de datos aprovisionado máximo para almacenar copias de seguridad? 
El almacenamiento de copias de seguridad es el almacenamiento asociado a las copias de seguridad de las bases de datos automatizadas que se usan para la restauración a un momento dado y para la restauración geográfica. La Base de datos SQL de Microsoft Azure le proporciona hasta un 200 % de almacenamiento de base de datos aprovisionado máximo, para que pueda almacenar copias de seguridad sin coste adicional. Por ejemplo, si tiene una instancia de base de datos de tipo Estándar con un tamaño de base de datos aprovisionado de 250 GB, se le proporcionarán 500 GB para almacenar sus copias de seguridad sin coste adicional. Si su base de datos excede el tamaño del almacenamiento de copias de seguridad suministrado, puede elegir reducir el período de retención poniéndose en contacto con el Servicio técnico de Azure o pagar el almacenamiento de copias de seguridad extra; si se decide por la segunda opción, esta se facturará según la tarifa de almacenamiento de redundancia geográfica con acceso de lectura (RA-GRS) estándar. Para obtener más información sobre la facturación RA-GRS, consulte los Detalles de los precios de almacenamiento.

## Me cambio de Web/Business a los nuevos niveles de servicio, ¿qué necesito saber?
Las bases de datos Web y Business de Azure se han retirado. Los niveles Basic, Standard, Premium y Elastic son los que reemplazan las bases de datos Web y Business. Tenemos Preguntas más frecuentes adicionales que le ayudarán en este período de transición. [Preguntas frecuentes relacionadas con la retirada de las versiones Web y Business](sql-database-web-business-sunset-faq.md)

## ¿Qué es un retraso de replicación esperado al replicar geográficamente una base de datos entre dos regiones dentro de la misma ubicación geográfica de Azure?  
Actualmente admitimos un RPO de cinco segundos, y el retraso de replicación debe ser inferior a este valor mientras la base de datos secundaria geográficamente esté hospedada en la región emparejada y sea del mismo nivel de servicio.

## ¿Qué es un retraso de replicación esperado cuando la base de datos secundaria geográficamente se crea en la misma región que la base de datos principal?  
Según los datos empíricos, no hay demasiada diferencia entre el retraso de replicación dentro de una región y entre regiones cuando se utiliza la región emparejada recomendada de Azure.

## Si hay un error de red entre dos regiones, ¿cómo funciona la lógica de reintento cuando está configurada la replicación geográfica?  
Si se produce una desconexión, intentamos volver a establecer las conexiones cada 10 segundos.

## ¿Qué puedo hacer para tener la seguridad de que se replica un cambio importante en la base de datos principal?
La base de datos secundaria geográficamente es una réplica asincrónica y no intentaremos mantenerla sincronizada completamente con la principal. Sin embargo, proporcionamos un método para forzar la sincronización. Este método está diseñado para garantizar la replicación de cambios importantes (por ejemplo, actualizaciones de contraseña). Afectará al rendimiento ya que bloqueará el subproceso de llamada hasta que todas las transacciones confirmadas se repliquen. Para más información, consulte [sp\_wait\_for\_database\_copy\_sync](https://msdn.microsoft.com/library/dn467644.aspx).

## ¿Qué herramientas están disponibles para supervisar el retraso de replicación entre la base de datos principal y la base de datos secundaria geográficamente?
Exponemos el retraso de replicación en tiempo real entre la base de datos principal y la base de datos secundaria geográficamente mediante una DMV. Para más información, consulte [sys.dm\_geo\_replication\_link\_status](https://msdn.microsoft.com/library/mt575504.aspx).

<!---HONumber=AcomDC_0302_2016-->