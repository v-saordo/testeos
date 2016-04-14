<properties 
	pageTitle="Puertos más allá de 1433 para Base de datos SQL | Microsoft Azure"
	description="Las conexiones de cliente de ADO.NET a Base de datos SQL de Azure V12 omiten al proxy e interactúan directamente con la base de datos. Los puertos que no sean 1433 se convierten en puertos importantes."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jhubbard"
	editor="" />


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2016" 
	ms.author="genemi"/>


# Puertos más allá de 1433 para ADO.NET 4.5 y Base de datos SQL V12


En este tema se describen los cambios que la Base de datos SQL V12 de Azure aporta el comportamiento de conexión de los clientes que usan ADO.NET 4.5 o una versión posterior.


## V11 de la Base de datos SQL: puerto 1433


Cuando su programa de cliente usa ADO.NET 4.5 para conectarse y consultar con la Base de datos SQL V11, la secuencia interna es la siguiente:


1. ADO.NET intenta conectarse a la Base de datos SQL.

2. ADO.NET usa el puerto 1433 para llamar a un módulo de middleware y el middleware se conecta a la base de datos SQL.

3. La Base de datos SQL devuelve su respuesta al middleware, que la reenvía a ADO.NET para el puerto 1433.


**Terminología:** describimos la secuencia anterior indicando que ADO.NET interactúa con Base de datos SQL mediante la *ruta de proxy*. Si no hay ningún middleware implicado diríamos que se usó la *ruta directa*.


## V12 de Base de datos SQL: fuera frente a dentro


Para las conexiones a V12 debemos preguntar si el programa cliente se ejecuta *fuera* o *dentro* del límite de la nube de Azure. En las subsecciones se describen dos escenarios comunes.


#### *Fuera:* el cliente se ejecuta en el equipo de escritorio


El puerto 1433 es el único puerto que debe estar abierto en su equipo de escritorio que hospeda su aplicación de cliente de la Base de datos SQL.


#### *Dentro:* el cliente se ejecuta en Azure


Cuando el cliente se ejecuta dentro del límite de la nube de Azure, usa lo que podemos llamar una *ruta directa* para interactuar con el servidor de Base de datos SQL. Cuando se ha establecido una conexión, las interacciones posteriores entre el cliente y la base de datos no implican ningún proxy de middleware.


La secuencia es la siguiente:


1. ADO.NET 4.5 (o posterior) inicia una breve interacción con la nube de Azure y recibe un número de puerto identificado dinámicamente.
 - El número de puerto identificado dinámicamente se encuentra en el intervalo de 11000-11999 o 14000-14999.

2. Luego, ADO.NET se conecta al servidor de Base de datos SQL directamente, sin ningún middleware entre ellos.

3. Las consultas se envían directamente a la base de datos y los resultados se devuelven directamente al cliente.


Asegúrese de que los intervalos de puertos 11000 a 11999 y 14000 a 14999 en la máquina cliente de Azure quedan disponible para las interacciones de cliente de ADO.NET 4.5 con Base de datos SQL V12.

- En concreto, los puertos del intervalo deben estar libres de cualquier otro bloqueador de salida.

- En la máquina virtual de Azure, **Firewall de Windows con seguridad avanzada** controla la configuración de puertos.
 - Puede usar la [interfaz de usuario del firewall](http://msdn.microsoft.com/library/cc646023.aspx) para agregar una regla para la que se especifique el protocolo **TCP** junto con un intervalo de puertos con una sintaxis similar a **11000-11999**.


## Aclaraciones de versiones


En esta sección se explican los monikers que hacen referencia a las versiones de producto. También se muestran algunos emparejamientos de versiones entre productos.


#### ADO.NET


- ADO.NET 4.0 admite el protocolo TDS 7.3, pero no 7.4.
- ADO.NET 4.5 y versiones posteriores admite el protocolo TDS 7.4.


#### Base de datos SQL V11 y V12


En este tema se resaltan las diferencias de conexión de cliente entre la Base de datos SQL V11 y V12.


*Nota:* La instrucción Transact-SQL `SELECT @@version;` devuelve un valor que comienza por un número como '11.' o '12.', que coinciden con los nombres de nuestra versión V11 y V12 para Base de datos SQL.


## Vínculos relacionados


- ADO.NET 4.6 se publicó el 20 de julio de 2015. Hay un anuncio de blog del equipo de .NET disponible [aquí](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).


- ADO.NET 4.5 se publicó el 15 de julio de 2012. Hay un anuncio de blog del equipo de .NET disponible [aquí](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
 - Hay una entrada de blog sobre ADO.NET 4.5.1 disponible [aquí](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).


- [Lista de versiones del protocolo TDS](http://www.freetds.org/userguide/tdshistory.htm)


- [Conexión a la base de datos SQL: vínculos, prácticas recomendadas y directrices de diseño](sql-database-connect-central-recommendations.md)


- [Firewall de Base de datos SQL de Azure](sql-database-firewall-configure.md)


- [Configuración del firewall en Base de datos SQL](sql-database-configure-firewall-settings.md)

<!---HONumber=AcomDC_0211_2016-->