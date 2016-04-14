<properties
   pageTitle="Descripción de las directivas de seguridad RunAs de la aplicación Service Fabric | Microsoft Azure"
   description="Información general sobre cómo ejecutar una aplicación de Service Fabric en cuentas de seguridad locales y del sistema, incluido el punto SetupEntry en el que una aplicación necesita realizar alguna acción con privilegios antes de iniciarse"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="bscholl"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/12/2016"
   ms.author="mfussell"/>

# RunAs: ejecutar de una aplicación de Service Fabric con diferentes permisos de seguridad
Azure Service Fabric permite proteger aplicaciones que se ejecutan en el clúster en distintas cuentas de usuario, lo que se conoce como **RunAs**. También protege los recursos que usan las aplicaciones con la cuenta de usuario, como archivos, directorios y certificados.

De forma predeterminada, las aplicaciones de Service Fabric se ejecutan en la misma cuenta en que se ejecuta el proceso Fabric.exe. Service Fabric también permite ejecutar aplicaciones en una cuenta de usuario local o una cuenta de sistema local especificada en el manifiesto de la aplicación. Los tipos de cuenta de sistema local compatibles con RunAs son **LocalUser**, **NetworkService**, **LocalService** y **LocalSystem**.

> [AZURE.NOTE] Las cuentas de dominio son compatibles con las implementaciones de Windows Server en las que Azure Active Directory está disponible.

Se pueden definir y crear grupos de usuarios, con el fin de que se puedan agregar uno o varios usuarios a cada grupo para poder administrarlos de forma conjunta. Esto es especialmente útil cuando hay varios usuarios para distintos puntos de entrada de servicio y es preciso que tengan ciertos privilegios comunes disponibles en el nivel de grupo.

## Configuración de la directiva RunAs en SetupEntryPoint

Como se describe en [Modelar una aplicación en Service Fabric](service-fabric-application-model.md), **SetupEntryPoint** es un punto de entrada con privilegios que se ejecuta con las mismas credenciales que Service Fabric (normalmente, la cuenta *NetworkService*) antes que cualquier otro punto de entrada. El ejecutable que especifica **EntryPoint** suele ser el host de servicios de ejecución prolongada, por lo que tener un punto de entrada de configuración independiente evita tener que ejecutar el host de servicios con privilegios elevados durante largos períodos. El archivo ejecutable especificado por **EntryPoint** se ejecuta después de salir **SetupEntryPoint** correctamente. El proceso resultante se supervisa y reinicia (comenzando de nuevo con **SetupEntryPoint**) si alguna vez finaliza o se bloquea.

A continuación aparece un ejemplo de manifiesto de servicio básico que muestra SetupEntryPoint y el EntryPoint principal del servicio.

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
~~~

### Configurar la directiva de RunAs mediante una cuenta local

Tras configurar el servicio para que tenga un punto de entrada de configuración, puede cambiar los permisos de seguridad que ejecuta en el manifiesto de aplicación. En el ejemplo siguiente, se muestra cómo configurar el servicio para que se ejecute con privilegios de cuenta de administrador de usuarios.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
~~~

En primer lugar, cree una sección **Principals** con un nombre de usuario, como SetupAdminUser. Esto indica que el usuario es miembro del grupo de administradores del sistema.

Después, debajo de la sección **ServiceManifestImport**, configure una directiva para aplicar esta entidad de seguridad a **SetupEntryPoint**. Esto indica a Service Fabric que, cuando se ejecute el archivo **MySetup.bat**, debería ser RunAs con privilegios de administrador. Dado que *no* ha aplicado una directiva al punto de entrada principal, el código de **MyServiceHost.exe** se ejecutará en la cuenta **NetworkService** del sistema. Esta es la cuenta predeterminada como la que se ejecutan todos los puntos de entrada de servicio.

Ahora agreguemos el archivo MySetup.bat al proyecto de Visual Studio para probar los privilegios de administrador. En Visual Studio, haga clic con el botón derecho en el proyecto de servicio y agregue un nuevo archivo, MySetup.bat.

Después asegúrese de que el archivo MySetup.bat está incluido en el paquete de servicio. De forma predeterminada, no lo está. Seleccione el archivo, haga clic con el botón derecho para ver el menú contextual y elija **Propiedades**. En el cuadro de diálogo de propiedades, asegúrese de que **Copiar en el directorio de salida** está establecido en **Copiar si es posterior**. Esto se muestra en la captura de pantalla siguiente.

![CopyToOutput de Visual Studio para el archivo por lotes de SetupEntryPoint][image1]

Ahora abra el archivo MySetup.bat y agregue los siguientes comandos:

~~~
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
~~~

Seguidamente, compile e implemente la solución en un clúster de desarrollo local. Una vez iniciado el servicio, como se muestra en el explorador de Service Fabric, puede ver que MySetup.bat se ha ejecutado correctamente de dos maneras. Abra un símbolo del sistema de PowerShell y escriba:

~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
~~~

Luego anote el nombre del nodo en que el servicio se ha implementado e iniciado en el explorador de Service Fabric, por ejemplo, Node 1. Después navegue hasta la carpeta de trabajo de la instancia de aplicación para buscar el archivo out.txt que muestra el valor de **TestVariable**. Por ejemplo, si se implementó en Node 2, puede ir a esta ruta de acceso para ver el valor de **MyApplicationType**:

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

###  Configurar la directiva de RunAs mediante cuentas de sistema locales
A menudo es preferible ejecutar el script de inicio mediante una cuenta de sistema local en lugar de usar una cuenta de administrador, como se mostró anteriormente. Normalmente, el hecho de ejecutar la directiva RunAs como administrador no funciona bien, ya que las máquinas tienen el Control de acceso de usuario (UAC) habilitado de forma predeterminada. En estos casos, **la recomendación es ejecutar el SetupEntryPoint como LocalSystem en lugar de un usuario local agregado al grupo de administradores**. En el ejemplo siguiente, se muestra cómo establecer SetupEntryPoint para que se ejecute como LocalSystem.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
~~~

##  Inicio de comandos de PowerShell desde SetupEntryPoint
Para ejecutar PowerShell desde el punto **SetupEntryPoint**, puede ejecutar **PowerShell.exe** en un archivo por lotes que apunte al archivo de PowerShell. En primer lugar, agregue un archivo de PowerShell al proyecto de servicio, por ejemplo, **MySetup.ps1**. No olvide configurar la propiedad *Copiar si es posterior* para que el archivo se incluya también en el paquete de servicio. El ejemplo siguiente muestra un archivo por lotes de ejemplo que inicia un archivo de PowerShell denominado MySetup.ps1, que establece una variable de entorno del sistema denominada **TestVariable**.


MySetup.bat para iniciar el archivo de PowerShell.

~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
~~~

En el archivo de PowerShell, agregue el siguiente procedimiento para establecer una variable de entorno del sistema.

~~~
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
~~~

**Nota:** De forma predeterminada, cuando se ejecuta el archivo por lotes, la búsqueda de archivos se realiza en la carpeta de aplicación denominada **work**. En este caso, cuando se ejecuta MySetup.bat queremos que encuentre MySetup.ps1 en la misma carpeta, que es la carpeta **paquete de código** de la aplicación. Para cambiar esta carpeta, configure la carpeta de trabajo, como se muestra a continuación.
    
~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint> 
~~~

## Uso de la directiva de redireccionamiento de consola para la depuración local de los puntos de entrada
Ocasionalmente, resulta útil ver el resultado de la consola tras ejecutar un script con fines de depuración. Para ello, puede establecer una directiva de redireccionamiento de consola que escriba la salida en un archivo. La salida del archivo se escribe en la carpeta de aplicación denominada **log** en el nodo donde se ha implementado y ejecutado la aplicación (vea dónde se encuentra esta ubicación en el ejemplo anterior).

**Nota:** No use nunca la directiva de redireccionamiento de consola en una aplicación implementada en producción, ya que esto puede influir en la capacidad de conmutación por error de la aplicación. **SOLO** debe usarla para propósitos de depuración y desarrollo local.

En el ejemplo siguiente, se muestra cómo establecer el redireccionamiento de la consola con un valor de FileRetentionCount.

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint> 
~~~

Si cambia el archivo MySetup.ps1 para escribir un comando **Echo**, este se escribirá en el archivo de salida con fines de depuración.

~~~
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to" 
~~~

**Después de haber depurado la secuencia de comandos, quite inmediatamente esta directiva de redireccionamiento de consola.**

## Aplicación de RunAsPolicy a servicios
En los pasos anteriores se explicaba cómo aplicar la directiva RunAs a SetupEntryPoint. Ahora se explicará más detalladamente cómo crear diferentes entidades de seguridad que se pueden aplicar como directivas de servicio.

### Creación de grupos de usuarios locales
Se pueden definir y crear grupos de usuarios que permitan agregar uno o varios usuarios a un grupo. Esto es especialmente útil si hay varios usuarios para distintos puntos de entrada de servicio y deben tener ciertos privilegios comunes disponibles en el nivel de grupo. En el ejemplo siguiente, se muestra un grupo local denominado **LocalAdminGroup** con privilegios de administrador. Dos usuarios, Customer1 y Customer2, se convierten en miembros de este grupo local.

~~~
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
~~~

### Creación de usuarios locales
Puede crear un usuario local para proteger un servicio dentro de la aplicación. Si se especifica un tipo de cuenta **LocalUser** en la sección de entidades de seguridad del manifiesto de aplicación, Service Fabric crea cuentas de usuario locales en las máquinas donde se implementa la aplicación. De forma predeterminada, los nombres de dichas cuentas no coinciden con los que se especifican en el manifiesto de aplicación (por ejemplo, Customer3 en el ejemplo siguiente). En su lugar, se generan dinámicamente y tienen contraseñas aleatorias.

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~

<!-- If an application requires that the user account and password be same on all machines (for example, to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true. The cluster manifest must also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

## Asignación de directivas a los paquetes de código de servicio
En la sección **RunAsPolicy** de **ServiceManifestImport**, se especifica la cuenta de la sección de entidades de seguridad que debe usarse para ejecutar un paquete de código. También se asocian los paquetes de código del manifiesto de servicio con las cuentas de usuario de la sección de entidades de seguridad. Puede especificarlo para los puntos de entrada de configuración o principales, o bien puede especificar Todos para que se aplique a ambos. En el ejemplo siguiente se muestra la aplicación de diferentes directivas:

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

Si no se especifica **EntryPointType**, el valor predeterminado se establece en EntryPointType =”Main”. El uso de **SetupEntryPoint** es especialmente útil si lo que se desea es ejecutar determinadas operaciones de configuración con privilegios elevados en una cuenta de sistema. El código de servicio real puede ejecutarse en una cuenta con menos privilegios.

### Aplicación de una directiva predeterminada a todos los paquetes de código de servicio
La sección **DefaultRunAsPolicy** se usa para especificar una cuenta de usuario predeterminada para todos los paquetes de código que no tienen ningún **RunAsPolicy** específico definido. Si la mayoría de los paquetes de código especificados en los manifiestos de servicio que usa una aplicación debe ejecutarse en el mismo usuario de RunAs, la aplicación solo puede definir una directiva RunAs predeterminada con dicha cuenta de usuario, en lugar de especificar una directiva **RunAsPolicy** para cada paquete de código. En el ejemplo siguiente, se especifica que si un paquete de código no tiene una directiva **RunAsPolicy** especificada, tendrá que ejecutarse en la directiva **MyDefaultAccount** especificada en la sección de entidades de seguridad.

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~

## Asignación de SecurityAccessPolicy a los puntos de conexión HTTP y HTTPS
Si se aplica una directiva RunAs a un servicio y el manifiesto de servicio declara que hay recursos de puntos de conexión con el protocolo HTTP, es preciso especificar una directiva **SecurityAccessPolicy** para asegurarse de que los puertos asignados a dichos puntos de conexión aparezcan correctamente en la lista de control de acceso de la cuenta de usuario de RunAs en la que se ejecuta el servicio. En caso contrario, **http.sys** no tendrá acceso al servicio y aparecerán errores en las llamadas del cliente. En el ejemplo siguiente, la cuenta Customer3 se aplica a un punto de conexión denominado **ServiceEndpointName** y se le asignan derechos de acceso completos.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

En el caso del punto de conexión HTTPS, también es preciso indicar el nombre del certificado que se va a devolver al cliente. Para ello, puede utilizar **EndpointBindingPolicy**, con el certificado definido en una sección de certificados del manifiesto de aplicación.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
~~~


## Ejemplo completo de un manifiesto de aplicación
El siguiente manifiesto de aplicación muestra muchos de los diferentes valores descritos anteriormente:

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
	 <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
~~~


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

* [Entender el modelo de aplicación](service-fabric-application-model.md)
* [Especificación de los recursos en un manifiesto de servicio](service-fabric-service-manifest-resources.md)
* [Implementar una aplicación](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png

<!---HONumber=AcomDC_0224_2016-->