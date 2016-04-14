<properties
   pageTitle="Asignación de variables en el Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para la asignación de variables de Transact-SQL en el Almacenamiento de datos SQL Azure para desarrollar soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Asignación de variables en el Almacenamiento de datos SQL
Las variables en el Almacenamiento de datos SQL se establecen mediante las instrucciones `DECLARE` o `SET`.

A continuación se indican formas totalmente válidas para establecer el valor de una variable:

## Configuración de variables con DECLARE

Inicializar variables con DECLARE es una de las maneras más flexibles de establecer el valor de una variable en el Almacenamiento de datos SQL.

```
DECLARE @v  int = 0
;
```

También puede utilizar DECLARE para establecer más de una variable a la vez. No se puede utilizar `SELECT` o `UPDATE` para ello:

```
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

No se puede inicializar y utilizar una variable en la misma instrucción DECLARE. Para ilustrar esta cuestión, el ejemplo siguiente **no** está permitido como @p1, porque se inicializa y se utiliza en la misma instrucción DECLARE. Esto generará un error.

```
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## Configuración de valores con SET
SET es un método muy común para configurar una sola variable.

Todos los ejemplos siguientes son formas válidas de establecer una variable con SET:

```
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Solo puede establecer una variable al mismo tiempo con SET. Sin embargo, como hemos visto anteriormente, se admiten los operadores compuestos.

## Limitaciones
Puede usar SELECT o UPDATE para la asignación de variables.


## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0114_2016-->