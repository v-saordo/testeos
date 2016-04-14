<properties
   pageTitle="Herramientas de administración para Almacenamiento de datos SQL | Microsoft Azure"
   description="Introducción a las herramientas de administración para Almacenamiento de datos SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="HappyNicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="mausher;nicw;barbkess;jrj;sonyama;"/>

# Herramientas de administración para Almacenamiento de datos SQL
En este tema se examinan y comparan distintas herramientas y opciones para administrar Almacenamiento de datos SQL, con objeto de que se elija la herramienta adecuada para cada necesidad específica. Elegir la herramienta adecuada depende del número de bases de datos que administre, la tarea y la frecuencia con la que se realiza una tarea.

## Portal de Azure clásico
El [Portal de Azure clásico][] es un portal clásico basado en web donde se pueden crear, actualizar y eliminar bases de datos, así como supervisar recursos de bases de datos. Es una herramienta muy útil si se está empezando a trabajar con Azure, se administran pocas bases de datos de almacenamiento de datos o hay que hacer algo rápidamente.

El portal incluye métricas que abarcan la configuración de DWU de rendimiento actual e histórica, la cantidad de almacenamiento que se está usando, las conexiones SQL correctas e incorrectas y un conjunto de datos y elementos visuales que permiten entender las consultas que se ejecutan en la instancia, así como los detalles de dichas consultas.

## SQL Server Data Tools en Visual Studio	
[SQL Server Data Tools][] (SSDT) en Visual Studio es una herramienta de cliente que se ejecuta en el equipo y permite conectarse a una base de datos en la nube, así como administrarla y desarrollarla. Si es un programador familiarizado con Visual Studio o con otros entornos de desarrollo integrado (IDE), pruebe a usar SSDT en Visual Studio.

SSDT incluye el Explorador de SQL Server, que permite visualizar, conectar y ejecutar scripts en bases de datos de Almacenamiento de datos SQL. Para conectarse rápidamente a Almacenamiento de datos SQL, simplemente haga clic en el botón **Abrir en Visual Studio** de la barra de comandos al visualizar los detalles de la base de datos en el Portal de Azure clásico.

Puede descargar la versión más reciente de [SQL Server Data Tools][] (SSDT), que es compatible con Almacenamiento de datos SQL.

## Herramientas de línea de comandos
Una opción es usar las herramientas de línea de comandos de PowerShell o sqlcmd para administrar Almacenamiento de datos SQL y automatizar las implementaciones de recursos de Azure. Se recomienda el uso de estas herramientas para administrar un gran número de servidores lógicos e implementar cambios en los recursos en un entorno de producción, ya que las tareas necesarias se pueden realizar mediante scripts y automatizar.

## Pasos siguientes
Para comenzar a usar estas herramientas, vaya al tema [conexión][].

<!--Image references-->

<!--Article references-->
[conexión]: sql-data-warehouse-develop-connections.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Portal de Azure clásico]: http://portal.azure.com/

<!---HONumber=AcomDC_0114_2016-->