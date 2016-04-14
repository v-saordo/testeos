<properties 
   pageTitle="Instrucciones y limitaciones generales de Base de datos SQL de Azure"
   description="En esta página se describen algunas limitaciones generales para Base de datos SQL de Azure, así como las áreas de interoperatividad y compatibilidad."
   services="sql-database"
   documentationCenter="na"
   authors="rothja"
   manager="jeffreyg"
   editor="monicar" />
<tags 
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="01/15/2016"
   ms.author="jroth" />

# Instrucciones y limitaciones generales de Base de datos SQL de Azure

En este tema se describen las instrucciones y las limitaciones generales de Base de datos SQL de Azure. Para obtener una visión global que le ayude a comprender las cuotas, la administración de recursos y el soporte técnico, consulte la sección [recursos adicionales](#additional-guidelines) al final de este tema.

## Conectividad

 - La autenticación de Windows no es compatible. Consulte [Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure](sql-database-manage-logins.md). 

 - La Base de datos SQL de Microsoft Azure admite el cliente de protocolo de secuencia de datos tabular (TDS) en la versión 7.3 o posterior.

 - Se permiten únicamente las conexiones TCP/IP.

 - El explorador de SQL Server 2008 no se admite porque la Base de datos SQL de Microsoft Azure no tiene puertos dinámicos, solo el puerto 1433.

## Trabajos/Agente SQL Server

Base de datos SQL de Microsoft Azure no admite trabajos o Agente SQL Server. Sin embargo, puede ejecutar el Agente SQL Server en su SQL Server local y conectarse a la Base de datos SQL de Microsoft Azure.

## Compatibilidad con la intercalación de SQL Server

La intercalación de bases de datos predeterminada que utiliza Base de datos SQL de Microsoft Azure es **SQL\_LATIN1\_GENERAL\_CP1\_CI\_AS**, donde **LATIN1\_GENERAL** es inglés (Estados Unidos), **CP1** es la página de códigos 1252, **CI** indica que no se distingue mayúsculas de minúsculas y **AS** especifica que se distinguen los acentos. Es posible modificar la intercalación de bases de datos de V12 mediante Transact-SQL. Para obtener más información acerca de cómo establecer la intercalación, consulte [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## Requisitos de nomenclatura

Algunos nombres de usuario no se permiten por razones de seguridad. No puede usar los siguientes nombres:

 - **admin** 
 - **administrator** 
 - **guest** 
 - **root** 
 - **sa** 

Los nombres de todos los objetos nuevos deben cumplir las reglas de SQL Server para los identificadores. Para obtener más información, consulte [Identificadores](https://msdn.microsoft.com/library/ms175874.aspx).

Además, los nombres de inicio de sesión y el usuario no pueden contener el carácter \\ (no se admite la autenticación de Windows).

## Directrices adicionales

- Además de las limitaciones generales descritas en este artículo, la Base de datos SQL tiene cuotas y limitaciones de recursos específicas según el **nivel de servicio** seleccionado. Para obtener una descripción general de los niveles de servicio, consulte [Niveles de servicio de la Base de datos SQL](sql-database-service-tiers.md).

- Para conocer los límites de la Base de datos SQL, consulte [Límites de recursos de la Base de datos SQL](sql-database-resource-limits.md).

- Para obtener directrices relacionadas con la seguridad, consulte [Instrucciones y limitaciones de seguridad de la Base de datos SQL de Azure](sql-database-security-guidelines.md).

- Otro asunto relacionado hace referencia a la compatibilidad de Base de datos SQL de Azure con las versiones locales de SQL Server, como SQL Server 2014. La última versión V12 de Base de datos SQL de Azure ha conseguido muchas mejoras en este sentido. Para obtener más detalles, consulte [Novedades de Base de datos SQL V12](sql-database-v12-whats-new.md).

- Para obtener información sobre la disponibilidad de controladores y la compatibilidad con Base de datos SQL, consulte [Bibliotecas de conexiones para Base de datos SQL y SQL Server](sql-database-libraries.md).

<!---HONumber=AcomDC_0121_2016-->