<properties
   pageTitle="El tamaño de la carpeta TEMP predeterminada es demasiado pequeño para un rol | Microsoft Azure"
   description="Un rol de servicio de nube tiene una cantidad limitada de espacio para la carpeta TEMP. En este artículo se ofrecen algunas sugerencias sobre cómo evitar quedarse sin espacio."
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="01/20/2016"
   ms.author="daleche" />

# El tamaño predeterminado de la carpeta TEMP es demasiado pequeño en un rol de trabajo o web de servicio en la nube.

El directorio temporal predeterminado de un rol web o de trabajo de servicio de nube tiene un tamaño máximo de 100 MB, que puede llenarse en algún momento. En este artículo se describe cómo evitar quedarse sin espacio para el directorio temporal.

>[AZURE.NOTE] Esto solo se aplica al uso de roles web o de trabajo en el SDK 1.0 a 1.4 de Azure.

## Póngase en contacto con el servicio de atención al cliente de Azure

Si necesita más ayuda en cualquier momento con este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure o de desbordamiento de pila](https://azure.microsoft.com/support/forums/).

Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](http://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte**. Para obtener información sobre el uso del soporte técnico de Azure, lea las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](http://azure.microsoft.com/support/faq/).

## ¿Por qué me quedo sin espacio?

Las variables del entorno de Windows estándar TEMP y TMP están disponibles en el código que se ejecuta en su aplicación. Tanto TEMP como TMP apuntan a un único directorio que tiene un tamaño máximo de 100 MB. Los datos que están almacenados en este directorio no persisten en el ciclo de vida del servicio en la nube; si las instancias de rol de un servicio en la nube se reciclan, el directorio se limpia.

## Sugerencia para corregir el problema

Implemente una de las siguientes alternativas:

- Configure un recurso de almacenamiento local y acceda a él directamente, en lugar de usar TEMP o TMP. Para acceder a un recurso de almacenamiento local desde el código que se ejecuta en la aplicación, llame al método [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx). Para obtener más información sobre la configuración de recursos de almacenamiento local, vea [Configurar los recursos de almacenamiento local](cloud-services-configure-local-storage-resources.md).

- Configure un recurso de almacenamiento local y apunte a los directorios TEMP y TMP para que apunten a la ruta de acceso del recurso de almacenamiento local. Esta modificación debe realizarse dentro del método [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).

En el ejemplo de código siguiente se muestra cómo modificar los directorios de destino para TEMP y TMP desde dentro del método OnStart:


```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## Pasos siguientes

Lea el blog que describe [cómo aumentar el tamaño de la carpeta temporal de ASP.NET de rol web de Azure](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Vea más [artículos de solución de problemas](..\?tag=top-support-issue&service=cloud-services) para servicios en la nube.

Para más información acerca de cómo solucionar los problemas de los roles de los servicios en la nube mediante el uso de datos de diagnóstico de equipos de PaaS de Azure, consulte la [serie de blogs de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

<!---HONumber=AcomDC_0204_2016-->