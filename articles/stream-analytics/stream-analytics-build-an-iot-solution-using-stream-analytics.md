<properties 
	pageTitle="Construir una solución de IoT con Análisis de transmisiones | Microsoft Azure" 
	description="tutorial de introducción de la solución de iot de Análisis de transmisiones de un escenario de peaje"
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"
/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/18/2016" 
	ms.author="jeffstok"
/>

# Construir una solución de IoT con Análisis de transmisiones

## Introducción

En este tutorial aprenderá a obtener información de sus datos en tiempo real mediante Análisis de transmisiones. El servicio de procesamiento de transmisiones de Azure permite a los desarrolladores solucionar fácilmente el espacio de datos en movimiento al combinar flujos de datos, como las secuencias de clic, los registros y los eventos generados por dispositivos, con registros de historiales o datos de referencia para obtener información de la empresa de forma rápida y sencilla. Análisis de transmisiones de Azure es un servicio de cálculo de transmisiones en tiempo real totalmente administrado y hospedado en Microsoft Azure que ofrece una gran resistencia, baja latencia y escalabilidad, para permitirle entrar funcionamiento en minutos.

Después de completar este tutorial, estará capacitado para lo siguiente:

-   Familiarizarse con el Portal de Análisis de transmisiones de Azure.
-   Configurar e implementar un trabajo de streaming.
-   Señalar los problemas reales y solucionarlos usando el lenguaje de consulta de Análisis de transmisiones.
-   Desarrollar soluciones de streaming para los clientes usando el lenguaje de consulta de Análisis de transmisiones.
-   Usar la experiencia de supervisión y registro para solucionar problemas.

## Requisitos previos

Necesitará los siguientes requisitos previos antes de realizar este tutorial

-   La versión más reciente de [Azure PowerShell](../powershell-install-configure.md)
-   Visual Studio 2015 o la versión de [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx).
-   [Suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/)
-   Privilegios administrativos en el equipo.
-   Descargue [TollApp.zip](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) del Centro de descarga de Microsoft.
-   Opcional: código fuente del generador de eventos TollApp desde [GitHub](https://github.com/streamanalytics/samples/tree/master/TollApp)

## Introducción al escenario: peajes


Un peaje es un fenómeno habitual, nos los podemos encontrar en muchas autopistas, puentes y túneles en todo el mundo. Cada peaje tiene varias cabinas, que pueden ser manuales, lo que significa que le atenderá un empleado, o automatizadas, donde un sensor en la parte superior de la cabina detecta una tarjeta RFID fijada en el parabrisas del vehículo cuando este atraviesa el peaje. Es fácil imaginar el paso de los vehículos a través de estos peajes como si fuera un flujo de eventos sobre los que se pueden realizar operaciones interesantes.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## Datos de entrada

Se trabajará con dos flujos de datos producidos por los sensores instalados en la entrada y salida de los peajes y un conjunto de datos de búsqueda estático que contiene los datos de matriculación de los vehículos.

### Flujo de datos de entrada

El flujo de datos de entrada contiene información sobre los automóviles que entran al peaje.
  
  
| TollId | EntryTime | LicensePlate | Estado | Asegúrese | Modelo | VehicleType | VehicleWeight | Toll | Etiqueta |
|---------|-------------------------|--------------|-------|--------|---------|--------------|----------------|------|-----------|
| 1 | 10-09-2014 12:01:00.000 | JNB 7001 | NY | Honda | CRV | 1 | 0 | 7 | |
| 1 | 10-09-2014 12:02:00.000 | YXZ 1001 | NY | Toyota | Camry | 1 | 0 | 4 | 123456789 |
| 3 | 10-09-2014 12:02:00.000 | ABC 1004 | CT | Ford | Taurus | 1 | 0 | 5 | 456789123 |
| 2 | 10-09-2014 12:03:00.000 | XYZ 1003 | CT | Toyota | Corolla | 1 | 0 | 4 | |
| 1 | 10-09-2014 12:03:00.000 | BNJ 1007 | NY | Honda | CRV | 1 | 0 | 5 | 789123456 |
| 2 | 10-09-2014 12:05:00.000 | CDE 1007 | NJ | Toyota | 4 x 4 | 1 | 0 | 6 | 321987654 |
  

Breve descripción de las columnas:
  
  
| TollId | Id. de la cabina de peaje que la identifica de forma única. |
|--------------|----------------------------------------------------------------|
| EntryTime | Fecha y hora de entrada del vehículo en la cabina de peaje en UTC. |
| LicensePlate | Número de matrícula del vehículo. |
| Estado | Estado de los Estados Unidos. |
| Asegúrese | Fabricante del automóvil. |
| Modelo | Número de modelo de vehículo. |
| VehicleType | 1 para vehículos de pasajeros y 2 para vehículos comerciales. |
| WeightType | Peso del vehículo en toneladas; es 0 para vehículos de pasajeros. |
| Toll | Costo del peaje en USD. |
| Etiqueta | Etiqueta electrónica del automóvil que automatiza el pago; en blanco cuando el pago se realiza manualmente. |


### Flujo de datos de salida

El flujo de datos de salida contiene información sobre los automóviles que salen del peaje.
  
  
| **TollId** | **ExitTime** | **LicensePlate** |
|------------|------------------------------|------------------|
| 1 | 10-09-2014 T12:03:00.0000000Z | JNB 7001 |
| 1 | 10-09-2014 T12:03:00.0000000Z | YXZ 1001 |
| 3 | 10-09-2014 T12:04:00.0000000Z | ABC 1004 |
| 2 | 10-09-2014 T12:07:00.0000000Z | XYZ 1003 |
| 1 | 10-09-2014 T12:08:00.0000000Z | BNJ 1007 |
| 2 | 10-09-2014 T12:07:00.0000000Z | CDE 1007 |

Breve descripción de las columnas:
  
  
| Columna | Descripción |
|--------------|-----------------------------------------------------------------|
| TollId | Id. de la cabina de peaje que la identifica de forma única. |
| ExitTime | La fecha y hora de salida del vehículo de la cabina de peaje en UTC. |
| LicensePlate | Número de matrícula del vehículo. |

### Datos de matriculación de vehículos comerciales

Usaremos una instantánea estática de la base de datos de matriculación de vehículos comerciales.
  
  
| LicensePlate | RegistrationId | Expirada |
|--------------|----------------|---------|
| SVT 6023 | 285429838 | 1 |
| XLZ 3463 | 362715656 | 0 |
| BAC 1005 | 876133137 | 1 |
| RIV 8632 | 992711956 | 0 |
| SNY 7188 | 592133890 | 0 |
| ELH 9896 | 678427724 | 1 |                      

Breve descripción de las columnas:
  
  
| Columna | Descripción |
|--------------|-----------------------------------------------------------------|
| LicensePlate | Número de matrícula del vehículo. |
| RegistrationId | RegistrationId |
| Expirada | 0 si la matriculación del vehículo está activa, 1 si expiró. |


## Configuración del entorno para Análisis de transmisiones de Azure

Para realizar este tutorial, deberá tener una suscripción a Microsoft Azure. Microsoft ofrece una evaluación gratuita para los servicios de Microsoft Azure, tal como se describe a continuación.

Si no tiene una cuenta de Azure, puede solicitar una versión de evaluación gratuita en la dirección <http://azure.microsoft.com/pricing/free-trial/>.

Nota: Para suscribirse a una evaluación gratuita, necesita un dispositivo móvil que pueda recibir mensajes de texto y una tarjeta de crédito válida.

Asegúrese de seguir los pasos de la sección "Limpiar la cuenta de Azure" al final de este ejercicio para que pueda aprovechar al máximo su crédito gratuito de 200 $ de Azure.

## Aprovisionamiento de los recursos de Azure necesarios para el tutorial

Este tutorial requiere dos Centros de eventos de Azure para recibir los flujos de datos de "Exit" y "Entry". Se usará la Base de datos SQL de Azure para generar los resultados de los trabajos de Análisis de transmisiones. También se usará el Almacenamiento de Azure para almacenar los datos de referencia sobre la matriculación de los vehículos.

Se puede usar el script Setup.ps1 de la carpeta TollApp en GitHub para crear todos los recursos necesarios. Se recomienda su uso para ahorrar tiempo. Si quiere obtener más información acerca de la configuración de estos recursos en el portal de Azure, consulte el apéndice "Configuración de recursos del tutorial en el Portal de Azure".

Descargue y guarde el soporte los archivos y carpetas de [TollApp](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) compatibles.

Abra una ventana "Microsoft Azure PowerShell" **COMO ADMINISTRADOR**. Si aún no tiene Azure PowerShell, siga estas instrucciones: [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

Windows bloquea automáticamente los archivos .ps1, .dll y .exe descargados de Internet. Es necesario establecer la directiva de ejecución antes de ejecutar el script. Asegúrese de ejecutar la ventana de Azure PowerShell como administrador. Ejecute "Set-ExecutionPolicy unrestricted". Cuando se le solicite, escriba "Y".

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Ejecute Get-ExecutionPolicy para asegurarse de que el comando funcionó.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Vaya al directorio que contiene los scripts y la aplicación del generador.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Escriba ".\\Setup.ps1" para configurar la cuenta de Azure, crear y configurar todos los recursos necesarios y empezar a generar eventos. El script seleccionará aleatoriamente una región para crear los recursos. Si quiere especificar la región, pase el parámetro - location, como se muestra en el siguiente ejemplo:

**.\\Setup.ps1-ubicación "centro de EE. UU."**

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

El script abrirá la página de inicio de sesión de Microsoft Azure. Escriba los credenciales de la cuenta.

Tenga en cuenta que si la cuenta tiene acceso a varias suscripciones, se le pedirá que escriba el nombre de la suscripción que quiere utilizar para el tutorial.

El script puede tardar varios minutos en ejecutarse. Una vez completado, debería obtener un resultado semejante a la siguiente captura de pantalla.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

También verá otra ventana parecida a la captura de pantalla de abajo. Esta aplicación envía eventos a su Centro de eventos y es necesaria para ejecutar los ejercicios del tutorial. Por lo tanto, no detenga o cierre esta ventana hasta que finalice el tutorial.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

Ahora podrá ver todos los recursos creados en el Portal de administración de Azure. Vaya a <https://manage.windowsazure.com> e inicie sesión con los credenciales de su cuenta.

### Centros de eventos

Haga clic en el elemento de menú "Bus de servicio" en el lado izquierdo del Portal de administración de Azure para ver los Centros de eventos que creó el script de la sección anterior.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

Verá todos los espacios de nombres disponibles en la suscripción. Haga clic en uno que empiece por "TollData", (en nuestro ejemplo será TollData4637388511). Haga clic en la ficha "Centros de eventos".

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

Verá dos centros de eventos denominados *entry* y *exit* en este espacio de nombres.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)

### Contenedor de Almacenamiento de Azure

Haga clic en el elemento de menú "Almacenamiento" en el lado izquierdo del Portal de administración de Azure para ver el contenedor de almacenamiento que se usa en el Laboratorio.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)

Haga clic en uno que empiece por "tolldata", (en nuestro ejemplo será tolldata4637388511). Abra la ficha "Contenedores" para ver el contenedor que se creó.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

Haga clic en el contenedor "tolldata" para ver el archivo JSON cargado con los datos de matriculación de los vehículos.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image13.png)

### Base de datos SQL de Azure

Haga clic en el elemento de menú "Bases de datos SQL" en el lado izquierdo del Portal de administración de Azure para ver la Base de datos SQL de Azure que se usará en el Laboratorio.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image14.png)

Haga clic en "TollDataDB".

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)

Copie el nombre del servidor sin el número de puerto (*nombredeservidor*.database.windows.net, por ejemplo).

## Conexión a la Base de datos desde Visual Studio

Usaremos Visual Studio para acceder a los resultados de consulta de la base de datos de salida.

Conectarse a la base de datos de Azure (el destino) desde Visual Studio:

1) Abra Visual Studio, haga clic en "Herramientas" y luego en el elemento de menú "Conectar a base de datos...".

2) Si se le solicita, seleccione "Microsoft SQL Server" como origen de datos.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)

3) En el campo Nombre del servidor pegue el nombre del SQL Server que copió en la sección anterior del Portal de Azure (*nombredeservidor*.database.windows.net)

4) En el campo Autenticación elija Autenticación de SQL Server.

5) Escriba "tolladmin" como nombre de usuario y "123toll!" como contraseña.

6) Elija TollDataDB como base de datos

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)
    
7) Haga clic en Aceptar.

8) Abra el Explorador de servidores.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)
  
9) Vea las 4 tablas creadas en la base de datos TollDataDB.
  
![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)
  
## Generador de eventos: proyecto ejemplo de TollApp

El script de PowerShell comienza a enviar eventos automáticamente mediante el programa de aplicación de ejemplo TollApp. No es necesario realizar pasos adicionales.

Sin embargo, si está interesado en los detalles de implementación, puede encontrar el código fuente de la aplicación TollApp en GitHub [samples/TollApp](https://github.com/streamanalytics/samples/tree/master/TollApp)

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

##Creación de un Análisis de transmisiones

Para crear un nuevo trabajo de análisis, vaya al Portal de Azure, abra Análisis de transmisiones y haga clic en "Nuevo" en la esquina inferior izquierda de la página.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)

Haga clic en "Creación rápida". Seleccione la misma región donde el script crea los otros recursos.

Para la configuración de la "Cuenta de almacenamiento de supervisión regional", seleccione "Crear nueva cuenta de almacenamiento" y asígnele un nombre único. Análisis de transmisiones de Azure usará esta cuenta para almacenar la información de supervisión de todos los futuros trabajos.

Haga clic en "Crear un Análisis de transmisiones" en la parte inferior de la página.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## Definición de orígenes de entrada

Haga clic en el trabajo de análisis creado en el portal.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image23.jpg)

Abra la ficha "Entradas" para definir el origen de datos.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.jpg)

Haga clic en "Agregar entrada",

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)

En la primera página, seleccione "Flujo de datos".

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image26.png)

Seleccione "Centro de eventos" en la segunda página del asistente.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image27.png)

Escriba "EntryStream" como alias de entrada.

Haga clic en la lista desplegable de "Centro de eventos" y seleccione una que empiece por "TollData" (por ejemplo, TollData9518658221).

Seleccione "entry" como nombre del Centro de eventos y "all" como nombre de la directiva de Centro de eventos.

La configuración se verá así:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

Continúe a la página siguiente. Seleccione los valores como JSON y codificación UTF8.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image29.png)

Haga clic en Aceptar en la parte inferior del cuadro de diálogo para finalizar al asistente.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image30.jpg)

Tiene que volver a realizar los mismos pasos para crear la segunda entrada de Centro de eventos para el flujo con eventos "Exit". En la tercera página, asegúrese de escribir los valores como se muestran a continuación.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)

Ahora tiene dos flujos de entrada definidos:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.jpg)

A continuación, agregaremos la entrada de datos de "Referencia" para el archivo blob con los datos de registro de los vehículos.

Haga clic en "Agregar entrada". Seleccione "Datos de referencia".

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image33.png)

En la página siguiente, seleccione la cuenta de almacenamiento que empieza por "tolldata". El nombre del contenedor debe ser "tolldata" y el nombre del blob en patrón de ruta de acceso debe ser "registration.json". Este nombre de archivo distingue mayúsculas de minúsculas, por lo que asegúrese de escribirlo en minúsculas.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)

En la página siguiente, seleccione los valores tal como se muestra a continuación y haga clic en Aceptar para finalizar al asistente.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image35.png)

Ahora todas las entradas están definidas.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image36.jpg)

## Defininición de salida

Vaya a la ficha "Salidas" y haga clic en "Agregar salida".

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.jpg)

Elija "Base de datos SQL".

Seleccione el nombre de servidor que se usó en "Conexión a la Base de datos desde Visual Studio". El nombre de la base de datos debe ser TollDataDB.

Escriba "tolladmin" como nombre de usuario y "123toll!" como contraseña. El nombre de tabla debe ser "TollDataRefJoin".

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.jpg)

## Consulta de Análisis de transmisiones de Azure

La ficha Consulta contiene una consulta SQL que realiza la transformación de los datos entrantes.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.jpg)

En este tutorial intentaremos responder varias cuestiones empresariales relacionadas con datos Toll y construir consultas de Análisis de transmisiones que puedan usarse para dar una respuesta adecuada en el Análisis de transmisiones.

Antes de empezar el primer trabajo de Análisis de transmisiones, veamos algunos escenarios y sintaxis de consulta.

## Introducción al lenguaje de consulta de Análisis de transmisiones de Azure
-----------------------------------------------------

Supongamos que necesitamos contar el número de vehículos que entra en el peaje. Dado que se trata de un flujo continuo de eventos, es esencial definir un "período de tiempo". Por lo tanto, se tiene que modificar la pregunta para que indique el "número de vehículos que entran al peaje cada 3 minutos". Esto se denomina comúnmente "tumbling count".

Veamos la consulta de Análisis de transmisiones que responde a esta pregunta:

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

Como puede ver, Análisis de transmisiones usa un lenguaje de consulta similar a SQL con algunas extensiones adicionales para permitir especificar los aspectos de la consulta que están relacionados con la hora.

Para obtener más información, puede leer sobre las construcciones de [Administración de tiempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) y [Ventanas](https://msdn.microsoft.com/library/azure/dn835019.aspx) que se usan en una consulta de MSDN.

## Pruebas de consultas de Análisis de transmisiones de Azure

Ahora que hemos escrito nuestra primera consulta de Análisis de transmisiones, es momento de probarla usando los archivos de datos de ejemplo ubicados en la carpeta TollApp en la siguiente ruta:

**..\\TollApp\\TollApp\\Data**

Esta carpeta contiene los archivos siguientes:

1.  Entry.json

2.  Exit.json

3.  Registration.json

## Pregunta 1: número de vehículos que entran al peaje

Abra el Portal de administración de Azure y vaya al trabajo de Análisis de transmisiones de Azure que creó. Abra la ficha Consulta y copie y pegue la consulta de la sección anterior.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image40.png)

Para comprobar esta consulta usando datos de ejemplo, haga clic en el botón Probar. En el cuadro de diálogo que se abre, vaya a Entry.json, un archivo con datos de ejemplo del flujo de eventos EntryTime.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)

Compruebe que el resultado de la consulta es el esperado:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.jpg)

## Pregunta 2: notificar el tiempo total que necesita cada vehículo para atravesar el peaje

Queremos saber el tiempo promedio que tarda un vehículo en pasar a través del peaje para valorar la eficiencia y la experiencia del cliente.

Para ello se debe combinar el flujo que contiene EntryTime con el flujo que contiene ExitTime. Se combinarán los flujos de TollId y las columnas LicencePlate. El operador JOIN requiere que se especifique un margen temporal que describa una diferencia de tiempo aceptable entre los eventos combinados. Usaremos la función DATEDIFF para especificar que los eventos no durarán más de 15 minutos entre sí. También se aplicará la función DATEDIFF a las horas Exit y Entry para calcular el tiempo real que transcurre un automóvil en el peaje. Tenga en cuenta la diferencia del uso de DATEDIFF en una instrucción SELECT en comparación con su uso en una condición JOIN.

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

Para probar esta consulta, actualícela en la ficha Consulta del trabajo:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.jpg)

Haga clic en Probar y especifique los archivos de entrada de ejemplo para EntryTime y ExitTime.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image44.png)

Active la casilla para probar la consulta y ver los resultados:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## Pregunta 3: notificar todos los vehículos comerciales con matriculaciones expiradas

Análisis de transmisiones de Azure puede usar instantáneas estáticas de datos para combinar con flujos de datos temporales. Para demostrar esta capacidad se utilizará la siguiente pregunta de ejemplo.

Si un vehículo comercial está registrado en la empresa de peaje, puede atravesar la cabina sin detenerse para inspección. Se usará la tabla de búsqueda de matriculaciones de vehículos comerciales para identificar todos los vehículos comerciales con matriculaciones expiradas.

    SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN Registration
    ON EntryStream.LicensePlate = Registration.LicensePlate
    WHERE Registration.Expired = '1'

Tenga en cuenta que para probar una consulta con datos de referencia es necesario definir un origen de entrada para los datos de referencia, como hicimos en el paso 5.

Para probar esta consulta, pegue la consulta en la ficha Consulta, haga clic en Probar y especifique los dos orígenes de entrada:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

Vea el resultado de la consulta:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image47.png)

## Inicio de un trabajo de Análisis de transmisiones


Ahora que hemos escrito nuestra primera consulta de Análisis de transmisiones, es momento de finalizar la configuración e iniciar el trabajo. Guarde la consulta de la pregunta 3, que generará un resultado que coincidirá con el esquema de la tabla de salida **TollDataRefJoin**.

Vaya al Panel del trabajo y haga clic en Iniciar.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.jpg)

En el cuadro de diálogo que aparece, cambie la hora de inicio del resultado a "Hora personalizada". Retrase el reloj y establézcalo una hora antes. Esto garantizará que se procesan todos los eventos del Centro de eventos desde el momento en que se inició la generación de los eventos al comienzo del tutorial. Ahora haga clic en la marca de verificación para iniciar el trabajo.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

El trabajo puede tardar unos minutos en iniciarse. Podrá ver el estado en la página de nivel superior de Análisis de transmisiones.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.jpg)

## Comprobación de resultados en Visual Studio

Abra el Explorador de servidores de Visual Studio y haga clic con el botón derecho en la tabla TollDataRefJoin. Seleccione "Mostrar datos de tabla" para ver el resultado del trabajo.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## Escalado horizontal de trabajos de Análisis de transmisiones de Azure

Análisis de transmisiones de Azure está diseñado para escalar de manera elástica y ser capaz de controlar una gran carga de datos. Las consultas de Análisis de transmisiones puede usar una cláusula **PARTITION BY** para indicar al sistema que este paso se escalará horizontalmente. PartitionId es una columna especial agregada por el sistema que coincide con el identificador de partición de la entrada (Centro de eventos).

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId    

Detenga el trabajo actual, actualice la consulta en la ficha Consulta y abra la ficha Escala.

Las unidades de streaming definen la cantidad de capacidad de proceso, que el trabajo puede recibir.

Mueva el control deslizante a 6.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.jpg)

Vaya a la ficha Salida y cambie el nombre de la tabla SQL a "TollDataTumblingCountPartitioned".

Ahora, si inicia el trabajo, Análisis de transmisiones podrá distribuir el trabajo entre más recursos de proceso y lograr un rendimiento mejor. Tenga en cuenta que la aplicación TollApp también envía eventos con que particiona TollId.

## Supervisión

La ficha Supervisión contiene estadísticas sobre el trabajo en ejecución.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image53.png)

Puede acceder a los registros de operaciones desde la pestaña Panel.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image54.jpg)

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image55.png)

Para obtener información adicional sobre un evento determinado, selecciónelo y haga clic en el botón "Detalles".

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image56.png)

## Conclusión

Este tutorial ofrecía una introducción al servicio de Análisis de transmisiones de Azure. Se mostró cómo configurar entradas y salidas de un trabajo de Análisis de transmisiones. Gracias al escenario de los datos de peaje, se pudieron explicar los tipos más comunes de problemas que surgen en el espacio de datos en movimiento y cómo pueden resolverse con simples consultas de tipo SQL en Análisis de transmisiones. Asimismo, se describieron las construcciones de extensión SQL para trabajar con datos temporales; se mostró cómo combinar los flujos de datos y cómo enriquecerlos con datos de referencia estática; y, finalmente, se explicó cómo escalar horizontalmente una consulta para lograr una mayor capacidad de procesamiento.

Este tutorial proporciona una buena cobertura de introducción, pero no se trata de un tutorial completo de ningún modo. Puede encontrar más patrones de consulta que usan el lenguaje SAQL [aquí](stream-analytics-stream-analytics-query-patterns.md). Para obtener más información sobre Análisis de transmisiones, consulte la [documentación en línea](https://azure.microsoft.com/documentation/services/stream-analytics/).

## Limpiar la cuenta de Azure

Detenga el trabajo de análisis de secuencia desde el Portal de Azure.

El script Setup.ps1 crea dos Centros de eventos de Azure y un servidor de base de datos SQL. Las siguientes instrucciones le ayudarán a limpiar los recursos al terminar el tutorial.

En la ventanta PowerShell, escriba ".\\Cleanup.ps1". Esto iniciará el script que elimina los recursos que se usaron en este tutorial.

Tenga en cuenta que los recursos se identifican por el nombre. Asegúrese de revisar cuidadosamente cada elemento antes de confirmar la eliminación.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image57.png)

<!---HONumber=AcomDC_0224_2016-->