<properties 
	pageTitle="Preguntas más frecuentes sobre la escala elástica de SQL Azure | Microsoft Azure" 
	description="Preguntas más frecuentes sobre el Escalado elástico de Base de datos SQL de Azure." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/01/2016" 
	ms.author="sidneyh"/>

# Preguntas frecuentes de herramientas de Base de datos elástica 

#### Si tengo un solo inquilino por partición y ninguna clave de particionamiento, ¿cómo relleno la clave de particionamiento para la información de esquema?

El objeto de información del esquema solo se usa en escenarios de combinación o división. Si una aplicación es inherentemente de un solo inquilino, no requiere la herramienta de combinación o división y, por lo tanto, no es necesario rellenar el objeto de información del esquema.

#### He aprovisionado una base de datos y ya tengo un Administrador de asignación de particiones, ¿cómo registro esta nueva base de datos como una partición?

Consulte **[Incorporación de una partición a una aplicación mediante la biblioteca de cliente de Base de datos elástica](sql-database-elastic-scale-add-a-shard.md)**.

#### ¿Cuánto cuestan las herramientas de Base de datos elástica?

El uso de la biblioteca cliente de Base de datos elástica no incurre en costos. Los costos se acumulan solo para las bases de datos SQL de Azure que usa para particiones y el Administrador de asignación de particiones, así como los roles web/de trabajo aprovisionados para la herramienta de combinación o división.

#### ¿Por qué mis credenciales no funcionan cuando agrego una partición de un servidor diferente?
No use credenciales en forma de "Id. de Usuario=nombreusuario@nombreservidor", en su lugar, simplemente use "Id. de Usuario=nombre de usuario". Además, asegúrese de que el inicio de sesión de "nombre de usuario" tiene permisos en la partición.

#### ¿Necesito crear un Administrador de asignación de particiones y rellenar las particiones cada vez que inicie las aplicaciones?

No, la creación del Administrador de asignación de particiones (por ejemplo, **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) es una operación única. La aplicación debe usar la llamada **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)** en el momento que se inicie la aplicación. Solo debería haber una de estas llamadas por dominio de aplicación.

#### Tengo preguntas acerca del uso de las herramientas de Base de datos elástica, ¿cómo puedo obtener ayuda? 

Póngase en contacto con nosotros a través del [foro de Base de datos SQL de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

#### Al obtener una conexión de base de datos mediante una clave de particionamiento, puedo consultar los datos de otras claves de particionamiento en la misma partición. ¿Esto es así por defecto?

Las API de Escalado elástico ofrecen una conexión a la base de datos correcta para su clave de particionamiento, pero no proporcionan filtrado de claves de particionamiento. Agregue cláusulas **WHERE** a su consulta para restringir el ámbito a la clave de particionamiento proporcionada, si es necesario.

#### ¿Puedo usar una edición diferente de Base de datos de Azure para cada partición en mi conjunto de particiones?

Sí, una partición es una base de datos individual y, por lo tanto, una partición podría ser una edición Premium y otra una edición Standard. Además, la edición de una partición puede escalar verticalmente o reducirse verticalmente varias veces durante la duración de la partición.

#### ¿La herramienta de combinación o división aprovisiona (o elimina) una base de datos durante una operación de combinación o división? 

No. En el caso de las operaciones de **división**, la base de datos de destino debe existir con el esquema apropiado y registrarse con el Administrador de asignación de particiones. En el caso de las operaciones de **combinación**, debe eliminar la partición desde el Administrador de asignación de particiones y, luego, eliminar la base de datos.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=AcomDC_0204_2016-->