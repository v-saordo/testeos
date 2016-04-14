<properties
   pageTitle="Administración de varios entornos en Service Fabric | Microsoft Azure"
   description="Las aplicaciones de Service Fabric se pueden ejecutar en clústeres cuyo tamaño oscila entre una y miles de máquinas. En algunos casos, deseará configurar su aplicación de forma diferente para cada uno de los entornos. Este artículo explica cómo definir distintos parámetros de aplicación por entorno."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/25/2015"
   ms.author="seanmck"/>

# Administración de los parámetros de la aplicación en varios entornos

Puede crear clústeres de Service Fabric de Azure con cualquier número de máquinas, desde una sola máquina hasta miles de ellas. Aunque los archivos binarios de una aplicación pueden ejecutarse sin modificación alguna en este amplio espectro de entornos, con frecuencia deseará configurar la aplicación de forma diferente en función del número de equipos en que vaya a implementarla.

A modo de ejemplo simple, considere `InstanceCount` en un servicio sin estado. Cuando se ejecutan las aplicaciones de Azure, normalmente deseará establecer este parámetro en el valor especial de "-1". Esto garantiza que el servicio se ejecuta en cada nodo del clúster. Sin embargo, esta configuración no es adecuada para un clúster de una máquina, ya que no puede haber varios procesos que escuchen el mismo punto de conexión en una máquina individual. En su lugar, normalmente `InstanceCount` se establecerá en "1".

## Especificación de parámetros concretos del entorno

La solución a este problema de configuración es un conjunto de servicios predeterminados con parámetros y los archivos de parámetros de la aplicación que rellene los valores de dichos parámetros para un entorno determinado.

### Servicios predeterminados

Las aplicaciones de Service Fabric constan de una colección de instancias de servicio. Aunque se puede crear una aplicación vacía y, a continuación, crear todas las instancias del servicio de forma dinámica, la mayoría de las aplicaciones tienen un conjunto de servicios principales que se deben crear siempre que se crea una instancia de la aplicación. Estos se conocen como "servicios predeterminados". Se especifican en el manifiesto de aplicación con marcadores de posición para la configuración de cada entorno entre corchetes:

    <DefaultServices>
        <Service Name="Stateful1">
            <StatefulService
                ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

                <UniformInt64Partition
                    PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808"
                    HighKey="9223372036854775807"
                />
        </StatefulService>
    </Service>
  </DefaultServices>

Todos los parámetros con nombre deben definirse dentro del elemento Parameters del manifiesto de aplicación:

    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>

El atributo DefaultValue especifica el valor que se usará en ausencia de un parámetro más específico para un entorno determinado.

>[AZURE.NOTE] No todos los parámetros de instancias de servicio son adecuados para la configuración de cada entorno. En el ejemplo anterior, los valores LowKey y HighKey del esquema de partición del servicio se definen explícitamente para todas las instancias del servicio, ya que el intervalo de partición es una función del dominio de datos, no del entorno.


### Opciones de configuración del servicio de cada entorno

El [modelo de aplicación de Service Fabric](service-fabric-application-model.md) permite a los servicios incluir paquetes de configuración que contienen pares clave-valor personalizados que se pueden leer en tiempo de ejecución. Los valores de estas opciones también se pueden diferenciar por entorno, para lo que se debe especificar `ConfigOverride` en el manifiesto de aplicación.

Suponga que tiene la siguiente opción en Config\\Settings.xml para el servicio `Stateful1`:


    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>

Para reemplazar este valor para un par entorno/aplicación específico, cree `ConfigOverride` al importar el manifiesto de servicio en el manifiesto de aplicación.

    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>

Este parámetro se puede configurar después por entorno, tal como se mostró anteriormente. Puede hacerlo declarándolo en la sección de parámetros del manifiesto de aplicación y especificar valores específicos del entorno en el archivo de parámetros de la aplicación.

>[AZURE.NOTE] En el caso de las opciones de configuración, hay tres lugares donde se puede establecer el valor de una clave: el paquete de configuración del servicio, el manifiesto de aplicación y el archivo de parámetros de la aplicación. Service Fabric siempre elegirá entre el archivo de parámetros de la aplicación en primer lugar (si se especifica), luego el manifiesto de aplicación y, finalmente, el paquete de configuración.


### Archivos de parámetros de la aplicación

El proyecto de aplicación de Service Fabric puede incluir uno o más archivos de parámetro de la aplicación. Cada uno de ellos define valores concretos para los parámetros que se definen en el manifiesto de aplicación:

    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>

De forma predeterminada, una aplicación nueva incluye dos archivos de parámetros de aplicación, denominados Local.xml y Cloud.xml:

![Archivos de parámetros de la aplicación en el Explorador de soluciones][app-parameters-solution-explorer]

Para crear un nuevo archivo de parámetros, basta con copiar y pegar uno existente y asignarle un nombre nuevo.

## Identificación de parámetros específicos del entorno durante la implementación

En el momento de la implementación, es preciso elegir el archivo de parámetros apropiado que se aplicará en la aplicación. Esto se puede realizar desde el cuadro de diálogo Publicar de Visual Studio o a través de PowerShell.

### Implementación desde Visual Studio

Al publicar una aplicación en Visual Studio, puede elegir en la lista de archivos de parámetros disponibles.

![Elección de un archivo de parámetros en el cuadro de diálogo Publicar][publishdialog]

### Implementación a partir de PowerShell

El script de PowerShell `DeployCreate-FabricApplication.ps1` acepta un archivo de parámetros como parámetro.

    ./DeployCreate-FabricApplication -ApplicationPackagePath <app_package_path> -ApplicationDefinitionFilePath <app_instance_definition_path>

## Pasos siguientes

Para más información acerca de algunos de los conceptos principales descritos en este tema, consulte el artículo [Introducción técnica a Service Fabric](service-fabric-technical-overview.md). Para más información sobre otras funcionalidades de administración de aplicaciones disponibles en Visual Studio, consulte [Uso de Visual Studio para simplificar la escritura y la administración de las aplicaciones de Service Fabric](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]: ./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png

<!---HONumber=AcomDC_0218_2016-->