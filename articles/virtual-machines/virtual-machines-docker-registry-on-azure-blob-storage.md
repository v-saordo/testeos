<properties 
  pageTitle="Implementación de su propio Docker Registry privado en Azure | Microsoft Azure"
  description="Describe cómo puede utilizar Docker Registry para hospedar las imágenes de contenedor en el servicio de almacenamiento de blobs de Azure."
  services="virtual-machines"
  documentationCenter="virtual-machines"
  authors="ahmetalpbalkan"
  editor="squillace"
  manager="" 
  tags="azure-service-management,azure-resource-manager" />

<tags
  ms.service="virtual-machines"
  ms.devlang="multiple"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure-services"
  ms.date="02/01/2016" 
  ms.author="ahmetb" />

# Implementación de su propio Docker Registry privado en Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



Este documento describe un Docker Registry privado y muestra cómo se puede implementar una imagen de contenedor de Docker Registry 2.0 en un Docker Registry privado en Microsoft Azure con el almacenamiento de blobs de Azure.

En este documento se considera que:

1. Sabe cómo usar Docker y tiene imágenes de Docker para almacenar. (¿No es así? [Más información acerca de Docker](https://www.docker.com).
2. Dispone de un servidor que tiene instalado el motor de Docker. (¿No es así? [Hágalo rápidamente en Azure](https://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/).)


## ¿Qué es un Docker Registry privado?

Para enviar sus aplicaciones que están en el contenedor a la nube, construya una imagen de contenedor de Docker y almacénelo en algún lugar para poder usarlo usted y otros usuarios.

Aunque es fácil crear una imagen de contenedor y enviarla a la nube, almacenar la imagen generada de forma confiable es un reto. Por ese motivo, Docker ofrece un servicio centralizado llamado [Docker Hub][docker-hub] para almacenar las imágenes en la nube de contenedor y que permite crear contenedores en cualquier momento con esas imágenes.

Aunque [Docker Hub][docker-hub] es un servicio de pago para almacenar las imágenes del contenedor de aplicaciones privadas, Docker respeta las necesidades de los desarrolladores y proporciona un conjunto de herramientas de código abierto para almacenar las imágenes en su propio Docker Registry privado detrás de un firewall o de forma local, sin llegar a la red Internet pública. Dado que el almacenamiento de blobs de Azure es fácil de proteger, puede utilizarlo rápidamente para crear y usar un Docker Registry privado de Azure controlado por usted.

## ¿Por qué se debe hospedar Docker Registry en Azure?

Hospedar la instancia de Docker Registry en Microsoft Azure y almacenar sus imágenes en el almacenamiento de blobs de Azure puede tener varias ventajas:

**Seguridad:** las imágenes de Docker no abandonan los centros de datos de Azure, por lo que no cruzan la Internet pública como si ocurriría si se usará Docker Hub.
  
**Rendimiento:** las imágenes de Docker se almacenan dentro del mismo centro de datos o región que sus aplicaciones. Esto significa que las imágenes se van a extraer con mayor rapidez y confiabilidad en comparación con Docker Hub.

**Confiabilidad:** mediante el uso de almacenamiento de blobs de Microsoft Azure, puede utilizar muchas propiedades de almacenamiento, como alta disponibilidad, redundancia, almacenamiento premium (SSD), etc.

## Configuración de Docker Registry para utilizar el almacenamiento de blobs de Azure

(Se recomienda leer la [documentación de Docker Registry 2.0 ][registry-docs] antes de continuar).

También puede [configurar][registry-config] Docker Registry de dos maneras diferentes. Puede:

1. Use un archivo `config.yml`. En este caso necesitará crear una imagen de Docker independiente encima de la imagen de `registry`.
2. Reemplace el archivo de configuración predeterminado mediante variables de entorno: todo se hará sin crear ni mantener una imagen de Docker independiente.

Por simplicidad, este tema sigue la opción 2, el uso de variables de entorno.

Para ejecutar una instancia de Docker Registry que: * Utiliza la cuenta de Almacenamiento de Azure para almacenar las imágenes * Escucha en el puerto 5000 de la máquina virtual * No tiene ninguna autenticación configurada (no recomendado, consulte la nota siguiente)

Deberá ejecutar el siguiente comando de Docker en el terminal bash (reemplace `<storage-account>` y `<storage-key>` por sus credenciales):

```sh
$ docker run -d -p 5000:5000 \
     -e REGISTRY_STORAGE=azure \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTNAME="<storage-account>" \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTKEY="<storage-key>" \
     -e REGISTRY_STORAGE_AZURE_CONTAINER="registry" \
     --name=registry \
     registry:2
```

Una vez que el comando termina, para ver el contenedor que hospeda la instancia de Docker Registry privado ejecute el comando `docker ps` en el host:

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                    NAMES
3698ddfebc6f        registry:2          "registry cmd/regist   2 seconds ago       Up 1 seconds        0.0.0.0:5000->5000/tcp   registry
```

> [AZURE.IMPORTANT] La configuración de seguridad para Docker Registry no se cubre en este documento y cualquier usuario sin autenticación tendrá acceso al registro de forma predeterminada si abre el puerto para el puerto del registro en el extremo de la máquina virtual o el equilibrador de carga si utiliza el comando de implementación anterior.
>
> Lea la documentación [Configuración de Docker Registry][registry-config] para aprender a proteger la instancia del registro y sus imágenes.

## Pasos siguientes

Una vez configurado el registro, es hora de usarlo más. Comience con [registry-docs] de Docker.

[docker-hub]: https://hub.docker.com/
[registry]: https://github.com/docker/distribution
[registry-docs]: http://docs.docker.com/registry/
[registry-config]: http://docs.docker.com/registry/configuration/
 

<!---HONumber=AcomDC_0204_2016-->