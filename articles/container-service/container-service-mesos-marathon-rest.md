<properties
   pageTitle="Administración de contenedores de ACS con la API de REST | Microsoft Azure"
   description="Implemente contenedores en un clúster de Mesos del servicio Contenedor de Azure mediante la API de REST de Marathon."
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
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
   ms.author="nepeters"/>
   
# Administración de contenedores con la API de REST

Mesos proporciona un entorno para implementar y escalar la carga de trabajo agrupada al tiempo que reduce el hardware subyacente. Sobre Mesos, los marcos de trabajo administran la programación y la ejecución de cargas de trabajo de proceso. Aunque hay marcos de trabajo disponibles para muchas cargas de trabajo conocidas, en este documento se detalla cómo crear y escalar implementaciones de contenedores con Marathon.

Antes de trabajar con estos ejemplos, necesitará un clúster de Mesos configurado en ACS y tener conectividad remota a este clúster. Para más información sobre estos aspectos, consulte los siguientes artículos:

- [Deploying an Azure Container Service Cluster (Implementación de un clúster del servicio Contenedor de Azure)](./container-service-deployment.md) 
- [Connecting to an ACS Cluster (Conexión a un clúster del servicio Contenedor de Azure)](./container-service-connect.md)


Una vez conectado al clúster de ACS, se puede acceder a Mesos y las API de REST relacionadas mediante http://localhost:local-port. Los ejemplos de este documento suponen que está realizando la tunelización en el puerto 80. Por ejemplo, se puede alcanzar el punto de conexión de Marathon en `http://localhost/marathon/v2/`. Para más información sobre las diversas API, consulte la documentación de Mesosphere para la [API de Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) y la [API de Chronos](https://mesos.github.io/chronos/docs/api.html) y la documentación de Apache de la [API del programador de Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/)

## Recopilación de información de Mesos y Marathon

Antes de implementar contenedores en el clúster de Mesos, recopile información sobre dicho clúster, como los nombres y el estado actual de los agentes de Mesos. Para ello, consulte el punto de conexión `master/slaves` en un patrón de Mesos. Si todo va bien, verá una lista de agentes de Mesos y varias propiedades para cada uno.

```bash
curl http://localhost/master/slaves
```

Ahora, utilice el punto de conexión `/apps` de Marathon para buscar las implementaciones actuales de aplicaciones en el clúster de Mesos. Si se trata de un nuevo clúster, verá una matriz vacía para las aplicaciones.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## Implementación de un contenedor de Docker

Los contenedores de Docker se implementan a través de Marathon mediante un archivo json que describe la implementación deseada. En el ejemplo siguiente, se implementará el contenedor nginx y se enlazará el puerto 80 del agente de Mesos al puerto 80 del contenedor.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Para implementar un contenedor de Docker, cree su propio archivo json o utilice el ejemplo proporcionado aquí: [Demostración de ACS de Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json) y almacénelo en una ubicación accesible. A continuación, ejecute el siguiente comando, especificando el nombre del archivo json, para implementar el contenedor.

```
curl -X POST http://localhost/marathon/v2/groups -d @marathon.json -H "Content-type: application/json"
```

El resultado será similar al siguiente:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Ahora, si consulta Marathon sobre las aplicaciones, esta nueva aplicación se mostrará en el resultado.

```
curl localhost/marathon/v2/apps
```

## Escalado de un contenedor de Docker

La API de Marathon también puede utilizarse para escalar horizontalmente implementaciones de aplicaciones o reducirlas verticalmente. En el ejemplo anterior se implementó una instancia de una aplicación. Vamos a escalar esta instancia horizontalmente a tres instancias. Para ello, cree un archivo json con el siguiente texto json y almacénelo en una ubicación accesible.

```json
{ "instances": 3 }
```

Ejecute el comando siguiente para escalar la aplicación horizontalmente.

> Nota: El URI será http://localhost/marathon/v2/apps/ y luego se escalará el identificador de la aplicación. Si se utiliza el ejemplo de nginx aquí proporcionado, el URI sería http://localhost/v2/nginx.

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Por último, consulta el punto de conexión de Marathon para aplicaciones y observará que ahora hay tres del contenedor nginx.

```
curl localhost/marathon/v2/apps
```

## Interacción de la API de REST de Marathon con PowerShell

Esta misma acción se puede realizar mediante PowerShell en un sistema Windows. Este ejercicio rápido llevará a cabo tareas parecidas al último ejercicio, pero esta vez usando comandos de PowerShell.

Para recopilar información sobre el clúster de Mesos, como los nombres y los estados de los agentes, ejecute el siguiente comando.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Los contenedores de Docker se implementan a través de Marathon mediante un archivo json que describe la implementación deseada. En el ejemplo siguiente, se implementará el contenedor nginx y se enlazará el puerto 80 del agente de Mesos al puerto 80 del contenedor.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Cree su propio archivo json o utilice el ejemplo aquí proporcionado: [Demostración de ACS de Azure](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json), y almacénelo en una ubicación accesible. A continuación, ejecute el siguiente comando, especificando el nombre del archivo json, para implementar el contenedor.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

La API de Marathon también puede utilizarse para escalar horizontalmente implementaciones de aplicaciones o reducirlas verticalmente. En el ejemplo anterior se implementó una instancia de una aplicación. Vamos a escalar esta instancia horizontalmente a tres instancias. Para ello, cree un archivo json con el siguiente texto json y almacénelo en una ubicación accesible.

```json
{ "instances": 3 }
```

Ejecute el comando siguiente para escalar la aplicación horizontalmente.

> Nota: El URI será http://loclahost/marathon/v2/apps/ y luego se escalará el identificador de la aplicación. Si se utiliza el ejemplo de nginx aquí proporcionado, el URI sería http://localhost/v2/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

<!---HONumber=AcomDC_0224_2016-->