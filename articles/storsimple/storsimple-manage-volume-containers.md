<properties 
   pageTitle="Administración de contenedores de volúmenes de StorSimple| Microsoft Azure"
   description="Explica cómo se puede usar la página de contenedores de volúmenes del Servicio de Administrador de StorSimple para agregar, modificar o eliminar contenedores de volúmenes."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/08/2016"
   ms.author="v-sharos" />

# Usar el servicio de Administrador de StorSimple para administrar contenedores de volúmenes de StorSimple

## Información general

Este tutorial explica cómo usar el Servicio Administrador de StorSimple para crear y administrar contenedores de volúmenes StorSimple.

En un dispositivo Microsoft Azure StorSimple, los contenedores de volúmenes contienen uno o varios volúmenes que comparten la configuración de cuentas de almacenamiento, cifrado y consumo de ancho de banda. Un dispositivo puede tener varios contenedores de volúmenes para todos sus volúmenes.

Los contenedores de volúmenes tienen los siguientes atributos:

- **Volúmenes**: Los volúmenes de StorSimple en niveles o anclados localmente que se encuentran dentro del contenedor de volúmenes. Un contenedor de volúmenes puede contener hasta 256 volúmenes de StorSimple.

- **Cifrado**: una clave de cifrado que se puede definir para cada contenedor de volúmenes. Esta clave se utiliza para cifrar los datos que se envían desde un dispositivo de StorSimple a la nube. Se utiliza una clave de grado militar AES de 256 bits con la clave especificada por el usuario. Para proteger los datos, se recomienda habilitar siempre el cifrado de almacenamiento en la nube.

- **Cuenta de almacenamiento**: la cuenta de almacenamiento vinculada a su proveedor de servicios de almacenamiento en la nube. Todos los volúmenes que se encuentran en un contenedor de volúmenes comparten esta cuenta de almacenamiento. Al crear el contenedor de volúmenes puede elegir una cuenta de almacenamiento de una lista existente, o bien crear una cuenta nueva y, a continuación, especificar las credenciales de acceso de dicha cuenta.

- **Ancho de banda en la nube**: el ancho de banda que consume el dispositivo cuando sus datos se envían a la nube. Puede aplicar un control del ancho de banda especificando un valor entre 1 y 1000 Mbps al definir este contenedor. Si desea que el dispositivo consuma todo el ancho de banda disponible, elija la opción Sin límites en este campo. También puede crear y aplicar una plantilla de ancho de banda para asignar el ancho de banda según la programación.

![Página Contenedores de volúmenes](./media/storsimple-manage-volume-containers/HCS_VolumeContainersPage.png)

Los siguientes procedimientos explican cómo utilizar la página **Contenedores de volúmenes** de StorSimple para realizar las siguientes operaciones comunes:

- Agregar un contenedor de volúmenes 
- Modificar un contenedor de volúmenes 
- Eliminar un contenedor de volúmenes 

## Agregar un contenedor de volúmenes

Realice los siguientes pasos para agregar un contenedor de volúmenes.

[AZURE.INCLUDE [storsimple-add-volume-container](../../includes/storsimple-add-volume-container.md)]


## Modificar un contenedor de volúmenes

Realice los siguientes pasos para modificar una contenedor de volúmenes.

[AZURE.INCLUDE [storsimple-modify-volume-container](../../includes/storsimple-modify-volume-container.md)]


## Eliminar un contenedor de volúmenes

Los contenedores de volúmenes contienen volúmenes. Solo se puede eliminar si primero se eliminan todos los volúmenes que contiene. Realice los siguientes pasos para eliminar una contenedor de volúmenes.

[AZURE.INCLUDE [storsimple-delete-volume-container](../../includes/storsimple-delete-volume-container.md)]

## Pasos siguientes

- Obtenga más información sobre la [administración de volúmenes de StorSimple](storsimple-manage-volumes.md). 
- Obtenga más información sobre el [uso del servicio StorSimple Manager para administrar su dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0114_2016-->