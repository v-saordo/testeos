<properties
    pageTitle="Almacenamiento premium de Azure: diseño de alto rendimiento | Microsoft Azure"
    description="Diseñe aplicaciones de alto rendimiento con Almacenamiento premium de Azure. El Almacenamiento premium le ofrece compatibilidad con discos de alto rendimiento y baja latencia para cargas de trabajo con un uso intensivo de E/S, que se ejecutan en máquinas virtuales de Azure."
    services="storage"
    documentationCenter="na"
    authors="ms-prkhad"
    manager=""
	editor="tysonn" />

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/20/2016"
    ms.author="prkhad"/>

# Almacenamiento premium de Azure: diseño de alto rendimiento

## Información general  
Este artículo proporciona instrucciones para crear aplicaciones de alto rendimiento con Almacenamiento premium de Azure. Puede usar las instrucciones proporcionadas en este documento junto con los procedimientos recomendados de rendimiento aplicables a las tecnologías usadas por la aplicación. Para ilustrar las directrices, hemos usado SQL Server en Almacenamiento premium como ejemplo en este documento.

Si bien en este artículo se tratan los escenarios de rendimiento de la capa de almacenamiento, deberá optimizar la capa de la aplicación. Por ejemplo, si hospeda una granja de SharePoint en Almacenamiento premium de Azure, puede usar los ejemplos de SQL Server de este artículo para optimizar el servidor de bases de datos. Además, optimice el servidor web de la granja de SharePoint y el servidor de aplicaciones para obtener el máximo rendimiento.

Este artículo le ayudará a responder a las siguientes preguntas habituales acerca de cómo optimizar el rendimiento de las aplicaciones en Almacenamiento premium de Azure:

-   ¿Cómo medir el rendimiento de las aplicaciones?  
-   ¿Por qué no se ve el alto rendimiento esperado?  
-   ¿Qué factores influyen en el rendimiento de las aplicaciones en Almacenamiento premium?  
-   ¿Cómo influyen estos factores en el rendimiento de las aplicaciones en Almacenamiento premium?  
-   ¿Cómo puede optimizar para IOPS, el ancho de banda y la latencia?  

Proporcionamos estas directrices específicamente para Almacenamiento premium porque las cargas de trabajo que se ejecutan en Almacenamiento premium dependen mucho del rendimiento. Se proporcionan ejemplos donde corresponda. También puede aplicar algunas de estas instrucciones a las aplicaciones que se ejecutan en máquinas virtuales de IaaS con discos de Almacenamiento estándar.

Antes de comenzar, si no está familiarizado con Almacenamiento premium, lea primero los artículos [Almacenamiento premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](storage-premium-storage.md) y [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure](storage-scalability-targets.md#premium-storage-accounts).

## Indicadores del rendimiento de las aplicaciones  
Evaluamos si una aplicación tiene un buen rendimiento o no mediante indicadores de rendimiento como: la rapidez con la que una aplicación procesa una solicitud del usuario, la cantidad de datos que una aplicación procesa por solicitud, cuántas solicitudes procesa una aplicación en un determinado período, cuánto tiempo tiene que esperar un usuario para obtener una respuesta después de enviar su solicitud. Los términos técnicos para estos indicadores de rendimiento son IOPS, latencia y rendimiento o ancho de banda.

En esta sección, trataremos los indicadores de rendimiento comunes en el contexto de Almacenamiento premium. En la siguiente sección, Reunión de los requisitos de rendimiento de las aplicaciones, obtendrá información sobre cómo medir estos indicadores de rendimiento de las aplicaciones. Más adelante, en Optimización del rendimiento de las aplicaciones, obtendrá información acerca de los factores que afectan a estos indicadores de rendimiento y recomendaciones para optimizarlos.

## E/S  
IOPS es el número de solicitudes que la aplicación envía a los discos de almacenamiento en un segundo. Una operación de entrada y salida se puede de lectura o escritura, secuencial o aleatoria. Las aplicaciones OLTP como un sitio web de venta directa en línea necesita procesar muchas solicitudes de usuario simultáneas inmediatamente. Las solicitudes de usuario suponen insertar y actualizar transacciones con un uso intensivo de las bases de datos y que la aplicación debe procesar rápidamente. Por lo tanto, las aplicaciones OLTP requieren IOPS muy alta. Dichas aplicaciones controlan millones de solicitudes de E/S pequeñas y aleatorias. Si tiene este tipo de aplicación, debe diseñar la infraestructura de aplicaciones para optimizar la IOPS. En la sección siguiente, *Optimización del rendimiento de las aplicaciones*, analizaremos en detalle todos los factores que debe tener en cuenta para obtener una IOPS alta.

Cuando conecte un disco de Almacenamiento premium a la máquina virtual a gran escala, Azure aprovisiona automáticamente un número garantizado de IOPS según la especificación del disco. Por ejemplo, un disco P30 aprovisiona 5000 IOPS. Cada tamaño de máquina virtual a gran escala también tiene un límite de IOPS específico que puede admitir. Por ejemplo, una máquina virtual GS5 estándar tiene un límite de 80.000 IOPS.

## Rendimiento  
El rendimiento o ancho de banda es la cantidad de datos que la aplicación envía a los discos de almacenamiento en un intervalo especificado. Si la aplicación está realizando operaciones de entrada y salida con tamaños de unidad de E/S grandes, requiere un alto rendimiento. Las aplicaciones de almacenamiento de datos tienden a emitir operaciones con un uso intensivo de análisis que acceden a grandes porciones de datos a la vez y suelen llevar a cabo operaciones masivas. En otras palabras, estas aplicaciones requieren un mayor rendimiento. Si tiene este tipo de aplicación, debe diseñar su infraestructura para optimizar el rendimiento. En la sección siguiente, analizamos en detalle los factores que se deben optimizar para lograrlo.

Cuando conecte un disco de Almacenamiento premium a la máquina virtual a gran escala, Azure aprovisiona el rendimiento según la especificación del disco. Por ejemplo, un disco P30 aprovisiona 200 MB para el rendimiento del segundo disco. Cada tamaño de máquina virtual a gran escala también tiene un límite de rendimiento específico que puede admitir. Por ejemplo, la máquina virtual GS5 estándar tiene un rendimiento máximo de 2.000 MB por segundo.

Hay una relación entre el rendimiento y la IOPS, tal como se muestra en la siguiente fórmula.

![](media/storage-premium-storage-performance/image1.png)

Por lo tanto, es importante determinar los valores óptimos de rendimiento e IOPS que requiere su aplicación. Si intenta optimizar uno, el otro también se ve afectado. En una sección posterior, *Optimización del rendimiento de las aplicaciones*, trataremos con más detalle cómo optimizar el rendimiento y la IOPS.

## Latency  
La latencia es el tiempo que tarda una aplicación en recibir una sola solicitud, enviarla a los discos de almacenamiento y enviar la respuesta al cliente. Se trata de una medida crítica del rendimiento de una aplicación, además de la IOPS y el rendimiento. La latencia de un disco de almacenamiento premium es el tiempo que tarda en recuperar la información de una solicitud y comunicarla de nuevo a la aplicación. Almacenamiento premium proporciona latencias bajas coherentes. Si habilita el almacenamiento en caché de host ReadOnly en discos de almacenamiento premium, puede obtener una latencia de lectura mucho menor. Trataremos el almacenamiento en caché de disco con más detalle en la sección *Optimización del rendimiento de las aplicaciones*.

Cuando se optimiza la aplicación para obtener una IOPS y un rendimiento mayores, afectará a la latencia de la aplicación. Después de ajustar el rendimiento de las aplicaciones, siempre se evalúa la latencia de la aplicación para evitar un comportamiento inesperado de alta latencia.

## Reunión de los requisitos de rendimiento de las aplicaciones  
El primer paso para diseñar aplicaciones de alto rendimiento que se ejecutan en Almacenamiento premium de Azure es entender los requisitos de rendimiento de las aplicaciones. Después de reunir los requisitos de rendimiento, puede optimizar la aplicación para lograr un rendimiento óptimo.

En la sección anterior, explicamos los indicadores de rendimiento comunes: IOPS, rendimiento y latencia. Debe identificar cuál de estos indicadores de rendimiento son fundamentales para que la aplicación proporcione la experiencia de usuario deseada. Por ejemplo, una IOPS alta es más importante para las aplicaciones OLTP que procesan millones de transacciones en un segundo. Por otra parte, un alto rendimiento es fundamental para las aplicaciones de Almacenamiento de datos que procesan grandes cantidades de datos en un segundo. Una latencia extremadamente baja es fundamental para las aplicaciones en tiempo real, como sitios web de streaming de vídeo en directo.

A continuación, mida los requisitos para obtener el máximo rendimiento de sus aplicaciones a lo largo de toda su duración. Use la siguiente lista de comprobación de ejemplo como punto de partida. Registre los requisitos para obtener el máximo rendimiento durante períodos de carga de trabajo normales, pico y valle. Al identificar los requisitos para todos los niveles de carga de trabajo, podrá determinar los requisitos de rendimiento generales de la aplicación. Por ejemplo, la carga de trabajo normal de un sitio web de comercio electrónico serán las transacciones atendidas durante la mayoría de los días en un año. La carga de trabajo máxima del sitio web serán las transacciones atendidas durante la temporada de vacaciones o eventos de ventas especiales. La carga de trabajo máxima normalmente se experimenta durante un período limitado, pero puede que la aplicación deba escalar su funcionamiento normal de dos o más veces. Descubra los requisitos de percentil 50, percentil 90 y percentil 99. Esto ayuda a filtrar los valores atípicos en los requisitos de rendimiento, por lo que puede centrar sus esfuerzos en optimizar para los valores correctos.

**Lista de comprobación de requisitos de rendimiento de las aplicaciones**

| **Requisitos de rendimiento** | **Percentil 50** | **Percentil 90** | **Percentil 99** |
|---|---|---|---|
| Máx. Transacciones por segundo | | | |
| Porcentaje de operaciones de lectura | | | |
| Porcentaje de operaciones de escritura | | | |
| Porcentaje de operaciones aleatorias | | | |
| Porcentaje de operaciones secuenciales | | | |
| Tamaño de las solicitudes de E/S | | | |
| Rendimiento medio | | | |
| Máx. Rendimiento | | | |
| Mín. Latency | | | |
| Latencia media | | | |
| Máx. CPU | | | |
| Promedio de CPU | | | |
| Máx. Memoria | | | |
| Promedio de memoria | | | |
| Profundidad de la cola | | | |

>**Nota importante:** Debe considerar la posibilidad de escalar estos números en función del crecimiento futuro previsto de la aplicación. Es buena idea para planificar el crecimiento antes de tiempo, porque podría ser más difícil cambiar la infraestructura para mejorar el rendimiento más adelante.

Si tiene una aplicación existente y desea cambiar a Almacenamiento premium, primero prepare la lista de comprobación anterior para la aplicación existente. A continuación, cree un prototipo de la aplicación en Almacenamiento premium y diseñe la aplicación de acuerdo con las directrices descritas en *Optimización del rendimiento de las aplicaciones*, en una sección posterior de este documento. La siguiente sección describe las herramientas que puede usar para recopilar las mediciones de rendimiento.

Cree una lista de comprobación similar a la aplicación existente para el prototipo. Con herramientas de pruebas comparativas puede simular las cargas de trabajo y medir el rendimiento de las aplicaciones de prototipo. Consulte la sección [Pruebas comparativas](#benchmarking) para obtener más información. Gracias a ello, puede determinar si Almacenamiento premium puede alcanzar o superar los requisitos de rendimiento de las aplicaciones. A continuación, puede implementar las mismas directrices para la aplicación de producción.

### Contadores para medir los requisitos de rendimiento de las aplicaciones  
La mejor forma de medir los requisitos de rendimiento de las aplicaciones es usar las herramientas de supervisión del rendimiento proporcionadas por el sistema operativo del servidor. Puede usar PerfMon para Windows e iostat para Linux. Estas herramientas capturan contadores correspondientes a cada medida explicada en la sección anterior. Debe capturar los valores de estos contadores cuando la aplicación funciona con cargas de trabajo normales, pico y valle.

Los contadores de rendimiento están disponibles para el procesador y la memoria, así como en cada disco lógico y físico del servidor. Al usar discos de Almacenamiento premium con una máquina virtual, los contadores del disco físico son para cada disco de Almacenamiento premium y los contadores del disco lógico son para cada volumen creado en los discos de Almacenamiento premium. Debe capturar los valores de los discos que hospedan la carga de trabajo de la aplicación. Si hay una asignación uno a uno entre los discos lógicos y físicos, puede hacer referencia a los contadores del disco físico; de lo contrario, haga referencia a los contadores del disco lógico. En Linux, el comando iostat genera un informe de uso de CPU y disco. El informe de uso del disco proporciona estadísticas por cada dispositivo físico o partición. Si tiene un servidor de bases de datos con sus datos e inicia sesión en discos independientes, recopile estos datos para ambos discos. La tabla siguiente describe los contadores de los discos, el procesador y la memoria:

| Contador | Descripción | PerfMon | Iostat |
|---|---|---|---|
| **E/S por segundo o transacciones por segundo** | Número de solicitudes de E/S emitidas en el disco de almacenamiento por segundo. | Lecturas de disco/s <br> Escrituras en disco/s | tps <br> r/s <br> w/s |
| **Escrituras y lecturas de disco** | Porcentaje de operaciones de lectura y escritura realizadas en el disco. | % de tiempo de lectura de disco <br> % de tiempo de escritura de disco | r/s <br> w/s |
| **Rendimiento** | Cantidad de datos que se leen o escriben en el disco por segundo. | Bytes de lectura de disco/s <br> Bytes de escritura en disco/s | kB\_read/s <br> kB\_wrtn/s |
| **Latency** | Tiempo total para completar una solicitud de E/S del disco. | Promedio de segundos de disco/lectura <br> Promedio de segundos de disco/escritura | await <br> svctm |
| **Tamaño de E/S** | El tamaño de E/S las solicitudes en los discos de almacenamiento. | Promedio de bytes de disco/lectura <br> Promedio de bytes de disco/escritura | avgrq-sz |
| **Profundidad de la cola** | Número de solicitudes de E/S pendientes de lectura de o escritura en el disco de almacenamiento. | Longitud actual de cola de disco | avgqu-sz |
| **Máx. memoria** | Cantidad de memoria necesaria para ejecutar la aplicación sin problemas | % de bytes confirmados en uso | Use vmstat |
| **Máx. CPU** | Cantidad de CPU necesaria para ejecutar la aplicación sin problemas | % de tiempo de procesador | %util |

Obtenga más información sobre [iostat](http://linuxcommand.org/man_pages/iostat1.html) y [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx).


## Optimización del rendimiento de las aplicaciones  
Los principales factores que influyen en el rendimiento de una aplicación que se ejecuta en Almacenamiento premium son la naturaleza de las solicitudes de E/S, el tamaño de la máquina virtual, el tamaño del disco, el número de discos, la caché de disco, el multithreading y la profundidad de la cola. Puede controlar algunos de estos factores con mecanismos proporcionados por el sistema. Es posible que la mayoría de las aplicaciones no le de opción de modificar el tamaño de E/S y la profundidad de la cola directamente. Por ejemplo, si usa SQL Server, no puede elegir la profundidad de la cola y el tamaño de E/S. SQL Server selecciona los valores de tamaño de E/S y profundidad de la cola óptimos para obtener el máximo rendimiento. Es importante comprender los efectos de ambos tipos de factores en rendimiento de su aplicación para poder aprovisionar los recursos adecuados para satisfacer las necesidades de rendimiento.

En esta sección, consulte la lista de comprobación de los requisitos de la aplicación que creó para averiguar la cantidad que necesita para optimizar el rendimiento de las aplicaciones. En función de ello, podrá determinar qué factores de esta sección debe optimizar. Para ver los efectos de cada factor en el rendimiento de las aplicaciones, ejecute las herramientas de pruebas comparativas en la configuración de su aplicación. Vea la sección [Pruebas comparativas](#Benchmarking) al final de este artículo para conocer los pasos para ejecutar las herramientas de pruebas comparativas comunes en las máquinas virtuales de Windows y de Linux.

### Optimización de IOPS, rendimiento y latencia de un vistazo  
La tabla siguiente resume todos los factores de rendimiento y los pasos necesarios para optimizar la IOPS, el rendimiento y la latencia. Las secciones que siguen a este resumen describen cada factor con mucha más profundidad.

| | **E/S** | **Rendimiento** | **Latency** |
|---|---|---|---|
| **Escenario de ejemplo** | Aplicación OLTP empresarial que requiere una tasa muy alta de transacciones por segundo. | Aplicación de Almacenamiento de datos de empresa que procesa grandes cantidades de datos. | Aplicaciones casi en tiempo real que requieren respuestas instantáneas a solicitudes de usuario, como juegos en línea. |
| Factores de rendimiento | | | |
| **Tamaño de E/S** | Un tamaño de E/S menor produce una mayor IOPS. | Un tamaño de E/S mayor produce un mayor rendimiento. | |
| **Tamaño de VM** | Use un tamaño de máquina virtual que ofrezca una IOPS mayor que los requisitos de la aplicación. Consulte los tamaños de las máquinas virtuales y sus límites de IOPS aquí. | Use un tamaño de máquina virtual con un límite de rendimiento mayor que los requisitos de la aplicación. Consulte los tamaños de máquinas virtuales y sus límites de rendimiento aquí. | Use un tamaño de máquina virtual que ofrezca una escala de límites mayor que los requisitos de la aplicación. Consulte los tamaños de máquinas virtuales y sus límites aquí. |
| **Tamaño del disco** | Use un tamaño de disco que ofrezca una IOPS mayor que los requisitos de la aplicación. Consulte los tamaños de disco y sus límites de IOPS aquí. | Use un tamaño de disco con un límite de rendimiento mayor que los requisitos de la aplicación. Consulte los tamaños de disco y sus límites de rendimiento aquí. | Use un tamaño de disco que ofrezca una escala de límites mayor que los requisitos de la aplicación. Consulte los tamaños de disco y sus límites aquí. |
| **Máquina virtual y límites de escala de disco** | El límite de IOPS del tamaño de la máquina virtual elegido debe ser mayor que la IOPS total controlada por los discos de almacenamiento premium conectados. | El límite de rendimiento del tamaño de la máquina virtual elegido debe ser mayor que el rendimiento total controlado por los discos de almacenamiento premium conectados. | Los límites de la escala del tamaño de la máquina virtual elegidos deben ser mayores que los límites de escala total de los discos de almacenamiento premium conectados. |
| **Almacenamiento en caché de disco** | Habilite la caché de solo lectura en los discos de almacenamiento premium con operaciones intensivas de lectura para obtener una mayor IOPS de lectura. | | Habilite la caché de solo lectura en los discos de almacenamiento premium con operaciones intensivas de lectura para obtener latencias de lectura muy bajas. |
| **Seccionamiento del disco** | Use varios discos y secciónelos conjuntamente para conseguir un límite de IOPS y rendimiento combinado superior. Tenga en cuenta que el límite combinado por máquina virtual debe ser mayor que los límites combinados de los discos premium conectados. | |
| **Tamaño de franja** | Un menor tamaño de franja para un patrón de E/S pequeño y aleatorio visto en las aplicaciones OLTP. Por ejemplo, puede usar un tamaño de franja de 64 KB para aplicaciones OLTP de SQL Server. | Un mayor tamaño de franja para un patrón de E/ grande y secuencial visto en las aplicaciones de Almacenamiento de datos. Por ejemplo, puede usar un tamaño de franja de 256 KB para aplicaciones de Almacenamiento de datos de SQL Server. | |
| **Multithreading** | Use el multithreading para insertar un mayor número de solicitudes en Almacenamiento premium, lo que dará lugar a una mayor IOPS y rendimiento. Por ejemplo, en SQL Server establezca un valor MAXDOP alto para asignar más CPU a SQL Server. | |
| **Profundidad de la cola** | Una profundidad de la cola mayor produce una IOPS mayor. | Una profundidad de la cola mayor produce un mayor rendimiento. | Una profundidad de la cola menor produce latencias más bajas. |

## Naturaleza de las solicitudes de E/S  
Una solicitud de E/S es una unidad de operación de entrada y salida que la aplicación va a realizar. La identificación de la naturaleza de las solicitudes de E/S, aleatorias o secuenciales, lectura o escritura, pequeñas o grandes, ayudará a determinar los requisitos de rendimiento de la aplicación. Es muy importante comprender la naturaleza de las solicitudes de E/S para tomar las decisiones correctas al diseñar la infraestructura de las aplicaciones.

El tamaño de E/S es uno de los factores más importantes. El tamaño de E/S es el tamaño de la solicitud de operación de entrada/salida generada por la aplicación. El tamaño de E/S tiene una repercusión considerable en el rendimiento, especialmente en la IOPS y el ancho de banda que la aplicación es capaz de lograr. La fórmula siguiente muestra la relación entre IOPS, tamaño de E/S y ancho de banda y rendimiento. ![](media/storage-premium-storage-performance/image1.png)

Algunas aplicaciones permiten modificar su tamaño de E/S, mientras que otras aplicaciones no lo permiten. Por ejemplo, SQL Server determina el tamaño de E/S óptimo en sí; no proporciona a los usuarios ningún botón para cambiarlo. Por otro lado, Oracle proporciona un parámetro llamado [DB\_BLOCK\_SIZE](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) con el que puede configurar el tamaño de la solicitud de E/S de la base de datos.

Si usa una aplicación que no permite cambiar el tamaño de E/S, use las directrices de este artículo para optimizar el KPI de rendimiento que es más importante para su aplicación. Por ejemplo,

-   Una aplicación OLTP genera millones de solicitudes de E/S pequeñas y aleatorias. Para controlar estos tipos de solicitudes de E/S, debe diseñar la infraestructura de la aplicación para obtener una mayor IOPS.  
-   Una aplicación de almacenamiento de datos genera solicitudes de E/S grandes y secuenciales. Para controlar estos tipos de solicitudes de E/S, debe diseñar sla infraestructura de la aplicación para obtener el mayor ancho de banda o el rendimiento.

Si usa una aplicación que le permite cambiar el tamaño de E/S, use esta regla general para el tamaño de E/S, además otras directrices de rendimiento.

-   Un tamaño de E/S menor para obtener una mayor IOPS. Por ejemplo, 8 KB para una aplicación OLTP.  
-   Un tamaño de E/S mayor para obtener un mayor ancho de banda y rendimiento. Por ejemplo, 1024 KB para una aplicación de Almacenamiento de datos.

Este es un ejemplo de cómo calcular la IOPS y el ancho de banda y el rendimiento de la aplicación. Considere una aplicación con un disco P30. El máximo rendimiento/ancho de banda e IOPS que un disco P30 puede lograr es 200 MB por segundo y 5000 IOPS respectivamente. Ahora, si la aplicación requiere la IOPS máxima en el disco P30 y usa un tamaño de E/S más pequeño, como 8 KB, el ancho de banda resultante que podrá obtener es de 40 MB por segundo. Sin embargo, si la aplicación requiere el máximo rendimiento/ancho de banda del disco P30 y usa un tamaño de E/S mayor, como 1024 KB, el número de IOPS resultante será menor, 200 IOPS. Por lo tanto, ajuste el tamaño de E/S para que cumpla los requisitos de IOPS y ancho de banda y rendimiento de la aplicación. En la tabla siguiente se resumen los distintos tamaños de E/S y la IOPS y el rendimiento correspondientes para un disco P30.

| **Requisito de la aplicación** | **Tamaño de E/S** | **E/S** | **Rendimiento/ancho de banda** |
|-----------------------------|--------------|----------|--------------------------|
| IOPS máx. | 8 KB | 5\.000 | 40 MB por segundo |
| Rendimiento máx. | 1024 KB | 200 | 200 MB por segundo |
| Rendimiento máx.+ IOPS alta | 64 KB | 3\.200 | 200 MB por segundo |
| IOPS máx. + alto rendimiento | 32 KB | 5\.000 | 160 MB por segundo |

Para obtener una IOPS y un ancho de banda mayores que el valor máximo de un solo disco de almacenamiento premium, use varios discos premium seccionados conjuntamente. Por ejemplo, seccione dos discos P30 para obtener una IOPS combinada de 10.000 IOPS o un rendimiento combinado de 400 MB por segundo. Como se explica en la sección siguiente, debe usar un tamaño de máquina virtual que admita la IOPS y el rendimiento de disco combinados.

>**Nota:** a medida que aumente la IOPS o el rendimiento, el otro también aumenta, asegúrese de que no supera los límites de IOPS o rendimiento del disco o la máquina virtual al aumentar cualquiera de ellos.

Para ver los efectos del tamaño de E/S en el rendimiento de las aplicaciones, puede ejecutar las herramientas de pruebas comparativas en la máquina virtual y los discos. Cree varias ejecuciones de pruebas y use un tamaño de E/S diferente para cada ejecución para ver el impacto. Consulte la sección [Pruebas comparativas](#Benchmarking) al final de este artículo para más detalles.

## Tamaños de máquina virtual a gran escala  
Al empezar a diseñar una aplicación, una de las primeras cosas que hay que hacer es elegir una máquina virtual para hospedar la aplicación. Almacenamiento premium viene con tamaños de máquina virtual a gran escala que pueden ejecutar aplicaciones que requieren una mayor capacidad de proceso y un alto rendimiento de E/S del disco local. Estas máquinas virtuales proporcionan procesadores más rápidos, una mayor proporción de memoria a núcleo y una unidad de estado sólido (SSD) para el disco local. Algunos ejemplos de máquinas virtuales a gran escala que admiten Almacenamiento premium son las máquinas virtuales de la serie DS y GS.

Las máquinas virtuales a gran escala están disponibles en distintos tamaños con un número diferente de núcleos de CPU, memoria, sistema operativo y tamaño del disco temporal. Cada tamaño de máquina virtual también tiene el número máximo de discos de datos que se puede conectar a la máquina virtual. Por lo tanto, el tamaño de máquina virtual seleccionado afectará al procesamiento, la memoria y la capacidad de almacenamiento que están disponibles para su aplicación. También afecta al proceso y los costos de almacenamiento. Por ejemplo, a continuación se proporcionan las especificaciones del mayor tamaño de máquina virtual en una serie DS y una serie GS:

| Tamaño de VM | Núcleos de CPU | Memoria | Tamaños de disco de VM | Discos de datos máx. | Tamaño de memoria caché | E/S | Límites de E/S de la memoria caché de ancho de banda |
|---|---|---|---|---|---|---|---|
| Standard\_DS14 | 16 | 112 GB | OS = 1.023 GB <br> SSD Local = 224 GB | 32 | 576 GB | 50\.000 E/S por segundo <br> 512 MB por segundo | 4\.000 IOPS y 33 MB por segundo |
| Standard\_GS5 | 32 | 448 GB | SO = 1023 GB <br> SSD Local = 896 GB | 64 | 4224 GB | 80\.000 E/S por segundo <br> 2000 MB por segundo | 5\.000 IOPS y 50 MB por segundo |

Para una lista completa de todos los tamaños de máquinas virtuales de Azure disponibles, vea [Tamaños de máquinas virtuales de Azure](../virtual-machines/virtual-machines-size-specs.md). Elija un tamaño de máquina virtual que puede cumplir y escale a los requisitos de rendimiento de las aplicaciones que desee. Además, tenga en cuenta que debe seguir consideraciones importantes al elegir los tamaños de las máquinas virtuales.

*Límites de escala* Los límites máximos de IOPS por máquina virtual y por disco son diferentes e independientes entre sí. Asegúrese de que la aplicación mantiene la IOPS dentro de los límites de la máquina virtual, así como los discos de premium conectados a ella. En caso contrario, el rendimiento de las aplicaciones experimentará una limitación.

Por ejemplo, suponga que el requisito de la aplicación es un máximo de 4.000 IOPS. Para lograrlo, aprovisiona un disco P30 en una máquina virtual DS1. El disco P30 puede proporcionar hasta 5.000 IOPS. Sin embargo, la máquina virtual DS1 está limitada a 3.200 IOPS. Por consiguiente, el rendimiento de las aplicaciones estará limitado a 3.200 IOPS por el límite de la máquina virtual y el rendimiento disminuirá. Para evitar esta situación, elija un tamaño de máquina virtual y de disco que cumplan los requisitos de la aplicación.

*Costo de operación* En muchos casos, es posible que el costo general de operación con Almacenamiento premium sea inferior al uso del almacenamiento estándar.

Por ejemplo, considere una aplicación que requiere más de 16.000 IOPS. Para obtener este rendimiento, necesitará una máquina virtual IaaS de Azure Standard\_D14, que puede proporcionar una IOPS máxima de 16.000 con 32 discos de 1 TB de almacenamiento estándar. Cada disco de almacenamiento estándar de 1 TB puede alcanzar un máximo de 500 IOPS. El costo estimado de esta máquina virtual por mes será de 1.570 USD. El costo mensual de 32 discos de almacenamiento estándar será de 1.638 USD. El costo mensual total estimado será de 3.208 USD.

Sin embargo, si hospeda la misma aplicación en Almacenamiento premium, necesitará un tamaño de máquina virtual menor y menos discos de almacenamiento premium, lo que reduce el costo total. Una máquina virtual Standard\_DS13 puede cumplir los requisitos de 16.000 IOPS con cuatro discos P30. La máquina virtual DS13 tiene un máximo de 25.600 IOPS y cada disco P30 tiene un máximo de 5.000 IOPS. En general, esta configuración puede lograr 5.000 x 4 = 20.000 IOPS. El costo estimado de esta máquina virtual al mes será de 1.003 USD. El costo mensual de cuatro discos P30 de almacenamiento Premium será de 544,34 USD. El costo mensual total estimado será de 1,544 USD.

La tabla siguiente resume el análisis de costos de este escenario de Almacenamiento premium y estándar.

| | **Standard** | **Premium** |
|---|---|---|
| **Costo de máquina virtual al mes** | 1\.570,58 USD (Standard\_D14) | 1\.003,66 USD (Standard\_DS13) |
| **Costo de discos al mes** | 1\.638,40 USD (32 discos x 1 TB) | 544,34 USD (4 discos x P30) |
| **Costo total al mes** | 3\.208,98 USD | 1\.544,34 USD |

*Linux Distros* Con Almacenamiento premium de Azure, obtendrá el mismo nivel de rendimiento para las máquinas virtuales de Windows y de Linux. Se admiten muchas versiones de las distribuciones de Linux; puede ver la lista completa en [Linux en distribuciones aprobadas por Azure](../virtual-machines/virtual-machines-linux-endorsed-distributions.md). Es importante tener en cuenta que son adecuadas distintas distribuciones para diferentes tipos de carga de trabajo. Podrá ver diferentes niveles de rendimiento según la distribución en la que se ejecuta la carga de trabajo. Pruebe las distribuciones de Linux con su aplicación y elija la que mejor se adapte.

Cuando ejecute Linux con Almacenamiento premium, compruebe las actualizaciones más recientes acerca de los controladores necesarios para garantizar un alto rendimiento.

## Tamaños de disco de Almacenamiento premium  
Actualmente, Almacenamiento premium de Azure ofrece tres tamaños de disco. Cada tamaño de disco tiene un límite de escala diferente de IOPS, ancho de banda y almacenamiento. Elija el tamaño de disco de Almacenamiento premium adecuado según los requisitos de la aplicación y el tamaño de la máquina virtual a gran escala. La tabla siguiente muestra los tamaños de tres discos y sus capacidades.

| **Tipo de disco** | **P10** | **P20** | **P30** |
|---------------------|-------------------|-------------------|-------------------|
| Tamaño del disco | 128 GB | 512 GB | 1024 GB (1 TB) |
| IOPS por disco | 500 | 2300 | 5000 |
| Rendimiento de disco | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo |

El número de discos que elija depende del tamaño de disco elegido. Puede usar un único disco P30 o varios discos P10 para cubrir los requisitos de la aplicación. Tenga en cuenta las consideraciones enumeradas a continuación al realizar su elección.

*Límites de escala (IOPS y rendimiento)* Los límites de IOPS y rendimiento del tamaño de cada disco Premium son diferentes e independientes de los límites de escala de la máquina virtual. Asegúrese de que la IOPS y el rendimiento totales de los discos están dentro de los límites de escala del tamaño de máquina virtual elegido.

Por ejemplo, si un requisito de la aplicación es un máximo de 250 MB/s de rendimiento y usa una máquina virtual DS4 con un solo disco P30. La máquina virtual DS4 puede proporcionar un máximo rendimiento de 256 MB/s. Sin embargo, un único disco P30 tiene un límite de rendimiento de 200 MB/s. Por lo tanto, la aplicación se restringe a 200 MB/s debido al límite del disco. Para superar este límite, aprovisione más de un disco de datos a la máquina virtual.

>**Nota:** Las lecturas atendidas por la caché no se incluyen en la IOPS y el rendimiento del disco, por lo que no están sujetas a los límites del disco. La caché tiene su límite de IOPS y rendimiento independiente de la máquina virtual.
>
>Por ejemplo, inicialmente sus lecturas y escrituras son 60MB/s y 40MB/s respectivamente. Con el tiempo, la memoria caché se prepara y atiende cada vez más y más lecturas de la memoria caché. Entonces, puede obtener un mayor rendimiento de escritura desde el disco.

*Número de discos* Para determinar el número de discos que necesitará, evalúe los requisitos de la aplicación. Cada tamaño de máquina virtual también tiene un límite en el número de discos que puede conectar a la máquina virtual. Normalmente, es dos veces el número de núcleos. Asegúrese de que el tamaño de la máquina virtual que elija puede admitir el número de discos necesario.

Recuerde que los discos de Almacenamiento premium tienen capacidades de rendimiento superiores en comparación con los discos de almacenamiento estándar. Por tanto, si va a migrar la aplicación de la máquina virtual IaaS de Azure del almacenamiento estándar a Almacenamiento premium, probablemente necesitará menos discos premium para conseguir un rendimiento igual o superior para la aplicación.

## Almacenamiento en caché de disco  
Las máquinas virtuales a gran escala que aprovechan Almacenamiento premium de Azure tienen una tecnología de almacenamiento en caché de niveles múltiples denominada BlobCache. BlobCache usa una combinación de la RAM de máquina virtual y SSD local para almacenar en caché. Esta memoria caché está disponible para los discos de Almacenamiento premium persistentes y los discos locales de la máquina virtual. De forma predeterminada, esta configuración de la caché se establece en lectura y escritura para los discos del sistema operativo y de solo lectura para los discos de datos hospedados en Almacenamiento premium. Con la caché de disco habilitada en los discos de Almacenamiento premium, la máquinas virtuales a gran escala pueden lograr niveles de rendimiento extremadamente altos que superan el rendimiento del disco subyacente.

Para más información acerca del funcionamiento de BlobCache, consulte la publicación de blog [Almacenamiento premium de Azure](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/).

Es importante habilitar la memoria caché en el conjunto de discos correcto. Si debe habilitar el almacenamiento en caché de disco en un disco de premium o no dependerá del patrón de la carga de trabajo que el disco manejará. La tabla siguiente muestra el valor predeterminado de las opciones de caché para discos del sistema operativo y datos.

| **Tipo de disco** | **Configuración de la memoria caché predeterminada** |
|---|---|
| Disco del sistema operativo | ReadWrite |
| Disco de datos | None |

A continuación se muestra la configuración de caché de disco recomendada para los discos de datos:

| **Configuración de almacenamiento en caché de disco** | **Recomendación sobre cuándo usar esta configuración** |
|---|---|
| None | Configure la caché de host como Ninguno para los discos de solo escritura y con escritura intensiva. |
| ReadOnly | Configure la caché de host como ReadOnly para discos de solo lectura y lectura y escritura. |
| ReadWrite | Configure la caché de host como ReadWrite solo si la aplicación maneja correctamente la escritura de datos en caché a discos persistentes cuando es necesario. |

*ReadOnly* Mediante la configuración del almacenamiento en caché ReadOnly en discos de datos de Almacenamiento premium, puede lograr una baja latencia de lectura y obtener una IOPS de lectura y un rendimiento de la aplicación muy altos. Esto se debe a dos razones:

1.  Las lecturas realizadas desde la memoria caché, que se encuentra en la memoria de la máquina virtual y el SSD local, son mucho más rápidas que las lecturas desde el disco de datos, que se encuentra en el almacenamiento de blobs de Azure.  
2.  Almacenamiento premium no cuenta las lecturas que se atienden desde la caché para la IOPS y el rendimiento del disco. Por lo tanto, la aplicación es capaz de lograr una IOPS y un rendimiento totales mayores.

*ReadWrite* De forma predeterminada, los discos del sistema operativo tienen habilitada la caché ReadWrite. Recientemente hemos agregado también compatibilidad para el almacenamiento en caché ReadWrite en los discos de datos. Si usa el almacenamiento en caché ReadWrite, debe tener una manera adecuada de escribir los datos de la memoria caché en discos persistentes. Por ejemplo, SQL Server administra por sí mismo la escritura de los datos en caché en los discos de almacenamiento persistentes. El uso de la memoria caché ReadWrite con una aplicación que no administre la persistencia de los datos necesarios puede provocar la pérdida de los datos, si se bloquea la máquina virtual.

Por ejemplo, puede aplicar estas directrices a un SQL Server que funciona en Almacenamiento premium del modo siguiente:

1.  Configure la caché "ReadOnly" en discos de almacenamiento premium que hospeda archivos de datos. a. Las rápidas lecturas de la caché reducen el tiempo de consulta de SQL Server, ya que las páginas de datos se recuperan mucho más rápido de la memoria caché que directamente desde los discos de datos. b. Atender las lecturas de la caché significa que hay un rendimiento adicional de los discos de datos premium. SQL Server puede usar este rendimiento adicional para recuperar más páginas de datos y otras operaciones, como copia de seguridad/restauración, cargas por lotes y volver a generar un índice.  
2.  Configure la caché “Ninguna” en los discos de almacenamiento premium que hospedan los archivos de registro. a. Los archivos de registro tienen sobre todo muchas operaciones de escritura. Por lo tanto, no se benefician de la caché ReadOnly.

## Seccionamiento del disco  
Cuando una máquina virtual a gran escala esté conectada a varios discos persistente almacenamiento premium, los discos se pueden seccionar conjuntamente para agregar sus IOPS, ancho de banda y capacidad de almacenamiento.

En Windows, puede usar espacios de almacenamiento para seccionar discos conjuntamente. Debe configurar una columna para cada disco de un grupo. De lo contrario, el rendimiento general del volumen seccionado puede ser inferior al esperado debido a una distribución desigual del tráfico entre los discos.

Importante: Con la IU del Administrador del servidor, puede establecer el número total de columnas en hasta 8 para un volumen seccionado. Al conectar más de 8 discos, use PowerShell para crear el volumen. Mediante PowerShell, puede establecer un número de columnas igual al número de discos. Por ejemplo, si hay 16 discos en un solo conjunto de secciones; especifique 16 columnas en el parámetro *NumberOfColumns* del cmdlet de PowerShell *New-VirtualDisk*.

En Linux, use la utilidad MDADM para seccionar discos conjuntamente. Para ver los pasos detallados para seccionar discos en Linux, consulte [Configuración del software RAID en Linux](../virtual-machines/virtual-machines-linux-configure-raid.md).

*Tamaño de franja* Una configuración importante en el seccionamiento del disco es el tamaño de franja. El tamaño de franja o tamaño de bloque es el fragmento de datos más pequeño que la aplicación puede manejar en un volumen seccionado. El tamaño de franja que configurar depende del tipo de aplicación y su patrón de solicitudes. Si elije un tamaño de franja incorrecto, podría provocar la desalineación de E/S, lo que conduce a una disminución del rendimiento de la aplicación.

Por ejemplo, si una solicitud de E/S generada por la aplicación es mayor que el tamaño de franja del disco, el sistema de almacenamiento escribe a través de límites de la unidad de franja en más de un disco. Cuando llega el momento para tener acceso a esos datos, tendrá que buscar en las unidades con más de una franja para completar la solicitud. El efecto acumulativo de este comportamiento puede provocar una degradación del rendimiento considerable. Por otro lado, si el tamaño de la solicitud de E/S es menor que el tamaño de franja, y si es aleatoria por naturaleza, las solicitudes de E/S pueden acumularse en el mismo disco, causar un cuello de botella y, en última instancia, degradar el rendimiento de E/S.

Según el tipo de carga de trabajo que se ejecute la aplicación, elija un tamaño de franja adecuado. Para solicitudes de E/S pequeñas aleatorias, use un tamaño de franja más pequeño. Por otra parte, para solicitudes de E/S secuenciales grandes, use un tamaño de franja mayor. Descubra las recomendaciones de tamaño de franja para la aplicación que se ejecutará en Almacenamiento premium. Para SQL Server, configure el tamaño de franja de 64 KB para cargas de trabajo OLTP y 256 KB para cargas de trabajo de almacenamiento de datos. Para más información, consulte [Prácticas recomendadas para mejorar el rendimiento para SQL Server en máquinas virtuales de Azure: Consideraciones sobre discos y rendimiento](virtual-machines-sql-server-performance-best-practices.md#disks-and-performance-considerations).

>**Nota:** Puede seccionar conjuntamente un máximo de 32 discos de almacenamiento premium en una serie de máquinas virtuales DS y 64 discos de almacenamiento premium en una serie de máquinas virtuales GS.

## Multithreading  
Azure ha diseñado la plataforma Almacenamiento premium para ser enormemente paralela. Por lo tanto, una aplicación multiproceso logra un rendimiento mucho mayor que una aplicación uniproceso. Una aplicación multiproceso divide sus tareas en varios subprocesos y aumenta la eficacia de su ejecución mediante el uso de los recursos de la máquina virtual y el disco al máximo.

Por ejemplo, si la aplicación se ejecuta en una máquina virtual de un solo núcleo que usa dos subprocesos, la CPU puede cambiar entre los dos subprocesos para lograr la eficiencia. Mientras un subproceso está esperando que se complete la E/S del disco, la CPU puede cambiar a otro subproceso. De esta manera, dos subprocesos pueden lograr más que un único subproceso. Si la máquina virtual tiene más de un núcleo, reduce aún más el tiempo de ejecución, ya que cada núcleo puede ejecutar tareas en paralelo.

No puede cambiar la forma en que una aplicación comercial implementa uniprocesos o multi-threading. Por ejemplo, SQL Server es capaz de manejar varios núcleos y CPU. Sin embargo, SQL Server determina en qué condiciones aprovechará uno o varios subprocesos para procesar una consulta. Puede ejecutar consultas y generar los índices mediante el multi-threading. Para una consulta que implica combinar tablas grandes y ordenar los datos antes de devolverlos al usuario, SQL Server probablemente usará varios subprocesos. Sin embargo, un usuario no puede controlar si SQL Server se ejecuta una consulta con un único subproceso o varios subprocesos.

Hay opciones de configuración que puede modificar para tener en cuenta este multi-threading o el procesamiento en paralelo de una aplicación. Por ejemplo, en el caso de SQL Server es la configuración con el máximo grado de paralelismo. Esta configuración, denominada MAXDOP, permite configurar el número máximo de procesadores que puede usar SQL Server cuando realiza el procesamiento en paralelo. Puede configurar MAXDOP para consultas individuales u operaciones de índice. Resulta especialmente útil cuando desea equilibrar los recursos del sistema para una aplicación crítica para el rendimiento.

Por ejemplo, supongamos que su aplicación con SQL Server ejecuta una consulta de gran tamaño y una operación de índice al mismo tiempo. Supongamos que desea que la operación de índice sea más eficaz en comparación con la consulta de gran tamaño. En tal caso, puede establecer el valor de MAXDOP de la operación de índice para que sea mayor que el valor de MAXDOP para la consulta. De este modo, SQL Server tiene un mayor número de procesadores que puede aprovechar para la operación de índice en comparación con el número de procesadores que puede dedicar a la consulta de gran tamaño. Recuerde que no controla el número de subprocesos que SQL Server usará para cada operación. Puede controlar el número máximo de procesadores que se dedicó a multi-threading.

Obtenga más información sobre [Grados de paralelismo](https://technet.microsoft.com/library/ms188611.aspx) en SQL Server. Descubra la configuración que influye en el multi-threading en la aplicación y sus configuraciones para optimizar el rendimiento.

## Profundidad de la cola  
La profundidad de la cola, la longitud de la cola o el tamaño de la cola es el número de solicitudes de E/S pendientes en el sistema. El valor de la profundidad de la cola determina cuántas operaciones de E/S, que procesarán los discos de almacenamiento, puede alinear la aplicación. Afecta a los tres indicadores de rendimiento de las aplicaciones que analizamos en este artículo: IOPS, rendimiento y latencia.

La profundidad de la cola y multi-threading están estrechamente relacionados. El valor de la profundidad de la cola indica el número de multi-threading que puede lograrse mediante la aplicación. Si la profundidad de la cola es grande, la aplicación puede ejecutar más operaciones simultáneamente, es decir, más multi-threading. Si la profundidad de la cola es pequeña, incluso si la aplicación es multiproceso, no tendrá suficientes solicitudes alineadas para la ejecución simultánea.

Normalmente, las aplicaciones comerciales no le permiten cambiar la profundidad de la cola, porque si se establece incorrectamente, hará más daño que beneficio. Las aplicaciones establecerán el valor correcto de profundidad de la cola para obtener un rendimiento óptimo. Sin embargo, es importante entender este concepto, para que pueda solucionar problemas de rendimiento con la aplicación. También puede observar los efectos de profundidad de la cola mediante la ejecución de herramientas de pruebas comparativas del sistema.

Algunas aplicaciones proporcionan opciones para influir en la profundidad de la cola. Por ejemplo, la configuración de MAXDOP (grado máximo de paralelismo) en SQL Server se explicó en la sección anterior. MAXDOP es una forma de influir en la profundidad de la cola y el multi-threading, aunque no cambia directamente el valor de Profundidad de la cola de SQL Server.

*Profundidad de cola alta* Una profundidad de la cola alta alinea más operaciones en el disco. El disco conoce la siguiente solicitud de su cola de antemano. Por lo tanto, el disco puede programar las operaciones de antemano y procesarlas en una secuencia óptima. Puesto que la aplicación está enviando solicitudes más al disco, el disco puede procesar más E/S paralelas. En última instancia, la aplicación podrá lograr una mayor IOPS. Puesto que la aplicación está procesando más solicitudes, también aumenta el rendimiento total de la aplicación.

Normalmente, una aplicación puede lograr un rendimiento máximo con 8-16+ E/S pendientes para cada disco conectado. Si la profundidad de la cola es uno, la aplicación no inserta suficientes E/S en el sistema y procesará menos cantidad en un período determinado. En otras palabras, menor rendimiento.

Por ejemplo, en SQL Server, al establecer el valor de MAXDOP para una consulta en "4", se informa a SQL Server que puede usar un máximo de cuatro núcleos para ejecutar la consulta. SQL Server determinará el mejor valor de profundidad de la cola y el número de núcleos para la ejecución de la consulta.

*Profundidad de la cola óptima* Un valor de profundidad de la cola muy alto también tiene sus inconvenientes. Si el valor de profundidad de la cola es demasiado alto, la aplicación intentará manejar una IOPS muy alta. A menos que la aplicación tiene discos persistentes con suficientes IOPS aprovisionada, esto puede afectar negativamente a las latencias de la aplicación. La siguiente fórmula muestra la relación entre la E/S por segundo, la latencia y la profundidad de la cola. ![](media/storage-premium-storage-performance/image6.png)

No debe configurar la profundidad de la cola en cualquier valor alto, sino en un valor óptimo, que puede ofrecer suficientes IOPS para la aplicación sin afectar a las latencias. Por ejemplo, si la latencia de la aplicación debe ser 1 milisegundo, la profundidad de la cola necesaria para lograr 5.000 IOPS es QD = 5000 x 0,001 = 5.

*Profundidad de la cola para un volumen seccionado* Para un volumen seccionado, mantenga una profundidad de la cola lo suficientemente alta para que cada disco tenga una profundidad de la cola máxima individual. Por ejemplo, supongamos una aplicación que inserta una profundidad de la cola de 2 y hay 4 discos en la franja. Las dos solicitudes de E/S irán a dos discos y los dos discos restantes estarán inactivos. Por lo tanto, configure la profundidad de la cola de modo que todos los discos puedan estar ocupados. La siguiente fórmula muestra cómo determinar la profundidad de la cola de volúmenes seccionados. ![](media/storage-premium-storage-performance/image7.png)

## Limitaciones  
Almacenamiento premium de Azure aprovisiona un número especificado de IOPS y rendimiento de acuerdo con los tamaños de la máquina virtual y de disco que elija. Cada vez que la aplicación intenta que la IOPS o el rendimiento estén por encima de los límites que puede administrar la máquina virtual o el disco, Almacenamiento premium lo limitará. Esto se manifiesta en forma de una disminución del rendimiento de la aplicación. Esto puede significar una latencia mayor, un rendimiento menor o una IOPS menor. Si Almacenamiento premium no lo limita, la aplicación podría fallar completamente al exceder lo que sus recursos son capaces de conseguir. Por lo tanto, para evitar problemas de rendimiento debido a la limitación, aprovisione siempre suficientes recursos para su aplicación. Tenga en cuenta lo que hemos explicado en las secciones anteriores sobre los tamaños de la máquina virtual y el disco. Las pruebas comparativas son la mejor forma de averiguar qué recursos necesitará para hospedar su aplicación.

## Pruebas comparativas  
Las pruebas comparativas consisten en el proceso de simular cargas de trabajo diferentes en la aplicación y medir el rendimiento de las aplicaciones para cada carga de trabajo. Mediante los pasos descritos en una sección anterior, recopiló los requisitos de rendimiento de las aplicaciones. Al ejecutar las herramientas de pruebas comparativas en las máquinas virtuales en las que se hospeda la aplicación, puede determinar los niveles de rendimiento que la aplicación puede lograr con Almacenamiento premium. En esta sección se proporcionan ejemplos de pruebas comparativas realizados con una máquina virtual estándar de DS14 aprovisionada con discos de Almacenamiento premium de Azure.

Hemos usado las herramientas de pruebas comparativas comunes Iometer y FIO, para Windows y Linux respectivamente. Estas herramientas generan varios subprocesos que simulan una carga de trabajo de producción y miden el rendimiento del sistema. Con estas herramientas, también puede configurar parámetros como la profundidad de la cola y el tamaño de bloque, que normalmente no se puede cambiar de una aplicación. Esto proporciona más flexibilidad para controlar el rendimiento máximo en una máquina virtual a gran escala aprovisionada con discos premium para diferentes tipos de cargas de trabajo de la aplicación. Para más información sobre la herramienta de pruebas comparativas, visite [Iometer](http://www.iometer.org/) y [FIO](http://freecode.com/projects/fio).

Para seguir estos ejemplos, cree una máquina virtual estándar DS14 y conecte 11 discos de Almacenamiento premium a la máquina virtual. De los discos 11, configure 10 discos con almacenamiento en caché del host como "Ninguno" y secciónelos en un volumen denominado NoCacheWrites. Configure el almacenamiento en caché del host como "ReadOnly" en el disco restante de host y cree un volumen denominado CacheReads con este disco. Con esta configuración, podrá ver el rendimiento máximo de lectura y escritura de una máquina virtual estándar DS14. Para pasos detallados sobre de la creación de una máquina virtual DS14 con discos de premium, vaya a [Creación y uso de la cuenta de Almacenamiento premium para un disco de datos de la máquina virtual](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk).

*Preparación de la memoria caché* El disco con almacenamiento en caché de host ReadOnly podrá proporcionar una IOPS mayor que el límite del disco. Para obtener este máximo rendimiento de lectura de la caché de host, primero debe preparar la memoria caché de este disco. Esto garantiza que las E/S de lectura en las qué la herramienta de pruebas comparativas manejará el volumen de CacheReads alcanzan realmente la memoria caché y no en el disco directamente. Los aciertos de caché generan IOPS adicionales desde el único disco con la memoria caché habilitada.

>**Importante:** debe preparar la memoria caché antes de ejecutar pruebas comparativas y cada vez que se reinicie la máquina virtual.

#### Iometer   
[Descargue la herramienta Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) en la máquina virtual.

*Archivo de prueba* Iometer usa un archivo de prueba que se almacena en el volumen en el que se ejecutará la prueba comparativa. Realiza lecturas y escrituras en el archivo de prueba para medir la IOPS y el rendimiento del disco. Iometer crea este archivo de prueba si no proporcionó ninguno. Cree un archivo de prueba de 200 GB llamado iobw.tst en los volúmenes CacheReads y NoCacheWrites.

*Especificaciones de acceso* Las especificaciones: solicitud de tamaño de E/S, porcentaje de lectura/escritura y porcentaje de aleatorio o secuencial, se configuran mediante la pestaña “Access Specifications” (Especificaciones de Access) en Iometer. Cree una especificación de acceso para cada uno de los escenarios descritos a continuación. Cree las especificaciones de acceso y "Guarde" con un nombre como – RandomWrites\_8K, RandomReads\_8K. Seleccione la especificación correspondiente al ejecutar el escenario de prueba.

A continuación se muestra un ejemplo de especificaciones de acceso para el escenario de IOPS de escritura máxima: ![](media/storage-premium-storage-performance/image8.png)

*Especificaciones de prueba de IOPS máxima* Para demostrar el número máximo de E/S por segundo, use el tamaño de solicitud más pequeño. Use el tamaño de solicitud de 8K y cree especificaciones de lecturas y escrituras aleatorias.

| Especificación de acceso | Tamaño de la solicitud | % aleatorio | % lectura |
|----------------------|--------------|----------|--------|
| RandomWrites\_8K | 8 K | 100 | 0 |
| RandomReads\_8K | 8 K | 100 | 100 |

*Especificaciones de prueba de rendimiento máximo* Para demostrar el rendimiento máximo, use el tamaño de la solicitud más grande. Use el tamaño de la solicitud de 64 KB y cree especificaciones de lecturas y escrituras aleatorias.

| Especificación de acceso | Tamaño de la solicitud | % aleatorio | % lectura |
|----------------------|--------------|----------|--------|
| RandomWrites\_64K | 64 K | 100 | 0 |
| RandomReads\_64K | 64 K | 100 | 100 |

*Ejecución de la prueba Iometer* Realice los siguientes pasos para preparar la memoria caché

1.  Cree dos especificaciones de acceso con los valores que se muestran a continuación:

	| Nombre | Tamaño de la solicitud | % aleatorio | % lectura |
	|-------------------|--------------|----------|--------|
	| RandomWrites\_1MB | 1MB | 100 | 0 |
	| RandomReads\_1MB | 1MB | 100 | 100 |

2.  Ejecute la prueba Iometer para inicializar el disco de la caché con los parámetros siguientes. Use tres subprocesos de trabajo para el volumen de destino y una profundidad de la cola de 128. Establezca la duración del “tiempo de ejecución” de la prueba en 2 horas en la pestaña "Configuración de prueba".

	| Escenario | Volumen de destino | Nombre | Duración |
	|-----------------------|---------------|-------------------|----------|
	| Inicializar caché de disco | CacheReads | RandomWrites\_1MB | 2 horas |

3.  Ejecute la prueba Iometer para el preparar el disco de la caché con los parámetros siguientes. Use tres subprocesos de trabajo para el volumen de destino y una profundidad de la cola de 128. Establezca la duración del “tiempo de ejecución” de la prueba en 2 horas en la pestaña "Configuración de prueba".

	| Escenario | Volumen de destino | Nombre | Duración |
	|--------------------|---------------|------------------|----------|
	| Preparación de la caché de disco | CacheReads | RandomReads\_1MB | 2 horas |

Una vez preparado el disco de memoria caché, continúe con los escenarios de prueba que se muestran a continuación. Para ejecutar la prueba Iometer, use al menos tres subprocesos de trabajo para **cada** volumen de destino. Para cada subproceso de trabajo, seleccione el volumen de destino, establezca la profundidad de la cola y seleccione una de las especificaciones de prueba guardadas, tal como se muestra en la tabla siguiente, para ejecutar el escenario de prueba correspondiente. La tabla también muestra los resultados esperados para IOPS y rendimiento al ejecutar estas pruebas. Para todos los escenarios, se usa un tamaño pequeño de E/S de 8 KB y una profundidad de la cola alta de 128.

| Escenario de prueba | Volumen de destino | Nombre | Resultado |
|--------------------|---------------|-------------------|--------------|
| Máx. IOPS de lectura | CacheReads | RandomWrites\_8K | 50\.000 IOPS |
| Máx. IOPS de escritura | NoCacheWrites | RandomReads\_8K | 64\.000 IOPS |
| Máx. IOPS combinado | CacheReads | RandomWrites\_8K | 100\.000 IOPS |
| | NoCacheWrites | RandomReads\_8K | |
| Máx. MB/s de lectura | CacheReads | RandomWrites\_64K | 524 MB/s |
| Máx. Escritura MB/s | NoCacheWrites | RandomReads\_64K | 524 MB/s |
| Combinado MB/s | CacheReads | RandomWrites\_64K | 1000 MB/s |
| | NoCacheWrites | RandomReads\_64K | |

A continuación se muestran capturas de pantalla de los resultados de la prueba de Iometer para los escenarios IOPS y rendimiento combinados.

*IOPS máxima de lectura y escritura combinada* ![](media/storage-premium-storage-performance/image9.png)

*Rendimiento máximo de lectura y escritura combinado* ![](media/storage-premium-storage-performance/image10.png)

### FIO  
FIO es una popular herramienta para el almacenamiento de información de referencia en las máquinas virtuales de Linux. Tiene flexibilidad para seleccionar distintos tamaños de E/S y lecturas y escrituras secuenciales o aleatorias. Genera subprocesos de trabajo o procesos para realizar las operaciones de E/S especificadas. Puede especificar el tipo de operaciones de E/S que debe realizar cada subproceso de trabajo con archivos de trabajo. Hemos creado un archivo de trabajo por escenario que se ilustra en los ejemplos siguientes. Puede cambiar las especificaciones de estos archivos de trabajo para tener referencia de diferentes cargas de trabajo en Almacenamiento premium. En los ejemplos, usamos una máquina virtual estándar 14 DS que ejecuta **Ubuntu**. Use la misma configuración descrita al principio de la [sección Pruebas comparativas](#Benchmarking) y prepare la memoria caché antes de ejecutar las pruebas comparativas.

Antes de comenzar, [descargue FIO](https://github.com/axboe/fio) e instálelo en la máquina virtual.

Ejecute el siguiente comando para Ubuntu:

		apt-get install fio

Usaremos cuatro subprocesos de trabajo para realizar las operaciones de escritura y cuatro subprocesos de trabajo para realizar las operaciones de lectura en los discos. El trabajo de escritura dirigirá el tráfico al volumen "nocache", que tiene 10 discos con la memoria caché establecida en "Ninguno". El trabajo de la lectura dirigirá el tráfico al volumen "readcache", que tiene 1 disco con la memoria caché establecida en "ReadOnly".

*IOPS de escritura máxima* Cree el archivo de trabajo con las especificaciones siguientes para obtener la IOPS de escritura máxima. Asígnele el nombre "fiowrite.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Tenga en cuenta los siguientes aspectos clave que están en consonancia con las instrucciones de diseño que se tratan en secciones anteriores. Estas especificaciones son esenciales para obtener la IOPS máxima: - Una profundidad de la cola alta: 256. -Un tamaño de bloque pequeño: 8 KB. -Varios subprocesos que realizan escrituras aleatorias.

Ejecute el siguiente comando para ejecutar la prueba FIO durante 30 segundos:

	sudo fio --runtime 30 fiowrite.ini

Mientras se ejecuta la prueba, podrá ver el número de IOPS de escritura que envían la máquina virtual y los discos premium. Como se muestra en el ejemplo siguiente, la máquina virtual DS14 está ofreciendo su límite máximo de IOPS de escritura: 50.000 IOPS. ![](media/storage-premium-storage-performance/image11.png)

*IOPS de lectura máxima* Cree el archivo de trabajo con las especificaciones siguientes para obtener la IOPS de lectura máxima. Asígnele el nombre "fioread.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Tenga en cuenta los siguientes aspectos clave que están en consonancia con las instrucciones de diseño que se tratan en secciones anteriores. Estas especificaciones son esenciales para alcanzar la IOPS máxima

-   Una profundidad de la cola alta de 256.  
-   Un tamaño de bloque pequeño de 8 KB.  
-   Varios subprocesos que realizan escrituras aleatorias.

Ejecute el siguiente comando para ejecutar la prueba FIO durante 30 segundos:

	sudo fio --runtime 30 fioread.ini

Mientras se ejecuta la prueba, podrá ver el número de IOPS de lectura que envían la máquina virtual y los discos premium. Como se muestra en el ejemplo siguiente, la máquina virtual DS14 proporciona más de 64.000 IOPS de lectura. Se trata de una combinación del rendimiento de la caché y el disco. ![](media/storage-premium-storage-performance/image12.png)

*IOPS de lectura y escritura máxima* Cree el archivo de trabajo con las especificaciones siguientes para obtener la IOPS de lectura y escritura combinadas máxima. Asígnele el nombre "fioreadwrite.ini".

```
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Tenga en cuenta los siguientes aspectos clave que están en consonancia con las instrucciones de diseño que se tratan en secciones anteriores. Estas especificaciones son esenciales para alcanzar la IOPS máxima

-   Una profundidad de la cola alta de 128.  
-   Un tamaño de bloque pequeño de 4 KB.  
-   Varios subprocesos que realizan lecturas y escrituras aleatorias.

Ejecute el siguiente comando para ejecutar la prueba FIO durante 30 segundos:

	sudo fio --runtime 30 fioreadwrite.ini

Mientras se ejecuta la prueba, podrá ver el número de IOPS de lectura y escritura combinadas que envían la máquina virtual y los discos premium. Como se muestra en el ejemplo siguiente, la máquina virtual DS14 proporciona más de 100.000 IOPS de lectura y escritura combinadas. Se trata de una combinación del rendimiento de la caché y el disco. ![](media/storage-premium-storage-performance/image13.png)

*Rendimiento máximo combinado* Para obtener el rendimiento de lectura y escritura combinado máximo, use un tamaño de bloque y la profundidad de la cola más grandes con varios subprocesos que realizan lecturas y escrituras. Puede usar un tamaño de bloque de 64 KB y una profundidad de la cola de 128.

## Pasos siguientes  

Más información sobre Almacenamiento premium de Azure:

- [Almacenamiento premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](storage-premium-storage.md)  

Para los usuarios de SQL Server, lea artículos sobre procedimientos recomendados para SQL Server:

- [Procedimientos recomendados para SQL Server en Máquinas virtuales de Azure](../virtual-machines/virtual-machines-sql-server-performance-best-practices.md)
- [Almacenamiento premium de Azure proporciona el máximo rendimiento para SQL Server en una máquina virtual de Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx) 

<!---HONumber=AcomDC_0224_2016-->