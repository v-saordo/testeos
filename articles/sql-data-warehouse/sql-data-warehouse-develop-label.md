<properties
   pageTitle="Uso de etiquetas para instrumentar consultas en Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para usar etiquetas para instrumentar las consultas en Almacenamiento de datos SQL de Azure para el desarrollo de soluciones."
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

# Uso de etiquetas para instrumentar consultas en Almacenamiento de datos SQL
Almacenamiento de datos SQL admite un concepto conocido como etiquetas de consulta. Antes de entrar en materia, vamos a ver un ejemplo:

	```
	SELECT *
	FROM sys.tables
	OPTION (LABEL = 'My Query Label')
	;
	```

Esta última línea etiqueta la cadena 'Mi etiqueta de consulta' a la consulta. Esto es especialmente útil dado que la etiqueta se puede consultar a través de las DMV. De esta manera contamos con un mecanismo para realizar el seguimiento de los problemas y también para ayudar a identificar el progreso a través de una ejecución de ETL.

Una buena convención de nomenclatura realmente ayuda en este caso. Por ejemplo, algo como ' PROYECTO : PROCEDIMIENTO : INSTRUCCIÓN : COMENTARIO' ayudaría a identificar de forma única la consulta entre todo el código de control del código fuente.

Para buscar por etiqueta, puede usar la siguiente consulta que emplea las vistas de administración dinámica:

	```
	SELECT  *
	FROM    sys.dm_pdw_exec_requests r
	WHERE   r.[label] = 'My Query Label'
	;
	``` 

> [AZURE.NOTE]Es esencial que encierre entre corchetes o comillas dobles la etiqueta de la palabra al consultar. Etiqueta es una palabra reservada y producirá un error si no se ha delimitado.


## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0114_2016-->