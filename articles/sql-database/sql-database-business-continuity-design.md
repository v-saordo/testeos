<properties 
   pageTitle="Diseño de la base de datos SQL para la continuidad del negocio" 
   description="Directrices para realizar su elección En esta sección se proporcionan directrices que le ayudarán a elegir las características BCDR que deben usarse, así como el momento en que deben usarse. Esto incluye descripciones de lo que obtendrá automáticamente con el uso de la base de datos SQL."
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

#Diseño para la continuidad del negocio

El diseño de su aplicación para la continuidad del negocio requiere que responda a las preguntas siguientes:

1. ¿Qué característica de continuidad del negocio es la más adecuada para proteger mi aplicación frente a las interrupciones?
2. ¿Qué nivel de topología de replicación y redundancia debo usar?

##Cuándo se debe usar la restauración geográfica

Las bases de datos SQL proporcionan una protección integrada básica de todas las bases de datos de manera predeterminada. Dicha protección se obtiene al almacenar las copias de seguridad de la base de datos en el almacenamiento de Azure con redundancia geográfica (GRS). Si elige este método, no es necesaria ninguna configuración especial o asignación de recursos adicionales. Con estas copias de seguridad, podrá recuperar la base de datos en cualquier región mediante el comando de restauración geográfica. Vea la sección [Recuperación tras una interrupción](sql-database-disaster-recovery.md) para obtener información sobre los detalles del uso de la restauración geográfica para recuperar la aplicación.

Deberá usar la protección integrada si su aplicación cumple los criterios siguientes:

1. No se considera crítica. No tiene un SLA vinculante, por lo que no incurrirá en responsabilidades financieras en caso de tiempo de inactividad de 24 horas o más.
2. La tasa de cambio de los datos es baja (por ejemplo de transacciones por cada hora). El RPO de 1 hora no dará como resultado pérdidas de datos masivas.
3. La aplicación es sensible a los costes, por lo que el coste adicional de la replicación geográfica no está justificado. 

> [AZURE.NOTE] La restauración geográfica no asigna previamente la capacidad informática a ninguna región en particular para restaurar bases de datos activas de la copia de seguridad durante la interrupción. El servicio administrará la carga de carga de trabajo asociada a las solicitudes de restauración geográfica de forma que se minimice el impacto en las bases de datos existentes en dicha región, por lo que sus demandas de capacidad tendrán prioridad. Por lo tanto, el tiempo de recuperación de la base de datos dependerá del número de bases de datos que se recuperarán en la misma región de manera simultánea.

##Cuándo se debe usar la replicación geográfica

La replicación geográfica crea una base de datos de réplica (secundaria) en una región distinta de la principal. Esta replicación garantiza que la base de datos tendrá los datos y los recursos necesarios para admitir la carga de trabajo de la aplicación después del proceso de recuperación. Vea la sección[Recuperación tras una interrupción](sql-database-disaster-recovery.md) para obtener información sobre el uso de la conmutación por error para recuperar su aplicación.

Deberá usar la replicación geográfica si su aplicación cumple los criterios siguientes:

1. Es crítica. Tiene un SLA vinculante con unos valores de RTO y RPO agresivos. La pérdida de datos y de disponibilidad incurriría en responsabilidades financieras. 
2. La tasa de cambio de los datos es alta (por ejemplo de transacciones por cada minuto o segundo). El RPO de 1 hora asociado a la protección predeterminada probablemente tendrá como resultado una pérdida de datos inaceptable.
3. El coste asociado al uso de la replicación geográfica es significativamente menor que el de la responsabilidad financiera potencial y la pérdida de negocio.

> [AZURE.NOTE] La replicación geográfica no es compatible si su aplicación usa bases de datos de nivel básico.

##Cuándo elegir la replicación geográfica estándar vs la replicación geográfica activa

Las bases de datos de nivel Standard no admiten la opción de usar la replicación geográfica activa, por lo que si su aplicación usa bases de datos Standard y cumple los criterios anteriores, deberá usar la replicación geográfica estándar. Por el contrario, con las bases de datos Premium, se pueden elegir cualquiera de las opciones. La replicación geográfica estándar se ha diseñado como una solución de recuperación ante desastres más sencilla y menos costosa. Resulta especialmente adecuada para aplicaciones que usan la replicación solo para la protección frente a eventos imprevistos como, por ejemplo, las interrupciones. Para realizar la recuperación con la replicación geográfica estándar, solo podrá usar la región emparejada de recuperación ante desastres y tan solo podrá crear una base de datos secundaria por cada base de datos principal que tenga. Es posible que una base de datos secundaria adicional sea necesaria para el escenario de actualización de la aplicación. Por ello, si este escenario es fundamental para su aplicación, deberá habilitar la replicación geográfica activa. Vea la sección [Actualización de la aplicación sin tiempo de inactividad](sql-database-business-continuity-application-upgrade.md) para obtener más detalles.

> [AZURE.NOTE] La replicación geográfica activa también admite el acceso de solo lectura a la base de datos secundaria, lo que proporciona capacidad adicional para las cargas de trabajo de solo lectura.

##Cómo habilitar la replicación geográfica

Puede habilitar la replicación geográfica mediante el Portal de Azure clásico, mediante una llamada a la API de REST o mediante el comando de PowerShell.

###Portal de Azure

[AZURE.VIDEO sql-database-enable-geo-replication-in-azure-portal]

1. Inicie sesión en el [Portal de Azure](https://portal.Azure.com).
2. En el lado izquierdo de la pantalla, seleccione **EXAMINAR** y, a continuación, seleccione **Bases de datos SQL**.
3. Desplácese hasta la hoja de su base de datos, seleccione el **Mapa de replicación geográfica** y haga clic en **Configurar replicación geográfica**.
4. Desplácese hasta la hoja de replicación geográfica. Seleccione la región de destino. 
5. Desplácese hasta la hoja Crear base de datos secundaria. Seleccione un servidor existente en la región de destino o cree uno nuevo.
6. Seleccione el tipo de base de datos secundaria (*legible* o *no legible*).
7. Haga clic en **Crear** para completar la configuración.

> [AZURE.NOTE] La región emparejada de recuperación ante desastres de la hoja de replicación geográfica se marcará como *Recomendada*. Si usa una base de datos de nivel Premium, podrá elegir una región diferente. Si está usando una base de datos Standard, no podrá cambiar esta opción. La base de datos Premium ofrece la opción de especificar el tipo de base de datos secundaria (*legible* o *no legible*). La base de datos Standard solo permite seleccionar una base de datos secundaria *no legible*.


###PowerShell

Use el cmdlet de PowerShell [New-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) para crear una configuración de la replicación geográfica. Este comando es sincrónico y se devuelve cuando las bases de datos principales y secundarias están sincronizadas.

Configurar la replicación geográfica con una base de datos secundaria no legible, para una base de datos Premium o Standard:
		
    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "None"

Para crear la replicación geográfica con una base de datos secundaria legible para una base de datos Premium:

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"
		 

###API de REST 

Use la API [Crear base de datos](https://msdn.microsoft.com/library/mt163685.aspx) con el elemento *createMode* establecido como *NonReadableSecondary* o *Secondary*, para crear mediante programación una base de datos secundaria de replicación geográfica.

Esta API es asincrónica. Cuando vuelva, use la API [Obtener vínculo de replicación](https://msdn.microsoft.com/library/mt600778.aspx) para comprobar el estado de la operación. El campo *ReplicationState* del cuerpo de la respuesta tendrá el valor CATCHUP cuando se complete la operación.


##Cómo elegir la configuración de conmutación por error 

Al diseñar la aplicación para la continuidad del negocio, debe tener en cuenta varias opciones de configuración. La elección dependerá de la topología de la implementación de la aplicación y de las partes de las aplicaciones que sean más vulnerables a las interrupciones. Consulte [Diseño de soluciones de nube para la recuperación ante desastres mediante la replicación geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md), para obtener más instrucciones.

<!---HONumber=AcomDC_0211_2016-->