<properties
   pageTitle="Especificación de los puntos de conexión de servicio de Service Fabric | Microsoft Azure"
   description="Cómo describir los recursos de punto de conexión en un manifiesto de servicio, incluida la configuración de puntos de conexión HTTPS"
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
   ms.date="01/26/2016"
   ms.author="sumukhs"/>

# Especificación de los recursos en un manifiesto de servicio

## Información general

El manifiesto de servicio permite que los recursos que utilizará el servicio sean declarados o modificados sin cambiar el código compilado. Service Fabric de Azure admite la configuración de recursos de puntos de conexión para el servicio. El acceso a los recursos especificados en el manifiesto de servicio puede controlarse a través de SecurityGroup en el manifiesto de aplicación. La declaración de recursos permite cambiar estos recursos durante la implementación, lo que significa que no es necesario que el servicio introduzca un nuevo mecanismo de configuración.

## Extremos

Cuando se define un recurso de punto de conexión en el manifiesto de servicio, Service Fabric asigna puertos desde el intervalo de puertos de aplicación reservados si no se especifica un puerto concreto (por ejemplo, mire el punto de conexión *ServiceEndpoint1* a continuación). Además, los servicios también pueden solicitar un puerto específico en un recurso. Es posible asignar números de puerto diferentes a réplicas de servicio que se ejecutan en nodos de clúster, mientras que las réplicas del mismo servicio que se ejecuta en el mismo nodo comparten el mismo puerto. Estos puertos pueden ser utilizados por las réplicas de servicio para varios propósitos, como la replicación, la escucha de solicitudes de cliente, etc.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Consulte [Configuración de Reliable Services con estado](service-fabric-reliable-services-configuration.md) para obtener más información acerca de cómo hacer referencia a los puntos de conexión del archivo de configuración del paquete de configuración (settings.xml).

## Ejemplo: Especificación de un punto de conexión HTTP para el servicio

El siguiente manifiesto de servicio define un recurso de punto de conexión TCP y dos recursos de punto de conexión HTTP en el elemento &lt;Recursos&gt;.

Service Fabric hace ACL automáticamente en los puntos de conexión HTTP.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## Ejemplo: Especificación de un punto de conexión HTTPS para el servicio

El protocolo HTTPS ofrece autenticación de servidor y también se usa para cifrar la comunicación entre cliente y servidor. Para habilitar esta opción en su servicio Service Fabric, al definir el servicio, especifique el protocolo en la sección *Recursos -> Puntos de conexión -> Punto de conexión* del manifiesto de servicio, como se mostró anteriormente para el punto de conexión *ServiceEndpoint3*.

>[AZURE.NOTE] No se puede cambiar el protocolo de un servicio durante la actualización de la aplicación, porque esto supondría un cambio importante.


Este es un ejemplo ApplicationManifest que debe establecer para HTTPS. (Necesitará proporcionar la huella digital para el certificado). EndpointRef es una referencia a EndpointResource en ServiceManifest, para la que establece el protocolo HTTPS. Puede agregar más de un certificado Endpointcertificates.

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

<!---HONumber=AcomDC_0211_2016-->