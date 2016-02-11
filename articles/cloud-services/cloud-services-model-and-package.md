<properties
    pageTitle="Qué es un modelo y un paquete de servicio en la nube en Azure | Microsoft Azure"
    description="Describe el modelo (.csdef, .cscfg) y el paquete (.cspkg) de servicio en la nube en Azure"
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>
<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/21/2016"
    ms.author="adegeo"/>

# ¿Qué es el modelo de servicio en la nube y cómo se empaqueta?
Un servicio en la nube se crea a partir de tres componentes: la definición de servicio _(.csdef)_, la configuración de servicio _(.cscfg)_ y un paquete de servicio _(.cspkg)_. Los archivos **ServiceDefinition.csdef** y **ServiceConfig.cscfg** se basan ambos en XML y describen la estructura del servicio en la nube y cómo se configura; lo que se conoce en conjunto como modelo. **ServicePackage.cspkg** es un archivo ZIP que se genera a partir de **ServiceDefinition.csdef** y, entre otras cosas, contiene todas las dependencias necesarias basadas en archivos binarios. Azure crea un servicio en la nube a partir de **ServicePackage.cspkg** y **ServiceConfig.cscfg**.

Una vez que se ejecuta el servicio en la nube en Azure, puede volver a configurarlo mediante el archivo **ServiceConfig.cscfg**, pero no puede alterar la definición.

## ¿Sobre qué le gustaría saber más?

* Quiero saber más sobre los archivos [ServiceDefinition.csdef](#csdef) y [ServiceConfig.cscfg](#cscfg).
* Eso ya lo sé. Deme [algunos ejemplos](#next-steps) sobre lo que puedo configurar.
* Quiero crear el archivo [ServicePackage.cspkg](#cspkg).
* Estoy usando Visual Studio y quiero...
    * [Crear un nuevo servicio en la nube][vs_create]
    * [Volver a configurar un servicio en la nube existente][vs_reconfigure]
    * [Implementar un proyecto de servicio en la nube][vs_deploy]
    * [Escritorio remoto en una instancia de servicio en la nube][remotedesktop]

<a name="csdef"></a>
## ServiceDefinition.csdef
El archivo **ServiceDefinition.csdef** especifica los valores que usa Azure para configurar un servicio en la nube. El [esquema de definición de servicio de Azure (archivo .csdef)](https://msdn.microsoft.com/library/azure/ee758711.aspx) proporciona el formato permitido para un archivo de definición de servicio. En el ejemplo siguiente se muestra la configuración que se puede definir para los roles web y de trabajo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

Puede hacer referencia al [[esquema de definición de servicio]] para entender mejor el esquema XML que se usa aquí; sin embargo, a continuación se da una explicación rápida de algunos de los elementos:

>**Sites** contiene las definiciones de sitios web o aplicaciones web que se hospedan en IIS7.
>
>**InputEndpoints** contiene las definiciones de los extremos que se usan para ponerse en contacto con el servicio en la nube.
>
>**InternalEndpoints** contiene las definiciones de los extremos que se usan en las instancias de rol para comunicarse entre sí.
>
>**ConfigurationSettings** contiene las definiciones de configuración de las características de un rol concreto.
>
>**Certificates** contiene las definiciones de los certificados que son necesarios para un rol. En el ejemplo de código anterior se muestra un certificado que se usa para la configuración de Azure Connect.
>
>**LocalResources** contiene las definiciones de los recursos de almacenamiento local. Un recurso de almacenamiento local es un directorio reservado en el sistema de archivos de la máquina virtual en la que se ejecuta una instancia de un rol.
>
>**Imports** contiene las definiciones de los módulos importados. El ejemplo de código anterior muestra los módulos de Conexión a Escritorio remoto y Azure Connect.
>
>**Startup** contiene las tareas que se ejecutan cuando se inicia el rol. Las tareas se definen en un archivo ejecutable o .cmd.



<a name="cscfg"></a>
## ServiceConfiguration.cscfg
La configuración de los valores del servicio en la nube viene determinada por los valores del archivo **ServiceConfiguration.cscfg**. Especifique el número de instancias que desea implementar para cada rol en este archivo. Los valores de configuración que ha definido en el archivo de definición de servicio se agregan al archivo de configuración de servicio. Las huellas digitales de los certificados de administración que están asociados con el servicio en la nube también se agregan al archivo. El [esquema de configuración de servicio de Azure (archivo .cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx) proporciona el formato permitido para un archivo de configuración de servicio.

El archivo de configuración de servicio no se empaqueta con la aplicación, sino que se carga en Azure como un archivo independiente y se usa para configurar el servicio en la nube. Puede cargar un nuevo archivo de configuración de servicio sin volver a implementar el servicio en la nube. Los valores de configuración del servicio en la nube pueden cambiarse mientras el servicio en la nube se está ejecutando. En el ejemplo siguiente se muestran los valores de configuración que se pueden definir para los roles web y de trabajo:

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Puede hacer referencia al [esquema de configuración de servicio](https://msdn.microsoft.com/library/azure/ee758710.aspx) para comprender mejor el esquema XML que se usa aquí; sin embargo, a continuación se da una explicación rápida de los elementos:

>**Instances** configura el número de instancias en ejecución para el rol. Para evitar la posibilidad de que el servicio en la nube deje de estar disponible durante las actualizaciones, es recomendable que implemente más de una instancia de los roles accesibles a través de web. Al hacerlo, estará siguiendo las instrucciones del [contrato de nivel de servicio (SLA) de Cálculo de Azure](http://azure.microsoft.com/support/legal/sla/), que garantiza la conectividad externa del 99,95 % para los roles accesibles a través de Internet cuando se implementan dos o más instancias de rol para un servicio.

>**ConfigurationSettings** configura los valores de las instancias en ejecución de un rol. El nombre de los elementos `<Setting>` debe coincidir con las definiciones de configuración del archivo de definición de servicio.

>**Certificates** configura los certificados usados por el servicio. En el ejemplo de código anterior se muestra cómo definir el certificado para el módulo RemoteAccess. El valor del atributo *thumbprint* debe establecerse en la huella digital del certificado que se va a usar.

<p/>

 >[AZURE.NOTE] La huella digital del certificado puede agregarse al archivo de configuración mediante un editor de texto, o el valor se puede agregar en la pestaña el**Certificados** de la página **Propiedades** del rol en Visual Studio.



## Definición de los puertos de las instancias de rol
Azure permite un solo punto de entrada a un rol web. Esto significa que todo el tráfico se produce a través de una dirección IP. Puede configurar sus sitios web para que compartan un puerto configurando el encabezado host para dirigir la solicitud a la ubicación correcta. También puede configurar las aplicaciones para que escuchen en puertos conocidos de la dirección IP.

En el ejemplo siguiente se muestra la configuración de un rol web con un sitio web y una aplicación web. El sitio web se configura como la ubicación de entrada predeterminada en el puerto 80 y las aplicaciones web se configuran para recibir solicitudes de un encabezado host alternativo que se llama "mail.mysite.cloudapp.net".

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" port="80" />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" hostheader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## Cambio de la configuración de un rol
Puede actualizar la configuración de su servicio en la nube mientras se ejecuta en Azure, sin necesidad de desconectarlo. Para cambiar la información de configuración, puede cargar un nuevo archivo de configuración, o editar el archivo de configuración existente y aplicarlo al servicio en ejecución. Pueden realizarse los siguientes cambios en la configuración de un servicio:

- **Cambiar los valores de configuración**: cuando los valores de una configuración cambian, una instancia de rol puede elegir aplicar el cambio mientras la instancia está en línea, o bien reciclar la instancia correctamente y aplicar el cambio mientras la instancia está sin conexión.

- **Cambiar la topología de servicio de las instancias de rol**: los cambios en la topología no afectan a las instancias en ejecución, excepto donde se vaya a eliminar una instancia. Por lo general, el resto de instancias no es necesario reciclarlas; sin embargo, puede decidir hacerlo en respuesta a un cambio en la topología.

- **Cambiar la huella digital del certificado**: solo puede actualizar un certificado cuando una instancia de rol está sin conexión. Si un certificado se agrega, elimina o cambia mientras la instancia de rol está en línea, Azure dejará la instancia sin conexión para actualizar el certificado y la volverá a poner en línea una vez completado el cambio.

### Control de los cambios de configuración con eventos de tiempo de ejecución de servicio
La [biblioteca de tiempo de ejecución de Azure](https://msdn.microsoft.com/library/azure/mt419365.aspx) incluye el espacio de nombres [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.aspx), que proporciona clases para interactuar con el entorno de Azure desde el código que se ejecuta en una instancia de un rol. La clase [RoleEnvironment](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) define los siguientes eventos que se producen antes y después de un cambio de configuración:

- **[Evento](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx) Changing**: se produce antes de que el cambio de configuración se aplique a una instancia especificada de un rol, lo que le ofrece la oportunidad de dar de baja las instancias de rol, en caso necesario.
- **[Evento](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changed.aspx) Changed**: se produce después de que el cambio de configuración se aplica a una instancia especificada de un rol.

> [AZURE.NOTE] Dado que los cambios de certificado siempre ponen las instancias de un rol sin conexión, no producen los eventos RoleEnvironment.Changing o RoleEnvironment.Changed.

<a name="cspkg"></a>
## ServicePackage.cspkg
Para implementar una aplicación como un servicio en la nube de Azure, primero debe empaquetar la aplicación en el formato adecuado. Puede usar la herramienta de línea de comandos **CSPack** (que se instala con el [SDK de Azure](https://azure.microsoft.com/downloads/)) para crear el archivo de paquete como una alternativa a Visual Studio.

**CSPack** usa el contenido del archivo de configuración de servicio y del archivo de definición de servicio para definir el contenido del paquete. **CSPack** genera un archivo de paquete de aplicación (.cspkg) que puede cargar en Azure mediante el [Portal de Azure clásico](cloud-services-how-to-create-deploy/#how-to-deploy-a-cloud-service). De forma predeterminada, el paquete se denomina `[ServiceDefinitionFileName].cspkg`, pero puede especificar un nombre diferente mediante la opción `/out` de **CSPack**.

###### Ubicación de la herramienta CSPack (en Windows)
| Versión del SDK | Ruta de acceso |
| ----------- | ---- |
| 1\.7+ | C:\\Archivos de programa\\Microsoft SDKs\\Azure\\.NET SDK\\[sdk-version]\\bin\\ |
| &lt;1.6 | C:\\Archivos de programa\\Azure SDK\\[sdk-version]\\bin\\ |

>[AZURE.NOTE]
CSPack.exe (en Windows) está disponible cuando se ejecuta el acceso directo del **símbolo del sistema de Microsoft Azure ** que se instala con el SDK.
>  
>Ejecute el programa CSPack.exe por sí solo para ver documentación sobre todos los comandos y modificadores posibles.

<p />

>[AZURE.TIP]
Ejecute su servicio en la nube localmente en el **emulador de proceso de Microsoft Azure**, use la opción **/copyonly**. Esta opción copia los archivos binarios de la aplicación en un esquema de directorio desde el que se pueden ejecutar en el emulador de proceso.

### Comando de ejemplo para empaquetar un servicio en la nube
En el ejemplo siguiente se crea un paquete de aplicación que contiene la información de un rol web. El comando especifica el archivo de definición de servicio que se usará, el directorio donde se pueden encontrar los archivos binarios y el nombre del archivo de paquete.

    cspack [DirectoryName][ServiceDefinition]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /out:[OutputFileName]

Si la aplicación contiene un rol web y un rol de trabajo, se usa el siguiente comando:

    cspack [DirectoryName][ServiceDefinition]
           /out:[OutputFileName]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]

Donde las variables se definen como de la manera siguiente:

| Variable | Valor |
| ------------------------- | ----- |
| [DirectoryName] | El subdirectorio bajo el directorio raíz del proyecto que contiene el archivo .csdef del proyecto de Azure.|
| [ServiceDefinition] | El nombre del archivo de definición de servicio. De forma predeterminada, este archivo se denomina ServiceDefinition.csdef. |
| [OutputFileName] | El nombre del archivo de paquete generado. Normalmente, se establece en el nombre de la aplicación. Si no se especifica ningún nombre de archivo, el paquete de aplicación se crea como [ApplicationName].cspkg.|
| [RoleName] | El nombre del rol, tal y como se define en el archivo de definición de servicio.|
| [RoleBinariesDirectory] | La ubicación de los archivos binarios para el rol.|
| [VirtualPath] | Los directorios físicos de cada ruta de acceso virtual definida en la sección Sites de la definición de servicio.|
| [PhysicalPath] | Los directorios físicos del contenido de cada ruta de acceso virtual definida en el nodo de sitio de la definición de servicio.|
| [RoleAssemblyName] | El nombre del archivo binario del rol.| 


## Pasos siguientes

Voy a crear un paquete de servicio en la nube y quiero...

* [Configuración de recursos de almacenamiento local](cloud-services-configure-local-storage-resources.md)
* [Configurar Escritorio remoto para una instancia de servicio en la nube][remotedesktop]
* [Implementar un proyecto de servicio en la nube][deploy]

Estoy usando Visual Studio y quiero...

* [Crear un nuevo servicio en la nube][vs_create]
* [Volver a configurar un servicio en la nube existente][vs_reconfigure]
* [Implementar un proyecto de servicio en la nube][vs_deploy]
* [Configurar Escritorio remoto para una instancia de servicio en la nube][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop.md
[vs_remote]: ../vs-azure-tools-remote-desktop-roles.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md

<!---HONumber=AcomDC_0128_2016-->