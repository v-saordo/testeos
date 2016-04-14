<properties
    pageTitle="Prueba de carga de la aplicación mediante Visual Studio Team Services | Microsoft Azure"
    description="Aprenda a realizar pruebas de esfuerzo en sus aplicaciones de Azure Service Fabric mediante Visual Studio Team Services."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="10/28/2015"
    ms.author="cawa" />

# Prueba de carga de la aplicación mediante Visual Studio Team Services

En este artículo se muestra cómo usar las funciones de prueba de carga de Microsoft Visual Studio para realizar pruebas de esfuerzo en una aplicación. Se usa un back-end de servicio con estado de Azure Service Fabric y un front-end web de servicio sin estado. La aplicación de ejemplo que se usa aquí es un simulador de ubicación de aviones. En él se proporciona un identificador de avión, una ubicación de salida y un destino. El back-end de la aplicación procesa las solicitudes y el front-end muestra en un mapa el avión que coincide con los criterios.

En el siguiente diagrama se muestra la aplicación de Service Fabric que va a probar.

![Diagrama de la aplicación de ejemplo de ubicación de avión][0]

## Requisitos previos
Antes de comenzar, deberá hacer lo siguiente:

- Obtenga una cuenta de Visual Studio Team Services. Puede obtener una de forma gratuita en [Visual Studio Team Services](https://www.visualstudio.com).
- Obtenga e instale Visual Studio 2013 o Visual Studio 2015. Este artículo usa Visual Studio 2015 Enterprise Edition, pero Visual Studio 2013 y otras ediciones deberían funcionar de un modo similar.
- Implemente la aplicación en un entorno de ensayo. Consulte [Cómo implementar aplicaciones en un clúster remoto con Visual Studio](service-fabric-publish-app-remote-cluster.md) para obtener información acerca de este punto.
- Comprenda el patrón de uso de la aplicación. Esta información se usa para simular el modelo de carga.
- Comprenda el objetivo de las pruebas de carga. Esto le ayudará a interpretar y analizar los resultados de la prueba de carga.

## Creación y ejecución del proyecto de prueba de carga y rendimiento web

### Creación de un proyecto de prueba de carga y rendimiento web.

1. Abra Visual Studio 2015. Elija **Archivo** > **Nuevo** > **Proyecto** en la barra de menú para abrir el cuadro de diálogo **Nuevo proyecto**.

2. Expanda el nodo **Visual C#** y elija **Prueba** > **Proyecto de prueba de carga y rendimiento web**. Asigne un nombre al proyecto y, a continuación, elija el botón **Aceptar**.

    ![Captura de pantalla del cuadro de diálogo Nuevo proyecto][1]

    Debería ver un nuevo proyecto de prueba de carga y rendimiento web en el Explorador de soluciones.

    ![Captura de pantalla del Explorador de soluciones con el nuevo proyecto][2]

### Grabación de un proyecto de prueba de rendimiento web

1. Abra el proyecto .webtest.

2. Elija el icono **Agregar grabación** para iniciar una sesión de grabación en el explorador.

    ![Captura de pantalla del icono Agregar grabación en un explorador][3]

    ![Captura de pantalla del botón Grabar en un explorador][4]

3. Vaya a la aplicación de Service Fabric. El panel de grabación debe mostrar las solicitudes web.

    ![Captura de pantalla de solicitudes web en el panel de grabación][5]

4. Realice una secuencia de acciones que espera que realicen los usuarios. Estas acciones se usan como patrón para generar la carga.

5. Cuando haya terminado, elija el botón **Detener** para parar la grabación.

    ![Captura de pantalla del botón Detener][6]

    El proyecto .webtest en Visual Studio debe haber capturado una serie de solicitudes. Los parámetros dinámicos se reemplazan automáticamente. En este momento, puede eliminar las solicitudes de dependencia adicionales repetidas que no forman parte de su escenario de prueba.

6. Guarde el proyecto y, a continuación, elija el comando **Ejecutar prueba** para ejecutar la prueba de rendimiento web localmente y asegurarse de que todo funciona correctamente.

    ![Captura de pantalla del comando Ejecutar prueba][7]

### Parametrización de la prueba de rendimiento web

La prueba de rendimiento web se puede parametrizar al convertirla en una prueba de rendimiento web codificada y luego editando el código. Como alternativa, puede enlazar la prueba de rendimiento web con una lista de datos para que la prueba se itere a través de los datos. Consulte [Generar y ejecutar una prueba de rendimiento web codificada](https://msdn.microsoft.com/library/ms182552.aspx) para obtener más información sobre cómo convertir la prueba de rendimiento web en una prueba codificada. Consulte [Agregar un origen de datos a una prueba de rendimiento web](https://msdn.microsoft.com/library/ms243142.aspx) para obtener información sobre cómo enlazar datos a una prueba de rendimiento web.

En este ejemplo, convertiremos la prueba de rendimiento web en una prueba codificada para que pueda reemplazar el identificador del avión con un GUID generado y agregar más solicitudes para enviar vuelos a ubicaciones diferentes.

### Creación de un proyecto de prueba de carga

Un proyecto de prueba de carga se compone de uno o varios de los escenarios descritos por la prueba de rendimiento web y la prueba unitaria, junto con los valores adicionales especificados de prueba de carga. Los pasos siguientes muestran cómo crear un proyecto de prueba de carga:

1. En el menú contextual de su proyecto de prueba de carga y rendimiento web, elija **Agregar** > **Prueba de carga**. En el asistente de **prueba de carga**, elija el botón **Siguiente** para configurar los valores de la prueba.

2. En la sección **Patrón de carga**, elija si desea una carga de usuarios constante o una carga por pasos, que comienza con unos pocos usuarios y los va aumentando a medida que pasa el tiempo.

    Si tiene una buena estimación de la cantidad de carga de usuario y quiere ver cómo rinde el sistema actual, elija **Carga constante**. Si su objetivo es saber si el rendimiento es constante con distintas cargas, elija **Carga por pasos**.

3. En la sección **Combinación de pruebas**, elija el botón **Agregar** y seleccione la prueba que quiere incluir en la prueba de carga. Puede usar la columna **Distribución** para especificar el porcentaje total de pruebas que se ejecutaron para cada prueba.

4. En la sección **Parámetros de ejecución**, especifique la duración de la prueba de carga.

    >[AZURE.NOTE] La opción **Iteraciones de prueba** está disponible solo al ejecutar pruebas de carga localmente mediante Visual Studio.

5. En la sección **Ubicación** de **Parámetros de ejecución**, especifique la ubicación donde se generan las solicitudes de prueba de carga. El asistente puede solicitarle que inicie sesión en su cuenta de Team Services. Inicie sesión y elija una ubicación geográfica. Cuando haya terminado, elija el botón **Finalizar**.

6. Después de crear la prueba de carga, abra el proyecto .loadtest y elija el actual parámetro de ejecución, como **Parámetros de ejecución** > **Parámetros de ejecución1 [Activo]**. Se abrirán los parámetros de ejecución en la ventana **Propiedades**.

7. En la sección **Resultados** de la ventana de propiedades **Parámetros de ejecución**, el valor **Almacenamiento de detalles de tiempo** debería tener **Ninguno** como valor predeterminado. Cambie este valor a **Todos los detalles individuales** para obtener más información sobre el resultado de la prueba de carga. Vea [Pruebas de carga](https://www.visualstudio.com/load-testing.aspx) para obtener más información acerca de cómo conectarse a Visual Studio Team Services y ejecutar una prueba de carga.

### Ejecución de la prueba de carga mediante Visual Studio Team Services

Elija el comando **Ejecutar prueba de carga** para iniciar la ejecución de la prueba.

![Captura de pantalla del comando Ejecutar prueba de carga][8]

## Visualización y análisis de los resultados de las pruebas de carga

A medida que progresa la prueba de carga la información sobre el rendimiento se va representando gráficamente. Debería ver algo parecido al siguiente gráfico.

![Captura de pantalla del gráfico de rendimiento para los resultados de pruebas de carga][9]

1. Elija el vínculo **Descargar informe** cerca de la parte superior de la página. Una vez que se haya descargado el informe, elija el botón **Ver informe**.

    En la pestaña **Gráfico**, puede ver los gráficos de varios contadores de rendimiento. En la pestaña **Resumen**, aparecen los resultados generales de la prueba . En la pestaña **Tablas** se muestra el número total de pruebas de carga superadas y no superadas.

2. Elija el número de vínculos en las columnas **Prueba** > **Con error** y **Errores** > **Recuento** para ver los detalles del error.

    La pestaña **Detalle** muestra información de escenario virtual de prueba y de usuario para las solicitudes con error. Estos datos pueden ser útiles si la prueba de carga incluye varios escenarios.

Consulte [Analizar los resultados de pruebas de carga en la vista Gráficos del Analizador de prueba de carga](https://www.visualstudio.com/load-testing.aspx) para obtener más información sobre cómo ver los resultados de las pruebas de carga.

## Automatización de la prueba de carga

La prueba de carga de Visual Studio Team Services proporciona API que permiten administrar las pruebas de carga y analizar los resultados en una cuenta de Team Services. Consulte [Las API de REST de las pruebas de carga en la nube](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx) para obtener más información.

## Pasos siguientes
- [Supervisión y diagnóstico de servicios en una configuración de desarrollo de máquina local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[0]: ./media/service-fabric-vso-load-test/OverviewDiagram.png
[1]: ./media/service-fabric-vso-load-test/NewProjectDialog.png
[2]: ./media/service-fabric-vso-load-test/Project.png
[3]: ./media/service-fabric-vso-load-test/AddRecording.png
[4]: ./media/service-fabric-vso-load-test/AddRecording2.png
[5]: ./media/service-fabric-vso-load-test/ActionSequence.png
[6]: ./media/service-fabric-vso-load-test/StopRecording.png
[7]: ./media/service-fabric-vso-load-test/RunTest.png
[8]: ./media/service-fabric-vso-load-test/RunTest2.png
[9]: ./media/service-fabric-vso-load-test/Graph.png

<!---HONumber=AcomDC_0128_2016-->