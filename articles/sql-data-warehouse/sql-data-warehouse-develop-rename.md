<properties
   pageTitle="Cambio de nombre en el Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para cambiar el nombre de tablas en el Almacenamiento de datos SQL de Azure para desarrollar soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# Cambio de nombre en el Almacenamiento de datos SQL
Aunque SQL Server permite cambiar el nombre de base de datos mediante el procedimiento almacenado ```sp_renamedb```, el Almacenamiento de datos SQL usa la sintaxis DDL para lograr el mismo objetivo. El comando DDL es ```RENAME OBJECT```.

## Cambio de nombre de una tabla

Actualmente, solo puede cambiarse el nombre de tablas. La sintaxis para cambiar el nombre de una tabla es:

```
RENAME OBJECT Customer TO NewCustomer;
```


Al cambiar el nombre de una tabla, se actualizan todos los objetos y las propiedades asociados a la tabla para hacer referencia al nuevo nombre de la tabla. Por ejemplo, se actualizan las definiciones, los índices, las restricciones y los permisos de la tabla. Las vistas no se actualizan.

## Cambio de nombre de una tabla externa

Si se cambia el nombre de una tabla externa, cambia el nombre de tabla en PDW de SQL Server. No afecta a la ubicación de los datos externos de la tabla.

## Cambio del esquema de una tabla
Si se pretende cambiar el esquema al que un objeto pertenece, esto se consigue mediante ALTER SCHEMA:

```
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```

## El cambio de nombre de una tabla exige el bloqueo exclusivo de la tabla.

Es importante recordar que no se puede cambiar el nombre de una tabla mientras se usa. El cambio de nombre de una tabla requiere un bloqueo exclusivo en la tabla. Si la tabla se está usando, puede que tenga que terminar la sesión en que se usa la tabla. Para terminar una sesión deberá usar el comando [KILL](https://msdn.microsoft.com/library/ms173730.aspx). Tenga cuidado al usar ```KILL```, ya que al terminar la sesión se revertirá cualquier trabajo no confirmado. Las sesiones de Almacenamiento de datos SQL llevan el prefijo 'SID'. Tendrá que incluirlo con el número de sesión al invocar el comando KILL. Por ejemplo, ```KILL 'SID1234'```.


## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md

<!---HONumber=AcomDC_0114_2016-->