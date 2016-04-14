<properties
   pageTitle="Instalación de Visual Studio y SSDT para Almacenamiento de datos SQL | Microsoft Azure"
   description="Instalación de herramientas de desarrollo de Visual Studio y SSDT para Almacenamiento de datos SQL de Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Instalación de Visual Studio 2015 y SSDT para Almacenamiento de datos SQL

Para desarrollar aplicaciones para Almacenamiento de datos SQL, se recomienda usar Visual Studio 2013 o versiones posteriores junto con la versión más reciente de SQL Server Data Tools (SSDT).

Para ejecutar consultas desde el entorno de desarrollo integrado (IDE) de Visual Studio, solo necesita instalar SSDT. Esto instalará el IDE de Visual Studio junto con SSDT, para que pueda usar el Explorador de objetos de SQL Server para conectarse al servidor Azure SQL Server. Después podrá ver y ejecutar consultas en las bases de datos de Almacenamiento de datos SQL.


## Paso 1: Descarga e instalación de Visual Studio

Si decide instalar Visual Studio, puede usar Visual Studio 2013 o Visual Studio 2015 con Almacenamiento de datos SQL. Si ya tiene Visual Studio 2013 o 2015 instalado, vaya al paso 2 para instalar SSDT.

Para instalar Visual Studio 2015, siga estos pasos:

1. [Descargue Visual Studio 2015](https://www.visualstudio.com/downloads) desde Visual Studio Team Services.
2. Siga la guía [Instalación de Visual Studio](https://msdn.microsoft.com/library/e2h7fzkw.aspx) en MSDN para elegir las configuraciones predeterminadas.

## Paso 2: Descarga e instalación de la versión más reciente de SQL Server Data Tools (SSDT)

Si no lo tiene instalado Visual Studio, necesitará la versión más reciente de SQL Server Data Tools (SSDT) que admita Almacenamiento de datos SQL.

Para instalar la versión más reciente de SSDT, siga estos pasos:

1. [Descargue SQL Server Data Tools Preview](https://msdn.microsoft.com/library/mt204009.aspx) para Visual Studio 2013 o 2015.
2. Instale siguiendo las instrucciones de instalación en el sitio de descarga.

## Pasos siguientes

Ahora que tiene la versión más reciente de SSDT, está listo para [conectar](./sql-data-warehouse-get-started-connect.md) la base de datos.

<!--Anchors-->

<!--Image references-->

<!---HONumber=AcomDC_0309_2016-->