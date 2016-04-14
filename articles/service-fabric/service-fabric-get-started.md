<properties
   pageTitle="Configuración del entorno de desarrollo | Microsoft Azure"
   description="Instale las herramientas, el SDK y el motor en tiempo de ejecución y cree un clúster de desarrollo local. Después de completar esta instalación, estará listo para crear aplicaciones."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/09/2016"
   ms.author="seanmck"/>

# Preparación del entorno de desarrollo
 Para compilar y ejecutar [aplicaciones de Service Fabric de Azure][1] en la máquina de desarrollo, debe instalar el motor en tiempo de ejecución, el SDK y las herramientas. También es preciso que habilite la ejecución de los scripts de Windows PowerShell que se incluyen en el SDK.

## Requisitos previos
### Versiones de sistemas operativos compatibles
Se admiten las siguientes versiones de sistemas operativos:

- Windows 8/Windows 8.1
- Windows Server 2012 R2
- Windows 10

### Visual Studio 2015

Las herramientas de Service Fabric dependen de Visual Studio 2015, que encontrará en el [sitio web de Visual Studio][2].

> [AZURE.NOTE] Si no está ejecutando una de las versiones de sistema operativo compatibles o prefiere no instalar Visual Studio 2015 en su equipo, puede configurar una máquina virtual de Azure con Windows Server 2012 R2 y Visual Studio 2015 preinstalados. Puede hacerlo mediante una imagen de la galería de máquinas virtuales de Azure.

## Instalar el motor en tiempo de ejecución, el SDK y las herramientas

La instalación de los componentes de Service Fabric se realiza mediante el Instalador de plataforma web. Siga estas instrucciones para instalar:

1. [Descargue el SDK][3] con el Instalador de plataforma web.

2. Haga clic en **Instalar** para comenzar el proceso de instalación.

3. Revise y acepte el CLUF.

La instalación continuará automáticamente.

## Habilitar la ejecución del script de PowerShell

Service Fabric usa scripts de Windows PowerShell para crear un clúster de desarrollo local e implementar aplicaciones desde Visual Studio. De forma predeterminada, Windows bloqueará la ejecución de estos scripts. Para habilitarlos, debe modificar la directiva de ejecución de PowerShell. Abra PowerShell como administrador y escriba el siguiente comando:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## Pasos siguientes
Ahora que está configurado su entorno de desarrollo, puede iniciar la creación y ejecución de aplicaciones.

- [Creación de la primera aplicación de Service Fabric en Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
- [Introducción a la implementación y actualización de aplicaciones en un clúster local](service-fabric-get-started-with-a-local-cluster.md)
- [Más información sobre los modelos de programación: Reliable Services y Reliable Actors](service-fabric-choose-framework.md)
- [Consulta de los ejemplos de código de Service Fabric en GitHub](https://aka.ms/servicefabricsamples)
- [Visualización del clúster mediante el Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md)
- [Siga la ruta de aprendizaje de Service Fabric para obtener una amplia introducción a la plataforma](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "Página de campaña de Service Fabric"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[3]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "Vínculo de WebPI"

<!---HONumber=AcomDC_0218_2016-->