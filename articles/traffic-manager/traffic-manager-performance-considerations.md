<properties
   pageTitle="Consideraciones de rendimiento del Administrador de tráfico de Azure | Microsoft Azure"
   description="Descripción del rendimiento en el Administrador de tráfico y cómo probar el rendimiento de su sitio web al usar el Administrador de tráfico"
   services="traffic-manager"
   documentationCenter=""
   authors="kwill-MSFT"
   manager="adinah"
   editor="joaoma" />

<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="joaoma" />


# Consideraciones de rendimiento sobre el Administrador de tráfico

En esta página se explican las consideraciones de rendimiento relacionadas con el uso de Administrador de tráfico. Escenarios como: tiene un sitio web en la región de EE. UU. y uno en Asia. Uno de ellos presenta problemas en la comprobación de estado de los sondeos de Administrador de tráfico, todos los usuarios se enviarán a la región que funciona correctamente y el comportamiento podría parecer un problema de rendimiento. Sin embargo, sería un comportamiento esperado si consideramos la distancia con respecto a la solicitud del usuario.

  

## Nota importante acerca del funcionamiento del Administrador de tráfico

- El Administrador de tráfico básicamente solo hace una cosa: la resolución de DNS. Esto significa que el único impacto en el rendimiento que puede tener el Administrador de tráfico en su sitio web es la búsqueda de DNS inicial.
- Un punto de aclaración sobre la búsqueda de DNS del Administrador de tráfico. Administrador de tráfico rellena y actualiza con regularidad los servidores de raíz DNS de Microsoft basados en su directiva y en los resultados del sondeo. Por lo tanto, incluso durante la búsqueda de DNS inicial no hay ninguna participación por parte del Administrador de tráfico ya que la solicitud DNS la controlan los servidores de raíz DNS de Microsoft normales. Si el Administrador se vuelve inactivo (por ejemplo, se produce un error en las máquinas virtuales que realizan el sondeo de directivas y la actualización de DNS), no habrá ningún impacto en su nombre de DNS de Administrador de tráfico, ya que las entradas de los servidores DNS de Microsoft se mantendrán; el único impacto será que el sondeo y la actualización basados en políticas no se producirán (por ejemplo, si su sitio principal deja de funcionar, Administrador de tráfico no podrá actualizar DNS para señalar su sitio de conmutación por error).
- El tráfico NO fluye a través del Administrador de tráfico. No hay ningún servidor de Administrador de tráfico que actúe como intermediario entre los clientes y su servicio hospedado de Azure. Una vez finalizada la búsqueda de DNS, Administrador de tráfico se eliminará completamente de la comunicación entre cliente y servidor.
- La búsqueda de DNS es muy rápida y se almacena en la memoria caché. La búsqueda de DNS inicial dependerá del cliente y de sus servidores DNS configurados. Normalmente, un cliente puede realizar una búsqueda de DNS en ~ 50 ms (consulte http://www.solvedns.com/dns-comparison/)). Una vez que se realice la primera búsqueda, los resultados se almacenarán en la memoria caché para el TTL de DNS, que para Administrador de tráfico es un valor predeterminado de 300 segundos.
- La directiva de Administrador de tráfico que elija (rendimiento, conmutación por error, round robin) no influye en el rendimiento de DNS. La directiva de rendimiento puede afectar negativamente a la experiencia del usuario, por ejemplo, si envía a usuarios de Estados Unidos a un servicio alojado en Asia, pero este problema de rendimiento no está causado por el Administrador de tráfico.

  

## Prueba del rendimiento del Administrador de tráfico

Hay algunos sitios web disponibles públicamente que puede usar para determinar el rendimiento y el comportamiento del Administrador de tráfico. Estos sitios son útiles para determinar la latencia de DNS y a cuál de los servicios hospedados se está dirigiendo a los usuarios de todo el mundo. Tenga en cuenta que la mayor parte de estas herramientas no almacenan en la memoria caché los resultados de DNS, de modo que la ejecución de las pruebas varias veces mostrará la búsqueda de DNS completa, mientras que los clientes que se conecten al extremo del Administrador de tráfico solo podrán ver el impacto completo del rendimiento máximo de la búsqueda de DNS durante el TTL.


## Herramientas de muestra para medir el rendimiento


Una de las herramientas más sencillas es WebSitePulse. Escriba la dirección URL y podrá ver las estadísticas, como el tiempo de resolución de DNS, primer byte, último byte y otras estadísticas de rendimiento. Puede elegir entre tres ubicaciones diferentes desde las que probar su sitio. En este ejemplo verá que la primera ejecución muestra que la primera búsqueda de DNS lleva 0,204 seg. La segunda vez que ejecutamos esta prueba en el mismo extremo del Administrador de tráfico, el tiempo de búsqueda de DNS será de 0,002 segundos, puesto que los resultados ya se han almacenado en la memoria caché.

http://www.websitepulse.com/help/tools.php


![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

Tiempo de DNS cuando se encuentra almacenado en la memoria caché:


![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)



Otra herramienta realmente para obtener el tiempo de resolución de DNS desde varias regiones geográficas simultáneamente es la herramienta de comprobación de sitios web de Watchmouse. Escriba la dirección URL y verá el tiempo de resolución de DNS, el tiempo de conexión y la velocidad desde varias ubicaciones geográficas. Esto también resulta útil para probar la directiva de rendimiento del Administrador de tráfico para ver a qué servicio hospedado se está enviando a sus diferentes usuarios de todo el mundo.

http://www.watchmouse.com/en/checkit.php


![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

http://tools.pingdom.com/: esto probará un sitio web y proporcionará estadísticas de rendimiento para cada elemento de la página en un gráfico visual. Si cambia a la pestaña Análisis de página podrá ver el porcentaje de tiempo dedicado a realizar la búsqueda de DNS.

 

http://www.whatsmydns.net/: este sitio realizará una búsqueda de DNS desde 20 ubicaciones geográficas distintas y mostrará los resultados en un mapa. Esto es una excelente representación visual que le ayudará a determinar a qué servicio hospedado se conectarán sus clientes.

 

http://www.digwebinterface.com: es similar al sitio de watchmouse, pero este muestra información de DNS más detallada, incluidos registros CNAME y A. Asegúrese de activar «Colorear resultados» y «Estadísticas» en Opciones y seleccione «Todo» en los servidores de nombres.

## Pasos siguientes


[Información acerca de los métodos de enrutamiento del tráfico del Administrador de tráfico](traffic-manager-load-balancing-methods.md)

[Pruebe la configuración del Administrador de tráfico](traffic-manager-testing-settings.md)

[Operaciones del Administrador de tráfico (referencia de la API de REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets del Administrador de tráfico de Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)
 

<!---HONumber=AcomDC_0218_2016-->