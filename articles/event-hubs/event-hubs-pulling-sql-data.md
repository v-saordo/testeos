<properties
   pageTitle="Extracción de datos SQL para Centros de eventos de Azure | Microsoft Azure"
   description="Información general de la importación a Centros de eventos desde un ejemplo SQL"
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor=""/>

<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/26/2016"
   ms.author="spyros;spyrossak" />

# Extracción de datos de SQL a un Centro de eventos de Azure

Una arquitectura típica para que una aplicación procese datos en tiempo real implica primero su inserción en un Centro de eventos de Azure. Puede ser un escenario de IoT, como la supervisión del tráfico en distintos tramos de una carretera, o un escenario de juego, como la supervisión de las acciones de una multitud de concursantes frenéticos, o un escenario empresarial, como la supervisión de la ocupación del edificio. En estos casos, los productores de datos suelen ser objetos externos que producen datos de serie temporal que hay que recopilar, analizar, almacenar y actuar en consecuencia, y puede haya invertido mucho esfuerzo en la construcción de la infraestructura para estos procesos. ¿Qué sucede si desea traer datos de algo como una base de datos en lugar de un origen de datos de streaming, y usarlos con los demás datos de streaming? ¿Se plantea la posibilidad de usar Análisis de transmisiones de Azure, Explorador de datos remotos (RDX), o cualquier otra herramienta para analizar y actuar en datos de variación lenta procedentes de Microsoft Dynamics AX o de un sistema personalizado de administración de instalaciones? Para solucionar este problema, hemos escrito y publicado como código abierto un pequeño ejemplo en la nube que puede modificar e implementar, que extraerá los datos de una tabla SQL y los insertará en un Centro de eventos de Azure para usarlos como entrada en las aplicaciones analíticas auxiliares. Tenga en cuenta que este es un escenario poco frecuente y lo opuesto a lo que hace normalmente con un Centro de eventos. Pero en caso de que esto sea lo que tiene que hacer, puede encontrar el código en la Galería de ejemplos de Azure, [aquí](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-import-from-sql/).

Tenga en cuenta que el código de este ejemplo es sólo eso, un ejemplo. **No** pretende ser una aplicación de producción y no se ha intentado de ningún modo hacerlo adecuado para su uso en un entorno de este tipo; se trata estrictamente de un ejemplo dirigido al desarrollador para que lo pruebe por sí mismo. Debe revisar los factores relacionados con la seguridad, el rendimiento, la funcionalidad y el costo antes de decidirse por una arquitectura integral.

## Estructura de la aplicación

La aplicación está escrita en C# y el archivo readme.md del ejemplo contiene toda la información necesaria para modificar, crear y publicar la aplicación. En las secciones siguientes se proporciona información general de alto nivel sobre lo que hace la aplicación.

Partimos del supuesto de que tiene acceso a to a una tabla SQL de Azure. También debe configurar un Centro de eventos de Azure y conocer la cadena de conexión necesaria para tener acceso al mismo.

Cuando se inicia la solución SqlToEventHub, esta lee un archivo de configuración (App.config) para obtener varios elementos, tal como se describe en el archivo readme.md: Estos son bastante descriptivos, como el nombre de la tabla de datos, etc., y no hay que recombinar aquí las explicaciones.

Una vez que la aplicación ha leído el archivo de configuración, entra en un bucle, que lee la tabla SQL e inserta registros en el Centro de eventos, y luego espera un intervalo de espera definido por el usuario antes de repetirlo todo. Algunas cosas que conviene destacar:

1. La aplicación se basa en el supuesto de que algún proceso externo actualiza la tabla SQL y solo se quieren enviar todas las actualizaciones a un Centro de eventos.
2. La tabla SQL debe tener un campo que incluya un número único y creciente, por ejemplo, un número de registro. Puede ser tan sencillo como un campo denominado "Id", o cualquier otra cosa que se incremente cuando lo que actualice la base de datos agregue registros como "Creation\_time" o "Sequence\_number". La aplicación anota y almacena el valor de ese campo en cada iteración. En cada pasada posterior por el bucle, la aplicación consulta básicamente en la tabla todos los registros donde el valor de ese campo supere el valor que vio la última vez a través del bucle. A este último valor se lo llamamos el "desplazamiento".
3. La aplicación crea una tabla "TableOffsets" cuando se inicia, para almacenar los desplazamientos. La tabla se crea con la consulta "CreateOffsetTableQuery" definida en el archivo de configuración. 
4. Hay varias consultas que se usan para trabajar con la tabla de desplazamientos, que se definen en el archivo de configuración como "OffsetQuery", "UpdateOffsetQuery" e "InsertOffsetQuery". No debe cambiarlas.
5. Por último, la consulta "DataQuery" definida en el archivo de configuración es la consulta que se va a ejecutar para extraer los registros de la tabla SQL. Actualmente está limitada con fines de optimización a los primeros 1000 registros en cada pasada por el bucle; si, por ejemplo, se han agregado 25 000 registros a la base de datos desde la última consulta, se podría tardar un rato para ejecutar la consulta. Al limitar la consulta a 1000 registros por vez, las consultas son mucho más rápidas. Al seleccionar los primeros 1000, simplemente inserta lotes sucesivos de 1000 registros en el Centro de eventos.    

## Pasos siguientes

Para implementar la solución, clone o descargue la aplicación SqlToEventHub, edite el archivo App.config, compílelo y finalmente, publíquelo. Una vez publicada la aplicación, puede verla ejecutándose en el Portal de Azure clásico, en Servicios en la nube, y supervisar los eventos que entran en el Centro de eventos. Tenga en cuenta que la frecuencia vendrá determinada por dos cosas: la frecuencia de las actualizaciones de la tabla SQL y el intervalo de suspensión que haya especificado en el archivo de configuración de la aplicación.

<!---HONumber=AcomDC_0302_2016-->