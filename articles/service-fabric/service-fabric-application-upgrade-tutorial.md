 <properties
   pageTitle="Tutorial sobre las actualizaciones de aplicaciones de Service Fabric | Microsoft Azure"
   description="Este artículo le guía a través de la experiencia de implementación de una aplicación de Service Fabric, la modificación del código y la aplicación de actualizaciones con Visual Studio."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/04/2016"
   ms.author="subramar"/>



# Tutorial sobre la actualización de aplicaciones de Service Fabric con Visual Studio

Azure Service Fabric simplifica el proceso de actualización de aplicaciones en la nube asegurándose de que solo se actualicen los servicios modificados y que el estado de las aplicaciones se supervise durante todo el proceso de actualización. También revierte automáticamente la aplicación a la versión anterior si se detecta algún problema. Las actualizaciones de aplicaciones de Service Fabric presentan un *tiempo de inactividad cero*, ya que la aplicación puede actualizarse sin que se ocasione tiempo de inactividad. Este tutorial explica cómo llevar a cabo una actualización sencilla acumulativa desde Visual Studio.


## Paso 1: Crear y publicar el ejemplo de Visual Objects

Puede realizar estos pasos descargando la aplicación de GitHub y agregando los archivos **webgl utils.js** y **gl-matrix-min.js** al proyecto, como se menciona en el archivo Léame del ejemplo. Sin eso, la aplicación no funcionará. Después de agregar estos elementos al proyecto, genere y publique la aplicación haciendo clic con el botón derecho en el proyecto de aplicación, **VisualObjectsApplication**, luego seleccione el comando **Publicar** en el elemento de menú de Service Fabric de la siguiente forma.

![Menú contextual para una aplicación de Service Fabric][image1]

Aparecerá otro cuadro emergente donde puede configurar **Punto de conexión de la conexión** como **Clúster local**. La ventana debe ser similar a la siguiente antes de hacer clic en **Publicar**.

![Publicación de una aplicación de Service Fabric][image2]

Ahora ya puede hacer clic en **Publicar** en el cuadro de diálogo. Ya puede usar el [explorador de Service Fabric para ver el clúster y la aplicación](service-fabric-visualizing-your-cluster.md). La aplicación Visual Objects tiene un servicio web al que puede acudir escribiendo [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) en la barra de direcciones del navegador. Debería ver 10 objetos visuales flotantes desplazándose por la pantalla.

## Paso 2: Actualizar el ejemplo de objetos visuales

Puede que observe que con la versión que se implementó en el Paso 1, los objetos visuales no giran. Vamos a actualizar esta aplicación a una donde los objetos visuales giren.

Elija el proyecto VisualObjects.ActorService dentro de la solución VisualObjects y abra el archivo **StatefulVisualObjectActor.cs**. Dentro de ese archivo, vaya al método `MoveObject`, comente `this.State.Move()` y quite la marca de comentario de `this.State.Move(true)`. Este cambio hará que los objetos giren después de actualizar el servicio. Ahora puede compilar (no recompilar) la solución, que generará los proyectos modificados. Si selecciona **Recompilar todo**, tendrá que actualizar las versiones de todos los proyectos.

También necesitamos generar la versión de nuestra aplicación. Para realizar los cambios de versión puede utilizar la opción **Editar archivos de manifiesto** de Visual Studio después de hacer clic con el botón derecho en la solución. Se abrirá el cuadro de diálogo para las versiones de edición de la siguiente forma:

![Cuadro de diálogo de control de versiones][image3]

Elija la pestaña **Editar las versiones del manifiesto** y actualice las versiones de los proyectos modificados y sus paquetes de código, junto con la aplicación, a la versión 2.0.0. Una vez realizados los cambios, el manifiesto debe ser similar al siguiente (las partes negrita muestran los cambios):

![Actualización de versiones][image4]

Las herramientas de Visual Studio pueden hacer resúmenes automáticos de las versiones si selecciona **Actualizar automáticamente versiones de aplicación y de servicio**. Si utiliza [SemVer](http://www.semver.org), tiene que actualizar el código o la opción de paquete de configuración solo si esa opción está seleccionada.

Guarde los cambios y marque la casilla **Actualizar la aplicación**.


## Paso 3: Actualizar la aplicación

Familiarícese con los [parámetros de actualización de la aplicación](service-fabric-application-upgrade-parameters.md) y el [proceso de actualización](service-fabric-application-upgrade.md) para obtener una buena comprensión de los distintos parámetros de actualización, tiempos de espera y criterio de estado que se puede aplicar. Para los fines de este tutorial, dejaremos el criterio de evaluación del estado del servicio definido en el valor predeterminado (modo sin supervisión). Para configurar estas opciones, elija **Configurar opciones de actualización** y modifique los parámetros según sea necesario.

Ahora, estamos preparados para iniciar la actualización de la aplicación mediante la opción **Publicar**. Esto actualizará la aplicación a la versión 2.0.0 en la que giran los objetos. Observará que Service Fabric actualiza los dominios de actualización uno a uno (algunos objetos se actualizará en primer lugar y luego otros). El servicio está accesible durante este tiempo a través del cliente (explorador).


Ahora, a medida que se lleva a cabo la actualización de la aplicación, puede supervisar el proceso usando el explorador de Service Fabric (pestaña **Actualizaciones en curso** en las aplicaciones).

En unos minutos, todos los dominios de actualización deben estar actualizados (completados). La ventana de salida de Visual Studio también debe indicar que la actualización ha finalizado. Ahora *todos* los objetos visuales de la ventana del explorador deben haber comenzado a girar.

Puede que desee cambiar las versiones y pasar de la versión 2.0.0 a la versión 3.0.0 como ejercicio, o incluso volver de la versión 2.0.0 a la 1.0.0. Juegue con los tiempos de espera y las directivas de mantenimiento para familiarizarse. Cuando se realiza la implementación en un clúster de Azure, los parámetros que se utilizan serán diferentes de los que se usan cuando se va a implementar en un clúster local; se recomienda establecer los tiempos de espera con prudencia.


## Pasos siguientes

El artículo [Actualización de aplicaciones de Service Fabric con PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) le explica en detalle lo que tiene que hacer para actualizar una aplicación mediante PowerShell.

Puede controlar cómo se actualiza una aplicación usando [parámetros de actualización](service-fabric-application-upgrade-parameters.md).

Consiga que sus actualizaciones de aplicaciones sean compatibles aprendiendo a usar la [serialización de datos](service-fabric-application-upgrade-data-serialization.md).

Aprenda a usar funcionalidades avanzadas para actualizar una aplicación. Para ello, consulte [Actualización de la aplicación de Service Fabric: temas avanzados](service-fabric-application-upgrade-advanced.md).

Solucione problemas habituales en las actualizaciones de aplicaciones consultando los pasos que figuran en [Solucionar problemas de actualizaciones de aplicaciones](service-fabric-application-upgrade-troubleshooting.md).



[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png

<!---HONumber=AcomDC_0211_2016-->