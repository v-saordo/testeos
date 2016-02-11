<properties
 pageTitle="Tamaños de los servicios en la nube"
 description="Enumera los diferentes tamaños para los roles web y de trabajo del servicio en la nube de Azure."
 services="cloud-services"
 documentationCenter=""
 authors="Thraka"
 manager="timlt"
 editor=""/>
<tags
 ms.service="cloud-services"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/14/2015"
 ms.author="adegeo"/>

# Tamaños de los servicios en la nube

En este tema se describen las opciones y los tamaños disponibles para las instancias de rol de servicio en la nube (roles web y roles de trabajo). También ofrece consideraciones de implementación que hay que tener en cuenta a la hora de planear usar estos recursos.

Las máquinas virtuales y servicios en la nube de Azure son dos de los distintos tipos de recursos de proceso que ofrece Azure. Para obtener más explicaciones, consulte [Cálculo de las opciones de hospedaje proporcionadas por Azure](fundamentals-application-models.md).

> [AZURE.NOTE]Para ver límites relacionados de Azure, consulte [Suscripción de Azure y límites de servicio, cuotas y restricciones](../azure-subscription-service-limits.md)

## Tamaños de instancias de roles web y de trabajo

Las consideraciones siguientes pueden ayudarle a decidirse por un tamaño:

* Las instancias de máquinas virtuales de la serie D están diseñadas para ejecutar aplicaciones que exigen mayor capacidad de proceso y rendimiento de disco temporal. Las máquinas virtuales de la serie D proporcionan procesadores más rápidos, una mayor proporción de memoria a núcleo y una unidad de estado sólido (SSD) para el disco temporal. Para obtener más información, consulte el anuncio en el blog de Azure, [Nuevos tamaños de máquinas virtuales de la serie D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).  

*   Serie de Dv2, una evolución de la serie D original, presenta una CPU más eficaz. La CPU de la serie Dv2 es un 35 % aproximadamente más rápida que la CPU de la serie D. Se basa en el procesador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz de la última generación; y con Intel Turbo Boost Technology 2.0, puede alcanzar los 3,2 GHz. La serie Dv2 tiene las mismas configuraciones de disco y memoria que la serie D.

    La disponibilidad regional de la serie Dv2 se basará en esta programación: octubre de 2015: Este 2 de EE. UU., Centro de EE. UU., Centro y norte de EE. UU., Oeste de EE. UU. Noviembre 2015: Este de EE. UU., Norte de Europa, Oeste de Europa Enero de 2016: Centro y Sur de EE. UU., Este de Asia y del Pacífico Sur, Sudeste de Asia y del Pacífico Sur, Este de Japón, Oeste de Japón, Este de Australia, Sudeste de Australia, Sur de Brasil

* Los roles web y los roles de trabajo requieren más espacio en el disco temporal que las máquinas virtuales de Azure debido a los requisitos del sistema. Los archivos del sistema reservan 4 GB de espacio para el archivo de paginación de Windows y 2 GB de espacio para el archivo de volcado de memoria de Windows.

* El disco del sistema operativo contiene el sistema operativo invitado de Windows e incluye la carpeta Archivos de programa (incluidas las instalaciones realizadas a través de tareas de inicio a menos que especifique otro disco), cambios del Registro, la carpeta System32 y .NET Framework.

* El disco de recursos local contiene registros de Azure y archivos de configuración, Diagnósticos de Azure (que incluye sus registros de IIS) y cualquier recurso de almacenamiento local que defina.

* El disco de aplicaciones es donde se extrae su .cspkg e incluye su sitio web, los archivos binarios, el proceso de host de rol, las tareas de inicio, web.config, etc.

* Los tamaños de máquina virtual A8/A10 y A9/A11 tienen las mismas capacidades. Las instancias de máquina virtual A8 y A9 incluyen otro adaptador de red que se conecta a una red de acceso de memoria directa remota (RDMA) para una rápida comunicación entre máquinas virtuales. Las instancias A8 y A9 están diseñadas para aplicaciones informáticas de alto rendimiento que requieren una comunicación constante y de baja latencia entre nodos durante la ejecución, por ejemplo, aplicaciones que utilizan la interfaz de paso de mensajes (MPI). Las instancias de máquina virtual A10 y A11 no incluyen el adaptador de red adicional. Las instancias A10 y A11 están diseñadas para aplicaciones informáticas de alto rendimiento que no requieren la comunicación constante y de baja latencia entre nodos, también conocidas como aplicaciones paramétricas o embarazosamente paralelas.

|Tamaño|Núcleos<br>de CPU|Memoria|Tamaños de disco|
|---|---|---|---|
|ExtraSmall|1|768 MB|SO = tamaño del SO invitado<br/>Recurso local = 15384 MB<br/>Aplicaciones = aprox. 1,5 GB|
|Pequeña|1|1,75 GB|SO = tamaño del SO invitado<br/>Recurso local = 225304 MB<br/>Aplicaciones = aprox. 1,5 GB|
|Mediano|2|3,5 GB|SO = tamaño del SO invitado<br/>Recurso local = 496664 MB<br/>Aplicaciones = aprox. 1,5 GB|
|Grande|4|7 GB|SO = tamaño del SO invitado<br/>Recurso local = 1018904 MB<br/>Aplicaciones = aprox. 1,5 GB|
|ExtraLarge|8|14 GB|SO = Tamaño del SO invitado<br/>Recurso local = 2 083 864 MB<br/>Aplicaciones = aprox. 1,5 GB|
|A5|2|14 GB|SO = tamaño del SO invitado<br/>Recurso local = 496664 MB<br/>Aplicaciones = aprox. 1,5 GB|
|A6|4|28 GB|SO = tamaño del SO invitado<br/>Recurso local = 1018904 MB<br/>Aplicaciones = aprox. 1,5 GB|
|A7|8|56 GB|SO = Tamaño del SO invitado<br/>Recurso local = 2 083 864 MB<br/>Aplicaciones = aprox. 1,5 GB
|A8|8|56 GB|SO = tamaño del SO invitado<br/>Recurso local = 1 856 172 MB<br/>Aplicaciones = aprox. 1,5 GB<blockquote> Nota: para obtener información y algunas consideraciones sobre el uso de este tamaño, vea <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Acerca de las instancias intensivas de proceso A8, A9, A10 y A11</a>.</blockquote>|
|A9|16|112 GB|SO = tamaño del SO invitado<br/>Recurso local = 1 856 172 MB<br/>Aplicaciones = aprox. 1,5 GB<blockquote> Nota: para obtener información y algunas consideraciones sobre el uso de este tamaño, vea <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Acerca de las instancias intensivas de proceso A8, A9, A10 y A11</a>.</blockquote>|
|A10|8|56 GB|SO = tamaño del SO invitado<br/>Recurso local = 1 856 172 MB<br/>Aplicaciones = aprox. 1,5 GB<blockquote> Nota: para obtener información y algunas consideraciones sobre el uso de este tamaño, vea <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Acerca de las instancias intensivas de proceso A8, A9, A10 y A11</a>.</blockquote>|
|A11|16|112 GB|SO = tamaño del SO invitado<br/>Recurso local = 1 856 172 MB<br/>Aplicaciones = aprox. 1,5 GB<blockquote> Nota: para obtener información y algunas consideraciones sobre el uso de este tamaño, vea <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Acerca de las instancias intensivas de proceso A8, A9, A10 y A11</a>.</blockquote>|
|Standard\_D1|1|3,5 GB|SO = tamaño del SO invitado<br/>Recurso local = 46104 MB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard\_D2|2|7 GB|SO = tamaño del SO invitado<br/>Recurso local = 97 304 MB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard\_D3|4|14 GB|SO = tamaño del SO invitado<br/>Recurso local = 199704 MB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard\_D4|8|28 GB|SO = tamaño del SO invitado<br/>Recurso local = 404504 MB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard\_D11|2|14 GB|SO = tamaño del SO invitado<br/>Recurso local = 97 304 MB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard\_D12|4|28 GB|SO = tamaño del SO invitado<br/>Recurso local = 199704 MB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard\_D13|8|56 GB|SO = tamaño del SO invitado<br/>Recurso local = 404504 MB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard\_D14|16|112 GB|SO = tamaño del SO invitado<br/>Recurso local = 814 104 MB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard\_D1\_v2|1|3,5 GB|SO = tamaño del SO invitado<br/>Recurso local = 46104 MB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard\_D2\_v2|2|7 GB|SO = tamaño del SO invitado<br/>Recurso local = 97304 MB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard\_D3\_v2|4|14 GB|SO = tamaño del SO invitado<br/>Recurso local = 199704 MB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard\_D4\_v2|8|28 GB|SO = tamaño del SO invitado<br/>Recurso local = 404504 MB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard\_D5\_v2|16|56 GB|SO = tamaño del SO invitado<br/>Recurso local = 814 104 MB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard\_D11\_v2|2|14 GB|SO = tamaño del SO invitado<br/>Recurso local = 97304 MB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard\_D12\_v2|4|28 GB|SO = tamaño del SO invitado<br/>Recurso local = 199704 MB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard\_D13\_v2|8|56 GB|SO = tamaño del SO invitado<br/>Recurso local = 404504 MB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard\_D14\_v2|16|112 GB|SO = tamaño del SO invitado<br/>Recurso local = 814 104 MB<br/>Aplicaciones = aprox. 1,5 GB|
## Configuración de tamaños para los Servicios en la nube

Puede especificar el tamaño de la máquina virtual de una instancia de rol como parte del modelo de servicio descrito por el archivo de definición de servicio. El tamaño del rol determina la cantidad de núcleos de CPU, la capacidad de memoria y el tamaño del sistema de archivos local que se asigna a una instancia en ejecución. Elija el tamaño del rol en función del requisito de recursos de la aplicación.

El siguiente es un ejemplo de cómo establecer que el tamaño del rol sea pequeño para una instancia de rol web:


    <WebRole name="WebRole1" vmsize="Small">
    …
    </WebRole>

Asegúrese de que el tamaño de recurso local especificado es menor o igual que el tamaño máximo de recurso local indicado en la tabla anterior.
## Pasos siguientes

[Configurar un servicio en la nube para Azure](https://msdn.microsoft.com/library/hh124108)

<!---HONumber=AcomDC_0128_2016-->