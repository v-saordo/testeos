<properties
   pageTitle="Conexión a un clúster del servicio Contenedor de Azure | Microsoft Azure"
   description="Conéctese a un clúster del servicio Contenedor de Azure mediante un túnel SSH."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contenedores, microservicios, Mesos, Azure"/>
   
<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>
   

# Conexión a un clúster del servicio Contenedor de Azure

Los clústeres de Mesos y Swarm implementados por el servicio Contenedor de Azure exponen puntos de conexión de REST, sin embargo, estos puntos de conexión no están abiertos al mundo exterior. Para administrar estos puntos de conexión, se debe crear un túnel SSH. Cuando se haya establecido un túnel SSH, puede ejecutar comandos contra los puntos de conexión y ver la interfaz de usuario del clúster a través de un explorador en su propio sistema. Este documento le guiará en la creación de un túnel SSH en Linux, OSX y Windows.

> Aunque puede crear una sesión de SSH con un sistema de administración del clúster, no se recomienda. Trabajar directamente en un sistema de administración tiene el riesgo de que la configuración se puede cambiar de forma involuntaria.

## Túnel SSH en Linux y OSX

Lo primero que hay que hacer es buscar el nombre DNS público de los patrones con equilibrio de carga. Para ello, expanda el grupo de recursos de forma que se muestre cada recurso. Busque y seleccione la dirección IP pública del patrón. Se abrirá una hoja que contiene información sobre la dirección IP pública, que incluirá el nombre DNS. Guarde este nombre para usarlo más adelante. <br />

![Conexión Putty](media/pubdns.png)

Ahora, abra un shell y ejecute el siguiente comando, donde:

**PORT** es el puerto del punto de conexión que desea exponer. Para Swarm es 2375 y para Mesos el puerto 80. **USERNAME** es el nombre de usuario proporcionado cuando se implementa el clúster. **DNSPREFIX** es el prefijo DNS que proporcionó cuando implementó el clúster. **REGION** es la región en la que está ubicado el grupo de recursos.

```
ssh -L PORT:localhost:PORT -N [USERNAME]@[DNSPREFIX]man.[REGION].cloudapp.azure.com -p 2200
```
### Túnel de Mesos

Para abrir un túnel a los puntos de conexión relacionados con Mesos, ejecute un comando parecido al siguiente.

```
ssh -L 80:localhost:80 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Ahora se puede acceder a los puntos de conexión relacionados con Mesos en:

- Mesos: `http://localhost/mesos`
- Marathon: `http://localhost/marathon`
- Chronos: `http://localhost/chronos`. 

De igual forma, se puede establecer comunicación con la API de REST de cada aplicación mediante este túnel: Marathon: `http://localhost/marathon/v2`. Para obtener más información sobre las diversas API disponibles, consulte la documentación de Mesosphere para la [API de Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) y la [API de Chronos](https://mesos.github.io/chronos/docs/api.html), y la documentación de Apache de la [API del programador de Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

### Túnel de Swarm

Para abrir un túnel al punto de conexión de Swarm, ejecute un comando parecido al siguiente.

```
ssh -L 2375:localhost:2375 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Ahora puede establecer la variable de entorno DOCKER\_HOST de la manera siguiente y seguir usando la CLI de Docker de la manera habitual.

```
export DOCKER_HOST=:2375
```

## Túnel SSH en Windows

Existen varias opciones para crear túneles SSH en Windows. En este documento encontrará información detallada sobre el uso de Putty.

Descargue Putty en el sistema Windows y ejecute la aplicación.

Escriba un nombre de host formado por el nombre de usuario de administrador de clústeres y el nombre DNS público del primer patrón en el clúster. El nombre de host se parecerá a este: `adminuser@PublicDNS`. Escriba 2200 para el puerto.

![Conexión Putty](media/putty1.png)

Seleccione `SSH` y `Authentication`, y agregue el archivo de clave privada para la autenticación.

![Conexión Putty](media/putty2.png)

Seleccione `Tunnels` y configure (`configure`) los siguientes puertos reenviados:
- **Puerto de origen:** su preferencia (80 para Mesos y 2375 para Swarm).
- **Destino:** localhost: 80 (para Mesos) o localhost:2375 (para Swarm).

En el siguiente ejemplo se configura para Mesos, pero tendría un aspecto similar para Docker Swarm.

> El puerto 80 no se debe estar usando al crear este túnel.

![Conexión Putty](media/putty3.png)

Cuando haya terminado, guarde la configuración de conexión y conecte la sesión de Putty. Cuando se conecte, la configuración del puerto se podrá ver en el registro de eventos de Putty.

![Conexión Putty](media/putty4.png)

Cuando se haya configurado el túnel para Mesos, puede tener acceso al punto de conexión relacionado en:

- Mesos: `http://localhost/mesos`
- Marathon: `http://localhost/marathon`
- Chronos: `http://localhost/chronos`. 

Cuando el túnel se haya configurado para Docker Swarm, se podrá acceder al clúster de Swarm mediante la CLI de Docker. Primero deberá configurar una variable de entorno de Windows denominada `DOCKER_HOST` con un valor de ` :2375`.

## Pasos siguientes
 
Implemente y administre contenedores con Mesos o Swarm.
 
- [Administración de contenedores con la API de REST](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0309_2016-->