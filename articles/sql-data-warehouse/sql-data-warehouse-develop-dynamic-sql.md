<properties
   pageTitle="SQL dinámico en Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para usar SQL dinámico en Almacenamiento de datos SQL Azure para desarrollar soluciones."
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

# SQL dinámico en Almacenamiento de datos SQL
Al desarrollar código de aplicación para Almacenamiento de datos SQL, puede que necesite usar SQL dinámico con el fin de proporcionar soluciones flexibles, genéricas y modulares. Almacenamiento de datos SQL no admite por el momento tipos de datos blob. Esto puede limitar el tamaño de las cadenas ya que los tipos de blob incluyen tipos varchar (max) y nvarchar (max). Si ha utilizado estos tipos en el código de aplicación al crear cadenas muy grandes, será necesario que divida el código en fragmentos y utilice en su lugar la instrucción EXEC.

Un ejemplo sencillo:

```
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Si la cadena es corta, puede usar [sp\_executesql][] como de costumbre.

> [AZURE.NOTE]Las instrucciones ejecutadas como SQL dinámico seguirán sujetas a todas las reglas de validación de TSQL.

## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp\_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0114_2016-->