<properties
   pageTitle="Modelo de aplicación de Service Fabric | Microsoft Azure"
   description="Cómo modelar y describir las aplicaciones y servicios en Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor="mani-ramaswamy"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="12/30/2015"   
   ms.author="seanmck"/>

# Modelar una aplicación en Service Fabric

En este artículo se proporciona información general del modelo de aplicación de Service Fabric y también se describe cómo definir una aplicación y servicio a través de archivos de manifiesto y conseguir que se empaquete la aplicación y esté lista para su implementación.

## Entender el modelo de aplicación

Una aplicación es una colección de servicios constituyentes que realizan una determinada función o funciones. Un servicio realiza una función completa e independiente (puede iniciarse y ejecutarse independientemente de otros servicios) y se compone de código, configuración y datos. Para cada servicio, el código consta de los archivos binarios ejecutables, la configuración consta de una configuración del servicio que se puede cargar en tiempo de ejecución y los datos constan de datos estáticos arbitrarios que el servicio va a consumir. Cada componente de este modelo de aplicación jerárquico puede tener varias versiones y actualizarse independientemente.

![Modelo de aplicación de Service Fabric][appmodel-diagram]


Un tipo de aplicación es una categorización de una aplicación, que consta de un conjunto de tipos de servicio. Un tipo de servicio es una categorización de un servicio. La categorización puede tener una configuración diferente, pero cuya funcionalidad básica sigue siendo la misma. Las instancias de un servicio son las distintas variaciones de la configuración del servicio del mismo tipo de servicio.

Las clases (o "tipos") de aplicaciones y servicios se describen mediante archivos XML (manifiestos de aplicación y manifiestos de servicio) que son las plantillas en las que se puede crear una instancia de las aplicaciones desde el almacén de imágenes del clúster.

El código de las diversas instancias de aplicación se ejecutará como procesos independientes, incluso cuando lo hospede el mismo nodo de Service Fabric. Asimismo, el ciclo de vida de cada instancia de aplicación se puede administrar (es decir, actualizar) de forma independiente. En el siguiente diagrama se muestra cómo los tipos de aplicación constan de tipos de servicio, que a su vez constan de código, configuración y paquetes. Para simplificar el diagrama, solo se muestran los paquetes code/config/data para `ServiceType4`, aunque cada tipo de servicio podría incluir algunos o todos los tipos de paquetes.

![Tipos de aplicaciones de Service Fabric y tipos de servicio][cluster-imagestore-apptypes]

Dos archivos de manifiesto se usan para describir aplicaciones y servicios: el manifiesto de servicio y el manifiesto de aplicación. Estos aparecen detallados en las secciones subsiguientes.

Puede haber una o más instancias de un tipo de servicio activas en el clúster. Por ejemplo, las instancias de servicio con estado o réplicas logran una alta confiabilidad mediante la replicación del estado entre réplicas ubicadas en distintos nodos del clúster. Esta replicación esencialmente proporciona redundancia para que el servicio esté disponible, incluso si se produce un error en un nodo de un clúster. Un [servicio con particiones](service-fabric-concepts-partitioning.md) divide aún más su estado (y patrones de acceso a ese estado) entre los nodos del clúster.

En el siguiente diagrama se muestra la relación entre aplicaciones e instancias de servicio, particiones y réplicas.

![Particiones y réplicas dentro de un servicio][cluster-application-instances]


>[AZURE.TIP] Puede ver el diseño de las aplicaciones en un clúster mediante la herramienta Explorador de Service Fabric disponible en http://&lt;yourclusteraddress&gt;:19080/Explorer. Para más información, consulte [Visualización del clúster mediante el Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md).

## Describir un servicio

El manifiesto de servicio define mediante declaración el tipo de servicio y la versión. Especifica los metadatos de servicio, como el tipo de servicio, las propiedades de estado, las métricas de equilibrio de carga, archivos binarios del servicio y archivos de configuración. Dicho de otro modo, describe los paquetes de código, configuración y datos que componen un paquete de servicio para admitir uno o más tipos de servicio. Este es un ejemplo sencillo de manifiesto de servicio:

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
~~~

Los atributos **Versión** son cadenas no estructuradas y no analizadas por el sistema. Se usan para dotar a cada componente de varias versiones para posibilitar las actualizaciones.

**ServiceTypes** declara qué tipos de servicio admite **CodePackages** en el manifiesto. Cuando se crea una instancia de un servicio en uno de estos tipos de servicio, todos los paquetes de código declarados en este manifiesto se activan mediante la ejecución de sus puntos de entrada. Se espera que los procesos resultantes registren los tipos de servicio admitidos en tiempo de ejecución. Tenga en cuenta que los tipos de servicio se declaran en el nivel de manifiesto y no en el nivel de paquete de código. De modo que, cuando hay varios paquetes de código, se activan todos cada vez que el sistema busca cualquiera de los tipos de servicio declarados.

**SetupEntryPoint** es un punto de entrada con privilegios que se ejecuta con las mismas credenciales que Service Fabric (normalmente, la cuenta *LocalSystem*) antes que cualquier otro punto de entrada. El archivo ejecutable especificado por **EntryPoint** suele ser el host de servicio de ejecución prolongada. La presencia de un punto de entrada de configuración independiente evita tener que ejecutar el host de servicio con privilegios elevados durante largos períodos de tiempo. El archivo ejecutable especificado por **EntryPoint** se ejecuta después de salir **SetupEntryPoint** correctamente. El proceso resultante se supervisa y reinicia (comenzando de nuevo con **SetupEntryPoint**) si alguna vez finaliza o se bloquea.

**DataPackage** declara una carpeta nombrada por el atributo **Nombre** que contiene datos estáticos arbitrarios que va a consumir el proceso en tiempo de ejecución.

**ConfigPackage** declara una carpeta nombrada por el atributo **Nombre** que contiene un archivo *Settings.xml*. Este archivo contiene secciones de configuración del par clave-valor definida por el usuario que el proceso puede volver a leer en tiempo de ejecución. Durante una actualización, si solo ha cambiado la **versión** de **ConfigPackage**, no se reiniciará el proceso en ejecución. En su lugar, una devolución de llamada notifica el proceso que los ajustes de configuración han cambiado, por lo que pueden volver a cargarse de forma dinámica. Este es un ejemplo de archivo *Settings.xml*:

~~~
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSecion">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
~~~

> [AZURE.NOTE]Un manifiesto de servicio puede contener varios paquetes de código, configuración y datos, pudiendo tener cada uno de ellos varias versiones de forma independiente.

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## Describir una aplicación


Mediante declaración, el manifiesto de aplicación describe el tipo de aplicación y la versión. Especifica metadatos de composición de servicios como nombres estables, esquema de particiones, recuento de instancias/factor de replicación, directiva de seguridad y aislamiento, restricciones de ubicación, reemplazos de configuración y tipos de servicio constituyentes. También se describen los dominios de equilibrio de carga en los que se coloca la aplicación.

Por lo tanto, un manifiesto de aplicación describe elementos en el nivel de aplicación y hace referencia a uno o más manifiestos de servicio para componer un tipo de aplicación. Este es un ejemplo sencillo de manifiesto de aplicación:

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example application manifest</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
~~~

Al igual que los manifiestos de servicio, los atributos **Versión** son cadenas no estructuradas y no analizadas por el sistema. También se usan para dotar a cada componente de varias versiones para posibilitar las actualizaciones.

**ServiceManifestImport** contiene referencias a manifiestos de servicio que componen este tipo de aplicación. Los manifiestos de servicio importados determinan qué tipos de servicio son válidos dentro de este tipo de aplicación.

**DefaultServices** declara instancias de servicio que se crean automáticamente cada vez que se crea una instancia de una aplicación en este tipo de aplicación. Los servicios predeterminados son simplemente una comodidad y se comportan como servicios normales en todos los aspectos después de su creación. Se actualizan junto con cualquier otro servicio de la instancia de aplicación y también se pueden quitar.

> [AZURE.NOTE]Un manifiesto de aplicación puede contener varias importaciones y servicios predeterminados del manifiesto de servicio. Cada importación del manifiesto de servicio puede tener varias versiones independientemente.

Para información sobre cómo mantener diferentes parámetros de aplicación y servicio para entornos individuales, vea [Administración de los parámetros de la aplicación en varios entornos](service-fabric-manage-multiple-environment-app-configuration.md).

<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Application parameters
*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->

## Empaquetar una aplicación

### Diseño del paquete

El manifiesto de aplicación, los manifiestos de servicio y otros archivos de paquete necesarios deben organizarse en un diseño específico para la implementación en un clúster Service Fabric. Los manifiestos de ejemplo en este artículo deberían organizarse con la siguiente estructura de directorios:

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
~~~

Las carpetas reciben un nombre para coincidir con los atributos **Nombre** de cada elemento correspondiente. Por ejemplo, si el manifiesto de servicio contenía dos paquetes de código con los nombres **MiCódigoA** y **MiCódigoB**, dos carpetas con los mismos nombres incluirían los archivos binarios necesarios para cada paquete de código.

### Usar SetupEntrypoint

Los escenarios de uso de **SetupEntryPoint** habituales corresponden a cuando se necesita ejecutar un archivo ejecutable antes de iniciar el servicio o cuando necesita realizar una operación con privilegios elevados. Por ejemplo:

- configuración e inicialización de las variables de entorno que el ejecutable del servicio necesita. Esto no se limita tan solo a los archivos ejecutables escritos a través de los modelos de programación de Service Fabric. Por ejemplo, npm.exe necesita algunas variables de entorno configuradas para implementar una aplicación node.js.

- Configuración del control de acceso mediante la instalación de certificados de seguridad.

### Crear un paquete mediante Visual Studio

Si usa Visual Studio 2015 para crear su aplicación, puede utilizar el comando Package para crear automáticamente un paquete que coincida con el diseño descrito anteriormente.

Para crear un paquete, haga clic con el botón derecho en el proyecto de aplicación del Explorador de soluciones y elegir el comando Package, como se muestra a continuación:

![Empaquetado de una aplicación con Visual Studio][vs-package-command]

Una vez completado el empaquetado, busque la ubicación del paquete en la ventana **Salida**. Tenga en cuenta que el paso de empaquetado se produce automáticamente al implementar o depurar su aplicación en Visual Studio.

### Probar el paquete

Puede comprobar la estructura del paquete localmente a través de PowerShell mediante el comando **Test-ServiceFabricApplicationPackage**. Este comando comprobará si existen problemas de análisis de manifiestos y verificará todas las referencias. Tenga en cuenta que este comando solo comprueba la corrección estructural de los directorios y archivos del paquete. No comprobará ninguno de los contenidos del paquete de datos más allá de comprobar que todos los archivos necesarios están presentes.

~~~
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
~~~

Este error muestra que el archivo *MySetup.bat* al que se hace referencia en el manifiesto de servicio **SetupEntryPoint** no está en el paquete de código. Después de agregar el archivo que falta, se comprueba la aplicación:

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
~~~

Una vez que la aplicación se empaqueta correctamente y supera la verificación, está lista para la implementación.

## Pasos siguientes

[Implementar y quitar aplicaciones][10]

[Administración de los parámetros de la aplicación en varios entornos][11]

[RunAs: ejecución de una aplicación de Service Fabric con diferentes permisos de seguridad][12]

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png
[vs-package-command]: ./media/service-fabric-application-model/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md

<!---HONumber=AcomDC_0107_2016-->