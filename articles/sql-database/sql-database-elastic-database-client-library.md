<properties
    pageTitle="Base de datos SQL de Azure: biblioteca de cliente"
    description="Crear aplicaciones de bases de datos de .NET escalables"
    services="sql-database"
    documentationCenter=""
    manager="jeffreyg"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/01/2016"
    ms.author="ddove;sidneyh"/>

# Información general de la biblioteca de cliente de bases de datos elásticas

La **biblioteca de cliente de bases de datos elásticas** le ayuda a desarrollar fácilmente aplicaciones compartidas usando cientos, o incluso miles, de bases de datos de SQL de Azure hospedadas en Microsoft Azure. Estos diseños se usan normalmente para aplicaciones de Software como servicio (SaaS), que son generalmente arquitecturas de inquilino único, donde cada inquilino se aprovisiona con una base de datos. Uno de los objetivos de la biblioteca es crear y administrar este tipo de aplicación.

Biblioteca de cliente de Base de datos elástica está ahora disponible como software de código abierto en [GitHub](https://github.com/Azure/elastic-db-tools). Para instalar la biblioteca, consulte [Base de datos SQL de Microsoft Azure: escala elástica](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). La biblioteca de cliente forma parte de las herramientas de Base de datos elástica, que es una [característica específica de Base de datos elástica](sql-database-elastic-scale-introduction.md).

## Capacidades de cliente

Desarrollar, escalar y administrar aplicaciones de escalado horizontal mediante *particionamiento* (se trata a continuación) presenta desafíos para el desarrollador y para el administrador. La biblioteca de cliente facilita las operaciones de estos roles. En la ilustración siguiente se describen las funciones principales que brinda la biblioteca cliente de bases de datos elásticas. La ilustración muestra un entorno con muchas bases de datos, donde cada base de datos corresponde a una partición. En este ejemplo, se colocan muchos clientes en la misma base de datos con un mapa de intervalo, aunque lo mismo se aplica si hay una base de datos para cada cliente (inquilino). Las herramientas facilitan el desarrollo de aplicaciones de Base de datos SQL de Azure con particiones mediante las siguientes capacidades específicas:

Si desea obtener las definiciones de los términos que se usan aquí, consulte el [glosario de las herramientas de bases de datos elásticas](sql-database-elastic-scale-glossary.md).

![Capacidades de Escalado elástico][1]

1.  **Administración de mapas de particiones**: para administrar una colección de particiones, se crea una base de datos especial denominada "administrador de mapas de particiones". es la capacidad que tiene una aplicación de administrar distintos metadatos acerca de sus particiones. Los desarrolladores pueden usar esta funcionalidad para registrar bases de datos como particiones, describir las asignaciones de claves de particionamiento individuales o intervalos de claves para esas bases de datos y mantener estos metadatos a medida que la cantidad y la composición de bases de datos evoluciona para reflejar los cambios en la capacidad. Sin la biblioteca de cliente de bases de datos flexible, necesitará dedicar mucho tiempo a escribir el código de administración al implementar el particionamiento. Para obtener más información, consulte [Administración de mapas de particiones](sql-database-elastic-scale-shard-map-management.md).

* **Enrutamiento dependiente de los datos**: imagine que la aplicación recibe una solicitud. Según el valor de clave de partición de la solicitud, la aplicación necesita determinar la base de datos correcta que contiene los datos para este valor de clave y luego abrir una conexión a ella para procesar la solicitud. El enrutamiento dependiente de los datos proporciona la capacidad de abrir conexiones con una sola llamada simple al mapa de particiones de la aplicación. El enrutamiento dependiente de los datos era otra área del código de infraestructura que ahora está cubierta por la funcionalidad de la biblioteca de cliente de bases de datos elásticas. Para obtener más información, consulte [Enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md).

* **Consultas a través de particiones múltiples (MSQ)**: las consultas a través de particiones múltiples cuando una solicitud implica varias particiones (o todas). Una consulta a través de particiones múltiples ejecuta el mismo código T-SQL en todas las particiones o en un conjunto de particiones. Los resultados de las particiones implicadas se combinan en un conjunto de resultados global usando la semántica UNION ALL. La funcionalidad que se expone a través de la biblioteca de cliente gestiona muchas tareas, como: administración de conexiones, administración de subprocesos, gestión de errores y procesamiento de resultados intermedios. MSQ puede consultar hasta cientos de particiones. Para obtener más información, consulte [Consultas a través de particiones múltiples](sql-database-elastic-scale-multishard-querying.md).

En general, los clientes que usan las herramientas de bases de datos elásticas pueden esperar obtener toda la funcionalidad de T-SQL al enviar las operaciones de partición local en lugar de las operaciones entre particiones que tienen su propia semántica.

## Pasos siguientes

Pruebe la [aplicación de ejemplo](sql-database-elastic-scale-get-started.md) que muestra las funciones de cliente.

Para instalar la biblioteca, vaya a la [biblioteca de cliente de Base de datos elástica](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Para obtener instrucciones sobre cómo usar la herramienta de división y combinación, consulte la [información general de la herramienta de división y combinación](sql-database-elastic-scale-overview-split-and-merge.md).

[La biblioteca de cliente de bases de datos elásticas tiene ahora código abierto.](https://azure.microsoft.com/blog/elastic-database-client-library-is-now-open-sourced/)


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]: ./media/sql-database-elastic-database-client-library/glossary.png

<!---HONumber=AcomDC_0204_2016-->