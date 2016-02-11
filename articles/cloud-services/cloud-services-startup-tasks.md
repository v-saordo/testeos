<properties 
pageTitle="Ejecución de tareas de inicio en los servicios en la nube de Azure | Microsoft Azure" 
description="Las tareas de inicio ayudan a preparar el entorno de servicio en la nube para su aplicación. Esto le enseñará cómo funcionan las tareas de inicio y cómo realizarlas" 
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
ms.date="12/07/2015" 
ms.author="adegeo"/>



# Configuración y ejecución de tareas de inicio para un servicio en la nube

Puede usar las tareas de inicio para realizar operaciones antes de que se inicie un rol. Estas operaciones incluyen la instalación de un componente, el registro de componentes COM, el establecimiento de las claves del registro o el inicio de un proceso de ejecución largo.

>[AZURE.NOTE]Las tareas de inicio no son aplicables a las máquinas virtuales, solo a los roles web y de trabajo del servicio en la nube.

## Cómo funcionan las tareas de inicio

Las tareas de inicio son acciones que se emprenden antes de comenzar los roles y se definen en el archivo [ServiceDefinition.csdef] con el uso del elemento [Task] dentro del elemento [Startup]. Con frecuencia, las tareas de inicio son archivos por lotes, pero también pueden ser aplicaciones de consola o archivos por lotes que inician scripts de PowerShell.

Las variables de entorno pasan información a una tarea de inicio y el almacenamiento local puede usarse para pasar información de una tarea de inicio. Por ejemplo, una variable de entorno puede especificar la ruta de acceso a un programa que quiera instalar, los archivos pueden escribirse en un almacenamiento local desde donde los roles los pueden leer más tarde.

La tarea de inicio puede registrar información y errores en el directorio especificado por la variable de entorno **TEMP**. Durante la tarea de inicio, la variable de entorno **TEMP** se resuelve en el directorio *C:\\Resources\\temp\\[guid]. [ roleName] \\RoleTemp* cuando se ejecutan en la nube.

Las tareas de inicio también se puede ejecutar varias veces entre reinicios. Por ejemplo, se ejecutará la tarea de inicio cada vez que el rol se recicla y los reciclajes de rol pueden no incluir siempre un reinicio. Las tareas de inicio deben escribirse de forma que les sea posible ejecutarse varias veces sin problemas.

Las tareas de inicio tienen que terminar con un **errorlevel** (o código de salida) de cero para que el proceso de inicio se complete. Si una tarea de inicio finaliza con un **errorlevel** distinto de cero, el rol no se iniciará.


## Orden de inicio de rol

A continuación se enumera el procedimiento de inicio de rol en Azure:

1. La instancia se marca como **Starting** y no recibe tráfico.

2. Todas las tareas de inicio se ejecutan de acuerdo con su atributo **taskType**.
    - Las tareas con el valor **simple** se ejecutan sincrónicamente, una a una.
    - Las tareas con los valores **background** y **foreground** se inician de forma asincrónica, paralelas a la tarea de inicio.  
       
    > [AZURE.WARNING]Puede que IIS no se haya configurado por completo durante la fase de la tarea de inicio en el proceso de inicio, por lo que los datos específicos de rol pueden no estar disponibles. Las tareas de inicio que requieren datos específicos de la role tienen que usar [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).

3. Se inicia el proceso de host de rol y se crea el sitio en IIS.

4. Se llama al método [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).

5. La instancia se marca como **Ready** y el tráfico se enruta a la instancia.

6. Se llama al método [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx).


## Ejemplo de una tarea de inicio

Las tareas de inicio se definen en el archivo [ServiceDefinition.csdef], en el elemento **Task**. El atributo **commandLine** especifica el nombre y los parámetros del archivo por lotes o del comando de consola, el atributo **executionContext** especifica el nivel de privilegio de la tarea de inicio y el atributo **taskType** especifica cómo se ejecutará la tarea.

En este ejemplo, una variable de entorno **MyVersionNumber**, se crea para la tarea de inicio y se establece en el valor "**1.0.0.0**".

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

En el ejemplo siguiente, el archivo por lotes **Startup.cmd** escribe la línea "The current version is 1.0.0.0" en el archivo StartupLog.txt en el directorio especificado por la variable de entorno TEMP. La línea `EXIT /B 0` garantiza que la tarea de inicio finaliza con un **errorlevel** de cero.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [AZURE.NOTE]En Visual Studio, la propiedad **Copiar en el directorio de salida** para el archivo por lotes de inicio debe establecerse en **Copiar siempre** para asegurarse de que el archivo por lotes de inicio se implementa debidamente en el proyecto en Azure (**approot\\bin** para los roles Web y **approot** para los roles de trabajo).

## Descripción de los atributos de la tarea

A continuación se describen los atributos del elemento **Task** en el archivo [ServiceDefinition.csdef]\:

**commandLine** - especifica la línea de comandos para la tarea de inicio:

- El comando, con los parámetros de línea de comandos opcionales, que comienza la tarea de inicio.
- Con frecuencia es el nombre de archivo de un archivo por lotes .bat o .cmd.
- La tarea es relativa a la carpeta AppRoot\\Bin para la implementación. Las variables de entorno no se expanden para determinar la ruta de acceso y el archivo de la tarea. Si se requiere la expansión del entorno, puede crear un pequeño script .cmd que llame a la tarea de inicio.
- Puede ser una aplicación de consola o un archivo por lotes que inicie un [script de PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** - especifica el nivel de privilegio de la tarea de inicio. El nivel de privilegio puede tener los valores limited o elevated:

- **limited** la tarea de inicio se ejecuta con los mismos privilegios que el rol. Cuando el atributo **executionContext** para el elemento [Runtime] también tiene el valor **limited**, se usan los privilegios de usuario.

- **elevated** la tarea de inicio se ejecuta con privilegios de administrador. Esto permite a las tareas de inicio instalar programas, realizar cambios en la configuración de IIS, realizar cambios en el registro y otras tareas de nivel de administrador, sin aumentar el nivel de privilegio del propio rol.

> [AZURE.NOTE]No es preciso que el nivel de privilegios de la tarea de inicio sea el mismo que el del propio rol.

**taskType** - especifica la manera en que se ejecuta una tarea de inicio.

- **simple** Las tareas se ejecutan sincrónicamente, una a una, en el orden especificado en el archivo [ServiceDefinition.csdef]. Cuando una tarea de inicio **simple**finaliza con un **errorlevel** de cero, se ejecuta la siguiente tarea de inicio **simple**. Si no hay más tareas de inicio con el valor **simple** para ejecutarse, se iniciará el propio rol.   

    > [AZURE.NOTE]Si la tarea **simple** finaliza con un valor **errorlevel** distinto de cero, se bloqueará la instancia. Las subsiguientes tareas de inicio **simple** y el propio rol no se iniciarán.

    Para asegurarse de que el archivo por lotes finaliza con un valor **errorlevel** de cero, ejecute el comando `EXIT /B 0` al final del proceso de archivo por lotes.

- **background** Las tareas se ejecutan de forma asincrónica, en paralelo con el inicio del rol.

- **foreground** Las tareas se ejecutan de forma asincrónica, en paralelo con el inicio del rol. La diferencia clave entre una tarea con el valor **foreground** y otra con el valor **background** es que una tarea **foreground** impide que el rol se recicle o se cierre hasta que haya finalizado la tarea. Las tareas con el valor **background** no tienen esta restricción.

## Variables de entorno

Las variables de entorno son una manera de pasar información a una tarea de inicio. Por ejemplo, puede colocar la ruta de acceso a un blob que contiene un programa para instalar, o los números de puerto que usará el rol o las configuraciones que controlan las funciones de la tarea de inicio.

Hay dos tipos de variables de entorno para las tareas de inicio; variables de entorno estáticas y variables de entorno basadas en los miembros de la clase [RoleEnvironment]. Ambos tipos se encuentran en las sección [Environment] del archivo [ServiceDefinition.csdef] y usan el elemento [Variable] y el atributo **name**.

Las variables de entorno estáticas usan el atributo **value** del elemento [Variable]. El ejemplo anterior crea la variable de entorno **MyVersionNumber** que tiene un valor estático de "**1.0.0.0**". Otro ejemplo sería crear una variable de entorno **StagingOrProduction** para la que pueda establecer manualmente los valores de "**staging**"o"**production**" para realizar acciones de inicio diferentes en función del valor de la variable de entorno **StagingOrProduction**.

Las variables de entorno que se basan en los miembros de la clase RoleEnvironment no usan el atributo **value** del elemento [Variable]. En su lugar, el elemento secundario [RoleInstanceValue], con el valor de atributo **xPath** adecuado, se usa para crear una variable de entorno basándose en un miembro específico de la clase [RoleEnvironment]. Los valores del atributo **xPath** para acceder a distintos valores [RoleEnvironment] pueden encontrarse en [Valores xPath en Azure](https://msdn.microsoft.com/library/azure/hh404006.aspx).



Por ejemplo, para crear una variable de entorno que sea "**true**" cuando se ejecuta la instancia en el emulador de proceso, y "**false**" cuando se ejecuta en la nube, use los siguientes elementos [Variable] y [RoleInstanceValue]\:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
    
            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->
    
            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>
    
        </Environment>
    </Task>
</Startup>
```

## Pasos siguientes
Aprenda a realizar algunas [tareas de inicio comunes](cloud-services-startup-tasks-common.md) con el servicio en la nube.

[Empaquete](cloud-services-model-and-package.md) el servicio en la nube.


[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Task]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Environment]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx

<!---HONumber=AcomDC_1210_2015-->