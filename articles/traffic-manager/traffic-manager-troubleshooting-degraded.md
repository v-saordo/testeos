<properties
   pageTitle="Solución de problemas de estado degradado en Administrador de tráfico de Azure"
   description="Cómo solucionar problemas de perfiles de Administrador de tráfico cuando se muestra como muestra un estado degradado."
   services="traffic-manager"
   documentationCenter=""
   authors="kwill-MSFT"
   manager="carmonm"
   editor="joaoma" />

<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/02/2015"
   ms.author="joaoma" />

# Solución de problemas de estado degradado en el Administrador de tráfico de Azure

En esta página se describirá cómo solucionar problemas del perfil del Administrador de tráfico de Azure, que muestra un estado degradado y proporcionar algunos puntos clave para entender los sondeos del administrador de tráfico.

Ha configurado un perfil de Administrador de tráfico orientado a algunos de sus servicios hospedados cloudapp.net y, tras unos segundos, verá el estado como Degradado.

![degradedstate](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degraded.png)

Si accede a la ficha Extremos de ese perfil, verá uno o varios de los extremos en estado Sin conexión:

![sin conexión](./media/traffic-manager-troubleshooting-degraded/traffic-manager-offline.png)

## Notas importantes sobre los sondeos del Administrador de tráfico

- Administrador de tráfico solo considera que un extremo está EN LÍNEA si el sondeo recibe un 200 desde la ruta de acceso del sondeo.
- Un redireccionamiento 30x (o cualquier otra respuesta distinta de 200) producirá un error, incluso si la dirección URL a la que se ha efectuado el redireccionamiento devuelve un 200.

- En los sondeos HTTPs, se omiten los errores de certificado.
 
- No importa el contenido real de la ruta de acceso del sondeo, siempre y cuando se devuelva 200. Una técnica común si el contenido del sitio web real no devuelve 200 (por ejemplo, si las páginas ASP efectúan un redireccionamiento a una página de inicio de sesión de ACS o alguna otra dirección URL de CNAME) consiste en establecer la ruta de acceso en algo similar a "/favicon.ico".
 
- El procedimiento recomendado es establecer la ruta de acceso del sondeo en algo que tenga una lógica suficiente para determinar si el sitio está activo o inactivo. En el ejemplo anterior, si la ruta de acceso se establece en "/favicon.ico", solamente estará probando si w3wp.exe responde, pero no si el sitio web está en buen estado. Una mejor opción sería establecer una ruta de acceso a algo como "/Probe.aspx" y dentro de Probe.aspx incluir lógica suficiente para determinar si su sitio es correcto (por ejemplo, comprobar los contadores de rendimiento para asegurarse de que no está utilizando el total de la capacidad de la CPU o recibiendo un gran número de solicitudes con error, intente obtener acceso a recursos, como el estado de la sesión o la base de datos para asegurarse de que la lógica de la aplicación funciona, etc.).
 
- Si se degradan todos los extremos de un perfil, Administrador de tráfico tratará todos los extremos como correctos y enrutará el tráfico a todos los extremos. Esto permite asegurarse de que cualquier posible problema con el mecanismo de sondeo que provoca la consideración de sondeos como incorrectos de manera errónea no ocasionará una interrupción completa del servicio.

  

## Solución de problemas

Una herramienta para solucionar problemas de errores de sondeo de Administrador de tráfico es wget. Puede obtener los archivos binarios y el paquete de dependencias de [wget](http://gnuwin32.sourceforge.net/packages/wget.htm). Tenga en cuenta que puede utilizar otros programas como Fiddler o curl en lugar de wget (básicamente solo necesita algo que le mostrará la respuesta HTTP sin formato).

Una vez que instale wget, vaya a un símbolo del sistema y ejecute wget en la dirección URL + el puerto y la ruta de acceso del sondeo que está configurado en el Administrador de tráfico. En este ejemplo sería http://watestsdp2008r2.cloudapp.net:80/Probe.

![solución de problemas](./media/traffic-manager-troubleshooting-degraded/traffic-manager-troubleshooting.png)

Uso de Wget:

![wget](./media/traffic-manager-troubleshooting-degraded/traffic-manager-wget.png)

 

Tenga en cuenta que wget indica que la dirección URL devuelve un redireccionamiento 301 a http://watestsdp2008r2.cloudapp.net/Default.aspx. Como se obtiene de la sección "Notas importantes sobre los sondeos de Administrador de tráfico" anterior, un redireccionamiento 30x se considera un error por parte del sondeo del Administrador de tráfico y esto hará que el sondeo de informe de un estado sin conexión. En este punto es sencillo comprobar la configuración del sitio web y asegurarse de que la ruta de acceso /Probe devuelve 200 (o volver a configurar el sondeo de Administrador de tráfico para que señale a una ruta de acceso que devuelva 200).

 

Si el sondeo está usando el protocolo HTTPs, podrá agregar el parámetro "--no-check-certificate" a wget para que pase por alto la falta de coincidencia de certificados en la dirección URL de cloudapp.net.


## Pasos siguientes


[Información acerca de los métodos de enrutamiento del tráfico del Administrador de tráfico](traffic-manager-load-balancing-methods.md)

[¿Qué es el Administrador de tráfico?](../traffic-manmager-overview.md)

[Servicios en la nube](http://go.microsoft.com/fwlink/?LinkId=314074)

[Sitios web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Operaciones del Administrador de tráfico (referencia de la API de REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets del Administrador de tráfico de Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)
 

<!---HONumber=AcomDC_1210_2015-->